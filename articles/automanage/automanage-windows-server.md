---
title: Azure Automanage för Windows Server
description: Lär dig mer Azure Automanage för virtuella datorer metodtips för tjänster som automatiskt publiceras och konfigureras för Windows Server-datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 98d91356b55be015b2c1b73787dad0bb7d8fd424
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863019"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Azure Automanage metodtips för virtuella datorer – Windows Server

Dessa Azure-tjänster publiceras automatiskt åt dig när du använder automanage för virtuella datorer (VM) på en virtuell Windows Server-dator. De är viktiga för våra metodtips white paper, som du hittar i [våra Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

För alla dessa tjänster kommer vi att automatiskt registrera, konfigurera automatiskt, övervaka föravdrift och åtgärda om avdrift upptäcks. Mer information om den här processen finns i [Azure Automanage för virtuella datorer.](automanage-virtual-machines.md)

## <a name="supported-windows-server-versions"></a>Windows Server-versioner som stöds

Automanage stöder följande Windows Server-versioner:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Deltagande tjänster

|Tjänst    |Beskrivning    |Miljöer som stöds<sup>1</sup>    |Inställningar som stöds<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[Övervakning av VM-insikter](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor for VMs övervakar prestanda och hälsotillstånd för dina virtuella datorer, inklusive deras processer som körs och beroenden av andra resurser. Läs [mer](../azure-monitor/vm/vminsights-overview.md).    |Produktion    |No    |
|[Säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-overview)    |Azure Backup tillhandahåller oberoende och isolerade säkerhetskopior för att skydda mot oavsiktlig förstöring av data på dina virtuella datorer. Läs [mer](../backup/backup-azure-vms-introduction.md). Avgifterna baseras på antalet och storleken på de virtuella datorer som skyddas. Läs [mer](https://azure.microsoft.com/pricing/details/backup/).    |Produktion    |Yes    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center är ett enhetligt system för hantering av infrastruktursäkerhet som förstärker dina datacenters säkerhetsstatus och ger avancerat skydd mot hot i dina hybridarbetsbelastningar i molnet. Läs [mer](../security-center/security-center-introduction.md).  Automanage konfigurerar prenumerationen där den virtuella datorn finns i den kostnadsfria nivån av Azure Security Center. Om din prenumeration redan har Azure Security Center konfigureras den inte om automatiskt.    |Produktion, Dev/Test    |No    |
|[Microsoft-programvara mot skadlig kod](https://docs.microsoft.com/azure/security/fundamentals/antimalware)    |Microsoft Antimalware för Azure är ett kostnadsfritt realtidsskydd som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Den genererar aviseringar när känd skadlig eller oönskad programvara försöker installera sig själv eller köras på dina Azure-system. Läs [mer](../security/fundamentals/antimalware.md). |Produktion, Dev/Test    |Yes    |
|[Hantering av uppdateringar](https://docs.microsoft.com/azure/automation/update-management/overview)    |Du kan använda Uppdateringshantering i Azure Automation för att hantera uppdateringar av operativsystemet för dina virtuella datorer. Du kan snabbt utvärdera statusen för tillgängliga uppdateringar på alla agentdatorer och hantera processen för att installera nödvändiga uppdateringar för servrar. Läs [mer](../automation/update-management/overview.md).    |Produktion, Dev/Test    |No    |
|[Ändringsspårning & inventering](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Ändringsspårning och inventering kombineras funktioner för ändringsspårning och inventering så att du kan spåra ändringar i infrastrukturen för virtuella datorer och servrar. Tjänsten stöder ändringsspårning mellan tjänster, daemonprogram, register och filer i din miljö för att hjälpa dig att diagnostisera oönskade ändringar och skapa aviseringar. Med inventeringsstöd kan du fråga gästresurser för att få insyn i installerade program och andra konfigurationsobjekt.  Läs [mer](../automation/change-tracking/overview.md).    |Produktion, Dev/Test    |No    |
|[Azure-gästkonfiguration](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) | Gästkonfigurationsprincipen används för att övervaka konfigurationen och rapportera om datorns kompatibilitet. Tjänsten Automanage installerar Windows [säkerhetsbaslinjer med hjälp](/windows/security/threat-protection/windows-security-baselines) av gästkonfigurationstillägget. För Windows-datorer kommer gästkonfigurationstjänsten automatiskt att tillämpa baslinjeinställningarna igen om de inte följer standard. Läs [mer](../governance/policy/concepts/guest-configuration.md).    |Produktion, Dev/Test    |No    |
|[Startdiagnostik](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)    | Startdiagnostik är en felsökningsfunktion för virtuella Azure-datorer (VM) som möjliggör diagnostisering av vm-startfel. Startdiagnostik gör det möjligt för en användare att observera tillståndet för den virtuella datorn när den startas genom att samla in information om serieloggar och skärmbilder. Detta aktiveras endast för datorer som använder hanterade diskar. |Produktion, Dev/Test    |No    |
|[Azure Automation – konto](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation har stöd för hantering under hela livscykeln för din infrastruktur och dina program. Läs [mer](../automation/automation-intro.md).    |Produktion, Dev/Test    |No    |
|[Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor lagrar loggdata på en Log Analytics-arbetsyta, som är en Azure-resurs och en container där data samlas in, aggregeras och fungerar som en administrativ gräns. Läs [mer](../azure-monitor/logs/design-logs-deployment.md).    |Produktion, Dev/Test    |No    |


<sup>1</sup> Miljövalet är tillgängligt när du aktiverar Automanage. Läs [mer](automanage-virtual-machines.md#environment-configuration). Du kan också justera standardinställningarna för miljön och ange egna inställningar enligt begränsningarna för bästa praxis.


## <a name="next-steps"></a>Nästa steg

Prova att aktivera Automanage för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)