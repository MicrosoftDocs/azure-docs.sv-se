---
title: Felsöka Azure Stream Analytics frågor lokalt med hjälp av jobb diagram i Visual Studio
description: I den här artikeln beskrivs fel sökning av frågor lokalt med hjälp av jobb diagram i Azure Stream Analytics verktyg för Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: d0e94fda1fb21be1a01516f4cecf657426ae867e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019456"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Felsöka Azure Stream Analytics frågor lokalt med hjälp av jobb diagram i Visual Studio

Jobb som inte returnerar resultat eller oväntade resultat är vanliga fel söknings scenarier för strömnings frågor. Du kan använda jobb diagrammet när du testar din fråga lokalt i Visual Studio för att undersöka den mellanliggande resultat uppsättningen och måtten för varje steg. Jobb diagram kan hjälpa dig att snabbt isolera orsaken till ett problem när du felsöker problem.

## <a name="debug-a-query-using-job-diagram"></a>Felsöka en fråga med hjälp av jobb diagram

Ett Azure Stream Analytics-skript används för att transformera indata till utdata. Jobb diagrammet visar hur data flödar från inmatnings källor (Händelsehubben, IoT Hub osv.) genom flera fråge steg och slutligen till att generera mottagare. Varje fråge steg mappas till en temporär resultat uppsättning som definierats i skriptet med hjälp av en `WITH` instruktion. Du kan visa data och mått för varje fråge steg i varje mellanliggande resultat uppsättning för att hitta källan till ett problem.

> [!NOTE]
> Det här jobb diagrammet visar endast data och mått för lokal testning i en enda nod. Den bör inte användas för prestanda justering och fel sökning.

### <a name="start-local-testing"></a>Starta lokal testning

Använd den här [snabb](stream-analytics-quick-create-vs.md) starten för att lära dig hur du skapar ett Stream Analytics jobb med Visual Studio eller [Exportera ett befintligt jobb till ett lokalt projekt](stream-analytics-vs-tools.md#export-jobs-to-a-project). Följ dessa [instruktioner](stream-analytics-live-data-local-testing.md)om du vill testa frågan med lokala indata. Om du vill testa med Live-ingångar går du vidare till nästa steg.

> [!NOTE]
> Om du exporterar ett jobb till ett lokalt projekt och vill testa mot en Live-indataströmmen måste du ange autentiseringsuppgifterna för alla indata igen.  

Välj indata och utdata från skript redigeraren och välj **Kör lokalt**. Jobb diagrammet visas på den högra sidan.

### <a name="view-the-intermediate-result-set"></a>Visa den mellanliggande resultat uppsättningen  

1. Välj steget fråga för att navigera till skriptet. Du dirigeras automatiskt till motsvarande skript i redigeraren till vänster.

   ![Jobb diagram navigera i skript](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Välj steget fråga och välj för **hands version** i dialog rutan visas. Resultat uppsättningen visas på en flik i det nedre resultat fönstret.

   ![Resultat för förhands granskning av jobb diagram](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Visa steg mått

I det här avsnittet ska du utforska de mått som är tillgängliga för varje del av diagrammet.

#### <a name="input-sources-live-stream"></a>Inmatade källor (direkt uppspelning)

![Jobb diagram Live ingångs källor](./media/debug-locally-using-job-diagram/live-input.png)

|Mått|Beskrivning|
|-|-|
|**TaxiRide**| Namnet på indatamängden.|
|**Händelsehubb** | Typ av indatakälla.|
|**Händelser**|Antalet lästa händelser.|
|**Eftersläpande händelse källor**|Hur många fler meddelanden som behöver läsas för Event Hubs och IoT Hub indata.|
|**Händelser i byte**|Antalet lästa byte.|
| **Degraderade händelser**|Antalet händelser som hade ett annat problem än med deserialisering.|
|**Tidiga händelser**| Antalet händelser som har en program tids stämpling före den övre gränsen.|
|**Sena händelser**| Antalet händelser som har en tidsstämpel för program efter den övre gränsen.|
|**Händelsekällor**| Antalet data enheter som lästs. Till exempel antalet blobbar.|

#### <a name="input-sources-local-input"></a>Inmatade källor (lokala ingångar)

![Jobb diagram lokala ingångs källor](./media/debug-locally-using-job-diagram/local-input.png)

|Mått|Beskrivning|
|-|-|
|**TaxiRide**| Namnet på indatamängden.|
|**Radantal**| Antalet rader som genereras från steget.|
|**Datavolym**| Storleken på data som genereras från det här steget.|
|**Lokal indatamängd**| Använd lokala data som indata.|

#### <a name="query-steps"></a>Frågesteg

![Frågeresultat för jobb diagram](./media/debug-locally-using-job-diagram/query-step.png)

|Mått|Beskrivning|
|-|-|
|**TripData**|Namnet på den tillfälliga resultat uppsättningen.|
|**Radantal**| Antalet rader som genereras från steget.|
|**Datavolym**| Storleken på data som genereras från det här steget.|
  
#### <a name="output-sinks-live-output"></a>Utgående handfat (direktsända utdata)

![Jobb diagram som visar de lokala sinkarna för utdata.](./media/debug-locally-using-job-diagram/live-output.png)

|Mått|Beskrivning|
|-|-|
|**regionaggEH**|Namnet på utdata.|
|**Händelser**|Antalet händelser som ska matas till sinks.|

#### <a name="output-sinks-local-output"></a>Utgående Sinks (lokala utdata)

![Data mottagare för lokalt utgående jobb diagram](./media/debug-locally-using-job-diagram/local-output.png)

|Mått|Beskrivning|
|-|-|
|**regionaggEH**|Namnet på utdata.|
|**Lokal utdata**| Resultat av utdata till en lokal fil.|
|**Radantal**| Antalet utgående rader i den lokala filen.|
|**Datavolym**| Storleken på datautdata till den lokala filen.|

### <a name="close-job-diagram"></a>Stäng jobb diagram

Om du inte behöver jobb diagrammet längre väljer du **Stäng** i det övre högra hörnet. När du har stängt fönstret diagram måste du starta lokal testning igen för att se det.

### <a name="view-job-level-metrics-and-stop-running"></a>Visa Mät värden för jobb nivå och sluta köra

Andra mått på jobb nivå visas i pop up-konsolen. Tryck på **CTRL + C** i konsolen om du vill stoppa jobbet.

![Jobb diagram stoppa jobb](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Begränsningar

* Power BI och Azure Data Lake Storage Gen1 utgående mottagare stöds inte på grund av begränsningar för autentiserings modellen.

* Endast moln indatatyper har stöd för [tids principer](./stream-analytics-time-handling.md) , medan alternativ för lokal indatamängd inte gör det.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Stream Analytics jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Använda Visual Studio för att visa Azure Stream Analytics-jobb](stream-analytics-vs-tools.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)](stream-analytics-live-data-local-testing.md)