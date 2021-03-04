---
title: Vad är VM Insights?
description: Översikt över VM Insights, som övervakar hälso tillstånd och prestanda för virtuella Azure-datorer och identifierar och mappar program komponenter och deras beroenden automatiskt.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046422"
---
# <a name="overview-of-vm-insights"></a>Översikt över VM Insights

VM Insights övervakar prestanda och hälsa för dina virtuella datorer och skalnings uppsättningar för virtuella datorer, inklusive processer som körs och beroenden för andra resurser. Det kan hjälpa till att leverera förutsägbar prestanda och tillgänglighet för viktiga program genom att identifiera Flask halsar och nätverks problem och kan också hjälpa dig att förstå om ett problem är relaterat till andra beroenden.

VM Insights stöder Windows-och Linux-operativsystem på följande datorer:

- Virtuella Azure-datorer
- Azure Virtual Machine Scale Sets
- Virtuella hybrid datorer som är anslutna till Azure Arc
- Lokala virtuella datorer
- Virtuella datorer som finns i en annan moln miljö
  

VM Insights lagrar data i Azure Monitor loggar, vilket gör det möjligt att leverera kraftfull agg regering och filtrering och analysera data trender över tid. Du kan visa dessa data i en enda virtuell dator från den virtuella datorn direkt, eller så kan du använda Azure Monitor för att leverera en sammanställd vy över flera virtuella datorer.

![Den virtuella datorns insikter perspektiv i Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Prissättning
Det finns ingen direkt kostnad för VM-insikter, men du debiteras för aktiviteten i Log Analytics-arbetsytan. Utifrån priserna som publiceras på [sidan Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/)faktureras VM Insights för:

- Data som matats in från agenter och lagras i arbets ytan.
- Hälso tillstånds data som samlats in från gäst hälsa (för hands version)
- Aviserings regler baserade på logg-och hälso data.
- Meddelanden som skickas från varnings regler.

Logg storleken varierar med sträng längden för prestanda räknare och kan öka med antalet logiska diskar och nätverkskort som allokeras till den virtuella datorn. Om du redan använder Tjänstkarta, är den enda ändringen som visas de extra prestanda data som skickas till `InsightsMetrics` data typen Azure Monitor.


## <a name="configuring-vm-insights"></a>Konfigurera VM Insights
Stegen för att konfigurera VM Insights är följande. Följ varje länk för detaljerad vägledning om varje steg:

- [Skapa Log Analytics arbets yta.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Lägg till VMInsights-lösning till arbets ytan.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installera agenter på den virtuella datorn och skalnings uppsättningen för den virtuella datorn som ska övervakas.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Nästa steg

- Se [distribuera VM Insights](./vminsights-enable-overview.md) för krav och metoder som aktiverar övervakning för dina virtuella datorer.
