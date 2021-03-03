---
title: Aktivera Azure Monitor för en virtuell dator eller skalnings uppsättning för virtuell dator i Azure Portal
description: Lär dig hur du aktiverar VM Insights på en enskild virtuell Azure-dator eller skalnings uppsättning för virtuella datorer med hjälp av Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 47dde48e916361620a832d26e6249c4147d0f8b5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733745"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Aktivera Azure Monitor för en virtuell dator eller skalnings uppsättning för virtuell dator i Azure Portal
I den här artikeln beskrivs hur du aktiverar VM Insights för en virtuell dator eller skalnings uppsättning för virtuella datorer med hjälp av Azure Portal. Den här proceduren kan användas för följande:

- Virtuell Azure-dator
- Skalnings uppsättning för virtuella Azure-datorer
- Hybrid virtuell dator som är ansluten till Azure-bågen

## <a name="prerequisites"></a>Förutsättningar

- [Skapa och konfigurera en Log Analytics-arbetsyta](./vminsights-configure-workspace.md). Du kan också skapa en ny arbets yta under den här processen.
- Se [operativ system som stöds](./vminsights-enable-overview.md#supported-operating-systems) för att säkerställa att operativ systemet för den virtuella datorn eller skalnings uppsättningen för virtuella datorer som du aktiverar stöds. 

## <a name="enable-vm-insights"></a>Aktivera VM Insights

Från Azure Portal väljer du **virtuella datorer**, **skalnings uppsättningar för virtuella** datorer eller **servrar – Azure-båge** och väljer en resurs i listan. I avsnittet **övervakning** på menyn väljer du **insikter** och **aktiverar** sedan. I följande exempel visas en virtuell Azure-dator, men menyn liknar skalnings uppsättningen för virtuella Azure-datorer eller Azure-bågen.

![Aktivera VM-insikter för en virtuell dator](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Om den virtuella datorn inte redan är ansluten till en Log Analytics arbets yta, uppmanas du att välja en. Om du inte redan har [skapat en arbets yta](../logs/quick-create-workspace.md)kan du välja ett standardvärde för den plats där den virtuella datorn eller skalnings uppsättningen för virtuella datorer distribueras i prenumerationen. Den här arbets ytan kommer att skapas och konfigureras om den inte redan finns. Om du väljer en befintlig arbets yta konfigureras den för VM-insikter om den inte redan är det.

> [!NOTE]
> Om du väljer en arbets yta som inte tidigare har kon figurer ATS för VM-insikter läggs hanterings paketet för *VMInsights* till i den här arbets ytan. Detta tillämpas på alla agenter som redan är anslutna till arbets ytan, oavsett om den är aktive rad för VM-insikter. Prestanda data samlas in från de virtuella datorerna och lagras i tabellen *InsightsMetrics* .

![Välj arbetsyta](media/vminsights-enable-portal/select-workspace.png)

Du får status meddelanden när konfigurationen utförs.

>[!NOTE]
>Om du använder en manuell uppgraderings modell för den virtuella datorns skalnings uppsättning uppgraderar du instanserna för att slutföra installationen. Du kan starta uppgraderingar från sidan **instanser** i avsnittet **Inställningar** .

![Aktivera distributions bearbetning för VM Insights-övervakning](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Nästa steg

* Se [använda VM Insights-mappning](vminsights-maps.md) för att Visa identifierade program beroenden. 
* Se [Visa Azure VM-prestanda](vminsights-performance.md) för att identifiera Flask halsar, övergripande användning och den virtuella datorns prestanda.
