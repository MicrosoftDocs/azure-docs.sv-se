---
title: Viktig information om SDK:n och API:et för Azure Cosmos DB .NET-ändringsflödesprocessorn
description: Lär dig allt om SDK:n och API:et för ändringsflödesprocessorn, inklusive tillbakadragningsdatum och ändringar som görs mellan olika versioner av SDK:n för .NET-ändringsflödesprocessorn.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.openlocfilehash: 8f38ed175241bdb9235795d7a999a82d0799d46d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876588"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>SDK för .NET-ändringsflödesprocessorn: Ladda ned och se viktig information
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
>
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
> * [REST](/rest/api
> * [REST Resource Provider](/rest/api
> * [SQL](sql-api-query-reference.md)
> * [Masskörningsbibliotek – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Masskörningsbibliotek – Java](sql-api-sdk-bulk-executor-java.md)

|   | Länkar  |
|---|---|
|**SDK-nedladdning**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-dokumentation**|[API-referensdokumentation om bibliotek för ändringsflödesprocessorn](/dotnet/api/microsoft.azure.documents.changefeedprocessor)|
|**Komma igång**|[Kom igång med SDK för .NET-ändringsflödesprocessorn](change-feed.md)|
|**Aktuellt ramverk som stöds**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://dotnet.microsoft.com/download) |

> [!NOTE]
> Om du använder ändringsflödesprocessorn rekommenderar vi version 3.x av [.NET SDK](change-feed-processor.md), vars SDK har inbyggt ändringsflöde. 

## <a name="release-notes"></a>Versionsanmärkningar

### <a name="v2-builds"></a>v2-versioner

### <a name="232"></a><a id="2.3.2"></a>2.3.2
* Kompatibilitet för lagringsplatser för lån har lagts till i v3 SDK så att migreringsvägar med frekvent åtkomst kan användas. Ett program kan migrera till v3 SDK och migrera tillbaka till biblioteket för ändringsflödesprocessorn utan tillståndsförlust.

### <a name="231"></a><a id="2.3.1"></a>2.3.1
* Ett problem har åtgärdats som gjorde att en `FeedProcessing.ChangeFeedObserverCloseReason.Unknown`-stängningsorsak skickades till `FeedProcessing.IChangeFeedObserver.CloseAsync` om partitionen inte hittades eller om målrepliken inte var uppdaterad mot lässessionen. Nu används `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone`- och `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`-stängningsorsaker i dessa fall.
* Den nya stängningsorsaken `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` har lagts till, som skickas för att stänga ändringsflödesövervakaren när målrepliken inte är uppdaterad mot lässessionen.

### <a name="230"></a><a id="2.3.0"></a>2.3.0
* En ny metod (`ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`) och associerat offentligt gränssnitt (`ICheckpointPartitionProcessorFactory`) har lagts till. Detta gör att en implementering av `IPartitionProcessor`-gränssnittet kan använda en inbyggd kontrollpunktsmekanism. Den nya fabriken liknar den befintliga `IPartitionProcessorFactory`, förutom att dess `Create`-metod även stöder `ILeaseCheckpointer`-parametern.
* Endast en av de två metoderna, antingen `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` eller `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory`, kan användas för samma `ChangeFeedProcessorBuilder`-instans.

### <a name="228"></a><a id="2.2.8"></a>2.2.8
* Stabilitets- och diagnosförbättringar:
  * Stöd har lagts till för att upptäcka tidskrävande läsningar av ändringsflöden. Följande steg utförs om läsningen tar längre tid än värdet som anges i egenskapen `ChangeFeedProcessorOptions.ChangeFeedTimeout`:
    * Läsningen av ändringsflödet på den partition som orsakar problem avbryts.
    * Instansen av ändringsflödesprocessorn blir av med ägarskapet av det lån som orsakar problem. Det avlägsnade lånet plockas upp igen under nästa steg då ett lån ska göras, antingen av samma eller av en annan instans av ändringsflödesprocessorn. Det gör att läsningen av ändringsflödet börjar om.
    * Ett problem rapporteras till hälsoövervakaren. Standardhälsoövervakaren skickar alla rapporterade problem till spårningsloggen.
  * En ny offentlig egenskap har lagts till: `ChangeFeedProcessorOptions.ChangeFeedTimeout`. Standardvärdet för den här egenskapen är 10 minuter.
  * Ett nytt offentligt enum-värde har lagts till: `Monitoring.MonitoredOperation.ReadChangeFeed`. Om värdet för `HealthMonitoringRecord.Operation` är `Monitoring.MonitoredOperation.ReadChangeFeed` betyder det att hälsoproblemet har att göra med läsningen av ändringsflödet.

### <a name="227"></a><a id="2.2.7"></a>2.2.7
* Förbättrad belastningsutjämningsstrategi i scenariot då det tar längre tid att hämta alla lån än förfallotiden för lån, till exempel på grund av nätverksproblem:
  * I det här scenariot betraktade belastningsutjämningsalgoritmen felaktigt lån som förfallna, vilket gjorde att lån stals från aktiva ägare. Detta ledde till onödig ombalansering av många lån.
  * Det här problemet har åtgärdats i den här versionen. Nu undviks återförsök vid en konflikt när förfallna lån hämtas som ägaren inte har ändrat och hämtningen av förfallna lån skjuts upp till nästa belastningsutjämningsiteration.

### <a name="226"></a><a id="2.2.6"></a>2.2.6
* Förbättrad hantering av övervakarundantag.
* Utförlig information om övervakarfel:
  * När en övervakare stängs på grund av ett undantag av övervakarens ProcessChangesAsync, tar CloseAsync nu emot orsaksparametern ChangeFeedObserverCloseReason.ObserverError.
  * Spårning har lagts till för att identifiera fel i användarkoden i en övervakare.

### <a name="225"></a><a id="2.2.5"></a>2.2.5
* Stöd har lagts till för att hantera delningar i samlingar som använder delat databasflöde.
  * Ett problem har åtgärdats i den här versionen som kan uppstå på grund av en delning i samlingar som använder delat databasflöde när delningen resulterar i en ombalansering av partitioner där endast ett, i stället för två, nyckelintervall för underordnade partitioner skapas. När detta händer kan ändringsflödesprocessorn fastna i borttagningen av lånet för den gamla partitionens nyckelintervall, i stället för att skapa nya lån. Problemet har åtgärdats i den här versionen.

### <a name="224"></a><a id="2.2.4"></a>2.2.4
* En ny egenskap, ChangeFeedProcessorOptions.StartContinuation, har lagts till som gör det möjligt att starta ändringsflödet från en begäran om fortsättningstoken. Egenskapen används bara om lånesamlingen är tom eller om ett lån inte har någon angiven fortsättningstoken. För lån i lånesamlingen med en angiven ContinuationToken används ContinuationToken och ChangeFeedProcessorOptions.StartContinuation ignoreras.

### <a name="223"></a><a id="2.2.3"></a>2.2.3
* Stöd har lagts till för anpassade lagringsplatser så att fortsättningstoken kan bevaras per partition.
  * Till exempel kan en Azure Cosmos DB-lånesamling partitioneras på valfritt sätt med en anpassad lagringsplats.
  * Anpassade lagringsplatser för lån kan använda den nya utökningspunkten ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) och det nya offentliga gränssnittet ILeaseStoreManager.
  * ILeaseManager-gränssnittet har omstrukturerats till gränssnitt med flera roller.
* Mindre icke-bakåtkompatibel ändring: utökningspunkten ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager) har tagits bort. Använd ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) i stället.

### <a name="222"></a><a id="2.2.2"></a>2.2.2
* I den här versionen har ett problem åtgärdats som inträffar när en delning bearbetas i en övervakad samling och en partitionerad lånesamling används. När ett lån bearbetas för en delad partition får inte lånet som är kopplat till den partitionen tas bort. Problemet har åtgärdats i den här versionen.

### <a name="221"></a><a id="2.2.1"></a>2.2.1
* Fast beräkning för konton med flera skrivregioner och nytt format för sessionstoken.

### <a name="220"></a><a id="2.2.0"></a>2.2.0
* Stöd har lagts till för partitionerade lånesamlingar. Partitionsnyckeln måste definieras som /id.
* Mindre icke-bakåtkompatibel ändring: RequestOptions- och CancellationToken-parametrar har lagts till i metoderna för IChangeFeedDocumentClient-gränssnittet och ChangeFeedDocumentClient-klassen. IChangeFeedDocumentClient är en avancerad utökningspunkt som ger stöd för anpassade implementeringar av dokumentklienten som kan användas med ändringsflödesprocessorn. Du kan till exempel anpassa DocumentClient och fånga upp alla anrop till den om du vill utöka spårningen, arbeta med felhantering osv. Med den här uppdateringen måste koden som implementerar IChangeFeedDocumentClient ändras så att nya parametrar finns med i implementeringen.
* Mindre diagnostikförbättringar.

### <a name="210"></a><a id="2.1.0"></a>2.1.0
* Ett nytt API har lagts till: Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync(). Det kan användas för att uppskatta arbetet för varje partition.
* Stöder Microsoft.Azure.DocumentDB SDK 2.0. Kräver Microsoft.Azure.DocumentDB 2.0 eller senare.

### <a name="206"></a><a id="2.0.6"></a>2.0.6
* Den offentliga egenskapen ChangeFeedEventHost.HostName har lagts till för kompatibilitet med v1.

### <a name="205"></a><a id="2.0.5"></a>2.0.5
* Ett konkurrenstillstånd som inträffar i samband med partitionsdelningar har åtgärdats. Konkurrenstillståndet kan göra att lån som erhålls genast går förlorade vid partitionsdelningen vilket ger upphov till konkurrens. Problemet med konkurrenstillståndet har åtgärdats i den här versionen.

### <a name="204"></a><a id="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a id="2.0.3-prerelease"></a>2.0.3 (förhandsversion)
* Åtgärdat följande problem:
  * När en partitionsdelning görs kan det förekomma dubbel bearbetning av dokument som ändrats före delningen.
  * GetEstimatedRemainingWork-API:et returnerade 0 när det inte fanns några lån i lånesamlingen.

* Följande undantag görs offentliga. Tillägg som implementerar IPartitionProcessor kan returnera dessa undantag.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a id="2.0.2-prerelease"></a>2.0.2 (förhandsversion)
* Mindre API-ändringar:
  * ChangeFeedProcessorOptions.IsAutoCheckpointEnabled har tagits bort och markerats som föråldrad.

### <a name="201-prerelease"></a><a id="2.0.1-prerelease"></a>2.0.1 (förhandsversion)
* Stabilitetsförbättringar:
  * Bättre initiering av lånelagringsplatsen. När lånelagringsplatsen är tom kan endast en instans av processorn initiera den. De andra väntar.
  * Mer stabil/effektiv förnyelse/frisläppning av lån. Förnyelsen och frisläppningen av ett lån på en partition görs oberoende av förnyelsen på andra. I v1 gjordes detta i följd för alla partitioner.
* Nytt v2-API:
  * Verktygsmönster för flexibel konstruktion av processorn: klassen ChangeFeedProcessorBuilder.
    * Stöder valfri kombination av parametrar.
    * Kan använda DocumentClient-instansen för övervakning och/eller lånesamling (inte tillgängligt i v1).
  * IChangeFeedObserver.ProcessChangesAsync stöder nu CancellationToken.
  * IRemainingWorkEstimator – den återstående arbetsberäkningen kan användas separat från processorn.
  * Nya utökningspunkter:
    * IPartitionLoadBalancingStrategy – för anpassad belastningsutjämning av partitioner mellan instanser av processorn.
    * ILease, ILeaseManager – för anpassad lånehantering.
    * IPartitionProcessor – för anpassade bearbetningsändringar på en partition.
* Loggning – använder [LibLog](https://github.com/damianh/LibLog)-biblioteket.
* 100 % bakåtkompatibel med v1-API:et.
* Ny kodbas.
* Kompatibel med version 1.21.1 och senare av [SQL .NET SDK](sql-api-sdk-dotnet.md).

### <a name="v1-builds"></a>v1-versioner

### <a name="133"></a><a id="1.3.3"></a>1.3.3
* Ytterligare loggning har lagts till.
* En DocumentClient-läcka har åtgärdats som inträffade när beräkningen av väntande arbete anropades flera gånger.

### <a name="132"></a><a id="1.3.2"></a>1.3.2
* Korrigeringar har gjorts i beräkningen av väntande arbete.

### <a name="131"></a><a id="1.3.1"></a>1.3.1
* Stabilitetsförbättringar.
  * Ett problem med hanteringen av avbrutna uppgifter har åtgärdats som gjorde att övervakare stoppades på vissa partitioner.
* Stöd för manuell hantering av kontrollpunkter.
* Kompatibel med version 1.21 och senare av [SQL .NET SDK](sql-api-sdk-dotnet.md).

### <a name="120"></a><a id="1.2.0"></a>1.2.0
* Stöd för .NET Standard 2.0 har lagts till. Nu stöder paketet moniker för `netstandard2.0`- och `net451`-ramverk.
* Kompatibel med version 1.17.0 och senare av [SQL .NET SDK](sql-api-sdk-dotnet.md).
* Kompatibel med version 1.5.1 och senare av [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md).

### <a name="111"></a><a id="1.1.1"></a>1.1.1
* Ett problem med beräkningen av återstående arbete har åtgärdats som inträffade när ändringsflödet var tomt eller när det inte fanns något väntande arbete.
* Kompatibel med version 1.13.2 och senare av [SQL .NET SDK](sql-api-sdk-dotnet.md).

### <a name="110"></a><a id="1.1.0"></a>1.1.0
* En metod har lagts till för att hämta en beräkning av återstående arbete som ska bearbetas i ändringsflödet.
* Kompatibel med version 1.13.2 och senare av [SQL .NET SDK](sql-api-sdk-dotnet.md).

### <a name="100"></a><a id="1.0.0"></a>1.0.0
* GA SDK
* Kompatibel med version 1.14.1 och lägre av [SQL .NET SDK](sql-api-sdk-dotnet.md).

## <a name="release--retirement-dates"></a>Utgivnings- och tillbakadragningsdatum

Microsoft meddelar kunder minst **12 månader** innan en SDK-version dras tillbaka för att säkerställa en smidig övergång till en nyare version eller en version som stöds. Nya funktioner, verktyg och optimeringar läggs endast till i den aktuella SDK-versionen. Därför rekommenderar vi att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt.

> [!WARNING]
> Efter den 31 augusti 2022 kommer Azure Cosmos DB inte att erbjuda buggkorrigeringar, nya funktioner eller stöd för version 1.x av Azure Cosmos DB .NET eller .NET Core SDK för SQL API. Om du föredrar att inte uppgradera kommer begäranden från version 1.x av SDK:n att fortsätta att hanteras av tjänsten Azure Cosmos DB.

<br/>

| Version | Utgivningsdatum | Förfallodatum |
| --- | --- | --- |
| [2.3.2](#2.3.2) |Den 11 augusti 2020 |--- |
| [2.3.1](#2.3.1) |Den 30 juli 2020 |--- |
| [2.3.0](#2.3.0) |Den 2 april 2020 |--- |
| [2.2.8](#2.2.8) |Den 28 oktober 2019 |--- |
| [2.2.7](#2.2.7) |Den 14 maj 2019 |--- |
| [2.2.6](#2.2.6) |Den 29 januari 2019 |--- |
| [2.2.5](#2.2.5) |Den 13 december 2018 |--- |
| [2.2.4](#2.2.4) |Den 29 november 2018 |--- |
| [2.2.3](#2.2.3) |Den 19 november 2018 |--- |
| [2.2.2](#2.2.2) |31 oktober 2018 |--- |
| [2.2.1](#2.2.1) |Den 24 oktober 2018 |--- |
| [1.3.3](#1.3.3) |Den 8 maj 2018 |--- |
| [1.3.2](#1.3.2) |Den 18 april 2018 |--- |
| [1.3.1](#1.3.1) |13 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 oktober 2017 |--- |
| [1.1.1](#1.1.1) |Den 29 augusti 2017 |--- |
| [1.1.0](#1.1.0) |Den 13 augusti 2017 |--- |
| [1.0.0](#1.0.0) |Den 7 juli 2017 |--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även

Mer information om Cosmos DB finns på sidan om tjänsten [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
