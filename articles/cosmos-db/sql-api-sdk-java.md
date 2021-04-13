---
title: 'Azure Cosmos DB: SQL Java API, SDK & resurser'
description: Lär dig allt om SQL Java API och SDK, inklusive lanseringsdatum, tillbakagångar och ändringar som görs mellan varje version av Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6644f495f28fb76503948c18354a5af0fcf832e5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364761"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK för SQL API: Information och resurser
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
> * [Spark 3 OLTP Connector](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP Connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resursprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

Detta är den ursprungliga Azure Cosmos DB Sync Java SDK v2 för SQL API som stöder synkrona åtgärder.

> [!IMPORTANT]  
> Det här *är inte* den senaste Java SDK:n för Azure Cosmos DB! Överväg att [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) för ditt projekt. Om du vill uppgradera följer du anvisningarna i [guiden Migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) och guiden Reactor vs [RxJava.](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/reactor-rxjava-guide.md) 
>

| | Länkar |
|---|---|
|**SDK-nedladdning**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentation**|[Java API-referensdokumentation](/java/api/com.microsoft.azure.documentdb)|
|**Bidra till SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Komma igång**|[Kom igång med Java SDK](./create-sql-api-java.md)|
|**Självstudiekurs om webbapp**|[Utveckling av webbprogram med Azure Cosmos DB](sql-api-java-application.md)|
|**Minsta körningstid som stöds**|[Java Development Kit (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>Viktig information

### <a name="261"></a><a name="2.6.1"></a>2.6.1
* En bugg vid hantering av en fråga via tjänstinterop har åtgärdats.

### <a name="260"></a><a name="2.6.0"></a>2.6.0
* Stöd har lagts till för att fråga ändringsflöde från tidpunkt.

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Åtgärdar det primära partitionscachens problem med documentCollection-fråga.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Stöd har lagts till för 449 återförsök av anpassad konfiguration.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Åtgärdar timeout-problem med anslutningspoolen.
* Åtgärdar uppdatering av autentiseringstoken vid interna återförsök.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Rätt principtagg för replik på klientsidan har uppdaterats på databaseAccount och gjort så att databaseAccount-konfiguration läser från cachen.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Undvik återförsök vid fel med ogiltigt partitionsnyckelintervall om användaren tillhandahåller pkRangeId.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Optimerade uppdateringar av cacheminnet för partitionsnyckelintervall.
* Åtgärdar scenariot där SDK:n inte partitionerar partitionsdelningstips från servern och resulterar i felaktig uppdatering av routningscacheminnen på klientsidan.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Uppdateringar av optimerad samlingscache.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Stöd har lagts till för att hämta inre undantagsmeddelande från diagnostiksträngen för begäran.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Introducerade versions-API på PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Separat timeout-stöd har lagts till för direktläge.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* Användning av null-felmeddelande från tjänsten och produktion av dokumentklientfel.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Socketanslutningsförbättring med SoKeepAlive-standardvärdet true.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Stöd för diagnostiksträng för begäran har lagts till.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Bugg i PartitionKey för Hash V2 har åtgärdats.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Stöd har lagts till för sammansatta index.
* En bugg har åtgärdats i den globala slutpunktshanteraren för att tvinga fram uppdatering.
* Bugg för upsertar med förvillkor i direktläge har åtgärdats.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* En bugg i gatewayadresscachen har åtgärdats.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Skrivstöd för flera regioner har lagts till för direktläge.
* Stöd har lagts till för hantering av IOExceptions som utlöses som ServiceUnavailable-undantag från en proxyserver.
* En bugg i återförsöksprincipen för slutpunktsidentifiering har åtgärdats.
* En bugg har åtgärdats för att säkerställa att null-pekarundantag inte inställts i BaseDatabaseAccountConfigurationProvider.
* En bugg har åtgärdats som säkerställer att QueryIterator inte returnerar null-värden.
* En bugg har åtgärdats som säkerställer att stora PartitionKey tillåts

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Skrivstöd för flera regioner har lagts till för gatewayläge.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* En bugg har åtgärdats i Nyckelintervall för läspartition för en fråga.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* En bugg i inställningen av storleken på fortsättningstokenhuvudet i DirectHttps-läge har åtgärdats.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Stöd för redundans för direktuppspelning har lagts till.
* Stöd har lagts till för anpassade metadata.
* Förbättrad sessionshanteringslogik.
* En bugg har åtgärdats i cacheminnet för partitionsnyckelintervall.
* En NPE-bugg har åtgärdats i direktläge.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Stöd för unikt index har lagts till.
* Stöd har lagts till för att begränsa storleken på fortsättningstoken i feedalternativ.
* En bugg i Json-serialisering (tidsstämpel) har åtgärdats.
* En bugg i Json-serialisering (uppräkning) har åtgärdats.
* Beroende av com.fasterxml.michael.core:michael-databind uppgraderad till 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Förbättrad anslutningspool för direktläge.
* Förbättrad förbättring av prefetch för icke-ordningefter frågor mellan partitioner.
* Förbättrad UUID-generering.
* Förbättrad logik för sessionskonsekvens.
* Stöd har lagts till för multipolygon.
* Stöd har lagts till för partitionsnyckelintervallstatistik för insamling.
* En bugg i stöd för flera regioner har åtgärdats.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Förbättrad prestanda för Json-serialisering.
* Den här SDK-versionen kräver den senaste versionen [Azure Cosmos DB emulatorn](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Interna ändringar för Microsofts vänbibliotek.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Ett problem har åtgärdats vid läsning av nyckelintervall för en enskild partition.
* Åtgärdat ett problem i ResourceID-parsning som påverkar databasen med korta namn.
* Ett problem somorsaken till partitionsnyckelkodning har åtgärdats.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Kritiska felkorrigeringar för bearbetning av förfrågningar under partitionsdelningar.
* Åtgärdat ett problem med konsekvensnivåerna Strong och BoundedStaleness.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Stöd har lagts till för en ny konsekvensnivå som kallas ConsistentPrefix.
* En bugg har åtgärdats i läsningen av samlingen i sessionsläge.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Stöd har aktiverats för partitionerad samling med så lite som 2 500 RU/s och skalning i steg om 100 RU/s.
* En bugg i den interna sammansättningen som kan orsaka NullRef-undantag i vissa frågor har åtgärdats.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* En bugg i frågemotorns konfiguration som kan orsaka undantag för frågor i gatewayläge har åtgärdats.
* Åtgärdat några buggar i sessionscontainern som kan orsaka undantaget "Ägarresurs hittades inte" för begäranden omedelbart efter att samlingen har skapats.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Stöd har lagts till för aggregeringsfrågor (COUNT, MIN, MAX, SUM och AVG). Se [Sammansättningsstöd](sql-query-aggregate-functions.md).
* Stöd för ändringsflöde har lagts till.
* Stöd för insamlingskvotinformation har lagts till via RequestOptions.setPopulateQuotaInfo.
* Stöd har lagts till för skriptloggning av lagrade procedurer via RequestOptions.setScriptLoggingEnabled.
* En bugg har åtgärdats där frågan i DirectHttps-läge kan sluta svara när det uppstår begränsningsfel.
* En bugg i sessionskonsekvensläge har åtgärdats.
* En bugg har åtgärdats som kan orsaka NullReferenceException i HttpContext när förfrågningsfrekvensen är hög.
* Förbättrad prestanda för DirectHttps-läge.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Stöd för enkel klientinstansbaserad proxy med ConnectionPolicy.setProxy() API har lagts till.
* DocumentClient.close() API har lagts till för att stänga av DocumentClient-instansen korrekt.
* Förbättrad frågeprestanda i direkt anslutningsläge genom att härleda frågeplanen från den interna sammansättningen i stället för gatewayen.
* Ange FAIL_ON_UNKNOWN_PROPERTIES = falskt så att användarna inte behöver definiera JsonIgnoreProperties i sin POJO.
* Omstrukturerad loggning för att använda SLF4J.
* Åtgärdat några andra buggar i konsekvensläsaren.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Åtgärdat en bugg i anslutningshanteringen för att förhindra anslutningsläckor i direkt anslutningsläge.
* En bugg har åtgärdats i TOP-frågan där det kan orsaka NullReference-undantag.
* Förbättrad prestanda genom att minska antalet nätverkssamtal för interna cacheminnen.
* Statuskod, ActivityID och begärande-URI har lagts till i DocumentClientException för bättre felsökning.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Ett problem har åtgärdats i anslutningshanteringen för stabilitet.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Stöd för konsekvensnivån BoundedStaleness har lagts till.
* Stöd har lagts till för direktanslutning för CRUD-åtgärder för partitionerade samlingar.
* En bugg har åtgärdats vid fråge av en databas med SQL.
* En bugg har åtgärdats i sessionscachen där sessionstoken kan anges felaktigt.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Stöd har lagts till för parallella frågor mellan partitioner.
* Stöd har lagts till för TOP/ORDER BY-frågor för partitionerade samlingar.
* Stöd för stark konsekvens har lagts till.
* Stöd har lagts till för namnbaserade begäranden när du använder direktanslutning.
* Har åtgärdats för att ActivityId ska vara konsekvent för alla återförsök av förfrågningar.
* En bugg som är relaterad till sessionscachen vid återskapande av en samling med samma namn har åtgärdats.
* Polygon- och LineString-datatyper har lagts till när du anger en indexeringsprincip för samlingar för rumsliga geobegränsningsfrågor.
* Problem med Java Doc för Java 1.8 har åtgärdats.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* En bugg i PartitionKeyDefinitionMap har åtgärdats för att cachelagra samlingar med enskilda partitioner och inte göra extra hämtning av partitionsnyckelbegäranden.
* En bugg har åtgärdats som gjorde att inget nytt försök skulle visas när ett felaktigt partitionsnyckelvärde angavs.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Stöd för databaskonton för flera regioner har lagts till.
* Stöd har lagts till för automatiskt återförsök för begränsade begäranden med alternativ för att anpassa maximalt antal återförsök och högsta återförsöksväntetid.  Se RetryOptions och ConnectionPolicy.getRetryOptions().
* Inaktuell IPartitionResolver-baserad anpassad partitioneringskod. Använd partitionerade samlingar för högre lagring och dataflöde.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Stöd för återförsöksprincip för hastighetsbegränsning har lagts till.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Stöd för TTL (Time to Live) har lagts till för dokument.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementerat [partitionerade samlingar](partitioning-overview.md) [och användardefinierade prestandanivåer](performance-levels.md).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* En bugg i HashPartitionResolver har åtgärdats för att generera hash-värden i little-endian för att vara konsekventa med andra SDK:er.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Lägg till hash& intervallpartitions matchare för att hjälpa till med program för horisontell partitionering över flera partitioner.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementera Upsert. Nya upsertXXX-metoder har lagts till för att stödja Upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar sker internt.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Versionen hoppas över för att anpassa versionsnumret till andra SDK:er

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Stöder geospatialt index
* Validerar ID-egenskapen för alla resurser. ID:n för resurser får inte innehålla ?, /, #, \, tecken eller sluta med blanksteg.
* Lägger till det nya huvudet "förlopp för indextransformering" i ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementerar V2-indexeringsprincip

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Utgivnings- och tillbakasättningsdatum

Microsoft meddelar kunder minst **12 månader** innan en SDK-version dras tillbaka för att säkerställa en smidig övergång till en nyare version eller en version som stöds. Nya funktioner, funktioner och optimeringar läggs bara till i den aktuella SDK:n. Därför rekommenderar vi att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

> [!WARNING]
> Efter den 30 maj 2020 kommer Azure Cosmos DB inte längre att göra felkorrigeringar, lägga till nya funktioner och ge stöd för version 1.x av Azure Cosmos DB Java SDK för SQL API. Om du föredrar att inte uppgradera kommer begäranden från version 1.x av SDK:n att fortsätta att hanteras av tjänsten Azure Cosmos DB.
>
> Efter den 29 februari 2016 kommer Azure Cosmos DB inte längre att göra felkorrigeringar, lägga till nya funktioner och ge stöd för version 0.x av Azure Cosmos DB Java SDK för SQL API. Om du föredrar att inte uppgradera fortsätter begäranden som skickas från version 0.x av SDK:n att betjänas av Azure Cosmos DB tjänsten.


| Version | Utgivningsdatum | Förfallodatum |
| --- | --- | --- |
| [2.6.1](#2.6.1) |Den 17 dec 2020 |--- |
| [2.6.0](#2.6.0) |Den 16 juli 2020 |--- |
| [2.5.1](#2.5.1) |Den 3 juni 2020 |--- |
| [2.5.0](#2.5.0) |Den 12 maj 2020 |--- |
| [2.4.7](#2.4.7) |20 feb 2020 |--- |
| [2.4.6](#2.4.6) |Den 24 januari 2020 |--- |
| [2.4.5](#2.4.5) |10 november 2019 |--- |
| [2.4.4](#2.4.4) |Den 24 oktober 2019 |--- |
| [2.4.2](#2.4.2) |26 sep 2019 |--- |
| [2.4.1](#2.4.1) |Den 18 juli 2019 |--- |
| [2.4.0](#2.4.0) |Den 4 maj 2019 |--- |
| [2.3.0](#2.3.0) |Den 24 april 2019 |--- |
| [2.2.3](#2.2.3) |Den 16 april 2019 |--- |
| [2.2.2](#2.2.2) |Den 5 april 2019 |--- |
| [2.2.0](#2.2.0) |27 mars 2019 |--- |
| [2.1.3](#2.1.3) |13 mars 2019 |--- |
| [2.1.2](#2.1.2) |9 mars 2019 |--- |
| [2.1.1](#2.1.1) |13 dec 2018 |--- |
| [2.1.0](#2.1.0) |20 november 2018 |--- |
| [2.0.0](#2.0.0) |September 21, 2018 |--- |
| [1.16.4](#1.16.4) |September 10, 2018 |Den 30 maj 2020 |
| [1.16.3](#1.16.3) |September 09, 2018 |Den 30 maj 2020 |
| [1.16.2](#1.16.2) |Den 29 juni 2018 |Den 30 maj 2020 |
| [1.16.1](#1.16.1) |Den 16 maj 2018 |Den 30 maj 2020 |
| [1.16.0](#1.16.0) |Den 15 mars 2018 |Den 30 maj 2020 |
| [1.15.0](#1.15.0) |14 november 2017 |Den 30 maj 2020 |
| [1.14.0](#1.14.0) |Den 28 oktober 2017 |Den 30 maj 2020 |
| [1.13.0](#1.13.0) |Den 25 augusti 2017 |Den 30 maj 2020 |
| [1.12.0](#1.12.0) |11 juli 2017 |Den 30 maj 2020 |
| [1.11.0](#1.11.0) |10 maj 2017 |Den 30 maj 2020 |
| [1.10.0](#1.10.0) |Den 11 mars 2017 |Den 30 maj 2020 |
| [1.9.6](#1.9.6) |Den 21 februari 2017 |Den 30 maj 2020 |
| [1.9.5](#1.9.5) |Den 31 januari 2017 |Den 30 maj 2020 |
| [1.9.4](#1.9.4) |Den 24 november 2016 |Den 30 maj 2020 |
| [1.9.3](#1.9.3) |Den 30 oktober 2016 |Den 30 maj 2020 |
| [1.9.2](#1.9.2) |Den 28 oktober 2016 |Den 30 maj 2020 |
| [1.9.1](#1.9.1) |Den 26 oktober 2016 |Den 30 maj 2020 |
| [1.9.0](#1.9.0) |Den 3 oktober 2016 |Den 30 maj 2020 |
| [1.8.1](#1.8.1) |Den 30 juni 2016 |Den 30 maj 2020 |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |Den 30 maj 2020 |
| [1.7.1](#1.7.1) |Den 30 april 2016 |Den 30 maj 2020 |
| [1.7.0](#1.7.0) |Den 27 april 2016 |Den 30 maj 2020 |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |Den 30 maj 2020 |
| [1.5.1](#1.5.1) |Den 31 december 2015 |Den 30 maj 2020 |
| [1.5.0](#1.5.0) |Den 4 december 2015 |Den 30 maj 2020 |
| [1.4.0](#1.4.0) |Den 5 oktober 2015 |Den 30 maj 2020 |
| [1.3.0](#1.3.0) |Den 5 oktober 2015 |Den 30 maj 2020 |
| [1.2.0](#1.2.0) |Den 5 augusti 2015 |Den 30 maj 2020 |
| [1.1.0](#1.1.0) |Den 9 juli 2015 |Den 30 maj 2020 |
| 1.0.1 |Den 12 maj 2015 |Den 30 maj 2020 |
| [1.0.0](#1.0.0) |Den 7 april 2015 |Den 30 maj 2020 |
| 0.9.5-prelease |9 mars 2015 |Den 29 februari 2016 |
| 0.9.4-prelease |Den 17 februari 2015 |Den 29 februari 2016 |
| 0.9.3-prelease |Den 13 januari 2015 |Den 29 februari 2016 |
| 0.9.2-prelease |Den 19 december 2014 |Den 29 februari 2016 |
| 0.9.1-prelease |Den 19 december 2014 |Den 29 februari 2016 |
| 0.9.0-prelease |Den 10 december 2014 |Den 29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns på sidan om tjänsten [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
