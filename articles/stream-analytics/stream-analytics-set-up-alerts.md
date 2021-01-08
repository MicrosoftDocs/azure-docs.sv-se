---
title: Konfigurera övervaknings aviseringar för Azure Stream Analytics jobb
description: Den här artikeln beskriver hur du använder Azure Portal för att konfigurera övervakning och aviseringar för Azure Stream Analytics jobb.
author: jseb225
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 06/21/2019
ms.openlocfilehash: 7884f8baa24180fcb94f77a45c3457ba62d3f351
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018147"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Konfigurera aviseringar för Azure Stream Analytics jobb

Det är viktigt att övervaka ditt Azure Stream Analytics jobb så att jobbet körs kontinuerligt utan problem. I den här artikeln beskrivs hur du konfigurerar aviseringar för vanliga scenarier som ska övervakas. 

Du kan definiera regler för mått från operation loggar data via portalen och [program mässigt](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a).

## <a name="set-up-alerts-in-the-azure-portal"></a>Konfigurera aviseringar i Azure Portal
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Få aviseringar när ett jobb oväntat stoppas

I följande exempel visas hur du konfigurerar aviseringar för när jobbet övergår i fel tillstånd. Den här aviseringen rekommenderas för alla jobb.

1. Öppna det Stream Analytics jobb som du vill skapa en avisering för i Azure Portal.

2. Gå till avsnittet **övervakning** på sidan **jobb** .  

3. Välj **mått** och sedan **ny varnings regel**.

   ![Installation av Azure Portal Stream Analytics-varningar](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Ditt Stream Analytics jobb namn ska visas automatiskt under **resurs**. Klicka på **Lägg till villkor** och välj **alla administrativa åtgärder** under **Konfigurera signal logik**.

   ![Välj signal namn för Stream Analytics avisering](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Under **Konfigurera signal logik**, ändra **händelse nivå** till **alla** och ändra **status** till **misslyckades**. Lämna **händelsen som initierad av** tomt och välj **färdig**.

   ![Konfigurera signal logik för Stream Analytics avisering](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Välj en befintlig åtgärds grupp eller skapa en ny grupp. I det här exemplet skapades en ny åtgärds grupp med namnet **TIDashboardGroupActions** med en **e-** poståtgärd som skickar ett e-postmeddelande till användare med rollen **ägare** Azure Resource Manager.

   ![Konfigurera en avisering för ett Azure streaming Analytics-jobb](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. **Resurs**, **villkor** och **Åtgärds grupper** måste ha en post. Observera att de villkor som definierats måste uppfyllas för att aviseringarna ska kunna utlösas. Du kan till exempel mäta medelvärdet för ett mått för de senaste 15 minuterna, var femte minut.

   ![Skärm bild som visar dialog rutan skapa regel med resurs, villkor och ÅTGÄRDs grupp.](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Lägg till en **varnings regel namn**, **Beskrivning** och din **resurs grupp** i **aviserings informationen** och klicka på **skapa aviserings regel** för att skapa regeln för ditt Stream Analytics jobb.

   ![Skärm bild som visar dialog rutan skapa regel med AVISERINGs information.](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scenarier att övervaka

Följande aviseringar rekommenderas för att övervaka Stream Analytics jobbets prestanda. Dessa mått bör utvärderas varje minut under den senaste 5-minuters perioden.

|Mått|Villkor|Tids mängd|Tröskelvärde|Korrigerande åtgärder|
|-|-|-|-|-|
|SU%-användning|Större än|Maximal|80|Det finns flera faktorer som ökar SU-användningen. Du kan skala med Query parallellisering eller öka antalet enheter för strömning. Mer information finns i [Utnyttja frågeparallellisering i Azure Stream Analytics](stream-analytics-parallelization.md).|
|Körnings fel|Större än|Totalt|0|Granska aktivitets-eller resurs loggarna och gör lämpliga ändringar i indata, frågor eller utdata.|
|Fördröjning för vattenstämpel|Större än|Maximal|När genomsnitts värdet för det här måttet under de senaste 15 minuterna är större än för sent tillkommer tolerans (i sekunder). Om du inte har ändrat den sena införsel toleransen är standardvärdet 5 sekunder.|Försök att öka antalet SUs-eller Parallel-frågor. Mer information om SUs finns i [förstå och justera strömnings enheter](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Mer information om hur du parallellt din fråga finns i använda [query parallellisering i Azure Stream Analytics](stream-analytics-parallelization.md).|
|Fel vid deserialisering av indataport|Större än|Totalt|0|Granska aktivitets-eller resurs loggarna och gör lämpliga ändringar i indatamängden. Mer information om resurs loggar finns i [felsöka Azure Stream Analytics att använda resurs loggar](stream-analytics-job-diagnostic-logs.md)|

## <a name="next-steps"></a>Nästa steg

* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)