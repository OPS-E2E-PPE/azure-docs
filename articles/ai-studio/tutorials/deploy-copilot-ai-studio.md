---
title: Build and deploy a question and answer copilot with prompt flow in Azure AI Studio
titleSuffix: Azure AI Studio
description: Use this article to build and deploy a question and answer copilot with prompt flow in Azure AI Studio
manager: nitinme
ms.service: azure-ai-studio
ms.custom:
  - build-2024
ms.topic: tutorial
ms.date: 5/21/2024
ms.reviewer: eur
ms.author: eur
author: eric-urban
---

# Tutorial: Build and deploy a question and answer copilot with prompt flow in Azure AI Studio

[!INCLUDE [Feature preview](../includes/feature-preview.md)]

In this [Azure AI Studio](https://ai.azure.com) tutorial, you use generative AI and prompt flow to build, configure, and deploy a copilot for your retail company called Contoso. Your retail company specializes in outdoor camping gear and clothing. 

The copilot should answer questions about your products and services. It should also answer questions about your customers. For example, the copilot can answer questions such as "How much do the TrailWalker hiking shoes cost?" and "How many TrailWalker hiking shoes did Daniel Wilson buy?".

The steps in this tutorial are:

1. Add your data to the chat playground.
1. Create a prompt flow from the playground.
1. Customize prompt flow with multiple data sources.
1. Evaluate the flow using a question and answer evaluation dataset.
1. Deploy the flow for consumption.

## Prerequisites

- An Azure subscription - <a href="https://azure.microsoft.com/free/cognitive-services" target="_blank">Create one for free</a>.
- Access granted to Azure OpenAI in the desired Azure subscription.

    Currently, access to this service is granted only by application. You can apply for access to Azure OpenAI by completing the form at <a href="https://aka.ms/oai/access" target="_blank">https://aka.ms/oai/access</a>. Open an issue on this repo to contact us if you have an issue.

- An [AI Studio hub](../how-to/create-azure-ai-resource.md), [project](../how-to/create-projects.md), and [deployed Azure OpenAI](../how-to/deploy-models-openai.md) chat model. Complete the [AI Studio playground quickstart](../quickstarts/get-started-playground.md) to create these resources if you haven't already.

- You need a local copy of product and customer data. The [Azure-Samples/aistudio-python-quickstart-sample repository on GitHub](https://github.com/Azure-Samples/aistudio-python-quickstart-sample/tree/main/data) contains sample retail customer and product information that's relevant for this tutorial scenario. Clone the repository or copy the files from [1-customer-info](https://github.com/Azure-Samples/aistudio-python-quickstart-sample/tree/main/data/1-customer-info) and [3-product-info](https://github.com/Azure-Samples/aistudio-python-quickstart-sample/tree/main/data/3-product-info). 

## Add your data and try the chat model again

In the [AI Studio playground quickstart](../quickstarts/get-started-playground.md) (that's a prerequisite for this tutorial), you can observe how your model responds without your data. Now you add your data to the model to help it answer questions about your products.

[!INCLUDE [Chat with your data](../includes/chat-with-data.md)]

## Create a prompt flow from the playground

Now you might ask "How can I further customize this copilot?" You might want to add multiple data sources, compare different prompts or the performance of multiple models. A [prompt flow](../how-to/prompt-flow.md) serves as an executable workflow that streamlines the development of your LLM-based AI application. It provides a comprehensive framework for managing data flow and processing within your application.

In this section, you learn how to transition to prompt flow from the playground. You export the playground chat environment including connections to the data that you added. Later in this tutorial, you [evaluate the flow](#evaluate-the-flow-using-a-question-and-answer-evaluation-dataset) and then [deploy the flow](#deploy-the-flow) for [consumption](#use-the-deployed-flow).

> [!NOTE]
> The changes made in prompt flow aren't applied backwards to update the playground environment. 

You can create a prompt flow from the playground by following these steps:
1. If you aren't already in the [Azure AI Studio](https://ai.azure.com) playground, select **Build** from the top menu and then select **Playground** from the collapsible left menu.
1. Select **Open in prompt flow** from the menu above the **Chat session** pane.
1. Enter a folder name for your prompt flow. Then select **Open**. Azure AI Studio exports the playground chat environment including connections to your data to prompt flow. 

    :::image type="content" source="../media/tutorials/chat/prompt-flow-from-playground.png" alt-text="Screenshot of the open in prompt flow dialog." lightbox="../media/tutorials/chat/prompt-flow-from-playground.png":::

Within a flow, nodes take center stage, representing specific tools with unique capabilities. These nodes handle data processing, task execution, and algorithmic operations, with inputs and outputs. By connecting nodes, you establish a seamless chain of operations that guides the flow of data through your application. For more information, see [prompt flow tools](../how-to/prompt-flow.md#prompt-flow-tools).

To facilitate node configuration and fine-tuning, a visual representation of the workflow structure is provided through a DAG (Directed Acyclic Graph) graph. This graph showcases the connectivity and dependencies between nodes, providing a clear overview of the entire workflow. The nodes in the graph shown here are representative of the playground chat experience that you exported to prompt flow. 

   :::image type="content" source="../media/tutorials/chat/prompt-flow-overview-graph.png" alt-text="Screenshot of the default graph exported from the playground to prompt flow." lightbox="../media/tutorials/chat/prompt-flow-overview-graph.png":::

> [!WARNING]
> Azure AI Studio is in preview and is subject to change. The screenshots and instructions in this tutorial might not match the current experience. 

Nodes can be added, updated, rearranged, or removed. The nodes in your flow at this point include:
- **DetermineIntent**: This node determines the intent of the user's query. It uses the system prompt to determine the intent. You can edit the system prompt to provide scenario-specific few-shot examples.
- **ExtractIntent**: This node formats the output of the **DetermineIntent** node and sends it to the **RetrieveDocuments** node.
- **RetrieveDocuments**: This node searches for top documents related to the query. This node uses the search type and any parameters you pre-configured in playground.
- **FormatRetrievedDocuments**: This node formats the output of the **RetrieveDocuments** node and sends it to the **DetermineReply** node.
- **DetermineReply**: This node contains an extensive system prompt, which asks the LLM to respond using the retrieved documents only. There are two inputs: 
    - The **RetrieveDocuments** node provides the top retrieved documents.
    - The **FormatConversation** node provides the formatted conversation history including the latest query. 

The **FormatReply** node formats the output of the **DetermineReply** node.

In prompt flow, you should also see:
- **Save**: You can save your prompt flow at any time by selecting **Save** from the top menu. Be sure to save your prompt flow periodically as you make changes in this tutorial. 
- **Compute session.**: The compute session that you created [earlier in this tutorial](#create-compute-and-compute session-that-are-needed-for-prompt-flow). You can start and stop compute sessions and compute instances via **Project settings** in the left menu. To work in prompt flow, make sure that your compute session is in the **Running** status.

    :::image type="content" source="../media/tutorials/chat/prompt-flow-overview.png" alt-text="Screenshot of the prompt flow editor and surrounding menus." lightbox="../media/tutorials/chat/prompt-flow-overview.png":::

- **Tools**: You can return to the prompt flow anytime by selecting **Prompt flow** from **Tools** in the left menu. Then select the prompt flow folder that you created earlier (not the sample flow).

   :::image type="content" source="../media/tutorials/chat/prompt-flow-return.png" alt-text="Screenshot of the list of your prompt flows." lightbox="../media/tutorials/chat/prompt-flow-return.png":::


## Customize prompt flow with multiple data sources

Earlier in the [Azure AI Studio](https://ai.azure.com) playground, you [added your data](#add-your-data-and-try-the-chat-model-again) to create one search index that contained product data for the Contoso copilot. So far, users can only inquire about products with questions such as "How much do the TrailWalker hiking shoes cost?". But they can't get answers to questions such as "How many TrailWalker hiking shoes did Daniel Wilson buy?" To enable this scenario, we add another index with customer information to the flow.

### Create the customer info index

You need a local copy of example customer information. For more information and links to example data, see the [prerequisites](#prerequisites).

Follow these instructions on how to create a new index:

1. Select **Index** from the left menu. Then select **+ New index**.

    :::image type="content" source="../media/tutorials/chat/add-index-new.png" alt-text="Screenshot of the indexes page with the button to create a new index." lightbox="../media/tutorials/chat/add-index-new.png":::

    You're taken to the **Create an index** wizard. 

1. On the **Source data** page, select **Upload files** from the **Data source** dropdown. Then select **Upload** > **Upload folder** to browse your local files. 
1. Select the customer info files that you downloaded or created earlier. See the [prerequisites](#prerequisites). Then select **Next**.

    :::image type="content" source="../media/tutorials/chat/add-index-dataset-upload-folder.png" alt-text="Screenshot of the customer data source selection options." lightbox="../media/tutorials/chat/add-index-dataset-upload-folder.png":::

1. Select the same Azure AI Search service connection (*contosooutdooraisearch*) that you used for your product info index. Then select **Next**.
1. Enter **customer-info** for the index name. 

    :::image type="content" source="../media/tutorials/chat/add-index-settings.png" alt-text="Screenshot of the Azure AI Search service and index name." lightbox="../media/tutorials/chat/add-index-settings.png":::

1. Select a virtual machine to run indexing jobs. The default option is **Auto select**. Then select **Next**.
1. On the **Search settings** page under **Vector settings**, deselect the **Add vector search to this search resource** checkbox. This setting helps determine how the model responds to requests. Then select **Next**.
    
    > [!NOTE]
    > If you add vector search, more options would be available here for an additional cost. 


1. Review the details you entered, and select **Create**. 

    :::image type="content" source="../media/tutorials/chat/add-index-review.png" alt-text="Screenshot of the review and finish index creation page." lightbox="../media/tutorials/chat/add-index-review.png":::

    > [!NOTE]
    > You use the *customer-info* index and the *contosooutdooraisearch* connection to your Azure AI Search service in prompt flow later in this tutorial. If the names you enter differ from what's specified here, make sure to use the names you entered in the rest of the tutorial.

1. You're taken to the index details page where you can see the status of your index creation.

    :::image type="content" source="../media/tutorials/chat/add-index-created-details.png" alt-text="Screenshot of the customer info index details." lightbox="../media/tutorials/chat/add-index-created-details.png":::

For more information on how to create an index, see [Create an index](../how-to/index-add.md).

## Create a compute sessions that's needed for prompt flow

You use prompt flow to optimize the messages that are sent to the copilot's chat model. Prompt flow requires a compute instance and a compute session. To create a compute instance and a compute session, follow the steps in [how to create a compute session](../how-to/create-manage-compute-session.md).

To complete the rest of the tutorial, make sure that your compute session is in the **Running** status. You might need to select **Refresh** to see the updated status.

> [!IMPORTANT]
> You're charged for compute instances while they are running. To avoid incurring unnecessary Azure costs, pause the compute instance when you're not actively working in prompt flow. For more information, see [how to start and stop compute](../how-to/create-manage-compute.md#start-or-stop-a-compute-instance).

### Add customer information to the flow

> [!WARNING]
> Azure AI Studio is in preview and is subject to change. The screenshots and instructions in this tutorial might not match the current experience. 

After you're done creating your index, return to your prompt flow and follow these steps to add the customer info to the flow:

1. Select the **RetrieveDocuments** node from the graph and rename it **RetrieveProductInfo**. Now the retrieve product info node can be distinguished from the retrieve customer info node that you add to the flow.

   :::image type="content" source="../media/tutorials/chat/node-rename-retrieve-product-info.png" alt-text="Screenshot of the prompt flow node for retrieving product info." lightbox="../media/tutorials/chat/node-rename-retrieve-product-info.png":::

1. Select **+ Python** from the top menu to create a new [Python node](../how-to/prompt-flow-tools/python-tool.md) that's used to retrieve customer information.  

   :::image type="content" source="../media/tutorials/chat/node-new-retrieve-customer-info.png" alt-text="Screenshot of the prompt flow node for retrieving customer info." lightbox="../media/tutorials/chat/node-new-retrieve-customer-info.png":::

1. Name the node **RetrieveCustomerInfo** and select **Add**.
1. Copy and paste the Python code from the **RetrieveProductInfo** node into the **RetrieveCustomerInfo** node to replace all of the default code. 
1. Select the **Validate and parse input** button to validate the inputs for the **RetrieveCustomerInfo** node. If the inputs are valid, prompt flow parses the inputs and creates the necessary variables for you to use in your code.

   :::image type="content" source="../media/tutorials/chat/customer-info-validate-parse.png" alt-text="Screenshot of the validate and parse input button." lightbox="../media/tutorials/chat/customer-info-validate-parse.png":::

1. Edit the **RetrieveCustomerInfo** inputs that prompt flow parsed for you so that it can connect to your *customer-info* index.

   :::image type="content" source="../media/tutorials/chat/customer-info-edit-inputs.png" alt-text="Screenshot of inputs to edit in the retrieve customer info node." lightbox="../media/tutorials/chat/customer-info-edit-inputs.png":::

    > [!NOTE]
    > The graph is updated immediately after you set the **queries** input value to **ExtractIntent.output.search_intents**. In the graph you can see that **RetrieveCustomerInfo** gets inputs from **ExtractIntent**.

    The inputs are case sensitive, so be sure they match these values exactly:
    
    | Name | Type | Value |
    |----------|----------|-----------|
    | **embeddingModelConnection** | Azure OpenAI | *Default_AzureOpenAI* |
    | **embeddingModelName** | string | *None* |
    | **indexName** | string | *customer-info* |
    | **queries** | string | *${ExtractIntent.output.search_intents}* |
    | **queryType** | string | *simple* |
    | **searchConnection** | Azure AI Search | *contosooutdooraisearch* |
    | **semanticConfiguration** | string | *None* |
    | **topK** | int | *5* |

1. Select **Save** from the top menu to save your changes.

### Format the retrieved documents to output

> [!WARNING]
> Azure AI Studio is in preview and is subject to change. The screenshots and instructions in this tutorial might not match the current experience. 

Now that you have both the product and customer info in your prompt flow, you format the retrieved documents so that the large language model can use them.

1. Select the **FormatRetrievedDocuments** node from the graph.
1. Copy and paste the following Python code to replace all contents in the **FormatRetrievedDocuments** code block. 

    ```python
    from promptflow import tool
     
    @tool
    def format_retrieved_documents(docs1: object, docs2: object, maxTokens: int) -> str:
      formattedDocs = []
      strResult = ""
      docs = [val for pair in zip(docs1, docs2) for val in pair]
      for index, doc in enumerate(docs):
        formattedDocs.append({
          f"[doc{index}]": {
            "title": doc['title'],
            "content": doc['content']
          }
        })
        formattedResult = { "retrieved_documents": formattedDocs }
        nextStrResult = str(formattedResult)
        if (estimate_tokens(nextStrResult) > maxTokens):
          break
        strResult = nextStrResult
      
      return {
              "combined_docs": docs,
              "strResult": strResult
          }
     
    def estimate_tokens(text: str) -> int:
      return (len(text) + 2) / 3
    ```

1. Select the **Validate and parse input** button to validate the inputs for the **FormatRetrievedDocuments** node. If the inputs are valid, prompt flow parses the inputs and creates the necessary variables for you to use in your code.

1. Edit the **FormatRetrievedDocuments** inputs that prompt flow parsed for you so that it can extract product and customer info from the **RetrieveProductInfo** and **RetrieveCustomerInfo** nodes.

   :::image type="content" source="../media/tutorials/chat/format-retrieved-documents-edit-inputs.png" alt-text="Screenshot of inputs to edit in the format retrieved documents node." lightbox="../media/tutorials/chat/format-retrieved-documents-edit-inputs.png":::

    The inputs are case sensitive, so be sure they match these values exactly:
    
    | Name | Type | Value |
    |----------|----------|-----------|
    | **docs1** | object | *${RetrieveProductInfo.output}* |
    | **docs2** | object | *${RetrieveCustomerInfo.output}* |
    | **maxTokens** | int | *5000* |

1. Select the **DetermineReply** node from the graph.
1. Set the **documentation** input to *${FormatRetrievedDocuments.output.strResult}*.

   :::image type="content" source="../media/tutorials/chat/determine-reply-edit-inputs.png" alt-text="Screenshot of editing the documentation input value in the determine reply node." lightbox="../media/tutorials/chat/determine-reply-edit-inputs.png":::

1. Select the **outputs** node from the graph.
1. Set the **fetched_docs** input to *${FormatRetrievedDocuments.output.combined_docs}*.

   :::image type="content" source="../media/tutorials/chat/outputs-edit.png" alt-text="Screenshot of editing the fetched_docs input value in the outputs node." lightbox="../media/tutorials/chat/outputs-edit.png":::

1. Select **Save** from the top menu to save your changes.

### Chat in prompt flow with product and customer info

By now you have both the product and customer info in prompt flow. You can chat with the model in prompt flow and get answers to questions such as "How many TrailWalker hiking shoes did Daniel Wilson buy?" Before proceeding to a more formal evaluation, you can optionally chat with the model to see how it responds to your questions.

1. Select **Chat** from the top menu in prompt flow to try chat.
1. Enter "How many TrailWalker hiking shoes did Daniel Wilson buy?" and then select the right arrow icon to send.
1. The response is what you expect. The model uses the customer info to answer the question.

   :::image type="content" source="../media/tutorials/chat/chat-with-data-customer.png" alt-text="Screenshot of the assistant's reply with product and customer grounding data." lightbox="../media/tutorials/chat/chat-with-data-customer.png":::

## Evaluate the flow using a question and answer evaluation dataset

In [Azure AI Studio](https://ai.azure.com), you want to evaluate the flow before you [deploy the flow](#deploy-the-flow) for [consumption](#use-the-deployed-flow).

In this section, you use the built-in evaluation to evaluate your flow with a question and answer evaluation dataset. The built-in evaluation uses AI-assisted metrics to evaluate your flow: groundedness, relevance, and retrieval score. For more information, see [built-in evaluation metrics](../concepts/evaluation-metrics-built-in.md).

### Create an evaluation

You need a question and answer evaluation dataset that contains questions and answers that are relevant to your scenario. Create a new file locally named **qa-evaluation.jsonl**. Copy and paste the following questions and answers (`"truth"`) into the file.

```json
{"question": "What color is the CozyNights Sleeping Bag?", "truth": "Red", "chat_history": [], }
{"question": "When did Daniel Wilson order the BaseCamp Folding Table?", "truth": "May 7th, 2023", "chat_history": [] }
{"question": "How much does TrailWalker Hiking Shoes cost? ", "truth": "$110", "chat_history": [] }
{"question": "What kind of tent did Sarah Lee buy?", "truth": "SkyView 2 person tent", "chat_history": [] }
{"question": "What is Melissa Davis's phone number?", "truth": "555-333-4444", "chat_history": [] }
{"question": "What is the proper care for trailwalker hiking shoes?", "truth": "After each use, remove any dirt or debris by brushing or wiping the shoes with a damp cloth.", "chat_history": [] }
{"question": "Does TrailMaster Tent come with a warranty?", "truth": "2 years", "chat_history": [] }
{"question": "How much did David Kim spend on the TrailLite Daypack?", "truth": "$240", "chat_history": [] }
{"question": "What items did Amanda Perez purchase?", "truth": "TrailMaster X4 Tent, TrekReady Hiking Boots (quantity 3), CozyNights Sleeping Bag, TrailBlaze Hiking Pants, RainGuard Hiking Jacket, and CompactCook Camping Stove", "chat_history": [] }
{"question": "What is the Brand for TrekReady Hiking Boots", "truth": "TrekReady", "chat_history": [] }
{"question": "How many items did Karen Williams buy?", "truth": "three items of the Summit Breeze Jacket", "chat_history": [] }
{"question": "France is in Europe", "truth": "Sorry, I can only truth questions related to outdoor/camping gear and equipment", "chat_history": [] }
```

Now that you have your evaluation dataset, you can evaluate your flow by following these steps:

1. Select **Evaluate** > **Built-in evaluation** from the top menu in prompt flow.
   
    :::image type="content" source="../media/tutorials/chat/evaluate-built-in-evaluation.png" alt-text="Screenshot of the option to create a built-in evaluation from prompt flow." lightbox="../media/tutorials/chat/evaluate-built-in-evaluation.png":::

    You're taken to the **Create a new evaluation** wizard.

1. Enter a name for your evaluation and select a compute session.
1. Select **Question and answer pairs with retrieval-augmented generation** from the scenario options.

    :::image type="content" source="../media/tutorials/chat/evaluate-basic-scenario.png" alt-text="Screenshot of selecting an evaluation scenario." lightbox="../media/tutorials/chat/evaluate-basic-scenario.png":::

1. Select the flow to evaluate. In this example, select *Contoso outdoor flow* or whatever you named your flow. Then select **Next**.

1. Select the metrics you want to use to evaluate your flow. In this example, select **Groundedness**, **Relevance**, and **Retrieval score**. 

    :::image type="content" source="../media/tutorials/chat/evaluate-metrics.png" alt-text="Screenshot of selecting evaluation metrics." lightbox="../media/tutorials/chat/evaluate-metrics.png":::

1. Select a model to use for evaluation. In this example, select **gpt-35-turbo-16k**. Then select **Next**.

    > [!NOTE]
    > Evaluation with AI-assisted metrics needs to call another GPT model to do the calculation. For best performance, use a model that supports at least 16k tokens such as gpt-4-32k or gpt-35-turbo-16k model. If you didn't previously deploy such a model, you can deploy another model by following the steps in [the AI Studio chat playground quickstart](../quickstarts/get-started-playground.md#deploy-a-chat-model). Then return to this step and select the model you deployed.

1. Select **Add new dataset**. Then select **Next**.

    :::image type="content" source="../media/tutorials/chat/evaluate-add-dataset.png" alt-text="Screenshot of the option to use a new or existing dataset." lightbox="../media/tutorials/chat/evaluate-add-dataset.png":::

1. Select **Upload files**, browse files, and select the **qa-evaluation.jsonl** file that you created earlier. 

    :::image type="content" source="../media/tutorials/chat/evaluate-upload-files.png" alt-text="Screenshot of the dataset upload files button." lightbox="../media/tutorials/chat/evaluate-upload-files.png":::

1. After the file is uploaded, you need to map the properties from the file (data source) to the evaluation properties. Enter the following values for each data source property:

    :::image type="content" source="../media/tutorials/chat/evaluate-map-data-source.png" alt-text="Screenshot of the evaluation dataset mapping." lightbox="../media/tutorials/chat/evaluate-map-data-source.png":::

    | Name | Description | Type | Data source |
    |----------|----------|-----------|-----------|
    | **chat_history** | The chat history | list | *${data.chat_history}* |
    | **query** | The query | string | *${data.question}* |
    | **question** | A query seeking specific information | string | *${data.question}* |
    | **answer** | The response to question generated by the model as answer | string | ${run.outputs.reply} |
    | **documents** | String with context from retrieved documents | string | ${run.outputs.fetched_docs} |

1. Select **Next**.
1. Review the evaluation details and then select **Submit**. 

    :::image type="content" source="../media/tutorials/chat/evaluate-review-finish.png" alt-text="Screenshot of the review and finish page within the create evaluation dialog." lightbox="../media/tutorials/chat/evaluate-review-finish.png":::

    You're taken to the **Metric evaluations** page.

### View the evaluation status and results

Now you can view the evaluation status and results by following these steps:

1. After you [create an evaluation](#create-an-evaluation), if you aren't there already go to **Build** > **Evaluation**. On the **Metric evaluations** page, you can see the evaluation status and the metrics that you selected. You might need to select **Refresh** after a couple of minutes to see the **Completed** status.

    :::image type="content" source="../media/tutorials/chat/evaluate-status-completed.png" alt-text="Screenshot of the metric evaluations page." lightbox="../media/tutorials/chat/evaluate-status-completed.png":::

    > [!TIP]
    > Once the evaluation is in **Completed** status, you don't need compute session or compute to complete the rest of this tutorial. You can stop your compute instance to avoid incurring unnecessary Azure costs. For more information, see [how to start and stop compute](../how-to/create-manage-compute.md#start-or-stop-a-compute-instance).

1. Select the name of the evaluation that completed first (*contoso-evaluate-from-flow_variant_0*) to see the evaluation details with the columns that you mapped earlier.

    :::image type="content" source="../media/tutorials/chat/evaluate-view-results-detailed.png" alt-text="Screenshot of the detailed metrics results page." lightbox="../media/tutorials/chat/evaluate-view-results-detailed.png":::

1. Select the name of the evaluation that completed second (*evaluation_contoso-evaluate-from-flow_variant_0*) to see the evaluation metrics: **Groundedness**, **Relevance**, and **Retrieval score**.

    :::image type="content" source="../media/tutorials/chat/evaluate-view-results-metrics.png" alt-text="Screenshot of the average metrics scores." lightbox="../media/tutorials/chat/evaluate-view-results-metrics.png":::

For more information, see [view evaluation results](../how-to/evaluate-flow-results.md).

## Deploy the flow

Now that you [built a flow](#create-a-prompt-flow-from-the-playground) and completed a metrics-based [evaluation](#evaluate-the-flow-using-a-question-and-answer-evaluation-dataset), it's time to create your online endpoint for real-time inference. That means you can use the deployed flow to answer questions in real time.

Follow these steps to deploy a prompt flow as an online endpoint from [Azure AI Studio](https://ai.azure.com).

1. Have a prompt flow ready for deployment. If you don't have one, see [how to build a prompt flow](../how-to/flow-develop.md).
1. Optional: Select **Chat** to test if the flow is working correctly. Testing your flow before deployment is recommended best practice.

1. Select **Deploy** on the flow editor. 

    :::image type="content" source="../media/tutorials/chat/deploy-from-flow.png" alt-text="Screenshot of the deploy button from a prompt flow editor." lightbox = "../media/tutorials/chat/deploy-from-flow.png":::

1. Provide the requested information on the **Basic Settings** page in the deployment wizard. 

    :::image type="content" source="../media/tutorials/chat/deploy-basic-settings.png" alt-text="Screenshot of the basic settings page in the deployment wizard." lightbox = "../media/tutorials/chat/deploy-basic-settings.png":::

1. Select **Next** to proceed to the advanced settings pages. 
1. On the **Advanced settings - Endpoint** page, leave the default settings and select **Next**. 
1. On the **Advanced settings - Deployment** page, leave the default settings and select **Next**. 
1. On the **Advanced settings - Outputs & connections** page, make sure all outputs are selected under **Included in endpoint response**. 

    :::image type="content" source="../media/tutorials/chat/deploy-advanced-outputs-connections.png" alt-text="Screenshot of the advanced settings page in the deployment wizard." lightbox = "../media/tutorials/chat/deploy-advanced-outputs-connections.png":::

1. Select **Review + Create** to review the settings and create the deployment. 
1. Select **Create** to deploy the prompt flow.  

    :::image type="content" source="../media/tutorials/chat/deploy-review-create.png" alt-text="Screenshot of the review prompt flow deployment settings page." lightbox = "../media/tutorials/chat/deploy-review-create.png":::

For more information, see [how to deploy a flow](../how-to/flow-deploy.md).

## Use the deployed flow

Your copilot application can use the deployed prompt flow to answer questions in real time. You can use the REST endpoint or the SDK to use the deployed flow.

1. To view the status of your deployment in [Azure AI Studio](https://ai.azure.com), select **Deployments** from the left navigation. Once the deployment is created successfully, you can select the deployment to view the details.

    :::image type="content" source="../media/tutorials/chat/deployments-state-updating.png" alt-text="Screenshot of the prompt flow deployment state in progress." lightbox = "../media/tutorials/chat/deployments-state-updating.png":::

    > [!NOTE]
    > If you see a message that says "Currently this endpoint has no deployments" or the **State** is still *Updating*, you might need to select **Refresh** after a couple of minutes to see the deployment.

1. Optionally, the details page is where you can change the authentication type or enable monitoring.

    :::image type="content" source="../media/tutorials/chat/deploy-authentication-monitoring.png" alt-text="Screenshot of the prompt flow deployment details page." lightbox = "../media/tutorials/chat/deploy-authentication-monitoring.png":::

1. Select the **Consume** tab. You can see code samples and the REST endpoint for your copilot application to use the deployed flow. 

    :::image type="content" source="../media/tutorials/chat/deployments-score-url-samples.png" alt-text="Screenshot of the prompt flow deployment endpoint and code samples." lightbox = "../media/tutorials/chat/deployments-score-url-samples.png":::

## Clean up resources

To avoid incurring unnecessary Azure costs, you should delete the resources you created in this tutorial if they're no longer needed. To manage resources, you can use the [Azure portal](https://portal.azure.com?azure-portal=true). 

You can also [stop or delete your compute instance](../how-to/create-manage-compute.md#start-or-stop-a-compute-instance) in [Azure AI Studio](https://ai.azure.com) as needed.


## Azure AI Studio enterprise chat solution demo

Learn how to create a retail copilot using your data with Azure AI Studio in this [end-to-end walkthrough video](https://youtu.be/Qes7p5w8Tz8).
> [!VIDEO https://www.youtube.com/embed/Qes7p5w8Tz8]

## Next steps

* Learn more about [prompt flow](../how-to/prompt-flow.md).
* [Deploy an enterprise chat web app](./deploy-chat-web-app.md).
