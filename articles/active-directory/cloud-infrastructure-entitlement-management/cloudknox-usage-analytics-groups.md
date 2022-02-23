---
title: View analytic information about groups in CloudKnox Permissions Management 
description: How to view analytic information about groups in CloudKnox Permissions Management.
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

# View analytic information about groups

> [!IMPORTANT]
> CloudKnox Permissions Management (CloudKnox) is currently in PREVIEW.
> Some information relates to a prerelease product that may be substantially modified before it's released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

The **Analytics** dashboard in CloudKnox Permissions Management (CloudKnox) collects detailed information, analyzes, reports on, and visualizes data about all identity types. System administrators can use the information to make informed decisions about granting permissions and reducing risk on unused permissions for: 

- **Users**: Tracks assigned permissions and usage of various identities.
- **Groups**: Tracks assigned permissions and usage of the group and the group members.
- **Active resources**: Tracks active resources (used in the last 90 days).
- **Active tasks**: Tracks active tasks (performed in the last 90 days).
- **Access keys**: Tracks the permission usage of access keys for a given user.
- **Serverless functions**: Tracks assigned permissions and usage of the serverless functions.

This article describes how to view usage analytics about groups.

## Create a query to view groups

When you select **Groups**, the **Usage Analytics** dashboard provides a high-level overview of groups. 

1. On the main **Analytics** dashboard, select **Groups** from the  drop-down list at the top of the screen. 

    The following components make up the **Groups** dashboard:

    - **Authorization system type**: Select the authorization you want to use: Amazon Web Services (AWS), Microsoft Azure, or Google Cloud Platform (GCP).
    - **Authorization system**: Select from a **List** of accounts and **Folders**.
    - **Group type**: Select **All**, **ED**, or **Local**.
    - **Group activity status**: Select **All**, **Active**, or **Inactive**.
    - **Tasks Type**: Select **All**, **High-risk tasks**, or **Delete tasks**
    - **Search**: Enter group name to find specific group.
1. To display the criteria you've selected, select **Apply**.
    - **Reset filter**: Select to discard your changes.


## View the results of your query

The **Groups** table displays the results of your query:

- **Group Name**: Provides the name of the group. 
    - To view details about the group, select the down arrow. 
- A **Group type** icon displays to the left of the group name to describe the type of group (**ED** or **Local**).
- The **Domain/Account** name.
- The **Permission creep index (PCI)**: Provides the following information:
    - **Index**: A numeric value assigned to the PCI.
    - **Since**: How many days the PCI value has been at the displayed level.
- **Tasks**: Displays the number of **Granted** and **Executed** tasks.
- **Resources**: The number of resources used.
- **Users**: The number of users who accessed the group.
- Select the ellipses **(...)** and select **Tags** to add a tag.

## Add a tag to a group

1. Select the ellipses **(...)** and select **Tags**.
1. From the **Select a tag** dropdown, select a tag.
1. To create a custom tag select **New custom tag**, add a tag name, and then select **Create**.
1. In the **Value (Optional)** box, enter a value.
1. Select the ellipses **(...)** to select **Advanced save** options, and then select **Save**.
1. To add the tag to the serverless function, select **Add tag**.

## View detailed information about a group

1. Select the down arrow to the left of the **Group name**.

    The list of **Tasks** organized by **Unused** and **Used** displays.

1. Select the arrow to the left of the group name to view details about the task.
1. Select **Information** (**i**) to view when the task was last used.
1. From the **Tasks** dropdown, select **All tasks**, **High-risk tasks**, and **Delete tasks**.
1. The pane on the right displays a list of **Users**, **Policies** for **AWS** and **Roles** for **GCP or AZURE**, and **Tags**.

## Apply filters to your query  

There are many filter options within the **Groups** screen, including filters by **Authorization system type**, **Authorization system**, **Group type**, **Group activity status**, and **Tasks type**. 
Filters can be applied in one, two, or all three categories depending on the type of information you're looking for. 

### Apply filters by authorization system type

1. From the **Authorization system type** dropdown, select the authorization system you want to use: **AWS**, **Azure**, or **GCP**.
1. Select **Apply** to run your query and display the information you selected.

    Select **Reset filter** to discard your changes. 

### Apply filters by authorization system

1. From the **Authorization system type** dropdown, select the authorization system you want to use: **AWS**, **Azure**, or **GCP**. 
1. From the **Authorization system** dropdown, select accounts from a **List** of accounts and **Folders**.
1. Select **Apply** to run your query and display the information you selected.

    Select **Reset filter** to discard your changes. 


### Apply filters by group type

You can filter user details by type of user, user role, app, or service used, or by resource.

1. From the **Authorization system type** dropdown, select the authorization system you want to use: **AWS**, **Azure**, or **GCP**.
1. From the **Authorization system** dropdown, select from a **List** of accounts and **Folders**.
1. From the **Group type** dropdown, select the type of user: **All**, **ED**, or **Local**.
1. Select **Apply** to run your query and display the information you selected.

    Select **Reset filter** to discard your changes.

### Apply filters by group activity status

You can filter user details by type of user, user role, app, or service used, or by resource.

1. From the **Authorization system type** dropdown, select the authorization system you want to use: **AWS**, **Azure**, or **GCP**.
1. From the **Authorization system** dropdown, select from a **List** of accounts and **Folders**.
1. From the **Group activity status** dropdown, select the type of user: **All**, **Active**, or **Inactive**.
1. Select **Apply** to run your query and display the information you selected.

    Select **Reset filter** to discard your changes.

### Apply filters by tasks type

You can filter user details by type of user, user role, app, or service used, or by resource.

1. From the **Authorization system type** dropdown, select the authorization system you want to use: **AWS**, **Azure**, or **GCP**.
1. From the **Authorization system** dropdown, select from a **List** of accounts and **Folders**.
1. From the **Tasks type** dropdown, select the type of user: **All**, **High-risk tasks**, or **Delete tasks**.
1. Select **Apply** to run your query and display the information you selected.

    Select **Reset filter** to discard your changes.


## Export the results of your query

- To view a report of the results of your query as a comma-separated values (CSV) file, select **Export**, and then select **CSV**. 
- To view a list of members of the groups in your query, select **Export**, and then select  **Memberships**. 



## Next steps

- To view active tasks, see [View analytic information about active tasks](cloudknox-usage-analytics-active-tasks.md).
- To view assigned permissions and usage by users, see [View analytic information about users](cloudknox-usage-analytics-users.md).
- To view active resources, see [View analytic information about active resources](cloudknox-usage-analytics-active-resources.md).
- To view the permission usage of access keys for a given user, see [View analytic information about access keys](cloudknox-usage-analytics-access-keys.md).
- To view assigned permissions and usage of the serverless functions, see [View analytic information about serverless functions](cloudknox-usage-analytics-serverless-functions.md).
