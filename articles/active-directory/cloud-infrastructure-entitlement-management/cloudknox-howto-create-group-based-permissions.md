---
title: Select group-based permissions settings in CloudKnox Permissions Management with the User management dashboard
description: How to select group-based permissions settings in CloudKnox Permissions Management with the User management dashboard.
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

# Select group-based permissions settings

> [!IMPORTANT]
> CloudKnox Permissions Management (CloudKnox) is currently in PREVIEW.
> Some information relates to a prerelease product that may be substantially modified before it's released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

This article describes how you can create  and manage group-based permissions in CloudKnox Permissions Management (CloudKnox) with the User management dashboard.

## Select administrative permissions settings for a group

1. To display the **User Management** dashboard, select **User** (your initials) in the upper right of the screen, and then select **User Management**.
1. Select the **Groups** tab, and then press the **Create Permission** button in the upper right of the table.
1. In the **Set Group Permission** box, begin typing the name of an **Azure Active Directory Security Group** in your tenant.

1. Select the permission setting you want:
2. 
    - **Admin for all authorization system types** provides **View**, **Control**, and **Approve** permissions for all authorization system types.
    - **Admin for selected authorization system types** provides **View**, **Control**, and **Approve** permissions for selected authorization system types.
    - **Custom** allows you to set **View**, **Control**, and **Approve** permissions for the authorization system types that you select.
1. Select **Next**

1. If you selected **Admin for all authorization system types**
    - Select Identities for each Authorization System that you would like members of this group to Request on.

1. If you selected **Admin for selected authorization system types**
    - Select **Viewer**, **Controller**, or **Approver** for the **Authorization system types** you want.
    - Select **Next** and then select Identities for each Authorization System that you would like members of this group to Request on.

1. If you select **Custom**, select the **Authorization system types** you want.
    - Select **Viewer**, **Controller**, or **Approver** for the **Authorization Systems** you want.
    - Select **Next** and then select Identities for each Authorization System that you would like members of this group to Request on.

1. Select **Save**, The following message appears: **New group has been created successfully.**
1. To see the group you created in the **Groups** table, refresh the page.

## Next steps

- For information about how to manage user information, see [Manage users and groups with the User management dashboard](cloudknox-ui-user-management.md).
- For information about how to view information about active and completed tasks, see [View information about active and completed tasks](cloudknox-ui-tasks.md).
- For information about how to view personal and organization information, see [View personal and organization information](cloudknox-product-account-settings.md).

