---
title: What are Azure Active Directory recommendations? | Microsoft Docs
description: Provides a general overview of Azure Active Directory recommendations.
services: active-directory
author: shlipsey3
manager: amycolannino
ms.service: active-directory
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 02/16/2023
ms.author: sarahlipsey
ms.reviewer: hafowler  
ms.collection: M365-identity-device-management

# Customer intent: As an Azure AD administrator, I want guidance to so that I can keep my Azure AD tenant in a healthy state.

---

# What are Azure Active Directory recommendations?

Keeping track of all the settings and resources in your tenant can be overwhelming. The Azure Active Directory (Azure AD) recommendations feature helps monitor the status of your tenant so you don't have to. The Azure AD recommendations feature helps ensure your tenant is in a secure and healthy state while also helping you maximize the value of the features available in Azure AD.

The Azure AD recommendations feature provides you with personalized insights with actionable guidance to:

- Help you identify opportunities to implement best practices for Azure AD-related features.
- Improve the state of your Azure AD tenant.
- Optimize the configurations for your scenarios.

This article gives you an overview of how you can use Azure AD recommendations. As an administrator, you should review your tenant's Azure AD recommendations, and their associated resources periodically. 

## What it is 

The Azure AD recommendations feature is the Azure AD specific implementation of [Azure Advisor](../../advisor/advisor-overview.md), which is a personalized cloud consultant that helps you follow best practices to optimize your Azure deployments. Azure Advisor analyzes your resource configuration and usage data to recommend solutions that can help you improve the cost effectiveness, performance, reliability, and security of your Azure resources.

*Azure AD recommendations* use similar data to support you with the roll-out and management of Microsoft's best practices for Azure AD tenants to keep your tenant in a secure and healthy state. Azure AD recommendations provide a holistic view into your tenant's security, health, and usage. 
 
## How it works

On a daily basis, Azure AD analyzes the configuration of your tenant. During this analysis, Azure AD compares the data of a recommendation with the actual configuration of your tenant. If a recommendation is flagged as applicable to your tenant, the recommendation appears in the **Recommendations** section of the Azure AD Overview area. The recommendations are listed in order of priority so you can quickly determine where to focus first. 

Each recommendation contains a description, a summary of the value of addressing the recommendation, and a step-by-step action plan. If applicable, impacted resources associated with the recommendation are listed, so you can resolve each affected area. If a recommendation doesn't have any associated resources, the impacted resource type is *Tenant level*. so your step-by-step action plan impacts the entire tenant and not just a specific resource.

![Screenshot of the Overview page of the tenant with the Recommendations option highlighted.](./media/overview-recommendations/recommendations-preview-option-tenant-overview.png)

## Recommendation details

Each recommendation provides the same set of details that explain what the recommendation is, why it's important, and how to fix it.

The **Status** of a recommendation can be updated manually or automatically. If all resources are addressed according to the action plan, the status will automatically change to *Completed* the next time the recommendations service runs. The recommendation service runs every 24-48 hours, depending on the recommendation. 

![Screenshot of the Mark as options.](./media/overview-recommendations/recommendations-object.png)

The **Priority** of a recommendation could be low, medium, or high. These values are determined by several factors, such as security implications, health concerns, or potential breaking changes.

![Screenshot of a recommendation's status, priority, and impacted resource type.](./media/overview-recommendations/recommendation-status-risk.png)

- **High**: Must do. Not acting will result in severe security implications or potential downtime.
- **Medium**: Should do. No severe risk if action isn't taken.
- **Low**: Might do. No security risks or health concerns if action isn't taken.

The **Impacted resources** for a recommendation could be things like applications or users. This detail gives you an idea of what type of resources you need to address. The impacted resource could also be at the tenant level, so you may need to make a global change. 

The **Status description** tells you the date the recommendation status changed and if it was changed by the system or a user.

The recommendation's **Value** is an explanation of why completing the recommendation will benefit you, and the value of the associated feature. 

The **Action plan** provides step-by-step instructions to implement a recommendation. May include links to relevant documentation or direct you to other pages in the Azure AD portal.

## Roles and licenses

The following roles provide *read-only* access to recommendations:

- Reports Reader
- Security Reader
- Global Reader

The following roles provide *update and read-only* access to recommendations:

- Global Administrator
- Security Administrator
- Security Operator
- Cloud apps Administrator
- Apps Administrator

The Azure AD recommendations feature is automatically enabled. If you'd like to disable this feature, go to **Azure AD** > **Preview features**. Locate the **Recommendations** feature, and change the **State**.

Azure AD only displays the recommendations that apply to your tenant, so you may not see all supported recommendations listed. Currently, all recommendations are available in all tenants, regardless of the license type.

### Recommendations available for all Azure AD tenants

The recommendations listed in the following table are available to all Azure AD tenants. The table provides the impacted resources and links to available documentation.

| Recommendation  | Impacted resources | Availability |
|---- |---- |---- |
| [Convert per-user MFA to Conditional Access MFA](recommendation-turn-off-per-user-mfa.md) | Users | Generally available |
| [Migrate applications from AD FS to Azure AD](recommendation-migrate-apps-from-adfs-to-azure-ad.md) | Users | Generally available |
| [Migrate to Microsoft Authenticator](recommendation-migrate-to-authenticator.md) | Users | Preview |
| [Minimize MFA prompts from known devices](recommendation-migrate-apps-from-adfs-to-azure-ad.md)  | Users | Generally available |

## How to use Azure AD recommendations

1. Go to **Azure AD** > **Recommendations**.

1. Select a recommendation from the list to view the details, status, and action plan. 

    ![Screenshot of the list of recommendations.](./media/overview-recommendations/recommendations-list.png)

1. Follow the **Action plan**.

1. If applicable, *right-click on the status* of a resource in a recommendation, select **Mark as**, then select a status.

    - The status for the resource appears as regular text, but you can right-click on the status to open the menu.
    - You can set each resource to a different status as needed.
    
    ![Screenshot of the status options for a resource.](./media/overview-recommendations/resource-mark-as-option.png)

1. The recommendation service automatically marks the recommendation as complete, but if you need to manually change the status of a recommendation, select **Mark as** from the top of the page and select a status.

    ![Screenshot of the Mark as options, to highlight the difference from the resource menu.](./media/overview-recommendations/recommendations-object.png)

    - Mark a recommendation as **Completed** if all impacted resources have been addressed.
        - Active resources may still appear in the list of resources for manually completed recommendations. If the resource is completed, the service will update the status the next time the service runs. 
        - If the service identifies an active resource for a manually completed recommendation the next time the service runs, the recommendation will automatically change back to **Active**.
        - Completing a recommendation is the only action collected in the audit log. To view these logs, go to **Azure AD** > **Audit logs** and filter the service to "Azure AD recommendations."
    - Mark a recommendation as **Dismissed** if you think the recommendation is irrelevant or the data is wrong.
        - Azure AD will ask for a reason why you dismissed the recommendation so we can improve the service.
    - Mark a recommendation as **Postponed** if you want to address the recommendation at a later time.
        - The recommendation will become **Active** when the selected date occurs.
    - You can reactivate a completed or postponed recommendation to keep it top of mind and reassess the resources.

Continue to monitor the recommendations in your tenant for changes.

### Use Microsoft Graph with Azure Active Directory recommendations

Azure Active Directory recommendations can be viewed and managed using Microsoft Graph on the `/beta` endpoint. You can view recommendations along with their impacted resources, mark a recommendation as completed by a user, postpone a recommendation for later, and more. 

To get started, follow these instructions to work with recommendations using Microsoft Graph in Graph Explorer. The example uses the Migrate apps from Active Directory Federated Services (ADFS) to Azure AD recommendation.

1. Sign in to [Graph Explorer](https://aka.ms/ge).
1. Select **GET** as the HTTP method from the dropdown.
1. Set the API version to **beta**.
1. Add the following query to retrieve recommendations, then select the **Run query** button.

    ```http
    GET https://graph.microsoft.com/beta/directory/recommendations
    ```

1. To view the details of a specific `recommendationType`, use the following API. This example retrieves the detail of the "Migrate apps from AD FS to Azure AD" recommendation.

    ```http
    GET https://graph.microsoft.com/beta/directory/recommendations?$filter=recommendationType eq 'adfsAppsMigration'
    ```

1. To view the impacted resources for a specific recommendation, expand the `impactedResources` relationship.

    ```http
    GET https://graph.microsoft.com/beta/directory/recommendations?$filter=recommendationType eq 'adfsAppsMigration'&$expand=impactedResources
    ```

For more information, see the [Microsoft Graph documentation for recommendations](/graph/api/resources/recommendations-api-overview).

## Next steps

* [Learn more about Microsoft Graph](/graph/overview)
* [Get started with Azure AD reports](overview-reports.md)
* [Learn about Azure AD monitoring](overview-monitoring.md)
