---
title: Manage Azure savings plans
titleSuffix: Microsoft Cost Management
description: Learn how to manage savings plans. See steps to change the plan's scope, split a plan, and optimize its use.
author: bandersmsft
ms.reviewer: onwokolo
ms.service: cost-management-billing
ms.subservice: savings-plan
ms.topic: how-to
ms.date: 11/17/2023
ms.author: banders
---

# Manage Azure savings plans
After you buy an Azure savings plan, with sufficient permissions, you can make the following types of changes to a savings plan:

- Update a savings plan scope.
- Change autorenewal settings.
- View savings plan details and utilization.
- Delegate savings plan role-based access control (RBAC) roles.

Except for autorenewal, none of the changes causes a new commercial transaction or changes the end date of the savings plan.

You can't make the following types of changes after purchase:
- Hourly commitment
- Term length
- Billing frequency

To learn more, see [Savings plan permissions](permission-view-manage.md). _Permission needed to manage a savings plan is separate from subscription permission._

## Change the savings plan scope
Your hourly savings plan benefit is to automatically use from savings plan-eligible resources that run in the savings plan's benefit scope. To learn more, see [Savings plan scopes](scope-savings-plan.md). Changing a savings plan's benefit scope doesn't alter the savings plan's term.

To update a savings plan scope as a billing administrator:
1. Sign in to the Azure portal and go to **Cost Management + Billing**.
    - If you're an Enterprise Agreement admin, on the left menu, select **Billing scopes**. Then in the list of billing scopes, select one.
    - If you're a Microsoft Customer Agreement billing profile owner, on the left menu, select **Billing profiles**. In the list of billing profiles, select one.
1. On the left menu, select **Products + services** > **Savings plans**. The list of savings plans for your Enterprise Agreement enrollment or billing profile appears.
1. Select the savings plan you want.
1. Select **Settings** > **Configuration**.
1. Change the scope.

If you purchased a savings plan, were added to a savings plan, or were assigned one or more savings plan RBAC roles, follow these steps to update a savings plan scope.
1. Sign in to the Azure portal.
1. Select **All Services** > **Savings plans** to list savings plans to which you have access.
1. Select the savings plan you want.
1. Select **Settings** > **Configuration**.
1. Change the scope.

Selectable scopes must be from Enterprise offers (MS-AZR-0017P or MS-AZR-0148P), Microsoft Customer Agreements, and Microsoft Partner Agreements.

If you aren't a billing administrator and you change from shared to single scope, you may only select a subscription where you're the owner. Only subscriptions within the same billing account/profile as the savings plan can be selected.

If all subscriptions are moved out of a management group, the scope of the savings plan is automatically changed to **Shared**.
## Change the autorenewal setting
To update the autorenewal setting as a billing administrator:
1. Sign in to the Azure portal and go to **Cost Management + Billing**.
    - If you're an Enterprise Agreement admin, on the left menu, select **Billing scopes**. Then in the list of billing scopes, select one.
    - If you're a Microsoft Customer Agreement billing profile owner, on the left menu, select **Billing profiles**. In the list of billing profiles, select one.
1. On the left menu, select **Products + services** > **Savings plans**. The list of savings plans for your Enterprise Agreement enrollment or billing profile appears.
1. Select the savings plan you want.
1. Select **Settings** > **Renewal**.

If you purchased a savings plan, were added to a savings plan, or were assigned one or more savings plan RBAC roles, follow these steps to update the autorenewal setting.
1. Sign in to the Azure portal.
1. Select **All Services** > **Savings plans** to list savings plans to which you have access.
1. Select the savings plan you want.
1. Select **Settings** > **Renewal**.

## View savings plan details and utilization
If you purchased a savings plan, were added to a savings plan, or were assigned one or more savings plan RBAC roles, follow these steps to view savings plan details and utilization.
1. Sign in to the Azure portal.
1. Select **All Services** > **Savings plans** to list savings plans to which you have access.
1. Select the savings plan you want.
1. To rename the savings plan, select **Rename**. To view payment history or upcoming payments, select the link to the right of **Billing frequency**.

## Delegate savings plan RBAC roles
Users and groups who gain the ability to purchase, manage, or view savings plans via RBAC roles must do so from **Home** > **Savings plan**.

### Delegate the savings plan purchaser role to a specific subscription
To delegate the purchaser role to a specific subscription, and after you have elevated access:
1. Go to **Home** > **Savings plans** to see all savings plans that are in the tenant.
1. To make modifications to the savings plan, add yourself as an owner of the savings plan order by using **Access control (IAM)**.

### Delegate savings plan administrator, contributor, or reader roles to a specific savings plan
To delegate the administrator, contributor, or reader roles to a specific savings plan:
1. Go to **Home** > **Savings plans**.
1. Select the savings plan you want.
1. Select **Access control (IAM)** on the leftmost pane.
1. Select **Add**, and then select **Add role assignment** from the top navigation bar.

### Delegate savings plan administrator, contributor, or reader roles to all savings plans
[User Access administrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) rights are required to grant RBAC roles at the tenant level. To get User Access administrator rights, follow the steps in [Elevate access steps](../../role-based-access-control/elevate-access-global-admin.md).

### Delegate the administrator, contributor, or reader role to all savings plans in a tenant
1. Go to **Home** > **Savings plans**.
1. Select **Role assignment** from the top navigation bar.

## Cancel, exchange, or refund
You can't cancel, exchange, or refund savings plans.

## Transfer a savings plan
Although you can't cancel, exchange, or refund a savings plan, you can transfer it from one supported agreement to another. For more information about supported transfers, see [Azure product transfer hub](../manage/subscription-transfer.md#product-transfer-support).

## Need help?
If you have Azure savings plan for compute questions, contact your account team or [create a support request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Temporarily, Microsoft will only provide answers to expert support requests in English for questions about Azure savings plan for compute.

## Next steps

To learn more about Azure savings plans, see:

- [View savings plan utilization](utilization-cost-reports.md)
- [Cancellation policy](cancel-savings-plan.md)
- [Renew a savings plan](renew-savings-plan.md)
