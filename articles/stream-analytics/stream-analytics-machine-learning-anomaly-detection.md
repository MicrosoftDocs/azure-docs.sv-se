---
title: Avvikelse identifiering i Azure Stream Analytics
description: Den här artikeln beskriver hur du använder Azure Stream Analytics och Azure Machine Learning tillsammans för att identifiera avvikelser.
ms.service: stream-analytics
author: jseb225
ms.author: jeanb
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: ec37ea6cbb1c1c6693aab1f6855948d32b85e95b
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441201"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Avvikelse identifiering i Azure Stream Analytics

Azure Stream Analytics finns i både molnet och Azure IoT Edge och erbjuder inbyggda Machine Learning-baserade avvikelse identifierings funktioner som kan användas för att övervaka de två vanligaste avvikelserna: temporära och permanenta. Med **AnomalyDetection_SpikeAndDip** -och **AnomalyDetection_ChangePoint** -funktionerna kan du utföra avvikelse identifiering direkt i ditt Stream Analytics jobb.

Machine Learning-modeller förutsätter en jämnt insticks tids serie. Om tids serien inte är enhetlig kan du infoga ett agg regerings steg med ett rullande-fönster innan du anropar avvikelse identifiering.

Machine Learning-åtgärderna stöder inte säsongs beroende trender eller flera variate-korrelationer för tillfället.

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Avvikelse identifiering med Machine Learning i Azure Stream Analytics

Följande videoklipp visar hur du identifierar en avvikelse i real tid med hjälp av Machine Learning-funktioner i Azure Stream Analytics. 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>Modell beteende

I allmänhet förbättras modellens precision med mer data i glidande fönster. Data i det angivna glidnings fönstret behandlas som en del av dess normala värde intervall för den tids perioden. Modellen tar endast hänsyn till händelse historik över glidande fönster för att kontrol lera om den aktuella händelsen är avvikande. När glidande fönster flyttas avlägsnas gamla värden från modellens utbildning.

Funktionerna fungerar genom att etablera en viss normal baserat på vad de har sett hittills. Avvikare identifieras genom att jämföra med den etablerade normala nivån inom konfidensnivå. Fönster storleken bör baseras på de minsta händelser som krävs för att träna modellen för normal beteende, så att den kan känna igen när en avvikelse inträffar.

Modellens svars tid ökar med historik storleken eftersom den måste jämföras med ett högre antal tidigare händelser. Vi rekommenderar att du bara inkluderar det nödvändiga antalet händelser för bättre prestanda.

Luckor i tids serien kan vara ett resultat av att modellen inte tar emot händelser vid vissa tidpunkter. Den här situationen hanteras av Stream Analytics med Imputation Logic. Historikens storlek, samt varaktigheten för samma glidande fönster, används för att beräkna genomsnitts takten som händelser förväntas komma till.

En avvikelse generator som är tillgänglig [här](https://aka.ms/asaanomalygenerator) kan användas för att mata in en IoT-hubb med data med olika avvikande mönster. Ett ASA-jobb kan ställas in med dessa avvikelse identifierings funktioner för att läsa från den här IoT-hubben och identifiera avvikelser.

## <a name="spike-and-dip"></a>Insamling och DIP

Tillfälliga avvikelser i en tids serie händelse ström kallas för toppar och DIP. Toppar och DIP kan övervakas med hjälp av Machine Learning-baserad operator [AnomalyDetection_SpikeAndDip](/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
).

![Exempel på insamling och DIP-avvikelse](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

Om en andra insamling är mindre än den första i samma glidande fönster är det troligt att den beräknade poängen för den mindre inökningen troligen inte är tillräckligt betydande jämfört med poängen för den första inökningen inom den angivna förtroende nivån. Du kan försöka minska modellens konfidensnivå för att upptäcka sådana avvikelser. Men om du börjar få för många aviseringar kan du använda ett högre konfidens intervall.

I följande exempel fråga förutsätter vi en enhetlig ingångs frekvens för en händelse per sekund i en glidande period på två minuter med historiken 120 händelser. Den slutgiltiga SELECT-instruktionen extraherar och matar ut poäng och avvikelse status med en konfidensnivå på 95%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>Ändra punkt

Beständiga avvikelser i en tids serie händelse ström är ändringar i distributionen av värden i händelse strömmen, t. ex. nivå ändringar och trender. I Stream Analytics identifieras sådana avvikelser med Machine Learning-baserade [AnomalyDetection_ChangePoint](/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) -operatören.

Beständiga ändringar de senaste mycket längre än toppar och DIP och kan tyda på oåterkalleliga händelser. Beständiga ändringar syns vanligt vis inte för blott ögat, men kan identifieras med **AnomalyDetection_ChangePoint** -operatören.

Följande bild är ett exempel på en nivå ändring:

![Exempel på nivå ändrings avvikelse](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

Följande bild är ett exempel på en trend ändring:

![Exempel på en avvikelse i trend förändring](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

Följande exempel fråga förutsätter en enhetlig ingångs frekvens för en händelse per sekund i ett glidande 20-minuters fönster med historik storleken 1200 händelser. Den slutgiltiga SELECT-instruktionen extraherar och matar ut poäng och avvikelse status med en konfidensnivå på 80%.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>Prestanda egenskaper

Prestandan för dessa modeller beror på Historik storlek, fönster varaktighet, händelse belastning och om partitionering på funktions nivå används. I det här avsnittet beskrivs dessa konfigurationer och innehåller exempel på hur du kan upprätthålla förbruknings frekvensen på 1 KB, 5 K och 10 000 händelser per sekund.

* **Historik storlek** – dessa modeller utför linjärt med **Historik storlek**. Om historikens storlek är längre, tar det längre tid för modeller att skapa en ny händelse. Detta beror på att modellerna jämför den nya händelsen med var och en av de tidigare händelserna i kommandobufferten.
* **Fönster varaktighet** – **fönstrets varaktighet** ska motsvara hur lång tid det tar att ta emot så många händelser som anges i historik storleken. Utan att många händelser visas i fönstret Azure Stream Analytics skulle tillräkna saknade värden. CPU-förbrukningen är därför en funktion av historik storleken.
* **Händelse inläsning** – den större **händelse inläsningen**, desto mer arbete som utförs av modellerna, vilket påverkar CPU-förbrukningen. Jobbet kan skalas ut genom att göra det köras parallellt, förutsatt att affärs logiken använder fler ingångs partitioner.
* Partitionering på funktions **nivå**  -  **Partitionering på funktions nivå** görs med hjälp av ```PARTITION BY``` funktions anropet avvikelse identifiering. Den här typen av partitionering lägger till en överordnad status som måste behållas för flera modeller på samma tidpunkt. Partitionering på funktions nivå används i scenarier som partitionering på enhets nivå.

### <a name="relationship"></a>Relation
Historik storlek, fönster varaktighet och total händelse belastning är relaterade på följande sätt:

windowDuration (i MS) = 1000 * historySize/(totalt antal ingångs händelser per sekund/antal startpartitioner)

När du partitionerar funktionen efter deviceId lägger du till "PARTITION BY deviceId" i anropet till funktionen för avvikelse identifiering.

### <a name="observations"></a>Anmärkningar
Följande tabell innehåller observationer av data flödet för en nod (6 SU) för det icke-partitionerade fallet:

| Historik storlek (händelser)    | Fönster varaktighet (MS) | Totalt antal ingångs händelser per sekund |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2 200 |
| 600 | 728 | 1 650 |
| 6 000 | 10 910 | 1 100 |

Följande tabell innehåller observationer av data flödet för en nod (6 SU) för det partitionerade fallet:

| Historik storlek (händelser) | Fönster varaktighet (MS) | Totalt antal ingångs händelser per sekund | Antal enheter |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1 091 | 1 100 | 10 |
| 600 | 10 910 | 1 100 | 10 |
| 6 000 | 218 182 | <550 | 10 |
| 60 | 21 819 | 550 | 100 |
| 600 | 218 182 | 550 | 100 |
| 6 000 | 2 181 819 | <550 | 100 |

Exempel kod för att köra icke-partitionerade konfigurationer ovan finns i [strömningen i skala lagrings platsen](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) i Azure-exempel. Koden skapar ett Stream Analytics-jobb utan partitionering på funktions nivå, som använder Händelsehubben som indata och utdata. Inläsningen av indatamängden genereras med hjälp av test klienter. Varje ingångs händelse är ett 1 KB JSON-dokument. Händelser simulerar en IoT-enhet som skickar JSON-data (för upp till 1 kB-enheter). Historik storlek, fönster varaktighet och total händelse belastning varierar över 2 partitioner.

> [!Note]
> Om du vill ha en mer exakt uppskattning kan du anpassa exemplen efter ditt scenario.

### <a name="identifying-bottlenecks"></a>Identifiera Flask halsar
Använd fönstret mått i ditt Azure Stream Analytics jobb för att identifiera Flask halsar i din pipeline. Granska **indata/utdata-händelser** för data flöde och ["fördröjning av vattenstämpel"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) eller **eftersläpande händelser** för att se om jobbet hålls i takt med indata. För Event Hub-mått söker du efter **begränsade begär Anden** och justerar tröskel enheterna enligt detta. För Cosmos DB Mät värden granskar du **Max förbrukade ru/s per nyckel intervall** under genomflödet för att se till att dina partitionerings nyckel intervall är enhetligt förbrukade. Övervaka **logg-i/o** och **CPU** för Azure SQL DB.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)