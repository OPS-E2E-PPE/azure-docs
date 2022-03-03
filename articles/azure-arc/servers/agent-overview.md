---
title:  Overview of the Azure Connected Machine agent
description: This article provides a detailed overview of the Azure Arc-enabled servers agent available, which supports monitoring virtual machines hosted in hybrid environments.
ms.date: 03/03/2022
ms.topic: conceptual 
ms.custom: devx-track-azurepowershell
---

# Overview of Azure Connected Machine agent

The Azure Connected Machine agent enables you to manage your Windows and Linux machines hosted outside of Azure on your corporate network or other cloud providers. This article provides a detailed overview of the agent, system and network requirements, and the different deployment methods.

>[!NOTE]
> The [Azure Monitor agent](../../azure-monitor/agents/azure-monitor-agent-overview.md) (AMA) does not replace the Connected Machine agent. The Azure Monitor agent will replace the Log Analytics agent, Diagnostics extension, and Telegraf agent for both Windows and Linux machines. Review the Azure Monitor documentation about the new agent for more details.

## Agent component details

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Azure Arc-enabled servers agent overview." border="false":::

The Azure Connected Machine agent package contains several logical components, which are bundled together.

* The Hybrid Instance Metadata service (HIMDS) manages the connection to Azure and the connected machine's Azure identity.

* The guest configuration agent provides functionality such as assessing whether the machine complies with required policies and enforcing compliance.

    Note the following behavior with Azure Policy [guest configuration](../../governance/policy/concepts/guest-configuration.md) for a disconnected machine:

  * An Azure Policy assignment that targets disconnected machines is unaffected.
  * Guest assignment is stored locally for 14 days. Within the 14-day period, if the Connected Machine agent reconnects to the service, policy assignments are reapplied.
  * Assignments are deleted after 14 days, and are not reassigned to the machine after the 14-day period.

* The Extension agent manages VM extensions, including install, uninstall, and upgrade. Extensions are downloaded from Azure and copied to the `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` folder on Windows, and for Linux to `/opt/GC_Ext/downloads`. On Windows, the extension is installed to the following path `%SystemDrive%\Packages\Plugins\<extension>`, and on Linux the extension is installed to `/var/lib/waagent/<extension>`.

## Instance metadata

Metadata information about the connected machine is collected after the Connected Machine agent registers with Azure Arc-enabled servers. Specifically:

* Operating system name, type, and version
* Computer name
* Computer manufacturer and model
* Computer fully qualified domain name (FQDN)
* Domain name (if joined to an Active Directory domain)
* Active Directory and DNS fully qualified domain name (FQDN)
* UUID (BIOS ID)
* Connected Machine agent heartbeat
* Connected Machine agent version
* Public key for managed identity
* Policy compliance status and details (if using guest configuration policies)
* SQL Server installed (Boolean value)
* Cluster resource ID (for Azure Stack HCI nodes)
* Hardware manufacturer
* Hardware model
* Cloud provider
* Amazon Web Services (AWS) account ID, instance ID and region (if running in AWS)

The following metadata information is requested by the agent from Azure:

* Resource location (region)
* Virtual machine ID
* Tags
* Azure Active Directory managed identity certificate
* Guest configuration policy assignments
* Extension requests - install, update, and delete.

## Download agents

You can download the Azure Connected Machine agent package for Windows and Linux from the locations listed below.

* [Windows agent Windows Installer package](https://aka.ms/AzureConnectedMachineAgent) from the Microsoft Download Center.

* Linux agent package is distributed from Microsoft's [package repository](https://packages.microsoft.com/) using the preferred package format for the distribution (.RPM or .DEB).

The Azure Connected Machine agent for Windows and Linux can be upgraded to the latest release manually or automatically depending on your requirements. For more information, see [here](manage-agent.md).

## Prerequisites

### Supported environments

Azure Arc-enabled servers supports the installation of the Connected Machine agent on any physical server and virtual machine hosted *outside* of Azure. This includes support for virtual machines running on platforms like:

* VMware
* Azure Stack HCI
* Other cloud environments

Azure Arc-enabled servers *does not* support installing the agent on virtual machines running in Azure, or virtual machines running on Azure Stack Hub or Azure Stack Edge as they are already modeled as Azure VMs.

### Supported operating systems

The following versions of the Windows and Linux operating system are officially supported for the Azure Connected Machine agent:

* Windows Server 2008 R2 SP1, 2012 R2, 2016, 2019, and 2022
  * Both Desktop and Server Core experiences are supported
  * Azure Editions are supported when running as a virtual machine on Azure Stack HCI
* Azure Stack HCI
* Ubuntu 16.04, 18.04, and 20.04 LTS (x64)
* CentOS Linux 7 and 8  (x64)
* SUSE Linux Enterprise Server (SLES) 12 and 15 (x64)
* Red Hat Enterprise Linux (RHEL) 7 and 8 (x64)
* Amazon Linux 2 (x64)
* Oracle Linux 7 (x64)

> [!WARNING]
> The Linux hostname or Windows computer name cannot use one of the reserved words or trademarks in the name, otherwise attempting to register the connected machine with Azure will fail. For a list of reserved words, see [Resolve reserved resource name errors](../../azure-resource-manager/templates/error-reserved-resource-name.md).

> [!NOTE]
> While Azure Arc-enabled servers supports Amazon Linux, the following features are not support by this distribution:
>
> * The Dependency agent used by Azure Monitor VM insights
> * Azure Automation Update Management

### Software requirements

* NET Framework 4.6 or later is required. [Download the .NET Framework](/dotnet/framework/install/guide-for-developers).
* Windows PowerShell 5.1 is required. [Download Windows Management Framework 5.1.](https://www.microsoft.com/download/details.aspx?id=54616).

### Required permissions

* To onboard machines, you are a member of the **Azure Connected Machine Onboarding** or [Contributor](../../role-based-access-control/built-in-roles.md#contributor) role in the resource group.

* To read, modify, and delete a machine, you are a member of the **Azure Connected Machine Resource Administrator** role in the resource group.

* To select a resource group from the drop-down list when using the **Generate script** method, at a minimum you are a member of the [Reader](../../role-based-access-control/built-in-roles.md#reader) role for that resource group.

### Azure subscription and service limits

Before configuring your machines with Azure Arc-enabled servers, review the Azure Resource Manager [subscription limits](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) and [resource group limits](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) to plan for the number of machines to be connected.

Azure Arc-enabled servers supports up to 5,000 machine instances in a resource group.

### Register Azure resource providers

Azure Arc-enabled servers depend on the following Azure resource providers in your subscription in order to use this service:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**
* **Microsoft.HybridConnectivity**

If these resource providers are not already registered, you can register them using the following commands:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridConnectivity
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
az provider register --namespace 'Microsoft.HybridConnectivity'
```

You can also register the resource providers in the [Azure portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### Transport Layer Security 1.2 protocol

To ensure the security of data in transit to Azure, we strongly encourage you to configure machine to use Transport Layer Security (TLS) 1.2. Older versions of TLS/Secure Sockets Layer (SSL) have been found to be vulnerable and while they still currently work to allow backwards compatibility, they are **not recommended**.

|Platform/Language | Support | More Information |
| --- | --- | --- |
|Linux | Linux distributions tend to rely on [OpenSSL](https://www.openssl.org) for TLS 1.2 support. | Check the [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) to confirm your version of OpenSSL is supported.|
| Windows Server 2012 R2 and higher | Supported, and enabled by default. | To confirm that you are still using the [default settings](/windows-server/security/tls/tls-registry-settings).|

## Networking configuration

The Azure Connected Machine agent for Linux and Windows communicates outbound securely to Azure Arc over TCP port 443. By default, the agent uses the default route to the internet to reach Azure services. You can optionally [configure the agent to use a proxy server](manage-agent.md#update-or-remove-proxy-settings) if your network requires it. Proxy servers don't make the Connected Machine agent more secure because the traffic is already encrypted.

To further secure your network connectivity to Azure Arc, instead of using public networks and proxy servers, you can implement an [Azure Arc Private Link Scope](private-link-security.md) (preview).

> [!NOTE]
> Azure Arc-enabled servers does not support using a [Log Analytics gateway](../../azure-monitor/agents/gateway.md) as a proxy for the Connected Machine agent.

If outbound connectivity is restricted by your firewall or proxy server, make sure the URLs listed below are not blocked. When you only allow the IP ranges or domain names required for the agent to communicate with the service, you need to allow access to the following Service Tags and URLs.

Service Tags:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure
* Storage

URLs:

| Agent resource | Description | When required| Endpoint used with private link |
|---------|---------|--------|---------|
|`aka.ms`|Used to resolve the download script during installation|At installation time, only| Public |
|`download.microsoft.com`|Used to download the Windows installation package|At installation time, only| Public |
|`packages.microsoft.com`|Used to download the Linux installation package|At installation time, only| Public |
|`login.windows.net`|Azure Active Directory|Always| Public |
|`login.microsoftonline.com`|Azure Active Directory|Always| Public |
|`pas.windows.net`|Azure Active Directory|Always| Public |
|`management.azure.com`|Azure Resource Manager - to create or delete the Arc server resource|When connecting or disconnecting a server, only| Public, unless a [resource management private link](../../azure-resource-manager/management/create-private-link-access-portal.md) is also configured |
|`*.his.arc.azure.com`|Metadata and hybrid identity services|Always| Private |
|`*.guestconfiguration.azure.com`| Extension management and guest configuration services |Always| Private |
|`guestnotificationservice.azure.com`, `*.guestnotificationservice.azure.com`|Notification service for extension and connectivity scenarios|Always| Private |
|`azgn*.servicebus.windows.net`|Notification service for extension and connectivity scenarios|Always| Public |
|`*.blob.core.windows.net`|Download source for Azure Arc-enabled servers extensions|Always, except when using private endpoints| Not used when private link is configured |
|`dc.services.visualstudio.com`|Agent telemetry|Optional| Public |

For a list of IP addresses for each service tag/region, see the JSON file [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publishes weekly updates containing each Azure Service and the IP ranges it uses. This information in the JSON file is the current point-in-time list of the IP ranges that correspond to each service tag. The IP addresses are subject to change. If IP address ranges are required for your firewall configuration, then the **AzureCloud** Service Tag should be used to allow access to all Azure services. Do not disable security monitoring or inspection of these URLs, allow them as you would other Internet traffic.

For more information, see [Virtual network service tags](../../virtual-network/service-tags-overview.md).

## Installation and configuration

Connecting machines in your hybrid environment directly with Azure can be accomplished using different methods, depending on your requirements and the tools you prefer to use. The following table highlights each method so that you can determine which works best for your deployment.

| Method | Description |
|--------|-------------|
| Interactively | Manually install the agent on a single or small number of machines by [connecting machines using a deployment script](onboard-portal.md).<br> From the Azure portal, you can generate a script and execute it on the machine to automate the install and configuration steps of the agent.|
| Interactively | [Connect machines from Windows Admin Center](onboard-windows-admin-center.md) |
| Interactively or at scale | [Connect machines using PowerShell](onboard-powershell.md) |
| Interactively or at scale | [Connect machines using Windows PowerShell Desired State Configuration (DSC)](onboard-dsc.md) |
| At scale | [Connect machines using a service principal](onboard-service-principal.md) to install the agent at scale non-interactively.|
| At scale | [Connect machines by running PowerShell scripts with Configuration Manager](onboard-configuration-manager-powershell.md)
| At scale | [Connect machines with a Configuration Manager custom task sequence](onboard-configuration-manager-custom-task.md)
| At scale | [Connect machines from Automation Update Management](onboard-update-management-machines.md) to create a service principal that installs and configures the agent for multiple machines managed with Azure Automation Update Management to connect machines non-interactively. |

> [!IMPORTANT]
> The Connected Machine agent cannot be installed on an Azure Windows virtual machine. If you attempt to, the installation detects this and rolls back.

## Connected Machine agent technical overview

### Windows agent installation details

The Connected Machine agent for Windows can be installed by using one of the following three methods:

* Running the file `AzureConnectedMachineAgent.msi`.
* Manually by running the Windows Installer package `AzureConnectedMachineAgent.msi` from the Command shell.
* From a PowerShell session using a scripted method.

Installing, upgrading, or removing the Connected Machine agent will not require you to restart your server.

After installing the Connected Machine agent for Windows, the following system-wide configuration changes are applied.

* The following installation folders are created during setup.

    |Folder |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |azcmagent CLI and instance metadata service executables.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\GC | Extension service executables.|
    |%ProgramFiles%\AzureConnectedMachineAgent\GuestConfig\GC | Guest configuration (policy) service executables.|
    |%ProgramData%\AzureConnectedMachineAgent |Configuration, log and identity token files for azcmagent CLI and instance metadata service.|
    |%ProgramData%\GuestConfig |Extension package downloads, guest configuration (policy) definition downloads, and logs for the extension and guest configuration services.|

* The following Windows services are created on the target machine during installation of the agent.

    |Service name |Display name |Process name |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |This service implements the Hybrid Instance Metadata service (IMDS) to manage the connection to Azure and the connected machine's Azure identity.|
    |GCArcService |Guest configuration Arc Service |gc_service |Monitors the desired state configuration of the machine.|
    |ExtensionService |Guest configuration Extension Service | gc_service |Installs the required extensions targeting the machine.|

* The following virtual service account is created during agent installation.

    | Virtual Account  | Description |
    |------------------|---------|
    | NT SERVICE\\himds | Unprivileged account used to run the Hybrid Instance Metadata Service. |

    > [!TIP]
    > This account requires the "Log on as a service" right. This right is automatically granted during agent installation, but if your organization configures user rights assignments with Group Policy, you may need to adjust your Group Policy Object to grant the right to  "NT SERVICE\\himds" or "NT SERVICE\\ALL SERVICES" to allow the agent to function.

* The following local security group is created during agent installation.

    | Security group name | Description |
    |---------------------|-------------|
    | Hybrid agent extension applications | Members of this security group can request Azure Active Directory tokens for the system-assigned managed identity |

* The following environmental variables are created during agent installation.

    |Name |Default value |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |<`http://localhost:40342/metadata/identity/oauth2/token`> ||
    |IMDS_ENDPOINT |<`http://localhost:40342`> ||

* There are several log files available for troubleshooting. They are described in the following table.

    |Log |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Records details of the heartbeat and identity agent component.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contains the output of the azcmagent tool commands.|
    |%ProgramData%\GuestConfig\arc_policy_logs\ |Records details about the guest configuration (policy) agent component.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Records details about the Extension agent component.|
    |%ProgramData%\GuestConfig\extension_logs\\\<Extension>|Records details from the installed extension.|

* The local security group **Hybrid agent extension applications** is created.

* During uninstall of the agent, the following artifacts are not removed.

  * %ProgramData%\AzureConnectedMachineAgent\Log
  * %ProgramData%\AzureConnectedMachineAgent and subdirectories
  * %ProgramData%\GuestConfig

### Linux agent installation details

The Connected Machine agent for Linux is provided in the preferred package format for the distribution (.RPM or .DEB) that's hosted in the Microsoft [package repository](https://packages.microsoft.com/). The agent is installed and configured with the shell script bundle [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Installing, upgrading, or removing the Connected Machine agent will not require you to restart your server.

After installing the Connected Machine agent for Linux, the following system-wide configuration changes are applied.

* The following installation folders are created during setup.

    |Folder |Description |
    |-------|------------|
    |/opt/azcmagent/ |azcmagent CLI and instance metadata service executables.|
    |/opt/GC_Ext/ | Extension service executables.|
    |/opt/GC_Service/ |Guest configuration (policy) service executables.|
    |/var/opt/azcmagent/ |Configuration, log and identity token files for azcmagent CLI and instance metadata service.|
    |/var/lib/GuestConfig/ |Extension package downloads, guest configuration (policy) definition downloads, and logs for the extension and guest configuration services.|

* The following daemons are created on the target machine during installation of the agent.

    |Service name |Display name |Process name |Description |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine Agent Service |himds |This service implements the Hybrid Instance Metadata service (IMDS) to manage the connection to Azure and the connected machine's Azure identity.|
    |gcad.service |GC Arc Service |gc_linux_service |Monitors the desired state configuration of the machine. |
    |extd.service |Extension Service |gc_linux_service | Installs the required extensions targeting the machine.|

* There are several log files available for troubleshooting. They are described in the following table.

    |Log |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Records details of the heartbeat and identity agent component.|
    |/var/opt/azcmagent/log/azcmagent.log |Contains the output of the azcmagent tool commands.|
    |/var/lib/GuestConfig/arc_policy_logs |Records details about the guest configuration (policy) agent component.|
    |/var/lib/GuestConfig/ext_mgr_logs |Records details about the extension agent component.|
    |/var/lib/GuestConfig/extension_logs|Records details from extension install/update/uninstall operations.|

* The following environmental variables are created during agent installation. These variables are set in `/lib/systemd/system.conf.d/azcmagent.conf`.

    |Name |Default value |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |<`http://localhost:40342/metadata/identity/oauth2/token`> ||
    |IMDS_ENDPOINT |<`http://localhost:40342`> ||

* During uninstall of the agent, the following artifacts are not removed.

  * /var/opt/azcmagent
  * /var/lib/GuestConfig

### Agent resource governance

Azure Connected Machine agent is designed to manage agent and system resource consumption. The agent approaches resource governance under the following conditions:

* The Guest Configuration agent is limited to use up to 5% of the CPU to evaluate policies.
* The Extension Service agent is limited to use up to 5% of the CPU to install and manage extensions.

  * Once installed, each extension is limited to use up to 5% of the CPU while running. For example, if you have 2 extensions installed, they can use a combined total of 10% of the CPU.
  * The Log Analytics agent and Azure Monitor Agent are allowed to use up to 60% of the CPU during their install/upgrade/uninstall operations on Red Hat Linux, CentOS, and other enterprise Linux variants. The limit is higher for this combination of extensions and operating systems to accommodate the performance impact of [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux) on these systems.

## Next steps

* To begin evaluating Azure Arc-enabled servers, follow the article [Connect hybrid machines with Azure Arc-enabled servers](learn/quick-enable-hybrid-vm.md).

* Before you deploy the Azure Arc-enabled servers agent and integrate with other Azure management and monitoring services, review the [Planning and deployment guide](plan-at-scale-deployment.md).

* Troubleshooting information can be found in the [Troubleshoot Connected Machine agent guide](troubleshoot-agent-onboard.md).
