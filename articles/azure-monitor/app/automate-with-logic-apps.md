---
title: Automate Application Insights processes by using Logic Apps
description: Learn how you can quickly automate repeatable processes by adding the Application Insights connector to your logic app.
ms.topic: conceptual
ms.date: 07/31/2022
author: guywild
ms.author: guywild
ms.reviewer: yossi-y
---

# Automate Application Insights processes by using Logic Apps

Do you find yourself repeatedly running the same queries on your telemetry data to check whether your service is functioning properly? Are you looking to automate these queries for finding trends and anomalies and then build your own workflows around them? The Application Insights connector for Azure Logic Apps is the right tool for this purpose.

> [!NOTE]
> The Application Insights connector has been replaced by the [Azure Monitor connector](../logs/logicapp-flow-connector.md). It's integrated with Azure Active Directory instead of requiring an API key. You can also use it to retrieve data from a Log Analytics workspace.

With this integration, you can automate numerous processes without writing a single line of code. You can create a logic app with the Application Insights connector to quickly automate any Application Insights process.

You can also add other actions. The Logic Apps feature of Azure App Service makes hundreds of actions available. For example, by using a logic app, you can automatically send an email notification or create a bug in Azure DevOps. You can also use one of the many available [templates](../../logic-apps/logic-apps-create-logic-apps-from-templates.md) to help speed up the process of creating your logic app.

## Create a logic app for Application Insights

In this tutorial, you learn how to create a logic app that uses the Log Analytics autocluster algorithm to group attributes in the data for a web application. The flow automatically sends the results by email. This example shows how you can use Application Insights analytics and Logic Apps together.

### Create a logic app
1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select **Create a resource** > **Web** > **Logic App**.

    ![Screenshot that shows the New logic app window.](./media/automate-with-logic-apps/1createlogicapp.png)

### Create a trigger for your logic app
1. In the **Logic Apps Designer** window, under **Start with a common trigger**, select **Recurrence**.

    ![Screenshot that shows the Logic Apps Designer window.](./media/automate-with-logic-apps/2logicappdesigner.png)

1. In the **Interval** box, enter **1**. In the **Frequency** box, select **Day**.

    ![Screenshot that shows the Logic Apps Designer Recurrence window.](./media/automate-with-logic-apps/3recurrence.png)

### Add an Application Insights action

1. Select **New step**.

1. In the **Choose an action** search box, enter **Application Insights**.

1. Under **Actions**, select **Visualize Analytics query - Azure Application Insights**.

    ![Screenshot that shows the Logic App Designer Choose an action window.](./media/automate-with-logic-apps/4visualize.png)

### Connect to an Application Insights resource

For this step, you need an application ID and an API key for your resource.

1. Select **API Access** > **Create API key**.

    ![Screenshot that shows the API Access page in the Azure portal with the Create API key button selected.](./media/automate-with-logic-apps/5apiaccess.png)

    ![Screenshot that shows the Application ID in the Azure portal.](./media/automate-with-logic-apps/6apikey.png)

1. Provide a name for your connection, the application ID, and the API key.

    ![Screenshot that shows the Logic App Designer flow connection window.](./media/automate-with-logic-apps/7connection.png)

### Specify the Log Analytics query and chart type
In the following example, the query selects the failed requests within the last day and correlates them with exceptions that occurred as part of the operation. Log Analytics correlates the failed requests based on the `operation_Id` identifier. The query then segments the results by using the autocluster algorithm.

When you create your own queries, verify that they're working properly in Log Analytics before you add them to your flow.

1. In the **Query** box, add the following Log Analytics query:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. In the **Chart Type** box, select **Html Table**.

    ![Screenshot that shows the Log Analytics query configuration window.](./media/automate-with-logic-apps/8query.png)

### Configure the logic app to send email

1. Select **New step**.

1. In the search box, enter **Office 365 Outlook**.

1. Select **Send an email - Office 365 Outlook**.

    ![Screenshot that shows the Send an email button on the Office 365 Outlook screen.](./media/automate-with-logic-apps/9sendemail.png)

1. In the **Send an email** window:

   1. Enter the email address of the recipient.
   1. Enter a subject for the email.
   1. Select anywhere in the **Body** box. On the **Dynamic content** menu that opens at the right, select **Body**.
   1. Select the **Add new parameter** dropdown list and select **Attachments** and **Is HTML**.

      ![Screenshot that shows the Send an email window with the Body box highlighted and the Dynamic content menu with Body highlighted on the right side.](./media/automate-with-logic-apps/10emailbody.png)

      ![Screenshot that shows the Add new parameter dropdown list in the Send an email window with the Attachments and Is HTML checkboxes selected.](./media/automate-with-logic-apps/11emailparameter.png)

1. On the **Dynamic content** menu:

    1. Select **Attachment Name**.
    1. Select **Attachment Content**.
    1. In the **Is HTML** box, select **Yes**.

       ![Screenshot that shows the Office 365 email configuration screen.](./media/automate-with-logic-apps/12emailattachment.png)

### Save and test your logic app

1. Select **Save** to save your changes.

    You can wait for the trigger to run the logic app, or you can run the logic app immediately by selecting **Run**.

    ![Screenshot that shows the Save button on the Logic Apps Designer screen.](./media/automate-with-logic-apps/13save.png)

    When your logic app runs, the recipients you specified in the email list will receive an email that looks like this example:

    ![Image that shows an email message generated by a logic app with a query result set.](./media/automate-with-logic-apps/email-generated-by-logic-app-generated-email.png)

    > [!NOTE]
    > The log app generates an email with a JPG file that depicts the query result set. If your query doesn't return results, the logic app won't create a JPG file.

## Next steps

- Learn more about creating [Log Analytics queries](../logs/get-started-queries.md).
- Learn more about [Logic Apps](../../logic-apps/logic-apps-overview.md).
