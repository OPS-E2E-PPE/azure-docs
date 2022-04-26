---
title: Azure SQL output binding for Functions
description: Learn to use the Azure SQL output binding in Azure Functions.
author: dzsquared
ms.topic: reference
ms.date: 4/1/2022
ms.author: drskwier
ms.reviewer: cachai
ms.devlang: csharp
---

# Azure SQL output binding for Azure Functions (preview)

The Azure SQL output binding lets you write to a database.

For information on setup and configuration details, see the [overview](./functions-bindings-azure-sql.md).


<a id="example" name="example"></a>

# [C#](#tab/csharp)

This section contains the following examples:

* [Http trigger, write one record](#http-trigger-write-one-record-c)
* [Http trigger, write to two tables](#http-trigger-write-to-two-tables-c)
* [Http trigger, write records using IAsyncCollector](#http-trigger-write-records-using-iasynccollector-c)

The examples refer to a `ToDoItem` class and a corresponding database table:

:::code language="csharp" source="~/functions-sql-todo-sample/ToDoModel.cs" range="6-14":::

:::code language="sql" source="~/functions-sql-todo-sample/sql/create.sql" range="1-7":::


<a id="http-trigger-write-one-record-c"></a>

### Http trigger, write one record

The following example shows a [C# function](functions-dotnet-class-library.md) that adds a record to a database, using data provided in an HTTP POST request as a JSON body.

:::code language="csharp" source="~/functions-sql-todo-sample/PostToDo.cs" range="4-49":::

<a id="http-trigger-write-to-two-tables-c"></a>

### Http trigger, write to two tables

The following example shows a [C# function](functions-dotnet-class-library.md) that adds records to a database in two different tables (`dbo.ToDo` and `dbo.RequestLog`), using data provided in an  HTTP POST request as a JSON body and multiple output bindings.


```cs
namespace AzureSQL.ToDo
{
    public static class PostToDo
    {
        // create a new ToDoItem from body object
        // uses output binding to insert new item into ToDo table
        [FunctionName("PostToDo")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = "PostFunction")] HttpRequest req,
            ILogger log,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<ToDoItem> toDoItems,
            [Sql("dbo.RequestLog", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<RequestLog> requestLogs)
        {
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            ToDoItem toDoItem = JsonConvert.DeserializeObject<ToDoItem>(requestBody);

            // generate a new id for the todo item
            toDoItem.Id = Guid.NewGuid();

            // set Url from env variable ToDoUri
            toDoItem.url = Environment.GetEnvironmentVariable("ToDoUri")+"?id="+toDoItem.Id.ToString();

            // if completed is not provided, default to false
            if (toDoItem.completed == null)
            {
                toDoItem.completed = false;
            }

            await toDoItems.AddAsync(toDoItem);
            await toDoItems.FlushAsync();
            List<ToDoItem> toDoItemList = new List<ToDoItem> { toDoItem };

            requestLog = new RequestLog();
            requestLog.RequestTimeStamp = DateTime.Now;
            requestLog.ItemCount = 1;
            await requestLogs.AddAsync(requestLog);
            await requestLogs.FlushAsync();

            return new OkObjectResult(toDoItemList);
        }
    }

    public class RequestLog {
        public DateTime RequestTimeStamp { get; set; }
        public int ItemCount { get; set; }
    }
}
```

<a id="http-trigger-write-records-using-iasynccollector-c"></a>

### HTTP trigger, write records using IAsyncCollector

The following example shows a [C# function](functions-dotnet-class-library.md) that adds a collection of records to a database, using data provided in an HTTP POST body JSON array.

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Newtonsoft.Json;
using System.IO;
using System.Threading.Tasks;

namespace AzureSQLSamples
{
    public static class WriteRecordsAsync
    {
        [FunctionName("WriteRecordsAsync")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "post", Route = "addtodo-asynccollector")]
            HttpRequest req,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] IAsyncCollector<ToDoItem> newItems)
        {
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            var incomingItems = JsonConvert.DeserializeObject<ToDoItem[]>(requestBody);
            foreach (ToDoItem newItem in incomingItems)
            {
                await newItems.AddAsync(newItem);
            }
            // Rows are upserted here
            await newItems.FlushAsync();

            return new CreatedResult($"/api/addtodo-asynccollector", "done");
        }
    }
}
```

# [JavaScript](#tab/javascript)

The Azure SQL binding for Azure Functions does not currently support JavaScript.

# [Python](#tab/python)

The Azure SQL binding for Azure Functions does not currently support Python.

---

## Attributes and annotations

# [C#](#tab/csharp)

In [C# class libraries](functions-dotnet-class-library.md), use the [Sql](https://github.com/Azure/azure-functions-sql-extension/blob/main/src/SqlAttribute.cs) attribute.

The attribute's constructor takes the SQL command text and the connection string setting name. For an output binding, the SQL command string is a table name where the data is to be stored. The connection string setting name corresponds to the application setting (in `local.settings.json` for local development) that contains the [connection string](/dotnet/api/microsoft.data.sqlclient.sqlconnection.connectionstring?view=sqlclient-dotnet-core-3.0&preserve-view=true#Microsoft_Data_SqlClient_SqlConnection_ConnectionString) to the Azure SQL or SQL Server instance.

Here's a `Sql` attribute example in a method signature:

```csharp
    [FunctionName("HTTPtoSQL")]
    public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "addtodo")] HttpRequest req,
            [Sql("dbo.ToDo", ConnectionStringSetting = "SqlConnectionString")] out ToDoItem newItem)
    {
        ...
    }
```

# [JavaScript](#tab/javascript)

The Azure SQL binding for Azure Functions does not currently support JavaScript.

# [Python](#tab/python)

The Azure SQL binding for Azure Functions does not currently support Python.

---


[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]



## Next steps

- [Read data from a database (Input binding)](./functions-bindings-azure-sql-input.md)
- [Review ToDo API sample with Azure SQL bindings](/samples/azure-samples/azure-sql-binding-func-dotnet-todo/todo-backend-dotnet-azure-sql-bindings-azure-functions/)
