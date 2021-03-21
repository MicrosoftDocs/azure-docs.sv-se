---
title: Data driven fel sökning i Azure Stream Analytics
description: I den här artikeln beskrivs hur du felsöker ditt Azure Stream Analytics jobb genom att använda jobb diagrammet och måtten i Azure Portal.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020408"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Datadriven felsökning med hjälp av jobbdiagrammet

Jobb diagrammet på bladet **övervakning** i Azure Portal kan hjälpa dig att visualisera jobb pipelinen. Den visar steg för indata, utdata och frågor. Du kan använda jobbdiagrammet för att undersöka mätvärden för varje steg så att du snabbt kan isolera källan till ett problem när du felsöker.

## <a name="using-the-job-diagram"></a>Använda jobb diagrammet

I Azure Portal, i ett Stream Analytics jobb, under **support + fel sökning** väljer du **jobb diagram**:

![Jobb diagram med mått – plats](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Välj varje fråge steg för att se motsvarande avsnitt i ett redigerings fönster för frågor. Ett Metric-diagram för steget visas i ett nedre fönster på sidan.

![Jobb diagram med mått – grundläggande jobb](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Om du vill se partitioner för inmatade Azure-Event Hubs väljer du **...** En snabb meny visas. Du kan också se ingångs sammanslagningen.

![Jobb diagram med mått – expandera partition](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Om du vill se mått diagrammet för endast en enda partition väljer du noden partition. Måtten visas längst ned på sidan.

![Jobb diagram med mått – fler mått](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Om du vill se mått diagrammet för en sammanslagning väljer du noden fusion. Följande diagram visar att inga händelser har släppts eller justerats.

![Jobb diagram med mått – rutnät](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Om du vill se information om Metric-värdet och-tiden pekar du på diagrammet.

![Jobb diagram med mått – hovring](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Felsöka med hjälp av mått

Måttet **QueryLastProcessedTime** anger när ett speciellt steg tar emot data. Genom att titta på topologin kan du arbeta baklänges från utmatnings processorn för att se vilket steg som inte tar emot data. Om ett steg inte hämtar data går du till fråge steget precis innan det. Kontrol lera om föregående processteg har ett tidsfönster och om tillräckligt med tid har gått för att mata ut data. (Observera att tidsfönstren är fästa i timmen.)
 
Om föregående processteg är en ingångs processor använder du måtten för ingångs mått för att besvara följande riktade frågor. De kan hjälpa dig att avgöra om ett jobb hämtar data från sina inmatnings källor. Granska varje partition om frågan är partitionerad.
 
### <a name="how-much-data-is-being-read"></a>Hur mycket data läses?

*   **InputEventsSourcesTotal** är antalet data enheter som lästs. Till exempel antalet blobbar.
*   **InputEventsTotal** är antalet lästa händelser. Det här måttet är tillgängligt per partition.
*   **InputEventsInBytesTotal** är antalet lästa byte.
*   **InputEventsLastArrivalTime** uppdateras med varje mottagen händelses köade tid.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Flyttas tiden framåt? Om faktiska händelser läses kanske inte skiljetecken utfärdas.

*   **InputEventsLastPunctuationTime** anger när ett skiljetecken utfärdades för att se till att tiden går framåt. Om interpunktion inte har utfärdats kan data flödet blockeras.
 
### <a name="are-there-any-errors-in-the-input"></a>Finns det några fel i indatamängden?

*   **InputEventsEventDataNullTotal** är antalet händelser som innehåller null-data.
*   **InputEventsSerializerErrorsTotal** är ett antal händelser som inte kunde deserialiseras korrekt.
*   **InputEventsDegradedTotal** är ett antal händelser som hade ett annat problem än med deserialisering.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Släpps eller justeras händelser?

*   **InputEventsEarlyTotal** är antalet händelser som har en program tids stämpling före den övre gränsen.
*   **InputEventsLateTotal** är antalet händelser som har en tidsstämpel för program efter den övre gränsen.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** är antalet händelser som släppts före jobbets start tid.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Faller vi bakom att läsa data?

*   **Inloggade inloggade händelser (totalt)** visar hur många meddelanden som behöver läsas för Event Hubs och Azure IoT Hub indata. När det här värdet är större än 0 innebär det att jobbet inte kan bearbeta data så snabbt som det kommer i. I det här fallet kan du behöva öka antalet enheter för strömning och/eller se till att jobbet kan vara parallellt. Du kan se mer information på det här på  [sidan Query parallellisering](./stream-analytics-parallelization.md). 


## <a name="get-help"></a>Få hjälp
Om du behöver ytterligare hjälp kan du prova vår [sida om Microsoft Q&en fråga för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Språk referens för Stream Analytics-fråga](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Stream Analytics hanterings REST API](/rest/api/streamanalytics/)