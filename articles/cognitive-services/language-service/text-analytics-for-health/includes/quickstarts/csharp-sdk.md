---
author: jboback
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 12/12/2022
ms.author: jboback
ms.custom: ignite-fall-2021
---

[Reference documentation](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [Additional samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.2.0) | [Library source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)

Use this quickstart to create a Text Analytics for health application with the client library for .NET. In the following example, you will create a C# application that can identify medical [entities](../../concepts/health-entity-categories.md), [relations](../../concepts/relation-extraction.md), and [assertions](../../concepts/assertion-detection.md) that appear in text.




## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services)
* The [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Once you have your Azure subscription, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Create a Language resource"  target="_blank">create a Language resource </a> in the Azure portal to get your key and endpoint.  After it deploys, click **Go to resource**.
    * You will need the key and endpoint from the resource you create to connect your application to the API. You'll paste your key and endpoint into the code below later in the quickstart.
    * You can use the free pricing tier (`Free F0`) to try the service (providing 5000 text records - 1000 characters each) and upgrade later to the `Standard S` pricing tier for production. You can also start with the `Standard S` pricing tier, receiving the same initial quota for free (5000 text records) before getting charged. For more information on pricing, visit [Language Service Pricing](https://azure.microsoft.com/pricing/details/cognitive-services/language-service/).

> [!div class="nextstepaction"]
> <a href="https://github.com/Azure/azure-sdk-for-net/issues/new?title=&body=%0A-%20**Package%20Name**:%20%0A-%20**Package%20Version**:%20%0A-%20**Operating%20System**:%20%0A-%20**.NET%20runtime%20version**:%20%0A%0A%5BEnter%20feedback%20here%5D%0A%0A%0A---%0A%23%23%23%23%20Document%20details%0A%0A⚠%20*Do%20not%20edit%20this%20section.%20It%20is%20required%20for%20learn.microsoft.com%20➟%20GitHub%20issue%20linking.%0A%0ALanguage%20Quickstart%20Feedback%0A*%20Content:%20%5BQuickstart:%20Using%20Text%20Analytics%20for%20health%20client%20library%20and%20REST%20API%20-%20Azure%20Cognitive%20Services%5D(https:%2F%2Flearn.microsoft.com%2Fazure%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart%3Fpivots%3Dprogramming-language-csharp)%0A*%20Content%20Source:%20%5Barticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md%5D(https:%2F%2Fgithub.com%2FMicrosoftDocs%2Fazure-docs%2Fblob%2Fmain%2Farticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md)%0A*%20Section:%20**Prerequisites**%0A*%20Service:%20**cognitive-services**%0A*%20Sub-service:%20**language-service**%0A&labels=Cognitive%20-%20Language%2CCognitive%20Language%20QS%20Feedback" target="_target">I ran into an issue</a>

## Setting up

### Create a new .NET Core application

Using the Visual Studio IDE, create a new .NET Core console app. This will create a "Hello World" project with a single C# source file: *program.cs*.

Install the client library by right-clicking on the solution in the **Solution Explorer** and selecting **Manage NuGet Packages**. In the package manager that opens select **Browse** and search for `Azure.AI.TextAnalytics`. Select version `5.2.0`, and then **Install**. You can also use the [Package Manager Console](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

> [!div class="nextstepaction"]
> <a href="https://github.com/Azure/azure-sdk-for-net/issues/new?title=&body=%0A-%20**Package%20Name**:%20%0A-%20**Package%20Version**:%20%0A-%20**Operating%20System**:%20%0A-%20**.NET%20runtime%20version**:%20%0A%0A%5BEnter%20feedback%20here%5D%0A%0A%0A---%0A%23%23%23%23%20Document%20details%0A%0A⚠%20*Do%20not%20edit%20this%20section.%20It%20is%20required%20for%20learn.microsoft.com%20➟%20GitHub%20issue%20linking.%0A%0ALanguage%20Quickstart%20Feedback%0A*%20Content:%20%5BQuickstart:%20Using%20Text%20Analytics%20for%20health%20client%20library%20and%20REST%20API%20-%20Azure%20Cognitive%20Services%5D(https:%2F%2Flearn.microsoft.com%2Fazure%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart%3Fpivots%3Dprogramming-language-csharp)%0A*%20Content%20Source:%20%5Barticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md%5D(https:%2F%2Fgithub.com%2FMicrosoftDocs%2Fazure-docs%2Fblob%2Fmain%2Farticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md)%0A*%20Section:%20**Set-up-the-environment**%0A*%20Service:%20**cognitive-services**%0A*%20Sub-service:%20**language-service**%0A&labels=Cognitive%20-%20Language%2CCognitive%20Language%20QS%20Feedback" target="_target">I ran into an issue</a>


## Code example

Copy the following code into your *program.cs* file. Remember to replace the `key` variable with the key for your resource, and replace the `endpoint` variable with the endpoint for your resource. Then run the code.  

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```csharp
using Azure;
using System;
using Azure.AI.TextAnalytics;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Example
{
    class Program
    {
        private static readonly AzureKeyCredential credentials = new AzureKeyCredential("replace-with-your-key-here");
        private static readonly Uri endpoint = new Uri("replace-with-your-endpoint-here");
        
        // Example method for extracting information from healthcare-related text 
        static async Task healthExample(TextAnalyticsClient client)
        {
            string document = "Prescribed 100mg ibuprofen, taken twice daily.";

            List<string> batchInput = new List<string>()
            {
                document
            };
            AnalyzeHealthcareEntitiesOperation healthOperation = await client.StartAnalyzeHealthcareEntitiesAsync(batchInput);
            await healthOperation.WaitForCompletionAsync();

            await foreach (AnalyzeHealthcareEntitiesResultCollection documentsInPage in healthOperation.Value)
            {
                Console.WriteLine($"Results of Azure Text Analytics for health async model, version: \"{documentsInPage.ModelVersion}\"");
                Console.WriteLine("");

                foreach (AnalyzeHealthcareEntitiesResult entitiesInDoc in documentsInPage)
                {
                    if (!entitiesInDoc.HasError)
                    {
                        foreach (var entity in entitiesInDoc.Entities)
                        {
                            // view recognized healthcare entities
                            Console.WriteLine($"  Entity: {entity.Text}");
                            Console.WriteLine($"  Category: {entity.Category}");
                            Console.WriteLine($"  Offset: {entity.Offset}");
                            Console.WriteLine($"  Length: {entity.Length}");
                            Console.WriteLine($"  NormalizedText: {entity.NormalizedText}");
                        }
                        Console.WriteLine($"  Found {entitiesInDoc.EntityRelations.Count} relations in the current document:");
                        Console.WriteLine("");

                        // view recognized healthcare relations
                        foreach (HealthcareEntityRelation relations in entitiesInDoc.EntityRelations)
                        {
                            Console.WriteLine($"    Relation: {relations.RelationType}");
                            Console.WriteLine($"    For this relation there are {relations.Roles.Count} roles");

                            // view relation roles
                            foreach (HealthcareEntityRelationRole role in relations.Roles)
                            {
                                Console.WriteLine($"      Role Name: {role.Name}");

                                Console.WriteLine($"      Associated Entity Text: {role.Entity.Text}");
                                Console.WriteLine($"      Associated Entity Category: {role.Entity.Category}");
                                Console.WriteLine("");
                            }
                            Console.WriteLine("");
                        }
                    }
                    else
                    {
                        Console.WriteLine("  Error!");
                        Console.WriteLine($"  Document error code: {entitiesInDoc.Error.ErrorCode}.");
                        Console.WriteLine($"  Message: {entitiesInDoc.Error.Message}");
                    }
                    Console.WriteLine("");
                }
            }
        }

        static async Task Main(string[] args)
        {
            var client = new TextAnalyticsClient(endpoint, credentials);
            await healthExample(client);
        }

    }
}

```

> [!div class="nextstepaction"]
> <a href="https://github.com/Azure/azure-sdk-for-net/issues/new?title=&body=%0A-%20**Package%20Name**:%20%0A-%20**Package%20Version**:%20%0A-%20**Operating%20System**:%20%0A-%20**.NET%20runtime%20version**:%20%0A%0A%5BEnter%20feedback%20here%5D%0A%0A%0A---%0A%23%23%23%23%20Document%20details%0A%0A⚠%20*Do%20not%20edit%20this%20section.%20It%20is%20required%20for%20learn.microsoft.com%20➟%20GitHub%20issue%20linking.%0A%0ALanguage%20Quickstart%20Feedback%0A*%20Content:%20%5BQuickstart:%20Using%20Text%20Analytics%20for%20health%20client%20library%20and%20REST%20API%20-%20Azure%20Cognitive%20Services%5D(https:%2F%2Flearn.microsoft.com%2Fazure%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart%3Fpivots%3Dprogramming-language-csharp)%0A*%20Content%20Source:%20%5Barticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md%5D(https:%2F%2Fgithub.com%2FMicrosoftDocs%2Fazure-docs%2Fblob%2Fmain%2Farticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md)%0A*%20Section:%20**Code-example**%0A*%20Service:%20**cognitive-services**%0A*%20Sub-service:%20**language-service**%0A&labels=Cognitive%20-%20Language%2CCognitive%20Language%20QS%20Feedback" target="_target">I ran into an issue</a>


### Output

```console
Results of Azure Text Analytics for health async model, version: "2022-03-01"

  Entity: 100mg
  Category: Dosage
  Offset: 11
  Length: 5
  NormalizedText:
  Entity: ibuprofen
  Category: MedicationName
  Offset: 17
  Length: 9
  NormalizedText: ibuprofen
  Entity: twice daily
  Category: Frequency
  Offset: 34
  Length: 11
  NormalizedText:
  Found 2 relations in the current document:

    Relation: DosageOfMedication
    For this relation there are 2 roles
      Role Name: Dosage
      Associated Entity Text: 100mg
      Associated Entity Category: Dosage

      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName


    Relation: FrequencyOfMedication
    For this relation there are 2 roles
      Role Name: Medication
      Associated Entity Text: ibuprofen
      Associated Entity Category: MedicationName

      Role Name: Frequency
      Associated Entity Text: twice daily
      Associated Entity Category: Frequency
```

> [!TIP]
> Fast Healthcare Interoperability Resources (FHIR) structuring is available for preview using the Language REST API. The client libraries are not currently supported. [Learn more](../../how-to/call-api.md) on how to use FHIR structuring in your API call.

## Clean up resources

If you want to clean up and remove a Cognitive Services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

* [Portal](../../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> <a href="https://github.com/Azure/azure-sdk-for-net/issues/new?title=&body=%0A-%20**Package%20Name**:%20%0A-%20**Package%20Version**:%20%0A-%20**Operating%20System**:%20%0A-%20**.NET%20runtime%20version**:%20%0A%0A%5BEnter%20feedback%20here%5D%0A%0A%0A---%0A%23%23%23%23%20Document%20details%0A%0A⚠%20*Do%20not%20edit%20this%20section.%20It%20is%20required%20for%20learn.microsoft.com%20➟%20GitHub%20issue%20linking.%0A%0ALanguage%20Quickstart%20Feedback%0A*%20Content:%20%5BQuickstart:%20Using%20Text%20Analytics%20for%20health%20client%20library%20and%20REST%20API%20-%20Azure%20Cognitive%20Services%5D(https:%2F%2Flearn.microsoft.com%2Fazure%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart%3Fpivots%3Dprogramming-language-csharp)%0A*%20Content%20Source:%20%5Barticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md%5D(https:%2F%2Fgithub.com%2FMicrosoftDocs%2Fazure-docs%2Fblob%2Fmain%2Farticles%2Fcognitive-services%2Flanguage-service%2Ftext-analytics-for-health%2Fquickstart.md)%0A*%20Section:%20**Clean-up-resources**%0A*%20Service:%20**cognitive-services**%0A*%20Sub-service:%20**language-service**%0A&labels=Cognitive%20-%20Language%2CCognitive%20Language%20QS%20Feedback" target="_target">I ran into an issue</a>
