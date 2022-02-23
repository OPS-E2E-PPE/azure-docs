---
title: View notification settings for a rule in the Autopilot dashboard in CloudKnox Permissions Management 
description: How to view notification settings for a rule  in the Autopilot dashboard in CloudKnox Permissions Management.
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

# View notification settings for a rule in the Autopilot dashboard

> [!IMPORTANT]
> CloudKnox Permissions Management (CloudKnox) is currently in PREVIEW.
> Some information relates to a prerelease product that may be substantially modified before it's released. Microsoft makes no warranties, express or implied, with respect to the information provided here.
 
This article describes how to view notification settings for a rule in the CloudKnox Permissions Management (CloudKnox) **Autopilot** dashboard.

> [!NOTE]
> Only users with **Administrator** permissions can view and make changes on the Autopilot tab. If you don’t have these permissions, contact your system administrator.

## View notification settings for a rule 

1. In the CloudKnox home page, select the **Autopilot** tab.
1. In the **Autopilot** dashboard, from the **Authorization system types** dropdown, select Amazon Web Services (**AWS**), Microsoft **Azure**, or Google Cloud Platform (**GCP**).
1. From the **Authorization system** dropdown, in the **List** and **Folders** box, select the account and folder names that you want, and then select **Apply**.  
1. In the **Autopilot** dashboard, select a rule.
1. In the far right of the row, select the ellipses **(...)**
1. To view notification settings for a rule, select **Notification settings**. 

    CloudKnox displays a list of subscribed users. These users are signed up to receive notifications for the selected rule.

1. To close the **Notification settings** box, select **Close**.


## Next steps

- For more information about viewing rules, see [View roles in the Autopilot dashboard](cloudknox-ui-autopilot.md).
- For information about creating rules, see [Create a rule](cloudknox-howto-create-rule.md).
- For information about generating, viewing, and applying rule recommendations for rules, see [Generate, view, and apply rule recommendations for rules](cloudknox-howto-recommendations-rule.md).
