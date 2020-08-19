---
title: Viktig information och resurser för Azure Cosmos DB Java SDK v4 för SQL API
description: Lär dig allt om Azure Cosmos DB Java SDK v4 för SQL API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: af0964dceca8b862d0008d878045203983a96bda
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586223"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core (SQL) API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Våren data v2](sql-api-sdk-java-spring-v2.md)
> * [Våren data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-anslutning](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core (SQL) kombinerar ett asynkront API och ett Sync-API till en maven-artefakt. V4 SDK ger förbättrade prestanda, nya API-funktioner och asynkront stöd baserat på projekt reaktor och det [Netta biblioteket](https://netty.io/). Användare kan förväntas förbättra prestanda med Azure Cosmos DB Java SDK v4 jämfört med [Azure Cosmos DB asynkron Java SDK v2](sql-api-sdk-async-java.md) och [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Den här viktig information gäller endast Azure Cosmos DB Java SDK v4. Om du använder en äldre version än v4 kan du läsa om hur du uppgraderar till v4 i [Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -guide.
>
> Här är tre steg för att komma igång snabbt!
> 1. Installera den [lägsta Java-körningen som stöds, JDK 8,](/java/azure/jdk/?view=azure-java-stable) så att du kan använda SDK: n.
> 2. Arbeta via [snabb starts guiden för Azure Cosmos DB Java SDK v4 som ger](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) dig till gång till maven-artefakten och går igenom grundläggande Azure Cosmos DB förfrågningar.
> 3. Läs [prestanda tipsen](performance-tips-java-sdk-v4-sql.md) för Azure Cosmos DB Java SDK v4 och [fel söknings](troubleshoot-java-sdk-v4-sql.md) guider för att optimera SDK för ditt program.
>
> [Azure Cosmos DB workshops och Labs](https://aka.ms/cosmosworkshop) är en annan fantastisk resurs för att lära dig hur du använder Azure Cosmos DB Java SDK v4!
>

## <a name="helpful-content"></a>Användbart innehåll

| Innehåll | Länk |
|---|---|
|**SDK-hämtning**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-dokumentation** | [Referens dokumentation för Java API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Bidra till SDK** | [Azure SDK för Java Central lagrings platsen på GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Komma igång** | [Snabb start: bygga en Java-app för att hantera Azure Cosmos DB SQL API-data](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [GitHub lagrings platsen med snabb starts kod](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Grundläggande kod exempel** | [Azure Cosmos DB: Java-exempel för SQL API](sql-api-java-sdk-samples.md) <br> [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Konsol program med ändrings flöde**| [Ändra feed-Java SDK v4-exempel](create-sql-api-java-changefeed.md) <br> [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Exempel på webbapp**| [Bygg en webbapp med Java SDK v4](sql-api-java-application.md) <br> [GitHub lagrings platsen med exempel kod](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Prestandatips**| [Prestandatips för Java SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Felsökning** | [Felsöka Java-SDK v4](troubleshoot-java-sdk-v4-sql.md) |
| **Migrera till v4 från en äldre SDK** | [Migrera till Java V4-SDK](migrate-java-v4-sdk.md) |
| **Lägsta körnings tid som stöds**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshops och labb** |[Start sida för Cosmos DB workshops](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Versions historik

### <a name="440-beta1-unreleased"></a>4.4.0 – beta. 1 (frisläppt)

### <a name="430-2020-07-29"></a>4.3.0 (2020-07-29)
#### <a name="new-features"></a>Nya funktioner
* Uppdaterad version av reaktor Core-bibliotek till `3.3.8.RELEASE` . 
* Uppdaterad version av reaktor-nett bibliotek till `0.9.10.RELEASE` . 
* Uppdaterade den uppdelade biblioteks versionen till `4.1.51.Final` . 
* Nya Överlagrings-API: er har lagts till `upsertItem` i `partitionKey` . 
* Stöd för öppen telemetri har lagts till. 
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Ett problem har åtgärd ATS där SSLException erhålls vid annullering av förfrågningar i GATEWAY-läge.
* Princip för omförsök för fasta resurser för att köra lagrade procedurer.
* Åtgärdat problem där SDK låser sig i fel SÖKNINGs läge på logg nivå. 
* Fasta Periodiska toppar i svars tid i direkt läge. 
* Fast problem med initierings tiden för den högsta klienten. 
* Fel i fast http-proxy vid anpassning av klienten med direkt läge och gateway-läge. 
* Fast potentiell NPE i användare skickar null-alternativ. 
* Lade till timeUnit `requestLatency` i i diagnostik-strängen.
* En dubblett av en URI-sträng togs bort från diagnostik strängen. 
* Fast diagnostik-sträng i korrekt JSON-format för punkt åtgärder.
* Åtgärdat problem med `.single()` operatör som gör att reaktor kedjan kan inblåsas i händelse av ett undantag som inte hittades. 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
#### <a name="new-features"></a>Nya funktioner
* Skript loggning har Aktiver ATS för `CosmosStoredProcedureRequestOptions` .
* Standardvärdet har uppdaterats `DirectConnectionConfig` `idleEndpointTimeout` till 1H och standardvärdet `connectTimeout` 5 SEK.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Ett fast problem där `GatewayConnectionConfig` `idleConnectionTimeout` ignorerades `DirectConnectionConfig` `idleConnectionTimeout` .
* Fasta `responseContinuationTokenLimitInKb` Get-och set-API: er i `CosmosQueryRequestOptions` .
* Ett problem har åtgärd ATS i frågan och ändra feed när samlingen återskapas med samma namn.
* Åtgärdat problem med högsta fråga som utlöser ClassCastException.
* Åtgärdat problem med order by-fråga utlöser NullPointerException.
* Fast problem vid hantering av avbrutna begär anden i direkt läge `onErrorDropped` som orsakar att reaktor anropas. 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Nya funktioner
* Stöd har lagts till för `GROUP BY` frågan.
* Ökat standardvärdet för maxConnectionsPerEndpoint till 130 i DirectConnectionConfig.
* Ökat standardvärdet för maxRequestsPerConnection till 30 i DirectConnectionConfig.
#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Fasta problem med order by-fråga returnerar dubbla resultat när de återupptas med hjälp av fortsättnings-token. 
* Korrigerade problem med värde fråga returnerar null-värden för nästlat objekt.
* Åtgärdat undantag för null-pekare i Request Manager i RntbdClientChannelPool.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Nya funktioner
* Har bytt namn `QueryRequestOptions` till `CosmosQueryRequestOptions` .
* Uppdaterat `ChangeFeedProcessorBuilder` till Builder-mönster.
* Uppdaterat `CosmosPermissionProperties` med nya behållar namn och underordnade resurs-API: er.
* Fler exempel & berikade dokument lades till `CosmosClientBuilder` . 
* Uppdaterade `CosmosDatabase`  &  `CosmosContainer` API: er med throughputProperties för autoskalning/autopilot-stöd. 
* Har bytt namn `CosmosClientException` till `CosmosException` . 
* Ersatts `AccessCondition`  &  `AccessConditionType` av `ifMatchETag()`  &  `ifNoneMatchETag()` API: er. 
* Sammanfogade alla `Cosmos*AsyncResponse`  &  `CosmosResponse` typer till en enda `CosmosResponse` typ.
* Har bytt namn `CosmosResponseDiagnostics` till `CosmosDiagnostics` .  
* Omsluten `FeedResponseDiagnostics` `CosmosDiagnostics` . 
* `jackson`Beroende av Azure-cosmos & förlitar sig på Azure-Core. 
* Ersatt `CosmosKeyCredential` av `AzureKeyCredential` typ. 
* `ProxyOptions`API: er har lagts till i `GatewayConnectionConfig` . 
* SDK har uppdaterats för att använda `Instant` typ i stället för `OffsetDateTime` . 
* Ny Enum-typ har lagts till `OperationKind` . 
* Har bytt namn `FeedOptions` till `QueryRequestOptions` . 
* `getETag()`  &  `getTimestamp()` API: er har lagts till i `Cosmos*Properties` typerna. 
* Information har lagts till `userAgent` i `CosmosException`  &  `CosmosDiagnostics` . 
* Ett nytt rad avstånd har uppdaterats `Diagnostics` till rad i systemet. 
* `readAll*`API: er har tagits bort, Använd fråga Markera alla API: er i stället.
* `ChangeFeedProcessor`API för uppskattnings fördröjning har lagts till.   
* Har lagt till stöd för autoskalning/autopilot-genomflöde i SDK.  
* Ersatt `ConnectionPolicy` med nya anslutnings konfigurationer. Exponerade `DirectConnectionConfig`  &  `GatewayConnectionConfig` API: er via `CosmosClientBuilder` för direktanslutna & Gateway mode-anslutnings konfigurationer.
* Flyttad `JsonSerializable`  &  `Resource` till implementerings paketet. 
* API har lagts `contentResponseOnWriteEnabled` till för CosmosClientBuilder, vilket inaktiverar fullständigt svars innehåll vid Skriv åtgärder.
* Exponerade `getETag()` API: er för svars typer.
* Flyttad `CosmosAuthorizationTokenResolver` till implementering. 
* API har bytt namn `preferredLocations`  &  `multipleWriteLocations` till `preferredRegions`  &  `multipleWriteRegions` . 
* Uppdaterat `reactor-core` till 3.3.5. release, `reactor-netty` till 0.9.7. release & `netty` till 4.1.49. Finale-versioner. 
* Stöd har lagts till `analyticalStoreTimeToLive` i SDK.     
* `CosmosClientException` utökar `AzureException` . 
* API: er har tagits bort `maxItemCount`  &  `requestContinuationToken` från `FeedOptions` i stället med `byPage()` API: er `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Infört `CosmosPermissionProperties` på offentlig yta för `Permission` API: er.
* Den borttagna `SqlParameterList` typen & ersatts av `List`
* Fast flera minnes läckor i direkt TCP-klient. 
* Stöd har lagts till för `DISTINCT` frågor. 
* Externa beroenden har tagits bort `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Flyttad `CosmosPagedFlux`  &  `CosmosPagedIterable` till `utils` paket. 
* Uppdaterad nettning till 4.1.45. slutlig & projekt reaktor till 3.3.3 version.
* Uppdaterade offentliga rest-kontrakt till `Final` klasser.
* Stöd har lagts till för avancerad diagnostik för punkt åtgärder.
* Paketet har uppdaterats till `com.azure.cosmos`
* `models`Paket för modell/rest-avtal har lagts till
* Paket har lagts till `utils` för `CosmosPagedFlux`  &  `CosmosPagedIterable` typer. 
* Uppdaterade offentliga API: er som ska användas i `Duration` SDK.
* Alla rest-kontrakt har lagts till i `models` paketet.
* `RetryOptions` har bytt namn till `ThrottlingRetryOptions` .
* `CosmosPagedFlux`  &  `CosmosPagedIterable` Sid brytnings typer har lagts till för API: er för frågor. 
* Stöd har lagts till för delning av TransportClient över flera instanser av CosmosClients med hjälp av ett nytt API i `CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Optimering av frågor genom att ta bort dubbel serialisering/deserialisering. 
* Optimeringar av svars rubriker genom att ta bort onödig kopiering och tillbaka. 
* Optimerad `ByteBuffer` serialisering/deserialisering genom att ta bort mellanliggande strängars instansiering.

#### <a name="key-bug-fixes"></a>Viktiga fel korrigeringar
* Undantag för fast ConnectionPolicy `toString()` null-pekare.
* Fast problem vid parsning av frågeresultat i händelse av frågor om värde ordning. 
* Problem med fast socket läcker med direkt TCP-klient.
* Åtgärdat `orderByQuery` med fel i fortsättnings-token.
* `ChangeFeedProcessor` fel korrigering för hantering av partition delas & när partitionen inte hittas.
* `ChangeFeedProcessor` fel korrigering vid synkronisering av låne uppdateringar mellan olika trådar.
* Fast konkurrens tillstånd `ArrayIndexOutOfBound` som orsakar undantag i StoreReader

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.