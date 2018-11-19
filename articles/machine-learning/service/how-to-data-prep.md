---
title: Prepare data with the Machine Learning Data Prep SDK for Python - Azure
description: Learn how to use the Azure Machine Learning Data Prep SDK for Python to load data of various formats, transform it to be more usable, and write that data to a location for your models to access.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/20/2018
---

# Prepare data for modeling with Azure Machine Learning
 
In this article, you learn about the use cases and unique features of the Azure Machine Learning Data Prep SDK. Data preparation is the most important part of a machine learning workflow. Real-world data is often broken, inconsistent, or unable to be used as training data without significant cleansing and transformation. Correcting errors and anomalies in raw data, and building new features that are relevant to the problem you're trying to solve, will increase model accuracy.

You can prepare your data in Python using the [Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py).

## Azure Machine Learning Data Prep SDK

The [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) is a Python library that includes:
+ Many common data preprocessing tools
+ Automated feature engineering and transformations derived from examples

The SDK is similar in core-functionality to popular libraries such as **Pandas** and **PySpark**, yet offers more flexibility. Pandas is typically most useful on smaller data sets (< 2-5 GB) before memory capacity-constraints affect performance. In contrast, PySpark is generally for big-data applications but carries an overhead that makes working with small data sets much slower.

The Azure Machine Learning Data Prep SDK offers:
- Practicality and convenience when working with small data sets

- Scalability for modern big-data applications

- The ability to use and scale the same code for both use-cases

### Install the SDK

Install the SDK in your Python environment using the following command.

```shell
pip install azureml-dataprep
```

Use the following code to import the package.

```python
import azureml.dataprep as dprep
```

### Examples and reference

To learn about the modules and functions of this SDK, see the [Data Prep SDK reference docs](https://aka.ms/data-prep-sdk).

The following examples highlight some of the unique functionality of the SDK, including:
+ Automatic file type detection
+ Automated feature engineering
+ Summary statistics

#### Automatic file type detection

Use the `smart_read_file()` function to load your data without having to specify the file type. This function automatically recognizes and parses the file type.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### Automated feature engineering

Use the SDK to split and derive columns by both example and inference to automate feature engineering. Assume you have a field in your dataflow object called `datetime` with a value of `2018-09-15 14:30:00`.

To automatically split the `datetime` field, call the following function.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

By not defining the example parameter, the function will automatically split the `datetime` field into two new fields `datetime_1` and `datetime_2`. The resulting values are `2018-09-15` and `14:30:00`, respectively. It's also possible to provide an example pattern, and the SDK will predict and execute your intended transformation. Using the same `datetime` object, the following code will create a new column `datetime_weekday` for the weekday based on the provided example.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### Summary statistics

You can generate quick summary statistics for a dataflow with one line of code. This method offers a convenient way to understand your data and how it's distributed.

```python
dataflow.get_profile()
```

Calling this function on a dataflow object will result in output like the following table.

![Summary Statistics Output](./media/concept-data-preparation/output-example.png)

## Multiple environment compatibilities

The SDK also allows for dataflow objects to be serialized and opened in *any* Python environment. The environment where it's opened can be different than the environment where it's saved. This functionality allows for easy transfer between Python environments and quick integration with Azure Machine Learning models.

Use the following code to save your dataflow objects.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

Use the following code to reopen your package in any environment and retrieve a list of dataflow objects.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## Data preparation pipeline

To see detailed examples and code for each preparation step, use the following how-to guides:

1. [Load data](how-to-load-data.md), which can be in various formats
2. [Transform](how-to-transform-data.md) it into a more usable structure
3. [Write](how-to-write-data.md)  that data to a location accessible to your models

![Data preparation process](./media/concept-data-preparation/data-prep-process.png)

## Next steps
Review an [example notebook](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) of data preparation using the Azure Machine Learning Data Prep SDK.

Azure Machine Learning Data Prep SDK [reference documentation](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py).
