---
title: Self-service hybrid data access workflows
description: This article describes how to create and manage hybrid self-service data access workflows in Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to #Required; leave this attribute/value as-is.
ms.date: 03/09/2022
ms.custom: template-how-to #Required; leave this attribute/value as-is.
---

# Self-service data access workflows for hybrid data estates

[!INCLUDE [Region Notice](./includes/workflow-regions.md)]

This guide will take you through the creation and management of self-service data access [workflows](concept-workflow.md) for hybrid data estates. 

## Create and enable self-service data access workflow

1. Sign in to [Azure Purview Studio](https://web.purview.azure.com/resource/) and select the Management center. You'll see three new icons in the table of contents.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/workflow-section.png" alt-text="Screenshot showing the management center left menu with the new workflow section highlighted.":::

1. To create new workflows, select Authoring. This will take you to the workflow authoring experience.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/workflow-authoring-experience.png" alt-text="Screenshot showing the authoring workflows page, showing a list of all workflows.":::

    >[!NOTE]
    >If the authoring tab is greyed out, you don't have the permissions to be able to author workflows. You'll need the [workflow admin role](catalog-permissions.md).

1. To create a new self-service workflow, select **+New** button.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/workflow-authoring-select-new.png" alt-text="Screenshot showing the authoring workflows page, with the + New button highlighted.":::

1. You'll be presented with different categories workflows creatable in Azure Purview. To create **an access request workflow** Select **Governance** and select **Continue**.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/select-governance.png" alt-text="Screenshot showing the new workflow window, with the Governance option selected.":::

1. In the next screen, you'll see all the templates provided by Azure Purview to create a self-service data access workflow. Select the template **Data access request** and select **Continue**.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/select-data-access-request.png" alt-text="Screenshot showing the new workflow window, with the Data access request option selected.":::

1. Next, enter workflow a name and optionally add a description. Then select **Continue**.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/name-and-continue.png" alt-text="Screenshot showing the new workflow window, with a name entered in the textbox.":::

1. You'll now be presented with a canvas where the selected template is loaded by default.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/workflow-canvas-inline.png" alt-text="Screenshot showing the workflow canvas with the selected template workflow steps displayed." lightbox="./media/how-to-workflow-self-service-data-access-hybrid/workflow-canvas-expanded.png":::

    The template has the following steps: 
    1. Trigger when a data access request is made. 
    1. Approval connector that specifies a user or group that will be contacted to approve the request.
    1. Condition to check approval status 
        - If approved:
            1. Condition to check if data source is registered for use governance (policy) 
                1. If a data source is registered with policy:
                    1. Create self-service policy 
                    1. Send email to requestor that access is provided 
                1. If data source isn't registered with policy:
                    1. Task connector to assign a task to a user or Microsoft Azure Active Directory group to manually provide access to requestor. 
                    1. Send an email to requestor that access is provided once the task is complete. 
        - If rejected:
            1. Send an email to requestor that data access request is denied.
1. The default template can be used as it is by populating two fields:  
    * Adding an approver's email address or Microsoft Azure Active Directory group in **Start and Wait for approval** Connector 
    * Adding a user's email address or Microsoft Azure Active Directory group in **Create task** connector to denote who is responsible for manually providing access if the source isn't registered with policy.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/required-fields-for-template-inline.png" alt-text="Screenshot showing the workflow canvas with the start and wait for an approval step, and the Create Task and wait for task completion steps highlighted, and the Assigned to textboxes highlighted within those steps." lightbox="./media/how-to-workflow-self-service-data-access-hybrid/required-fields-for-template-expanded.png":::

    > [!NOTE]
    > Please configure the workflow to create self-service policies ONLY for sources supported by Azure purview's policy feature. To see what's supported by policy, check the [Data owner policies documentation](tutorial-data-owner-policies-storage.md).

1. You can also modify the template by adding more connectors to suit your organizational needs. 

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/more-connectors-inline.png" alt-text="Screenshot showing the workflow authoring canvas, with a + button highlighted on the arrow between the two top steps, and the Next Step button highlighted at the bottom of the workspace." lightbox="./media/how-to-workflow-self-service-data-access-hybrid/more-connectors-expanded.png":::

1. Once you're done defining a workflow, you need to bind the workflow to a collection hierarchy path. The binding (or scoping) implies that this workflow is triggered only for data access requests in that collection. To bind a workflow or to apply a scope to a workflow, you need to select **Apply workflow**. Select the scope you want this workflow to be associated with and select **OK**.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/apply-workflow.png" alt-text="Screenshot showing the workflow workspace with the Apply workflow button selected at the top of the space, and the Apply workflow menu open, showing a list of items. One item is selected, and the O K button is highlighted at the bottom.":::

    >[!NOTE]
    > Purview workflow engine will always resolve to the closest workflow that the collection hierarchy path is associated with. In case a direct binding is not found, it will traverse up in the tree to find the workflow associated with the closest parent in the collection tree.

1. By default, the workflow will be enabled. You can disable by selecting the Enable toggle.
1. Finally select **Save and close** to create and enable the workflow.

    :::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/completed-workflows.png" alt-text="Screenshot showing the workflow authoring page with the newly created workflow listed among the other workflows.":::

## Edit an existing workflow

To modify an existing workflow, select the workflow and then select the **Edit** button. You'll now be presented with the canvas containing workflow definition. Modify the workflow and select **Save** to commit changes.

:::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/select-edit.png" alt-text="Screenshot showing the workflow authoring page, with the Edit button highlighted in the top menu.":::

## Disable a workflow

To disable a workflow, you can select the workflow and then select **Disable**. You can also disable the workflow by selecting **Edit** and changing the enable toggle in workflow canvas then saving.

:::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/select-disable.png" alt-text="Screenshot showing the workflow authoring page, with the Disable button highlighted in the top menu.":::

## Delete a workflow

To delete a workflow, select the workflow and then select **Delete**.

:::image type="content" source="./media/how-to-workflow-self-service-data-access-hybrid/select-delete.png" alt-text="Screenshot showing the workflow authoring page, with the Delete button highlighted in the top menu.":::

## Next steps

For more information about workflows, see these articles:

- [What are Azure Purview workflows](concept-workflow.md)
- [Approval workflow for business terms](how-to-workflow-business-terms-approval.md)
- [Manage workflow requests and approvals](how-to-workflow-manage-requests-approvals.md)

