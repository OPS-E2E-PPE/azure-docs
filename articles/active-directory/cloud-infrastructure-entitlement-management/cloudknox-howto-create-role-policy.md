---
title: Create a role/policy in the Remediation dashboard in CloudKnox Permissions Management 
description: How to create a role/policy in the Remediation dashboard in CloudKnox Permissions Management.
services: active-directory
author: Yvonne-deQ
manager: karenh444
ms.service: active-directory
ms.subservice: ciem
ms.workload: identity
ms.topic: how-to
ms.date: 02/23/2022
ms.author: v-ydequadros
---

# Create a role/policy in the Remediation dashboard

> [!IMPORTANT]
> CloudKnox Permissions Management (CloudKnox) is currently in PREVIEW.
> Some information relates to a prerelease product that may be substantially modified before it's released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

This article describes how you can use the **Remediation** dashboard in CloudKnox Permissions Management (CloudKnox) to create roles/policies for the Amazon Web Services (AWS), Microsoft Azure, or Google Cloud Platform (GCP) authorization systems. 

> [!NOTE]
> To view the **Remediation** tab, you must have **Viewer**, **Controller**, or **Administrator** permissions. To make changes on this tab, you must have **Controller** or **Administrator** permissions. If you don’t have these permissions, contact your system administrator.

> [!NOTE]
> Microsoft Azure uses the term *role* for what other cloud providers call *policy*. CloudKnox automatically makes this terminology change when you select the authorization system type. In the user documentation, we use *role/policy* to refer to both.

## Create a policy for AWS

1. On the CloudKnox home page, select the **Remediation** tab, and then select the **Role/Policies** tab.
1. Use the dropdown lists to select the **Authorization System Type** and **Authorization System**.
1. Select **Create policy**.
1. On the **Details** page, the **Authorization System Type** and **Authorization System** are pre-populated from your previous settings.
    - To change the settings, make a selection from the dropdown. 
1. Under **How would you like to create the policy?**, select the required option:

    - **Activity of user(s)**: Allows you to create a policy based on user activity.
    - **Activity of group(s)**: Allows you to create a policy based on the aggregated activity of all the users belonging to the group(s).
    - **Activity of resource(s)**: Allows you to create a policy based on the activity of a resource, for example, an EC2 instance.
    - **Activity of role**: Allows you to create a policy based on the aggregated activity of all the users that assumed the role.
    - **Activity of tag(s)**: Allows you to create a policy based on the aggregated activity of all the tags.
    - **Activity of Lambda function**: Allows you to create a new policy based on the Lambda function.
    - **From existing policy**: Allows you to create a new policy based on an existing policy.  
    - **New policy**: Allows you to create a new policy from scratch.
1. In **Tasks performed in the last**, select the duration: **90 days**, **60 days**, **30 days**, **7 days**, or **1 day**.
1. Depending on your preference, select or deselect **Include Access Advisor data.**
1. In **Settings**, from the **Available** column, select the plus sign **(+)** to move the identity into the **Selected** column, and then select **Next**.

1. On the **Tasks** page, from the **Available** column, select the plus sign **(+)** to move the task into the **Selected** column.
    - To add a whole category, select a category.
    - To add individual items from a category, select the down arrow on the left of the category name, and then select individual items.
1. In **Resources**, select **All Resources** or **Specific Resources**.

    If you select **Specific Resources**, a list of available resources appears. Find the resources you want to add, and then select **Add**.
1. In **Request Conditions**, select **JSON** .
1. In **Effect**, select **Allow** or **Deny**, and then select **Next**.
1. In **Policy name:**, enter a name for your policy.
1. To add another statement to your policy, select **Add statement**, and then, from the list of **Statements**, select a statement.
1. Review your **Task**, **Resources**, **Request Conditions**, and **Effect** settings, and then select **Next**.


1. On the **Preview** page, review the script to confirm it's what you want.
1. If your controller isn't enabled, select **Download JSON** or **Download Script** to download the code and run it yourself.

    If your controller is enabled, skip this step.
1. Select **Split policy**, and then select **Submit**.

    A message confirms that your policy has been submitted for creation

1. The [**CloudKnox Tasks**](cloudknox-ui-tasks.md) pane appears on the right.
    - The **Active** tab displays a list of the policies CloudKnox is currently processing.
    - The **Completed** tab displays a list of the policies CloudKnox has completed.
1. Refresh the **Role/Policies** tab to see the policy you created.



## Create a role for Azure

1. On the CloudKnox home page, select the **Remediation** tab, and then select the **Role/Policies** tab.
1. Use the dropdown lists to select the **Authorization System Type** and **Authorization System**.
1. Select **Create role**.
1. On the **Details** page, the **Authorization System Type** and **Authorization System** are pre-populated from your previous settings.
    - To change the settings, select the box and make a selection from the dropdown. 
1. Under **How would you like to create the role?**, select the required option:

    - **Activity of user(s)**: Allows you to create a role based on user activity.
    - **Activity of group(s)**: Allows you to create a role based on the aggregated activity of all the users belonging to the group(s).
    - **Activity of app(s)**: Allows you to create a role based on the aggregated activity of all apps.
    - **From existing role**: Allows you to create a new role based on an existing role.  
    - **New role**: Allows you to create a new role from scratch.

1. In **Tasks performed in the last**, select the duration: **90 days**, **60 days**, **30 days**, **7 days**, or **1 day**.
1. Depending on your preference: 
    - Select or deselect **Ignore non-Microsoft read actions**.
    - Select or deselect **Include read-only tasks**.
1. In **Settings**, from the **Available** column, select the plus sign **(+)** to move the identity into the **Selected** column, and then select **Next**.

1. On the **Tasks** page, in **Role name:**, enter a name for your role.
1. From the **Available** column, select the plus sign **(+)** to move the task into the **Selected** column.
    - To add a whole category, select a category.
    - To add individual items from a category, select the down arrow on the left of the category name, and then select individual items.
1. Select **Next**.

1. On the **Preview** page, review:
    - The list of selected **Actions** and **Not actions**.
    - The **JSON** or **Script** to confirm it's what you want.
1. If your controller isn't enabled, select **Download JSON** or **Download Script** to download the code and run it yourself.

    If your controller is enabled, skip this step.

1. Select **Submit**.

    A message confirms that your role has been submitted for creation

1. The [**CloudKnox Tasks**](cloudknox-ui-tasks.md) pane appears on the right.
    - The **Active** tab displays a list of the policies CloudKnox is currently processing.
    - The **Completed** tab displays a list of the policies CloudKnox has completed.
1. Refresh the **Role/Policies** tab to see the role you created.

## Create a role for GCP

1. On the CloudKnox home page, select the **Remediation** tab, and then select the **Role/Policies** tab.
1. Use the dropdown lists to select the **Authorization System Type** and **Authorization System**.
1. Select **Create role**.
1. On the **Details** page, the **Authorization System Type** and **Authorization System** are pre-populated from your previous settings.
    - To change the settings, select the box and make a selection from the dropdown. 
1. Under **How would you like to create the role?**, select the required option:

    - **Activity of user(s)**: Allows you to create a role based on user activity.
    - **Activity of group(s)**: Allows you to create a role based on the aggregated activity of all the users belonging to the group(s).
    - **Activity of service account(s)**: Allows you to create a role based on the aggregated activity of all service accounts.
    - **From existing role**: Allows you to create a new role based on an existing role.  
     - **New role**: Allows you to create a new role from scratch.

1. In **Tasks performed in the last**, select the duration: **90 days**, **60 days**, **30 days**, **7 days**, or **1 day**.
1. If you selected **Activity of service account(s)** in the previous step, select or deselect **Collect activity across all GCP authorization systems.**
1. From the **Available** column, select the plus sign **(+)** to move the identity into the **Selected** column, and then select **Next**.


1. On the **Tasks** page, in **Role name:**, enter a name for your role.
1. From the **Available** column, select the plus sign **(+)** to move the task into the **Selected** column.
    - To add a whole category, select a category.
    - To add individual items from a category, select the down arrow on the left of the category name, and then select individual items.
1. Select **Next**.
1. In **Role name:**, enter a name for your role.
1. To add another statement to your role, select **Add statement**, and then, from the list of **Statements**, select a statement.
1. Review your **Task**, **Resources**, **Request Conditions**, and **Effect** settings, and then select **Next**.


1. On the **Preview** page, review:
    - The list of selected **Actions**.
    - The **YAML** or **Script** to confirm it's what you want.
1. If your controller isn't enabled, select **Download YAML** or **Download Script** to download the code and run it yourself.
1. Select **Submit**.
    A message confirms that your role has been submitted for creation

1. The [**CloudKnox Tasks**](cloudknox-ui-tasks.md) pane appears on the right.

    - The **Active** tab displays a list of the policies CloudKnox is currently processing.
    - The **Completed** tab displays a list of the policies CloudKnox has completed.
1. Refresh the **Role/Policies** tab to see the role you created.


## Next steps

- For information on how to view existing roles/policies, requests, and permissions, see [View roles/policies, requests, and permission in the Remediation dashboard](cloudknox-ui-remediation.md).
- For information on how to modify a role/policy, see [Modify a role/policy](cloudknox-howto-modify-role-policy.md).
- For information on how to clone a role/policy, see [Clone a role/policy](cloudknox-howto-clone-role-policy.md).
- For information on how to delete a role/policy, see [Delete a role/policy](cloudknox-howto-delete-role-policy.md).
- To view information about roles/policies, see [View information about roles/policies](cloudknox-howto-view-role-policy.md).
- For information on how to attach and detach permissions for AWS identities, see [Attach and detach policies for AWS identities](cloudknox-howto-attach-detach-permissions.md).
- For information on how to revoke high-risk and unused tasks or assign read-only status for Azure and GCP identities, see [Revoke high-risk and unused tasks or assign read-only status for Azure and GCP identities](cloudknox-howto-revoke-task-readonly-status.md)
- For information on how to create or approve a request for permissions, see [Create or approve a request for permissions](cloudknox-howto-create-approve-privilege-request.md).
- For information on how to view information about roles/policies, see [View information about roles/policies](cloudknox-howto-view-role-policy.md)
