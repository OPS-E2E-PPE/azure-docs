---
title: Create Data Assets
titleSuffix: Azure Machine Learning
description: Learn how to create Azure Machine Learning data assets
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.custom: data4ml, ignite-2022
ms.author: xunwan
author: xunwan
ms.reviewer: franksoloman
ms.date: 09/22/2022
---

# Create data assets
[!INCLUDE [dev v2](../../includes/machine-learning-dev-v2.md)]

> [!div class="op_single_selector" title1="Select the version of Azure Machine Learning SDK you are using:"]
> * [v1](./v1/how-to-create-register-datasets.md)
> * [v2 (current version)](how-to-create-data-assets.md)

In this article, you learn how to create a data asset in Azure Machine Learning. By creating a data asset, you create a *reference* to the data source location, along with a copy of its metadata. Because the data remains in its existing location, you incur no extra storage cost, and don't risk the integrity of your data sources. You can create Data from Azure ML datastores, Azure Storage, public URLs, and local files.

> [!IMPORTANT]
> If you just want to access your data in an interactive session (for example, a Notebook) or a job, you are **not** required to create a data asset first. Creating a data asset would be an unnecessary step for you. 
>
> For more information about accessing your data in a notebook, please see [Access data from Azure cloud storage for interactive development](how-to-access-data-interactive.md).
>
> For more information about accessing your data - both local and cloud storage - in a job, please see [Access data in a job](how-to-read-write-data-v2.md).
>
> Creating data assets are useful when you want to:
> - **Share and reuse** data with other members of your team so that they don't need to remember file locations in cloud storage.
> - **Version** the metadata such as location, description and tags.
 

## Prerequisites

To create and work with data assets, you need:

* An Azure subscription. If you don't have one, create a free account before you begin. Try the [free or paid version of Azure Machine Learning](https://azure.microsoft.com/free/).

* An Azure Machine Learning workspace. [Create workspace resources](quickstart-create-resources.md).

* The [Azure Machine Learning CLI/SDK installed](how-to-configure-cli.md) and MLTable package installed (`pip install mltable`).

## Supported paths

When you create a data asset in Azure Machine Learning, you'll need to specify a `path` parameter that points to its location. Below is a table that shows the different data locations supported in Azure Machine Learning and examples for the `path` parameter:


|Location  | Examples  | Notes
|---------|---------|---------|
|A path on your local computer     | `./home/username/data/my_data`         ||
|A path on a datastore   |   `azureml://datastores/<data_store_name>/paths/<path>`      | |
|A path on a public http(s) server    |  `https://raw.githubusercontent.com/pandas-dev/pandas/main/doc/data/titanic.csv`    | https path pointing to a folder is not supported since https is not a filesystem. Please use other formats(wasbs/abfss/adl) instead for folder type of data.|
|A path on Azure Storage     |`wasbs://<containername>@<accountname>.blob.core.windows.net/<path_to_data>/` <br>  `abfss://<file_system>@<account_name>.dfs.core.windows.net/<path>` <br>  `adl://<accountname>.azuredatalakestore.net/<path_to_data>/`  ||


> [!NOTE]
> When you create a data asset from a local path, it will be automatically uploaded to the default Azure Machine Learning datastore in the cloud.

> [!IMPORTANT]
> The studio only supports browsing of credential-less ADLS Gen 2 datastores.

> [!IMPORTANT]
> Authentication to access data will use user's identity or compute MSI by default.


## Data asset types
 - [**URIs**](#Create a `uri_folder` data asset) - A **U**niform **R**esource **I**dentifier that is a reference to a storage location on your local computer or in the cloud that makes it easy to access data in your jobs. Azure Machine Learning distinguishes two types of URIs:`uri_file` and `uri_folder`.

 - [**MLTable**](#Create a `mltable` data asset) - `MLTable` helps you to abstract the schema definition for tabular data so it is more suitable for complex/changing schema or to be used in AutoML. If you just want to create a data asset for a job or you want to write your own parsing logic in Python you could use `uri_file`, `uri_folder`.

 The ideal scenarios to use `mltable` are:
 - The schema of your data is complex and/or changes frequently.
 - You only need a subset of data (for example: a sample of rows or files, specific columns, etc.)
 - AutoML jobs requiring tabular data.
 
If your scenario does not fit the above then it is likely that URIs are a more suitable type.

## Create a `uri_folder` data asset

Below shows you how to create a *folder* as an asset:

# [Azure CLI](#tab/cli)

Create a `YAML` file (`<file-name>.yml`):

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/data.schema.json

# Supported paths include:
# local: ./<path>
# blob:  wasbs://<containername>@<accountname>.blob.core.windows.net/<path>/
# ADLS gen2: abfss://<file_system>@<account_name>.dfs.core.windows.net/<path>/
# Datastore: azureml://datastores/<data_store_name>/paths/<path>
type: uri_folder
name: <name_of_data>
description: <description goes here>
path: <path>
```

Next, create the data asset using the CLI:

```azurecli
az ml data create -f <file-name>.yml
```

# [Python SDK](#tab/Python-SDK)

You can create a data asset in Azure Machine Learning using the following Python Code:

```python
from azure.ai.ml.entities import Data
from azure.ai.ml.constants import AssetTypes

# Supported paths include:
# local: './<path>'
# blob:  'wasbs://<containername>@<accountname>.blob.core.windows.net/<path>/'
# ADLS gen2: 'abfss://<file_system>@<account_name>.dfs.core.windows.net/<path>/'
# Datastore: 'azureml://datastores/<data_store_name>/paths/<path>'

my_path = '<path>'

my_data = Data(
    path=my_path,
    type=AssetTypes.URI_FOLDER,
    description="<description>",
    name="<name>",
    version='<version>'
)

ml_client.data.create_or_update(my_data)
```

# [Studio](#tab/Studio)

To create a Folder data asset in the Azure Machine Learning studio, use the following steps:

1. Navigate to [Azure Machine Learning studio](https://ml.azure.com)

1. Under **Assets** in the left navigation, select **Data**. On the Data assets tab, select Create
:::image type="content" source="./media/how-to-create-data-assets/data-assets-create.png" alt-text="Screenshot highlights Create in the Data assets tab.":::

1. Give your data asset a name and optional description. Then, select the "Folder (uri_folder)" option under Type, if it is not already selected.
:::image type="content" source="./media/how-to-create-data-assets/create-data-asset-folder-type.png" alt-text="In this screenshot, choose Folder (uri folder) in the Type dropdown.":::

1. You have a few options for your data source. If you already have the path to the folder you want to upload, choose "From a URI". If your folder is already stored in Azure, choose "From Azure storage". If you want to upload your folder from your local drive, choose "From local files".
:::image type="content" source="./media/how-to-create-data-assets/create-data-asset.png" alt-text="This screenshot shows data asset source choices.":::

1. Follow the steps, once you reach the Review step, click Create on the last page
---

## Create a `uri_file` data asset

Below shows you how to create a *specific file* as a data asset:

# [Azure CLI](#tab/cli)

Sample `YAML` file `<file-name>.yml` for data in local path is as below:

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/data.schema.json

# Supported paths include:
# local: ./<path>/<file>
# blob:  wasbs://<containername>@<accountname>.blob.core.windows.net/<path>/<file>
# ADLS gen2: abfss://<file_system>@<account_name>.dfs.core.windows.net/<path>/<file>
# Datastore: azureml://datastores/<data_store_name>/paths/<path>/<file>

type: uri_file
name: <name>
description: <description>
path: <uri>
```

```cli
> az ml data create -f <file-name>.yml
```

# [Python SDK](#tab/Python-SDK)
```python
from azure.ai.ml.entities import Data
from azure.ai.ml.constants import AssetTypes

# Supported paths include:
# local: './<path>/<file>'
# blob:  'wasbs://<containername>@<accountname>.blob.core.windows.net/<path>/<file>'
# ADLS gen2: 'abfss://<file_system>@<account_name>.dfs.core.windows.net/<path>/<file>'
# Datastore: 'azureml://datastores/<data_store_name>/paths/<path>/<file>'
my_path = '<path>'

my_data = Data(
    path=my_path,
    type=AssetTypes.URI_FILE,
    description="<description>",
    name="<name>",
    version="<version>"
)

ml_client.data.create_or_update(my_data)
```
# [Studio](#tab/Studio)

To create a File data asset in the Azure Machine Learning studio, use the following steps:

1. Navigate to [Azure Machine Learning studio](https://ml.azure.com)

1. Under **Assets** in the left navigation, select **Data**. On the Data assets tab, select Create
:::image type="content" source="./media/how-to-create-data-assets/data-assets-create.png" alt-text="Screenshot highlights Create in the Data assets tab.":::

1. Give your data asset a name and optional description. Then, select the "File (uri_file)" option under Type.
:::image type="content" source="./media/how-to-create-data-assets/create-data-asset-file-type.png" alt-text="In this screenshot, choose File (uri folder) in the Type dropdown.":::

1. You have a few options for your data source. If you already have the path to the file you want to upload, choose "From a URI". If your file is already stored in Azure, choose "From Azure storage". If you want to upload your file from your local drive, choose "From local files".
:::image type="content" source="./media/how-to-create-data-assets/create-data-asset.png" alt-text="This screenshot shows data asset source choices.":::

1. Follow the steps, once you reach the Review step, click Create on the last page
---
   
## Create a `mltable` data asset

`mltable` is a way to abstract the schema definition for tabular data to make it easier to share data assets (an overview can be found in [MLTable](concept-data.md#mltable)). 
`mltable` supports tabular data coming from following sources:
- Delimited files (CSV, TSV, TXT)
- Parquet files
- JSON Lines
- Delta Lake 

Find more details about what are the abilities we provide via `mltable` in [reference-yaml-mltable](reference-yaml-mltable.md).

In this section, we show you how to create a data asset when the type is a `mltable`.

### The MLTable file

The MLTable file is a file that provides the specification of the data's schema so that the `mltable` *engine* can materialize the data into an in-memory object (Pandas/Dask/Spark).

> [!NOTE]
> This file needs to be named exactly as `MLTable`.

An *example* MLTable file for delimited files is provided below:

```yml
type: mltable

paths:
  - pattern: ./*.txt
transformations:
  - read_delimited:
      delimiter: ,
      encoding: ascii
      header: all_files_same_headers
```

An *example* MLTable file for Delta Lake is provided below:
```yml
type: mltable

paths:
  - folder: ./

transformations:
  - read_delta_lake:
      timestamp_as_of: '2022-08-26T00:00:00Z'
#timestamp_as_of: Timestamp to be specified for time-travel on the specific Delta Lake data.
#version_as_of: Version to be specified for time-travel on the specific Delta Lake data.
```

For more transformations available in `mltable`, please look into [reference-yaml-mltable](reference-yaml-mltable.md). 


> [!IMPORTANT]
> We recommend co-locating the MLTable file with the underlying data in storage. For example:
> 
> ```Text
> ├── my_data
> │   ├── MLTable
> │   ├── file_1.txt
> .
> .
> .
> │   ├── file_n.txt
> ```
> Co-locating the MLTable with the data ensures a **self-contained *artifact*** where all that is needed is stored in that one folder (`my_data`); regardless of whether that folder is stored on your local drive or in your cloud store or on a public http server. You should **not** specify *absolute paths* in the MLTable file.

### Creating the data asset

Below shows you how to create a `mltable` data asset. The `path` can be any of the supported path formats outlined above.


# [Azure CLI](#tab/cli)

Create a `YAML` file (`<file-name>.yml`):

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/data.schema.json

# path must point to **folder** containing MLTable artifact (MLTable file + data
# Supported paths include:
# local: ./<path>
# blob:  wasbs://<containername>@<accountname>.blob.core.windows.net/<path>/
# ADLS gen2: abfss://<file_system>@<account_name>.dfs.core.windows.net/<path>/
# Datastore: azureml://datastores/<data_store_name>/paths/<path>

type: mltable
name: <name_of_data>
description: <description goes here>
path: <path>
```

> [!NOTE]
> The path points to the **folder** containing the MLTable artifact.

Next, create the data asset using the CLI:

```azurecli
az ml data create -f <file-name>.yml
```

# [Python SDK](#tab/Python-SDK)

You can create a data asset in Azure Machine Learning using the following Python Code:

```python
from azure.ai.ml.entities import Data
from azure.ai.ml.constants import AssetTypes

# my_path must point to folder containing MLTable artifact (MLTable file + data
# Supported paths include:
# local: './<path>'
# blob:  'wasbs://<containername>@<accountname>.blob.core.windows.net/<path>/'
# ADLS gen2: 'abfss://<file_system>@<account_name>.dfs.core.windows.net/<path>/'
# Datastore: 'azureml://datastores/<data_store_name>/paths/<path>'

my_path = '<path>'

my_data = Data(
    path=my_path,
    type=AssetTypes.MLTABLE,
    description="<description>",
    name="<name>",
    version='<version>'
)

ml_client.data.create_or_update(my_data)
```
> [!NOTE]
> The path points to the **folder** containing the MLTable artifact. 

# [Studio](#tab/Studio)

To create a Table data asset in the Azure Machine Learning studio, use the following steps. 
> [!NOTE]
> You must have a **folder** prepared containing the MLTable artifact.

1. Navigate to [Azure Machine Learning studio](https://ml.azure.com)

1. Under **Assets** in the left navigation, select **Data**. On the Data assets tab, select Create
:::image type="content" source="./media/how-to-create-data-assets/data-assets-create.png" alt-text="Screenshot highlights Create in the Data assets tab.":::

1. Give your data asset a name and optional description. Then, select the "Table (mltable)" option under Type.
:::image type="content" source="./media/how-to-create-data-assets/create-data-asset-table-type.png" alt-text="In this screenshot, choose Table (mltable) in the Type dropdown.":::

1. You have a few options for your data source. If you already have the path to the folder you want to upload, choose "From a URI". If your folder is already stored in Azure, choose "From Azure storage". If you want to upload your folder from your local drive, choose "From local files".
:::image type="content" source="./media/how-to-create-data-assets/create-data-asset.png" alt-text="This screenshot shows data asset source choices.":::

1. Follow the steps, once you reach the Review step, click Create on the last page
---



## Next steps

- [Read data in a job](how-to-read-write-data-v2.md#read-data-in-a-job)
