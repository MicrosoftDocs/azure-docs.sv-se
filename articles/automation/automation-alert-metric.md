---
title: Övervaka Azure Automation runbooks med mått varningar
description: Den här artikeln beskriver hur du konfigurerar en mått avisering baserat på status för Runbook-slut för ande.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 3a52824a7030d78647d8a664819f439d92d7296d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "100593958"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Övervaka runbook-flöden med måttaviseringar

I den här artikeln får du lära dig hur du skapar en [mått avisering](../azure-monitor/alerts/alerts-metric-overview.md) baserat på status för Runbook-slut för ande.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com)

## <a name="create-alert"></a>Skapa avisering

Med aviseringar kan du definiera ett villkor för övervakning av och en åtgärd som ska vidtas när villkoret är uppfyllt.

1. Starta tjänsten Azure Automation i Azure Portal genom att klicka på **alla tjänster** och sedan söka efter och välja **Automation-konton**.

2. I listan över Automation-konton väljer du det konto som du vill skapa en avisering för. 

3. Under **övervakning** väljer du **aviseringar** och väljer sedan **+ ny varnings regel**. Omfattningen för målet är redan definierad och kopplad till ditt Automation-konto.

### <a name="configure-alert-criteria"></a>Konfigurera aviserings villkor

1. Klicka på **Välj villkor**. Välj **mått** för **signal typen** och välj **Total jobb** från listan.

2. Sidan **Konfigurera signal logik** är den plats där du definierar den logik som utlöser aviseringen. Under det historiska diagrammet visas två dimensioner, **Runbook-namn** och **status**. Dimensioner är olika egenskaper för ett mått som kan användas för att filtrera resultat. För **Runbook-namn** väljer du den Runbook som du vill Avisera om eller lämnar tomt för aviseringar på alla Runbooks. För **status** väljer du en status från List rutan som du vill övervaka för. Runbook-namn och status värden som visas i list rutan är bara för jobb som har körts den senaste veckan.

   Om du vill Avisera om en status eller Runbook som inte visas i list rutan klickar du på alternativet **Lägg till anpassat värde** bredvid dimensionen. Den här åtgärden öppnar en dialog ruta där du kan ange ett anpassat värde, som inte har spridits för den dimensionen nyligen. Om du anger ett värde som inte finns för en egenskap kommer aviseringen inte att utlösas.

   > [!NOTE]
   > Om du inte anger något namn på **Runbook-namn** dimensionen, om det finns Runbooks som uppfyller status villkoren, som innehåller dolda system-Runbooks, får du en avisering.

    Om du till exempel vill Avisera när en Runbook returnerar en _misslyckad_ status, förutom att ange Runbook-namnet för **status** dimensionen, kan du **inte** lägga till det anpassade dimension svärdet.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Ange anpassat dimensions värde" border="false":::

3. Under **aviserings logik** definierar du villkoret och tröskelvärdet för aviseringen. En för hands version av det definierade villkoret visas under.

4. Under **utvärdera baserat på** väljer du TimeSpan för frågan och hur ofta du vill att frågan ska köras. Om du till exempel väljer **över de senaste 5 minuterna** för **perioden** och **var 1 minut** för **frekvens**, söker aviseringen efter antalet Runbooks som uppfyller dina kriterier under de senaste 5 minuterna. Den här frågan körs varje minut och när aviserings kriterierna du definierade inte längre hittas i ett 5-minuters fönster, löses aviseringen. Klicka på **klart** när du är färdig.

   ![Välj en resurs för aviseringen](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Definiera åtgärden som ska vidtas

1. Under **Åtgärds grupp** väljer du **Ange åtgärds grupp**. En åtgärds grupp är en grupp med åtgärder som du kan använda i mer än en avisering. Dessa kan inkludera, men är inte begränsade till, e-postmeddelanden, Runbooks, Webhooks och många fler. Mer information om åtgärds grupper och steg för att skapa en som skickar ett e-postmeddelande finns i [skapa och hantera åtgärds grupper](../azure-monitor/alerts/action-groups.md).

### <a name="define-alert-details"></a>Definiera aviseringsinformationen

1. Ge aviseringen ett eget namn och en beskrivning under **aviserings regel information**. Ange **allvarlighets grad** som matchar varnings villkoret. Det finns fem allvarlighets grader mellan 0 och 5. Aviseringarna behandlas oberoende av allvarlighets graden. du kan matcha allvarlighets graden så att den matchar din affärs logik.

1. Som standard är regler aktiverade när du skapar, om du inte väljer **Nej** för alternativet **Aktivera varnings regeln när du skapar** den. För aviseringar som har skapats i inaktiverat tillstånd kan du aktivera dem i framtiden när du är klar. Välj **skapa aviserings regel** för att spara ändringarna.

## <a name="receive-notification"></a>Ta emot meddelande

När aviserings villkoret är uppfyllt kör åtgärds gruppen den definierade åtgärden. I den här artikeln är ett e-postmeddelande som skickas. Följande bild är ett exempel på ett e-postmeddelande som du får när aviseringen har utlösts:

![E-postavisering](./media/automation-alert-activity-log/alert-email.png)

När måttet inte längre är utanför det angivna tröskelvärdet inaktive ras aviseringen och åtgärds gruppen kör den definierade åtgärden. Om en e-poståtgärds typ väljs skickas en lösnings-e-postadress som talar om att den har lösts.

## <a name="next-steps"></a>Nästa steg

* Mer information finns i [använda en avisering för att utlösa en Azure Automation Runbook](automation-create-alert-triggered-runbook.md).
