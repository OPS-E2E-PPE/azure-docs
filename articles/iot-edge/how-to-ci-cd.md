---
title: Azure IoT Edge continuous integration and continuous deployment | Microsoft Docs
description: Overview of the continuous integration and continuous deployment for Azure IoT Edge
author: shizn
manager: 
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
---

# Continuous integration and continuous deployment to Azure IoT Edge

This article demonstrates how you can use the continuous integration and continuous deployment features of Azure DevOps Services and Microsoft Team Foundation Server (TFS) to build, test, and deploy applications quickly and efficiently to your Azure IoT Edge. 

In this article, you will learn how to:
* Create and check in a sample IoT Edge solution.
* Install Azure IoT Edge extension for your Azure DevOps.
* Configure continuous integration (CI) to build the solution.
* Configure continuous deployment (CD) to deploy the solution and view responses.

It will take 30 minutes to complete the steps in this article.

![CI and CD](./media/how-to-ci-cd/cd.png)


## Create a sample Azure IoT Edge solution using Visual Studio Code

In this section, you will create a sample IoT Edge solution containing unit tests that you can execute as part of the build process. Before following the guidance in this section, complete the steps in [Develop an IoT Edge solution with multiple modules in Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. In VS Code command palette, type and run the command **Azure IoT Edge: New IoT Edge solution**. Then select your workspace folder, provide the solution name (The default name is **EdgeSolution**), and create a C# Module (**FilterModule**) as the first user module in this solution. You also need to specify the Docker image repository for your first module. The default image repository is based on a local Docker registry (`localhost:5000/filtermodule`). You need to change it to Azure Container Registry(`<your container registry address>/filtermodule`) or Docker Hub for further continuous integration.

    ![Set up ACR](./media/how-to-ci-cd/acr.png)

2. The VS Code window will load your IoT Edge solution workspace. You can optionally type and run **Azure IoT Edge: Add IoT Edge module** to add more modules. There is a `modules` folder, a `.vscode` folder, and a deployment manifest template file in the root folder. All user module codes will be subfolders under the folder `modules`. The `deployment.template.json` is the deployment manifest template. Some of the parameters in this file will be parsed from the `module.json`, which exists in every module folder.

3. Now your sample IoT Edge solution is ready. The default C# module acts as a pipe message module. In the `deployment.template.json`, you will see this solution contains two modules. The message will be generated from the `tempSensor` module, and will be directly piped via `FilterModule`, then sent to your IoT hub.

4. Save these projects, then check it into your Azure DevOps or TFS repository.
    
> [!NOTE]
> For more information about using Azure Repos, see [Share your code with Visual Studio and Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## Configure Azure Pipeline for continuous integration
In this section, you will create a build pipeline that is configured to run automatically when you check in any changes to the sample IoT Edge solution, and it will show build logs in Azure Pipeline.

1. Sign into your Azure DevOps organization (**https://**_your-account_**.visualstudio.com**) and open the project where you checked in the sample app.

    ![Check-in code](./media/how-to-ci-cd/init-project.png)

1. Visit [Azure IoT Edge For Azure Pipeline](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) on Azure DevOps Marketplace. Click **Get it free** and follow the wizard to install this extension to your Azure DevOps organization or download to your TFS.

    ![Install extension](./media/how-to-ci-cd/install-extension.png)

1. In your Azure DevOps, open the **Build & Release** hub and, in the **Builds** tab, choose **+ New pipeline**. Or, if you already have build pipelines, choose the **+ New** button.

    ![New pipeline](./media/how-to-ci-cd/add-new-build.png)

1. If prompted, select the **Azure DevOps Git** source type. Then select the project, repository, and branch where your code is located. Choose **Continue**.

    ![Select git](./media/how-to-ci-cd/select-vsts-git.png)

    In **Select a template** window, choose **start with an Empty process**.

    ![Select a template](./media/how-to-ci-cd/start-with-empty.png)

1. In the pipeline editor, choose the agent pool. 
    
    * If you would like to build your modules in platform amd64 for Linux containers, choose **Hosted Ubuntu 1604**
    * If you would like to build your modules in platform amd64 for Windows containers, choose **Hosted VS2017** 
    * If you would like to build your modules in platform arm32v7 for Linux containers, you need to Set up your own build agent by clicking the **Manage** button.
    
    ![Configure build agent](./media/how-to-ci-cd/configure-env.png)

1. In Agent job, click "+" to add two tasks in the build pipeline. The first one is from **Azure IoT Edge**. And the second one is from **Publish Build Artifacts**
    
    ![Add tasks](./media/how-to-ci-cd/add-tasks.png)

1. In the first **Azure IoT Edge** task, update the **Display name** to **Module Build and Push**, and in the **Action** dropdown list, select **Build and Push**. In the **Module.json File** textbox, add below path to it. Then choose **Container Registry Type**, make sure you configure and select the same registry in your code(module.json). This task will build and push all your modules in the solution and publish to the container registry you specified. If your modules will be pushed to different registries, you can have multiple **Module Build and Push** tasks. In case that the IoT Edge solution is not under the root of your code repository, you can specify **Path of Edge solution root** in build definition.
    
    ![Build and push](./media/how-to-ci-cd/build-and-push.png)

1. In **Publish Build Artifacts** task, you would specify the deployment file generated by the build task. Set the **Path to publish** to "config/deployment.json". If you set **Path of Edge solution root** in the last task, you will have to join the root path here. For example, if path of Edge solution root is "./edgesolution", then the **Path to publish** should be "./edgesolution/config/deployment.json". The `deployment.json` file is generated during build time, so it's safe to ignore the red error lines in the textbox. 

    ![Publish artifact](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Open the **Triggers** tab and turn on the **Continuous integration** trigger. Make sure the branch containing your code is included.

    ![Configure trigger](./media/how-to-ci-cd/configure-trigger.png)

    Save the new build pipeline. Click the **Save** button.


## Configure Azure Pipeline for continuous deployment
In this section, you will create a release pipeline that is configured to run automatically when your build pipeline drops artifacts, and it will show deployment logs in Azure Pipeline.

1. In the **Releases** tab, choose **+ New pipeline**. Or, if you already have release pipelines, choose the **+ New** button.  

    ![Add release pipeline](./media/how-to-ci-cd/add-release-pipeline.png)

    In **Select a template** window, choose **start with an Empty job.**

    ![Start with empty job](./media/how-to-ci-cd/start-with-empty-job.png)

2. Then the release pipeline would initialize with one stage: **Stage 1**. Rename the **Stage 1** to **QA** and treat it as a test environment. In a typical continuous deployment pipeline, it usually exists multiple stages, you can create more based on your DevOps practice.

    ![Create stage](./media/how-to-ci-cd/QA-env.png)

3. Link the release to the build artifacts. Click **Add** in artifacts area.

    ![Add artifacts](./media/how-to-ci-cd/add-artifacts.png)  
    
    In **Add an artifact page**, choose Source type **Build**. Then select the project and the build pipeline you created. Then Click **Add**.

    ![Add an artifact](./media/how-to-ci-cd/add-an-artifact.png)

    Open continuous deployment trigger so that new release will be created each time a new build is available.

    ![Configure trigger](./media/how-to-ci-cd/add-a-trigger.png)

4. Navigate to **QA stage** and configure the tasks in this stage.

    ![Configure QA tasks](./media/how-to-ci-cd/view-stage-tasks.png)

   Deployment task is platform insensitive, which means you can choose either **Hosted VS2017** or **Hosted Ubuntu 1604** in the **Agent pool** (or any other agent managed by yourself). Click "+" and add one task.

    ![Add tasks for QA](./media/how-to-ci-cd/add-task-qa.png)

5. In the Azure IoT Edge task, navigate to the **Action** dropdown list, select **Deploy to IoT Edge device**. Select your **Azure subscription** and input your **IoT Hub name**. You can specify an IoT Edge **deployment ID** and the deployment **priority**. You can also choose to deploy to single or multiple devices. If you are deploying to **multiple devices**, you need to specify the device **target condition**. The target condition is a filter to match a set of Edge devices in IoT Hub. If you want to use Device Tags as the condition, you need to update your corresponding devices Tags with IoT Hub device twin. Assume you have several IoT Edge devices have been tagged as 'qa', then the task configuration should be as in the following screenshot. 

    ![Deploy to QA](./media/how-to-ci-cd/deploy-to-qa.png)

    Save the new release pipeline. Click the **Save** button. And then click **Pipeline** to go back to the pipeline.

6. The second stage is for your production environment. To add a new stage "PROD", you can simply clone the Stage "QA" and rename cloned stage to **PROD**,

    ![Clone stage](./media/how-to-ci-cd/clone-stage.png)

7. Configure the tasks for your production environment. Assume you have several IoT Edge devices have been tagged as 'prod', in the task configurations, update the Target Condition to "prod", and set the deployment id as "deploy-prod". Click the **Save** button. And then click **Pipeline** to go back to the pipeline.
    
    ![Deploy to production](./media/how-to-ci-cd/deploy-to-prod.png)

7. Currently, our build artifact will be triggered continuously on **QA** stage and then **PROD** stage. But most of the times you need to integrate some test cases on the QA devices and manually approve the bits. Later the bits will be deployed to PROD environment. Set up an approval in PROD stage as the following.

    1. Open **Pre-deployment conditions** setting panel.

        ![Open pre-deployment conditions](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Set **Enabled** in **Pre-deployment approvals**. And fill in the **Approvers** input. Then click **Save**.
    
        ![Set conditions](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Now your release pipeline has been set up as following.

    ![Release pipeline](./media/how-to-ci-cd/release-pipeline.png)

    
## Verify IoT Edge CI/CD with the build and release pipelines

In this section, you will trigger a build to make the CI/CD pipeline work. Then verify the result with Azure DevOps portal. 

1. To trigger a build job, you can either push a commit to source code repository or manually trigger it. You can trigger a build job in your build pipeline by clicking the **Queue** button as in following screenshot.

    ![Manual trigger](./media/how-to-ci-cd/manual-trigger.png)

2. If the build pipeline is completed successfully, it will trigger a release to **QA** stage. Navigate to build pipeline logs and you should see the following.

    ![Build logs](./media/how-to-ci-cd/build-logs.png)

3. The successful deployment to **QA** stage would trigger a notification to the approver. Navigate to release pipeline, you can see the following. 

    ![Pending approval](./media/how-to-ci-cd/pending-approval.png)


4. After the approver approve this change, it can be deployed to **PROD**.

    ![Deploy to prod](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## Next steps

* Understand the IoT Edge deployment in [Understand IoT Edge deployments for single devices or at scale](module-deployment-monitoring.md)
* Walk through the steps to create, update, or delete a deployment in [Deploy and monitor IoT Edge modules at scale](how-to-deploy-monitor.md).
