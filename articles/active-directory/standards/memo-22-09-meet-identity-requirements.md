---
title: Memo 22-09 identity requirements overview
description: Guidance on meeting requirements outlined in US government OMB memorandum 22-09
services: active-directory 
ms.service: active-directory
ms.subservice: standards
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: martinco
ms.reviewer: martinco
ms.date: 3/10/2022
ms.custom: it-pro
ms.collection: M365-identity-device-management
---

# Meeting identity requirements of Memorandum 22-09 with Azure Active Directory

This series of articles offer guidance for employing Azure Active Directory (Azure AD) as a centralized identity management system for implementing Zero Trust principles as described by the US Federal Government’s Office of Management and Budget (OMB) [Memorandum M-22-09](https://www.whitehouse.gov/wp-content/uploads/2022/01/M-22-09.pdf). Throughout this document we refer to it as "The memo."

The release of Memorandum 22-09 is designed to support Zero trust initiatives within federal agencies; it also provides regulatory guidance in supporting Federal Cybersecurity and Data Privacy Laws. The Memo cites the [Department of Defense (DoD) Zero Trust Reference Architecture](https://dodcio.defense.gov/Portals/0/Documents/Library/(U)ZT_RA_v1.1(U)_Mar21.pdf), 

"The foundational tenet of the Zero Trust Model is that no actor, system, network, or service operating outside or within the security perimeter is trusted. Instead, we must verify anything and everything attempting to establish access. It is a dramatic paradigm shift in philosophy of how we secure our infrastructure, networks, and data, from verify once at the perimeter to continual verification of each user, device, application, and transaction."

The Memo identifies five core goals that must be reached by federal agencies. These goals are organized using the Cybersecurity Information Systems Architecture (CISA) Maturity Model. CISA’s zero trust model describes five complementary areas of effort – or pillars: Identity, Devices, Networks, Applications and Workloads, and Data; with three themes that cut across these areas (Visibility and Analytics, Automation and Orchestration, and Governance).

## Scope of guidance

This series of articles provides practical guidance for administrators and decision makers to adapt a plan to meet memo requirements. It assumes that you are using Microsoft 365 products, and therefore have an Azure Active Directory tenant available. If this is inaccurate, see [Access & create new tenant](../fundamentals/active-directory-access-create-new-tenant.md).

It features guidance encompassing existing agency investments in Microsoft technologies that align with the identity-related actions outlined in the memo:

* Agencies must employ centralized identity management systems for agency users that 
can be integrated into applications and common platforms.


*  Agencies must use strong multi-factor authentication (MFA) throughout their enterprise.

   *  MFA must be enforced at the application layer, instead of the network layer.

   *  For agency staff, contractors, and partners, phishing-resistant MFA is required.

  *  For public users, phishing-resistant MFA must be an option.

* Password policies must not require the use of special characters or regular rotation.

* When authorizing users to access resources, agencies must consider at least one device-level signal alongside identity information about the authenticated user.

 
## Next steps

The following articles are a part of this documentation set:

[Meet identity requirements of Memorandum 22-09](memo-22-09-meet-identity-requirements.md)

[Enterprise-wide identity management system](memo-22-09-enterprise-wide-identity-management-system.md)

[Multi-factor authentication](memo-22-09-multi-factor-authentication.md)

[Authorization](memo-22-09-authorization.md)

[Other areas of Zero Trust](memo-22-09-other-areas-zero-trust.md)

Additional Zero Trust Documentation

[Securing identity with Zero Trust](/security/zero-trust/deploy/identity)
