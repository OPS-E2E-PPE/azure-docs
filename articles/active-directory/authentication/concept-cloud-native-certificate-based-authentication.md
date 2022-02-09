---
title: Overview of cloud-native certificate-based authentication against Azure Active Directory (Preview) - Azure Active Directory 
description: Learn about cloud-native certificate-based authentication in Azure Active Directory without federation

services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 01/24/2022

ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: tommma

ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
---

# Overview of cloud-native certificate-based authentication against Azure Active Directory (Preview)

Cloud-native certificate-based authentication (CBA) enables customers to allow or require users to authenticate with X.509 certificates against their Azure Active Directory (Azure AD) for applications and browser sign-in. 
This feature enables customers to adopt a phishing resistant authentication and authenticate with an X.509 certificate against their Enterprise Public Key Infrastructure (PKI).

>[!NOTE]
>Cloud-native certificate-based authentication is currently in public preview. Some features might not be supported or have limited capabilities. For more information about previews, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## What is Azure AD cloud-native CBA?

Prior to this feature bringing cloud-managed support for CBA natively to Azure AD, customers had to implement federated, certificate-based authentication by deploying Active Directory Federation Services (AD FS) to be able to authenticate using X.509 certificates against Azure AD. With cloud-native certificate-based authentication, customers will be able to authenticate directly against Azure AD and eliminate the need for federated AD FS thereby helping simplify their environments and reduce costs.

The following images show how cloud-native CBA simplifies the customer environment by eliminating federated AD FS. 

**Certificate-based authentication with federated AD FS**

:::image type="content" border="false" source="./media/concept-cloud-native-certificate-based-authentication/cert-with-federation.png" alt-text="Diagram of certificate-based authentication with federation.":::

**Cloud-native certificate-based authentication**

:::image type="content" border="false" source="./media/concept-cloud-native-certificate-based-authentication/cloud-native-cert.png" alt-text="Diagram of cloud-native certificate-based authentication.":::


## Key benefits of using Azure ADcloud-native CBA

| Benefits | Description |
|---------|---------|
| Great user experience |- Users who need certificate-based authentication can now directly authenticate against Azure AD and not have to invest in federated AD FS.<br>- Portal UI enables users to easily configure how to map certificate fields to a user object attribute to look up the user in the tenant ([certificate username bindings](concept-cloud-native-certificate-based-authentication-technical-deep-dive.md#understanding-the-username-binding-policy))<br>- Portal UI to [configure authentication policies](concept-cloud-native-certificate-based-authentication-technical-deep-dive.md#understanding-the-authentication-binding-policy) to help determine which certificates are single-factor versus multifactor. |
| Easy to deploy and administer |- No need for complex on-premises deployments or network configuration.<br>- Directly authenticate against Azure AD. <br>- No management overhead or cost. |
| Secure |- On-premises passwords need not be stored in the cloud in any form.<br>- Protects your user accounts by working seamlessly with Azure AD Conditional Access policies, including multifactor authentication (MFA) and blocking legacy authentication.<br>- Strong authentication support where users can define authentication policies through the certificate fields like issuer or policy OID (object identifiers) to determine which certificates qualify as single-factor versus multifactor. |

## Feature highlights

- Facilitates onboarding to Azure quickly without being delayed by additional on-premises infrastructure to support certificate-based authentication in public and United States Government clouds. 
- Provides support for unphishable multifactor authentication.
- Supports user sign-in against cloud Azure AD using X.509 certificates into all web browser-based applications and into Microsoft Office client applications that use modern authentication.
- The feature works seamlessly with Conditional Access features such as MFA to help secure your users.
- It is a free feature, and you do not need any paid editions of Azure AD to use it.
- Eliminates the need for federated AD FS and reduces the cost and on-premises footprint.

## Next steps

- [Technical deep dive for cloud-native CBA](concept-cloud-native-certificate-based-authentication-technical-deep-dive.md)   
- [Limitations with cloud-native CBA](concept-cloud-native-certificate-based-authentication-limitations.md)
- [How to configure cloud-native CBA](how-to-certificate-based-authentication.md)
- [FAQ](cloud-native-certificate-based-authentication-faq.yml)
- [Troubleshoot cloud-native CBA](troubleshoot-cloud-native-certificate-based-authentication.md)

