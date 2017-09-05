---
title: Conditional access with mobile app management in Azure Active Directory | Microsoft Docs
description: Learn how Conditional access with mobile app management in Azure Active Directory works.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol

---
# Conditional access with mobile app management in Azure Active Directory  

Azure Active Directory (Azure AD) app-based conditional access in the Azure portal combined with Intune app protection policies help restrict access to cloud apps to mobile apps that support Intune app protection, for example restricting access to Exchange Online to the Outlook app. This support allows devices that are not enrolled for management by Intune MDM to still protect company data.   

Mobile app management conditional access allows you to combine with other policies such as device-based conditional access policies to provide flexibility in how to protect data for both personal and corporate devices. 

##Before you begin

This topic assumes that you are familiar with:

- The basic concepts of [conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal.md).

- How to [configure a conditional access policy](active-directory-conditional-access-azure-portal-get-started.md).


Additionally, you might want to take a look at the [best practices for conditional access in Azure Active Directory
](active-directory-conditional-access-best-practices.md).  



## Prerequisites

1.	Before you create an app-based conditional access policy, you must have an Enterprise Mobility + Security or an Azure Active Directory premium subscription, and the users must be licensed for EMS or Azure AD. 
2.	Before you create a new conditional access with mobile app management policy, you must review the scenarios and the migration considerations

## Supported platforms

-	iOS

-	Android

## Approved client applications 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Microsoft Teams

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## Exchange Online policy 

This scenario consists of a conditional access with mobile app management policy for access to Exchange Online with approved apps.


### Scenario playbook

This scenario assumes that a user:

- Configures email using a native mail application on iOS or Android to connect to Exchange

- Receives an email that indicates that access is only available using Outlook app

- Downloads the application with the link

- Opens the Outlook application and signs in with the Azure AD credentials

- Is prompted to install either Authenticor (iOS) or Company Portal (Android) to continue

- Installs the application and can return to the Outlook app to continue

- Is prompted to register a device

- Is able to access email

Any Intune app protection policies are activated at the time the access corporate data and may prompt the user to restart the application, use an additional PIN etc (if configured for the application and platform).

### Configuration 

**Step 1 - Configure an Azure AD conditional access policy for Exchange Online**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/01.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.

3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online**.

    ![Conditional access](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions:** As **Conditions**, you need to configure **Device platforms** and **Client apps**:

    a. As **Device platforms**, select **Android** and **iOS**.

    ![Conditional access](./media/active-directory-conditional-access-mam/03.png)

    b. As **Client apps**, select **Mobile apps and desktop apps**.

    ![Conditional access](./media/active-directory-conditional-access-mam/04.png)

5. As **Access controls**, you need to have **Require approved client app (preview)** selected.

    ![Conditional access](./media/active-directory-conditional-access-mam/05.png)
 

**Step 2 - Configure an Azure AD conditional access policy for Exchange Online with Active Sync (EAS)**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/06.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.


3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online**.

    ![Conditional access](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions:** As **Conditions**, you need to configure **Client apps**. 

    a. As **Client apps**, select **Exchange Active Sync**.

    ![Conditional access](./media/active-directory-conditional-access-mam/08.png)

    b. As **Access controls**, you need to have **Require approved client app (preview)** selected.

    ![Conditional access](./media/active-directory-conditional-access-mam/05.png)


**Step 3 - Configure Intune app protection policy for iOS and Android client applications**


![Conditional access](./media/active-directory-conditional-access-mam/09.png)

See [Protect apps and data with Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) for more information.


## Exchange Online and SharePoint Online policy

This scenario consists of a conditional access with mobile app management policy for access to Exchange Online and SharePoint Online with approved apps.

### Scenario playbook

This scenario assumes that a user:

- Tries to use the SharePoint app to connect and also to view their corporate sites

- Attempt to sign-in with the same credentials as the Outlook app credentials

- Does not have to re-register and can get access to the resources


### Configuration

**Step 1 - Configure an Azure AD conditional access policy for Exchange Online and SharePoint Online**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/71.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.


3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online** and **Office 365 SharePoint Online**. 

    ![Conditional access](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions:** As **Conditions**, you need to configure **Device platforms** and **Client apps**:

    a. As **Device platforms**, select **Android** and **iOS**.

    ![Conditional access](./media/active-directory-conditional-access-mam/03.png)

    b. As **Client apps**, select **Mobile apps and desktop apps**.

    ![Conditional access](./media/active-directory-conditional-access-mam/04.png)

5. As **Access controls**, you need to have **Require approved client app (preview)** selected.

    ![Conditional access](./media/active-directory-conditional-access-mam/05.png)




**Step 2 - Configure an Azure AD conditional access policy for Exchange Online with Active Sync (EAS)**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/06.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.

3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online**. Online 

    ![Conditional access](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions:** As **Conditions**, you need to configure **Client apps**:

    a. As **Client apps**, select **Exchange Active Sync**.

    ![Conditional access](./media/active-directory-conditional-access-mam/08.png)

    b. As **Access controls**, you need to have **Require approved client app (preview)** selected.

    ![Conditional access](./media/active-directory-conditional-access-mam/05.png)




**Step 3 - Configure Intune app protection policy for iOS and Android client applications**


![Conditional access](./media/active-directory-conditional-access-mam/09.png)

See [Protect apps and data with Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) for more information.


## Mobile application management or compliant device policy for Exchange Online and SharePoint Online

This scenario consists of a conditional access with mobile app management or compliant device policy for access to Exchange Online with approved apps.


### Scenario playbook

This scenario assumes that:
 
- Some user are already enrolled (with or without corporate devices)

- Users who are not enrolled and registered with Azure AD using an app protected application need to register a device to access resources

- Enrolled users using the app protected application don't have to re-register the device


### Configuration

**Step 1 - Configure an Azure AD conditional access policy for Exchange Online and SharePoint Online**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/62.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.

3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online** and **Office 365 SharePoint Online**. 

     ![Conditional access](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions:** As **Conditions**, you need to configure **Device platforms** and **Client apps**. 
 
    a. As **Device platforms**, select **Android** and **iOS**.

    ![Conditional access](./media/active-directory-conditional-access-mam/03.png)

    b. As **Client apps**, select **Mobile apps and desktop apps**.

    ![Conditional access](./media/active-directory-conditional-access-mam/04.png)

5. As **Access controls**, you need to have the following selected:

    - **Require device to be marked as compliant**

    - **Require approved client app (preview)**

    - **Require one of the selected controls**   
 
    ![Conditional access](./media/active-directory-conditional-access-mam/11.png)



**Step 2 - Configure an Azure AD conditional access policy for Exchange Online with Active Sync (EAS)**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/61.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.

3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online**. 

    ![Conditional access](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions:** As **Conditions**, you need to configure **Client apps**. 

    As **Client apps*, select **Exchange Active Sync**.

    ![Conditional access](./media/active-directory-conditional-access-mam/08.png)

5. As **Access controls**, you need to have **Require approved client app (preview)** selected.
 
    ![Conditional access](./media/active-directory-conditional-access-mam/11.png)




**Step 3 - Configure Intune app protection policy for iOS and Android client applications**


![Conditional access](./media/active-directory-conditional-access-mam/09.png)

See [Protect apps and data with Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) for more information.





## Mobile application management and compliant device policy for Exchange Online and SharePoint Online

This scenario consists of a conditional access with mobile app management and compliant device policy for access to Exchange Online with approved apps.

### Scenario playbook

This scenario assumes that a user:
 
-	Configures email using a native mail application on iOS or Android to connect to Exchange
-	Receives an email that indicates that access requires your device to be enrolled
-	Downloads the company portal and signs in to company portal
-	Checks mail and is asked to use the Outlook app
-	Downloads the Outlook app
-	Opens the Outlook app and enters the credentials used in the enrollment
-	User is able to access email

Any Intune app protection policies are activated at the time of access to the corporate data and may prompt the user to restart the application, use an additional PIN etc. (if configured for the application and platform)


### Configuration

**Step 1 - Configure an Azure AD conditional access policy for Exchange Online and SharePoint Online**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/62.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.

3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online** and **Office 365 SharePoint Online**. 

     ![Conditional access](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions:** As **Conditions**, you need to configure **Device platforms** and **Client apps**. 
 
    a. As **Device platforms**, select **Android** and **iOS**.

    ![Conditional access](./media/active-directory-conditional-access-mam/03.png)

    b. As **Client apps**, select **Mobile apps and desktop apps**.

    ![Conditional access](./media/active-directory-conditional-access-mam/04.png)

5. As **Access controls**, you need to have the following selected:

    - **Require device to be marked as compliant**

    - **Require approved client app (preview)**

    - **Require one of the selected controls**   
 
    ![Conditional access](./media/active-directory-conditional-access-mam/11.png)



**Step 2 - Configure an Azure AD conditional access policy for Exchange Online with Active Sync (EAS)**

For the conditional access policy in this step, you need to configure the following components:

![Conditional access](./media/active-directory-conditional-access-mam/61.png)

1. The **Name** of your conditional access policy.

2. **Users and groups**: Each conditional access policy must have at least one user or group selected.

3. **Cloud apps:** As cloud apps, you need to select **Office 365 Exchange Online**. 

    ![Conditional access](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions:** As **Conditions**, you need to configure **Client apps**. 

    As **Client apps**, select **Exchange Active Sync**.

    ![Conditional access](./media/active-directory-conditional-access-mam/08.png)

5. As **Access controls**, you need to have the following selected:

    - **Require device to be marked as compliant**

    - **Require approved client app (preview)**

    - **Require all the selected controls**   
 
    ![Conditional access](./media/active-directory-conditional-access-mam/64.png)




**Step 3 - Configure Intune app protection policy for iOS and Android client applications**


![Conditional access](./media/active-directory-conditional-access-mam/09.png)

See [Protect apps and data with Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune) for more information.



## Migration considerations

If you have policies in the Azure classic portal configured, you should migrate them to the Azure portal because:


- A user who is in an Azure classic portal policy and an Azure portal policy needs to meet the requirements in both policies 

- If you don't migrate your existing policies, you will not be able to implement policies that are granting access


## Migration from the Azure classic portal

In this scenario: 

- In your [Azure classic portal](https://manage.windowsazure.com), you have configured:

    - SharePoint Online

    ![Conditional access](./media/active-directory-conditional-access-mam/14.png)

    - A device-based conditional access policy

    ![Conditional access](./media/active-directory-conditional-access-mam/15.png)

- You want to configure a mobile application management conditional access policy in the Azure portal 
 

### Configuration 

- Review your device-based conditional access policies

- Migrate them to the Azure portal 

- Add mobile application management conditional access policies


## Migrating from Intune 

In this scenario:

- In [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), you have a mobile application management conditional access policy for either Exchange Online or SharePoint Online configured

    ![Conditional access](./media/active-directory-conditional-access-mam/15.png)

- You want to migrate to using mobile application management conditional access in the Azure portal


### Configuration 
 
- Review your device-based conditional access policies

- Migrate them to the Azure portal 

- Review you mobile application management conditional access policies configured for Exchange Online or SharePoint Online in Intune

- Add the control for **Require approved applications** in addition to the device-based control 
 

## Migrating from the Azure classic portal and Intune

In this scenario:

- You have the following configured:

    - **Azure classic portal:** Device-based conditional 

    - **Intune:** Mobile application management conditional access policies 
    
- You want to migrate both policies to using mobile application management conditional access policies in the Azure portal


### Configuration

- Review your device-based conditional access policies

- Migrate them to the Azure portal 

- Review you mobile application management conditional access policy configured for Exchange Online or SharePoint Online in Intune

- Add the control for **Require approved applications** in addition to the device-based 




## Next steps

If you want to know how to configure a conditional access policy, see [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

If you are ready to configure conditional access policies for your environment, see the [best practices for conditional access in Azure Active Directory](active-directory-conditional-access-best-practices.md). 