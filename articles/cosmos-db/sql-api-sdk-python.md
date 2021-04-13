---
title: Azure Cosmos DB SQL Python API, SDK & resurser
description: Lär dig allt om SQL Python API och SDK, inklusive lanseringsdatum, tillbakagångar och ändringar som görs mellan varje version av Azure Cosmos DB Python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 04/06/2021
ms.author: rosouz
ms.custom: devx-track-python
ms.openlocfilehash: ff551c5d677029f39d9a3db3a64f3d03e2c57eba
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366043"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK för SQL-API: Viktig information och resurser
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Asynkron Java-SDK v2](sql-api-sdk-async-java.md)
> * [Synkron Java-SDK v2](sql-api-sdk-java.md)
> * [Spring-data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring-data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP-anslutning](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Mass utförare – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

| Sida| Länk |
|---|---|
|**Ladda ned SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentation**|[Referensdokumentation för Python API](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos?view=azure-python&preserve-view=true)|
|**Installationsanvisningar för SDK**|[Installationsanvisningar för Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Komma igång**|[Kom igång med Python SDK](create-sql-api-python.md)|
|**Aktuell plattform som stöds**|[Python 2.7](https://www.python.org/downloads/) och [Python 3.6+](https://www.python.org/downloads/)|

## <a name="release-history"></a>Lanseringshistorik

## <a name="420"></a>4.2.0

**Felkorrigeringar**
- Bugg där fortsättningstoken inte respekteras när query_iterable används för att hämta resultat per sida har åtgärdats.
- En bugg där resurstoken inte respekteras för dokumentläsningar och borttagningar har åtgärdats. 

**Nya funktioner**
- Stöd har lagts till för `partitionKey` att skicka frågor till Change-Feed.

## <a name="410"></a>4.1.0

- Utfasningsvarning för "uppskjutet" indexeringsläge har lagts till. Backend tillåter inte längre skapande av containrar med det här läget och anger dem till konsekventa i stället.

**Nya funktioner**
- Lade till möjligheten att ange TTL för analyslagring när du skapar en ny container.

**Felkorrigeringar**
- Stöd för som `dicts` indata för get_client-API:er har åtgärdats.
- Python 2/3-kompatibilitet har åtgärdats i fråge-iteratorer.
- Typtipsfel har åtgärdats.
- En bugg har åtgärdats där alternativrubriker inte lades till upsert_item funktionen. 
- Ett fel som utlöses när ett icke-sträng-ID används i ett objekt har åtgärdats. Nu höjs TypeError i stället för AttributeError.


## <a name="400"></a>4.0.0

* Stabil version.
* HttpLoggingPolicy har lagts till i pipelinen för att göra det möjligt att skicka in en anpassad loggare för begärande- och svarshuvuden.

### <a name="400b6"></a>4.0.0b6

* En bugg i synchronized_request för medie-API:er har åtgärdats.
* MediaReadMode och MediaRequestTimeout har tagits bort från ConnectionPolicy eftersom mediebegäranden inte stöds.

### <a name="400b5"></a>4.0.0b5

* modulen azure.cosmos.errors är inaktuell och ersätts av azure.cosmos.exceptions
* Parametrarna för `access_condition` åtkomstvillkor ( , , ) har gjorts `if_match` `if_none_match` inaktuella och ersatts med separata `match_condition` parametrar och `etag` .
* En bugg i routningsmappningsprovidern har åtgärdats.
* Stöd för distinkta frågor, förskjutningar och gränser har lagts till.
* Standarddokumentfrågekörningskontext används nu för

  * Frågor om ändringsflöde
  * enpartitionsfrågor ( `partitionkey` , `partitionKeyRangeId` finns i alternativen)
  * Icke-dokumentfrågor

* Fel för aggregeringar på flera partitioner, med aktivera korspartitionsfråga inställt på sant, men inget nyckelord för "värde" finns
* Träffar frågeplansslutpunkten för andra scenarier för att hämta frågeplan
* Stöd `__repr__` har lagts till för Cosmos-entitetsobjekt.
* Uppdaterad dokumentation.

### <a name="400b4"></a>4.0.0b4

* Stöd har lagts till för ett nyckelordsargument för alla åtgärder för att ange en absolut tidsgräns i `timeout` sekunder som åtgärden måste slutföras inom. Om tidsgränsvärdet överskrids `azure.cosmos.errors.CosmosClientTimeoutError` utlöses ett .

* En ny har lagts `ConnectionRetryPolicy` till för att hantera återförsöksbeteende vid HTTP-anslutningsfel.

* Nya nyckelordsargument för konstruktor och konfiguration per åtgärd har lagts till:

  * `retry_total` – Maximalt antal återförsök.
  * `retry_backoff_max` – Maximal väntetid för återförsök i sekunder.
  * `retry_fixed_interval` – Ett återförsöksintervall i millisekunder har åtgärdats.
  * `retry_read` – Maximalt antal lästa återförsök för socketar.
  * `retry_connect` – Maximalt antal anslutningsfel vid återförsök.
  * `retry_status` – Maximalt antal återförsök för felstatuskoder.
  * `retry_on_status_codes` – En lista över specifika statuskoder att försöka igen med.
  * `retry_backoff_factor` – Faktor för att beräkna väntetiden mellan återförsök.

### <a name="400b3"></a>4.0.0b3

* Funktionerna `create_database_if_not_exists()` `create_container_if_not_exists` och har lagts till i CosmosClient respektive Database.

### <a name="400b2"></a>4.0.0b2

* Version 4.0.0b2 är den andra iterationen i vårt arbete med att skapa ett klientbibliotek som passar metodtipsen för Python-språket.

**Icke-bakåtkompatibla ändringar**

* Klientanslutningen har anpassats för att använda HTTP-pipelinen som definierats i `azure.core.pipeline` .

* Interaktiva objekt har nu bytt namn till proxy. Du måste bland annat:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Konstruktorn `CosmosClient` för har uppdaterats:

  * Parametern `auth` har bytt namn till och tar nu en `credential` autentiseringstyp direkt. Det innebär att det primära nyckelvärdet, en ordlista med resurstoken eller en lista över behörigheter kan skickas in. Det gamla ordlisteformatet stöds dock fortfarande.

  * Parametern har gjorts till en parameter med endast nyckelord, och även om den fortfarande stöds kan vart och ett av de enskilda attributen för principen nu skickas `connection_policy` som explicita nyckelordsargument:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* En ny konstruktor har lagts till i `CosmosClient` för att skapa via en anslutningssträng som hämtats från Azure Portal.

* Vissa `read_all` åtgärder har bytt namn till `list` åtgärder:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Alla åtgärder som tar `request_options` eller `feed_options` parametrar har flyttats till nyckelordsparametrar. Dessutom stöds dessa alternativordlistor fortfarande, men alla enskilda alternativ i ordlistan stöds nu som explicita nyckelordsargument.

* Felhierarkin ärvs nu från `azure.core.AzureError` :

  * `HTTPFailure` har bytt namn till `CosmosHttpResponseError`
  * `JSONParseFailure` har tagits bort och ersatts av `azure.core.DecodeError`
  * Ytterligare fel har lagts till för specifika svarskoder:
    * `CosmosResourceNotFoundError` för status 404
    * `CosmosResourceExistsError` för status 409
    * `CosmosAccessConditionFailedError` för status 412

* `CosmosClient` kan nu köras i en kontexthanterare för att hantera stängning av klientanslutningen.

* Iterbara svar (till exempel frågesvar och listsvar) är nu av typen `azure.core.paging.ItemPaged` . Metoden har `fetch_next_block` ersatts av en sekundär iterator som används av `by_page` metoden .

### <a name="400b1"></a>4.0.0b1

Version 4.0.0b1 är den första förhandsversionen av vårt arbete med att skapa ett användarvänligt klientbibliotek som passar de bästa metoderna för Python-språk. Mer information om detta och förhandsversioner av andra Azure SDK-bibliotek finns på https://aka.ms/azure-sdk-preview1-python .

**Större ändringar: Ny API-design**

* Åtgärder är nu begränsade till en viss klient:

  * `CosmosClient`: Den här klienten hanterar åtgärder på kontonivå. Detta omfattar hantering av tjänstegenskaper och en lista över databaserna i ett konto.
  * `Database`: Den här klienten hanterar åtgärder på databasnivå. Detta omfattar att skapa och ta bort containrar, användare och lagrade procedurer. Den kan nås från en cosmosClient-instans efter namn.
  * `Container`: Den här klienten hanterar åtgärder för en viss container. Detta inkluderar frågor och infogning av objekt och hantering av egenskaper.
  * `User`: Den här klienten hanterar åtgärder för en viss användare. Detta inkluderar att lägga till och ta bort behörigheter och hantera användaregenskaper.

    Dessa klienter kan nås genom att navigera nedåt i klienthierarkin med hjälp av `get_<child>_client` metoden . Fullständig information om det nya API:et finns i [referensdokumentationen](https://aka.ms/azsdk-python-cosmos-ref).

* Klienterna nås via namn i stället för per ID. Du behöver inte sammanfoga strängar för att skapa länkar.

* Du behöver inte längre importera typer och metoder från enskilda moduler. Det offentliga API-ytan är tillgänglig direkt i `azure.cosmos` paketet.

* Egenskaper för enskilda förfrågningar kan anges som nyckelordsargument i stället för att skapa en separat `RequestOptions` instans.

### <a name="302"></a>3.0.2

* Stöd har lagts till för MultiPolygon-datatyp
* Felkorrigering i återförsöksprincip för sessionsläsning
* Felkorrigering för felaktiga utfyllnadsproblem vid avkodning av base 64-strängar

### <a name="301"></a>3.0.1

* Felkorrigering i LocationCache
* Felkorrigering av logik för slutpunktsförsök
* Dokumentation har åtgärdats

### <a name="300"></a>3.0.0

* Skrivstöd för flera regioner har lagts till
* Namnge ändringar
  * DocumentClient till CosmosClient
  * Samling till container
  * Dokument till objekt
  * Paketnamnet har uppdaterats till "azure-cosmos"
  * Namnområdet har uppdaterats till "azure.cosmos"

### <a name="233"></a>2.3.3

* Stöd för proxy har lagts till
* Stöd har lagts till för läsning av ändringsflöde
* Stöd har lagts till för samlingskvotrubriker
* Felkorrigering för problem med stora sessionstoken
* Felkorrigering för ReadMedia API
* Felkorrigering i cacheminnet för partitionsnyckelintervall

### <a name="232"></a>2.3.2

* Stöd har lagts till för standardförsök vid anslutningsproblem.

### <a name="231"></a>2.3.1

* Uppdaterad dokumentation för att referera Azure Cosmos DB i stället för Azure DocumentDB.

### <a name="230"></a>2.3.0

* Den här SDK-versionen kräver den senaste versionen Azure Cosmos DB emulatorn som är tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* bugfix för aggregerat dict
* bugfix för att trimma snedstreck i resurslänken
* tester för Unicode-kodning

### <a name="220"></a>2.2.0

* Stöd har lagts till för funktionen Enheter för begäran per minut (RU/m).
* Stöd har lagts till för en ny konsekvensnivå som kallas ConsistentPrefix.

### <a name="210"></a>2.1.0

* Stöd har lagts till för sammansättningsfrågor (COUNT, MIN, MAX, SUM och AVG).
* Lade till ett alternativ för att inaktivera SSL-verifiering vid körning mot DocumentDB-emulatorn.
* Begränsningen för modulen beroende begäranden har tagits bort så att den är exakt 2.10.0.
* Lägre minsta dataflöde för partitionerade samlingar från 10 100 RU/s till 2 500 RU/s.
* Stöd har lagts till för att aktivera skriptloggning under körning av lagrade procedurer.
* REST API versionen har 2017-01-19 med den här versionen.

### <a name="201"></a>2.0.1

* Gjorde redigeringsändringar i dokumentationskommentarer.

### <a name="200"></a>2.0.0

* Stöd för Python 3.5 har lagts till.
* Stöd har lagts till för anslutningspooler med hjälp av begärandemodulen.
* Stöd för sessionskonsekvens har lagts till.
* Stöd har lagts till för TOP/ORDERBY-frågor för partitionerade samlingar.

### <a name="190"></a>1.9.0

* Stöd för återförsöksprincip för begränsade begäranden har lagts till. (Begränsade begäranden får ett undantag med för hög begärandefrekvens, felkod 429.) Som standard försöker DocumentDB igen nio gånger för varje begäran när felkoden 429 påträffas och respekterar återförsöktiden i svarshuvudet.
  En fast tid för återförsöksintervall kan nu anges som en del av retryOptions-egenskapen för ConnectionPolicy-objektet om du vill ignorera retryAfter-tiden som returneras av servern mellan återförsöken.
  DocumentDB väntar nu i högst 30 sekunder för varje begäran som begränsas (oavsett antal återförsök) och returnerar svaret med felkoden 429.
  Den här gången kan också åsidosättas i egenskapen RetryOptions på ConnectionPolicy-objektet.

* DocumentDB returnerar nu x-ms-throttle-retry-count och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran för att ange antalet omförsök för begränsning och den kumulativa tid då begäran väntade mellan återförsöken.

* Tog bort klassen RetryPolicy och motsvarande egenskap (retry_policy) som exponeras för klassen document_client och introducerade i stället klassen RetryOptions som exponerar egenskapen RetryOptions för klassen ConnectionPolicy som kan användas för att åsidosätta några av standardalternativen för återförsök.

### <a name="180"></a>1.8.0

* Stöd för geo-replikerade databaskonton har lagts till.
* Testkorrigeringar för att flytta den globala värden och masterKey till de enskilda testklasserna.

### <a name="170"></a>1.7.0

* Stöd för TTL-funktionen (Time To Live) för dokument har lagts till.

### <a name="161"></a>1.6.1

* Felkorrigeringar som rör partitionering på serversidan för att tillåta specialtecken i partitionsnyckelsökvägen.

### <a name="160"></a>1.6.0

* Stöd har lagts till för funktionen partitionerade samlingar på serversidan.

### <a name="150"></a>1.5.0

* Ramverket för horisontell partitionering på klientsidan har lagts till i SDK:n. Implementerat klasserna HashPartionResolver och RangePartitionResolver.

### <a name="142"></a>1.4.2

* Implementera Upsert. Nya UpsertXXX-metoder har lagts till för att stödja Upsert-funktionen.
* Implementera ID-Based routning. Inga offentliga API-ändringar, alla ändringar sker internt.

### <a name="130"></a>1.3.0

* Versionen hoppas över för att anpassa versionsnumret till andra SDK:er

### <a name="120"></a>1.2.0

* Stöder geospatialt index.
* Validerar ID-egenskapen för alla resurser. ID:n för resurser får `?, /, #, \\` inte innehålla tecken eller sluta med blanksteg.
* Lägger till det nya huvudet "förlopp för indextransformering" i ResourceResponse.

### <a name="110"></a>1.1.0

* Implementerar V2-indexeringsprincip

### <a name="101"></a>1.0.1

* Stöder proxyanslutning

## <a name="release--retirement-dates"></a>Frisläppningsdatum & tillbaka

Microsoft tillhandahåller meddelanden minst **12** månader innan ett SDK dras tillbaka för att underlätta övergången till en nyare version som stöds. Nya funktioner, verktyg och optimeringar läggs endast till i den aktuella SDK-versionen. Därför rekommenderar vi att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt.

> [!WARNING]
> Efter den 31 augusti 2022 kommer Azure Cosmos DB inte längre att göra felkorrigeringar eller ge stöd för version 1.x och 2.x av Azure Cosmos DB Python SDK för SQL API. Om du föredrar att inte uppgradera fortsätter begäranden som skickas från version 1.x och 2.x av SDK:n att betjänas av Azure Cosmos DB tjänsten.

| Version | Utgivningsdatum | Förfallodatum |
| --- | --- | --- |
| [4.2.0](#420) |Den 9 oktober 2020 |--- |
| [4.1.0](#410) |10 augusti 2020 |--- |
| [4.0.0](#400) |Den 20 maj 2020 |--- |
| [3.0.2](#302) |15 november 2018 |--- |
| [3.0.1](#301) |Den 4 oktober 2018 |--- |
| [2.3.3](#233) |September 08, 2018 |Den 31 augusti 2022 |
| [2.3.2](#232) |Den 8 maj 2018 |Den 31 augusti 2022 |
| [2.3.1](#231) |Den 21 december 2017 |Den 31 augusti 2022 |
| [2.3.0](#230) |Den 10 november 2017 |Den 31 augusti 2022 |
| [2.2.1](#221) |29 sep 2017 |Den 31 augusti 2022 |
| [2.2.0](#220) |10 maj 2017 |Den 31 augusti 2022 |
| [2.1.0](#210) |Den 1 maj 2017 |Den 31 augusti 2022 |
| [2.0.1](#201) |Den 30 oktober 2016 |Den 31 augusti 2022 |
| [2.0.0](#200) |Den 29 september 2016 |Den 31 augusti 2022 |
| [1.9.0](#190) |Den 7 juli 2016 |Den 31 augusti 2022 |
| [1.8.0](#180) |Den 14 juni 2016 |Den 31 augusti 2022 |
| [1.7.0](#170) |26 april 2016 |Den 31 augusti 2022 |
| [1.6.1](#161) |Den 8 april 2016 |Den 31 augusti 2022 |
| [1.6.0](#160) |Den 29 mars 2016 |Den 31 augusti 2022 |
| [1.5.0](#150) |Den 3 januari 2016 |Den 31 augusti 2022 |
| [1.4.2](#142) |Den 6 oktober 2015 |Den 31 augusti 2022 |
| 1.4.1 |Den 6 oktober 2015 |Den 31 augusti 2022 |
| [1.2.0](#120) |Den 6 augusti 2015 |Den 31 augusti 2022 |
| [1.1.0](#110) |Den 9 juli 2015 |Den 31 augusti 2022 |
| [1.0.1](#101) |Den 25 maj 2015 |Den 31 augusti 2022 |
| 1.0.0 |Den 7 april 2015 |Den 31 augusti 2022 |
| 0.9.4-prelease |Den 14 januari 2015 |Den 29 februari 2016 |
| 0.9.3-prelease |Den 9 december 2014 |Den 29 februari 2016 |
| 0.9.2-prelease |Den 25 november 2014 |Den 29 februari 2016 |
| 0.9.1-prelease |Den 23 september 2014 |Den 29 februari 2016 |
| 0.9.0-prelease |Den 21 augusti 2014 |Den 29 februari 2016 |

## <a name="faq"></a>Vanliga frågor

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Cosmos DB finns på sidan om tjänsten [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
