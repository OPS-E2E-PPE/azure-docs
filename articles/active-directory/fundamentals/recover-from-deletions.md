---
title: Recover from deletions in Azure Active Directory
description: Learn how to recover from unintended deletions.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/20/2022
ms.author: baselden
ms.reviewer: baselden
ms.custom: "it-pro, seodec18"
ms.collection: M365-identity-device-management
---

# Recover from deletions

This article addresses recovering from soft and hard deletions in your Azure AD tenant. If you haven’t already done so, we recommend first reading the [Recoverability best practices article](recoverability-overview.md) for foundational knowledge.

## Monitor for deletions

The [Azure AD Audit Log](../reports-monitoring/concept-audit-logs.md) contains information on all delete operations performed in your tenant. We recommend that you export these logs to a security information and event management (SIEM) tool such as [Microsoft Sentinel](../../sentinel/overview.md). You can also use Microsoft Graph to audit changes and build a custom solution to monitor differences over time. For more information on finding deleted items using Microsoft Graph, see [List deleted items - Microsoft Graph v1.0. ](/graph/api/directory-deleteditems-list?tabs=http)

### Audit log 

The Audit Log always records a "Delete \<object\>" event when an object in the tenant is removed from an active state by either a soft or hard deletion.

[![Screenshot of audit log showing deletions](./media/recoverability/delete-audit-log.png)](./media/recoverability/delete-audit-log.png#lightbox)



A delete event for applications, users, and Microsoft 365 Groups is a soft delete. For any other object type, it's a hard delete. Track the occurrence of hard-delete events by comparing "Delete \<object\>" events with the type of object that has been deleted, noting those that do not support soft-delete. In addition, note "Hard Delete \<object\>" events. 


| Object type | Activity in log| Result |
| - | - | - |
| Application| Delete application| Soft deleted |
| Application| Hard delete application| Hard deleted |
| User| Delete user| Soft deleted |
| User| Hard delete user| Hard deleted |
| Microsoft 365 Group| Delete group| Soft deleted |
| Microsoft 365 Group| Hard delete group| Hard deleted |
| All other objects| Delete “objectType”| Hard deleted |


> [!NOTE]
> The audit log does not distinguish the group type of a deleted group. Only Microsoft 365 Groups are soft-deleted. If you see a Delete group entry, it may be the soft delete of a M365 group, or the hard delete of another type of group. **It is therefore important that your documentation of your known good state include the group type for each group in your organization**. To learn more about documenting your known good state, see [Recoverability best practices](recoverability-overview.md).
### Monitor support tickets

A sudden increase in support tickets regarding access to a specific object may indicate that there has been a deletion. Because some objects have dependencies, deletion of a group used to access an application, an application itself, or a Conditional Access policy targeting an application can all cause broad sudden impact. If you see a trend like this, check to ensure that none of the objects required for access have been deleted.

## Soft deletions

When objects such as users, Microsoft 365 groups, or application registrations are “soft deleted,” they enter a suspended state in which they aren't available for use by other services. In this state, items retain their properties and can be restored for 30 days. After 30 days, objects in the soft-deleted state are permanently or “hard” deleted.

> [!NOTE]
> Objects cannot be restored from a hard-deleted state. They must be recreated and reconfigured.
  
### When soft deletes occur

It's important to understand why object deletions occur in your environment to prepare for them. This section outlines frequent scenarios for soft deletion by object class. Keep in mind there may be scenarios your organization sees which are unique to your organization so a discovery process is key to preparation.

### Users

Users enter the soft delete state anytime the user object is deleted by using the Azure portal, Microsoft Graph, or PowerShell. 

The most frequent scenarios for user deletion are:

* An administrator intentionally deletes a user in the Azure AD portal in response to a request, or as part of routine user maintenance.

* An automation script in Microsoft Graph or PowerShell triggers the deletion. For example, you may have a script that removes users who haven't signed in for a specified time period.

* A user is moved out of scope for synchronization with Azure Active Directory (Azure AD) connect.

* A user is removed in an HR system and is deprovisioned via an automated workflow.

### Microsoft 365 Groups

The most frequent scenarios for Microsoft 365 Groups being deleted are:

* An administrator intentionally deletes the group, for example in response to a support request.

* An automation script in Microsoft Graph or PowerShell triggers the deletion. For example, you may have a script that deletes groups that haven't been accessed or attested to by the group owner for a specific period of time.

* Non-admins’ unintentional deletion of a group they own.



### Application objects and service principals

The most frequent scenarios for application deletion are:

* An administrator intentionally deletes the application, for example in response to a support request.

* An automation script in Microsoft Graph or PowerShell triggers the deletion. For example, you may want a process for deleting abandoned applications that are no longer used or managed. In general, create an offboarding process for applications rather than scripting to avoid unintentional deletions. 

### Properties maintained with soft delete


| Object type| Important properties maintained |
| - | - |
| Users (including external users)| **All properties maintained**, including ObjectID, group memberships, roles, licenses, application assignments. |
| Microsoft 365 Groups| **All properties maintained**, including ObjectID, group memberships, licenses, application assignments |
| Application Registration| **All properties maintained.** (See additional information following this table.) |




When you delete an application, the application registration by default enters the soft-delete state. To understand the relationship between application registrations and service principals, see [Apps & service principals in Azure AD - Microsoft identity platform](/azure/active-directory/develop/app-objects-and-service-principals).



## Recover from soft deletion

You can restore soft deleted items in the Azure portal or with Microsoft Graph.

### Users

You can see soft-deleted users in the Azure portal on the Users – Deleted users page.

![screenshot showing restoring users in the Azure portal](media/recoverability/deletion-restore-user.png)

For details on restoring users, see the following documentation:

* See [Restore or permanently remove recently deleted user](active-directory-users-restore.md) for restoring in the Azure portal.

* See [Restore deleted item – Microsoft Graph v1.0](/graph/api/directory-deleteditems-restore?tabs=http) for restoring with Microsoft Graph.

### Groups

You can see soft-deleted Microsoft 365 (Microsoft 365) Groups in the Azure portal in the Groups – Deleted groups screen.

![Screenshot showing restoring groups in the Azure portal.](media/recoverability/deletion-restore-groups.png)


For details on restoring soft deleted Microsoft 365 Groups, see the following documentation:

* To restore from the Azure portal, see [Restore a deleted Microsoft 365 group. ](../enterprise-users/groups-restore-deleted.md) 

* To restore by using Microsoft Graph, see [Restore deleted item – Microsoft Graph v1.0](/graph/api/directory-deleteditems-restore?tabs=http). 

### Applications

Applications have two objects, the application registration and the service principle. For more information on the differences between the registration and the service principal, see [Apps & service principals in Azure AD.](/azure/active-directory/develop/app-objects-and-service-principals)

To restore an application from the Azure portal, select App registrations, then deleted applications. Select the application registration to restore, and then select Restore app registration. 

[![A screenshot showing the app registration restore process in the azure portal.](./media/recoverability/deletion-restore-application.png)](./media/recoverability/deletion-restore-application.png#lightbox)
  
## Hard deletions

A “hard deletion” is the permanent removal of an object from your Azure Active Directory (Azure AD) tenant. Objects that don't support soft delete are removed in this way. Similarly, soft deleted objects are hard deleted once the deletion time is 30 days ago. The only object types that support a soft delete are:

* Users

* Microsoft 365 Groups

* Application registration

> [!IMPORTANT]
> All other item types are hard deleted. When an item is hard deleted it cannot be restored: it must be recreated. Neither administrators nor Microsoft can restore hard deleted items. It's important to prepare for this situation by ensuring that you have processes and documentation to minimize potential disruption from a hard delete.
For information on preparing for and documenting current states, see [Recoverability best practices](recoverability-overview.md).

### When hard deletes usually occur

Hard deletes most often occur in the following circumstances.

Moving from soft to hard delete

* A soft-deleted object wasn't restored within 30 days.

* An administrator intentionally deletes an object in the soft delete state

Directly hard deleted

* The object type deleted doesn't support soft delete.

* An administrator chooses to permanently delete an item by using the portal, typically in response to a request.

* An automation script triggers the deletion of the object by using Microsoft Graph or PowerShell. Use of an automation script to clean up stale objects isn't uncommon. Microsoft recommends a robust off-boarding process for objects in your tenant to avoid mistakes that may result in mass-deletion of critical objects.  

## Recover from hard deletion

Hard deleted items must be recreated and reconfigured. It's best to avoid unwanted hard deletions. 

### Review soft-deleted objects 

Ensure you have a process to frequently review items in the soft delete state and restore them if appropriate. To do so, you should:

* Frequently [list deleted items](/graph/api/directory-deleteditems-list?tabs=http). 

* Ensure that you have specific criteria for what should be restored.

* Ensure that you have specific roles or users assigned to evaluating and restoring items as appropriate.

* Develop and test a continuity management plan. For more information, see [Considerations for your Enterprise Business Continuity Management Plan. ](/compliance/assurance/assurance-developing-your-ebcm-plan)


For more information on avoiding unwanted deletions, see the following topics in the [Recoverability best practices](recoverability-overview.md) article.

* Business continuity and disaster planning

* Document known good states

* Monitoring and data retention
