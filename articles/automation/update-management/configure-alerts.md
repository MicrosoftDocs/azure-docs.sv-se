---
title: Skapa aviseringar för Azure Automation Uppdateringshantering
description: Den här artikeln beskriver hur du konfigurerar Azure-aviseringar för att meddela om status för uppdaterings bedömningar eller distributioner.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601429"
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

1. Ditt Automation-konto har redan valts som resurs på sidan **skapa aviserings regel** . Om du vill ändra det väljer du **Redigera resurs**.

1. På sidan Välj en resurs väljer du **Automation-konton** i list rutan **Filtrera efter resurs typ** .

1. Välj det Automation-konto som du vill använda och välj sedan **slutförs**.

1. Välj **Lägg till villkor** för att välja den signal som passar ditt krav.

1. För en dimension väljer du ett giltigt värde i listan. Om det värde som du vill använda inte finns i listan väljer du **\+** bredvid dimensionen och skriver in det anpassade namnet. Välj sedan det värde som du vill söka efter. Om du vill välja alla värden för en dimension väljer du knappen **Välj \*** . Om du inte väljer ett värde för en dimension ignorerar Uppdateringshantering dimensionen.

    ![Konfigurera signallogiken](./media/manage-updates-for-vm/signal-logic.png)

1. Under **aviserings logik** anger du värden i fälten **tids mängd** och **tröskelvärde** och väljer sedan **Slutför**.

1. På nästa sida anger du ett namn och en beskrivning för aviseringen.

1. Ange fältet **allvarlighets grad** till **information (allvarlighets grad 2)** för lyckad körning eller **information (allvarlighets grad 1)** för en misslyckad körning.

    ![Skärm bild som visar avsnittet Definiera aviserings information med fälten varnings regel namn, beskrivning och allvarlighets grad markerade.](./media/manage-updates-for-vm/define-alert-details.png)

1. Välj **Ja** om du vill aktivera varnings regeln.

## <a name="configure-action-groups-for-your-alerts"></a>Konfigurera åtgärds grupper för dina aviseringar

När du har konfigurerat dina aviseringar kan du konfigurera en åtgärds grupp, som är en grupp åtgärder som ska användas över flera aviseringar. Åtgärderna kan omfatta e-postmeddelanden, Runbooks, Webhooks och mycket mer. Mer information om åtgärds grupper finns i [skapa och hantera åtgärds grupper](../../azure-monitor/alerts/action-groups.md).

1. Välj en avisering och välj sedan **Lägg till åtgärds grupper** under **åtgärder**. Då visas fönstret **Välj en åtgärds grupp att koppla till den här varnings regeln** .

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="Användning och uppskattade kostnader.":::

1. Markera kryss rutan för den åtgärds grupp som ska bifogas och tryck på Välj.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [aviseringar i Azure Monitor](../../azure-monitor/alerts/alerts-overview.md).

* Lär dig mer om [logg frågor](../../azure-monitor/logs/log-query-overview.md) för att hämta och analysera data från en Log Analytics-arbetsyta.

* Hantera [användning och kostnader med Azure Monitor loggar](../../azure-monitor/logs/manage-cost-storage.md) beskriver hur du styr dina kostnader genom att ändra din data lagrings period och hur du analyserar och varnar för din data användning.