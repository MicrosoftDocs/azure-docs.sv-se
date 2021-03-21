---
title: Självstudie – övervaka en hybrid dator med Azure Monitor for VMs
description: Lär dig hur du samlar in och analyserar data från en hybrid dator i Azure Monitor.
ms.topic: tutorial
ms.date: 09/23/2020
ms.openlocfilehash: 409ad0976e02e42e385e22a103cfc06af5a4f3f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587698"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>Självstudie: övervaka en hybrid dator med Azure Monitor for VMs

[Azure Monitor](../overview.md) kan samla in data direkt från dina hybrid datorer till en Log Analytics arbets yta för detaljerad analys och korrelation. Detta innebär vanligt vis att installera [Log Analytics agent](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) på datorn med hjälp av ett skript, manuellt eller en automatiserad metod enligt dina konfigurations hanterings standarder. Arc-aktiverade servrar har nyligen lanserat stöd för att installera Log Analytics-och beroende agentens [VM-tillägg](../manage-vm-extensions.md) för Windows och Linux, vilket gör att Azure Monitor kan samla in data från virtuella datorer som inte är Azure-datorer.

Den här självstudien visar hur du konfigurerar och samlar in data från dina Linux-eller Windows-datorer genom att aktivera Azure Monitor for VMs följa en förenklad uppsättning steg, vilket effektiviserar upplevelsen och tar kortare tid.  

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Funktioner för virtuella dator tillägg är bara tillgängliga i listan över [regioner som stöds](../overview.md#supported-regions).

* Se vilka [operativ system som stöds](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) för att säkerställa att de servrar som du aktiverar stöds av Azure Monitor for VMS.

* Granska brand Väggs kraven för den Log Analytics agent som finns i [översikten över Log Analytics-agenten](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements). Azure Monitor for VMs kartans beroende agent skickar inte några data och kräver inte några ändringar i brand väggar eller portar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="enable-azure-monitor-for-vms"></a>Aktivera Azure Monitor for VMs

1. Starta tjänsten Azure Arc i Azure Portal genom att klicka på **alla tjänster** och sedan söka efter och välja **datorer – Azure-båge**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Sök efter Arc-aktiverade servrar i alla tjänster" border="false":::

1. På sidan **datorer – Azure-båge** väljer du den anslutna dator som du skapade i [snabb starts](quick-enable-hybrid-vm.md) artikeln.

1. I den vänstra rutan under avsnittet **övervakning** väljer du **insikter** och **aktiverar** sedan.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Välj Insights-alternativ på menyn till vänster" border="false":::

1. På sidan Azure Monitor **insikter-onboarde** uppmanas du att skapa en arbets yta. I den här självstudien rekommenderar vi inte att du väljer en befintlig Log Analytics arbets yta om du redan har en. Välj standard, som är en arbets yta med ett unikt namn i samma region som din registrerade anslutna dator. Den här arbets ytan skapas och konfigureras åt dig.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Aktivera Azure Monitor for VMs sida" border="false":::

1. Du får status meddelanden medan konfigurationen utförs. Den här processen tar några minuter eftersom tillägg är installerade på den anslutna datorn.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Aktivera Azure Monitor for VMs förlopps status meddelande" border="false":::

    När det är klart får du ett meddelande om att datorn har registrerats och insikten har distribuerats.

## <a name="view-data-collected"></a>Visa data som samlas in

När distributionen och konfigurationen är slutförd väljer du **insikter** och väljer sedan fliken **prestanda** . På fliken prestanda visas en Välj grupp med prestanda räknare som samlats in från gäst operativ systemet på datorn. Rulla nedåt för att visa fler räknare och flytta musen över ett diagram för att Visa medelvärde och percentiler som tas från den tidpunkt då Log Analytics VM-tillägget installerades på datorn.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Azure Monitor for VMs prestanda diagram för vald dator" border="false":::

Välj **Map** för att öppna funktionen Maps, som visar processerna som körs på datorn och deras beroenden. Välj **Egenskaper** för att öppna egenskaps rutan om den inte redan är öppen.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Azure Monitor for VMs karta för vald dator" border="false":::

Expandera processerna för datorn. Välj en av processerna för att visa dess information och för att markera dess beroenden.

Välj din dator igen och välj sedan **logg händelser**. Du ser en lista över tabeller som lagras i Log Analytics arbets ytan för datorn. Den här listan är olika beroende på om du använder en Windows-eller Linux-dator. Välj **händelse** tabellen. **Händelse** tabellen innehåller alla händelser från händelse loggen i Windows. Log Analytics öppnar med en enkel fråga för att hämta insamlade händelse logg poster.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om Azure Monitor kan du läsa följande artikel:

> [!div class="nextstepaction"]
> [Översikt över Azure Monitor](../../../azure-monitor/overview.md)