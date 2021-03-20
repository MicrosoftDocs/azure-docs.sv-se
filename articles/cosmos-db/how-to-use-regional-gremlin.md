---
title: Regionala slut punkter för Azure Cosmos DB diagram databas
description: Lär dig hur du ansluter till en närmast graf Database-slutpunkt för ditt program
author: christopheranderson
ms.author: chrande
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/09/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3e30252d8f5e80538139f8100f1070385c1b6016
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93361795"
---
# <a name="regional-endpoints-for-azure-cosmos-db-graph-account"></a>Regionala slut punkter för Azure Cosmos DB diagram konto
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB Graph Database [distribueras globalt](distribute-data-globally.md) så att program kan använda flera Läs-slut punkter. Program som behöver skriv åtkomst på flera platser bör aktivera kapacitet för [flera regioner](how-to-multi-master.md) .

Skäl att välja fler än en region:
1. **Vågrät Läs skalbarhet** – när program belastningen ökar kan det vara försiktig att dirigera Läs trafik till olika Azure-regioner.
2. **Lägre latens** – du kan minska belastningen på nätverks fördröjningen för varje genom gång genom att dirigera Läs-och Skriv trafik till närmaste Azure-region.

Krav för **data placering** uppnås genom att ange Azure Resource Manager princip på Cosmos DB-konto. Kunden kan begränsa regioner till vilka Cosmos DB replikerar data.

## <a name="traffic-routing"></a>Trafik dirigering

Cosmos DB Graph Database Engine körs i flera regioner, som var och en innehåller flera kluster. Varje kluster har hundratals datorer. Cosmos DB Graph-konto DNS CNAME- *AccountName.Gremlin.Cosmos.Azure.com* matchas mot DNS A-post för ett kluster. En enskild IP-adress för en belastningsutjämnare döljer intern kluster sto pol Ogin.

En regional DNS CNAME-post skapas för alla regioner i Cosmos DB Graph-konto. Formatet på den regionala slut punkten är *AccountName-region.Gremlin.Cosmos.Azure.com*. Region segmentet för den regionala slut punkten erhålls genom att ta bort alla blank steg från [Azures region](https://azure.microsoft.com/global-infrastructure/regions) namn. `"East US 2"`Region för `"contoso"` globalt databas konto skulle till exempel ha en DNS CNAME- *contoso-eastus2.Gremlin.Cosmos.Azure.com*

TinkerPop Gremlin-klienten är utformad för att fungera med en enda server. Programmet kan använda global skrivbar DNS CNAME för Läs-och Skriv trafik. Region medveten program ska använda regionala slut punkter för Läs trafik. Använd endast regional slut punkt för Skriv trafik om en speciell region har kon figurer ATS för att godkänna skrivningar. 

> [!NOTE]
> Cosmos DB diagram motor kan godkänna Skriv åtgärder i Läs region genom att proxy trafik för att skriva region. Vi rekommenderar inte att du skickar skrivningar till skrivskyddad region eftersom det ökar fördröjnings fördröjningen och omfattas av begränsningar i framtiden.

Det globala databas kontot CNAME pekar alltid på en giltig Skriv region. Cosmos DB uppdaterar det globala databas kontot CNAME för att peka på ny region vid redundans på Server sidan. Om programmet inte kan hantera vidarebefordran av trafik efter redundansväxlingen ska det använda det globala databas kontot DNS CNAME.

> [!NOTE]
> Cosmos DB dirigerar inte trafik baserat på den geografiska närheten av anroparen. Det är upp till varje program att välja rätt region enligt unika program behov.

## <a name="portal-endpoint-discovery"></a>Portal slut punkts identifiering

Det enklaste sättet att hämta listan över regioner för Azure Cosmos DB graf-konto är översikts blad i Azure Portal. Den fungerar för program som inte ändrar regioner ofta, eller har ett sätt att uppdatera listan via program konfiguration.

:::image type="content" source="./media/how-to-use-regional-gremlin/get-end-point-portal.png " alt-text="Hämta regioner i Cosmos DB Graph-konto från portalen":::

Exemplet nedan visar allmänna principer för åtkomst till regional Gremlin-slutpunkt. Programmet bör överväga antalet regioner som trafiken ska skickas till och antalet motsvarande Gremlin-klienter att instansiera.

```csharp
// Example value: Central US, West US and UK West. This can be found in the overview blade of you Azure Cosmos DB Gremlin Account. 
// Look for Write Locations in the overview blade. You can click to copy and paste.
string[] gremlinAccountRegions = new string[] {"Central US", "West US" ,"UK West"};
string gremlinAccountName = "PUT-COSMOSDB-ACCOUNT-NAME-HERE";
string gremlinAccountKey = "PUT-ACCOUNT-KEY-HERE";
string databaseName = "PUT-DATABASE-NAME-HERE";
string graphName = "PUT-GRAPH-NAME-HERE";

foreach (string gremlinAccountRegion in gremlinAccountRegions)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = $"{gremlinAccountName}-{gremlinAccountRegion.ToLowerInvariant().Replace(" ", string.Empty)}.gremlin.cosmos.azure.com";

  GremlinServer regionalGremlinServer = new GremlinServer(
    hostname: regionalGremlinEndPoint, 
    port: 443,
    enableSsl: true,
    username: "/dbs/" + databaseName + "/colls/" + graphName,
    password: gremlinAccountKey);

  GremlinClient regionalGremlinClient = new GremlinClient(
    gremlinServer: regionalGremlinServer,
    graphSONReader: new GraphSON2Reader(),
    graphSONWriter: new GraphSON2Writer(),
    mimeType: GremlinClient.GraphSON2MimeType);
}
```

## <a name="sdk-endpoint-discovery"></a>Identifiering av SDK-slutpunkt

Programmet kan använda [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) för att identifiera Läs-och skriv platser för graf-konto. Dessa platser kan ändras när som helst genom manuell omkonfigurering på Server sidan eller automatisk redundans.

TinkerPop Gremlin SDK har inget API för att identifiera Cosmos DB graf-databasens konto områden. Program som kräver slut punkts identifiering måste vara värd för 2 separata SDK: er i process utrymmet.

```csharp
// Depending on the version and the language of the SDK (.NET vs Java vs Python)
// the API to get readLocations and writeLocations may vary.
IDocumentClient documentClient = new DocumentClient(
    new Uri(cosmosUrl),
    cosmosPrimaryKey,
    connectionPolicy,
    consistencyLevel);

DatabaseAccount databaseAccount = await cosmosClient.GetDatabaseAccountAsync();

IEnumerable<DatabaseAccountLocation> writeLocations = databaseAccount.WritableLocations;
IEnumerable<DatabaseAccountLocation> readLocations = databaseAccount.ReadableLocations;

// Pick write or read locations to construct regional endpoints for.
foreach (string location in readLocations)
{
  // Convert preferred read location to the form "[acountname]-[region].gremlin.cosmos.azure.com".
  string regionalGremlinEndPoint = location
    .Replace("http:\/\/", string.Empty)
    .Replace("documents.azure.com:443/", "gremlin.cosmos.azure.com");
  
  // Use code from the previous sample to instantiate Gremlin client.
}
```

## <a name="next-steps"></a>Nästa steg
* [Hantera databas konto kontroll](how-to-manage-database-account.md) i Azure Cosmos DB
* [Hög tillgänglighet](high-availability.md) i Azure Cosmos DB
* [Global distribution med Azure Cosmos DB – under huven](global-dist-under-the-hood.md)
* [Azure CLI-exempel](cli-samples.md) för Azure Cosmos DB
