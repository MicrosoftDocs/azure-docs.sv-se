---
title: Felsöka Azure Stream Analytics frågor lokalt med hjälp av jobb diagram i Visual Studio Code
description: Den här artikeln beskriver hur du felsöker frågor lokalt med hjälp av jobb diagram i Azure Stream Analytics-tillägget för Visual Studio Code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 25ad696ad345fbf672f6bf26eb3f35a13fb03ea5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019507"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio-code"></a>Felsöka Azure Stream Analytics frågor lokalt med hjälp av jobb diagram i Visual Studio Code

Strömmande jobb som inte returnerar något resultat eller oväntade resultat behöver ofta fel sökning. Visual Studio Code-tillägget för Azure Stream Analytics integrerar jobb diagram, mått, diagnostikloggar och mellanliggande resultat så att du snabbt kan isolera källan till ett problem. Du kan använda jobb diagrammet när du testar din fråga lokalt för att undersöka den mellanliggande resultat uppsättningen och måtten för varje steg.

## <a name="debug-a-query-using-job-diagram"></a>Felsöka en fråga med hjälp av jobb diagram

Ett Azure Stream Analytics-skript används för att transformera indata till utdata. Jobb diagrammet visar hur data flödar från inmatnings källor, som Händelsehubben eller IoT Hub, genom flera fråge steg till att generera mottagare. Varje fråge steg mappas till en temporär resultat uppsättning som definierats i skriptet med hjälp av en `WITH` instruktion. Du kan visa data och mått för varje fråge steg i varje mellanliggande resultat uppsättning för att hitta källan till ett problem.

> [!NOTE]
> Det här jobb diagrammet visar endast data och mått för lokal testning i en enda nod. Den bör inte användas för prestanda justering och fel sökning.

### <a name="start-local-testing"></a>Starta lokal testning

Använd den här [snabb](quick-create-visual-studio-code.md) starten för att lära dig hur du skapar ett Stream Analytics jobb med Visual Studio Code eller [Exportera ett befintligt jobb till ett lokalt projekt](visual-studio-code-explore-jobs.md). Autentiseringsuppgifter för indata och utdata fylls i automatiskt för exporterade jobb.

Följ dessa [instruktioner](visual-studio-code-local-run.md)om du vill testa frågan med lokala indata. Om du vill testa med Live-indatamängden [konfigurerar](stream-analytics-add-inputs.md) du inflyttningen till nästa steg. 

Öppna skript filen *\. asaql* och välj **Kör lokalt**. Välj sedan **Använd lokal Indatatyp** eller **Använd Live-ininformation**. Jobb diagrammet visas till höger i fönstret.

### <a name="view-the-output-and-intermediate-result-set"></a>Visa utdata och mellanliggande resultat uppsättning  

1. Alla utskrifter visas i resultat fönstret längst ned till höger i Visual Studio Code-fönstret.

   > [!div class="mx-imgBorder"]
   > ![Resultat för jobb utdata](./media/debug-locally-using-job-diagram-vs-code/job-output-results.png)

2. Välj steget fråga för att navigera till skriptet. Du dirigeras automatiskt till motsvarande skript i redigeraren till vänster. Det mellanliggande resultatet visas i resultat fönstret längst ned till höger i Visual Studio Code-fönstret.

   > [!div class="mx-imgBorder"]
   > ![Resultat för förhands granskning av jobb diagram](./media/debug-locally-using-job-diagram-vs-code/preview-result.png)

### <a name="view-metrics"></a>Visa mått

I det här avsnittet ska du utforska de mått som är tillgängliga för varje del av diagrammet.

1. Välj fliken **mått** bredvid fliken **resultat** längst ned till höger i Visual Studio Code-fönstret.

2. Välj **jobb** i list rutan. Du kan välja ett tomt utrymme i en graf-nod för att navigera till Mät värden på jobb nivå. Den här vyn innehåller alla mått, som uppdateras var tionde sekund när jobbet körs. Du kan markera eller avmarkera måtten på höger sida om du vill visa dem i diagrammen.

   > [!div class="mx-imgBorder"]
   > ![Mät värden för jobb diagram](./media/debug-locally-using-job-diagram-vs-code/job-metrics.png)

3. Välj indata-källans namn i list rutan för att se indata-mått. Indatakällan i skärm bilden nedan kallas *citat tecken*. Mer information om ingående mått finns i [förstå Stream Analytics jobb övervakning och övervaka frågor](stream-analytics-monitoring.md).

   > [!div class="mx-imgBorder"]
   > ![Mät värden för jobb diagram](./media/debug-locally-using-job-diagram-vs-code/input-metrics.png)

4. Välj ett fråge steg från jobb diagrammet eller Välj steg namnet i list rutan för att se steg nivå mått. Vattenstämpelns fördröjning är det enda tillgängliga steg måttet.

   > [!div class="mx-imgBorder"]
   > ![Steg mått](./media/debug-locally-using-job-diagram-vs-code/step-metrics.png)

5. Välj utdata i diagrammet eller i list rutan för att se utdata relaterade mått. Mer information om utmatnings mått finns i [förstå Stream Analytics jobb övervakning och övervaka frågor](stream-analytics-monitoring.md). Direkt sändnings mottagare stöds inte.

   > [!div class="mx-imgBorder"]
   > ![Utmatnings mått](./media/debug-locally-using-job-diagram-vs-code/output-metrics.png)

### <a name="view-diagnostic-logs"></a>Visa diagnostikloggar

Diagnostikloggar för jobb nivå innehåller diagnostikinformation för inmatnings data källor och utgående mottagare. När du väljer en inmatnings nod eller en utdataparameter visas bara motsvarande loggar. Inga loggar visas om du väljer ett fråge steg. Du kan hitta alla loggar på jobb nivån och du kan filtrera loggarna efter allvarlighets grad och tid.

   > [!div class="mx-imgBorder"]
   > ![Diagnostikloggar](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs.png)

   Välj en loggpost för att se hela meddelandet.

   > [!div class="mx-imgBorder"]
   > ![Diagnostiskt loggnings meddelande](./media/debug-locally-using-job-diagram-vs-code/diagnostic-logs-message.png)


## <a name="other-job-diagram-features"></a>Andra jobb diagram funktioner

Du kan välja **stoppa** eller **pausa** från verktygsfältet efter behov. När jobbet har pausats kan du återuppta det från senaste utdata.

> [!div class="mx-imgBorder"]
> ![Stoppa eller pausa jobb](./media/debug-locally-using-job-diagram-vs-code/stop-pause-job.png)

Välj **jobb Sammanfattning** längst upp till höger i jobb diagrammet för att se egenskaper och konfigurationer för ditt lokala jobb.

> [!div class="mx-imgBorder"]
> ![Översikt över lokalt jobb](./media/debug-locally-using-job-diagram-vs-code/job-summary.png)

## <a name="limitations"></a>Begränsningar

* Direkt sändnings mottagare stöds inte i lokal körning.

* Körning av jobb lokalt med JavaScript-funktionen stöds bara i Windows-operativsystemet.

* Anpassad kod och Azure Machine Learning funktioner i C# stöds inte. 

* Endast moln indatatyper har stöd för [tids principer](./stream-analytics-time-handling.md) , medan alternativ för lokal indatamängd inte är det.

## <a name="next-steps"></a>Nästa steg

* [Snabb start: skapa ett Stream Analytics jobb med Visual Studio Code](quick-create-visual-studio-code.md)
* [Utforska Azure Stream Analytics med Visual Studio Code](visual-studio-code-explore-jobs.md)
* [Testa Stream Analytics frågor lokalt med exempel data med Visual Studio Code](visual-studio-code-local-run.md)
* [Testa Azure Stream Analytics jobb lokalt med Live-indatamängden med Visual Studio Code](visual-studio-code-local-run-live-input.md)