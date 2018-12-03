﻿---
title: Data storage and ingress in The Azure Time Series Insights (preview) | Microsoft Docs
description: Understanding data storage and ingress in The Azure Time Series Insights (preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
---

# Data storage and ingress in the Azure Time Series Insights (preview)

This article describes changes to data storage and ingress from the Azure Time Series Insights (preview). It covers the underlying storage structure, file format, and **Time Series ID** property. It also discusses the underlying ingress process, throughput, and limitations.

## Data storage

When creating a Time Series Insights update (**PAYG Sku**) environment, you are creating two resources:

1. An Azure TSI environment.
1. An Azure storage general purpose V1 account where the data will be stored.

The TSI (preview) uses Azure Blob Storage with the Parquet file type. Azure TSI manages all the data operations including creating blobs, indexing, and partitioning the data in the Azure Storage account. These blobs are created using an Azure Storage account. To ensure that all events can be queried in a performant manner. The TSI update will support Azure Storage general-purpose V1 and V2 "hot" configuration options.  

Like any other Azure Storage blob, you can read and write to your Azure TSI-created blobs to support different integration scenarios.

> [!TIP]
> It is important to remember TSI performance can be adversely affected by reading or writing to your blobs too frequently.

For an overview of Azure Blob Storage works, read the [Storage blobs introduction](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

For more about the Parquet file type, review [Supported file types in Azure Storage](https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs#Parquet-format).

## Parquet file format

Parquet is column-oriented data file format that was designed for:

* Interoperability
* Space efficiency
* Query efficiency

Azure TSI chose Parquet since it provides efficient data compression and encoding schemes with enhanced performance to handle complex data in bulk.

For a better understanding of what the Parquet file format is all about, head over to the [official Parquet page](https://parquet.apache.org/documentation/latest/).

## Event structure in Parquet

Two copies of blobs created by Azure TSI will be stored in the following formats:

1. The first, an initial copy, will be partitioned by arrival time:

    * `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI internal suffix>.parquet`
    * Blob creation time for blobs partitioned by arrival time.

1. The second, a repartitioned copy, will be partitioned by dynamic grouping of time series ID:

    • `V=1/PT=TsId/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_< TSI internal suffix >.parquet`
    * Min event timestamp in the blob for blobs partitioned by time series ID.

> [!NOTE]
> * `<YYYY>` maps to year.
> * `<MM>` maps to month.
> * `<YYYYMMDDHHMMSSfff>` maps to full timestamp in milliseconds.

Azure TSI events are mapped to Parquet file contents as follows:

* Every event maps to a single row.
* Built-in **Timestamp** column with an event timestamp. The **Timestamp** property is never null.  It defaults to **Event Source Enqueued Time** if the **Timestamp** property is not specified in the event source.  **Timestamp** is in UTC.  
* All other properties map to columns will end with `_string` (string), `_bool` (boolean), `_datetime` (datetime), and `_double` (double) depending on property type.
* This is the first version of the file format, and we refer to it as ***V=1**.  If this feature evolves the name will be incremented accordingly to **V=2**, **V=3**, and so on.

## How to partition

Each Azure TSI (preview) environment must have a **Time Series ID** property and a **Timestamp** property, which uniquely identify it. Your **Time Series ID** acts as a logical partition for your data and provides the Azure TSI (preview) environment with a natural boundary for distributing data across physical partitions. Physical partition management is managed by Azure TSI (preview) in an Azure Storage account.

Azure TSI uses dynamic partitioning to optimize storage utilization and query performance by dropping and recreating partitions. The TSI (preview) dynamic partitioning algorithm strives to avoid a single physical partition having data for multiple different logical partitions. Or in other words the partitioning algorithm’s goal is to keep all data related to a single **Time Series ID** to be exclusively present in Parquet file(s) without being interleaved with other **Time Series IDs**. The dynamic partitioning algorithm also strives to preserve the original order of events within a single **Time Series ID**.

Initially, at ingress time, data is partitioned by the **Timestamp** so a single, logical partition within a given time range might be spread across multiple physical partitions. A single physical partition could also contain many or all logical partitions.  Due to blob size limitations, even with optimal partitioning a single logical partition could occupy multiple physical partitions.

> [!NOTE]
> The **Timestamp** value is the message **Enqueued Time** in your configured event source by default.  

If you are uploading historical data or batch messages, you should designate the **Timestamp** property in your data that maps to the appropriate **Timestamp** value you wish to store with your data.  The **Timestamp** property is case-sensitive. For more, read the [Time Series Model article](./time-series-insights-update-tsm.md).

## Physical partition

A physical partition is a block blob stored in Azure Storage. The actual size of the blobs will vary as it depends on the push rate, however we expect blobs to be approximately 20-50 MB in size. Because of that expectation, the TSI team selected 20 MB to be the size to optimize query performance. This could change over time based on file size and the velocity of data ingress.

> [!NOTE]
> * Blobs are sized at 20MB.
> * Azure blobs are occasionally repartitioned for better performance by being dropped and recreated.
> * Also note that the same TSI data can be present in multiple blobs.

## Logical partition

A logical partition is a partition within a physical partition that stores all the data associated with a single partition key value. The TSI (preview) will logically partition each blob based on two properties:

1. **Time Series ID** - is the partition key for all TSI data within the event stream and the model.
1. **Timestamp** - based on the initial ingress.

The Azure TSI (preview) provides performant queries that are based on these two properties. These two properties also provide the most effective method for delivering TSI data quickly.

It is important to select an appropriate **Time Series IDs**, as it is an immutable property.  Please see [Choosing Time Series IDs](./time-series-insights-update-how-to-id.md) for more information.

## Your Azure Storage account

### Storage

When you create a TSI PAYG environment, you create two resources – the TSI environment and an Azure storage general purpose V1 account where the data will be stored. In the future, Azure Storage general purpose V1 accounts will be limited to existing Azure customers, therefore all new customers to Azure provisioning a TSI (preview) environment will by default create an Azure Storage general purpose V2 ‘Hot’ account.  We chose to make Azure Storage general purpose V1 the default due to its interoperability, price, and performance.  

Azure TSI will publish up to two copies of each event in your Azure Storage account. The initial copy is always preserved to ensure you can query it performantly using other services. Thus, Spark, Hadoop, or other familiar tools can be easily used across **Time Series IDs** over raw Parquet files since these engines support basic file name filtering. Grouping blobs by year and month is useful to collect blob list within specific time range for a custom job.  

Additionally, TSI will repartition the Parquet files to optimize for the Azure TSI APIs, and the most recently repartitioned file will also be saved.

During Public Preview, data will be stored indefinitely in your Azure Storage account. The ability to delete data will be added in the future, allowing a greater degree of control of managing old data.

### Writing and editing Time Series Insights blobs

To ensure query performance and data availability, do not edit or delete any blobs created by TSI.

### Accessing and exporting data from Time Series Insights (preview)

You may want to access data stored in Azure TSI (preview) Explorer to use in conjunction with other services. For example, you may want to use your data for reporting in Power BI, to perform machine learning using Azure Machine Learning Studio or in a notebook application Jupyter Notebooks, etc.

There are three general paths to access your data:

* The Azure TSI (preview) Explorer.
* The Azure TSI (preview) APIs.
* Directly from an Azure Storage account.

### From the Time Series Insights (preview) Explorer

You can export data as a CSV file from the TSI (preview) Explorer. Read more about the [the TSI (preview) Explorer](./time-series-insights-update-explorer.md).

### From the Time Series Insights (preview) APIs

The API endpoint can be reached at `/getRecorded`. To learn more about this API, read about [Time Series Query](./time-series-insights-update-tsq.md).

### From an Azure Storage account

1. You need to have read access granted to whatever account you will be using to access your TSI data. To learn more about granting read access to Azure Blob Storage, read [Managing access to Storage resources](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources).

1. For more information on direct ways to read data from Azure Blob Storage, read [Moving data to and from Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

1. Exporting data from an Azure Storage account:

    * First make sure your account has the necessary requirements met to export data. Read [Storage import and export requirements](https://docs.microsoft.com/azure/storage/common/storage-import-export-requirements) for more information.
    * Learn about other ways to export data from your Azure Storage account by visiting [Storage import and export data from blobs](https://docs.microsoft.com/azure/storage/common/storage-import-export-data-from-blobs)

### Blob storage considerations

* Azure storage does have read and write limits based on how heavy your TSI (preview) usage is.  
* The Azure TSI Private Preview does not yet provide any kind of Parquet blob meta-store to support external data processing systems. However, we are investigating this and may add support in the future.  
* Customers will need to read the Azure blobs partitioned by time to be able to process the data.
* The Azure TSI Private Preview performs dynamic repartitioning of blob data for better performance. This is accomplished by dropping and recreating the blobs. Most services will be best served by using the original files.  
* Your TSI (preview) data may be duplicated across blobs.

### Data deletion

The Azure TSI Private Preview does not currently support data deletion but will in the future. We expect to support it by GA, but potentially sooner. We will notify users when we support data deletion.

Do not delete blobs since Time Series Insights (preview) maintains metadata about the blobs inside of TSI update.

## Ingress

### Azure Time Series Insights ingress policies

The Azure TSI Private Preview supports the same event sources and file types that it does today.

Supported event sources include:

* Azure IoT Hub
* Azure Event Hubs
  * Note: Azure Event Hub instances support Kafka.

Supported file types include:

* JSON
  * For more on the supported JSON shapes we can handle, see the [Time Series Query](./time-series-insights-update-tsq.md) documentation.

### Data availability

The Azure TSI Private Preview indexes data using a blob-size optimization strategy. This means that data will be available to query once it’s indexed (which is based on how much data is coming in and at what velocity). As we approach Public Preview, logic will be added to look for new events every few seconds (which will make data available for queries faster and more reliable).

> [!IMPORTANT]
> * Public Preview TSI will make data available within 60 seconds of it hitting an event source.  
> * During the Private Preview we expect to see a longer period before the data is made available.  
>   * If you experience any significant latency, please contact us.

### Scale

We expect the Azure TSI Private Preview to support up to 6Mbps per environment. Enhanced scaling support is planned for future releases.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## Next steps

Read the [Azure TSI (preview) Storage and Ingress](./time-series-insights-update-storage-ingress.md).

Read about the new [Time Series Model](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-storage-ingress/storage-architecture.png
[2]: media/v2-update-storage-ingress/parquet-files.png
[3]: media/v2-update-storage-ingress/blob-storage.png