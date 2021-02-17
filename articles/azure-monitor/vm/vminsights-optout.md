---
title: Inaktivera övervakning i Azure Monitor for VMs
description: Den här artikeln beskriver hur du stoppar övervakningen av dina virtuella datorer i Azure Monitor for VMs.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625084"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>Inaktivera övervakning av de virtuella datorerna i Azure Monitor for VMs

När du har aktiverat övervakning av dina virtuella datorer (VM) kan du senare välja att inaktivera övervakning i Azure Monitor for VMs. Den här artikeln visar hur du inaktiverar övervakning för en eller flera virtuella datorer.  

Azure Monitor for VMs stöder för närvarande inte selektiv inaktive ring av VM-övervakning. Din Log Analytics arbets yta kan ha stöd för Azure Monitor for VMs och andra lösningar. Den kan också samla in andra övervaknings data. Om din Log Analytics arbets yta tillhandahåller dessa tjänster måste du förstå hur du inaktiverar övervakningen och metoderna för att inaktivera övervakning innan du börjar.

Azure Monitor for VMs förlitar sig på följande komponenter för att ge sitt erfarenhet:

* En Log Analytics-arbetsyta som lagrar övervaknings data från virtuella datorer och andra källor.
* En samling prestanda räknare som har kon figurer ATS i arbets ytan. Samlingen uppdaterar övervaknings konfigurationen på alla virtuella datorer som är anslutna till arbets ytan.
* `VMInsights`, som är en övervaknings lösning som kon figurer ATS i arbets ytan. Den här lösningen uppdaterar övervaknings konfigurationen på alla virtuella datorer som är anslutna till arbets ytan.
* `MicrosoftMonitoringAgent` och `DependencyAgent` , som är Azure VM-tillägg. Dessa tillägg samlar in och skickar data till arbets ytan.

När du förbereder för att inaktivera övervakning av dina virtuella datorer bör du tänka på följande:

* Om du har utvärderat med en enskild virtuell dator och använt den förvalda standard Log Analytics arbets ytan kan du inaktivera övervakning genom att avinstallera beroende agenten från den virtuella datorn och koppla från Log Analytics-agenten från den här arbets ytan. Den här metoden är lämplig om du tänker använda den virtuella datorn i andra syfte och bestämmer dig senare för att återansluta den till en annan arbets yta.
* Om du har valt en befintlig Log Analytics-arbetsyta som stöder andra övervaknings lösningar och data insamling från andra källor kan du ta bort lösnings komponenter från arbets ytan utan att avbryta eller påverka arbets ytan.  

>[!NOTE]
> När du har tagit bort lösnings komponenterna från din arbets yta kan du fortsätta att se prestanda-och kart data för dina virtuella Azure-datorer. Data kommer slutligen att sluta visas i vyerna **prestanda** och **karta** . Alternativet **Aktivera** är tillgängligt från den valda virtuella Azure-datorn så att du kan återaktivera övervakningen i framtiden.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Ta bort Azure Monitor for VMs helt

Om du fortfarande behöver Log Analytics arbets ytan följer du de här stegen för att ta bort Azure Monitor for VMs helt. Du tar bort `VMInsights` lösningen från arbets ytan.  

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan enligt dina inaktuella inmatnings förslag. Välj **Log Analytics**.
3. I listan med Log Analytics arbets ytor väljer du den arbets yta som du valde när du aktiverade Azure Monitor for VMs.
4. Välj **lösningar** till vänster.  
5. I listan med lösningar väljer du **VMInsights (arbets ytans namn)**. På sidan **Översikt** för lösningen väljer du **ta bort**. När du uppmanas att bekräfta väljer du **Ja**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Inaktivera övervakning och behåll arbets ytan  

Om din Log Analytics arbets yta fortfarande behöver stöd för övervakning från andra källor, följer du dessa steg för att inaktivera övervakning av den virtuella dator som du använde för att utvärdera Azure Monitor for VMs. För virtuella Azure-datorer tar du bort den beroende agentens VM-tillägg och Log Analytics-agentens VM-tillägg för Windows eller Linux direkt från den virtuella datorn. 

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
