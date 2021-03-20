---
title: Använd inbyggda Notebook-kommandon och-funktioner i Azure Cosmos DB C#-anteckningsböcker (för hands version)
description: Lär dig hur du använder inbyggda kommandon och funktioner för att utföra vanliga åtgärder med hjälp av Azure Cosmos DB inbyggda C#-antecknings böcker.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340489"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Använd inbyggda Notebook-kommandon och-funktioner i Azure Cosmos DB C#-anteckningsböcker (för hands version)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Inbyggda Jupyter Notebook i Azure Cosmos DB gör att du kan analysera och visualisera dina data från Azure Portal. Den här artikeln beskriver hur du använder inbyggda Notebook-kommandon och-funktioner för att utföra vanliga åtgärder i C#-anteckningsboker.

## <a name="install-a-new-nuget-package"></a>Installera ett nytt NuGet-paket
När du har aktiverat Notebook support för dina Azure Cosmos-konton kan du öppna en ny antecknings bok och installera ett paket.

Infoga och kör följande kod i en ny Code-cell, Ersätt ``PackageToBeInstalled`` med det önskade NuGet-paketet och ``optionalVersion`` med en speciell version av paketet om du vill. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

Du kan installera flera NuGet-paket i samma cell. Paket kommer att vara tillgängliga för användning från alla antecknings böcker i arbets ytan för Azure Cosmos-kontot. 

För närvarande stöder inte arbets ytan för C#-anteckningsboker rekursiv matchning av NuGet-paket. Om ett NuGet-paket är beroende av andra NuGet-paket som inte är installerade, måste du uttryckligen referera till dem tillsammans med det överordnade paketet.

> [!TIP]
> Om din bärbara dator kräver ett anpassat paket, rekommenderar vi att du lägger till en cell i din bärbara dator för att installera paketet och göra det till den första cellen. Detta minskar risken för konflikter med andra paket som Azure Cosmos DB läses in som standard. Det är också enkelt att installera paketen igen om du [återställer arbets ytan](#reset-notebooks-workspace), vilket tar bort alla paket. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Använd den inbyggda Azure Cosmos DB .NET SDK
Version 3 av [Azure Cosmos dB .NET SDK för SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) installeras och ingår i Notebook-miljön för Azure Cosmos-kontot.

Skapa en instans av ``CosmosClient`` för att köra en SDK-åtgärd. 

Exempel:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Mer information finns i [.net v3 SDK-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> Den inbyggda Azure Cosmos DB .NET SDK stöds endast för SQL (Core) API-konton. För andra API: er måste du [installera den relevanta .net-driv rutinen](#install-a-new-nuget-package) som motsvarar API: et. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Ange anpassade alternativ med ``CosmosClientOptions``
För större flexibilitet kan du ange den anpassade ``CosmosClientOptions`` egenskapen och skicka den till din ``CosmosClient`` instans. Du kan använda den här egenskapen för att:

- Ange ett program namn i User Agent-suffixet för att inkludera det i varje begäran.
- Ange den önskade region som ska användas när åtgärder körs mot tjänsten.
- Ange en anpassad princip för återförsök för att hantera frekvens-begränsade begär Anden.

Se artikeln [COSMOSCLIENTOPTIONS API Reference](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) för alla inställningar som stöds. Följande är ett exempel som visar hur du ställer in `cosmosClientOptions` egenskapen:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Åtkomst till konto slut punkten och primära nyckel variabler
Du kan komma åt den inbyggda slut punkten och nyckeln för Azure Cosmos-kontot där din bärbara dator finns.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key`` ``Cosmos.Endpoint`` Variablerna och kan bara användas för SQL API. För andra API: er hittar du slut punkten och nyckeln i bladet **anslutnings strängar** eller **nycklar** i ditt Azure Cosmos-konto.  

## <a name="print-console-output-in-c-code"></a>Skriv ut konsol utdata i C#-kod
I C#-koden kan du använda syntaxen display. AsMarkdown () med [String-interpolation](/dotnet/csharp/language-reference/tokens/interpolated) för att skriva ut konsol utdata som visas när du kör cellen. 

Exempel: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> Console. WriteLine ()-syntaxen stöds inte för närvarande i C#-anteckningsboker. Använd display. AsMarkdown för att skriva ut konsolens utdata från programmet. 

## <a name="use-built-in-nteract-data-explorer"></a>Använd inbyggda nteract data Explorer
Du kan använda den inbyggda [nteract data Utforskaren](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) för att filtrera och visualisera en samling objekt. I en cell ska du placera variabeln som du vill visualisera på den sista raden, som automatiskt visas i nteract när du kör cellen.

I exemplet *GetingStarted_Csharp. ipynb* kan vi till exempel skriva ut variabeln med vårt resultat, ``telemetryEvents`` . Se [GettingStarted_Csharp. ipynb Notebook](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) för hela exemplet. 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Csharp Query-cell":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteract data Utforskare":::

## <a name="use-built-in-dictionary-viewer"></a>Använd inbyggda ordboks visnings program
Du kan använda det inbyggda lexikon visnings programmet för att visa en variabel. I en cell ska du placera variabeln som du vill visualisera på den sista raden, som visas automatiskt när cellen körs.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Inbyggda ordboks visnings program":::

## <a name="upload-json-items-to-a-container"></a>Ladda upp JSON-objekt till en behållare
Du kan använda ``%%upload`` kommandot Magic för att överföra data från en JSON-fil till en angiven Azure Cosmos-behållare. Använd följande kommando för att ladda upp objekten:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Ersätt ``{database_id}`` och ``{container_id}`` med namnet på databasen och behållaren i ditt Azure Cosmos-konto. 
- Ersätt ``{url_location_of_file}`` med platsen för din JSON-fil. Filen måste vara en matris med giltiga JSON-objekt och den bör vara tillgänglig via det offentliga Internet.

Exempel:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Med utmatnings statistik kan du beräkna de faktiska RU/s som används för att överföra objekten. Om till exempel 25 000 ru: er förbrukades över 38 sekunder, är det effektiva RU/s 25 000 ru: er/38 sekunder = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Kör en annan antecknings bok i den aktuella bärbara datorn 
Du kan använda ``%%run`` kommandot Magic för att köra en annan antecknings bok i din arbets yta från din aktuella antecknings bok. Använd syntaxen:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Ersätt ``{notebookName}`` med namnet på den antecknings bok som du vill köra. Antecknings boken måste finnas i din aktuella arbets yta för mina antecknings böcker. 

## <a name="reset-notebooks-workspace"></a>Återställ arbets ytan för antecknings böcker
Om du vill återställa arbets ytan för antecknings böcker till standardinställningarna väljer du **Återställ arbets yta** i kommando fältet. Detta tar bort alla anpassade installerade paket och startar om Jupyter-servern. Dina antecknings böcker, filer och Azure Cosmos-resurser kommer inte att påverkas.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Återställ arbets ytan för antecknings böcker":::

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om fördelarna med att [Azure Cosmos DB Jupyter Notebooks](cosmosdb-jupyter-notebooks.md)
- Lär dig mer om [Azure Cosmos dB .NET SDK för SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3)
