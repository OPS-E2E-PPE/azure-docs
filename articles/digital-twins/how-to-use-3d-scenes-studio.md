---
# Mandatory fields.
title: Use 3D Scenes Studio (all features)
titleSuffix: Azure Digital Twins
description: Learn how to use all the features of 3D Scenes Studio for Azure Digital Twins.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 05/03/2022
ms.topic: how-to
ms.service: digital-twins
ms.custom: event-tier1-build-2022

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Build 3D scenes with 3D Scenes Studio for Azure Digital Twins

Azure Digital Twins [3D Scenes Studio](https://explorer.digitaltwins.azure.net/3dscenes) is an immersive 3D environment, where business and front-line workers can consume and investigate operational data from their Azure Digital Twins solutions with visual context.

## Prerequisites

To use 3D Scenes Studio, you'll need the following resources:
* An Azure Digital Twins instance. For instructions, see [Set up an instance and authentication](how-to-set-up-instance-cli.md).
    * Obtain *Azure Digital Twins Data Owner* or *Azure Digital Twins Data Reader* access to the instance. For instructions, see [Set up user access permissions](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
    * Take note of the *host name* of your instance to use later.
* An Azure storage account. For instructions, see [Create a storage account](/azure/storage/common/storage-account-create?tabs=azure-portal).
    * Obtain *Storage Blob Data Owner* access to the storage account. For instructions, see [Assign Azure roles using the Azure portal](../role-based-access-control/role-assignments-portal.md).
* A private container in the storage account. For instructions, see [Create a container](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).
    * Take note of the *URL* of your storage container to use later.

You should also configure CORS for your storage account, so that 3D Scenes Studio will be able to access your storage container.
1. Return to the storage account's page in the portal.
1. Scroll down in the left menu to **Resource sharing (CORS)** and select it.
1. On the **Resource sharing (CORS)** page for your storage account, fill in an entry with the following details:
    1. **Allowed origins** - Enter *https://explorer.digitaltwins.azure.net*. You can add additional origins if you want, or use * to allow general access.
    1. **Allowed methods** - Select the checkboxes for *GET*, *POST*, *OPTIONS*, and *PUT*. You can add additional methods if you want.
    1. **Allowed headers** - Enter *Authorization,x-ms-version,x-ms-blob-type*. You can add additional headers if you want.
1. Select **Save**.

    :::image type="content"  source="media/how-to-use-3d-scenes-studio/cors.png" alt-text="Screenshot of the Azure portal where the CORS entry is being created and saved." lightbox="media/how-to-use-3d-scenes-studio/cors.png":::

Now you have all the necessary resources to work with scenes in 3D Scenes Studio.

## Initialize your 3D Scenes Studio environment

In this section, you'll set the environment in *3D Scenes Studio* and customize your scene for the sample graph that's in your Azure Digital Twins instance.

1. Navigate to the [3D Scenes Studio](https://explorer.digitaltwins.azure.net/3dscenes). The studio will open, connected to the Azure Digital Twins instance that you accessed last in the Azure Digital Twins Explorer.
1. Select the **Edit** icon next to the instance name to configure the instance and storage container details.

    :::image type="content" source="media/how-to-use-3d-scenes-studio/studio-edit-environment-1.png" alt-text="Screenshot of 3D Scenes Studio highlighting the edit environment icon, which looks like a pencil." lightbox="media/how-to-use-3d-scenes-studio/studio-edit-environment-1.png":::

    1. The **Environment URL** should start with *https://*, followed by the *host name* of your instance from the [Prerequisites](#prerequisites) section.
    
    1. For the **Container URL**, enter the URL of your storage container from the [Prerequisites](#prerequisites) section.
    
    1. Select **Save**.
    
    :::image type="content" source="media/how-to-use-3d-scenes-studio/studio-edit-environment-2.png" alt-text="Screenshot of 3D Scenes Studio highlighting the Save button for the environment." lightbox="media/how-to-use-3d-scenes-studio/studio-edit-environment-2.png":::

## Create and view scenes 

The 3D representation of an environment in 3D Scenes Studio is called a *scene*. A scene consists of a 3D file and a configuration file that's created for you automatically.

To create a scene, start with a segmented 3D file in *.GLTF* or *.GLB* format. You can download and view a sample 3D file using this link: [Download RobotArms.glb](https://cardboardresources.blob.core.windows.net/public/RobotArms.glb). 

>[!TIP]
>3D Scenes Studio supports animation. If you use a 3D model file that contains animations, they will play in the scene.

You can use 3D Scenes Studio with a 3D file that's already present in your storage container, or you can upload the file directly to 3D Scenes Studio, which will add it to the container automatically. Here are the steps to use a 3D file to create a new scene.

1. From the home page of 3D Scenes Studio, select the **Add 3D scene** button to start creating a new scene. 

1. Enter a **Name** for the scene, and select one of the following tabs for the file upload option:
    1. **From container** to enter the URL of a 3D file that's already in your storage container
    1. **Upload file** to upload a 3D file from your computer

    :::image type="content" source="media/how-to-use-3d-scenes-studio/add-scene.png" alt-text="Screenshot of 3D Scenes Studio, Create new scene dialog." lightbox="media/how-to-use-3d-scenes-studio/add-scene.png":::
1. Select **Create**.

### View scenes in globe view

The home page of 3D Scenes Studio shows a **List view** of your scenes. 

You can also select **Globe view** to see your scenes placed visually on a globe.

:::image type="content" source="media/how-to-use-3d-scenes-studio/globe-view-1.png" alt-text="Screenshot of 3D Scenes Studio, Globe is highlighted on the home screen." lightbox="media/how-to-use-3d-scenes-studio/globe-view-1.png":::

The resulting globe view looks like this:

:::image type="content" source="media/how-to-use-3d-scenes-studio/globe-view-2.png" alt-text="Screenshot of 3D Scenes Studio, showing scenes in Globe view." lightbox="media/how-to-use-3d-scenes-studio/globe-view-2.png":::

### View scenes individually

You can select an individual scene from the home page to open it in **Build** mode. Here, you can see the 3D mesh for the scene and edit its [elements](#add-elements) and [behaviors](#add-behaviors).

:::image type="content" source="media/how-to-use-3d-scenes-studio/scene-build.png" alt-text="Screenshot of 3D Scenes Studio, showing a scene in the builder." lightbox="media/how-to-use-3d-scenes-studio/scene-build.png":::

You can switch to **View** mode to enable filtering on specific elements and visualization of element behaviors that you've created.

:::image type="content" source="media/how-to-use-3d-scenes-studio/scene-view.png" alt-text="Screenshot of 3D Scenes Studio, showing a scene in the viewer." lightbox="media/how-to-use-3d-scenes-studio/scene-view.png":::

## Add elements

An *element* is a self-defined set of 3D meshes that is linked to data on one or more underlying digital twins.

One way to create a new element is to select **New element** from the **Elements** tab in the **Build** view for a scene.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-element-start-button.png" alt-text="Screenshot of 3D Scenes Studio in the builder for a scene. The New element button is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-element-start-button.png":::

Alternatively, you can select a mesh component directly from the visualization and create a new element that is connected to it already.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-element-start-mesh.png" alt-text="Screenshot of 3D Scenes Studio in the builder for a scene. The mesh for a tank is highlighted and there is an option to Create new element." lightbox="media/how-to-use-3d-scenes-studio/new-element-start-mesh.png":::

This will open the **New element** panel where you can fill in element information.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-element-panel.png" alt-text="Screenshot of New element options in 3D Scenes Studio." lightbox="media/how-to-use-3d-scenes-studio/new-element-panel.png":::

### Name and primary twin

A *primary twin* is the main digital twin counterpart for an element. You connect the element to a twin in your Azure Digital Twins instance so that the element can represent your twin and its data within the 3D visualization.

In the **New element** panel, the **Primary twin** dropdown list contains names of all the twins in the connected Azure Digital Twins instance. 

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-element-primary-twin.png" alt-text="Screenshot of the New element options in 3D Scenes Studio. The Primary twin dropdown list is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-element-primary-twin.png":::

Select a twin to link to this element. This will automatically apply the digital twin ID (`$dtId`) as the element **Name**. You can rename the element if you want, to make it understandable for both builders and consumers of the 3D scene.

>[!TIP]
>[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) can help you find the right twin to link to an element, by showing you a visual graph of your twins and letting you query for specific twin conditions.

### Meshes

The **Meshes** tab is where you specify which components of the visual 3D mesh represent this element.

If you started element creation by selecting a mesh in the visualization, that mesh will already be filled in here. You can select meshes in the visualization now to add them to the element.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-element-meshes.png" alt-text="Screenshot of the New element options in 3D Scenes Studio. The Meshes tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-element-meshes.png":::

### Behaviors

A *behavior* is a scenario for your scene. You can select **Add behavior** from this tab to enter the **New behavior** flow.


:::image type="content" source="media/how-to-use-3d-scenes-studio/new-element-behaviors.png" alt-text="Screenshot of the New element options in 3D Scenes Studio. The Behaviors tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-element-behaviors.png":::

For more details on creating new behaviors, see [Add behaviors](#add-behaviors).

### Aliased twins

An *aliased twin* is a secondary digital twin data source for an element. You can add aliased twins to an element if the data on the primary twin won't be enough to define all the behaviors you want for the element, so you need access to the data of additional twins.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-element-aliased-twins.png" alt-text="Screenshot of the New element options in 3D Scenes Studio. The Aliased twins tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-element-aliased-twins.png":::

You can't add aliased twins during new element creation. For instructions on adding aliased twins, see [Twins](#twins) as a behavior option.

Once there are aliased twins added to the element, you'll be able to view and modify them on this tab.

## Add behaviors

A *behavior* is a scenario for your scene that will leverage particular data on the related element's digital twin to drive viewer visualizations.

One way to create a new behavior is to select **New behavior** from the **Behaviors** tab of the **Build** view for a scene.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-start-button.png" alt-text="Screenshot of 3D Scenes Studio in the builder for a scene. The New behavior button is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-start-button.png":::

Alternatively, you can select an element from the **Elements** tab, and create a new behavior from [that element's Behaviors tab](#behaviors).

:::image type="content" source="media/how-to-use-3d-scenes-studio/modify-element-behaviors.png" alt-text="Screenshot of the Modify element options in 3D Scenes Studio. The Behaviors tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/modify-element-behaviors.png":::

This will open the **New behavior** panel where you can fill in behavior information.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-panel.png" alt-text="Screenshot of New behavior options in 3D Scenes Studio." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-panel.png":::

### Name and scene layers

Start by choosing a **Display name** for the behavior.

>[!TIP]
>Choose a name that will be clear to end users that will be viewing the scene, because this behavior name will be displayed as part of the scene visualization. 

For the **Scene layers** dropdown menu, you can add this behavior to an existing layer or create a new layer to help organize this behavior. For more information on layers, see [Manage layers](#manage-layers).

### Elements

In the **Elements** tab, select which elements this behavior should target. 

If you started the behavior creation process from a specific element, that element will already be selected here. Otherwise, you can choose elements here for the first time.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-elements.png" alt-text="Screenshot of the New behavior options in 3D Scenes Studio. The Elements tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-elements.png":::

### Twins

On the **Twins** tab, you can modify the set of twins whose data is available to this behavior. This includes the targeted elements' primary twins, and any aliased twins.

An *aliased twin* is a secondary digital twin data source for an element. After configuring an aliased twin, you'll be able to use properties from that twin in your behavior expressions for this element. You should only add aliased twins when there are additional twins with data beyond your primary twin that you want to leverage in your [status](#status), [alerts](#alerts), and [widgets](#widgets) for this behavior.

To create a new alias, select **Add twin alias** and **Create twin alias**.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-twins.png" alt-text="Screenshot of the New behavior options in 3D Scenes Studio. The Twins tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-twins.png":::

This will open a **New twin alias** panel where you can name the alias and select a twin to map.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-twins-new-alias.png" alt-text="Screenshot of the New twin alias panel in 3D Scenes Studio." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-twins-new-alias.png":::

>[!TIP]
>[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) can help you see twins that might be related to the primary twin for this element. You can query your graph using `SELECT * FROM digitaltwins WHERE $dtId="<primary-twin-id>`, and then use the [double-click expansion feature](how-to-use-azure-digital-twins-explorer.md#control-twin-graph-expansion) to explore related twins.

### Status 

In the **Status** tab, you can define states for your element. *States* are data-driven overlays on your elements to indicate the health or status of the element. 

To create a state, first choose whether the state is dependent on a **Single property** or a **Custom (advanced)** property expression. For a **Single property**, you'll get a dropdown list of properties on the primary twin. For **Custom (advanced)**, you'll get a text box.

Once you've defined your property expression, set value ranges to create state boundaries, and choose colors to represent each state in the visualization.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-status.png" alt-text="Screenshot of the New behavior options in 3D Scenes Studio. The Status tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-status.png":::

### Alerts

In the **Alerts** tab, you can set conditional notifications to help you quickly see when an element requires your attention.

First, select a **Trigger expression** involving properties of *PrimaryTwin* that will generate an alert badge when it evaluates to true. Then, customize your alert badge with a **Badge icon**, **Badge color**, and **Notification text**.

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-alerts.png" alt-text="Screenshot of the New behavior options in 3D Scenes Studio. The Alerts tab is highlighted." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-alerts.png":::

### Widgets 

Widgets are managed on the **Widgets** tab. *Widgets* are data-driven visuals that provide additional context and data, to help you understand the scenario that the behavior represents. Configuring widgets will help you make sure the right data is discoverable when an alert or status is active.

Select **Add widget** to bring up the **Widget library**, where you can select from different type of available widgets. 

:::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-widgets-library.png" alt-text="Screenshot of the New behavior options in 3D Scenes Studio. The Widgets tab is highlighted and the Widget library is visible." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-widgets-library.png":::

Here are the types of widget that you can create:
 
* **Gauge**: For representing numerical data points visually
    :::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-widgets-gauge.png" alt-text="Screenshot of creating a new gauge-type widget in 3D Scenes Studio." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-widgets-gauge.png":::

* **Link**: For including externally referenced content via a linked URL
    :::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-widgets-link.png" alt-text="Screenshot of creating a new link-type widget in 3D Scenes Studio." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-widgets-link.png":::

* **Value**: For directly displaying twin property values
    :::image type="content" source="media/how-to-use-3d-scenes-studio/new-behavior-widgets-value.png" alt-text="Screenshot of creating a new value-type widget in 3D Scenes Studio." lightbox="media/how-to-use-3d-scenes-studio/new-behavior-widgets-value.png":::

## Manage layers 

You can create *layers* in your scene to help organize your [behaviors](#add-behaviors). Layers act like tags on the behaviors, enabling you to define which behaviors need to be seen together, thus creating custom views of your scene for different roles or tasks.

If there are no layers in a scene, all of the behaviors will show up in the scene viewer. If there's at least one layer present in the scene, then only behaviors that are **tagged with a layer** will show up in the viewer.

One way to create layers is to use the **Scene layers** button in the **Build** view for a scene.

:::image type="content" source="media/how-to-use-3d-scenes-studio/layers-start-button.png" alt-text="Screenshot of 3D Scenes Studio builder for a scene. The Scene layers button is highlighted." lightbox="media/how-to-use-3d-scenes-studio/layers-start-button.png":::

Selecting **New layer** will prompt you to enter a name for the new layer you want to create.

Alternatively, you can create layers while [creating or modifying a behavior](#name-and-scene-layers). The behavior pane is also where you can add the behavior to a layer you've already created.

:::image type="content" source="media/how-to-use-3d-scenes-studio/layers-start-behavior.png" alt-text="Screenshot of the Modify Behavior options in 3D Scenes Studio. A Scene layer is being selected." lightbox="media/how-to-use-3d-scenes-studio/layers-start-behavior.png":::

## Modify theme 

In the **Build** view for a scene, you can use the **Theme** button to change the style, object colors, and background color of the display.

:::image type="content" source="media/how-to-use-3d-scenes-studio/theme.png" alt-text="Screenshot of 3D Scenes Studio builder for a scene. The Theme button is highlighted." lightbox="media/how-to-use-3d-scenes-studio/theme.png":::

## Share your environment

A *3D Scenes Studio environment* is formed from a unique pairing of an **Azure Digital Twins instance** and an **Azure storage container**. 

To share your environment with someone else, they need to have these permissions to your resources:
* *Azure Digital Twins Data Reader* access (or greater) on the Azure Digital Twins instance
* *Storage Blob Data Reader* access (or greater) to the storage container
    * *Storage Blob Data Reader* will allow them to view your scenes.
    * *Storage Blob Data Owner* will allow them to edit your scenes.

Once someone has the required permissions, there are two ways to give them access to your environment. You can do either of the following things:
* Use the Share button on the 3D Scenes Studio homepage to copy the **URL of your 3D Scenes Studio environment**. (The URL includes the URLs of both your Azure Digital Twins instance and your storage container.)
    :::image type="content" source="media/how-to-use-3d-scenes-studio/copy-url.png" alt-text="Screenshot of the Share button in 3D Scenes Studio." lightbox="media/how-to-use-3d-scenes-studio/copy-url.png":::

    Share it with the recipient, who can paste this URL directly into their browser to connect to your environment.
* Share the **URL of your Azure Digital Twins instance** and the **URL of your Azure storage container** that you used when [initializing your 3D Scenes Studio environment](#initialize-your-3d-scenes-studio-environment). The recipient can access [3D Scenes Studio](https://dev.explorer.azuredigitaltwins-test.net/3dscenes) and initialize it with these same URL values to connect to your same environment.

After this, the recipient can view and interact with your scenes in the studio.

## Next steps 

Try out 3D Scenes Studio with a sample scenario in [Get started with 3D Scenes Studio](quickstart-3d-scenes-studio.md).

Or, visualize your Azure Digital Twins graph differently using [Azure Digital Twins Explorer](how-to-use-azure-digital-twins-explorer.md).
