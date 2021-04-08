---
title: Visualiseringar för program ändrings analys – Azure Monitor
description: Lär dig hur du använder visualiseringar i program ändrings analys i Azure Monitor.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 8319885de26bf79f5e402c4d06b29e9dd94894de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655859"
---
# <a name="visualizations-for-application-change-analysis-preview"></a>Visualiseringar för program ändrings analys (för hands version)

## <a name="standalone-ui"></a>Fristående gränssnitt

I Azure Monitor finns det ett fristående fönster för ändrings analys som visar alla ändringar med insikter i program beroenden och resurser.

Sök efter ändrings analys i Sök fältet på Azure Portal för att starta upplevelsen.

![Skärm bild av sökning av ändrings analys i Azure Portal](./media/change-analysis/search-change-analysis.png)

Alla resurser under en vald prenumeration visas med ändringar från de senaste 24 timmarna. Alla ändringar visas med gammalt värde och nytt värde för att ge insikter på ett ögonblick.

![Skärm bild av bladet för ändrings analys i Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Klicka i en ändring om du vill visa fullständig Resource Manager-kodfragment och andra egenskaper.

![Skärm bild av ändrings information](./media/change-analysis/change-details.png)

Använd knappen Skicka feedback eller e-post för all feedback changeanalysisteam@microsoft.com .

![Skärm bild av knappen feedback på fliken Ändra analys](./media/change-analysis/change-analysis-feedback.png)

### <a name="multiple-subscription-support"></a>Stöd för flera prenumerationer

Användar gränssnittet har stöd för att välja flera prenumerationer för att Visa resurs ändringar. Använd prenumerations filtret:

![Skärm bild av prenumerations filter som stöder val av flera prenumerationer](./media/change-analysis/multiple-subscriptions-support.png)


## <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Program ändrings analys i verktyget diagnostisera och lösa problem

Program ändrings analys är en fristående detektor i webbappen diagnostisera och lösa problem verktyg. Den sammanställs också i **program krascher** och i **webb program**. När du anger verktyget diagnostisera och lösa problem registreras **Microsoft. ChangeAnalysis** Resource Provider automatiskt. Följ de här instruktionerna för att aktivera webb program i gäst ändrings spårning.

1. Välj **tillgänglighet och prestanda**.

    ![Skärm bild av fel söknings alternativen "tillgänglighet och prestanda"](./media/change-analysis/availability-and-performance.png)

2. Välj **program ändringar**. Funktionen är också tillgänglig i **program krascher**.

   ![Skärm bild av knappen "program krascher"](./media/change-analysis/application-changes.png)

3. Länken leder till program ändrings analysens användar gränssnitt som är begränsat till webbappen. Om webb program i gäst ändrings spårning inte är aktive rad, följer du banderollen för att hämta ändringar av fil-och app-inställningar.

   ![Skärm bild av alternativen för "program krascher"](./media/change-analysis/enable-changeanalysis.png)

4. Aktivera **ändrings analys** och välj **Spara**. Verktyget visar alla webbappar under en App Service plan. Du kan använda plan nivå växeln för att aktivera ändrings analyser för alla webbappar under en plan.

    ![Skärm bild av användar gränssnittet "Aktivera ändrings analys"](./media/change-analysis/change-analysis-on.png)

5. Ändrings data finns också i Välj identifierings program för **webbappar ned** och **programkrascher** . Du ser en graf som sammanfattar typen av ändringar över tid tillsammans med information om dessa ändringar. Som standard visas ändringar under de senaste 24 timmarna för att hjälpa till med omedelbara problem.

     ![Skärm bild av vyn ändra diff](./media/change-analysis/change-view.png)

## <a name="diagnose-and-solve-problems-tool"></a>Diagnostikverktyg för att diagnostisera och lösa problem
Ändrings analys är tillgängligt som ett inblicks kort i diagnostisera och lösa problem verktyg. Om det uppstår problem med en resurs och det finns ändringar som upptäckts under de senaste 72 timmarna, visas antalet ändringar i insikts kortet. Om du klickar på länken Visa ändrings information leder det till den filtrerade vyn från det fristående användar gränssnittet för ändrings analys.

![Skärm bild av visning av ändrings insikter i diagnostisera och lösa problem verktyget.](./media/change-analysis/change-insight-diagnose-and-solve.png)



## <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostisera och lösa problem med den virtuella datorn

Gå till diagnostisera och lösa problem verktyg för en virtuell dator.  Gå till **fel söknings verktyg**, bläddra nedåt på sidan och välj **analysera nya ändringar** för att visa ändringar på den virtuella datorn.

![Skärm bild av den virtuella datorn diagnostisera och lösa problem](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Ändrings analys i fel söknings verktyg](./media/change-analysis/analyze-recent-changes.png)

## <a name="activity-log-change-history"></a>Ändrings historik för aktivitets logg

Funktionen [Visa ändrings historik](../essentials/activity-log.md#view-change-history) i aktivitets loggen anropar program ändrings analys tjänstens Server del för att få ändringar som är associerade med en åtgärd. **Ändrings historik** som används för att anropa [Azures resurs diagram](../../governance/resource-graph/overview.md) direkt, men utbytt Server delen för att anropa program ändrings analys så att ändringar som returneras inkluderar resurs nivå ändringar från [Azure Resource graph](../../governance/resource-graph/overview.md), resurs egenskaper från [Azure Resource Manager](../../azure-resource-manager/management/overview.md)och ändringar i gästen från PaaS-tjänster som app Services webbappen. För att program ändrings analys tjänsten ska kunna söka efter ändringar i användarnas prenumerationer måste du registrera en resurs leverantör. Första gången du öppnar fliken **ändrings historik** börjar verktyget automatiskt att registrera **Microsoft. ChangeAnalysis** Resource Provider. Efter registreringen kommer ändringar från **Azure Resource Graph** att bli tillgängliga omedelbart och de senaste 14 dagarna. Ändringar från andra källor blir tillgängliga efter ~ 4 timmar efter det att prenumerationen har publicerats.

![Integration av aktivitets loggens ändrings historik](./media/change-analysis/activity-log-change-history.png)

## <a name="vm-insights-integration"></a>Integrering av VM Insights

Användare med aktiverade [VM-insikter](../vm/vminsights-overview.md) kan se vad som har ändrats på sina virtuella datorer som kan ha orsakat några toppar i ett mått diagram, till exempel processor eller minne. Ändrings data integreras i navigerings fältet för VM Insights-sidan. Användaren kan visa om några ändringar har gjorts i den virtuella datorn och välja **Undersök ändringar** för att Visa ändrings information i det fristående användar gränssnittet för program ändrings analys.

[![Integrering av VM Insights](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem i ändrings analys](change-analysis-troubleshoot.md)
