---
title: Analysera prestanda
titleSuffix: Azure Cognitive Search
description: TBD
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 298508f8068b47cbfc720f1d1e8ddf370323ed28
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582381"
---
# <a name="analyze-performance-in-azure-cognitive-search"></a>Analysera prestanda i Azure Kognitiv sökning

I den här artikeln beskrivs verktyg, beteenden och metoder för analys av frågor och indexerings prestanda i Kognitiv sökning.

## <a name="develop-baseline-numbers"></a>Utveckla bas linje nummer

I en stor implementering är det viktigt att göra ett test för prestandatest av din Kognitiv sökning tjänst innan du registrerar den i produktionen. Du bör testa både belastningen på Sök frågan som du förväntar dig, men även den förväntade arbets belastningen för data inmatning (om möjligt kan du köra dessa samtidigt). Med benchmark-nummer kan du verifiera rätt [Sök nivå](search-sku-tier.md), [tjänst konfiguration](search-capacity-planning.md)och förväntade [svars tid för frågor](search-performance-analysis.md#average-query-latency).

För att utveckla benchmarks, rekommenderar vi verktyget [Azure-Search-performance-test (GitHub)](https://github.com/Azure-Samples/azure-search-performance-testing) .

Om du vill isolera effekterna av en distribuerad tjänst arkitektur kan du prova med att testa tjänst konfigurationerna för en replik och en partition.

> [!NOTE]
> För lagrings optimerade nivåer (L1 och L2) bör du förvänta dig ett lägre flöde för frågor och högre latens än standard-nivåerna.
>

## <a name="use-diagnostic-logging"></a>Använda diagnostisk loggning

Det viktigaste verktyget som en administratör har för att diagnosticera möjliga prestanda problem är [diagnostikloggning](search-monitor-logs.md) som samlar in drift data och mått om din Sök tjänst. Diagnostisk loggning aktive ras via [Azure Monitor](../azure-monitor/overview.md). Det finns kostnader som är kopplade till att använda Azure Monitor och lagra data, men om du aktiverar det för tjänsten kan det vara instrumentellt att undersöka prestanda problem.

Det finns flera möjligheter för svars tider, oavsett om det sker under en nätverks överföring eller under bearbetning av innehåll i app Services-lagret eller på en Sök tjänst. En viktig fördel med diagnostisk loggning är att aktiviteter loggas från Sök tjänstens perspektiv, vilket innebär att loggen kan hjälpa dig att avgöra om prestanda problem beror på problem med frågan eller indexeringen eller någon annan felpunkt.

:::image type="content" source="media/search-performance/perf-log-event-chain.png" alt-text="Kedja av loggade händelser" border="true":::

Med diagnostikloggning kan du välja alternativ för lagring av loggad information. Vi rekommenderar att du använder [Log Analytics](../azure-monitor/logs/log-analytics-overview.md) så att du kan köra avancerade Kusto-frågor mot data för att besvara många frågor om användning och prestanda. 

På Sök tjänst portalens sidor kan du aktivera loggning via **diagnostikinställningar** och sedan utfärda Kusto-frågor mot Log Analytics genom att välja **loggar**. Mer information om hur du konfigurerar finns i [samla in och analysera loggdata](search-monitor-logs.md).

:::image type="content" source="media/search-performance/perf-log-menu-options.png" alt-text="Meny alternativ för loggning" border="true":::

## <a name="throttling-behaviors"></a>Begränsnings beteende

Begränsningen inträffar när Sök tjänsten har uppnått gränsen för vad den kan hantera från ett resurs perspektiv. Begränsning kan inträffa under frågor eller indexering. Från klient sidan resulterar ett API-anrop i ett 503 HTTP-svar när det har begränsats. Under indexeringen är det också möjligt att ta emot ett 207 HTTP-svar, vilket tyder på att ett eller flera objekt inte kunde indexeras. Det här felet är en indikator som Sök tjänsten får nära kapacitet. 

Som en tumregel är det bäst att kvantifiera den mängd begränsning som visas. Om till exempel en Sök fråga av 500 000 är begränsad, kanske det inte är det stora av ett problem. Men om en stor procent andel frågor begränsas under en period skulle detta vara ett större problem. Genom att titta på begränsning under en period, kan du också identifiera tids ramar där begränsningen kan uppstå, och hjälpa dig att avgöra hur du bäst kan hantera dem.

En enkel korrigering av de flesta begränsnings problem är att ge fler resurser på Sök tjänsten (vanligt vis repliker för frågebaserade begränsningar eller partitioner för indexerings-baserad begränsning). Men om du ökar repliker eller partitioner läggs det till på kostnad, vilket är därför viktigt att veta orsaken till att begränsningen inträffar. Att undersöka de villkor som orsakar begränsningen förklaras i följande flera avsnitt. 

Nedan visas ett exempel på en Kusto-fråga som kan identifiera en uppdelning av HTTP-svar från Sök tjänsten som har lästs in. Genom att fråga över en 7-dagars period visar det återgivna stapeldiagrammet att en relativt stor procent andel av Sök frågorna begränsades, jämfört med antalet lyckade (200) svar.

```kusto
AzureDiagnostics
| where TimeGenerated > ago(7d)
| summarize count() by resultSignature_d 
| render barchart 
```

:::image type="content" source="media/search-performance/perf-bar-chart-http-errors.png" alt-text="Stapeldiagram med antal HTTP-fel" border="true":::

Genom att undersöka begränsningen under en viss tids period kan du hjälpa dig att identifiera de tider då begränsningen kan uppstå oftare. I exemplet nedan används ett tids serie diagram för att visa antalet begränsade frågor som inträffat under en angiven tidsram. I det här fallet utfördes de begränsade frågor som korreleras med tiderna i med prestandatest.

```kusto
let ['_startTime']=datetime('2021-02-25T20:45:07Z');
let ['_endTime']=datetime('2021-03-03T20:45:07Z');
let intervalsize = 1m; 
AzureDiagnostics 
| where TimeGenerated > ago(7d)
| where resultSignature_d != 403 and resultSignature_d != 404 and OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete")
| summarize 
  ThrottledQueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete") and resultSignature_d == 503)/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart   
```

:::image type="content" source="media/search-performance/perf-line-chart-throttled-queries.png" alt-text="Linje diagram med begränsade frågor" border="true":::

## <a name="measure-individual-queries"></a>Mäta enskilda frågor

I vissa fall kan det vara bra att testa enskilda frågor för att se hur de fungerar. Det är viktigt att kunna se hur lång tid det tar för Sök tjänsten att slutföra arbetet, samt hur lång tid det tar att göra begäran om tur och retur från klienten och tillbaka till klienten. Diagnostikloggar kan användas för att söka efter enskilda åtgärder, men det kan vara enklare att göra detta från ett klient verktyg, t. ex. postman.

I exemplet nedan kördes en REST-baserad Sök fråga. Kognitiv sökning innehåller i varje svar det antal millisekunder det tar att slutföra frågan, synlig på fliken Sidhuvud i "förflutna-Time". Bredvid status överst i svaret hittar du varaktigheten för tur och retur. i det här fallet 418 millisekunder. I avsnittet resultat har fliken "rubriker" valts. Genom att använda dessa två värden markerade med en röd ruta i bilden nedan, ser vi att Sök tjänsten tog 21 MS att slutföra Sök frågan och att hela förfrågan om begäran om begäran tog 125 ms. Genom att subtrahera dessa två siffror kan vi avgöra att det tog 104 MS ytterligare tid att skicka Sök frågan till Sök tjänsten och att överföra Sök resultaten tillbaka till klienten.

Detta kan vara mycket användbart för att avgöra om det kan finnas nätverks fördröjningar eller andra faktorer som påverkar frågans prestanda.

:::image type="content" source="media/search-performance/perf-elapsed-time.png" alt-text="Fråga varaktighets mått" border="true":::

## <a name="query-rates"></a>Frekvens för frågor

En möjlig orsak till din Sök tjänst att begränsa förfrågningar beror på Sheer antal frågor som utförs där volymen samlas in som frågor per sekund (frågor per sekund) eller frågor per minut (QPM). När din Sök tjänst tar emot fler frågor per sekund tar det vanligt vis längre tid att svara på frågorna tills den inte längre kan fortsätta, vilket innebär att den skickar tillbaka en begränsning på 503 HTTP-svar. 

Följande Kusto-fråga visar frågesträngen som uppmätt i QPM, tillsammans med genomsnitts tiden för en fråga i millisekunder (AvgDurationMS) och det genomsnittliga antalet dokument (AvgDocCountReturned) som returneras i var och en.

```kusto
AzureDiagnostics
| where OperationName == "Query.Search" and TimeGenerated > ago(1d)
| extend MinuteOfDay = substring(TimeGenerated, 0, 16) 
| project MinuteOfDay, DurationMs, Documents_d, IndexName_s
| summarize QPM=count(), AvgDuractionMs=avg(DurationMs), AvgDocCountReturned=avg(Documents_d)  by MinuteOfDay
| order by MinuteOfDay desc 
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-queries-per-minute.png" alt-text="Diagram som visar frågor per minut" border="true":::

> [!TIP]
> Ta bort raden `| render timechart` och kör sedan frågan igen för att visa data bakom diagrammet.

## <a name="impact-of-indexing-on-queries"></a>Påverkan på indexering av frågor

En viktig faktor att tänka på när du tittar på prestanda är att indexeringen använder samma resurser som Sök frågor. Om du indexerar en stor mängd innehåll kan du se till att svars tiden växer när tjänsten försöker hantera båda arbets belastningarna.

Om frågorna saktar ned kan du titta på tidpunkten för indexerings aktiviteten för att se om den är i funktion med frågans försämring. T. ex. kanske en indexerare kör ett dagligt eller Tim jobb som motsvarar de försämrade prestandan hos Sök frågorna. 

Det här avsnittet innehåller en uppsättning frågor som kan hjälpa dig att visualisera Sök-och indexerings takten. I de här exemplen anges tidsintervallet i frågan. Se till att ange **set i fråga** när du kör frågorna i Azure Portal.

:::image type="content" source="media/search-performance/perf-set-query-time-range.png" alt-text="Ange tidsintervall i verktyget Query" border="true":::

<a name="average-query-latency"></a>

### <a name="average-query-latency"></a>Genomsnittlig fråge svars tid

I nedanstående fråga används en intervall storlek på 1 minut för att visa den genomsnittliga svars tiden för Sök frågorna. I diagrammet kan vi se att den genomsnittliga svars tiden var låg tills 5:17:45 och senast 5:53pm.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime']..['_endTime']) // Time range filtering
| summarize AverageQueryLatency = avgif(DurationMs, OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))
    by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-query-latency.png" alt-text="Diagram som visar genomsnittlig fråge svars tid" border="true":::

### <a name="average-queries-per-minute-qpm"></a>Genomsnittligt antal frågor per minut (QPM)

Följande fråga gör det möjligt för oss att titta på det genomsnittliga antalet frågor per minut för att se till att det inte fanns någon typ av insamling i Sök begär Anden som kan ha påverkat svars tiden. I diagrammet kan vi se att det finns en avvikelse, men inget att ange en topp i antalet begär Anden.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize QueriesPerMinute=bin(countif(OperationName in ("Query.Search", "Query.Suggest", "Query.Lookup", "Query.Autocomplete"))/(intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart
```

:::image type="content" source="media/search-performance/perf-line-chart-average-queries-per-minute.png" alt-text="Diagram som visar genomsnitts frågor per minut" border="true":::

### <a name="indexing-operations-per-minute-opm"></a>Indexerings åtgärder per minut (OPM)

Här ska vi titta på antalet indexerings åtgärder per minut. I diagrammet kan vi se att en stor mängd data har indexerats startade med 5:42 PM och avslutades med 5:50pm. Indexeringen började 3 minuter innan Sök frågorna började bli latenta och avslutades 3 minuter innan Sök frågorna inte längre är latenta.

Från det här kan vi se att det tog ungefär 3 minuter för Sök tjänsten att vara tillräckligt upptagen från indexeringen för att börja påverka Sök svars tiden. Vi kan också se att efter att indexeringen har slutförts tog det en stund för Sök tjänsten att slutföra allt arbete från det nyligen indexerade innehållet, tills Sök frågorna börjar inte längre vara latenta.

```kusto
let intervalsize = 1m; 
let _startTime = datetime('2021-02-23 17:40');
let _endTime = datetime('2021-02-23 18:00');
AzureDiagnostics
| where TimeGenerated between(['_startTime'] .. ['_endTime']) // Time range filtering
| summarize IndexingOperationsPerSecond=bin(countif(OperationName == "Indexing.Index")/ (intervalsize/1m), 0.01)
  by bin(TimeGenerated, intervalsize)
| render timechart 
```

:::image type="content" source="media/search-performance/perf-line-chart-indexing-operations.png" alt-text="Diagram som visar indexerings åtgärder per minut" border="true":::

## <a name="background-service-processing"></a>Bearbetning av bakgrunds tjänst

Det är inte ovanligt att se periodiska toppar i fråge-eller indexerings svars tid. Toppar kan uppstå som svar på indexering eller hög frekvens, men det kan också inträffa under sammanslagnings åtgärder. Sök index lagras i segment-eller Shards. Systemet sammanfogar med jämna mellanrum mindre Shards i stora Shards, vilket kan hjälpa till att optimera tjänst prestanda. Den här sammanslagnings processen rensar också dokument som tidigare har marker ATS för borttagning från indexet, vilket leder till återställning av lagrings utrymme. 

Sammanfogning av Shards är snabb, men även resurs intensiv och kan därmed försämra tjänstens prestanda. Av den anledningen är det troligt att om du ser kort burst-överföring av fråge svars tid och dessa mellanliggande objekt är i kraft med de senaste ändringarna i indexerat innehåll, är det troligt att du har attribut som svars tid till Shard. 

## <a name="next-steps"></a>Nästa steg

Läs de här ytterligare artiklarna om hur du analyserar tjänst prestanda.

+ [Prestandatips](search-performance-tips.md)
+ [Välj en tjänst nivå](search-sku-tier.md)
+ [Hantera kapacitet](search-capacity-planning.md)
