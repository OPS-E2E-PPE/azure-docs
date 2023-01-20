---
title: Quickstart - Azure Cosmos DB for NoSQL client library for Python
description: Learn how to build a .NET app to manage Azure Cosmos DB for NoSQL account resources and data in this quickstart.
author: seesharprun
ms.author: sidandrews
ms.service: cosmos-db
ms.subservice: nosql
ms.devlang: python
ms.topic: quickstart
ms.date: 1/17/2023
ms.custom: seodec18, seo-javascript-september2019, seo-python-october2019, devx-track-python, mode-api, ignite-2022, devguide-python, cosmos-db-dev-journey
---

# Quickstart: Azure Cosmos DB for NoSQL client library for Python

[!INCLUDE[NoSQL](../includes/appliesto-nosql.md)]

[!INCLUDE[Quickstart selector](includes/quickstart-selector.md)]

Get started with the Azure Cosmos DB client library for Python to create databases, containers, and items within your account. Follow these steps to install the package and try out example code for basic tasks.

> [!NOTE]
> The [example code snippets](https://github.com/azure-samples/cosmos-db-nosql-python-samples) are available on GitHub as a .NET project.

[API reference documentation](/python/api/azure-cosmos/azure.cosmos) | [Library source code](https://github.com/azure/azure-sdk-for-python/tree/main/sdk/cosmos/azure-cosmos) | [Package (PyPI)](https://pypi.org/project/azure-cosmos) | [Samples](samples-python.md)

## Prerequisites

- An Azure account with an active subscription.
  - No Azure subscription? You can [try Azure Cosmos DB free](../try-free.md) with no credit card required.
- [Python 3.7 or later](https://www.python.org/downloads/)
  - Ensure the `python` executable is in your `PATH`.
- [Azure Command-Line Interface (CLI)](/cli/azure/) or [Azure PowerShell](/powershell/azure/)

### Prerequisite check

- In a terminal or command window, run ``python --version`` to check that the .NET SDK is version 3.7 or later.
- Run ``az --version`` (Azure CLI) or ``Get-Module -ListAvailable AzureRM`` (Azure PowerShell) to check that you have the appropriate Azure command-line tools installed.

## Setting up

This section walks you through creating an Azure Cosmos DB account and setting up a project that uses Azure Cosmos DB for NoSQL client library for .NET to manage resources.

### Create an Azure Cosmos DB account

> [!TIP]
> No Azure subscription? You can [try Azure Cosmos DB free](../try-free.md) with no credit card required. If you create an account using the free trial, you can safely skip ahead to the [Create a new Python app](#create-a-new-python-app) section.

[!INCLUDE [Create resource tabbed conceptual - ARM, Azure CLI, PowerShell, Portal](./includes/create-resources.md)]

### Create a new Python app

Create a new Python code file (*app.py*) in an empty folder using your preferred integrated development environment (IDE).

### Install the package

Add the [`azure-cosmos`](https://pypi.org/project/azure-cosmos) PyPI package to the Python app. Use the `pip install` command to install the package.

```bash
pip install azure-cosmos
```

### Configure environment variables

[!INCLUDE [Create environment variables for key and endpoint](./includes/environment-variables.md)]

## Object model

[!INCLUDE [Explain DOCUMENT DB object model](./includes/object-model.md)]

You'll use the following Python classes to interact with these resources:

- [``CosmosClient``](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) - This class provides a client-side logical representation for the Azure Cosmos DB service. The client object is used to configure and execute requests against the service.
- [``DatabaseProxy``](/python/api/azure-cosmos/azure.cosmos.database.databaseproxy) - This class is a reference to a database that may, or may not, exist in the service yet. The database is validated server-side when you attempt to access it or perform an operation against it.
- [``ContainerProxy``](/python/api/azure-cosmos/azure.cosmos.container.containerproxy) - This class is a reference to a container that also may not exist in the service yet. The container is validated server-side when you attempt to work with it.

## Code examples

- [Authenticate the client](#authenticate-the-client)
- [Create a database](#create-a-database)
- [Create a container](#create-a-container)
- [Create an item](#create-an-item)
- [Get an item](#get-an-item)
- [Query items](#query-items)

The sample code described in this article creates a database named ``adventureworks`` with a container named ``products``. The ``products`` table is designed to contain product details such as name, category, quantity, and a sale indicator. Each product also contains a unique identifier.

For this sample code, the container will use the category as a logical partition key.

### Authenticate the client

From the project directory, open the *app.py* file. In your editor, import the `os` and `json` modules. Then, import the `CosmosClient` and `PartitionKey` classes from the `azure.cosmos` module.

#### [Sync](#tab/sync)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="imports":::

#### [Async](#tab/async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" id="imports":::

---

Create constants for the `COSMOS_ENDPOINT` and `COSMOS_KEY` environment variables using `os.environ`.

#### [Sync / Async](#tab/sync+async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="environment_variables":::

---

Create constants for the database and container names.

#### [Sync / Async](#tab/sync+async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="constants":::

---

Create a new client instance using the [`CosmosClient`](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) class constructor and the two variables you created as parameters.

#### [Sync](#tab/sync)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="create_client":::

#### [Async](#tab/async)

> [!IMPORTANT]
> Please the client instance in a coroutine function named `manage_cosmos`. Within the coroutine function, define the new client with the `async with` keywords. Outside of the coroutine function, use the `asyncio.run` function to execute the coroutine asynchronously.

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="24-26":::

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="70":::

---

### Create a database

Use the [`CosmosClient.create_database_if_not_exists`](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#azure-cosmos-cosmos-client-cosmosclient-create-database-if-not-exists) method to create a new database if it doesn't already exist. This method will return a [`DatabaseProxy`](/python/api/azure-cosmos/azure.cosmos.databaseproxy) reference to the existing or newly created database.

#### [Sync](#tab/sync)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="create_database":::

#### [Async](#tab/async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="28-29":::

---

### Create a container

The [`PartitionKey`](/python/api/azure-cosmos/azure.cosmos.partitionkey) class defines a partition key path that you can use when creating a container.

#### [Sync](#tab/sync)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="create_partition_key":::

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="create_container":::

#### [Async](#tab/async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="31":::

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="33-36":::

---

The [`Databaseproxy.create_container_if_not_exists`](/python/api/azure-cosmos/azure.cosmos.databaseproxy#azure-cosmos-databaseproxy-create-container-if-not-exists) method will create a new container if it doesn't already exist. This method will also return a [`ContainerProxy`](/python/api/azure-cosmos/azure.cosmos.containerproxy) reference to the container.

### Create an item

Create a new item in the container by first creating a new variable (`new_item`) with a sample item defined. In this example, the unique identifier of this item is `70b63682-b93a-4c77-aad2-65501347265f`. The partition key value is derived from the `/categoryId` path, so it would be `61dba35b-4f02-45c5-b648-c6badc0cbd79`.

#### [Sync / Async](#tab/sync+async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="new_item":::

---

> [!TIP]
> The remaining fields are flexible and you can define as many or as few as you want. You can even combine different item schemas in the same container.

Create an item in the container by using the [`ContainerProxy.create_item`](/python/api/azure-cosmos/azure.cosmos.containerproxy#azure-cosmos-containerproxy-create-item) method passing in the variable you already created.

#### [Sync](#tab/sync)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="create_item":::

#### [Async](#tab/async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="47":::

---

### Get an item

In Azure Cosmos DB, you can perform a point read operation by using both the unique identifier (``id``) and partition key fields. In the SDK, call [`ContainerProxy.read_item`](/python/api/azure-cosmos/azure.cosmos.containerproxy#azure-cosmos-containerproxy-read-item) passing in both values to return an item as a dictionary of strings and values (`dict[str, Any]`).

#### [Sync](#tab/sync)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="read_item":::

#### [Async](#tab/async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="49-53":::

---

In this example, the dictionary result is saved to a variable named `existing_item`.

### Query items

After you insert an item, you can run a query to get all items that match a specific filter. This example runs the SQL query: ``SELECT * FROM products p WHERE p.categoryId = "61dba35b-4f02-45c5-b648-c6badc0cbd79"``. This example uses query parameterization to construct the query. The query uses a string of the SQL query, and a dictionary of query parameters.

#### [Sync / Async](#tab/sync+async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="build_query":::

---

This example dictionary included the `@categoryId` query parameter and the corresponding value `61dba35b-4f02-45c5-b648-c6badc0cbd79`.

Once the query is defined, call [`ContainerProxy.query_items`](/python/api/azure-cosmos/azure.cosmos.containerproxy#azure-cosmos-containerproxy-query-items) to run the query and return the results as a paged set of items (`ItemPage[Dict[str, Any]]`).

#### [Sync / Async](#tab/sync+async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="query_items":::

---

Finally, use a for loop to iterate over the results in each page and perform various actions.

#### [Sync](#tab/sync)

:::code language="python" source="~/cosmos-db-nosql-python-samples/001-quickstart/app.py" id="iterate_query_results":::

#### [Async](#tab/async)

:::code language="python" source="~/cosmos-db-nosql-python-samples/002-quickstart-async/app.py" range="62-64":::

---

In this example, `json.dumps` is used to print the item to the console in a human-readable way.

## Run the code

This app creates an API for NoSQL database and container. The example then creates an item and then reads the exact same item back. Finally, the example issues a query that should only return that single item. At the final step, the example outputs the final item to the console.

Use a terminal to navigate to the application directory and run the application.

```bash
python app.py
```

The output of the app should be similar to this example:

```output
Database         cosmicworks
Container        products
Point read       Yamba Surfboard
Result list      [
 {
  "id": "70b63682-b93a-4c77-aad2-65501347265f",
  "categoryId": "61dba35b-4f02-45c5-b648-c6badc0cbd79",
  "categoryName": "gear-surf-surfboards",
  "name": "Yamba Surfboard",
  "quantity": 12,
  "sale": false,
  "_rid": "KSsMAPI2fH0BAAAAAAAAAA==",
  "_self": "dbs/KSsMAA==/colls/KSsMAPI2fH0=/docs/KSsMAPI2fH0BAAAAAAAAAA==/",
  "_etag": "\"48002b76-0000-0200-0000-63c85f9d0000\"",
  "_attachments": "attachments/",
  "_ts": 1674076061
 }
]
```

> [!NOTE]
> The fields assigned by Azure Cosmos DB will vary from this sample output.

## Clean up resources

[!INCLUDE [Clean up resources - Azure CLI, PowerShell, Portal](./includes/clean-up-resources.md)]

## Next steps

In this quickstart, you learned how to create an Azure Cosmos DB for NoSQL account, create a database, and create a container using the Python SDK. You can now dive deeper into guidance on how to import your data into the API for NoSQL.
