---
title: Choose a deployment method for the MedTech service - Azure Health Data Services
description: In this article, you'll learn how to choose a method to deploy the MedTech service.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/29/2022
ms.author: jasteppe
---

# Quickstart: Choose a deployment method for the MedTech service

The MedTech service provides multiple methods for deployment into an Azure Platform as a Service (PaaS) configuration. Each method has different advantages that will allow you to customize your development environment to suit your needs.

The different deployment methods are:

- Azure Resource Manager (ARM) template with Deploy to Azure button
- Azure PowerShell or Azure CLI automation
- Azure portal manual deployment

## Azure Resource Manager template with Deploy to Azure button

Using an ARM template with Azure portal is the easiest and fastest deployment method because it automates most of your configuration with the touch of a **Deploy to Azure** button. This button automatically generates the following configurations and resources: managed identity Azure role-based access (RBAC) roles, a provisioned workspace and namespace, an Event Hubs instance, a Fast Healthcare Interoperability Resources (FHIR&#174;) service instance, and a MedTech service instance. All you need to add are post-deployment device mapping, destination mapping, and a shared access policy key. This method simplifies your deployment, but doesn't allow for much customization.

For more information about the ARM template and the Deploy to Azure button, see [Deploy the MedTech service using an Azure Resource Manager template](deploy-new-button.md).

## Azure PowerShell and Azure CLI automation

Azure provides Azure PowerShell and Azure CLI to speed up your configurations when used in enterprise environments. Deploying MedTech service with Azure PowerShell or Azure CLI can be useful for adding automation so that you can scale your deployment for a large number of developers. This method is more detailed but provides extra speed and efficiency because it allows you to automate your deployment.

For more information about Using an ARM template with Azure PowerShell or Azure CLI, see [Deploy the MedTech service using an Azure Resource Manager template and Azure PowerShell or the Azure CLI](deploy-new-powershell-cli.md).

## Azure portal manual deployment

The manual deployment method uses the Azure portal to implement each deployment task individually. Using the manual deployment method will allow you to see all the details of how to complete the sequence of each deployment task. The manual deployment method can be beneficial if you need to customize or troubleshoot your deployment process. The manual deployment is the most complex method, but it provides valuable technical information and developmental options that will enable you to fine-tune your deployment precisely.

For more information about manual deployment with Azure portal, see [Deploy the MedTech service manually using the Azure portal](deploy-new-manual.md).

## Deployment architecture overview

The following data-flow diagram outlines the basic steps of MedTech service deployment and shows how these steps fit together with its data processing procedures. These basic steps may help you analyze the options and determine which deployment method is best for you.

:::image type="content" source="media/iot-get-started/get-started-with-iot.png" alt-text="Diagram showing MedTech service architecture overview." lightbox="media/iot-get-started/get-started-with-iot.png":::

There are six different steps of the MedTech service PaaS. Only the first four apply to deployment. All the methods of deployment will implement each of these four steps. However, the QuickStart template method will automatically implement part of step 1 and all of step 2. The other two methods will have to implement all of the steps individually. Here's a summary of each of the four deployment steps:

### Step 1: Prerequisites

- Have an Azure subscription
- Create RBAC roles contributor and user access administrator or owner. This feature is automatically done in the ARM template method with the Deploy to Azure button. It isn't included in the manual or PowerShell/CLI methods and needs to be implemented individually.

### Step 2: Provision

The ARM template method with the Deploy to Azure button automatically provides all these steps, but they aren't included in the manual or the PowerShell/CLI method and must be completed individually.

- Create a resource group and workspace for Event Hubs, FHIR, and MedTech services.
- Provision an Event Hubs instance to a namespace.
- Provision a FHIR service instance to the same workspace.
- Provision a MedTech service instance in the same workspace.

### Step 3: Configure

Each method needs to provide **all** these configuration details. They include: 

- Configure MedTech service to ingest data from an event hub.
- Configure device mapping properties.
- Configure destination mappings to an Observation resource in the FHIR service.
- When the prerequisites, provisioning, and configuration are complete, create and deploy MedTech service.

### Step 4: Post-Deployment

Each method must add **all** these post-deployment tasks:

- Connect to services using device and destination mapping.
- Use managed identity to grant access to the device message event hub.
- Use managed identity to grant access to the FHIR service, enabling FHIR to receive data from the MedTech service.
- Note: only the ARM template method requires a shared access key for post-deployment.

### Granting access to the device message event hub

For information about granting access to the device message event hub, see [Granting access to the device message event hub](deploy-new-deploy.md#grant-access-to-the-device-message-event-hub).

### Granting access to the FHIR service

For information about granting access to the FHIR service, see [Granting access to the FHIR service](deploy-new-deploy.md#grant-access-to-the-fhir-service).

## Next steps

In this article, you learned about the different types of deployment for MedTech service. To learn more about MedTech service, see

> [!div class="nextstepaction"]
> [What is MedTech service?](iot-connector-overview.md)

FHIR&#174; is a registered trademark of Health Level Seven International, registered in the U.S. Trademark Office and is used with their permission.
