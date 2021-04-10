---
title: Översikt över automatisk skalning med VM-skalningsuppsättningar i Azure
description: Lär dig mer om de olika sätt som du kan använda för att automatiskt skala en skalnings uppsättning för virtuella Azure-datorer baserat på prestanda eller enligt ett fast schema
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591396"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Översikt över automatisk skalning med VM-skalningsuppsättningar i Azure
En skalnings uppsättning i en virtuell Azure-dator kan öka eller minska antalet virtuella dator instanser som kör ditt program automatiskt. Det här automatiserade och elastiska beteendet minskar hanterings omkostnaderna för att övervaka och optimera programmets prestanda. Du skapar regler som definierar acceptabla prestanda för en positiv kund upplevelse. När de definierade tröskelvärdena är uppfyllda vidtar autoskalning-regler åtgärder för att justera kapaciteten för din skalnings uppsättning. Du kan också schemalägga händelser för att automatiskt öka eller minska kapaciteten för skalnings uppsättningen vid fasta tidpunkter. Den här artikeln innehåller en översikt över vilka prestanda mått som är tillgängliga och vilka åtgärder som kan utföras i autoskalning.


## <a name="benefits-of-autoscale"></a>Fördelar med autoskalning
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen.

> [!NOTE]
> När du använder automatiska instans reparationer för din skalnings uppsättning kan det maximala antalet instanser i skalnings uppsättningen vara 200. Läs mer om [Automatisk reparation av instanser](./virtual-machine-scale-sets-automatic-instance-repairs.md).

När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via lastbalanseraren. Du styr vilka mått som ska övervakas, till exempel processor eller minne, hur länge program belastningen måste uppfylla ett angivet tröskelvärde och hur många virtuella dator instanser som ska läggas till i skalnings uppsättningen.

På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.


## <a name="use-host-based-metrics"></a>Använd värdbaserade mått
Du kan skapa regler för autoskalning som är inbyggda i värd mått från dina VM-instanser. Värd mått ger dig insyn i prestandan för de virtuella dator instanserna i en skalnings uppsättning utan att behöva installera eller konfigurera ytterligare agenter och data samlingar. Regler för autoskalning som använder dessa mått kan skalas ut eller i antal VM-instanser som svar på processor användning, minnes krav eller disk åtkomst.

Regler för automatisk skalning som använder värdbaserade mått kan skapas med något av följande verktyg:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

Om du vill skapa regler för automatisk skalning som använder mer detaljerade prestanda mått, kan du [Installera och konfigurera Azure Diagnostics-tillägget](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) på VM-instanser eller [Konfigurera programmet med hjälp av App Insights](#application-level-metrics-with-app-insights).

Regler för automatisk skalning som använder värdbaserade mått, virtuella dator mått i gäst med Azure Diagnostic extension och App Insights kan använda följande konfigurations inställningar.

### <a name="metric-sources"></a>Mått källor
Regler för autoskalning kan använda mått från någon av följande källor:

| Måttkälla        | Användningsfall                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuell skalnings uppsättning    | För värdbaserade mått som inte kräver att ytterligare agenter installeras eller konfigureras.                                  |
| Lagringskonto      | Azure Diagnostic-tillägget skriver prestanda mått till Azure Storage som sedan används för att utlösa regler för autoskalning. |
| Service Bus-kö    | Ditt program eller andra komponenter kan skicka meddelanden till en Azure Service Bus kö för att utlösa regler.                   |
| Application Insights | Ett Instrumentation-paket som är installerat i programmet och som strömmar mått direkt från appen.                         |


### <a name="autoscale-rule-criteria"></a>Villkor för autoskalning av regel
Följande värdbaserade mått är tillgängliga för användning när du skapar regler för autoskalning. Om du använder Azure Diagnostic Extension eller App Insights definierar du vilka mått som ska övervakas och användas med regler för autoskalning.

| Måttnamn               |
|---------------------------|
| Processorprocentandel            |
| Nätverk – inkommande                |
| Nätverk – utgående               |
| Lästa byte på disk           |
| Disk-skrivna byte          |
| Disk Läs åtgärder/SEK  |
| Disk skrivnings åtgärder/SEK |
| Återstående CPU-krediter     |
| Förbrukade CPU-krediter      |

När du skapar regler för autoskalning för att övervaka ett specifikt mått, tittar reglerna på någon av följande mått för agg regerings åtgärder:

| Sammansättningstyp |
|------------------|
| Medelvärde          |
| Minimum          |
| Maximal          |
| Totalt            |
| Sista             |
| Antal            |

Reglerna för autoskalning utlöses sedan när måtten jämförs med det definierade tröskelvärdet med någon av följande operatorer:

| Operator                 |
|--------------------------|
| Större än             |
| Större än eller lika med |
| Mindre än                |
| Mindre än eller lika med    |
| Lika med                 |
| Inte lika med             |


### <a name="actions-when-rules-trigger"></a>Åtgärder när regler utlöser
När en regel utlösare för automatisk skalning, kan skalnings uppsättningen automatiskt skalas på något av följande sätt:

| Skalnings åtgärd     | Användningsfall                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Öka antalet med   | Ett fast antal virtuella dator instanser som ska skapas. Användbart i skalnings uppsättningar med ett mindre antal virtuella datorer.                                           |
| Öka procent med | En procents ökning av virtuella dator instanser. Lämpligt för större skalnings uppsättningar där en fast ökning inte kan förbättra prestanda avsevärt. |
| Öka antalet till   | Skapa så många VM-instanser som krävs för att uppnå önskad högsta mängd.                                                            |
| Minska antalet med   | Ett fast antal virtuella dator instanser som ska tas bort. Användbart i skalnings uppsättningar med ett mindre antal virtuella datorer.                                           |
| Minska procent med | En procent baserad minskning av virtuella dator instanser. Lämpligt för större skalnings uppsättningar där en fast ökning kanske inte märkbart minskar resursanvändningen och kostnaderna. |
| Minska antalet till   | Ta bort så många virtuella dator instanser som krävs för att uppnå önskad minimi mängd.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Mått för virtuella gäst datorer med Azure Diagnostics-tillägget
Tillägget Azure Diagnostics är en agent som körs inuti en VM-instans. Agenten övervakar och sparar prestanda mått för Azure Storage. Dessa prestanda mått innehåller mer detaljerad information om status för den virtuella datorn, t. ex. *AverageReadTime* för diskar eller *PercentIdleTime* för CPU. Du kan skapa regler för autoskalning baserat på en mer detaljerad medvetenhet om VM-prestanda, inte bara procent andelen CPU-användning eller minnes användning.

Om du vill använda Azure Diagnostics-tillägget måste du skapa Azure Storage-konton för dina VM-instanser, installera Azure Diagnostics-agenten och konfigurera de virtuella datorerna till att strömma vissa prestanda räknare till lagrings kontot.

Mer information finns i artiklarna om att aktivera Azure Diagnostics-tillägget på en [virtuell Linux-dator](../virtual-machines/extensions/diagnostics-linux.md) eller en [virtuell Windows-dator](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Mått på program nivå med App Insights
För att få bättre insyn i prestandan för dina program, kan du använda Application Insights. Du installerar ett litet instrumentande paket i programmet som övervakar appen och skickar telemetri till Azure. Du kan övervaka mått, till exempel svars tider för ditt program, prestanda för sid inläsning och antalet sessioner. Dessa program mått kan användas för att skapa regler för autoskalning på en detaljerad och inbäddad nivå när du utlöser regler baserade på användbara insikter som kan påverka kund upplevelsen.

Mer information om App Insights finns i artikeln [Vad är Application Insights](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Schemalagd autoskalning
Du kan också skapa regler för autoskalning baserat på scheman. Med dessa schemabaserade regler kan du automatiskt skala antalet virtuella dator instanser vid fasta tidpunkter. Med prestandabaserade regler kan det uppstå prestanda påverkan på programmet innan reglerna för autoskalning av regler utlöses och de nya virtuella dator instanserna är etablerade. Om du kan förutse sådana behov, är de ytterligare VM-instanserna etablerade och färdiga för den ytterligare kund användningen och program efter frågan.

Följande exempel är scenarier som kan dra nytta av schemabaserade regler för autoskalning:

- Skala ut antalet virtuella dator instanser automatiskt i början av arbets dagen när kundens efter frågan ökar. I slutet av arbets dagen skalar automatiskt upp antalet virtuella dator instanser för att minimera resurs kostnaderna över natten när program användningen är låg.
- Om en avdelning använder ett program i stor skala vid vissa delar av månads-eller räkenskaps cykeln, skalar automatiskt antalet virtuella dator instanser för att tillgodose de ytterligare kraven.
- När det finns ett marknadsförings evenemang, befordran eller semester försäljning kan du automatiskt skala antalet virtuella dator instanser före den förväntade kundens efter frågan. 


## <a name="next-steps"></a>Nästa steg
Du kan skapa regler för autoskalning som använder värdbaserade mått med något av följande verktyg:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure-mall](tutorial-autoscale-template.md)

I den här översikten beskrivs hur du använder regler för autoskalning för att skala vågrätt och öka eller minska *antalet* virtuella dator instanser i din skalnings uppsättning. Du kan också skala lodrätt för att öka eller minska *storleken* på den virtuella dator instansen. Mer information finns i [Lodrät autoskalning med skalnings uppsättningar för virtuella datorer](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns i [Hantera skalnings uppsättningar för virtuella datorer med Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Information om hur du genererar aviseringar när reglerna för autoskalning utlöses finns i [använda åtgärder för autoskalning för att skicka aviseringar via e-post och webhook i Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). Du kan också [använda gransknings loggar för att skicka aviseringar om e-post och webhook-aviseringar i Azure Monitor](../azure-monitor/alerts/alerts-log-webhook.md).
