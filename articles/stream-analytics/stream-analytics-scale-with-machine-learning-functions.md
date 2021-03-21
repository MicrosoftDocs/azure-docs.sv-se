---
title: Skala Machine Learning Studio (klassiska) funktioner i Azure Stream Analytics
description: I den här artikeln beskrivs hur du skalar Stream Analytics jobb som använder Machine Learning Studio (klassiska) funktioner, genom att konfigurera partitionering och strömma enheter.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 1ee1411aba7724d76ed8626de9b8b038d02339dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574262"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Skala ditt Stream Analytics jobb med Azure Machine Learning Studio (klassiska) funktioner

> [!TIP]
> Vi rekommenderar starkt att du använder [Azure Machine Learning UDF: er](machine-learning-udf.md) i stället för Azure Machine Learning Studio (klassisk) UDF för bättre prestanda och tillförlitlighet.

Den här artikeln beskriver hur du effektivt skalar Azure Stream Analytics jobb som använder Azure Machine Learning Studio (klassiska) funktioner. Information om hur du skalar Stream Analytics jobb i allmänhet finns i artikeln [skalförändra jobb](stream-analytics-scale-jobs.md).

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>Vad är en Studio-funktion (klassisk) i Stream Analytics?

En Machine Learning Studio (klassisk) funktion i Stream Analytics kan användas som ett vanligt funktions anrop i Stream Analytics frågespråket. Dessa funktions anrop är dock i själva verket Studio (klassiska) webb tjänst begär Anden.

Du kan förbättra data flödet för webb tjänst begär anden i Studio (klassisk) genom att "Gruppera" flera rader i samma webb tjänst-API-anrop. Den här grupperingen kallas för en mini-batch. Mer information finns i [Azure Machine Learning Studio (klassiska) webb tjänster](../machine-learning/classic/consume-web-services.md). Stöd för Studio (klassisk) i Stream Analytics.

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>Konfigurera ett Stream Analytics jobb med Studio-funktioner (klassisk)

Det finns två parametrar för att konfigurera den Studio-funktion (klassisk) som används av ditt Stream Analytics jobb:

* Batchstorleken för Studio (klassisk)-funktions anrop.
* Antalet strömnings enheter (SUs) som har allokerats för Stream Analyticss jobbet.

Om du vill fastställa lämpliga värden för SUs bestämmer du om du vill optimera svars tiden för Stream Analytics jobb eller genom strömningen för varje SU. SUs kan alltid läggas till i ett jobb för att öka data flödet för en väl partitionerad Stream Analytics fråga. Ytterligare SUs ökar kostnaden för att köra jobbet.

Fastställ latens *toleransen* för ditt Stream Analytics jobb. Om du ökar batchstorleken ökar svars tiden för dina Studio (klassiska) förfrågningar och svars tiden för Stream Analytics jobbet.

Om du ökar batchstorleken kan Stream Analytics jobbet bearbeta **Fler händelser** med **samma antal** webb tjänst begär anden i Studio (klassisk). Ökningen av den klassiska webb tjänst svars tiden i Studio är vanligt vis under streck till ökningen av batchstorleken. 

Det är viktigt att tänka på den mest kostnads effektiva batchstorleken för en Studio (klassisk) webb tjänst i en specifik situation. Standard grupp storleken för webb tjänst begär Anden är 1000. Du kan ändra standard storleken med hjälp av [Stream Analytics REST API](/previous-versions/azure/mt653706(v=azure.100) "REST-API för Stream Analytics") eller [PowerShell-klienten för Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

När du har bestämt dig för en batchstorlek kan du ange antalet enheter för strömning (SUs) baserat på antalet händelser som funktionen måste bearbeta per sekund. Mer information om enheter för strömning finns i [Stream Analytics skalnings jobb](stream-analytics-scale-jobs.md).

Varje 6 SUs får 20 samtidiga anslutningar till webb tjänsten Studio (klassisk). Men 1 SU-jobb och 3 SU-jobb får 20 samtidiga anslutningar.  

Om ditt program genererar 200 000 händelser per sekund och batchstorleken är 1000, är den resulterande webb tjänstens svars tid 200 MS. Den här frekvensen innebär att varje anslutning kan göra fem begär anden till webb tjänsten Studio (klassisk) varje sekund. Med 20 anslutningar kan Stream Analytics-jobbet bearbeta 20 000-händelser i 200 MS-och 100 000-händelser på en sekund.

För att bearbeta 200 000-händelser per sekund behöver Stream Analyticss jobbet 40 samtidiga anslutningar, som kommer ut till 12 SUs. I följande diagram illustreras begär Anden från Stream Analytics-jobbet till den klassiska webb tjänst slut punkten för Studio (klassisk). varje 6 SUs har 20 samtidiga anslutningar till Studio-webbtjänsten (klassisk) med max.

![Skala Stream Analytics med Studio (klassisk) funktioner två jobb exempel](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skala Stream Analytics med Studio (klassisk) funktioner två jobb exempel")

I allmänhet, ***B** _ för batchstorlek, _*_L_*_ för webb tjänstens svars tid vid batch-storlek B i millisekunder, är data flödet för ett Stream Analytics jobb med _ *_N_** SUS:

![Skala Stream Analytics med funktions formeln Studio (klassisk)](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Skala Stream Analytics med funktions formeln Studio (klassisk)")

Du kan också konfigurera "maximalt antal samtidiga anrop" i webb tjänsten Studio (klassisk). Vi rekommenderar att du anger den här parametern till det maximala värdet (200 för närvarande).

Mer information om den här inställningen finns i [skalnings artikeln för Machine Learning Studio (klassiska) webb tjänster](../machine-learning/classic/create-endpoint.md).

## <a name="example--sentiment-analysis"></a>Exempel – Attitydanalys
I följande exempel finns ett Stream Analytics jobb med funktionen sentiment Analysis Studio (klassisk), enligt beskrivningen i [själv studie kursen om Stream Analytics Machine Learning Studio (klassisk)](stream-analytics-machine-learning-integration-tutorial.md).

Frågan är en helt partitionerad fråga som följs av **sentiment** -funktionen, som visas i följande exempel:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Vi tar en titt på den konfiguration som krävs för att skapa ett Stream Analytics jobb, vilket gör sentiment analys av tweets med en hastighet av 10 000 tweets per sekund.

Kan det här Stream Analytics jobbet hantera trafiken med hjälp av 1 SU? Jobbet kan fortsätta med indatamängden med hjälp av standard-batchstorleken på 1000. Standard fördröjningen för webb tjänsten sentiment Analysis Studio (klassisk) (med en standard batchstorlek på 1000) skapar inte mer än en sekund svars tid.

Stream Analytics jobbets **övergripande** svars tid eller slut punkt till slut punkt brukar vara några sekunder. Ta en mer detaljerad titt på det här Stream Analyticss jobbet, *särskilt* Studio (klassiskt) funktions anrop. Med en batchstorlek på 1000 tar ett flöde med 10 000-händelser att ta cirka 10 förfrågningar till webb tjänsten. Även om det finns tillräckligt många samtidiga anslutningar för att hantera den här Indataporten med en SU.

Om frekvensen för inaktiva händelser ökar med 100x måste Stream Analyticss jobbet bearbeta 1 000 000 tweets per sekund. Det finns två alternativ för att uppnå den ökade skalningen:

1. Öka batchstorleken.
2. Partitionera indataströmmen för att bearbeta händelserna parallellt.

Med det första alternativet ökar jobb **svars tiden** .

Med det andra alternativet måste du etablera mer SUs om du vill ha fler samtidiga WebService-begäranden (klassiska Studio). Detta är ett större antal SUs-jobb, vilket ökar jobb **kostnaden**.

Nu ska vi titta på skalningen med följande tids fördröjnings mått för varje batchstorlek:

| Svarstid | Batchstorlek |
| --- | --- |
| 200 MS | 1000 – händelse grupper eller under |
| 250 MS | 5 000 – händelse batchar |
| 300 MS | 10 000 – händelse batchar |
| 500 ms | 25 000 – händelse batchar |

1. Med det första alternativet (**inte** etablering av mer SUS). Batchstorleken kan höjas till **25 000**. Om du ökar batchstorleken på det här sättet kan jobbet bearbeta 1 000 000-händelser med 20 samtidiga anslutningar till webb tjänsten Studio (klassisk) (med en svars tid på 500 ms per anrop). Ytterligare svars tid för Stream Analytics jobb på grund av sentiment-funktionen mot WebService-begärandena för Studio (klassisk) skulle öka från **200 MS** till **500 ms**. Batchstorleken **kan dock inte** ökas oändligt eftersom Studio (klassiska) webb tjänster kräver en begärans nytto Last storlek 4 MB eller mindre och webb tjänst begär timeout efter 100 sekunders åtgärd.
1. Med det andra alternativet lämnas batchstorleken på 1000, med 200-MS-webbtjänstens svars tid, var 20 samtidiga anslutningar till webb tjänsten att kunna bearbeta 1000 * 20 * 5 händelser = 100 000 per sekund. För att bearbeta 1 000 000-händelser per sekund behöver jobbet 60 SUs. Jämfört med det första alternativet skulle Stream Analytics jobb göra fler webb tjänst begär Anden, vilket i sin tur genererar en ökad kostnad.

Nedan visas en tabell för data flödet för Stream Analytics jobb för olika SUs-och batch-storlekar (i antal händelser per sekund).

| batchstorlek (ML-latens) | 500 (200 MS) | 1 000 (200 MS) | 5 000 (250 MS) | 10 000 (300 MS) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2 500 |5 000 |20 000 |30 000 |50 000 |
| **3 SUs** |2 500 |5 000 |20 000 |30 000 |50 000 |
| **6 SUs** |2 500 |5 000 |20 000 |30 000 |50 000 |
| **12 SUs** |5 000 |10 000 |40 000 |60 000 |100 000 |
| **18 SUs** |7 500 |15 000 |60 000 |90 000 |150 000 |
| **24 SUs** |10 000 |20 000 |80 000 |120 000 |200 000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50 000 |200 000 |300 000 |500 000 |

Nu bör du redan ha en god förståelse för hur Studio (klassiska) funktioner i Stream Analytics fungerar. Du är förmodligen också medveten om att Stream Analytics jobbs "pull"-data från data källor och varje "pull" returnerar en batch med händelser för det Stream Analytics jobb som ska bearbetas. Hur påverkar den här pull-modellen de Studio (klassiska) webb tjänst begär Anden?

Normalt är batchstorleken som vi anger för Studio (klassisk) funktioner inte exakt delbar med antalet händelser som returneras av varje Stream Analytics jobb "pull". När detta inträffar anropas webb tjänsten Studio (klassisk) med del-batchar. Genom att använda del batchar slipper du ytterligare jobb fördröjning i sammanslagning av händelser från pull till pull.

## <a name="new-function-related-monitoring-metrics"></a>Nya funktions relaterade övervaknings mått
I övervaknings ytan i ett Stream Analytics jobb har tre ytterligare Function-relaterade mått lagts till. De är **funktions begär Anden**, **funktions händelser** och **misslyckade funktions begär Anden**, som visas i bilden nedan.

![Skala Stream Analytics med Studio (klassisk) funktions mått](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skala Stream Analytics med Studio (klassisk) funktions mått")

Definieras enligt följande:

**Funktions begär Anden**: antalet funktions begär Anden.

**Funktions händelser**: antalet händelser i funktions begär Anden.

**Misslyckade funktions begär Anden**: antal misslyckade funktions begär Anden.

## <a name="key-takeaways"></a>Key takeaways

Om du vill skala ett Stream Analytics jobb med Studio (klassiska) funktioner bör du tänka på följande faktorer:

1. Frekvensen för ingående händelser.
2. Den tolererade svars tiden för Stream Analytics jobb som körs (och därmed batch-storleken för de Studio (klassiska) webb tjänst begär Anden).
3. Den tillhandahållna Stream Analytics SUs och antalet WebService-begäranden för Studio (klassisk) (ytterligare funktions relaterade kostnader).

En helt partitionerad Stream Analytics-fråga användes som ett exempel. Om en mer komplex fråga krävs är webbplatsen [Microsoft Q&en fråga för Azure Stream Analytics](/answers/topics/azure-stream-analytics.html) en bra resurs för att få ytterligare hjälp från Stream Analyticss teamet.

## <a name="next-steps"></a>Nästa steg
Mer information om Stream Analytics finns i:

* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)
