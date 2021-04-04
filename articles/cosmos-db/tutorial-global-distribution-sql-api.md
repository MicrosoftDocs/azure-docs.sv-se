---
title: 'Självstudie: Azure Cosmos DB global distributions guide för SQL API'
description: 'Självstudie: Lär dig hur du konfigurerar Azure Cosmos DB global distribution med SQL-API: t med .NET, Java, python och andra SDK: er'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: aacb8d4ffb98d553b17aa52e4c4b11a4837dc1c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337905"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Självstudie: Konfigurera Azure Cosmos DB global distribution med SQL-API: et
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I den här artikeln visar vi hur du använder Azure Portal för att konfigurera Azure Cosmos DB global distribution och sedan ansluter med SQL-API: et.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [SQL-API: erna](./introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> Ansluta till en önskad region med SQL-API: et

I syfte att dra nytta av den [globala distributionen](distribute-data-globally.md) kan klientprogrammen ange den beställda listan med inställningar för regioner som ska användas för att utföra dokumentåtgärder. Utifrån Azure Cosmos DB-kontokonfigurationen, den aktuella regionala tillgängligheten och den angivna listan över inställningar, så väljs den mest optimala slutpunkten för genomförande av skriv- och läsåtgärder av SQL-SDK:n.

Denna lista över inställningar anges när en anslutning initieras med SQL-SDK:erna. SDK: erna accepterar en valfri parameter `PreferredLocations` som är en ordnad lista över Azure-regioner.

SDK:n skickar automatiskt alla skrivningar till den aktuella skrivregionen. Alla läsningar skickas till den första tillgängliga regionen i listan över önskade platser. Om begäran Miss lyckas kommer klienten att Miss lyckas med att gå nedåt i listan till nästa region.

SDK försöker bara läsa från de regioner som anges i önskade platser. Om till exempel Azure Cosmos-kontot är tillgängligt i fyra regioner, men klienten endast anger två Läs-och icke-skrivbara regioner i `PreferredLocations` , kommer inga läsningar från den Läs region som inte anges i `PreferredLocations` . Om Läs regionerna som anges i `PreferredLocations` listan inte är tillgängliga, kommer läsningar att hanteras utanför Skriv regionen.

Programmet kan verifiera den aktuella Skriv slut punkten och den Läs slut punkt som valts av SDK: n genom att kontrol lera två egenskaper `WriteEndpoint` och `ReadEndpoint` tillgänglig i SDK version 1,8 och senare. Om `PreferredLocations` egenskapen inte anges kommer alla begär Anden att betjänas från den aktuella Skriv regionen.

Om du inte anger önskade platser men använder `setCurrentLocation` -metoden fyller SDK: n automatiskt i de prioriterade platserna baserat på den aktuella region som klienten körs i. SDK: n beställer regionerna utifrån en regions närhet till den aktuella regionen.

## <a name="net-sdk"></a>.NET SDK

SDK:n kan användas utan några kodändringar. I det här fallet styr SDK:n automatiskt både läsningar och skrivningar till den aktuella skrivregionen.

Parametern ConnectionPolicy för DocumentClient-konstruktorn har en egenskap som kallas Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations i .NET SDK version 1.8 och senare. Den här egenskapen är av typen samling `<string>` och bör innehålla en lista med regionnamn. Sträng värdena formateras efter region namns kolumnen på sidan [Azure-regioner][regions] , utan blank steg före eller efter det första respektive sista.

De aktuella skriv- och lässlutpunkterna är tillgängliga i DocumentClient.WriteEndpoint respektive DocumentClient.ReadEndpoint.

> [!NOTE]
> Slutpunkternas URL:er ska inte betraktas som långlivade konstanter. Tjänsten kan uppdatera dem när som helst. SDK:n hanterar sådana ändringar automatiskt.
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

Om du använder .NET v2 SDK använder du `PreferredLocations` egenskapen för att ange önskad region.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

Du kan också använda- `SetCurrentLocation` egenskapen och låta SDK: n välja önskad plats baserat på närhet.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Om du använder .NET v3 SDK använder du `ApplicationPreferredRegions` egenskapen för att ange önskad region.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

Du kan också använda- `ApplicationRegion` egenskapen och låta SDK: n välja önskad plats baserat på närhet.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> Slutpunkternas URL:er ska inte betraktas som långlivade konstanter. Tjänsten kan uppdatera dem när som helst. SDK:n hanterar sådana ändringar automatiskt.
>
>

Nedan visas ett kod exempel för Node.js/JavaScript.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

Följande kod visar hur du ställer in önskade platser med hjälp av python SDK:

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Java v4 SDK

Följande kod visar hur du ställer in önskade platser med Java SDK:

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) asynkront API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[Synkronisera](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST

När ett databas konto har gjorts tillgängligt i flera regioner kan klienterna fråga dess tillgänglighet genom att utföra en GET-begäran på denna URI `https://{databaseaccount}.documents.azure.com/`

Tjänsten returnerar en lista med regioner och URI:erna för deras motsvarande Azure Cosmos DB-slutpunkter för replikerna. Den aktuella skrivregionen indikeras i svaret. Klienten kan sedan välja lämplig slutpunkt för alla ytterligare REST API-förfrågningar enligt följande.

Exempelsvar

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* Alla PUT-, POST- och DELETE-förfrågningar måste gå till den angivna skriv-URI:n
* Alla får och andra skrivskyddade begär Anden (till exempel frågor) kan gå till valfri slut punkt för klientens val

Skrivförfrågningar till skrivskyddade regioner misslyckas med HTTP-felkoden 403 (Forbidden (Förbjudet)).

Om Skriv regionen ändras efter klientens första identifierings fas kommer efterföljande skrivningar till den tidigare Skriv regionen att Miss Missing med HTTP-felkoden 403 ("förbjudet"). Klienten bör sedan få listan med regioner igen och därigenom få den uppdaterade skrivregionen.

Och med detta är den här självstudiekursen klar. Mer information om hur du kan hantera ditt globalt replikerade kontos konsekvens finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Mer information om hur global databasreplikering fungerar i Azure Cosmos DB finns i [Distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med SQL-API:erna

Du kan nu fortsätta till nästa självstudiekurs och lära dig hur du utvecklar lokalt med hjälp av den lokala Azure Cosmos DB-emulatorn.

> [!div class="nextstepaction"]
> [Utveckla lokalt med emulatorn](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/