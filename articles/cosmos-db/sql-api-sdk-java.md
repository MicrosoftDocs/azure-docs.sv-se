---
title: 'Azure Cosmos DB: SQL Java API, SDK & resurser'
description: Lär dig allt om SQL Java API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 406690eb1303a25002d3d787e746bd091dd83fc6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586145"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK för SQL API: viktig information och resurser
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

Det här är den ursprungliga Azure Cosmos DB synkronisera Java SDK v2 för SQL API som stöder synkrona åtgärder.

> [!IMPORTANT]  
> Detta är *inte* den senaste Java SDK: n för Azure Cosmos DB! Överväg att använda [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md) för ditt projekt. Om du vill uppgradera följer du anvisningarna i guiden [migrera till Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) och [reaktor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) . 
>

| |  |
|---|---|
|**SDK-hämtning**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentation**|[Referens dokumentation för Java API](/java/api/com.microsoft.azure.documentdb)|
|**Bidra till SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Komma igång**|[Kom igång med Java SDK](sql-api-java-get-started.md)|
|**Själv studie kurs om webbapp**|[Utveckling av webb program med Azure Cosmos DB](sql-api-java-application.md)|
|**Lägsta körnings tid som stöds**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Viktig information

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* Korrigerar Master partition cache-problem på documentCollection-fråga.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* Stöd har lagts till för den anpassade konfigurationen för 449-försök.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* Åtgärdar timeout-problem för anslutningspoolen.
* Korrigerar uppdatering av auth-token vid interna återförsök.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* Uppdaterade den korrekta klient sidans-databaseAccount och gjorde databaseAccount-konfigurations läsningar från cachen.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* Undvik ett nytt försök med ett ogiltigt intervall för partitionsnyckel, om användaren tillhandahåller pkRangeId.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* Optimerade partitionerings nycklar, cache-uppdateringar.
* Korrigerar scenariot där SDK inte utlöser partitions delnings tips från servern och resulterar i att cachelagring på klient sidan inte är korrekt uppdaterat.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* Optimerade samlings-cache-uppdateringar.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* Stöd har lagts till för att hämta inre undantags meddelande från begärd diagnostisk sträng.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* Introducerade versions-API på PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* Separat timeout-stöd har lagts till för direkt läge.

### <a name="223"></a><a name="2.2.3"></a>punkt
* Använder null-felmeddelande från tjänst och genererar dokument klient undantag.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* Förbättring av socket-anslutning, tillägg av SoKeepAlive default True.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* Stöd för förfrågnings diagnos stöd har lagts till.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Fast bugg i PartitionKey för hash v2.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* Stöd har lagts till för sammansatta index.
* Åtgärdat fel i global slut punkts hanterare för att framtvinga uppdatering.
* Fast bugg för upsertar med för-villkor i direkt läge.

### <a name="211"></a><a name="2.1.1"></a>punkt
* Fast bugg i Gateway-diskcachen.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* Skriv stöd i flera regioner har lagts till för direkt läge.
* Stöd har lagts till för hantering av IOExceptions utlöst som ServiceUnavailable-undantag från en proxy.
* Åtgärdade ett fel i återförsöks principen för slut punkts identifiering.
* En bugg har åtgärd ATS för att se till att undantag för null-pekare inte genereras i BaseDatabaseAccountConfigurationProvider.
* En bugg har åtgärd ATS för att säkerställa att QueryIterator inte returnerar null-värden.
* En bugg har åtgärd ATS för att säkerställa att stora PartitionKey tillåts

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* Skriv stöd för flera regioner har lagts till i Gateway-läge.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* Ett fel har åtgärd ATS i nyckel intervall för Läs partition för en fråga.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* Ett fel uppstod vid inställning av huvud storlek för fortsättnings-token i DirectHttps-läge.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* Stöd för direkt uppspelning har lagts till.
* Stöd har lagts till för anpassade metadata.
* Förbättrad bearbetnings logik för sessioner.
* Ett fel har åtgärd ATS i cacheminnet för partitionens nyckel intervall.
* Ett NPE-fel har åtgärd ATS i direkt läge.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* Stöd har lagts till för unikt index.
* Stöd har lagts till för att begränsa den önskade token-storleken i matnings alternativ.
* En bugg har åtgärd ATS i JSON-serialisering (timestamp).
* En bugg har åtgärd ATS i JSON-serialisering (Enum).
* Beroende av com. fasterxml. Jackson. Core: Jackson-DataBind uppgraderat till 2.9.5.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* Förbättrad anslutningspoolen för direkt läge.
* Förbättrad för hämtnings förbättring för icke-OrderBy Cross partition-fråga.
* Förbättrad UUID-generering.
* Förbättrad konsekvens logik för sessioner.
* Stöd har lagts till för multipolygoner.
* Stöd har lagts till för statistik för partitionsnyckel i samlingen.
* Ett fel har åtgärd ATS i stöd för flera regioner.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* Förbättrad prestanda för JSON-serialisering.
* Den här SDK-versionen kräver den senaste versionen av [Azure Cosmos DB-emulatorn](https://aka.ms/cosmosdb-emulator).

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Interna ändringar för Microsoft-vänners bibliotek.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* Ett problem har åtgärd ATS vid läsning av nyckel intervall för enskilda partitioner.
* Ett problem har åtgärd ATS i ResourceID-parsning som påverkar databasen med korta namn.
* En problem åtgärd orsakades av partitionsnyckel.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* Viktiga fel korrigeringar för att begära bearbetning under partitions delning.
* Ett problem har åtgärd ATS med starka och BoundedStaleness konsekvens nivåer.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* Stöd har lagts till för en ny konsekvens nivå som kallas Konsekvensprincip.
* Ett fel uppstod vid läsning av insamling i sessionsläge.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* Aktiverat stöd för partitionerad samling med så låg som 2 500 RU/s och skala i steg om 100 RU/SEK.
* En bugg har åtgärd ATS i den inbyggda sammansättningen som kan orsaka NullRef-undantag i vissa frågor.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* En bugg har åtgärd ATS i konfigurationen för frågemotor som kan orsaka undantag för frågor i Gateway-läge.
* Korrigerade några buggar i sessions-behållaren som kan orsaka att det inte går att hitta en "ägare resurs"-undantag för förfrågningar direkt efter att samlingen har skapats.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* Stöd har lagts till för agg regerings frågor (antal, MIN, MAX, SUM och AVG). Se [agg regerings stöd](sql-query-aggregates.md).
* Stöd har lagts till för ändrings flöde.
* Stöd har lagts till för samlings kvot information via RequestOptions. setPopulateQuotaInfo.
* Stöd för skript loggning för lagrad procedur har lagts till via RequestOptions. setScriptLoggingEnabled.
* Ett fel har åtgärd ATS där fråga i DirectHttps-läge kan sluta svara vid upptäckt av begränsnings fel.
* Ett fel har åtgärd ATS i konsekvens läge för sessioner.
* En bugg har åtgärd ATS som kan orsaka NullReferenceException i HttpContext när begär ande frekvens är hög.
* Förbättrad prestanda för DirectHttps-läge.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* Stöd för enkel klient instans baserad proxy har lagts till med ConnectionPolicy. setProxy () API.
* Har lagt till DocumentClient. Close () API för att stänga av DocumentClient-instansen korrekt.
* Bättre prestanda för frågor i direkt anslutnings läge genom att härleda frågeuttrycket från den inbyggda sammansättningen i stället för gatewayen.
* Ange FAIL_ON_UNKNOWN_PROPERTIES = falskt så att användarna inte behöver definiera JsonIgnoreProperties i sina POJO.
* Omfaktorad loggning för att använda SLF4J.
* Åtgärdade några andra buggar i konsekvens läsaren.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* Ett fel har åtgärd ATS i anslutnings hanteringen för att förhindra anslutnings läckor i direkt anslutnings läge.
* Ett fel har åtgärd ATS i den översta frågan där det kan utlösa NullReference-undantag.
* Bättre prestanda genom att minska antalet nätverks anrop för interna cacheminnen.
* Har lagt till status kod, ActivityID och begärande-URI i DocumentClientException för bättre fel sökning.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* Ett problem har åtgärd ATS i anslutnings hanteringen för stabilitet.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* Stöd har lagts till för BoundedStaleness konsekvens nivå.
* Stöd har lagts till för direkt anslutning för CRUD-åtgärder för partitionerade samlingar.
* Ett fel har åtgärd ATS i frågan om en databas med SQL.
* En bugg har åtgärd ATS i cachen där sessionstoken kan anges felaktigt.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* Stöd har lagts till för parallella frågor över partitioner.
* Stöd har lagts till för överkant/sortera efter frågor för partitionerade samlingar.
* Stöd har lagts till för stark konsekvens.
* Stöd har lagts till för namnbaserade begär anden när direkt anslutning används.
* Åtgärdat för att göra ActivityId överkonsekvent över alla begär Anden om försök.
* Åtgärdade ett fel som är relaterat till sessionstoken när du återskapar en samling med samma namn.
* Tillagda polygon-och lin Est ring-datatyper när du angett samlings indexerings princip för geografiska frågor för geo-avgränsning.
* Problem med java doc för Java 1,8 har åtgärd ATS.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* En bugg har åtgärd ATS i PartitionKeyDefinitionMap för att cachelagra enskilda partitionsuppsättningar och inte göra extra hämtningar av partitionsnyckel.
* Ett fel har åtgärd ATS för att inte försöka igen när ett felaktigt värde angavs för partitionsnyckel.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* Stöd har lagts till för databas konton med flera regioner.
* Stöd har lagts till för automatiskt nya försök vid begränsade begär Anden med alternativ för att anpassa de maximala nya försöken och vänte tiden för Max antal försök.  Se RetryOptions och ConnectionPolicy. getRetryOptions ().
* Föråldrad IPartitionResolver-baserad anpassad partitionerings kod. Använd partitionerade samlingar för högre lagring och data flöde.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* Princip stöd för återförsök har lagts till för hastighets begränsning.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* Stöd för TTL (Time to Live) för dokument har lagts till.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* Implementerade [partitionerade samlingar](partition-data.md) och [användardefinierade prestanda nivåer](performance-levels.md).

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* En bugg har åtgärd ATS i HashPartitionResolver för att generera hash-värden i lite-endian för att vara konsekventa med andra SDK: er.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* Lägg till hash & intervall partition matchare för att hjälpa till med horisontell partitionering-program över flera partitioner.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Implementera upsert. Nya upsertXXX-metoder har lagts till som stöd för upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar internt.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* Versionen hoppades över för att placera versions numret i justering med andra SDK: er

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* Har stöd för GeoSpatialt index
* Verifierar ID-egenskapen för alla resurser. ID: n för resurser får inte innehålla?,/, #, \, tecken eller slutar med ett blank steg.
* Lägger till det nya huvudet "indexera omvandlings förlopp" i ResourceResponse.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* Implementerar v2-indexerings princip

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Datum för lansering och indragning

Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds. Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK: n, eftersom det rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

> [!WARNING]
> Efter 30 maj 2020 kommer Azure Cosmos DB inte längre att göra fel korrigeringar, lägga till nya funktioner och ge stöd till version 1. x av Azure Cosmos DB Java SDK för SQL API. Om du föredrar att inte uppgradera fortsätter de begär Anden som skickas från version 1. x av SDK att hanteras av Azure Cosmos DBs tjänsten.
>
> Efter 29 februari 2016 kommer Azure Cosmos DB inte längre att göra fel korrigeringar, lägga till nya funktioner och ge stöd till version 0. x av Azure Cosmos DB Java SDK för SQL API. Om du inte vill uppgradera, kommer begär Anden som skickats från version 0. x av SDK fortsätta att hanteras av tjänsten Azure Cosmos DB.


| Version | Lanserings datum | Förfallodatum |
| --- | --- | --- |
| [2.5.1](#2.5.1) |Juli 03 2020 |--- |
| [2.5.0](#2.5.0) |12 maj 2020 |--- |
| [2.4.7](#2.4.7) |Feb 20, 2020 |--- |
| [2.4.6](#2.4.6) |24 Jan 2020 |--- |
| [2.4.5](#2.4.5) |Den 10 november 2019 |--- |
| [2.4.4](#2.4.4) |Oktober 24 oktober 2019 |--- |
| [2.4.2](#2.4.2) |Den 26 sep 2019 |--- |
| [2.4.1](#2.4.1) |Jul 18, 2019 |--- |
| [2.4.0](#2.4.0) |Den 04 maj 2019 |--- |
| [2.3.0](#2.3.0) |24 april 2019 |--- |
| [punkt](#2.2.3) |16 april 2019 |--- |
| [2.2.2](#2.2.2) |Apr 05, 2019 |--- |
| [2.2.0](#2.2.0) |Mar 27, 2019 |--- |
| [2.1.3](#2.1.3) |Mar 13, 2019 |--- |
| [2.1.2](#2.1.2) |Mar 09, 2019 |--- |
| [punkt](#2.1.1) |Dec 13, 2018 |--- |
| [2.1.0](#2.1.0) |Nov 20, 2018 |--- |
| [2.0.0](#2.0.0) |21 september 2018 |--- |
| [1.16.4](#1.16.4) |10 september 2018 |30 maj 2020 |
| [1.16.3](#1.16.3) |2018 september, |30 maj 2020 |
| [1.16.2](#1.16.2) |29 juni 2018 |30 maj 2020 |
| [1.16.1](#1.16.1) |16 maj 2018 |30 maj 2020 |
| [1.16.0](#1.16.0) |15 mars 2018 |30 maj 2020 |
| [1.15.0](#1.15.0) |Den 14 november 2017 |30 maj 2020 |
| [1.14.0](#1.14.0) |28 oktober 2017 |30 maj 2020 |
| [1.13.0](#1.13.0) |25 augusti 2017 |30 maj 2020 |
| [1.12.0](#1.12.0) |11 juli 2017 |30 maj 2020 |
| [1.11.0](#1.11.0) |10 maj 2017 |30 maj 2020 |
| [1.10.0](#1.10.0) |11 mars 2017 |30 maj 2020 |
| [1.9.6](#1.9.6) |21 februari 2017 |30 maj 2020 |
| [1.9.5](#1.9.5) |31 januari 2017 |30 maj 2020 |
| [1.9.4](#1.9.4) |24 november 2016 |30 maj 2020 |
| [1.9.3](#1.9.3) |30 oktober 2016 |30 maj 2020 |
| [1.9.2](#1.9.2) |28 oktober 2016 |30 maj 2020 |
| [1.9.1](#1.9.1) |26 oktober 2016 |30 maj 2020 |
| [1.9.0](#1.9.0) |Den 03 oktober 2016 |30 maj 2020 |
| [1.8.1](#1.8.1) |30 juni 2016 |30 maj 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 maj 2020 |
| [1.7.1](#1.7.1) |30 april 2016 |30 maj 2020 |
| [1.7.0](#1.7.0) |27 april 2016 |30 maj 2020 |
| [1.6.0](#1.6.0) |29 mars 2016 |30 maj 2020 |
| [1.5.1](#1.5.1) |31 december 2015 |30 maj 2020 |
| [1.5.0](#1.5.0) |Den 04 december 2015 |30 maj 2020 |
| [1.4.0](#1.4.0) |Den 05 oktober 2015 |30 maj 2020 |
| [1.3.0](#1.3.0) |Den 05 oktober 2015 |30 maj 2020 |
| [1.2.0](#1.2.0) |Den 05 augusti 2015 |30 maj 2020 |
| [1.1.0](#1.1.0) |9 juli 2015 |30 maj 2020 |
| 1.0.1 |12 maj 2015 |30 maj 2020 |
| [1.0.0](#1.0.0) |07, 2015 |30 maj 2020 |
| 0.9.5 – för-leasing |Mar 09, 2015 |29 februari 2016 |
| 0.9.4 – för-leasing |17 februari 2015 |29 februari 2016 |
| 0.9.3 – för-leasing |13 januari 2015 |29 februari 2016 |
| 0.9.2 – för-leasing |19 december 2014 |29 februari 2016 |
| 0.9.1 till och – för-leasing |19 december 2014 |29 februari 2016 |
| 0.9.0 – för-leasing |10 december 2014 |29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

