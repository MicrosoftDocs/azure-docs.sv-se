---
title: Skala upp och ut i Azure Stream Analytics jobb
description: Den här artikeln beskriver hur du skalar ett Stream Analytics jobb genom att partitionera indata, justera frågan och ställa in jobb strömnings enheter.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: e3d4fd6b6b83681284278d10409a1c16394db31f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018691"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Skala ett Azure Stream Analytics jobb för att öka data flödet
Den här artikeln visar hur du ställer in en Stream Analytics-fråga för att öka data flödet för strömnings analys jobb. Du kan använda följande guide för att skala jobbet för att hantera högre belastning och dra nytta av mer system resurser (till exempel mer bandbredd, mer processor resurser, mer minne).
Som ett krav kan du behöva läsa följande artiklar:
-   [Förstå och justera direktuppspelningsenheter](stream-analytics-streaming-unit-consumption.md)
-   [Skapa kan göras parallella-jobb](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Fall 1 – din fråga är helt kan göras parallella över indataceller
Om frågan är helt kan göras parallella över inpartitioner, kan du följa stegen nedan:
1.  Redigera frågan så att den blir köras parallell genom att använda **partition med** nyckelord. Mer information finns i avsnittet köras Parallel Jobs [på den här sidan](stream-analytics-parallelization.md).
2.  Beroende på vilka utdatatyper som används i din fråga kan vissa utdata antingen inte kan göras parallella eller så behöver du ytterligare konfiguration för att vara köras parallell. PowerBI-utdata är till exempel inte kan göras parallella. Utdata slås alltid samman innan skickas till utgående mottagare. Blobbar, tabeller, ADLS, Service Bus och Azure-funktionen är automatiskt parallellt. SQL-och Azure Synapse Analytics-utdata har ett alternativ för parallellisering. Event Hub måste ha PartitionKey-konfigurationen inställd så att den matchar fältet **partition by** (vanligt vis PartitionID). För Event Hub ska du också betala extra uppmärksamhet för att matcha antalet partitioner för alla indata och alla utdata för att undvika över-över-partitioner mellan partitioner. 
3.  Kör din fråga med **6 Su** (vilket är den fulla kapaciteten för en enskild dator) för att mäta maximalt data flöde, och om du använder **Group by**, mäter du hur många grupper (kardinalitet) jobbet kan hantera. Allmänna symtom på system resurs gränser är följande:
    - Måttet SU% är över 80%. Detta indikerar att minnes användningen är hög. De faktorer som bidrar till ökningen av det här måttet beskrivs [här](stream-analytics-streaming-unit-consumption.md). 
    -   Tidsstämpeln för utdata faller bakom i förhållande till väggens Klock tid. Beroende på din fråge logik kan tidsstämpeln för utdata ha en logisk förskjutning från väggens klock klocka. De bör dock förfalla i ungefär samma takt. Om tidsstämpeln för utdata faller ytterligare och ytterligare bakom, är det en indikator att systemet är överarbetat. Det kan vara ett resultat av begränsning av utgående mottagare eller hög processor användning. Vi tillhandahåller inte mått för processor användning just nu, så det kan vara svårt att särskilja de två.
        - Om problemet beror på mottagar begränsningen kan du behöva öka antalet utgående partitioner (och även ange partitioner för att hålla jobbet fullständigt kan göras parallella) eller öka mängden resurser för mottagaren (till exempel antalet enheter för programbegäran för CosmosDB).
    - I jobb diagram finns det ett händelse mått per partition efter varje inläsning. Om händelse måttet för efter släpning håller på att öka, är det också en indikator att system resursen är begränsad (antingen på grund av utgående Sink-begränsning eller hög CPU).
4.  När du har fastställt gränserna för vad ett 6 SU-jobb kan komma åt kan du extrapolera linjärt bearbetnings kapaciteten för jobbet när du lägger till mer SUs, förutsatt att du inte har någon data förvrängning som gör att en viss partition är "frekvent".

> [!NOTE]
> Välj rätt antal enheter för strömning: eftersom Stream Analytics skapar en bearbetnings-nod för varje 6 SU-tillägg, är det bäst att göra antalet noder till en divisor av antalet indata-partitioner, så att partitionerna kan distribueras jämnt över noderna.
> Anta att du har mätt ditt 6 SU-jobb kan uppnå 4 MB/s bearbetnings hastighet och antalet indata-partitioner är 4. Du kan välja att köra jobbet med 12 SU för att uppnå ungefär 8 MB/s bearbetnings hastighet eller 24-SU för att uppnå 16 MB/s. Du kan sedan bestämma när du vill öka ditt SU-nummer för jobbet till det värde som är en funktion i din ingångs takt.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Fall 2 – om frågan inte är köras parallell.
Om frågan inte är köras parallell kan du följa stegen nedan.
1.  Börja med en fråga utan **partition genom** att först för att undvika partitionerings komplexitet och kör din fråga med 6 Su för att mäta den maximala belastningen som i [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Om du kan uppnå din förväntade belastning i data flödes perioden är du färdig. Alternativt kan du välja att mäta samma jobb som körs på 3 SU och 1 SU för att ta reda på det minsta antalet SU som fungerar för ditt scenario.
3.  Om du inte kan uppnå det önskade data flödet kan du försöka dela upp frågan i flera steg om möjligt, om det inte finns flera steg och allokera upp till 6 SU för varje steg i frågan. Om du till exempel har tre steg tilldelar du 18 SU i alternativet "skala".
4.  När du kör ett sådant jobb placerar Stream Analytics varje steg på en egen nod med dedikerade 6 SU-resurser. 
5.  Om du fortfarande inte har uppnått ditt belastnings mål kan du försöka använda en **partition genom** att börja med steg närmare inaktuella inaktuella. För **Group by** -operatorer som kanske inte är naturligt partitionerable kan du använda det lokala/globala samlings mönstret för att utföra en partitionerad **grupp genom** att följa av en icke-partitionerad **grupp av**. Om du till exempel vill räkna hur många bilar som passerar varje väg på varje 3 minuter, och data volymen ligger bortom vad som kan hanteras av 6 SU.

Fråga:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
I frågan ovan ska du räkna bilar per väg-Monte per partition och sedan lägga till antalet från alla partitioner.

Efter att ha partitioner ATS, för varje partition i steget, allokera upp till 6 SU, är varje partition med 6 SU det högsta, så varje partition kan placeras på en egen bearbetnings nod.

> [!Note]
> Om din fråga inte kan partitioneras kan du inte alltid förbättra genomflödet genom att lägga till ytterligare SU i en fråga med flera steg. Ett sätt att få prestanda är att minska volymen på de första stegen med hjälp av ett lokalt/globalt samlings mönster, enligt beskrivningen ovan i steg 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Fall 3 – du kör många oberoende frågor i ett jobb.
För vissa ISV-användningsfall, där det är mer kostnads effektivt att bearbeta data från flera klienter i ett enda jobb, med separata indata och utdata för varje klient, kan du komma igång med några av de (till exempel 20) oberoende frågorna i ett enda jobb. Detta förutsätter att inläsning av under frågor är relativt litet. I så fall kan du följa stegen nedan.
1.  I det här fallet ska du inte använda **partitionen** i frågan
2.  Minska antalet startpartitioner till det lägsta möjliga värdet 2 om du använder Event Hub.
3.  Kör frågan med 6 SU. Med förväntad belastning för varje under fråga lägger du till så många sådana under frågor som möjligt, tills jobbet når system resurs gränser. Se [fall 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) för symptomen när detta inträffar.
4.  När du har nått gränsen för under frågan som mäts ovan börjar du lägga till under frågan i ett nytt jobb. Antalet jobb som ska köras som en funktion av antalet oberoende frågor bör vara relativt linjärt, förutsatt att du inte har någon belastnings skev. Sedan kan du beräkna hur många av de 6 SU-jobb som du behöver köra som en funktion av antalet klienter som du vill betjäna.
5.  När du använder referens data koppling med sådana frågor, Union indatan innan du ansluter till samma referens data. Dela sedan upp händelserna om det behövs. Annars behåller varje referens data koppling en kopia av referens data i minnet, vilket troligt vis tar upp minnes användningen i onödan.

> [!Note] 
> Hur många klienter ska beställas i varje jobb?
> Det här fråge mönstret har ofta ett stort antal under frågor och resulterar i mycket stor och komplex topologi. Jobbets kontrollant kanske inte kan hantera en sådan stor topologi. Som en tumregel kan du hålla dig under 40 klienter för ett SU-jobb och 60-klienter för 3 SU-och 6 SU-jobb. När du har överskridit enhetens kapacitet kommer inte jobbet att starta.



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