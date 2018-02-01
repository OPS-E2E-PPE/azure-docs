---
title: Train and test your LUIS app | Microsoft Docs
description: Use Language Understanding (LUIS) to continuously work on your application to refine it and improve its language understanding.
services: cognitive-services
author: DeniseMak
manager: rstand

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 12/14/2017
ms.author: v-demak
---

# Train and test your LUIS app

Training is the process of teaching your Language Understanding (LUIS) app to improve its natural language understanding. You train your LUIS app after you make updates by adding, editing, labeling, or deleting entities, intents, or utterances. When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. 

Training and testing an app is an iterative process. After you train your LUIS app, you test it with sample utterances to see if the intents and entities are recognized correctly. If they're not, make updates to the LUIS app, train, and test again. 

Typically, before retraining, you should [relabel any utterances](#relabel-utterances-and-retrain) in which LUIS failed to identify the expected intents and entities. You can find the utterances to relabel by using the following procedures:
 
  * **Use interactive testing**: The [interactive testing pane](#interactive-testing) lets you type in an utterance and displays the intents and entities that your LUIS app detects.
  * **Review endpoint utterances in LUIS.ai**: LUIS identifies the [utterances](./Label-Suggested-Utterances.md) to relabel for you.
  * **Review endpoint utterances from log**: LUIS provides a [log of utterances](./luis-resources-faq.md#how-do-i-download-a-log-of-user-utterances) from users that have been passed to the LUIS app endpoint. This log includes the intents and entities you can review to see if they've been correctly identified.
  

In addition to relabeling utterances, you can also add new utterances, edit the intent or entity types, and [add features](./Add-Features.md) to your LUIS app to improve performance. 

## Train your app
To start the iterative process of training, you first need to train your LUIS app at least once. 

1. Access your app by selecting its name on the **My Apps** page. 

2. In your app, select **Train** in the top panel. 

    ![Train button](./media/luis-how-to-train-test/train-button.png)

3. When training is complete, a green notification bar appears at the top of the browser.

    ![Train & Test App page](./media/luis-how-to-train-test/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>If you have one or more intents in your app that do not contain example utterances, you cannot train your app. Add utterances for all your intents. For more information, see [Add example utterances](Add-example-utterances.md).

## Test your app
[LUIS.ai](http://www.luis.ai) provides two types of testing: interactive testing and batch testing.

## Access the test page

1. Access your app  by selecting its name on the **My Apps** page. 
2. Select **Test** in your application's top panel to access the **Test** slide-out page.

    ![Train & Test App page](./media/luis-how-to-train-test/test.png)

## Interactive testing
With interactive testing, you can test both the current and published versions of your app and compare their results in one screen. 
 
The **Test** slide-out page displays a single test section by default. The second panel is the **Inspection panel**.

![Train & Test App page](./media/luis-how-to-train-test/test-3-panel.png)

* In the **Test panel**, you type the test utterance in the text box, select Enter to submit it to your LUIS app, and then view the LUIS model results. 

* In the **Inspection panel**, you  inspect the LUIS results, change the top scoring intent, and then compare to the published model. The top identified entity is also shown.

>[!NOTE]
>You can compare the trained but unpublished model against the published model. Be aware that any testing against the published model is deducted from your Azure subscription quota balance. 

In an interactive test, you submit individual test utterances and view the LUIS score for each utterance separately. 

>[!TIP]
>To close the **Test** panel, select the **Test** button again.

## Relabel utterances and retrain
When you perform interactive testing, you might find that LUIS doesn't detect the intent or entities the way you expect for some utterances. The following steps walk you through relabeling an utterance and retraining.

### Relabel an utterance to retrain intents and entities
1. Import the sample LUIS app <a href="https://aka.ms/luis-travel-agent-01" target="_blank">Travel Agent - Sample 1</a>. This LUIS app has only a few sample utterances and provides a starting point for training. It has the following intents:
     * BookFlight
    * Weather.GetForecast
    * None 

2. Select the **Train** button in the top bar to train the new app.

3. On the **Test** panel, type in **buy a plane ticket to bangor me** and select Enter. Instead of the **BookFlight** intent, the test results show **Weather.GetForecast**.

    ![Interactive testing identifies the wrong intent](./media/luis-how-to-train-test/test-weather-1.png)

4. You need to teach LUIS that **buy a plane ticket to bangor me** should be mapped to the **BookFlight** intent. You teach LUIS about an utterance's intent by adding the utterance to the correct intent: 

      a. Go to the **Intents** page and select the **BookFlight** intent.

      b. Type **buy a plane ticket to bangor me** into the text box, and then select Enter. 

5. Select the **Train** button in the top bar to train the new app to retry LUIS with this new utterance.

6. Go back to the **Test** panel, type **book a flight to bangor** in the text box, and select Enter. 

    ![Interactive testing identifies the expected intent](./media/luis-how-to-train-test/test-weather-2.png)

   > [!NOTE]
   > In this step, you choose an utterance that's similar to the one you labeled, but *not* exactly the same. This similarity helps to test your LUIS app's ability to generalize.

7. Now the intent should be correctly detected as **BookFlight**. However, **bangor** isn't detected as a location yet.

    ![The intent is correctly identified but the location entity isn't detected](./media/luis-how-to-train-test/test-weather-2-no-entities.png)

8. You need to teach LUIS that **bangor me** in the utterance **buy a plane ticket to bangor me** should be mapped to the **Location** entity for Bangor, Maine: 

      a. Go to the **Intents** page, select the **BookFlight** intent, and find **buy a plane ticket to bangor me** in the list of utterances. 

      b. Select the words **bangor me** and choose the **Location** entity from the entity list.

      c. Select the **Location::ToLocation** hierarchical entity from the drop-down list.
 
    ![label the words bangor me as a Location entity](./media/luis-how-to-train-test/location-tolocation.png)

9. Select the **Train** button in the top bar to train the new app to retry LUIS with this new entity for that utterance.

10. After training succeeds, select **Test**, type **buy a plane ticket to paris** in the text box, and select Enter. Now the location entity is correctly detected.

    ![Testing identifies the location entity](./media/luis-how-to-train-test/test-weather-2-entity-detected.png)

> [!NOTE]
> In this step, you choose an utterance that's similar to the one you labeled, but *not* exactly the same. This similarity helps to test your LUIS app's ability to generalize.

### Perform interactive testing on current and published models
In this section, you publish the existing model, change the model, and then test an utterance to compare the published versus non-published model results.

1. Close the **Test** panel by selecting the **Test** button in the top bar. 

2. On the **Publish** page, publish your model. 

3. Select the **Test** button to reopen the **Test panel**.

4. Type **book me a flight to Boston tomorrow** as your test utterance and select Enter. The LUIS results of the test utterance in both the current and published models are shown in the following image: 

    ![Interactive testing of both current & published models](./media/luis-how-to-train-test/comparison.png)

If you are interactive testing on both trained and published models together, an entity might have a different prediction in each model.

>[!NOTE]
>About the interactive testing console:
 >- You can type as many test utterances as you want in the **Test panel**, but only one utterance at a time.
 >- The **Inspection panel** shows the result of the latest utterance. 
 >- To review the result of a previous utterance, just select it in the **Test panel** and its result displays on the right. 
 >- To clear all the entered test utterances and their results from the test console, select **Start over** at the upper-left corner of the **Test panel**. 


## Batch testing
With batch testing, you can run a comprehensive test on your current trained model to measure its performance in language understanding. In batch testing, you submit a large number of test utterances collectively in a batch file, known as a *dataset*. The dataset file should be written in JSON format and contain a maximum of 1,000 utterances. All you need to do is import this file to your app and run it to perform the test. Your LUIS app returns the result: detailed analysis of all utterances included in the batch.

You can import up to 10 dataset files to a single LUIS app. The utterances included in the dataset should be different from the example utterances you previously added while building your app. 
 
The following procedures guide you on how to import a dataset file, run a batch test on your current trained app by using the imported dataset, and access the test results in a detailed visualized view.

### Import a dataset file

1. Select **Test** in the top bar, and then select **Batch testing panel**.

    ![Batch Testing Link](./media/luis-how-to-train-test/batch-testing-link.png)

2. Select **Import dataset**. The **Import new dataset** dialog box appears. Select **Choose File** and locate the JSON file that contains the utterances to test.

    ![Import Dataset File](./media/luis-how-to-train-test/batchtest-importset.png)

3. In the **Dataset Name** field, type a name for your dataset file. An example of the JSON in the batch file follows:

    ```JSON
[
    {
        "text": "go to paris",
        "intent": "BookFlight",
        "entities": [
            {
                "entityName": "location",
                "startPos": 6,
                "endPos": 10
            }
        ]
    },
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book me a flight to paris",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "Location::ToLocation",
                "startPos": 20,
                "endPos": 24
            }
        ]
    },
    {
        "text": "book a flight from seattle to hong kong",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "Location::FromLocation",
                "startPos": 19,
                "endPos": 25
            },
            {
                "entity": "Location::ToLocation",
                "startPos": 30,
                "endPos": 38
            }
        ]
    }
]
    ```

4. Select **Done**. The dataset file is added.

5. To export, rename, delete, or download the imported dataset, use the three dots (**...**) at the end of the dataset row. 

    ![Dataset Actions](./media/luis-how-to-train-test/batch-testing-options.png)

### Run a batch test on your trained app

Select **Test** next to the dataset you've just imported. This displays the test result of the dataset.

![Batch Test Result](./media/luis-how-to-train-test/run-test.png)

In the preceding screenshot:
 
 - **State** of the dataset shows whether or not the dataset result contains errors. In the preceding example, an error sign is displayed, which indicates that there are errors in one or more utterances. If the test result contains no errors, a green sign displays instead. 
 - **Size** is the total number of utterances included in the dataset file.
 - **Last Run** is the date of the latest test run for this dataset. 
 - **Last Result** displays the percentage of correct predictions that result from the test.

### Access test result details in a visualized view
 
1. Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. Green points indicate correct prediction, and red ones indicate incorrect prediction. 

    ![Visualized Batch Test Result](./media/luis-how-to-train-test/graph1.png) 

    >[!NOTE]
    >The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances. 
  
2. To filter the view by a specific intent/entity, select your target intent/entity in the filtering panel. The data points and their distribution update according to your selection. 
 
    ![Visualized Batch Test Result](./media/luis-how-to-train-test/filter-by-entity.png) 

    >[!NOTE]
    >Hover over a data point to see the certainty score of its prediction.
 
    The graph contains four sections that represent the possible cases of your application's prediction:

    - **True Positive (TP)**: The data points in this section represent utterances in which your app correctly predicted the existence of the target intent/entity. 
    - **True Negative (TN)**: The data points in this section represent utterances in which your app correctly predicted the absence of the target intent/entity.
    - **False Positive (FP)**: The data points in this section represent utterances in which your app incorrectly predicted the existence of the target intent/entity.
    - **False Negative (FN)**: The data points in this section represent utterances in which your app incorrectly predicted the absence of the target intent/entity.

    This means that data points on the **False Positive** and **False Negative** sections indicate errors, which should be investigated. On the other hand, if all data points are on the **True Positive** and **True Negative** sections, then your application's performance is perfect on this dataset.
 
3. Select a data point to retrieve its corresponding utterance in the utterances table at the bottom of the page. To display all utterances in a section, select the section title.
  
A batch test helps you view the performance of each intent and entity in your current trained model on a specific set of utterances. This helps you take appropriate actions, when required, to improve performance, such as adding more example utterances to an intent if your app frequently fails to identify it.

## Next steps

If testing indicates that your LUIS app doesn't recognize the correct intents and entities, you can work to improve your LUIS app's performance by labeling more utterances or adding features. 

* [Label suggested utterances with LUIS](Label-Suggested-Utterances.md) 
* [Use features to improve your LUIS app's performance](Add-Features.md) 

