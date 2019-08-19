---
title: Create datasets to access data with azureml-datasets
titleSuffix: Azure Machine Learning service
description: Learn how to create Datasets from various sources and register Datasets with your workspace
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual	
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019

---

# Create and access datasets (Preview) in Azure Machine Learning

In this article, you'll learn how to create Azure Machine Learning datasets (preview), and how to access data from local or remote experiments.

With Azure Machine Learning datasets, you can: 

* **Keep a single copy of data in your storage** referenced by datasets. 

* **Easily access data during model training** without worrying about connection string or data path.

* **Share data & collaborate** with other users.

## Prerequisites

To create and work with datasets, you need:

* An Azure subscription. If you don’t have an Azure subscription, create a free account before you begin. Try the [free or paid version of Azure Machine Learning service](https://aka.ms/AMLFree) today.

* An [Azure Machine Learning service workspace](how-to-manage-workspace.md)

* The [Azure Machine Learning SDK for Python installed](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), which includes the azureml-datasets package.

> [!Note]
> Some Dataset classes (preview) have dependencies on the [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) package. For Linux users, these classes are supported only on the following distributions:  Red Hat Enterprise Linux, Ubuntu, Fedora, and CentOS.

## Dataset Types
Datasets are categorized into various types based on how users consume them in training. Currently we support TabularDatasets which represent data in a tabular format by parsing the provided file or list of files. This provides you with the ability to materialize the data into a pandas DataFrame. TabularDataset can be created from csv, tsv, parquet files, SQL query results etc. For a complete list, please visit our documentation.

To find out more about upcoming API changes, see [What is Azure Machine Learning service?](https://aka.ms/tabular-dataset) 

## Create datasets 

By creating a dataset, you create a reference to the data source location, along with a copy of its metadata. The data remains in  its existing location, so no extra storage cost is incurred.

For the data to be accessible by Azure Machine Learning service, datasets must be created from paths in [Azure datastores](how-to-access-data.md) or public web urls.

To create Datasets from an [Azure datastore](how-to-access-data.md):

* Verify you have `contributor` or `owner` access to the registered Azure datastore.

* Create the dataset by referencing to a path in the datastore.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```
### Create TabularDatasets

Use the `from_delimited_files()` method on `TabularDatasetFactory` class to read files in csv or tsv format, and create an unregistered TabularDataset. If you are reading from multiple files, results will be aggregated into one tabular representation.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|Name|Sex|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Braund, Mr. Owen Harris|male|22.0|1|0|A/5 21171|7.2500||S
1|2|1|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38.0|1|0|PC 17599|71.2833|C85|C
2|3|1|3|Heikkinen, Miss. Laina|female|26.0|0|0|STON/O2. 3101282|7.9250||S

## Register datasets

To complete the creation process, register your datasets with workspace:

Use the `register()` method to register datasets to your workspace so they can be shared with others and reused across various experiments.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

## Version datasets

You can register a new dataset under the same name by creating a new version. Dataset version is a way to bookmark the state of your data, so you can apply a specific version of the dataset for experimentation or future reproduction. Typical scenarios to consider versioning: 
* When new data is available for retraining.
* When you are applying different data preparation or feature engineering approaches.

```Python
# create a TabularDataset from new Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## Access your data during training

Registered datasets are accessible locally and remotely on compute clusters like the Azure Machine Learning compute. To access your registered Dataset across experiments, use the following code to get your workspace and registered dataset by name. The `get_by_name` method on the `Dataset` class by default returns the latest version of the dataset registered with the workspace.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## Next steps

* Use automated machine learning to [train with TabularDatasets](https://aka.ms/automl-dataset).
* For more examples of training with datasets, see the [sample notebooks](https://aka.ms/dataset-tutorial).
