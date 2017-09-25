---

title: Register using CSP Partner information with Azure Cost Management | Microsoft Docs
description: Use your CSP Partner information to register with Azure Cost Management by Cloudyn.
services: cost-management
keywords:
author: bandersmsft
ms.author: banders
ms.date: 09/15/2017
ms.topic: hero-article
ms.custom: mvc
ms.service: cost-management
manager: carmonm
---


# Register with the CSP Partner program and view cost data

As a CSP partner, you can register with Azure Cost Management by Cloudyn. Your registration provides access to the Cloudyn portal. This quickstart details the registration process needed to create a Cloudyn trial subscription and sign in to the Cloudyn portal. It also shows you how to start viewing cost data right away.

To complete registration, you must be a partner program administrator with access to the Partner Center API. Configuration of the Partner Center API is required for authentication and data access. For more information, see Connect to the Partner Center API.

## Log in to Azure

- Log in to the Azure portal at http://portal.azure.com.

## Create a trial registration

1. In the Azure portal, click **Cost Management + Billing** in the list of services.
2. Under **Overview**, click **Cost Management**  
    ![Cost Management page](./media/quick-register-csp/cost-mgt-billing-service.png)
3. On the **Cost Management** page, click **Go to Cost Management** to open the Cloudyn registration page in a new window.
4. On the Cloudyn portal trial registration page, type your company name, select **Microsoft CSP Partner Program Administrator**, and then click **Next**.  
5. Enter an **Application ID**, **Commerce ID**, **Application Secret key**, and select the **Default Pricing Plan**. If you don't have the information handy, sign in to the Partner Center portal at  [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) with your primary administrator account and do the following steps:
  1. Go to **Dashboard** > **Account Settings** > **App Management**.
  2. If you have previously created a Web App, skip this step. Otherwise, click **Add new web app** in the **Web App** section.
  3. Copy the **App ID** GUID from your web application.
  4. Select the key validity duration as one or two years, as needed. Select **Add key** and then copy and save the secret key value.  
    ![CSP Partner Center](./media/quick-register-csp/csp-partner-center.png)
  5. Go back to the registration page and paste the information.  
      ![CSP account credentials](./media/quick-register-csp/csp-reg.png)
6. Agree to the Terms of Use then validate your information. Click **Next** to authorize Cloudyn to collect Azure resource data. Data collected includes usage, performance, billing, and tag data from your subscriptions.  
7. Under **Invite other stakeholders**, you can add users by typing their email addresses. When complete, click **Next**. It takes about two hours for all your billing data to get added to Cloudyn.
8. Click **Go to Cloudyn** to open the Cloudyn portal and then on the **Cloud Accounts Management** page, you should see your registered CSP account information.

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## Next steps

In this quickstart, you used your CSP information to register with Cost Management. You also signed into the Cloudyn portal and started viewing cost data. To learn more about Azure Cost Management by Cloudyn, continue to the tutorial for Cost Management.

> [!div class="nextstepaction"]
> [View cost data](./tutorial-review-usage.md)
