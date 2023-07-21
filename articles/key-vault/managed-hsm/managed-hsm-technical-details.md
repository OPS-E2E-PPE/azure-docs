---
title: How Managed HSM implements key sovereignty, availability, performance, and scalability without tradeoffs
description: A technical description of how Customer Key control is implemented cryptographically by managed HSM
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: davinune
ms.author: davinune
ms.date: 07/20/2023
---

# Azure Managed HSM: key sovereignty, availability, performance, and scalability

Cryptographic keys are the root of trust for securing modern computer systems, be they in the cloud or on-premises. As such, controlling who has authority over those keys is critical to building secure and compliant applications. In Azure, our vision of how the key management should be done in the cloud is expressed in the concept of **key sovereignty**, which means a customer's organization has full and exclusive control over what people can access keys and change key management policies; and over what Azure services consume these keys. Once these decisions are made by the customer, Microsoft personnel are prevented through technical means from changing these decisions. The key management service code executes the customer's decisions until the customer tells it to do otherwise, and Microsoft personnel cannot intervene.

At the same time, it is our belief that every service in the cloud must be fully managed – it must provide the availability, resiliency, security and cloud fundamental promises, backed by service level agreements (SLA). In order to deliver a managed service, Microsoft needs to patch key management servers, upgrade HSM firmware, heal failing hardware, perform failovers, etc. – high privilege operations. As most security professionals know, denying someone with high privilege or physical access to a system access to the data within that system is a difficult problem. This article explains how we solved this problem in the Managed HSM service (giving customers both full key sovereignty and fully managed service SLAs) by using confidential computing technology paired with Hardware Security Modules (HSMs).

## The Managed HSM hardware environment

A customer's Managed HSM pool in any given Azure region is housed in a [secure Azure datacenter](../../security/fundamentals/physical-security.md), with three instances spread over several servers, each deployed in a different rack to ensure redundancy. Each server has a [FIPS 140-2 Level 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) validated Marvell Liquid Security HSM Adapter with multiple cryptographic cores used to create fully isolated HSM partitions including fully isolated credentials, data storage, access control, etc.

The physical separation of the instances inside the datacenter is critical to ensuring that the loss of a single component (top-of-rack switch, power management unit in a rack, etc.) can't affect all the instances of a pool. These servers are dedicated to the Azure Security HSM team, and are not shared with other Azure teams, and no customer workloads are deployed to these servers. Physical access controls, including locked racks, are used to prevent unauthorized access to the servers. These controls meet FedRAMP-High, PCI, SOC 1/2/3, ISO 270x, and other security and privacy standards, and are regularly independently verified as part of [Azure's compliance program](https://www.microsoft.com/trust-center/compliance/compliance-overview?rtc=1). The HSMs have enhanced physical security, validated to meet FIPS 140-2 Level 3 and the entire Managed HSM service is built on top of the standard [secure Azure platform](../../security/fundamentals/platform.md) including [Trusted Launch](../../virtual-machines/trusted-launch.md), which protects against advanced persistent threats (APTs).

The HSM adapters can support dozens of isolated HSM partitions. Running on each server is a control process, called Node Service (NS), that takes ownership of each adapter and installs the credentials for the adapter owner, in this case Microsoft. The HSM is designed so that ownership of the adapter does not provide Microsoft with access to data stored in customer partitions. It only allows Microsoft to create, resize and delete customer partitions, and it supports taking blind backups of any partition for the customer. A blind backup is one wrapped by a customer provided key that can be restored by the service 
code only inside an HSM instance owned by the customer, and whose contents are not readable by Microsoft.

### Architecture of a Managed HSM Pool

Figure 1 below show the architecture of an HSM pool, which consists of three Linux VMs, each running on an HSM server in its own datacenter rack to support availability. The important components are:
- The HSM fabric controller (HFC) is the control plane for the service, which drives automated patching and repairs for the pool.
- A FIPS 140-2 Level 3 compliant cryptographic boundary, exclusive for each customer, including three Intel SGX confidential enclaves, each connected to an HSM instance. The root keys for this boundary are generated and stored in the three HSMs. As we will describe, no Microsoft person has access to the data within this boundary; only service code running in the SGX enclave (including the Node Service agent), acting on behalf of the customer, has access.

![Architectural diagram of an MHSM pool showing the TEEs inside the customer cryptographic boundary and health maintenance operations outside of the boundary.](../../media/mhsm-technical-details/mhsm-architecture.png)

### The trusted execution environment (TEE)

A Managed HSM pool consists of three service instances each implemented as a Trusted Execution Environment (TEE) which uses [Intel Secure Guard Extensions (SGX)](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) capabilities and the [Open Enclave SDK](https://openenclave.io/sdk/). Execution within a TEE ensures that no person on either the VM hosting the service or the VM's host server has access to customer secrets, data, or the HSM partition. Each TEE is dedicated to a specific customer, and it runs TLS management, request handling, and access control to the HSM partition. No credentials or customer-specific data encryption keys exist in the clear outside this TEE, except as part of the Security Domain package. That package is encrypted to customer-provided key and downloads when their pool is first created.

The TEEs communicate among themselves using [attested TLS](https://arxiv.org/pdf/1801.05863.pdf), which combines the remote attestation capabilities of the SGX platform with TLS 1.2. This allows MSHM code in the TEE to limit its communication to only other code signed by the same MHSM service code signing key, preventing man-in-the-middle attacks. The MHSM service's code signing key is stored in Microsoft's Product Release and Security Service (which is also used to store, for example, the Windows code signing key), and is controlled by the Managed HSM team. As part of our regulatory and compliance obligations for change management, this key cannot be used by any other Microsoft team to sign their code.

The TLS certificates used for TEE to TEE communication are self-issued by the service code inside the TEE, and contain what is called a platform report generated by the Intel SGX enclave on the server. The platform report is signed with keys derived from keys fused by Intel into the CPU when it's manufactured. The report identifies the code that is loaded into the SGX enclave by its code signing key and binary hash. Given this platform report, service instances can determine that a peer is also signed by the MHSM service code signing key  and, with some crypto entanglement via the platform report, can also determine that the self-issued certificate signing key must also have been generated inside the TEE, preventing external impersonation.

## Delivering availability SLAs with full Customer Key control

### Managed HSM pool creation

The high-availability properties of Managed HSM pools come from the automatically managed triple redundant HSM instances that are always kept in sync (or if using [multi-region replication](multi-region-replication.md), from keeping all six instances in sync). Pool creation is managed by the HSM Fabric Controller (HFC) service that allocates pools across the available hardware in the Azure region chosen by the customer. When a new pool is requested, HFC selects three servers across several racks with available space on their HSM adapters, and starts creating the pool:
1. HFC instructs Node Service on each of the three TEEs to launch a new instance of the service code with a set of parameters that identify the customer's Azure Active Directory tenant, the internal VNET IP addresses of all three instances, and some other service configuration. One partition is randomly assigned as Primary.
1. The three instances start. Each instance connects to a partition on its local HSM adapter, then zeroizes and initializes the partition using randomly generated usernames and credentials (to ensure the partition cannot be accessed by a human operator or other TEE instance).
1.	The primary instance creates a partition owner root certificate with the private key generated in the HSM, and establishes ownership of the pool by signing a partition level certificate for the HSM partition with this root certificate. The primary also generates a data encryption key, which is used to protect all customer data at rest inside the service (for key material, a double wrapping is used as the HSM also protects the key material itself).
1.	Next, this ownership data is synchronized to the two secondary instances. Each secondary contacts the primary using attested TLS. The primary shares the partition owner root certificate with private key, and the data encryption key. The secondaries now use the partition root certificate to issue a partition certificate to their own HSM partitions. Once this is done, we have HSM partitions on three separate servers owned by the same partition root certificate.
1.	Still over the attested TLS link, the primary's HSM partition shares with the secondaries its generated data wrapping key (used to encrypt messages between the three HSMs), using a secure API provided by the HSM vendor. During this exchange, the HSMs confirm that they have the same partition owner certificate, and then use a Diffie-Hellman scheme to encrypt the messages such that Microsoft service code cannot read them – all the service code can do is transport opaque blobs between the HSMs.

  At this point, all three instances are ready to be exposed as a pool on the customer's VNET: They share the same partition owner certificate and private key, the same data encryption key, and a common data wrapping key. However, each instance has unique credentials to their HSM partitions. Now the final steps are completed:

1. Each instance generates an RSA key pair and a Certificate Signing Request (CSR) for its public facing TLS certificate. The CSR is signed by the Microsoft PKI system using a Microsoft public root, and the resultant TLS certificate is returned to the instance.
1. All three instances obtain their own SGX sealing key from their local CPU: this key is generated using the CPU's own unique key, and the TEE's code signing key.
1. The pool derives a unique pool key from the SGX sealing keys, encrypts all its secrets with this pool key, and then writes the encrypted blobs to disk. These blobs can only be decrypted by code signed by the same SGX sealing key, running on the same physical CPU. The secrets are thus bound to that particular instance.

The secure bootstrap process is now complete. This process has allowed for both the creation of a triple redundant HSM pool, and creation of a cryptographic guarantee of the sovereignty of customer data.

### Maintaining availability SLAs at runtime using confidential service healing

The pool creation story above can explain how the Managed HSM service is able to deliver its high availability SLAs by securely managing the servers that underlie the service. Imagine that a server, or an HSM adapter, or even the power supply to the rack fails. The goal of the MSHM service is, without any customer intervention or the possibility of secrets being exposed in clear text outside the TEE, to heal the pool back to three healthy instances. This is achieved through confidential service healing.
It starts with the HFC knowing which pools had instances on the failed server. HFC finds new, healthy servers within the pool's region to deploy the replacement instances to. It launches new instances, which are then treated exactly as a secondary during the initial provisioning step: initialize the HSM, find its primary, securely exchange secrets over attested TLS, sign the HSM into the ownership hierarchy, and then seal its service data to its new CPU. The service is now healed, fully automatically and fully confidentially.

### Recovering from disaster using the security domain

The Security Domain (SD) is a secured blob that contains all the credentials needed to rebuild the HSM partition from scratch: the partition owner key, the partition credentials, the data wrapping key, plus an initial backup of the HSM. Before the service becomes live, the customer must download the SD by providing a set of RSA encryption keys to secure it. The SD data originates in the TEEs and is protected by a generated symmetric key and an implementation of [Shamir's Secret Sharing algorithm](https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing) which splits the key shares across the customer provided RSA public keys according to customer selected quorum parameters. During this process, none of the service keys or credentials are ever exposed in plaintext outside the service code running in the TEEs; only the customer, by presenting a quorum of their RSA keys to the TEE, can decrypt the SD during a recovery scenario.

The SD is needed only for cases where due to some catastrophe, entire Azure region is, and Microsoft loses all three instances of the pool simultaneously. If only one or even two instances are lost, then confidential service healing with quietly recover to three healthy instances with no customer intervention. If the entire region is lost, then because SGX sealing keys are unique to each CPU, Microsoft has no way to recover the HSM credentials and partition owner keys; they exist only within the context of the instances. In the extremely unlikely event that this catastrophe happens, the customer can recover their previous pool state and data by creating a new blank pool, and injecting it into the SD, and then presenting their RSA key quorum to prove ownership of the SD. For the case where a customer has enabled multi-region replication, the even more unlikely catastrophe of both of the regions experiencing a simultaneous, complete failure would have to happen before customer intervention would be needed to recover the pool from the SD.

### Controlling access to the service

As we have described, our service code in the TEE is the only entity with access to the HSM itself, as the necessary credentials are not given to the customer or anyone else. Instead, the customer's pool is bound to their Azure Active Directory instance, and this is used for authentication and authorization. At initial provisioning, the customer can choose an initial set of Administrators for the pool, and these individuals, as well as the customer's Azure Active Directory tenant Global Administrator, can then set access control policies within the pool. All access control policies are stored by the service in the same database as the masked keys, also encrypted. Only the service code in the TEE has access to these access control policies.

## Conclusion

Managed HSM removes the need for customers to make tradeoffs between availability and control over cryptographic keys by using cutting edge, hardware backed confidential enclave technology. As discussed in this paper, the implementation is such that no Microsoft personnel can access Customer Key material or related secrets, even with physical access to the Managed HSM host machines and HSMs. This has allowed our customers in the sectors of financial services, manufacturing, public sector, defense and other verticals to accelerate their migration to the cloud with full confidence.

## What's next

Further reading:
- [Azure Key Vault Managed HSM – Control your data in the cloud](mhsm-control-data.md)
- [About the Managed HSM security domain](security-domain.md)
- [Managed HSM access control](access-control.md)
- [Local RBAC built in roles](built-in-roles.md)
- [Managing compliance in the cloud](https://www.microsoft.com/trust-center/compliance/compliance-overview?rtc=1)
