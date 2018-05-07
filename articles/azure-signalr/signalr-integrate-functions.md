---
title: Tutorial for integrating Azure functions with Azure SignalR Service | Microsoft Docs
description: In this tutorial, you learn how to use Azure Functions with Azure SignalR Service
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''

ms.assetid: 
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
#Customer intent: As an ASP.NET Core developer, I want to integrate my app with Azure Functions to push content updates triggered by my functions.
---

# Tutorial: Integrate Azure Functions with Azure SignalR Service

This tutorial builds on the chat room application created in previous tutorials. If you have not completed [Create a chat room with SignalR Service](signalr-quickstart-dotnet-core.md), and [Azure SignalR Service authentication](./signalr-authenticate-oauth.md), complete those exercises first. 

A common scenario with real-time applications is for content updates to originate from a server to be published to web clients. [Azure Functions](../azure-functions/functions-overview.md) is an excellent candidate for generating these content updates. A key benefit of using Azure functions is that you can run your code on-demand without worrying about the architecture of a whole application, or the infrastructure to run it. You also only pay for the time your code actually runs.  

Normally, this scenario would present a problem when trying to use SignalR because SignalR tries to maintain a connection between client and server for pushing content updates. Since the code only runs on-demand, a connection cannot be maintained. However, Azure SignalR Service can support this scenario since it manages connections for you at run-time.

In this tutorial, you will use Azure Functions to generate messages using a timer function at the beginning of each minute. The function will publish the messages to all clients of the chat room created in the previous tutorials. For more information timer functions, see [Timer Function](../azure-functions/functions-create-scheduled-function.md).

You can use any code editor to complete the steps in this quickstart. However, [Visual Studio Code](https://code.visualstudio.com/) is an excellent option available on the Windows, macOS, and Linux platforms.

The code for this tutorial is available for download in the [AzureSignalR-samples GitHub repository](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Chat app with server messages](./media/signalr-integrate-functions/signalr-functions-complete.png)

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a new Timer function with Azure Functions using the Azure CLI.
> * Configure the timer function for local Git repository deployment.
> * Connect the timer to your SignalR Service to push updates every minute

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## Prerequisites

To complete this tutorial, you must have the following prerequisites:

* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [Azure Cloud Shell configured](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Download or clone the [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) github repository.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Create a function app

You must create a function app to define the execution environment for your functions. The function app also lets you group multiple functions as a logical unit for easier management, deployment, and resource sharing. For more information, see [Create your first function using the Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md).

In this section, you will use the Azure Cloud Shell to create a new Azure Function app configured for deployment from a local Git repository. 

When creating the function app resources, create them in the same resource group you used in the previous tutorials. This approach makes managing all tutorial resources easier.

Copy the script below into your text editor, and replace the values of the variable parameters with the values for your resources. Copy and paste the updated script into your Azure Cloud Shell, and press **Enter** to create a storage account and function app.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Parameter | Description |
| -------------------- | --------------- |
| ResourceGroupName | This resource group name was suggested in previous tutorials. It is a good idea to keep all tutorial resources grouped together. Use the same resource group you used in the previous tutorials. | 
| location | Update this variable to the same location you used to create the resource group in the previous tutorials. | 
| functionappName | Update this variable with a unique name for your new function app. For example, signalrfunctionapp22665120. | 
| storageAccountName | Enter a name for the new storage account to hold function app code and settings. | 



## Configure the function app

In this section, you will configure the function app with an app setting containing the connection string for your Azure SignalR Service resource. Your function code will use this setting to connect and publish messages to the chat room. You will also configure the function app for deployment from a local Git repository.

Copy the script below and replace the values of the parameters. Paste the updated script into your Azure Cloud Shell and press **Enter**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Parameter | Description |
| -------------------- | --------------- |
| ResourceGroupName | This resource group name was suggested in previous tutorials. It is a good idea to keep all tutorial resources grouped together. Use the same resource group you used in the previous tutorials. | 
| functionappName | Update this variable with a unique name for your new function app. For example, signalrfunctionapp22665120. | 
| connstring | Enter connection string for your SignalR Service resource. You can retrieve this connection string from your SignalR Service resource page in the Azure portal by clicking **Keys** under **SETTINGS**. | 



Make a note the Git deployment URL returned from the last command. You will use this URL for deploying the function code.


## The timer function

The timer function sample is located in the */samples/Timer* directory of your download, or clone of the [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer) github repository. The timer function code is located in *TimerFunction.cs*. This code uses the connection string stored with the default configuration key (*Azure:SignalR:ConnectionString*) to create a proxy to the hub. Since the function code is running server-side, there's no reason to require it to authenticate as a regular client. The code is trusted to use the connection string. Using this hub proxy, the function code can call any of the methods you have defined on your hub. The code calls the `BroadcastMessage` method to publish a message containing the current time when the trigger fired.

The trigger for the function code is a *timerTrigger*, defined in the bindings in *TimerFunction/function.json*. It includes a [CRON expression](https://wikipedia.org/wiki/Cron#CRON_expression) to set the timer trigger to fire at the beginning of every minute.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## Building the timer function

Use the [.NET Core command-line interface (CLI)](https://docs.microsoft.com/dotnet/core/tools/) in the following steps to build the function and prepare it for deployment:

1. Navigate to the */samples/Timer* subdirectory of your download, or clone of the [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) github repository.

2. Restore the NuGet packages using the following command:

        dotnet restore

3. Build the *Timer* function app using the following command:

        msbuild /p:Configuration=Release


## Create and deploy the local Git repo

1. In a Git shell, navigate to the build subdirectory, */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Initialize the directory as a new Git repository using the following command:

        git init

3. Add a new commit for all files in the build directory.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Add a remote endpoint for the Git deployment URL you made note of during the configuration of your function app:

        git remote add Azure <enter your Git deployment URL>

5. Deploy the function app

        git push Azure master

   Once the code is deployed, the timer will immediately start firing every minute to execute your code.

## Test the chat app

Navigate to the chat application, the Timer function you just created will now be reporting the time at the beginning of each minute.

![Chat app with server messages](./media/signalr-integrate-functions/signalr-functions-complete.png)



## Clean up resources

If you want to continue to test the application, you can keep the resources you have created.

Otherwise, if you are finished with the sample application, you can delete the Azure resources to avoid charges. 

> [!IMPORTANT]
> Deleting a resource group is irreversible and that the resource group and all the resources in it are permanently deleted. Make sure that you do not accidentally delete the wrong resource group or resources. If you created the resources for hosting this sample inside an existing resource group that contains resources you want to keep, you can delete each resource individually from their respective blades instead of deleting the resource group.
> 
> 

Sign in to the [Azure portal](https://portal.azure.com) and click **Resource groups**.

In the **Filter by name...** textbox, type the name of your resource group. The instructions for this article used a resource group named *SignalRTestResources*. On your resource group in the result list, click **...** then **Delete resource group**.

   
![Delete](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


You will be asked to confirm the deletion of the resource group. Type the name of your resource group to confirm, and click **Delete**.
   
After a few moments, the resource group and all of its contained resources are deleted.


## Next steps

In this tutorial, you learned how to integrate with Azure Function to push updates to clients based on Azure Function triggers. To learn more about using Azure SignalR Server, continue to the Azure CLI samples for SignalR Service.

> [!div class="nextstepaction"]
> [Azure SignalR CLI Samples](./signalr-cli-samples.md)



