---
title: Skapa en RESTful-slutpunkt
description: I den här självstudien visas hur du skapar en RESTful-slutpunkt för anpassade providers. Den beskriver hur du hanterar begär Anden och svar för RESTful HTTP-metoder som stöds.
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98200217"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Redigera en RESTful-slutpunkt för anpassade providers

En anpassad Provider är ett kontrakt mellan Azure och en slut punkt. Med anpassade providers kan du anpassa arbets flöden i Azure. Den här självstudien visar hur du skapar en anpassad RESTful-slutpunkt för provider. Om du inte är bekant med Azure-anpassade leverantörer kan du läsa [Översikt över anpassade resurs leverantörer](overview.md).

> [!NOTE]
> Den här självstudien bygger på självstudien [som konfigurerar Azure Functions för Azure-anpassade leverantörer](./tutorial-custom-providers-function-setup.md). Några av stegen i den här självstudien fungerar bara om en Azure Function-app har kon figurer ATS för att fungera med anpassade providers.

## <a name="work-with-custom-actions-and-custom-resources"></a>Arbeta med anpassade åtgärder och anpassade resurser

I den här självstudien uppdaterar du Function-appen så att den fungerar som en RESTful-slutpunkt för din anpassade Provider. Resurser och åtgärder i Azure modelleras efter följande grundläggande RESTful-specifikation:

- **Lägg** till: skapa en ny resurs
- **Hämta (instans)**: Hämta en befintlig resurs
- **Ta bort**: ta bort en befintlig resurs
- **Post**: utlöser en åtgärd
- **Hämta (samling)**: lista alla befintliga resurser

 I den här självstudien använder du Azure Table Storage. Men alla databaser och lagrings tjänster kan fungera.

## <a name="partition-custom-resources-in-storage"></a>Partitionera anpassade resurser i lagring

Eftersom du skapar en RESTful-tjänst måste du lagra de skapade resurserna. För Azure Table Storage måste du generera partitions-och rad nycklar för dina data. För anpassade providers bör data partitioneras till den anpassade providern. När en inkommande begäran skickas till den anpassade providern lägger den anpassade providern till `x-ms-customproviders-requestpath` rubriken i utgående begär anden till slut punkten.

I följande exempel visas en `x-ms-customproviders-requestpath` rubrik för en anpassad resurs:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Baserat på exemplets `x-ms-customproviders-requestpath` Rubrik kan du skapa parametrarna *PartitionKey* och *rowKey* för ditt lagrings utrymme som visas i följande tabell:

Parameter | Mall | Beskrivning
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | Parametern *partitionKey* anger hur data partitioneras. Vanligt vis partitioneras data av den anpassade Provider-instansen.
*rowKey* | `{myResourceType}:{myResourceName}` | Parametern *rowKey* anger den enskilda identifieraren för data. Vanligt vis är identifieraren namnet på resursen.

Du måste också skapa en ny klass för att modellera din anpassade resurs. I den här självstudien lägger du till följande **CustomResource** -klass i din Function-app:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** är en enkel, generisk klass som accepterar indata. Den baseras på **TableEntity**, som används för att lagra data. Klassen **CustomResource** ärver två egenskaper från **TableEntity**: **partitionKey** och **rowKey**.

## <a name="support-custom-provider-restful-methods"></a>Stöd för anpassade Provider RESTful-metoder

> [!NOTE]
> Om du inte kopierar koden direkt från den här självstudien måste svars innehållet vara ett giltigt JSON som anger `Content-Type` sidhuvudet till `application/json` .

Nu när du har konfigurerat data partitionering skapar du de grundläggande CRUD och utlöser metoder för anpassade resurser och anpassade åtgärder. Eftersom anpassade providrar fungerar som proxyservrar måste RESTful-slutpunkten modellera och hantera begäran och svar. Följande kodfragment visar hur du hanterar grundläggande RESTful-åtgärder.

### <a name="trigger-a-custom-action"></a>Utlös en anpassad åtgärd

För anpassade leverantörer utlöses en anpassad åtgärd via POST-begäranden. En anpassad åtgärd kan alternativt acceptera en begär ande text som innehåller en uppsättning indataparametrar. Åtgärden returnerar sedan ett svar som signalerar resultatet av åtgärden och huruvida det lyckades eller misslyckades.

Lägg till följande **TriggerCustomAction** -Metod i din Function-app:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

Metoden **TriggerCustomAction** accepterar en inkommande begäran och upprepar bara svaret med en status kod.

### <a name="create-a-custom-resource"></a>Skapa en anpassad resurs

För anpassade providers skapas en anpassad resurs genom att skicka begär Anden. Den anpassade providern accepterar en JSON-begärantext, som innehåller en uppsättning egenskaper för den anpassade resursen. Resurser i Azure följer en RESTful modell. Du kan använda samma URL för begäran för att skapa, hämta eller ta bort en resurs.

Lägg till följande **CreateCustomResource** -Metod för att skapa nya resurser:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

Metoden **CreateCustomResource** uppdaterar inkommande begäran om du vill inkludera de Azure-/regionsspecifika fält **-ID**, **namn** och **typ**. Dessa fält är toppnivå egenskaper som används av tjänster i Azure. De gör att den anpassade providern samverkar med andra tjänster som Azure Policy, Azure Resource Manager mallar och Azure aktivitets logg.

Egenskap | Exempel | Beskrivning
---|---|---
**Namn** | {myCustomResourceName} | Namnet på den anpassade resursen
**bastyp** | Microsoft. CustomProviders/resourceProviders/{resourceTypeName} | Namn området resurs typ
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Resurs-ID

Förutom att lägga till egenskaperna, sparade du även JSON-dokumentet till Azure Table Storage.

### <a name="retrieve-a-custom-resource"></a>Hämta en anpassad resurs

För anpassade leverantörer hämtas en anpassad resurs via GET-begäranden. En anpassad Provider accepterar *inte* en JSON-begärans text. För GET-begäranden använder slut punkten `x-ms-customproviders-requestpath` rubriken för att returnera den resurs som redan har skapats.

Lägg till följande **RetrieveCustomResource** -Metod för att hämta befintliga resurser:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

I Azure följer resurserna en RESTful modell. URL: en för begäran som skapar en resurs returnerar även resursen om en GET-begäran utförs.

### <a name="remove-a-custom-resource"></a>Ta bort en anpassad resurs

För anpassade providers tas en anpassad resurs bort via DELETE-begäranden. En anpassad Provider accepterar *inte* en JSON-begärans text. För en DELETE-begäran använder slut punkten `x-ms-customproviders-requestpath` rubriken för att ta bort den redan skapade resursen.

Lägg till följande **RemoveCustomResource** -Metod för att ta bort befintliga resurser:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

I Azure följer resurserna en RESTful modell. URL: en för begäran som skapar en resurs tar också bort resursen om en begäran om borttagning utförs.

### <a name="list-all-custom-resources"></a>Lista alla anpassade resurser

För anpassade providers kan du räkna upp en lista över befintliga anpassade resurser med hjälp av hämtnings begär Anden. En anpassad Provider accepterar *inte* en JSON-begärans text. För en samling av GET-begäranden använder slut punkten `x-ms-customproviders-requestpath` rubriken för att räkna upp de redan skapade resurserna.

Lägg till följande **EnumerateAllCustomResources** -Metod för att räkna upp befintliga resurser:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey QueryComparisons. GreaterThan och QueryComparisons. LessThan är Azure Table Storage-syntax för att utföra en "StartsWith"-fråga för strängar.

Om du vill visa en lista över alla befintliga resurser genererar du en Azure Table Storage-fråga som säkerställer att resurserna finns under den anpassade providerns partition. Frågan kontrollerar sedan att rad nyckeln börjar med samma `{myResourceType}` värde.

## <a name="integrate-restful-operations"></a>Integrera RESTful-åtgärder

När alla RESTful-metoder har lagts till i Function-appen uppdaterar du huvud **körnings** metoden som anropar funktionerna för att hantera olika rest-begäranden:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

Den uppdaterade **körnings** metoden innehåller nu den *tableStorage* -databindning som du har lagt till för Azure Table Storage. Den första delen av metoden läser `x-ms-customproviders-requestpath` rubriken och använder `Microsoft.Azure.Management.ResourceManager.Fluent` biblioteket för att parsa värdet som ett resurs-ID. `x-ms-customproviders-requestpath`Rubriken skickas av den anpassade providern och anger sökvägen till den inkommande begäran.

Med hjälp av det parsade resurs-ID: t kan du generera **partitionKey** -och **rowKey** -värden för data för att söka efter eller lagra anpassade resurser.

När du har lagt till metoder och klasser måste du uppdatera **using** -metoderna för Function-appen. Lägg till följande kod överst i C#-filen:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Om du går förlorad när som helst i den här självstudien hittar du det fullständiga kod exemplet i den [anpassade providern C# RESTful Endpoint Reference](./reference-custom-providers-csharp-endpoint.md). När du är klar med Function-appen sparar du appens URL för funktionen. Den kan användas för att utlösa Function-appen i senare självstudier.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en RESTful-slutpunkt för att arbeta med en Azure-anpassad Provider-slutpunkt. Information om hur du skapar en anpassad Provider finns i artikeln [Självstudier: skapa en anpassad Provider](./tutorial-custom-providers-create.md).
