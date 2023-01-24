---
title: Use Form Recognizer containers in disconnected environments
titleSuffix: Azure Applied AI Services
description: Learn how to run Azure Cognitive Services Docker containers disconnected from the internet.
ms.service: applied-ai-services
ms.subservice: forms-recognizer
author: laujan
manager: nitinme
ms.topic: reference
ms.date: 01/23/2023
ms.author: lajanuar
---

# Use Form Recognizer containers in disconnected environments

<!-- markdownlint-disable MD036 -->
<!-- markdownlint-disable MD001 -->

Azure Cognitive Services Form Recognizer containers allow you to use Form Recognizer APIs with the benefits of containerization. Disconnected containers are offered through commitment tier pricing offered at a discounted rate compared to pay-as-you-go pricing. With commitment tier pricing, you can commit to using Form Recognizer features for a fixed fee, at a predictable total cost, based on the needs of your workload.

## Get started

Before attempting to run a Docker container in an offline environment, make sure you're familiar with the following requirements to successfully download and use the container:

* Host computer requirements and recommendations.
* The Docker `pull` command you'll use to download the container.
* How to validate that a container is running.
* How to send queries to the container's endpoint, once it's running.

## Request access to use containers in disconnected environments

Complete and submit the [request form](https://aka.ms/csdisconnectedcontainers) to request access to the containers disconnected from the Internet.

[!INCLUDE [Request access to public preview](../../../../includes/cognitive-services-containers-request-access.md)]

Access is limited to customers that meet the following requirements:

* Your organization should be identified as strategic customer or partner with Microsoft.
* Disconnected containers are expected to run fully offline, hence your use cases must meet one of the following or similar requirements:
  * Environment or device(s) with zero connectivity to internet.
  * Remote location that occasionally has internet access.
  * Organization under strict regulation of not sending any kind of data back to cloud.
* Application completed as instructed - Pay close attention to guidance provided throughout the application to ensure you provide all the necessary information required for approval.

## Create a new resource and purchase a commitment plan

1. Create a new [Form Recognizer resource](https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) in the Azure portal.

1. Enter the applicable information to create your resource. Be sure to select **Commitment tier disconnected containers** as your pricing tier.

    > [!NOTE]
    >
    > * You will only see the option to purchase a commitment tier if you have been approved by Microsoft.

    :::image type="content" source="../media/create-resource-offline-container.png" alt-text="A screenshot showing resource creation on the Azure portal.":::

1. Select **Review + Create** at the bottom of the page. Review the information, and select **Create**.

## Gather required parameters

There are three required parameters for all Cognitive Services' containers:

* The end-user license agreement (EULA) must be present with a value of *accept*.
* The endpoint URL for your resource from the Azure portal.
* The API key for your resource from the Azure portal.

Both the endpoint URL and API key are needed when you first run the container to configure it for disconnected usage. You can find the key and endpoint on the **Key and endpoint** page for your resource in the Azure portal:

  :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Azure portal keys and endpoint page.":::

> [!IMPORTANT]
> You will only use your key and endpoint to configure the container to run in a disconnected environment. After you configure the container, you won't need the key and endpoint values to send API requests. Store them securely, for example, using Azure Key Vault. Only one key is necessary for this process.

## Download a Docker container with `docker pull`

Download the Docker container that has been approved to run in a disconnected environment. For example:

|Docker pull command | Value |Format|
|----------|-------|------|
|&bullet; **`docker pull [image]`**</br>&bullet; **`docker pull [image]:latest`**|The latest container image.|&bullet; mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout</br>  </br>&bullet; mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice: latest |
|||
|&bullet; **`docker pull [image]:[version]`** | A specific container image |dockers pull mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt:2.1-preview |

  **Example Docker pull command**

```docker
docker pull mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice:latest
```

## Configure the container to be run in a disconnected environment

Now that you've downloaded your container, you'll need to execute the `docker run` command with the following parameter:

* **`DownloadLicense=True`**. This parameter will download a license file that will enable your Docker container to run when it isn't connected to the internet. It also contains an expiration date, after which the license file will be invalid to run the container. You can only use the license file in corresponding approved container.

> [!IMPORTANT]
>The `docker run` command will generate a template that you can use to run the container. The template contains parameters you'll need for the downloaded models and configuration file. Make sure you save this template.

The following example shows the formatting for the `docker run` command you'll use, with placeholder values. Replace these placeholder values with your own values.

| Placeholder | Value | Format or example |
|-------------|-------|---|
| `{IMAGE}` | The container image you want to use. | `mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice` |
| `{LICENSE_MOUNT}` | The path where the license will be downloaded, and mounted.  | `/host/license:/path/to/license/directory` |
| `{ENDPOINT_URI}` | The endpoint for authenticating your service request. You can find it on your resource's **Key and endpoint** page, on the Azure portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| `{API_KEY}` | The key for your Text Analytics resource. You can find it on your resource's **Key and endpoint** page, on the Azure portal. |`{string}`|
| `{CONTAINER_LICENSE_DIRECTORY}` | Location of the license folder on the container's local filesystem.  | `/path/to/license/directory` |

  **Example `docker run` command**

```docker

docker run --rm -it -p 5000:5000 \
 
-v {LICENSE_MOUNT} \

{IMAGE} \

eula=accept \

billing={ENDPOINT_URI} \

apikey={API_KEY} \

DownloadLicense=True \

Mounts:License={CONTAINER_LICENSE_DIRECTORY} 
```

After you've configured the container, use the next section to run the container in your environment with the license, and appropriate memory and CPU allocations.

## Form Recognizer container models and configuration

> [!IMPORTANT]
> If you're using the Translator, Neural text-to-speech, or Speech-to-text containers, read the **Additional parameters** section for information on commands or additional parameters you will need to use.

After you've [configured the container](#configure-the-container-to-be-run-in-a-disconnected-environment), the values for the downloaded translation models and container configuration will be generated and displayed in the container output:

```bash
-e MODELS= /path/to/model1/, /path/to/model2/
-e TRANSLATORSYSTEMCONFIG=/path/to/model/config/translatorsystemconfig.json
```

## Run the container in a disconnected environment

Once the license file has been downloaded, you can run the container in a disconnected environment with your license, appropriate memory, and suitable CPU allocations. The following example shows the formatting of the `docker run` command with placeholder values. Replace these placeholders values with your own values.

Whenever the container is run, the license file must be mounted to the container and the location of the license folder on the container's local filesystem must be specified with `Mounts:License=`. In addition, an output mount must be specified so that billing usage records can be written.

Placeholder | Value | Format or example |
|-------------|-------|---|
| `{IMAGE}` | The container image you want to use. | `mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice` |
 `{MEMORY_SIZE}` | The appropriate size of memory to allocate for your container. | `4g` |
| `{NUMBER_CPUS}` | The appropriate number of CPUs to allocate for your container. | `4` |
| `{LICENSE_MOUNT}` | The path where the license will be located and mounted.  | `/host/license:/path/to/license/directory` |
| `{OUTPUT_PATH}` | The output path for logging [usage records](#usage-records). | `/host/output:/path/to/output/directory` |
| `{CONTAINER_LICENSE_DIRECTORY}` | Location of the license folder on the container's local filesystem.  | `/path/to/license/directory` |
| `{CONTAINER_OUTPUT_DIRECTORY}` | Location of the output folder on the container's local filesystem.  | `/path/to/output/directory` |

  **Example `docker run` command**

```docker
docker run --rm -it -p 5000:5000 --memory {MEMORY_SIZE} --cpus {NUMBER_CPUS} \ 

-v {LICENSE_MOUNT} \

-v {OUTPUT_PATH} \

{IMAGE} \

eula=accept \

Mounts:License={CONTAINER_LICENSE_DIRECTORY}

Mounts:Output={CONTAINER_OUTPUT_DIRECTORY}
```

## Other parameters and commands

Here are a few more parameters and commands you may need to run the container.

#### Usage records

When operating Docker containers in a disconnected environment, the container will write usage records to a volume where they're collected over time. You can also call a REST API endpoint to generate a report about service usage.

#### Arguments for storing logs

When run in a disconnected environment, an output mount must be available to the container to store usage logs. For example, you would include `-v /host/output:{OUTPUT_PATH}` and `Mounts:Output={OUTPUT_PATH}` in the following example, replacing `{OUTPUT_PATH}` with the path where the logs will be stored:

```Docker
docker run -v /host/output:{OUTPUT_PATH} ... <image> ... Mounts:Output={OUTPUT_PATH}
```

#### Get records using the container endpoints

The container provides two endpoints for returning records about its usage.

#### Get all records

The following endpoint will provide a report summarizing all of the usage collected in the mounted billing record directory.

```http
https://<service>/records/usage-logs/
```

  **Example HTTPS endpoint**

  `http://localhost:5000/records/usage-logs`

The usage-log endpoint will return a JSON response similar to the following example:

```json
{
  "apiType": "string",
  "serviceName": "string",
  "meters": [
    {
      "name": "string",
      "quantity": 256345435
    }
  ]
}
```

#### Get records for a specific month

The following endpoint will provide a report summarizing usage over a specific month and year.

```HTTP
https://<service>/records/usage-logs/{MONTH}/{YEAR}
```

This usage-logs endpoint will return a JSON response similar to the following example:

```json
{
  "apiType": "string",
  "serviceName": "string",
  "meters": [
    {
      "name": "string",
      "quantity": 56097
    }
  ]
}
```

### Purchase a different commitment plan for disconnected containers

Commitment plans for disconnected containers have a calendar year commitment period. When you purchase a plan, you'll be charged the full price immediately. During the commitment period, you can't change your commitment plan, however you can purchase more unit(s) at a pro-rated price for the remaining days in the year. You have until midnight (UTC) on the last day of your commitment, to end a commitment plan.

You can choose a different commitment plan in the **Commitment tier pricing** settings of your resource under the **Resource Management** section.

### End a commitment plan

If you decide that you don't want to continue purchasing a commitment plan, you can set your resource's auto-renewal to **Do not auto-renew**. Your commitment plan will expire on the displayed commitment end date. After this date, you won't be charged for the commitment plan. You'll be able to continue using the Azure resource to make API calls, charged at pay-as-you-go pricing. You have until midnight (UTC) on the last day of the year to end a commitment plan for disconnected containers. If you cancel at or before that time, you won't be charged for the following year.

## Troubleshooting

Run the container with an output mount and logging enabled. These settings will enable the container generates log files that are helpful for troubleshooting issues that occur while starting or running the container.

> [!TIP]
> For more troubleshooting information and guidance, see [Disconnected containers Frequently asked questions (FAQ)](../../../cognitive-services/containers/disconnected-container-faq.yml).

## Next steps

[Deploy the Sample Labeling tool to an Azure Container Instance (ACI)](../deploy-label-tool.md#deploy-with-azure-container-instances-aci)
