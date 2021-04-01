---
title: Använd Query parallellisering och Scale i Azure Stream Analytics
description: Den här artikeln beskriver hur du skalar Stream Analytics jobb genom att konfigurera indata-partitioner, justera frågedefinitionen och ställa in jobb strömnings enheter.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 9149413d070bbb5eb8d0f8d0c99fe5ff705bcefb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98012333"
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Använd Query parallellisering i Azure Stream Analytics
Den här artikeln visar hur du kan dra nytta av parallellisering i Azure Stream Analytics. Du lär dig hur du skalar Stream Analytics jobb genom att konfigurera inpartitioner och justera analys frågans definition.
Som ett krav kan du vilja vara bekant med begreppet enhet för strömning som beskrivs i [förstå och justera strömnings enheter](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Vilka delar ingår i ett Stream Analytics jobb?
En Stream Analytics jobb definition innehåller minst en strömmande indata, en fråga och utdata. Indata är där jobbet läser data strömmen från. Frågan används för att transformera data inmatnings strömmen och utdata är där jobbet skickar jobb resultatet till.

## <a name="partitions-in-inputs-and-outputs"></a>Partitioner i indata och utdata
Med partitionering kan du dela upp data i del mängder baserat på en [partitionsnyckel](../event-hubs/event-hubs-scalability.md#partitions). Om InInformationen (till exempel Event Hubs) är partitionerad av en nyckel, rekommenderar vi starkt att du anger den här partitionsnyckel när du lägger till ininformation till ditt Stream Analytics-jobb. Skalning av ett Stream Analytics jobb drar nytta av partitioner i indata och utdata. Ett Stream Analytics jobb kan använda och skriva olika partitioner parallellt, vilket ökar data flödet. 

### <a name="inputs"></a>Indata
Alla Azure Stream Analytics-ingångar kan dra nytta av partitionering:
-   EventHub (du måste ange partitionsnyckel explicit med PARTITION med nyckelord om du använder kompatibilitetsnivå 1,1 eller lägre)
-   IoT Hub (du måste ange partitionsnyckel explicit med PARTITION med hjälp av nyckelord om du använder kompatibilitetsnivå 1,1 eller lägre)
-   Blob Storage

### <a name="outputs"></a>Utdata

När du arbetar med Stream Analytics kan du dra nytta av partitionering i utdata:
-   Azure Data Lake Storage
-   Azure Functions
-   Azure-tabell
-   Blob Storage (kan ange partitionsnyckel explicit)
-   Cosmos DB (du måste uttryckligen ange partitionsnyckel)
-   Event Hubs (du måste uttryckligen ange partitionsnyckel)
-   IoT Hub (du måste uttryckligen ange partitionsnyckel)
-   Service Bus
- SQL-och Azure Synapse-analys med valfri partitionering: Mer information finns på [sidan utdata till Azure SQL Database](./stream-analytics-sql-output-perf.md).

Power BI stöder inte partitionering. Du kan dock fortfarande partitionera indatamängden enligt beskrivningen i [det här avsnittet](#multi-step-query-with-different-partition-by-values) 

Mer information om partitioner finns i följande artiklar:

* [Översikt över Event Hubs-funktioner](../event-hubs/event-hubs-features.md#partitions)
* [Datapartitionering](/azure/architecture/best-practices/data-partitioning)


## <a name="embarrassingly-parallel-jobs"></a>Köras parallella jobb
Ett *köras parallellt* jobb är det mest skalbara scenariot i Azure Stream Analytics. Den ansluter en partition av indata till en instans av frågan till en partition av utdata. Den här parallellen har följande krav:

1. Om din fråge logik är beroende av samma nyckel som bearbetas av samma instans, måste du se till att händelserna går till samma partition som du har angett. För Event Hubs eller IoT Hub innebär det att händelse data måste ha **PartitionKey** -värdet inställt. Du kan också använda partitionerade avsändare. För Blob Storage innebär detta att händelserna skickas till samma partition-mapp. Ett exempel är en instans instans som samlar in data per userID där indata-händelsehubben partitioneras med hjälp av userID som partitionsnyckel. Men om din fråge logik inte kräver samma nyckel som ska bearbetas av samma instans, kan du ignorera det här kravet. Ett exempel på den här logiken är en enkel Select-Project-filter-fråga.  

2. Nästa steg är att göra din fråga partitionerad. För jobb med kompatibilitetsnivå 1,2 eller högre (rekommenderas) kan anpassade kolumner anges som partitionsnyckel i inkompatibla inställningar och jobbet kommer att paralellized automatiskt. Jobb med kompatibilitetsnivå 1,0 eller 1,1, kräver att du använder **partition av PartitionID** i alla steg i frågan. Flera steg är tillåtna, men alla måste vara partitionerade med samma nyckel. 

3. De flesta utdata som stöds i Stream Analytics kan dra nytta av partitionering. Om du använder en utdatatyp som inte stöder partitionering kan du inte *köras parallellt*. För Event Hub-utdata ser du till att **kolumnen partitionsnyckel** har angetts till samma partitionsnyckel som används i frågan. Mer information finns i [avsnittet utdata](#outputs) .

4. Antalet indata-partitioner måste vara lika med antalet utgående partitioner. Blob Storage-utdata kan stödja partitioner och ärver partitionerings schema för överordnad fråga. När du anger en partitionsnyckel för Blob Storage, partitioneras data per partition, vilket innebär att resultatet fortfarande är helt parallellt. Här är exempel på partitionsalternativ som tillåter ett helt parallellt jobb:

   * 8 indata-partitioner för händelsehubben och 8 Event Hub-utdataparametrar
   * 8 indata-partitioner för händelsehubben och Blob Storage-utdata
   * 8 indata-partitioner för händelsehubben och Blob Storage-utdata partitionerade med ett anpassat fält med godtycklig kardinalitet
   * 8 Blob Storage-datapartitioner och Blob Storage-utdata
   * 8 indata-partitioner för blob-lagring och 8 Event Hub-utdataparametrar

I följande avsnitt beskrivs några exempel scenarier som är köras parallella.

### <a name="simple-query"></a>Exempelfråga

* Inmatade: Event Hub med 8 partitioner
* Utdata: Event Hub med 8 partitioner ("partitionsnyckel" måste anges för att använda "PartitionId")

Fråga:

```SQL
    --Using compatibility level 1.2 or above
    SELECT TollBoothId
    FROM Input1
    WHERE TollBoothId > 100
    
    --Using compatibility level 1.0 or 1.1
    SELECT TollBoothId
    FROM Input1 PARTITION BY PartitionId
    WHERE TollBoothId > 100
```

Den här frågan är ett enkelt filter. Därför behöver vi inte bekymra dig om att partitionera de inloggade indatamängdarna som skickas till Event Hub. Observera att jobb med kompatibilitetsnivå före 1,2 måste innehålla **partition by PartitionID** -sats, så att den uppfyller kravet #2 från tidigare. För utdata måste vi konfigurera Event Hub-utdata i jobbet så att partitionsnyckel anges till **PartitionID**. En sista kontroll är att se till att antalet indata-partitioner är lika med antalet utgående partitioner.

### <a name="query-with-a-grouping-key"></a>Fråga med en grupperings nyckel

* Inmatade: Event Hub med 8 partitioner
* Utdata: Blob Storage

Fråga:

```SQL
    --Using compatibility level 1.2 or above
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
    --Using compatibility level 1.0 or 1.1
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Den här frågan har en grupperings nyckel. Därför måste de händelser som grupper ATS tillsammans skickas till samma Event Hub-partition. Eftersom vi i det här exemplet grupperar av TollBoothID bör vi se till att TollBoothID används som partitionsnyckel när händelserna skickas till Event Hub. I ASA kan vi använda **partition av PartitionID** för att ärva från det här partitionsnamnet och aktivera fullständig parallellisering. Eftersom utdata är Blob Storage behöver vi inte bekymra dig om att konfigurera ett nyckel värde för partitionen, enligt kravet #4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exempel på scenarier som *inte* är köras parallella

I föregående avsnitt visade vi vissa köras-parallella scenarier. I det här avsnittet diskuterar vi scenarier som inte uppfyller alla krav som ska köras parallellt. 

### <a name="mismatched-partition-count"></a>Antal felaktiga partitioner
* Inmatade: Event Hub med 8 partitioner
* Utdata: Event Hub med 32 partitioner

Om antalet partitioner för indata inte matchar antalet utdata, är topologin inte köras parallell oberoende av frågan. Vi kan dock fortfarande hämta vissa nivåer eller parallellisering.

### <a name="query-using-non-partitioned-output"></a>Fråga med icke-partitionerade utdata
* Inmatade: Event Hub med 8 partitioner
* Utdata: Power BI

Power BI-utdata stöder för närvarande inte partitionering. Därför är det här scenariot inte köras parallellt.

### <a name="multi-step-query-with-different-partition-by-values"></a>Fråga i flera steg med en annan PARTITION utifrån värden
* Inmatade: Event Hub med 8 partitioner
* Utdata: Event Hub med 8 partitioner
* Kompatibilitetsnivå: 1,0 eller 1,1

Fråga:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Som du kan se använder det andra steget **TollBoothId** som partitionerings nyckel. Det här steget är inte detsamma som det första steget, och därför kräver vi att vi gör ett blandat. 

### <a name="multi-step-query-with-different-partition-by-values"></a>Fråga i flera steg med en annan PARTITION utifrån värden
* Inmatade: Event Hub med 8 partitioner
* Utdata: Event Hub med 8 partitioner ("partitionsnyckel" måste anges för att använda "TollBoothId")
* Kompatibilitetsnivå – 1,2 eller senare

Fråga:

```SQL
    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Kompatibilitetsnivån 1,2 eller senare aktiverar parallell frågekörningen som standard. Till exempel är fråga från föregående avsnitt partitionerat så länge som "TollBoothId"-kolumnen har angetts som indatamängds nyckel. PARTITION BY PartitionId-sats krävs inte.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Beräkna det maximala antalet enheter för strömning av ett jobb
Det totala antalet enheter för strömning som kan användas av ett Stream Analytics jobb beror på antalet steg i frågan som definierats för jobbet och antalet partitioner för varje steg.

### <a name="steps-in-a-query"></a>Steg i en fråga
En fråga kan innehålla ett eller flera steg. Varje steg är en under fråga som definierats av nyckelordet **with** . Frågan som ligger utanför **with** -nyckelordet (endast en fråga) räknas också som ett steg, till exempel **Select** -uttrycket i följande fråga:

Fråga:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId
```

Den här frågan har två steg.

> [!NOTE]
> Den här frågan beskrivs mer detaljerat längre fram i artikeln.
>  

### <a name="partition-a-step"></a>Partitionera ett steg
Att partitionera ett steg kräver följande villkor:

* Indatakällan måste vara partitionerad. 
* **Select** -uttrycket för frågan måste läsa från en partitionerad indatakälla.
* Frågan i steget måste ha **partitionen med** nyckelord.

När en fråga är partitionerad, bearbetas inmatnings händelser och sammanställs i separata partitionsuppsättningar och utgående händelser skapas för varje grupp. Om du vill ha en kombinerad agg regering måste du skapa ett andra icke-partitionerat steg att aggregera.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Beräkna max enheter för strömning för ett jobb
Alla icke-partitionerade steg kan skala upp till sex strömnings enheter (SUs) för ett Stream Analytics jobb. Förutom detta kan du lägga till 6 SUs för varje partition i ett partitionerat steg.
Du kan se några **exempel** i tabellen nedan.

| Fråga                                               | Max SUs för jobbet |
| --------------------------------------------------- | ------------------- |
| <ul><li>Frågan innehåller ett steg.</li><li>Steget är inte partitionerat.</li></ul> | 6 |
| <ul><li>Indata-dataströmmen partitioneras av 16.</li><li>Frågan innehåller ett steg.</li><li>Steget är partitionerat.</li></ul> | 96 (6 * 16 partitioner) |
| <ul><li>Frågan innehåller två steg.</li><li>Inget av stegen har partitionerats.</li></ul> | 6 |
| <ul><li>Indata-dataströmmen partitioneras av 3.</li><li>Frågan innehåller två steg. Indatamängden är partitionerad och det andra steget är inte.</li><li><strong>Select</strong> -instruktionen läser från partitionerade inmatade.</li></ul> | 24 (18 för partitionerade steg + 6 för icke-partitionerade steg) |

### <a name="examples-of-scaling"></a>Exempel på skalning

Följande fråga beräknar antalet bilar i ett 3-minuters fönster som går via en avgifts station som har tre tollbooths. Den här frågan kan skalas upp till sex SUs.

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

Om du vill använda mer SUs för frågan måste både indata strömmen och frågan vara partitionerade. Eftersom Datastream-partitionen har angetts till 3 kan följande ändrade fråga skalas upp till 18 SUs:

```SQL
    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
```

När en fråga har partitionerats bearbetas inloggade händelser och sammanställs i separata partitionsuppsättningar. Utmatnings händelser skapas också för varje grupp. Partitionering kan orsaka oväntade resultat när fältet **Gruppera efter** inte är partitionsnyckel i indata-dataströmmen. Fältet **TollBoothId** i föregående fråga är till exempel inte partitionsnyckel för **INPUT1**. Resultatet är att data från TollBooth #1 kan spridas i flera partitioner.

Var och en av **INPUT1** -partitionerna bearbetas separat genom att Stream Analytics. Därför skapas flera poster av antalet bilar för samma Tollbooth i samma rullande-fönster. Om du inte kan ändra den här nyckeln kan du lösa det här problemet genom att lägga till ett icke-partitionerings-steg för att aggregera värden mellan partitioner, som i följande exempel:

```SQL
    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId
```

Den här frågan kan skalas till 24 SUs.

> [!NOTE]
> Om du ansluter till två strömmar kontrollerar du att strömmarna är partitionerade efter partitionsnyckel för den kolumn som du använder för att skapa kopplingarna. Kontrol lera också att du har samma antal partitioner i båda strömmarna.
> 
> 

## <a name="achieving-higher-throughputs-at-scale"></a>Uppnå högre data flöden i stor skala

Ett [köras parallellt](#embarrassingly-parallel-jobs) jobb är nödvändigt men inte tillräckligt för att hantera ett högre data flöde i stor skala. Varje lagrings system och dess motsvarande Stream Analytics-utdata har variationer i hur du uppnår bästa möjliga Skriv data flöde. Precis som med alla storskaliga scenarier finns det vissa utmaningar som kan lösas med hjälp av rätt konfigurationer. I det här avsnittet beskrivs konfigurationer för några vanliga utdata och innehåller exempel på hur man kan ta del av förbruknings frekvensen på 1 KB, 5 K och 10 000 händelser per sekund.

I följande observationer används ett Stream Analytics jobb med en tillstånds lös (direkt lagrings fråga), en grundläggande JavaScript-UDF som skriver till Event Hub, Azure SQL DB eller Cosmos DB.

#### <a name="event-hub"></a>Händelsehubb

|Inmatnings frekvens (händelser per sekund) | Enheter för strömning | Utgående resurser  |
|--------|---------|---------|
| 1K     |    1    |  2 DATA FLÖDES ENHETER   |
| 5 000     |    6    |  6 DATA FLÖDES ENHETER   |
| 10 000    |    12   |  10 DATA FLÖDES ENHETER  |

[Event Hub](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-eventhubs) -lösningen skalas linjärt i termer av strömnings enheter (SU) och data flöde, vilket gör det till det mest effektiva och bästa sättet att analysera och strömma data från Stream Analytics. Jobb kan skalas upp till 192 SU, som ungefär översätts till att bearbeta upp till 200 MB/s, eller 19 000 000 000 000 händelser per dag.

#### <a name="azure-sql"></a>Azure SQL
|Inmatnings frekvens (händelser per sekund) | Enheter för strömning | Utgående resurser  |
|---------|------|-------|
|    1K   |   3  |  S3   |
|    5 000   |   18 |  P4   |
|    10 000  |   36 |  P6   |

[Azure SQL](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-azuresql)  har stöd för skrivning parallellt, som kallas Ärv partitionering, men är inte aktiverat som standard. Att aktivera ärva partitionering, tillsammans med en helt parallell fråga, är dock inte tillräckligt för att uppnå högre data flöden. SQL Write-dataflödena är beroende av databas konfigurationen och tabell schemat. I artikeln [SQL-utdata](./stream-analytics-sql-output-perf.md) finns mer information om de parametrar som kan maximera Skriv data flödet. Som anges i [Azure Stream Analytics utdata till Azure SQL Database](./stream-analytics-sql-output-perf.md#azure-stream-analytics) artikel skalar den här lösningen inte linjärt som en helt parallell pipeline utöver 8 partitioner och kan behöva partitionera om innan SQL-utdata (se [i](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)). Premium SKU: er krävs för att hantera höga IO-priser tillsammans med kostnader för att logga säkerhets kopieringar på några minuter.

#### <a name="cosmos-db"></a>Cosmos DB
|Inmatnings frekvens (händelser per sekund) | Enheter för strömning | Utgående resurser  |
|-------|-------|---------|
|  1K   |  3    | 20 000 RU  |
|  5 000   |  24   | 60K RU  |
|  10 000  |  48   | 120K RU |

[Cosmos DB](https://github.com/Azure-Samples/streaming-at-scale/tree/main/eventhubs-streamanalytics-cosmosdb) utdata från Stream Analytics har uppdaterats för att använda inbyggd integrering under [kompatibilitetsnivå 1,2](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12). Kompatibilitetsnivån 1,2 möjliggör betydligt högre genomflöde och minskar RU-förbrukningen jämfört med 1,1, vilket är standard kompatibilitetsnivån för nya jobb. Lösningen använder CosmosDB-behållare partitionerade på/deviceId och resten av lösningen har kon figurer ATS identiskt.

Alla [strömningar i Azure-exempel](https://github.com/Azure-Samples/streaming-at-scale) använder en händelsehubben som indata som matas genom belastnings simulerings test klienter. Varje indata-händelse är ett 1 KB JSON-dokument, som översätter de konfigurerade inmatnings priserna till data flödes nivåerna (1 MB/s, 5 MB/s och 10 MB/s) enkelt. Händelser simulerar en IoT-enhet som skickar följande JSON-data (i ett förkortat format) för upp till 1 kB-enheter:

```
{
    "eventId": "b81d241f-5187-40b0-ab2a-940faf9757c0",
    "complexData": {
        "moreData0": 51.3068118685458,
        "moreData22": 45.34076957651598
    },
    "value": 49.02278128887753,
    "deviceId": "contoso://device-id-1554",
    "type": "CO2",
    "createdAt": "2019-05-16T17:16:40.000003Z"
}
```

> [!NOTE]
> Konfigurationerna kan ändras på grund av de olika komponenter som används i lösningen. Om du vill ha en mer exakt uppskattning kan du anpassa exemplen efter ditt scenario.

### <a name="identifying-bottlenecks"></a>Identifiera Flask halsar

Använd fönstret mått i ditt Azure Stream Analytics jobb för att identifiera Flask halsar i din pipeline. Granska **indata/utdata-händelser** för data flöde och ["fördröjning av vattenstämpel"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) eller **eftersläpande händelser** för att se om jobbet hålls i takt med indata. För Event Hub-mått söker du efter **begränsade begär Anden** och justerar tröskel enheterna enligt detta. För Cosmos DB Mät värden granskar du **Max förbrukade ru/s per nyckel intervall** under genomflödet för att se till att dina partitionerings nyckel intervall är enhetligt förbrukade. Övervaka **logg-i/o** och **CPU** för Azure SQL DB.

## <a name="get-help"></a>Få hjälp

Om du behöver ytterligare hjälp kan du prova vår [Microsoft Q&en fråge sida för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/