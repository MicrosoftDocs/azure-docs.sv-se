---
title: Vad är Azure Monitor for VMs?
description: Översikt över Azure Monitor for VMs, som övervakar hälso tillstånd och prestanda för virtuella Azure-datorer och identifierar och mappar program komponenter och deras beroenden automatiskt.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 796f77a860f0c3303d85aaaae290225841da1b89
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625061"
---
# <a name="overview-of-azure-monitor-for-vms"></a>Översikt över Azure Monitor for VMs

Azure Monitor for VMs övervakar de virtuella datorernas prestanda och hälsa och skalnings uppsättningar för virtuella datorer, inklusive processer som körs och beroenden på andra resurser. Det kan hjälpa till att leverera förutsägbar prestanda och tillgänglighet för viktiga program genom att identifiera Flask halsar och nätverks problem och kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.

Azure Monitor for VMs stöder Windows-och Linux-operativ system på följande:

- Virtuella Azure-datorer
- Azure Virtual Machine Scale Sets
- Virtuella hybrid datorer som är anslutna till Azure Arc
- Lokala virtuella datorer
- Virtuella datorer som finns i en annan moln miljö
  

Azure Monitor for VMs lagrar data i Azure Monitor loggar som gör det möjligt att leverera kraftfull agg regering och filtrering och analysera data trender över tid. Du kan visa dessa data i en enda virtuell dator från den virtuella datorn direkt, eller så kan du använda Azure Monitor för att leverera en sammanställd vy över flera virtuella datorer.

![Den virtuella datorns insikter perspektiv i Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Prissättning
Det finns ingen direkt kostnad för Azure Monitor for VMs, men du debiteras för aktiviteten på arbets ytan Log Analytics. Baserat på prissättningen som publiceras på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/)faktureras Azure Monitor for VMS för:

- Data som matats in från agenter och lagras i arbets ytan.
- Hälso tillstånds data som samlats in från gäst hälsa (för hands version)
- Aviserings regler baserade på logg-och hälso data.
- Meddelanden som skickas från varnings regler.

Logg storleken varierar med sträng längden för prestanda räknare och kan öka med antalet logiska diskar och nätverkskort som allokeras till den virtuella datorn. Om du redan använder Tjänstkarta, är den enda ändringen som visas de ytterligare prestanda data som skickas till `InsightsMetrics` data typen Azure Monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Konfigurera Azure Monitor for VMs
Stegen för att konfigurera Azure Monitor for VMs är följande. Följ varje länk för detaljerad vägledning om varje steg:

- [Skapa Log Analytics arbets yta.](../insights/vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Lägg till VMInsights-lösning till arbets ytan.](../insights/vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installera agenter på den virtuella datorn och skalnings uppsättningen för den virtuella datorn som ska övervakas.](../insights/vminsights-enable-overview.md)



## <a name="next-steps"></a>Nästa steg

- Se [distribuera Azure Monitor for VMS](../insights/vminsights-enable-overview.md) för krav och metoder för att aktivera övervakning för dina virtuella datorer.

