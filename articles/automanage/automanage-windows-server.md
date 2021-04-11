---
title: Azure automanage för Windows Server
description: Lär dig mer om metod tips för Azure automanage för virtuella datorer för tjänster som registreras och konfigureras automatiskt för Windows Server-datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: c1093491c5d4f0c475254e31c2be079d7dd4f5e1
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278855"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Metod tips för Azure automanage för virtuella datorer – Windows Server

Dessa Azure-tjänster registreras automatiskt åt dig när du använder automatisk hantering för virtuella datorer på en virtuell Windows Server-dator. De är viktiga för våra bästa praxis white paper, som du hittar i vårt [ramverk för moln införande](/azure/cloud-adoption-framework/manage/azure-server-management).

För alla dessa tjänster kommer vi att automatiskt registrera, konfigurera automatiskt, övervaka för drift och åtgärda om driften upptäcks. Mer information om den här processen finns i [Azure automanage för virtuella datorer](automanage-virtual-machines.md).

## <a name="supported-windows-server-versions"></a>Windows Server-versioner som stöds

Automanage stöder följande Windows Server-versioner:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure-utgåva

## <a name="participating-services"></a>Deltagande tjänster

|Tjänst    |Beskrivning    |Miljöer som stöds<sup>1</sup>    |Inställningar som stöds<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Övervakning av VM Insights](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor for VMs övervakar de virtuella datorernas prestanda och hälsa, inklusive processer som körs och beroenden för andra resurser. Läs [mer](../azure-monitor/vm/vminsights-overview.md).    |Produktion    |Inga    |
|[Säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-overview)    |Azure Backup tillhandahåller oberoende och isolerade säkerhetskopior för att skydda mot oavsiktlig förstöring av data på dina virtuella datorer. Läs [mer](../backup/backup-azure-vms-introduction.md). Avgifterna baseras på antalet och storleken på de virtuella datorer som skyddas. Läs [mer](https://azure.microsoft.com/pricing/details/backup/).    |Produktion    |Ja    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center är ett enhetligt infrastruktur säkerhets hanterings system som förstärker säkerhets position för dina data Center och ger avancerat skydd för dina hybrid arbets belastningar i molnet. Läs [mer](../security-center/security-center-introduction.md).  Den automatiska hanteringen konfigurerar den prenumeration där den virtuella datorn finns på den kostnads fria nivån av Azure Security Center. Om din prenumeration redan har publicerats på Azure Security Center, kommer den automatiska hanteringen inte att konfigurera om den.    |Produktion, dev/test    |Inga    |
|[Microsoft-programvara mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)    |Microsoft Antimalware för Azure är ett kostnads fritt real tids skydd som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Den genererar aviseringar när en känd skadlig eller oönskad program vara försöker installera sig själv eller köra på dina Azure-system. Läs [mer](../security/fundamentals/antimalware.md). |Produktion, dev/test    |Ja    |
|[Hantering av uppdateringar](https://docs.microsoft.com/azure/automation/update-management/overview)    |Du kan använda Uppdateringshantering i Azure Automation för att hantera uppdateringar av operativ system för dina virtuella datorer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agent datorer och hantera processen för att installera nödvändiga uppdateringar för servrar. Läs [mer](../automation/update-management/overview.md).    |Produktion, dev/test    |Inga    |
|[Ändringsspårning & inventering](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Ändringsspårning och Inventory kombinerar funktioner för ändrings spårning och inventering så att du kan spåra ändringar av virtuella datorer och Server infrastruktur. Tjänsten stöder ändrings spårning i tjänster, program vara, register och filer i din miljö för att hjälpa dig att diagnostisera oönskade ändringar och utlösa aviseringar. Med inventerings support kan du söka efter insyn i installerade program och andra konfigurations objekt i gäst resurser.  Läs [mer](../automation/change-tracking/overview.md).    |Produktion, dev/test    |Inga    |
|[Konfiguration av Azure-gäst](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) | Princip för gäst konfiguration används för att övervaka konfigurationen och rapportera om datorns kompatibilitet. Tjänsten för automatisk hantering installerar [Windows säkerhets bas linjer](/windows/security/threat-protection/windows-security-baselines) med hjälp av gäst konfigurations tillägget. För Windows-datorer kommer gäst konfigurations tjänsten automatiskt att tillämpa bas linje inställningarna igen om de inte är kompatibla. Läs [mer](../governance/policy/concepts/guest-configuration.md).    |Produktion, dev/test    |Inga    |
|[Startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)    | Startdiagnostik är en fel söknings funktion för Azure Virtual Machines (VM) som gör det möjligt att diagnostisera VM-startfel. Med startdiagnostik kan en användare se statusen för den virtuella datorn när den startas genom att samla in information om seriell logg information och skärm dum par. |Produktion, dev/test    |Inga    |
|[Azure Automation – konto](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation stöder hantering under hela livs cykeln för din infrastruktur och dina program. Läs [mer](../automation/automation-intro.md).    |Produktion, dev/test    |Inga    |
|[Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor lagrar loggdata i en Log Analytics arbets yta, som är en Azure-resurs och en behållare där data samlas in, aggregeras och fungerar som en administrativ gränser. Läs [mer](../azure-monitor/logs/design-logs-deployment.md).    |Produktion, dev/test    |Inga    |


<sup>1</sup> valet av miljö är tillgängligt när du aktiverar autohantering. Läs [mer](automanage-virtual-machines.md#environment-configuration). Du kan också justera standard inställningarna för miljön och ange egna inställningar inom metod tipsen.


## <a name="next-steps"></a>Nästa steg

Försök att aktivera autohantering för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)