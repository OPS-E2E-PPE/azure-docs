---
title: 'Azure Active Directory Terms of Use| Microsoft Docs'
description: Azure AD Terms of Use will allow you and your company the ability to provide terms of use to users of Azure AD servcies.
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2017
ms.author: billmath

---

# Azure Active Directory Terms of Use (Preview)
Azure AD Terms of Use provides a simple method organizations can use to present information to end users.  This ensures users see relevant disclaimers for legal or compliance requirements.

Azure AD Terms of Use uses the pdf format to present content.   This pdf can be any content, such as existing contract documents, allowing you to collect end user agreements during user sign-in.  You can use the terms of use for applications, groups of users, or if you have multiple terms of use for different purposes.

The remainder of this document describes how to get going with Azure AD Terms of Use.  

## Why use Azure AD Terms of Use
Finding it difficult to get employee’s or guests to agree to your terms of use before getting access? Need help figuring out who has or hasn’t agreed to your company terms of use?  Azure AD Terms of Use provides a simple method organizations can use to present information to end users.  This ensures that they see relevant disclaimers for legal or compliance requirements.

Azure AD Terms of Use can be used in the following scenarios:
-	General terms of use for all users in your organization.
-	Specific terms of use based on a user attributes (ex. doctors vs nurses or domestic vs international employees) (using dynamic groups).
-	Specific terms of use based on accessing high business impact apps, like Salesforce.


## Prerequisites
Use the following steps to configure Azure AD Terms of Use:

1. Sign in to Azure AD using a global administrator, security administrator, or a conditional access administrator for the directory you want to configure Azure AD Terms of Use.
2. Ensure that the directory has an Azure AD Premium P1, P2, EMS E3, or EMS E5 subscription.  If you do not [Get Azure AD Premium](active-directory-get-started-premium.md) or [start a trial](https://azure.microsoft.com/trial/get-started-active-directory/).
3. View the Azure AD Terms of User dashboard at [https://aka.ms/catou](https://aka.ms/catou).



## Add Terms of Use
Once you have finalized your Terms of Use, use the following procedure to add it.

### To add Terms of Use
1. Navigate to the dashboard at [https://aka.ms/catou](https://aka.ms/catou)
2. Click Add.</br>
![Add TOU](media/active-directory-tou/tou2.png)
3. Enter the **Name** for the Terms of Use
4. Enter **Display Name**.  This is the header that users see when they sign in.
5. **Browse** to your finalized terms of use pdf and select it.
6. You can **Enforce** the terms of use by using a template or a custom conditional access policy.
7. Click **Create**.
8. If you selected a custom conditional access template, then a new screen appears which allows you to customize the CA policy.
7. You should now see your new Terms of Use.</br>

![Add TOU](media/active-directory-tou/tou3.png)

## Delete Terms of Use
You can remove or delete old terms of use using the following procedure:

### To delete Terms of Use
1. Navigate to the dashboard at [https://aka.ms/catou](https://aka.ms/catou)
2. Select the terms of use you want to remove.
3. Click **Delete**.
4. You should no longer see your new terms of use.


## Audit Terms of Use
Azure AD Terms of Use provides easy to use auditing so that you can see who has accepted and when they accepted your terms of use.  To get started with auditing use the following procedure:

### To audit Terms of Use
1. Navigate to the dashboard at [https://aka.ms/catou](https://aka.ms/catou)
2. Click Audit Event.</br>
![Audit Event](media/active-directory-tou/tou8.png)
3.  On the Azure AD audit logs screen, you can filter the information using the provided drops downs to target specific audit log information.
![Audit Event](media/active-directory-tou/tou9.png)
4.  You can also download the information in a .csv file for use locally.

## Preview for end users
Users, who are in scope, will see the following once a terms of use is created and enforced.
-	Best practice is to have the font within the PDF at size 24.
![Audit Event](media/active-directory-tou/tou10.png)
-	This screen is how it appears on mobiles
![Audit Event](media/active-directory-tou/tou11.png)

## Additional information
The following information is something to be aware of and can assist with using terms of use.

-	Global administrator, security administrator, or conditional access administrator need read/write access.

-	After creating a Terms of Use, using the “access to cloud apps” template, the admin starts to see “sad clouds” when access other areas of the portal. If they refresh the browser, they start to see Azure AD token issues
    - This behavior is expected.
    - **Reason**: the user created a new conditional access policy that they are in scope of. The new policy is not satisfied so they are unable to access any cloud apps until that policy in satisfied.
    - **Resolution**: in order to resolve the user must sign out and sign back in. 

- If a tenant already has a conditional access policy enforced be aware of the following conditions:
    - The next time a Terms of Use is created the admin will see “sad clouds” when accessing other areas of the portal. 
    - If they refresh the browser, they start to see Azure AD token issues.
    - This behavior is expected.
    - **Reason**: due to limitations in the conditional access extensibility framework.
    - **Resolution**: in order to resolve the user must sign out and sign back in. Users will get a fresh token. 

-	If a Terms of Use is enforced using a custom conditional access policy be aware of the following conditions:
    - if the admin wants to delete a Terms of Use, the admin needs to make sure that it is not enforced with any policies.