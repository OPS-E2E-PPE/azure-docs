---
title: include file
description: include file
author: natekimball-msft
manager: koagbakp
services: azure-communication-services
ms.author: natekimball
ms.date: 03/24/2023
ms.topic: include
ms.service: azure-communication-services
ms.custom: mode-other
---

Get started with Azure Communication Services by using the Communication Services Java Email SDK to send Email messages.

## Understanding the email object model

The following classes and interfaces handle some of the major features of the Azure Communication Services Email SDK for Python.

| Name | Description |
| ---- |-------------|
| EmailAddress | This class contains an email address and an option for a display name. |
| EmailAttachment | This interface creates an email attachment by accepting a unique ID, email attachment [MIME type](../../../concepts/email/email-attachment-allowed-mime-types.md) string, and a string of content bytes. |
| EmailClient | This class is needed for all email functionality. You instantiate it with your connection string and use it to send email messages. |
| EmailMessage | This class combines the sender, content, and recipients. Custom headers, attachments, and reply-to email addresses can optionally be added, as well. |
| EmailSendResult | This class holds the results of the email send operation. It has an operation ID, operation status and error object (when applicable). |
| EmailSendStatus | This class represents the set of statuses of an email send operation. |

EmailSendResult returns the following status on the email operation performed.

| Status Name | Description |
| ----------- | ------------|
| NOT_STARTED | We're not sending this status from our service at this time. |
| IN_PROGRESS | The email send operation is currently in progress and being processed. |
| SUCCESSFULLY_COMPLETED | The email send operation has completed without error and the email is out for delivery. Any detailed status about the email delivery beyond this stage can be obtained either through Azure Monitor or through Azure Event Grid. [Learn how to subscribe to email events](../handle-email-events.md) |
| FAILED | The email send operation wasn't successful and encountered an error. The email wasn't sent. The result contains an error object with more details on the reason for failure or cancellation. |
| USER_CANCELLED | The email send operation was canceled before it could complete. The email wasn't sent. The result contains an error object with more details on the reason for failure or cancellation. |

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://www.microsoft.com/openjdk) version 8 or above.
- [Apache Maven](https://maven.apache.org/download.cgi).
- A deployed Communication Services resource and connection string. For details, see [Create a Communication Services resource](../../create-communication-resource.md).
- Create an [Azure Email Communication Services resource](../create-email-communication-resource.md) to start sending emails.
- A setup managed identity for a development environment, [see Authorize access with managed identity](../../identity/service-principal.md?pivot="programming-language-java").

Completing this quickstart incurs a small cost of a few USD cents or less in your Azure account.

> [!NOTE]
> We can also send an email from our own verified domain [Add custom verified domains to Email Communication Service](../add-custom-verified-domains.md).

### Prerequisite check
- In a terminal or command window, run `mvn -v` to check that Maven is installed.
- To view the domains verified with your Email Communication Services resource, sign in to the [Azure portal](https://portal.azure.com/). Locate your Email Communication Services resource and open the **Provision domains** tab from the left navigation pane.

## Set up the application environment

To set up an environment for sending emails, take the steps in the following sections.

### Create a new Java application
Open your terminal or command window and navigate to the directory where you would like to create your Java application. Run the following command to generate the Java project from the maven-archetype-quickstart template.

```console
mvn archetype:generate -DarchetypeArtifactId="maven-archetype-quickstart" -DarchetypeGroupId="org.apache.maven.archetypes" -DarchetypeVersion="1.4" -DgroupId="com.communication.quickstart" -DartifactId="communication-quickstart"
```

The `generate` goal creates a directory with the same name as the `artifactId` value. Under this directory, the **src/main/java** directory contains the project source code, the **src/test/java directory** contains the test source, and the **pom.xml** file is the project's Project Object Model (POM).

### Install the package

Open the **pom.xml** file in your text editor. Add the following dependency element to the group of dependencies.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-email</artifactId>
    <version>1.0.0-beta.2</version>
</dependency>
```

### Set up the app framework

Open **/src/main/java/com/communication/quickstart/App.java** in a text editor, add import directives, and remove the `System.out.println("Hello world!");` statement:

```java
package com.communication.quickstart;

import com.azure.communication.email.models.*;
import com.azure.communication.email.*;
import com.azure.core.util.polling.PollResponse;
import com.azure.core.util.polling.SyncPoller;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here.
    }
}
```

## Creating the email client with authentication

### Option 1: Authenticate using a connection string

To authenticate a client, you instantiate an `EmailClient` with your connection string. Learn how to [manage your resource's connection string](../../create-communication-resource.md#store-your-connection-string). You can also initialize the client with any custom HTTP client that implements the `com.azure.core.http.HttpClient` interface.

To instantiate a client, add the following code to the `main` method:

```java
// You can get your connection string from your resource in the Azure portal.
String connectionString = "endpoint=https://<resource-name>.communication.azure.com/;accesskey=<access-key>";

EmailClient emailClient = new EmailClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

### Option 2: Authenticate using Azure Active Directory

A [DefaultAzureCredential](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/identity/azure-identity#defaultazurecredential) object must be passed to the `EmailClientBuilder` via the `credential()` method. An endpoint must also be set via the `endpoint()` method.

The `AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID`, and `AZURE_TENANT_ID` environment variables are needed to create a `DefaultAzureCredential` object.

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<resource-name>.communication.azure.com/";
EmailClient emailClient = new EmailClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### Option 3: Authenticate using AzureKeyCredential

Email clients can also be created and authenticated using the endpoint and Azure Key Credential acquired from an Azure Communication Resource in the [Azure portal](https://portal.azure.com/).

```java
String endpoint = "https://<resource-name>.communication.azure.com";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key>");
EmailClient emailClient = new EmailClientBuilder()
    .endpoint(endpoint)
    .credential(azureKeyCredential)
    .buildClient();
```

For simplicity, this quickstart uses connection strings, but in production environments, we recommend using [service principals](../../../quickstarts/identity/service-principal.md).

## Basic email sending 

To send an email message, call the `beginSend` function from the `EmailClient`. This method returns a poller, which can be used to check on the status of the operation and retrieve the result once it's finished.

```java
EmailMessage message = new EmailMessage()
    .setSenderAddress("<donotreply@xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurecomm.net>")
    .setToRecipients("<emailalias@emaildomain.com>")
    .setSubject("Welcome to Azure Communication Services Email")
    .setBodyPlainText("This email message is sent from Azure Communication Services Email using the Java SDK.");

try
{
    SyncPoller<EmailSendResult, EmailSendResult> poller = emailClient.beginSend(message, null);

    PollResponse<EmailSendResult> pollResponse = null;

    Duration timeElapsed = Duration.ofSeconds(0);

    while (pollResponse == null
            || pollResponse.getStatus() == LongRunningOperationStatus.NOT_STARTED
            || pollResponse.getStatus() == LongRunningOperationStatus.IN_PROGRESS)
    {
        pollResponse = poller.poll();
        System.out.println("Email send poller status: " + pollResponse.getStatus());

        Thread.sleep(POLLER_WAIT_TIME.toMillis());
        timeElapsed = timeElapsed.plus(POLLER_WAIT_TIME);

        if (timeElapsed.compareTo(POLLER_WAIT_TIME.multipliedBy(18)) >= 0)
        {
            throw new RuntimeException("Polling timed out.");
        }
    }

    if (poller.getFinalResult().getStatus() == EmailSendStatus.SUCCEEDED)
    {
        System.out.printf("Successfully sent the email (operation id: %s)", poller.getFinalResult().getId());
    }
    else
    {
        throw new RuntimeException(poller.getFinalResult().getError().getMessage());
    }
}
catch (Exception exception)
{
    System.out.println(exception.getMessage());
}
```

Make these replacements in the code:

- Replace `<emailalias@emaildomain.com>` with the email address you would like to send a message to.
- Replace `<donotreply@xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurecomm.net>` with the MailFrom address of your verified domain.

### Run the code

1. Navigate to the directory that contains the **pom.xml** file and compile the project by using the `mvn` command.

   ```console
   mvn compile
   ```

1. Build the package.

   ```console
   mvn package
   ```

1. Run the following `mvn` command to execute the app.

   ```console
   mvn exec:java -D"exec.mainClass"="com.communication.quickstart.App" -D"exec.cleanupDaemonThreads"="false"
   ```

If you see that your application is hanging it could be due to email sending being throttled. You can [handle this through logging or by implementing a custom policy](#throw-an-exception-when-email-sending-tier-limit-is-reached).

### Sample code

You can download the sample app from [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/send-email)

## Advanced sending

### Send an email message to multiple recipients

To send an email message to multiple recipients, add the new addresses in the appropriate `EmailMessage` setter. These addresses can be added as `To`, `CC`, or `BCC` recipients.

```java
EmailMessage message = new EmailMessage()
    .setSenderAddress("<donotreply@xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurecomm.net>")
    .setSubject("Welcome to Azure Communication Services Email")
    .setBodyPlainText("This email message is sent from Azure Communication Services Email using the Java SDK.")
    .setToRecipients("<recipient1@emaildomain.com>", "<recipient2@emaildomain.com>")
    .setCcRecipients("<recipient3@emaildomain.com>")
    .setBccRecipients("<recipient4@emaildomain.com>");

SyncPoller<EmailSendResult, EmailSendResult> poller = emailClient.beginSend(message, null);
PollResponse<EmailSendResult> response = poller.waitForCompletion();

System.out.println("Operation Id: " + response.getValue().getId());
```

To customize the email message recipients further, you can instantiate the `EmailAddress` objects and pass that them to the appropriate `EmailMessage` setters.

```java
EmailAddress toAddress1 = new EmailAddress("<recipient1@emaildomain.com>")
    .setDisplayName("Recipient");

EmailAddress toAddress2 = new EmailAddress("<recipient2@emaildomain.com>")
    .setDisplayName("Recipient 2");

EmailMessage message = new EmailMessage()
    .setSenderAddress("<donotreply@xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurecomm.net>")
    .setSubject("Welcome to Azure Communication Services Email")
    .setBodyPlainText("This email message is sent from Azure Communication Services Email using the Java SDK.")
    .setToRecipients(toAddress1, toAddress2)
    .setCcRecipients(toAddress1, toAddress2)
    .setBccRecipients(toAddress1, toAddress2)

SyncPoller<EmailSendResult, EmailSendResult> poller = emailClient.beginSend(message, null);
PollResponse<EmailSendResult> response = poller.waitForCompletion();

System.out.println("Operation Id: " + response.getValue().getId());
```

You can download the sample app demonstrating this action from [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/send-email-advanced)

### Send an email message with attachments

We can add an attachment by defining an EmailAttachment object and adding it to our EmailMessage object. Read the attachment file and encode it using Base64.

```java
BinaryData attachmentContent = BinaryData.fromFile(new File("C:/attachment.txt").toPath());
EmailAttachment attachment = new EmailAttachment(
    "attachment.txt",
    "text/plain",
    attachmentContent
);

EmailMessage message = new EmailMessage()
    .setSenderAddress("<donotreply@xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurecomm.net>")
    .setToRecipients("<emailalias@emaildomain.com>")
    .setSubject("Welcome to Azure Communication Services Email")
    .setBodyPlainText("This email message is sent from Azure Communication Services Email using the Java SDK.");
    .setAttachments(attachment);

SyncPoller<EmailSendResult, EmailSendResult> poller = emailClient.beginSend(message, null);
PollResponse<EmailSendResult> response = poller.waitForCompletion();

System.out.println("Operation Id: " + response.getValue().getId());
```

For more information on acceptable MIME types for email attachments, see the [allowed MIME types](../../../concepts/email/email-attachment-allowed-mime-types.md) documentation.

You can download the sample app demonstrating this action from [GitHub](https://github.com/Azure-Samples/communication-services-java-quickstarts/tree/main/send-email-advanced)

### Throw an exception when email sending tier limit is reached

The Email API has throttling with limitations on the number of email messages that you can send. Email sending has limits applied per minute and per hour as mentioned in [API Throttling and Timeouts](https://learn.microsoft.com/azure/communication-services/concepts/service-limits). When you have reached these limits, additional email sends with `beginSend` calls will receive an error response of “429: Too Many Requests”. By default, the SDK is configured to retry these requests after waiting a certain period of time. We recommend you [set up logging with the Azure SDK](https://learn.microsoft.com/azure/developer/java/sdk/logging-overview) to capture these response codes.

Alternatively, you can manually define a custom policy as shown below. 

```java
import com.azure.core.http.HttpResponse;
import com.azure.core.http.policy.ExponentialBackoff;

public class CustomStrategy extends ExponentialBackoff {
    @Override
    public boolean shouldRetry(HttpResponse httpResponse) {
        int code = httpResponse.getStatusCode();

        if (code == HTTP_STATUS_TOO_MANY_REQUESTS) {
            throw new RuntimeException(httpResponse);
        }
        else {
            return super.shouldRetry(httpResponse);
        }
    }
}
```

Add this retry policy to your email client. This will ensure that 429 response codes throw an exception rather than being retried.

```java
import com.azure.core.http.policy.RetryPolicy;

EmailClient emailClient = new EmailClientBuilder()
    .connectionString(connectionString)
    .retryPolicy(new RetryPolicy(new CustomStrategy()))
    .buildClient();
```
