---
title: Azure Cosmos DB utlösare för functions 2. x och högre
description: Lär dig att använda Azure Cosmos DB-utlösaren i Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 6f4e43efeb1882f52bd335d83a3660a94040ab8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729223"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB utlösare för Azure Functions 2. x och högre

Azure Cosmos DB-utlösaren använder [Azure Cosmos DB ändra feed](../cosmos-db/change-feed.md) för att lyssna efter infogningar och uppdateringar över partitioner. Ändrings flödet publicerar infogningar och uppdateringar, tas inte bort.

Information om konfiguration och konfigurations information finns i [översikten](./functions-bindings-cosmosdb-v2.md).

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

I följande exempel visas en [C#-funktion](functions-dotnet-class-library.md) som anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

I följande exempel visas en Cosmos DB trigger-bindning i en *function.jspå* filen och en [C#-skript funktion](functions-reference-csharp.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster läggs till eller ändras.

Här är bindnings data i *function.jspå* filen:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är C#-skript koden:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="java"></a>[Java](#tab/java)

Den här funktionen anropas när det finns infogningar eller uppdateringar i den angivna databasen och samlingen.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


I [Java Functions runtime-biblioteket](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBTrigger` anteckningen för parametrar vars värde kommer från Cosmos dB.  Den här anteckningen kan användas med inbyggda Java-typer, Pojo eller null-värden med hjälp av `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I följande exempel visas en Cosmos DB trigger-bindning i en *function.jsi* filen och en [JavaScript-funktion](functions-reference-node.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster läggs till eller ändras.

Här är bindnings data i *function.jspå* filen:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är JavaScript-koden:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

I följande exempel visas hur du kör en funktion som data ändringar i Cosmos DB.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

I _run.ps1_ -filen har du åtkomst till dokumentet som utlöser funktionen via- `$Documents` parametern.

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

I följande exempel visas en Cosmos DB trigger-bindning i en *function.jspå* filen och en [python-funktion](functions-reference-python.md) som använder bindningen. Funktionen skriver logg meddelanden när Cosmos DB poster ändras.

Här är bindnings data i *function.jspå* filen:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Här är python-koden:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

---

## <a name="attributes-and-annotations"></a>Attribut och anteckningar

# <a name="c"></a>[C#](#tab/csharp)

Använd attributet [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) i [C#-klass bibliotek](functions-dotnet-class-library.md).

Attributets konstruktor tar databasens namn och samlings namn. Information om dessa inställningar och andra egenskaper som du kan konfigurera finns i [trigger-Configuration](#configuration). Här är ett `CosmosDBTrigger` attribut exempel i en metodsignatur:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Ett fullständigt exempel finns i [utlösare](#example).

# <a name="c-script"></a>[C#-skript](#tab/csharp-script)

Attribut stöds inte av C#-skript.

# <a name="java"></a>[Java](#tab/java)

I [Java Functions runtime-bibliotek](/java/api/overview/azure/functions/runtime)använder du `@CosmosDBInput` anteckningen för parametrar som läser data från Cosmos dB.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Attribut stöds inte av Java Script.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Attribut stöds inte av PowerShell.

# <a name="python"></a>[Python](#tab/python)

Attribut stöds inte av python.

---

## <a name="configuration"></a>Konfiguration

I följande tabell förklaras de egenskaper för bindnings konfiguration som du anger i *function.js* filen och `CosmosDBTrigger` attributet.

|function.jspå egenskap | Attributets egenskap |Beskrivning|
|---------|---------|----------------------|
|**bastyp** | saknas | Måste anges till `cosmosDBTrigger` . |
|**position** | saknas | Måste anges till `in` . Den här parametern anges automatiskt när du skapar utlösaren i Azure Portal. |
|**Namn** | saknas | Variabel namnet som används i funktions kod som representerar listan med dokument med ändringar. |
|**connectionStringSetting**|**ConnectionStringSetting** | Namnet på en app-inställning som innehåller anslutnings strängen som används för att ansluta till det Azure Cosmos DB-konto som övervakas. |
|**Databas**|**DatabaseName**  | Namnet på Azure Cosmos DB databasen med den samling som övervakas. |
|**Samling** |**CollectionName** | Namnet på den samling som övervakas. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Valfritt Namnet på en app-inställning som innehåller anslutnings strängen till det Azure Cosmos DB konto som innehåller låne samlingen. Om värdet inte anges `connectionStringSetting` används värdet. Den här parametern anges automatiskt när bindningen skapas i portalen. Anslutnings strängen för samlingen lån måste ha Skriv behörighet.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Valfritt Namnet på databasen som innehåller den samling som används för att lagra lån. Om inställningen inte anges används värdet för `databaseName` inställningen. Den här parametern anges automatiskt när bindningen skapas i portalen. |
|**leaseCollectionName** | **LeaseCollectionName** | Valfritt Namnet på den samling som används för att lagra lån. Om värdet inte anges används värdet `leases` . |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Valfritt När den är inställd på `true` skapas låne samlingen automatiskt när den inte redan finns. Standardvärdet är `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Valfritt Definierar antalet enheter för programbegäran som ska tilldelas när låne samlingen skapas. Den här inställningen används bara när `createLeaseCollectionIfNotExists` har angetts till `true` . Den här parametern anges automatiskt när bindningen skapas med hjälp av portalen.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Valfritt När värdet har angetts läggs värdet till som ett prefix till de lån som skapas i leasing samlingen för den här funktionen. Genom att använda ett prefix kan två separata Azure Functions dela samma Lease-samling med olika prefix.
|**feedPollDelay**| **FeedPollDelay**| Valfritt Tiden (i millisekunder) för fördröjningen mellan avsökning av en partition för nya ändringar i flödet efter att alla aktuella ändringar har tömts. Standardvärdet är 5 000 millisekunder eller 5 sekunder.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Valfritt När det är inställt definierar den, i millisekunder, intervallet för att starta en aktivitet för att beräkna om partitioner distribueras jämnt mellan kända värd instanser. Standardvärdet är 13000 (13 sekunder).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Valfritt När det är inställt definierar den, i millisekunder, intervallet för vilken lånet tas i ett lån som representerar en partition. Om lånet inte förnyas inom det här intervallet kommer det att förfalla och ägarskapet av partitionen flyttas till en annan instans. Standardvärdet är 60000 (60 sekunder).
|**leaseRenewInterval**| **LeaseRenewInterval**| Valfritt När det är inställt definierar förnyelse intervallet i millisekunder för alla lån för partitioner som för närvarande innehas av en instans. Standardvärdet är 17000 (17 sekunder).
|**checkpointFrequency**| **CheckpointFrequency**| Valfritt När det är inställt definierar den, i millisekunder, intervallet mellan låne kontroll punkter. Standardvärdet är alltid efter varje funktions anrop.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Valfritt När den här egenskapen anges anger den här egenskapen det maximala antalet objekt som tas emot per funktions anrop. Om åtgärder i den övervakade samlingen utförs genom lagrade procedurer bevaras [transaktions omfång](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) vid läsning av objekt från ändrings flödet. Det innebär att antalet mottagna objekt kan vara högre än det angivna värdet så att objekten som ändras av samma transaktion returneras som en del av en atomisk batch.
|**startFromBeginning**| **StartFromBeginning**| Valfritt Det här alternativet anger att utlösaren ska läsa ändringar från början av samlingens ändrings historik i stället för att starta vid den aktuella tiden. Det går bara att läsa från början första gången utlösaren startar, precis som vid efterföljande körningar. kontroll punkterna är redan lagrade. Att ställa in det här alternativet på `true` när det redan har skapats lån har ingen påverkan. |
|**preferredLocations**| **PreferredLocations**| Valfritt Definierar önskade platser (regioner) för geo-replikerade databas konton i Azure Cosmos DBs tjänsten. Värdena ska vara kommaavgränsade. Till exempel "östra USA, södra centrala USA, norra Europa". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Användning

Utlösaren kräver en andra samling som används för att lagra _lån_ över partitionerna. Både samlingen som övervakas och samlingen som innehåller lånet måste vara tillgängliga för att utlösaren ska fungera.

>[!IMPORTANT]
> Om flera funktioner har kon figurer ATS för att använda en Cosmos DB-utlösare för samma samling, ska varje funktion använda en dedikerad Lease-samling eller ange en annan `LeaseCollectionPrefix` för varje funktion. Annars aktive ras bara en av funktionerna. Information om prefixet finns i [avsnittet konfiguration](#configuration).

Utlösaren anger inte om ett dokument har uppdaterats eller infogats. det innehåller bara själva dokumentet. Om du behöver hantera uppdateringar och infogningar på olika sätt kan du göra det genom att implementera tidsstämpelfält för infogning eller uppdatering.

## <a name="next-steps"></a>Nästa steg

- [Läsa ett Azure Cosmos DB dokument (ingående bindning)](./functions-bindings-cosmosdb-v2-input.md)
- [Spara ändringar i ett Azure Cosmos DB dokument (utgående bindning)](./functions-bindings-cosmosdb-v2-output.md)
