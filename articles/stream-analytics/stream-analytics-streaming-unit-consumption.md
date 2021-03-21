---
title: Enheter för strömning i Azure Stream Analytics
description: I den här artikeln beskrivs inställningen för strömnings enheter och andra faktorer som påverkar prestanda i Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: a5a0e6feba966d2d10c5cd36432c3d5db172a795
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020017"
---
# <a name="understand-and-adjust-streaming-units"></a>Förstå och justera direktuppspelningsenheter

Strömnings enheter (SUs) representerar de data bearbetnings resurser som allokeras för att köra ett Stream Analytics jobb. Ju fler SU:er, desto fler processor- och minnesresurser allokeras för jobbet. Med den här kapaciteten kan du fokusera på fråge logiken och göra en sammanfattning av behovet av att hantera maskin vara för att köra Stream Analytics-jobbet inom rimlig tid.

För att minimera svarstiderna vid bearbetningen av dataströmmar utför Azure Stream Analytics-jobb all bearbetning i minnet. Det går inte att köra direkt uppspelnings jobbet när minnet börjar ta slut. För ett produktions jobb är det viktigt att övervaka resursanvändningen för ett direkt uppspelnings jobb och se till att det finns tillräckligt med resurser för att behålla jobben som kör 24/7.

Måttet SU%, som sträcker sig från 0% till 100%, beskriver minnes förbrukningen för din arbets belastning. För ett strömmande jobb med minimalt utrymme är det här måttet vanligt vis mellan 10% och 20%. Om SU%-användningen är hög (över 80%), eller om indata-händelser blir eftersläpande (även om den inte visar CPU-användning), kräver din arbets belastning troligen fler beräknings resurser, vilket kräver att du ökar antalet SUs. Vi rekommenderar att du håller SU-måttet under 80% för att få en tillfällig toppar. Om du vill reagera på ökade arbets belastningar och öka enheter för strömning bör du överväga att ställa in en avisering på 80% på SU-hälsomåttet. Du kan också använda mått för fördröjning av vattenstämplar och eftersläpande händelser för att se om det finns en effekt.

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurera Stream Analytics streaming Units (SUs)
1. Logga in på [Azure-portalen](https://portal.azure.com/)

2. Leta upp det Stream Analytics jobb som du vill skala i listan över resurser och öppna det sedan. 

3. På sidan jobb, under **Konfigurera** rubrik, väljer du **skala**. Standard antalet SUs är 3 när du skapar ett jobb.

    ![Azure Portal Stream Analytics jobb konfiguration][img.stream.analytics.preview.portal.settings.scale]
    
4. Använd skjutreglaget för att ställa in SUs för jobbet. Observera att du är begränsad till särskilda SU-inställningar. 
5. Du kan ändra antalet SUs som är tilldelat ditt jobb även när det körs. Detta är inte möjligt om ditt jobb använder en [icke-partitionerad utdata](./stream-analytics-parallelization.md#query-using-non-partitioned-output) eller har [en fråga med flera steg med olika partitioner med värden](./stream-analytics-parallelization.md#multi-step-query-with-different-partition-by-values). Du kanske är begränsad till att välja från en uppsättning SU-värden när jobbet körs. 

## <a name="monitor-job-performance"></a>Övervaka jobb prestanda
Med hjälp av Azure Portal kan du spåra data flödet för ett jobb:

![Azure Stream Analytics övervaka jobb][img.stream.analytics.monitor.job]

Beräkna det förväntade data flödet för arbets belastningen. Om data flödet är mindre än förväntat kan du finjustera partitionen, finjustera frågan och lägga till SUs i jobbet.

## <a name="how-many-sus-are-required-for-a-job"></a>Hur många SU:er behövs för ett jobb?

Om du väljer hur många SUs-filer som krävs för ett visst jobb beror det på partitionsuppsättningen för indata och frågan som definieras i jobbet. På sidan **skala** kan du ange rätt antal SUS. Vi rekommenderar att du allokerar mer än vad som behövs. Den Stream Analytics bearbetnings motorn optimerar för svars tid och data flöde till kostnaden för att allokera ytterligare minne.

I allmänhet är det bästa sättet att börja med 6 SUs för frågor som inte använder **partition av**. Ta sedan reda på den söt punkten genom att använda en utvärderings-och fel metod där du ändrar antalet SUs när du har överfört representativa data mängder och undersöker SU-hälsoanvändnings måttet. Det maximala antalet enheter för strömning som kan användas av ett Stream Analytics jobb beror på antalet steg i frågan som definierats för jobbet och antalet partitioner i varje steg. Du kan lära dig mer om begränsningarna [här](./stream-analytics-parallelization.md#calculate-the-maximum-streaming-units-of-a-job).

Mer information om hur du väljer rätt antal SUs finns på den här sidan: [skala Azure Stream Analytics jobb för att öka data flödet](stream-analytics-scale-jobs.md)

> [!Note]
> Hur många SUs-uppgifter som krävs för ett visst jobb beror på partitionens konfiguration för indata och på den fråga som definierats för jobbet. Du kan välja upp till din kvot i SUs för ett jobb. Som standard har varje Azure-prenumeration en kvot på upp till 500 SUs för alla analys jobb i en angiven region. Kontakta [Microsoft Support](https://support.microsoft.com)om du vill öka SUS för dina prenumerationer utöver den här kvoten. Giltiga värden för SUs per jobb är 1, 3, 6 och upp i steg om 6.

## <a name="factors-that-increase-su-utilization"></a>Faktorer som ökar den procentuella användningen av SU:er 

Temporala (tidsorienterade) frågedata är kärn uppsättningen av tillstånds känsliga operatorer som tillhandahålls av Stream Analytics. Stream Analytics hanterar statusen för dessa åtgärder internt för användarens räkning, genom att hantera minnes förbrukning, kontroll punkter för återhämtning och tillstånds återställning under tjänst uppgraderingar. Även om Stream Analytics hanterar tillstånden fullständigt, finns det ett antal rekommendationer för bästa praxis som användarna bör tänka på.

Observera att ett jobb med komplex fråge logik kan ha hög SU%-användning även när det inte kontinuerligt tar emot inkommande händelser. Detta kan inträffa efter en plötslig insamling i in-och utdata-händelser. Jobbet kan fortsätta att underhålla tillstånd i minnet om frågan är komplex.

SU%-användning kan plötsligt släppas till 0 under en kort period innan de återgår till förväntade nivåer. Detta inträffar på grund av tillfälliga fel eller systeminitierade uppgraderingar. Att öka antalet strömnings enheter för ett jobb kanske inte minskar SU-användningen om frågan inte är [helt parallell](./stream-analytics-parallelization.md).

Använd [Event Rate-mått](stream-analytics-monitoring.md)när du jämför användning under en viss tids period. InputEvents-och OutputEvents-mått visar hur många händelser som lästs och bearbetats. Det finns mått som indikerar antalet fel händelser, t. ex. deserialiserings fel. När antalet händelser per tidsenhet ökar ökar SU% i de flesta fall.

## <a name="stateful-query-logic-in-temporal-elements"></a>Tillstånds känslig fråge logik i temporala element
En av de unika funktionerna för Azure Stream Analytics jobb är att utföra tillstånds känslig bearbetning, till exempel fönster mängd, temporala kopplingar och temporala analys funktioner. Var och en av dessa operatörer behåller statusinformation. Maximal fönster storlek för de här fråge elementen är sju dagar. 

Det temporala fönstrets koncept visas i flera Stream Analytics frågedata:
1. Fönster mängd: Gruppera efter av rullande, hoppande och glidande fönster

2. Temporala kopplingar: delta med funktionen DATEDIFF

3. Temporala analys funktioner: ISFIRST, LAST och fördröjning med gräns varaktighet

Följande faktorer påverkar det minne som används (del av måttet för strömnings enheter) genom Stream Analytics-jobb:

## <a name="windowed-aggregates"></a>Fönster mängd
Förbrukat minne (tillstånds storlek) för en sammanslagen mängd är inte alltid direkt proportionellt mot fönstrets storlek. I stället är det förbrukade minnet proportionellt för dataens kardinalitet eller antalet grupper i varje tids period.


I följande fråga är det tal som är associerat med till exempel `clusterid` frågans kardinalitet. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

För att minimera eventuella problem som orsakas av hög kardinalitet i föregående fråga kan du skicka händelser till Händelsehubben partitionerad av `clusterid` och skala ut frågan genom att låta systemet bearbeta varje indatatagg separat med hjälp av **partitionen** som visas i exemplet nedan:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Det innebär att antalet `clusterid` värden som kommer till varje nod minskas, vilket minskar huvud gruppen för Group by-operatorn. 

Partitioner som rör händelsehubben bör partitioneras med tangentkombinationen för att undvika att ett minsknings steg behövs. Mer information finns i [Event Hubs översikt](../event-hubs/event-hubs-about.md). 

## <a name="temporal-joins"></a>Temporala kopplingar
Förbrukad mängd minne (tillstånds storlek) för en temporal koppling är proportionell till antalet händelser i det temporala wiggle rummet, vilket är händelsens ingångs frekvens multiplicerat med storleken på wiggle rums utrymme. Med andra ord är det minne som används av sammanfogningar proportionellt till avslags tidsintervallet multiplicerat med genomsnittlig händelse frekvens.

Antalet omatchade händelser i kopplingen påverkar minnes användningen för frågan. Följande fråga söker efter reklamannonser som genererar klick:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

I det här exemplet är det möjligt att många annonser visas och att jag klickar på den och det krävs för att behålla alla händelser i tids perioden. Förbrukat minne beror på tidsperiodens längd och händelsens frekvens. 

Du kan åtgärda detta genom att skicka händelser till Händelsehubben partitionerad av kopplings nycklarna (ID i det här fallet) och skala ut frågan genom att låta systemet bearbeta varje indatatagg separat med hjälp av  **partition** :

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

När frågan är utpartitionerad sprids den ut över flera noder. Därför minskar antalet händelser som kommer till varje nod, vilket minskar storleken på det tillstånd som hålls kvar i kopplings fönstret. 

## <a name="temporal-analytic-functions"></a>Temporala analys funktioner
Förbrukad mängd minne (tillstånds storlek) för en temporal analys funktion är proportionerlig till händelse frekvensen multiplicerat med varaktigheten. Det minne som används av analys funktionerna är inte proportionellt mot fönstrets storlek, utan i stället för antalet partitioner i varje tids period.

Reparationen liknar temporal koppling. Du kan skala ut frågan med **partition by**. 

## <a name="out-of-order-buffer"></a>Felaktig buffert 
Användaren kan konfigurera storleks gränsen för bufferten i konfigurations fönstret för händelse ordning. Bufferten används för att lagra indata under fönstrets varaktighet och ändra ordning på dem. Storleken på bufferten är proportionell mot händelsens ingångs frekvens multiplicerat med storleks fönstret. Standard fönstrets storlek är 0. 

Om du vill reparera spill för bufferten som ligger utanför ordningen kan du skala ut fråga med **partition by**. När frågan är utpartitionerad sprids den ut över flera noder. Därför minskar antalet händelser som kommer till varje nod, vilket minskar antalet händelser i varje beställnings buffert. 

## <a name="input-partition-count"></a>Antal inmatade partitioner 
Varje indatatagg i ett jobbs inmatare har en buffert. Ju större antal inpartitioner, desto mer resurs kommer jobbet att förbrukas. För varje strömnings enhet kan Azure Stream Analytics bearbeta ungefär 1 MB/s indata. Därför kan du optimera genom att matcha antalet Stream Analytics strömnings enheter med antalet partitioner i Händelsehubben. 

Normalt räcker ett jobb som kon figurer ATS med en enhet för strömning för en Event Hub med två partitioner (vilket är minimivärdet för Event Hub). Om Händelsehubben har fler partitioner, förbrukar ditt Stream Analyticss jobb fler resurser, men använder inte nödvändigt vis det extra data flöde som tillhandahålls av Event Hub. 

För ett jobb med 6 enheter för strömning kan du behöva 4 eller 8 partitioner från Händelsehubben. Undvik dock för många onödiga partitioner eftersom det orsakar orimlig resursanvändning. Till exempel en Event Hub med 16 partitioner eller större i ett Stream Analytics jobb som har 1 strömnings enhet. 

## <a name="reference-data"></a>Referens data 
Referens data i ASA läses in i minnet för snabb sökning. Med den aktuella implementeringen behåller varje kopplings åtgärd med referens data en kopia av referens data i minnet, även om du ansluter till samma referens data flera gånger. För frågor med **partition by** har varje partition en kopia av referens data, så partitionerna är helt frikopplade. Med multiplikator effekterna kan minnes användningen snabbt bli mycket hög om du ansluter till referens data flera gånger med flera partitioner.  

### <a name="use-of-udf-functions"></a>Användning av UDF-funktioner
När du lägger till en UDF-funktion Azure Stream Analytics läser in JavaScript-körningen i minnet. Detta kommer att påverka SU%.

## <a name="next-steps"></a>Nästa steg
* [Skapa kan göras parallella-frågor i Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skala Azure Stream Analytics jobb för att öka data flödet](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png
