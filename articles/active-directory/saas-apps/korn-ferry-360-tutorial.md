---
title: 'Tutorial: Azure AD SSO integration with Korn Ferry 360'
description: Learn how to configure single sign-on between Azure Active Directory and Korn Ferry 360.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/21/2022
ms.author: jeedes
---

# Tutorial: Azure AD SSO integration with Korn Ferry 360

In this tutorial, you'll learn how to integrate Korn Ferry 360 with Azure Active Directory (Azure AD). When you integrate Korn Ferry 360 with Azure AD, you can:

* Control in Azure AD who has access to Korn Ferry 360.
* Enable your users to be automatically signed-in to Korn Ferry 360 with their Azure AD accounts.
* Manage your accounts in one central location.

## Prerequisites

To get started, you need the following items:

* An Azure AD subscription. If you don't have a subscription, you can get a [free account](https://azure.microsoft.com/free/).
* Korn Ferry 360 single sign-on (SSO) enabled subscription.

## Scenario description

In this tutorial, you configure and test Azure AD SSO in a test environment.

* Korn Ferry 360 supports **SP** initiated SSO.

## Add Korn Ferry 360 from the gallery

To configure the integration of Korn Ferry 360 into Azure AD, you need to add Korn Ferry 360 from the gallery to your list of managed SaaS apps.

1. Sign in to the [Microsoft Entra admin center](https://entra.microsoft.com) as at least a [Cloud Application Administrator](../roles/permissions-reference.md#cloud-application-administrator).
1. Browse to **Identity** > **Applications** > **Enterprise applications** > **New application**.
1. In the **Add from the gallery** section, type **Korn Ferry 360** in the search box.
1. Select **Korn Ferry 360** from results panel and then add the app. Wait a few seconds while the app is added to your tenant.

 Alternatively, you can also use the [Enterprise App Configuration Wizard](https://portal.office.com/AdminPortal/home?Q=Docs#/azureadappintegration). In this wizard, you can add an application to your tenant, add users/groups to the app, assign roles, as well as walk through the SSO configuration as well. [Learn more about Microsoft 365 wizards.](/microsoft-365/admin/misc/azure-ad-setup-guides)

## Configure and test Azure AD SSO for Korn Ferry 360

Configure and test Azure AD SSO with Korn Ferry 360 using a test user called **B.Simon**. For SSO to work, you need to establish a link relationship between an Azure AD user and the related user in Korn Ferry 360.

To configure and test Azure AD SSO with Korn Ferry 360, perform the following steps:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - to enable your users to use this feature.
    1. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with B.Simon.
    1. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable B.Simon to use Azure AD single sign-on.
1. **[Configure Korn Ferry 360 SSO](#configure-korn-ferry-360-sso)** - to configure the single sign-on settings on application side.
    1. **[Create Korn Ferry 360 test user](#create-korn-ferry-360-test-user)** - to have a counterpart of B.Simon in Korn Ferry 360 that is linked to the Azure AD representation of user.
1. **[Test SSO](#test-sso)** - to verify whether the configuration works.

## Configure Azure AD SSO

Follow these steps to enable Azure AD SSO.

1. Sign in to the [Microsoft Entra admin center](https://entra.microsoft.com) as at least a [Cloud Application Administrator](../roles/permissions-reference.md#cloud-application-administrator).
1. Browse to **Identity** > **Applications** > **Enterprise applications** > **Korn Ferry 360** > **Single sign-on**.
1. On the **Select a single sign-on method** page, select **SAML**.
1. On the **Set up single sign-on with SAML** page, click the pencil icon for **Basic SAML Configuration** to edit the settings.

   ![Edit Basic SAML Configuration](common/edit-urls.png)

1. On the **Basic SAML Configuration** section, perform the following steps:

    a. In the **Identifier** text box, type a URL using the following pattern:
    `https://<customidentifier>.kornferry.com/`

     b. In the **Sign-on URL** text box, type a URL using the following pattern:
    `https://surveys.kornferry.com/<customidentifier>`

    > [!NOTE]
    > These values are not real. Update these values with the actual Identifier and Sign-on URL. Contact [Korn Ferry 360 Client support team](mailto:george.gold@kornferry.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section.

1. On the **Set up single sign-on with SAML** page, in the **SAML Signing Certificate** section,  find **Certificate (Base64)** and select **Download** to download the certificate and save it on your computer.

    ![The Certificate download link](common/certificatebase64.png)

1. On the **Set up Korn Ferry 360** section, copy the appropriate URL(s) based on your requirement.

    ![Copy configuration URLs](common/copy-configuration-urls.png)

### Create an Azure AD test user

In this section, you'll create a test user called B.Simon.

1. Sign in to the [Microsoft Entra admin center](https://entra.microsoft.com) as at least a [User Administrator](../roles/permissions-reference.md#user-administrator).
1. Browse to **Identity** > **Users** > **All users**.
1. Select **New user** > **Create new user**, at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Display name** field, enter `B.Simon`.  
   1. In the **User principal name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Select **Review + create**.
1. Select **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use single sign-on by granting access to Korn Ferry 360.

1. Sign in to the [Microsoft Entra admin center](https://entra.microsoft.com) as at least a [Cloud Application Administrator](../roles/permissions-reference.md#cloud-application-administrator).
1. Browse to **Identity** > **Applications** > **Enterprise applications** > **Korn Ferry 360**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.
1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.
1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure Korn Ferry 360 SSO

To configure single sign-on on **Korn Ferry 360** side, you need to send the downloaded **Certificate (Base64)** and appropriate copied URLs from the application configuration to [Korn Ferry 360 support team](mailto:george.gold@kornferry.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create Korn Ferry 360 test user

In this section, you create a user called B.Simon in Korn Ferry 360. Work with [Korn Ferry 360 support team](mailto:george.gold@kornferry.com) to add the users in the Korn Ferry 360 platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration with following options. 

* Click on **Test this application**, this will redirect to Korn Ferry 360 Sign-on URL where you can initiate the login flow. 

* Go to Korn Ferry 360 Sign-on URL directly and initiate the login flow from there.

* You can use Microsoft My Apps. When you click the Korn Ferry 360 tile in the My Apps, this will redirect to Korn Ferry 360 Sign-on URL. For more information, see [Azure AD My Apps](/azure/active-directory/manage-apps/end-user-experiences#azure-ad-my-apps).

## Next steps

Once you configure Korn Ferry 360 you can enforce session control, which protects exfiltration and infiltration of your organization’s sensitive data in real time. Session control extends from Conditional Access. [Learn how to enforce session control with Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
