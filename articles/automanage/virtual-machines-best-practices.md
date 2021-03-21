---
title: Azure automanage för Virtual Machines bästa praxis
description: Lär dig mer om metod tips för Azure automanage för virtuella datorer för tjänster som registreras och konfigureras automatiskt.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 5cdc0080c0d8b06bd0b7e19566b54b4f105648e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122110"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Metod tips för Azure automanage för virtuella datorer


Dessa Azure-tjänster registreras automatiskt åt dig när du använder automatisk hantering för virtuella datorer. De är viktiga för våra bästa praxis white paper, som du hittar i vårt [ramverk för moln införande](/azure/cloud-adoption-framework/manage/azure-server-management).

För alla dessa tjänster kommer vi att automatiskt registrera, konfigurera automatiskt, övervaka för drift och tjänstassisterad om driften upptäcks. Mer information om den här processen finns i [Azure automanage för virtuella datorer](automanage-virtual-machines.md).


## <a name="participating-services"></a>Deltagande tjänster

|Tjänst    |Beskrivning    |Profiler som stöds<sup>1</sup>    |Inställningar som stöds<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Övervakning av VM Insights    |Azure Monitor for VMs övervakar de virtuella datorernas prestanda och hälsa, inklusive processer som körs och beroenden för andra resurser. Läs [mer](../azure-monitor/vm/vminsights-overview.md).    |Metod tips för Azure VM – produktion    |Inga    |
|Backup    |Azure Backup tillhandahåller oberoende och isolerade säkerhetskopior för att skydda mot oavsiktlig förstöring av data på dina virtuella datorer. Läs [mer](../backup/backup-azure-vms-introduction.md). Avgifterna baseras på antalet och storleken på de virtuella datorer som skyddas. Läs [mer](https://azure.microsoft.com/pricing/details/backup/).    |Metod tips för Azure VM – produktion    |Ja    |
|Azure Security Center    |Azure Security Center är ett enhetligt infrastruktur säkerhets hanterings system som förstärker säkerhets position för dina data Center och ger avancerat skydd för dina hybrid arbets belastningar i molnet. Läs [mer](../security-center/security-center-introduction.md).  Den automatiska hanteringen konfigurerar den prenumeration där den virtuella datorn finns på den kostnads fria nivån av Azure Security Center. Om din prenumeration redan har publicerats på Azure Security Center, kommer den automatiska hanteringen inte att konfigurera om den.    |Metod tips för Azure VM – produktion, metod tips för Azure VM – utveckling/testning    |Inga    |
|Microsoft-programvara mot skadlig kod    |Microsoft Antimalware för Azure är ett kostnads fritt real tids skydd som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Den genererar aviseringar när en känd skadlig eller oönskad program vara försöker installera sig själv eller köra på dina Azure-system. Läs [mer](../security/fundamentals/antimalware.md). |Metod tips för Azure VM – produktion, metod tips för Azure VM – utveckling/testning    |Ja    |
|Uppdateringshantering    |Du kan använda Uppdateringshantering i Azure Automation för att hantera uppdateringar av operativ system för dina virtuella datorer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agent datorer och hantera processen för att installera nödvändiga uppdateringar för servrar. Läs [mer](../automation/update-management/overview.md).    |Metod tips för Azure VM – produktion, metod tips för Azure VM – utveckling/testning    |Inga    |
|Ändringsspårning & inventering    |Ändringsspårning och Inventory kombinerar funktioner för ändrings spårning och inventering så att du kan spåra ändringar av virtuella datorer och Server infrastruktur. Tjänsten stöder ändrings spårning i tjänster, program vara, register och filer i din miljö för att hjälpa dig att diagnostisera oönskade ändringar och utlösa aviseringar. Med inventerings support kan du söka efter insyn i installerade program och andra konfigurations objekt i gäst resurser.  Läs [mer](../automation/change-tracking/overview.md).    |Metod tips för Azure VM – produktion, metod tips för Azure VM – utveckling/testning    |Inga    |
|Konfiguration av Azure-gäst    | Princip för gäst konfiguration används för att övervaka konfigurationen och rapportera om datorns kompatibilitet. Tjänsten för automatisk hantering installerar [Windows säkerhets bas linjer](/windows/security/threat-protection/windows-security-baselines) med hjälp av gäst konfigurations tillägget. Läs [mer](../governance/policy/concepts/guest-configuration.md).    |Metod tips för Azure VM – produktion, metod tips för Azure VM – utveckling/testning    |Inga    |
|Azure Automation – konto    |Azure Automation stöder hantering under hela livs cykeln för din infrastruktur och dina program. Läs [mer](../automation/automation-intro.md).    |Metod tips för Azure VM – produktion, metod tips för Azure VM – utveckling/testning    |Inga    |
|Log Analytics-arbetsyta    |Azure Monitor lagrar loggdata i en Log Analytics arbets yta, som är en Azure-resurs och en behållare där data samlas in, aggregeras och fungerar som en administrativ gränser. Läs [mer](../azure-monitor/logs/design-logs-deployment.md).    |Metod tips för Azure VM – produktion, metod tips för Azure VM – utveckling/testning    |Inga    |


<sup>1</sup> konfigurations profiler är tillgängliga när du aktiverar automatisk hantering. Läs [mer](automanage-virtual-machines.md). Du kan också justera standardinställningarna för konfigurations profilen och ange egna inställningar inom metod tips.


## <a name="next-steps"></a>Nästa steg

Försök att aktivera autohantering för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)