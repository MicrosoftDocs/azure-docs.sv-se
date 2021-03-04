---
title: Inaktivera övervakning i VM Insights
description: Den här artikeln beskriver hur du stoppar övervakningen av dina virtuella datorer i VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 2de0dcd52745ebadb02ab8dbb563e28abf2822dc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046490"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Inaktivera övervakning av de virtuella datorerna i VM Insights

När du har aktiverat övervakning av dina virtuella datorer (VM) kan du senare välja att inaktivera övervakning i VM Insights. Den här artikeln visar hur du inaktiverar övervakning för en eller flera virtuella datorer.  

För närvarande stöder inte VM Insights selektiv inaktive ring av VM-övervakning. Din Log Analytics-arbetsyta kan stödja VM-insikter och andra lösningar. Den kan också samla in andra övervaknings data. Om din Log Analytics arbets yta tillhandahåller dessa tjänster måste du förstå hur du inaktiverar övervakningen och metoderna för att inaktivera övervakning innan du börjar.

VM Insights förlitar sig på följande komponenter för att leverera sin upplevelse:

* En Log Analytics-arbetsyta som lagrar övervaknings data från virtuella datorer och andra källor.
* En samling prestanda räknare som har kon figurer ATS i arbets ytan. Samlingen uppdaterar övervaknings konfigurationen på alla virtuella datorer som är anslutna till arbets ytan.
* `VMInsights`, som är en övervaknings lösning som kon figurer ATS i arbets ytan. Den här lösningen uppdaterar övervaknings konfigurationen på alla virtuella datorer som är anslutna till arbets ytan.
* `MicrosoftMonitoringAgent` och `DependencyAgent` , som är Azure VM-tillägg. Dessa tillägg samlar in och skickar data till arbets ytan.

När du förbereder för att inaktivera övervakning av dina virtuella datorer bör du tänka på följande:

* Om du har utvärderat med en enskild virtuell dator och använt den förvalda standard Log Analytics arbets ytan kan du inaktivera övervakning genom att avinstallera beroende agenten från den virtuella datorn och koppla från Log Analytics-agenten från den här arbets ytan. Den här metoden är lämplig om du tänker använda den virtuella datorn i andra syfte och bestämmer dig senare för att återansluta den till en annan arbets yta.
* Om du har valt en befintlig Log Analytics-arbetsyta som stöder andra övervaknings lösningar och data insamling från andra källor kan du ta bort lösnings komponenter från arbets ytan utan att avbryta eller påverka arbets ytan.  

>[!NOTE]
> När du har tagit bort lösnings komponenterna från din arbets yta kan du fortsätta att se prestanda-och kart data för dina virtuella Azure-datorer. Data kommer slutligen att sluta visas i vyerna **prestanda** och **karta** . Alternativet **Aktivera** är tillgängligt från den valda virtuella Azure-datorn så att du kan återaktivera övervakningen i framtiden.  

## <a name="remove-vm-insights-completely"></a>Ta bort VM Insights fullständigt

Om du fortfarande behöver Log Analytics arbets ytan följer du de här stegen för att helt ta bort VM-insikter. Du tar bort `VMInsights` lösningen från arbets ytan.  

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan enligt dina inaktuella inmatnings förslag. Välj **Log Analytics**.
3. I listan med Log Analytics arbets ytor väljer du den arbets yta som du valde när du aktiverade VM-insikter.
4. Välj **lösningar** till vänster.  
5. I listan med lösningar väljer du **VMInsights (arbets ytans namn)**. På sidan **Översikt** för lösningen väljer du **ta bort**. När du uppmanas att bekräfta väljer du **Ja**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Inaktivera övervakning och behåll arbets ytan  

Om din Log Analytics arbets yta fortfarande behöver stöd för övervakning från andra källor, följer du dessa steg för att inaktivera övervakning av den virtuella dator som du använde för att utvärdera VM-insikter. För virtuella Azure-datorer tar du bort den beroende agentens VM-tillägg och Log Analytics-agentens VM-tillägg för Windows eller Linux direkt från den virtuella datorn. 

>[!NOTE]
>Ta inte bort den Log Analytics agenten om: 
>
> * Azure Automation hanterar den virtuella datorn för att dirigera processer eller hantera konfiguration eller uppdateringar. 
> * Azure Security Center hanterar den virtuella datorn för säkerhet och hot identifiering. 
>
> Om du tar bort Log Analytics agenten kan du förhindra att tjänsterna och lösningarna hanterar den virtuella datorn proaktivt. 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. I Azure Portal väljer du **Virtual Machines**. 
3. Välj en virtuell dator i listan. 
4. Välj **tillägg** till vänster. På sidan **tillägg** väljer du **DependencyAgent**.
5. På sidan Egenskaper för tillägg väljer du **Avinstallera**.
6. På sidan **tillägg** väljer du **MicrosoftMonitoringAgent**. På sidan Egenskaper för tillägg väljer du **Avinstallera**.  
