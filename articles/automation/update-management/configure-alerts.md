---
title: Skapa aviseringar för Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du konfigurerar Azure-aviseringar för att meddela om status för uppdaterings bedömningar eller distributioner.
services: automation
ms.subservice: update-management
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 74207fe088034ff8d102fb2254d8ab78a6d57671
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579707"
---
# <a name="how-to-create-alerts-for-update-management"></a>Skapa aviseringar för Uppdateringshantering

Aviseringar i Azure meddelar dig om resultat från Runbook-jobb, service Health-problem eller andra scenarier som är relaterade till ditt Automation-konto. Azure Automation innehåller inte förkonfigurerade aviserings regler, men du kan skapa egna baserat på de data som genereras. Den här artikeln innehåller vägledning om hur du skapar aviserings regler med hjälp av måtten som ingår i Uppdateringshantering.

## <a name="available-metrics"></a>Tillgängliga mått

Azure Automation skapar två distinkta plattforms mått som är relaterade till Uppdateringshantering som samlas in och vidarebefordras till Azure Monitor. Måtten är tillgängliga för analys med [Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md) och för aviseringar med hjälp av en [varnings regel för mått](../../azure-monitor/alerts/alerts-metric.md).

De två mät värdena som genereras är:

* Antal datorspecifika körningar av uppdateringsdistributionen
* Antal körningar av uppdateringsdistributionen

När de används för aviseringar stöder båda måtten dimensioner som har ytterligare information som kan hjälpa dig att begränsa aviseringen till en enskild uppdaterings distributions information. I följande tabell visas information om mått och dimensioner som är tillgängliga när du konfigurerar en avisering.

|Signalnamn|Dimensioner|Description
|---|---|---|
|`Total Update Deployment Runs`|– Namnet på uppdateringsdistributionen<br>– Status | Aviseringar om den övergripande statusen för en uppdaterings distribution.|
|`Total Update Deployment Machine Runs`|– Namnet på uppdateringsdistributionen</br>– Status</br>– Måldator</br>-Uppdatera körnings-ID för distribution    |Aviseringar om status för en uppdaterings distribution som är riktad mot specifika datorer.|

## <a name="create-alert"></a>Skapa avisering

Följ stegen nedan för att ställa in aviseringar så att du kan se status för en uppdaterings distribution. Om du är nybörjare på Azure-aviseringar kan du läsa [Översikt över Azure-aviseringar](../../azure-monitor/alerts/alerts-overview.md).

1. I ditt Automation-konto väljer du **aviseringar** under **övervakning** och väljer sedan **ny aviserings regel**.

2. Ditt Automation-konto har redan valts som resurs på sidan **skapa aviserings regel** . Om du vill ändra det väljer du **Redigera resurs**.

3. På sidan Välj en resurs väljer du **Automation-konton** i list rutan **Filtrera efter resurs typ** .

4. Välj det Automation-konto som du vill använda och välj sedan **slutförs**.

5. Välj **Lägg till villkor** för att välja den signal som passar ditt krav.

6. För en dimension väljer du ett giltigt värde i listan. Om det värde som du vill använda inte finns i listan väljer du **\+** bredvid dimensionen och skriver in det anpassade namnet. Välj sedan det värde som du vill söka efter. Om du vill välja alla värden för en dimension väljer du knappen **Välj \*** . Om du inte väljer ett värde för en dimension ignorerar Uppdateringshantering dimensionen.

    ![Konfigurera signallogiken](./media/manage-updates-for-vm/signal-logic.png)

7. Under **aviserings logik** anger du värden i fälten **tids mängd** och **tröskelvärde** och väljer sedan **Slutför**.

8. På nästa sida anger du ett namn och en beskrivning för aviseringen.

9. Ange fältet **allvarlighets grad** till **information (allvarlighets grad 2)** för lyckad körning eller **information (allvarlighets grad 1)** för en misslyckad körning.

    ![Skärm bild som visar avsnittet Definiera aviserings information med fälten varnings regel namn, beskrivning och allvarlighets grad markerade.](./media/manage-updates-for-vm/define-alert-details.png)

10. Välj **Ja** om du vill aktivera varnings regeln.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurera åtgärds grupper för dina aviseringar

När du har konfigurerat dina aviseringar kan du konfigurera en åtgärds grupp, som är en grupp åtgärder som ska användas över flera aviseringar. Åtgärderna kan omfatta e-postmeddelanden, Runbooks, Webhooks och mycket mer. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../../azure-monitor/alerts/action-groups.md).

1. Välj en avisering och välj sedan **Skapa ny** under **Åtgärds grupper**.

2. Ange ett fullständigt namn och ett kort namn för åtgärds gruppen. Uppdateringshantering använder det korta namnet när meddelanden skickas med den angivna gruppen.

3. Under **åtgärder** anger du ett namn som anger åtgärden, till exempel **e-postavisering**.

4. För **Åtgärds typ** väljer du lämplig typ, till exempel **e-post/SMS/push/röst**.

5. Välj **Redigera information**.

6. Fyll i fönstret för åtgärds typen. Om du till exempel använder **e-post/SMS/push/röst** anger du ett åtgärds namn, markerar kryss rutan **e-** postadress, anger en giltig e-postadress och väljer sedan **OK**.

    ![Konfigurera en e-poståtgärdsgrupp](./media/manage-updates-for-vm/configure-email-action-group.png)

7. I fönstret Lägg till åtgärdsgrupp väljer du **OK**.

8. För e-postaviseringar kan du anpassa e-postmeddelandets ämne. Välj **Anpassa åtgärder** under **Skapa regel** och välj sedan **e-postämne**.

9. När du är klar väljer du **Skapa varningsregel**.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [aviseringar i Azure Monitor](../../azure-monitor/alerts/alerts-overview.md).

* Lär dig mer om [logg frågor](../../azure-monitor/logs/log-query-overview.md) för att hämta och analysera data från en Log Analytics-arbetsyta.

* Hantera [användning och kostnader med Azure Monitor loggar](../../azure-monitor/logs/manage-cost-storage.md) beskriver hur du styr dina kostnader genom att ändra din data lagrings period och hur du analyserar och varnar för din data användning.