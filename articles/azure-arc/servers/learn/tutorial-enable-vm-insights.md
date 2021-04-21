---
title: Självstudie – Övervaka en hybriddator med Azure Monitor VM-insikter
description: Lär dig hur du samlar in och analyserar data från en hybriddator i Azure Monitor.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834272"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Självstudie: Övervaka en hybriddator med VM-insikter

[Azure Monitor](../../../azure-monitor/overview.md) kan samla in data direkt från dina hybriddatorer till en Log Analytics-arbetsyta för detaljerad analys och korrelation. Detta innebär vanligtvis att Log [Analytics-agenten](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) installeras på datorn med hjälp av ett skript, manuellt eller automatiskt, enligt dina standarder för konfigurationshantering. Arc-aktiverade servrar introducerade nyligen stöd för att installera VM-tilläggen Log Analytics och Dependency [Agent](../manage-vm-extensions.md) för Windows och Linux, vilket gjorde det möjligt för [VM-insikter](../../../azure-monitor/vm/vminsights-overview.md) att samla in data från virtuella datorer som inte kommer från Azure.

Den här självstudien visar hur du konfigurerar och samlar in data från dina Linux- eller Windows-datorer genom att aktivera VM-insikter genom att följa en förenklad uppsättning steg, vilket effektiviserar upplevelsen och tar en kortare tid.  

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Funktionen för VM-tillägg är endast tillgänglig i listan över [regioner som stöds.](../overview.md#supported-regions)

* Se [Operativsystem som stöds för](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) att säkerställa att de servrars operativsystem som du aktiverar stöds av VM-insikter.

* Granska brandväggskraven för Log Analytics-agenten i [Översikt över Log Analytics-agenten.](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements) VM-insikter Map Dependency Agent överför inte några data i sig och kräver inte några ändringar i brandväggar eller portar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-vm-insights"></a>Aktivera insikter om virtuella datorer

1. Starta Azure Arc-tjänsten i Azure Portal genom att klicka på **Alla** tjänster och sedan söka **efter och välja Datorer - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Sök efter Arc-aktiverade servrar i Alla tjänster" border="false":::

1. På sidan **Datorer – Azure Arc** väljer du den anslutna datorn som du skapade i [snabbstartsartikeln.](quick-enable-hybrid-vm.md)

1. I det vänstra fönstret under avsnittet **Övervakning** väljer du **Insikter och** sedan **Aktivera**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Välj alternativet Insikter på den vänstra menyn" border="false":::

1. På sidan Azure Monitor **Insights Onboarding** uppmanas du att skapa en arbetsyta. I den här självstudien rekommenderar vi inte att du väljer en befintlig Log Analytics-arbetsyta om du redan har en. Välj standardvärdet, som är en arbetsyta med ett unikt namn i samma region som din registrerade anslutna dator. Den här arbetsytan skapas och konfigureras åt dig.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Sidan Aktivera VM-insikter" border="false":::

1. Du får statusmeddelanden när konfigurationen utförs. Den här processen tar några minuter eftersom tillägg installeras på den anslutna datorn.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Meddelande om förloppsstatus för VM-insikter" border="false":::

    När det är klart får du ett meddelande om att datorn har installerats och att insikten har distribuerats.

## <a name="view-data-collected"></a>Visa data som samlas in

När distributionen och konfigurationen är klar väljer **du Insikter** och sedan **fliken** Prestanda. På fliken Prestanda visas en grupp med prestandaräknare som samlats in från gästoperativsystemet på datorn. Rulla ned för att visa fler räknare och flytta musen över ett diagram för att visa medelvärden och percentiler som tagits från den tidpunkt då Log Analytics VM-tillägget installerades på datorn.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="VM-insikter Prestandadiagram för vald dator" border="false":::

Välj **Karta** för att öppna kartfunktionen, som visar de processer som körs på datorn och deras beroenden. Välj **Egenskaper** för att öppna egenskapsfönstret om det inte redan är öppet.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Karta över VM-insikter för vald dator" border="false":::

Expandera processerna för din dator. Välj en av processerna för att visa dess information och för att markera dess beroenden.

Välj din dator igen och välj sedan **Logghändelser.** Du ser en lista över tabeller som lagras på Log Analytics-arbetsytan för datorn. Den här listan skiljer sig beroende på om du använder en Windows- eller Linux-dator. Välj **tabellen** Händelse. **Händelsetabellen** innehåller alla händelser från Windows-händelseloggen. Log Analytics öppnas med en enkel fråga för att hämta insamlade händelseloggposter.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Monitor finns i följande artikel:

> [!div class="nextstepaction"]
> [Översikt över Azure Monitor](../../../azure-monitor/overview.md)