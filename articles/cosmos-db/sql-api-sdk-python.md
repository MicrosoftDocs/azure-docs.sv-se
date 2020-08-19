---
title: Azure Cosmos DB SQL python API, SDK & resurser
description: Lär dig allt om SQL python API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB python SDK.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: e9f9daea2c0d570efb81603784ee730b11668426
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585992"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB python SDK för SQL API: viktig information och resurser

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

| |  |
|---|---|
|**Hämta SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentation**|[Dokumentation om python API-referens](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instruktioner för SDK-installation**|[Installations anvisningar för python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**Komma igång**|[Kom igång med python SDK](create-sql-api-python.md)|
|**Aktuell plattform som stöds**|[Python 2,7](https://www.python.org/downloads/) och [python 3.5.3 +](https://www.python.org/downloads/)|

## <a name="release-history"></a>Versions historik

### <a name="410-2020-08-10"></a>4.1.0 (2020-08-10)

- Varning om utfasning har lagts till för "Lazy" Indexing-läge. Server delen tillåter inte längre att skapa behållare med det här läget och anger att de ska vara konsekventa i stället.

**Nya funktioner**
- Har lagt till möjligheten att ange TTL för analys lagring när du skapar en ny behållare.

**Fel korrigeringar**
- Fast stöd för förutsägelser som indata för get_client-API: er.
- Fast python 2/3-kompatibilitet i frågemeddelanden.
- Fel i fast typ tips (problem #12570).
- Ett fel har åtgärd ATS där alternativ rubriker inte lades till i upsert_item funktionen. Problem #11791 – tack @aalapatirvbd .
- Ett fast fel uppstod när ett icke-sträng-ID används i ett objekt. Den genererar nu TypeError snarare än AttributeError (utfärdande #11793).

### <a name="400"></a>4.0.0

* Stabil utgåva.
* Lade till HttpLoggingPolicy i pipeline för att aktivera överföring i en anpassad logg för begäran och svars rubriker.

### <a name="400b6"></a>4.0.0 B6

* Fast bugg i synchronized_request för media-API: er.
* Det finns inte stöd för att ta bort MediaReadMode och MediaRequestTimeout från ConnectionPolicy eftersom medie förfrågningar.

### <a name="400b5"></a>4.0.0 B5

* modulen Azure. Cosmos. errors inaktuell och ersatts av Azure. Cosmos. Exceptions
* Parametrarna för åtkomst villkor ( `access_condition` , `if_match` , `if_none_match` ) har ersatts av separata `match_condition` och `etag` parametrar.
* Fast bugg i routing Map-providern.
* Stöd har lagts till för frågor distinkt, förskjutning och gräns.
* Standard kontext för körning av dokument fråga används nu för

  * ChangeFeed-frågor
  * frågor för enkla partitioner (partitionkey, partitionKeyRangeId finns i alternativ)
  * Frågor som inte är dokument

* Fel för mängder på flera partitioner, med aktivera Cross partition-fråga inställd på True, men inget "värde"-nyckelord finns
* Träffar efter fråga plan-slutpunkt för andra scenarier för hämtning av frågeplan
* Stöd har lagts till `__repr__` för Cosmos entitet-objekt.
* Uppdaterad dokumentation.

### <a name="400b4"></a>4.0.0 B4

* Stöd har lagts till för ett `timeout` nyckelords argument för alla åtgärder för att ange en absolut tids gräns i sekunder inom vilken åtgärden måste utföras. Om timeout-värdet överskrids höjs en `azure.cosmos.errors.CosmosClientTimeoutError` .

* Lade till ett nytt `ConnectionRetryPolicy` för att hantera omprövnings beteende vid HTTP-anslutningsfel.

* Nya argument för konfigurations nyckelord för konstruktor och per åtgärd har lagts till:

  * `retry_total` – Högsta antal nya försök.
  * `retry_backoff_max` -Högsta vänte tid för återförsök i sekunder.
  * `retry_fixed_interval` – Fast återförsöksintervall i millisekunder.
  * `retry_read` -Maximalt antal Sockets-försök för läsning.
  * `retry_connect` -Maximalt antal nya försök för anslutnings fel.
  * `retry_status` -Maximalt antal nya försök vid fel status koder.
  * `retry_on_status_codes` – En lista med vissa status koder att försöka igen.
  * `retry_backoff_factor` – Faktor för att beräkna vänte tiden mellan återförsök.

### <a name="400b3"></a>4.0.0 B3

* Tillagda `create_database_if_not_exists()` och `create_container_if_not_exists` funktionella till CosmosClient respektive databas.

### <a name="400b2"></a>4.0.0 B2

* Version 4.0.0 B2 är den andra iterationen i våra ansträngningar för att bygga ett klient bibliotek som passar Best Practices för python-språk.

**Bryta ändringar**

* Klient anslutningen har anpassats till att använda HTTP-pipeline som definierats i `azure.core.pipeline` .

* Interaktiva objekt har nu bytt namn till proxyservrar. Det här omfattar:

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* Konstruktorn för `CosmosClient` har uppdaterats:

  * `auth`Parametern har bytt namn till `credential` och kommer nu att ta en autentiseringstyp direkt. Det innebär att huvud nyckelns värde, en ord lista med resurs-token eller en lista över behörigheter kan skickas. Det gamla ordboks formatet stöds dock fortfarande.

  * `connection_policy`Parametern har endast en parameter för nyckelord, och trots att den fortfarande stöds kan var och en av de enskilda attributen i principen skickas in som uttryckliga nyckelords argument:

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* En ny konstruktor har lagts till för `CosmosClient` att aktivera skapande via en anslutnings sträng som hämtats från Azure Portal.

* Vissa `read_all` åtgärder har bytt namn till `list` åtgärder:

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* Alla åtgärder som tar `request_options` eller `feed_options` parametrar har flyttats till parametrar med enbart nyckelord. Även om de här alternativ lexikonen fortfarande stöds, stöds nu var och en av de enskilda alternativen i ord listan som explicita nyckelords argument.

* Felhierarkin har nu ärvts från `azure.core.AzureError` :

  * `HTTPFailure` har bytt namn till `CosmosHttpResponseError`
  * `JSONParseFailure` har tagits bort och ersatts av `azure.core.DecodeError`
  * Ytterligare fel har lagts till för vissa svars koder:
    * `CosmosResourceNotFoundError` för status 404
    * `CosmosResourceExistsError` för status 409
    * `CosmosAccessConditionFailedError` för status 412

* `CosmosClient` kan nu köras i en kontext hanterare för att hantera stängning av klient anslutningen.

* Iterable-svar (till exempel fråge svar och list svar) är nu av typen `azure.core.paging.ItemPaged` . Metoden `fetch_next_block` har ersatts av en sekundär iterator, som används av `by_page` metoden.

### <a name="400b1"></a>4.0.0 B1

Version 4.0.0 B1 är den första förhands granskningen av våra ansträngningar för att skapa ett användarvänligt klient bibliotek som passar Best Practices för python-språk. Mer information om detta och för hands versioner av andra Azure SDK-bibliotek finns på https://aka.ms/azure-sdk-preview1-python .

**Bryta ändringar: ny API-design**

* Åtgärder är nu begränsade till en viss klient:

  * `CosmosClient`: Den här klienten hanterar åtgärder på konto nivå. Detta omfattar hantering av tjänst egenskaper och lista över databaser i ett konto.
  * `Database`: Den här klienten hanterar åtgärder på databas nivå. Detta omfattar att skapa och ta bort behållare, användare och lagrade procedurer. Den kan nås från en cosmosClient-instans efter namn.
  * `Container`: Den här klienten hanterar åtgärder för en viss behållare. Detta inkluderar frågor och infogning av objekt och hantering av egenskaper.
  * `User`: Den här klienten hanterar åtgärder för en viss användare. Detta innefattar att lägga till och ta bort behörigheter och hantera användar egenskaper.

    Dessa klienter kan nås genom att navigera nedåt i klientsessioner med hjälp av `get_<child>_client` metoden. Fullständig information om det nya API: et finns i [referens dokumentationen](https://aka.ms/azsdk-python-cosmos-ref).

* Klienternas åtkomst till namn i stället för med ID. Du behöver inte sammanfoga strängar för att skapa länkar.

* Du behöver inte importera typer och metoder från enskilda moduler. Området för den offentliga API-ytan är tillgängligt direkt i `azure.cosmos` paketet.

* Egenskaper för enskilda begär Anden kan anges som nyckelords argument i stället för att skapa en separat `RequestOptions` instans.

### <a name="302"></a>3.0.2

* Stöd har lagts till för data typen multipolygon
* Fel korrigering i arbets princip för att försöka läsa
* Fel korrigering för felaktiga utfyllnads problem vid avkodning av bas 64-strängar

### <a name="301"></a>3.0.1

* Fel korrigering i LocationCache
* Bugg åtgärds fel vid slut punkts omprövning
* Fast dokumentation

### <a name="300"></a>3.0.0

* Skriv stöd för flera regioner har lagts till
* Namn ändringar
  * DocumentClient till CosmosClient
  * Samling till container
  * Dokument till objekt
  * Paket namnet har uppdaterats till "Azure-Cosmos"
  * Namn området har uppdaterats till "Azure. Cosmos"

### <a name="233"></a>2.3.3

* Stöd har lagts till för proxy
* Stöd för läsning av ändrings flöde har lagts till
* Stöd har lagts till för samlings kvot rubriker
* Problem med Bugfix för stora sessioner
* Bugfix för ReadMedia-API
* Cache för Bugfix i partitionsnyckel

### <a name="232"></a>2.3.2

* Stöd har lagts till för standard återförsök vid anslutnings problem.

### <a name="231"></a>2.3.1

* Uppdaterad dokumentation till referens Azure Cosmos DB i stället för Azure-DocumentDB.

### <a name="230"></a>2.3.0

* Den här SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn kan laddas ned från https://aka.ms/cosmosdb-emulator .

### <a name="221"></a>2.2.1

* Bugfix för mängd dict
* Bugfix för att trimma snedstreck i resurs länken
* test för Unicode-kodning

### <a name="220"></a>2.2.0

* Stöd har lagts till för Request Unit per minut (RU/m)-funktionen.
* Stöd har lagts till för en ny konsekvens nivå som kallas Konsekvensprincip.

### <a name="210"></a>2.1.0

* Stöd har lagts till för agg regerings frågor (antal, MIN, MAX, SUM och AVG).
* Ett alternativ har lagts till för att inaktivera SSL-verifiering när den körs mot DocumentDB-emulatorn.
* Tog bort begränsningen för modulen för beroende begär Anden så att den är exakt 2.10.0.
* Sänkt minsta data flöde på partitionerade samlingar från 10 100 RU/s till 2500 RU/s.
* Stöd har lagts till för att aktivera skript loggning under körning av lagrad procedur.
* REST API-versionen har blivit 2017-01-19 med den här versionen.

### <a name="201"></a>2.0.1

* Gjort redaktionella ändringar i dokumentations kommentarer.

### <a name="200"></a>2.0.0

* Stöd har lagts till för python 3,5.
* Stöd har lagts till för anslutningspoolen med modulen begär Anden.
* Stöd har lagts till för konsekvens av sessioner.
* Stöd har lagts till för TOP-/ORDERBY-frågor för partitionerade samlingar.

### <a name="190"></a>1.9.0

* Princip stöd för återförsök har lagts till för begränsade begär Anden. (Begränsade begär Anden tar emot en begäran om för stort undantag, felkod 429.) Som standard försöker DocumentDB nio gånger för varje begäran när felkod 429 påträffas, vilket följer retryAfter-tiden i svars huvudet.
  Du kan nu ange ett intervall med fasta återförsöksintervall som en del av egenskapen RetryOptions i ConnectionPolicy-objektet om du vill ignorera retryAfter-tiden som returnerades av servern mellan Återförsöken.
  DocumentDB väntar nu i högst 30 sekunder för varje begäran som begränsas (oavsett antal försök) och returnerar svaret med felkoden 429.
  Den här tiden kan också åsidosättas i egenskapen RetryOptions på ConnectionPolicy-objektet.

* DocumentDB returnerar nu x-MS-begränsning-försök-antal och x-MS-begränsning-försök-wait-Time-MS som svars rubriker i varje begäran om att ange begränsningen för antal återförsök och den ackumulerade tiden som begäran väntar mellan Återförsöken.

* RetryPolicy-klassen och motsvarande egenskap (retry_policy) har tagits bort från klassen document_client. i stället introducerades en RetryOptions-klass som visar RetryOptions-egenskapen i ConnectionPolicy-klassen som kan användas för att åsidosätta några av standard alternativen för återförsök.

### <a name="180"></a>1.8.0

* Stöd har lagts till för geo-replikerade databas konton.
* Testa korrigeringarna för att flytta den globala värden och masterKey till de enskilda test klasserna.

### <a name="170"></a>1.7.0

* Har lagt till stöd för TTL-funktionen (Time to Live) för dokument.

### <a name="161"></a>1.6.1

* Fel korrigeringar relaterade till partitionering på Server sidan för att tillåta specialtecken i partitionsnyckel Sök vägs nyckel.

### <a name="160"></a>1.6.0

* Stöd för funktionen partitionerade samlingar för Server sidan har lagts till.

### <a name="150"></a>1.5.0

* Horisontell partitionering-ramverket för klient sidan har lagts till i SDK. Implementerade HashPartionResolver-och RangePartitionResolver-klasser.

### <a name="142"></a>1.4.2

* Implementera upsert. Nya UpsertXXX-metoder har lagts till som stöd för upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar internt.

### <a name="130"></a>1.3.0

* Versionen hoppades över för att placera versions numret i justering med andra SDK: er

### <a name="120"></a>1.2.0

* Har stöd för GeoSpatialt index.
* Verifierar ID-egenskapen för alla resurser. ID: n för resurser får inte innehålla `?, /, #, \\` tecken eller avslutas med blank steg.
* Lägger till det nya huvudet "indexera omvandlings förlopp" i ResourceResponse.

### <a name="110"></a>1.1.0

* Implementerar v2-indexerings princip

### <a name="101"></a>1.0.1

* Stöder proxyanslutningar

## <a name="release--retirement-dates"></a>Frisläpp & indragnings datum

Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds. Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK: n, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

> [!WARNING]
> Efter 31 augusti 2022 kommer Azure Cosmos DB inte längre att göra fel korrigeringar, lägga till nya funktioner och ge stöd till version 1. x eller 2. x av Azure Cosmos DB python SDK för SQL API. Om du föredrar att inte uppgradera fortsätter de begär Anden som skickas från version 1. x och 2. x av SDK att hanteras av Azure Cosmos DBs tjänsten.

| Version | Lanserings datum | Förfallodatum |
| --- | --- | --- |
| [4.0.0](#400) |20 maj, 2020 |--- |
| [3.0.2](#302) |Den 15 november 2018 |--- |
| [3.0.1](#301) |Okt 04, 2018 |--- |
| [2.3.3](#233) |Sept 08, 2018 |30 augusti 2020 |
| [2.3.2](#232) |08 maj, 2018 |30 augusti 2020 |
| [2.3.1](#231) |21 december 2017 |30 augusti 2020 |
| [2.3.0](#230) |10 november 2017 |30 augusti 2020 |
| [2.2.1](#221) |Sep 29, 2017 |30 augusti 2020 |
| [2.2.0](#220) |10 maj 2017 |30 augusti 2020 |
| [2.1.0](#210) |01 maj, 2017 |30 augusti 2020 |
| [2.0.1](#201) |30 oktober 2016 |30 augusti 2020 |
| [2.0.0](#200) |29 september 2016 |30 augusti 2020 |
| [1.9.0](#190) |Den 07 juli 2016 |30 augusti 2020 |
| [1.8.0](#180) |14 juni 2016 |30 augusti 2020 |
| [1.7.0](#170) |26 april 2016 |30 augusti 2020 |
| [1.6.1](#161) |08, 2016 |30 augusti 2020 |
| [1.6.0](#160) |29 mars 2016 |30 augusti 2020 |
| [1.5.0](#150) |03 januari 2016 |30 augusti 2020 |
| [1.4.2](#142) |06 oktober 2015 |30 augusti 2020 |
| 1.4.1 |06 oktober 2015 |30 augusti 2020 |
| [1.2.0](#120) |06 augusti 2015 |30 augusti 2020 |
| [1.1.0](#110) |9 juli 2015 |30 augusti 2020 |
| [1.0.1](#101) |25 maj 2015 |30 augusti 2020 |
| 1.0.0 |07, 2015 |30 augusti 2020 |
| 0.9.4 – för-leasing |14 januari 2015 |29 februari 2016 |
| 0.9.3 – för-leasing |Den 9 december 2014 |29 februari 2016 |
| 0.9.2 – för-leasing |25 november 2014 |29 februari 2016 |
| 0.9.1 till och – för-leasing |23 september 2014 |29 februari 2016 |
| 0.9.0 – för-leasing |21 augusti 2014 |29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan. 
