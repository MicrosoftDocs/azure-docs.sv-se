---
title: Livs cykel hantering av virtuella Azure VMware-lösningar
description: Lär dig att hantera alla aspekter av livs cykeln för dina virtuella Azure VMware-lösningar med Microsoft Azure inbyggda verktyg.
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: d8224a37e46b336ebf889fe1c075930f34f10ca4
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988536"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Livs cykel hantering av virtuella Azure VMware-lösningar

Med Microsoft Azure inbyggda verktyg kan du övervaka och hantera dina virtuella datorer (VM) i Azure-miljön. Du kan också övervaka och hantera dina virtuella datorer på Azure VMware-lösningen och dina lokala virtuella datorer. I den här översikten ska vi titta på Azure-erbjudanden för integrerad övervakning och hur du kan använda dess inbyggda verktyg för att hantera virtuella datorer i Azure VMware-lösningen under hela livs cykeln.

## <a name="benefits"></a>Fördelar

- Inbyggda Azure-tjänster kan användas för att hantera dina virtuella datorer i en hybrid miljö (Azure, Azure VMware-lösning och lokalt).
- Integrerad övervakning och visning av din Azure, Azure VMware-lösning och lokala virtuella datorer.
- Med Azure Uppdateringshantering i Azure Automation kan du hantera uppdateringar av operativ system för både dina Windows-och Linux-datorer. 
- Azure Security Center tillhandahåller Avancerat hot skydd, inklusive:
    - Övervakning av filintegritet
    - Filbaserad säkerhets aviseringar
    - Utvärdering av operativ Systems korrigering
    - Utvärdering av felkonfigurationer för säkerhet
    - Endpoint Protection-utvärdering 
- Distribuera enkelt Log Analytics-agenten med hjälp av Azure Arc-aktiverade servrar stöd för VM-tillägg för nya och befintliga virtuella datorer. 
- Log Analytics arbets ytan i Azure Monitor aktiverar insamling av logg samling och prestanda räknare med hjälp av Log Analytics-agenten eller-tilläggen. Samla in data och loggar till en enda punkt och presentera dessa data till olika Azure Native Services. 
- Ytterligare fördelar med Azure Monitor är: 
    - Sömlös övervakning 
    - Bättre insyn i infrastrukturen 
    - Snabb meddelanden 
    - Automatisk lösning 
    - Kostnads effektivitet 

## <a name="integrated-azure-monitoring-architecture"></a>Integrerad Azure-övervaknings arkitektur

Följande diagram visar den integrerade övervaknings arkitekturen för virtuella Azure VMware-lösningar.

![Integrerad Azure-övervaknings arkitektur](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="before-you-start"></a>Innan du börjar

Om du är nybörjare på Azure eller inte är bekant med någon av de tjänster som tidigare nämnts, kan du läsa följande artiklar:

- [Översikt över autentisering av Automation-konto](../automation/automation-security-overview.md)
- [Designa Azure Monitor loggar distribution](../azure-monitor/platform/design-logs-deployment.md) och [Azure Monitor](../azure-monitor/overview.md)
- [Planerings](../security-center/security-center-planning-and-operations-guide.md) -och [plattformar som stöds](../security-center/security-center-os-coverage.md) för Azure Security Center
- [Aktivera Azure Monitor for VMs översikt](../azure-monitor/insights/vminsights-enable-overview.md)
- [Vad är Azure Arc-aktiverade servrar?](../azure-arc/servers/overview.md) och [Vad är Azure Arc aktiverat Kubernetes?](../azure-arc/kubernetes/overview.md)
- [Översikt över Uppdateringshantering](../automation/update-management/overview.md)

## <a name="integrating-and-deploying-azure-native-services"></a>Integrering och distribution av inbyggda Azure-tjänster

### <a name="enable-azure-update-management"></a>Aktivera Azure Uppdateringshantering

Azure Uppdateringshantering i Azure Automation hanterar uppdateringar av operativ system för dina Windows-och Linux-datorer i en hybrid miljö. Den övervakar efterlevnaden av korrigeringen och vidarebefordrar aviseringar om avvikelser till Azure Monitor för reparation. Azure Uppdateringshantering måste ansluta till din Log Analytics arbets yta för att kunna använda lagrade data för att bedöma status för uppdateringar på dina virtuella datorer.

1.  Innan du kan lägga till Log Analytics i Azure Uppdateringshantering måste du först [skapa ett Azure Automation-konto](../automation/automation-create-standalone-account.md). Om du föredrar att skapa ditt konto med hjälp av en mall kan du läsa [skapa ett Automation-konto med hjälp av en Azure Resource Manager-mall](../automation/quickstart-create-automation-account-template.md).

2. **Log Analytics arbets ytan** möjliggör insamling av logg samling och prestanda räknare med hjälp av Log Analytics-agenten eller-tilläggen. Om du vill skapa din Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md). Om du vill kan du också skapa en arbets yta via [CLI](../azure-monitor/learn/quick-create-workspace-cli.md), [PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md)eller [Azure Resource Manager mall](../azure-monitor/samples/resource-manager-workspace.md).

3. Om du vill aktivera Azure Uppdateringshantering för dina virtuella datorer, se [aktivera uppdateringshantering från ett Automation-konto](../automation/update-management/enable-from-automation-account.md). I processen länkar du Log Analytics arbets ytan till ditt Automation-konto. 
 
4. När du har lagt till virtuella datorer i Azure Uppdateringshantering kan du [distribuera uppdateringar på virtuella datorer och granska resultaten](../automation/update-management/deploy-updates.md). 

### <a name="enable-azure-security-center"></a>Aktivera Azure Security Center

Azure Security Center tillhandahåller Avancerat skydd för dina hybrid arbets belastningar i molnet och lokalt. Det kommer att bedöma sårbarheten hos virtuella datorer i Azure VMware-lösningen och utlösa aviseringar efter behov. Dessa säkerhets aviseringar kan vidarebefordras till Azure Monitor för lösning.

Azure Security Center behöver inte distribueras. Mer information finns i en lista över [funktioner som stöds för virtuella datorer](../security-center/security-center-services.md).

1. Information om hur du lägger till virtuella datorer i Azure VMware och icke-virtuella datorer i Azure Security Center finns i [snabb start: konfigurera Azure Security Center](../security-center/security-center-get-started.md). 

2. När du har lagt till virtuella Azure VMware-datorer eller virtuella datorer från en annan miljö än Azure-miljö aktiverar du Azure Defender i Security Center. Security Center kommer att bedöma de virtuella datorerna för potentiella säkerhets problem. Den innehåller också rekommendationer på fliken Översikt. Mer information finns [i säkerhets rekommendationer i Azure Security Center](../security-center/security-center-recommendations.md).

3. Du kan definiera säkerhets principer i Azure Security Center. Information om hur du konfigurerar dina säkerhets principer finns i [arbeta med säkerhets principer](../security-center/tutorial-security-policy.md).

### <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Publicera virtuella datorer till Azure Arc-aktiverade servrar

Azure-bågen utökar Azure-hanteringen till vilken infrastruktur som helst, inklusive Azure VMware-lösning, lokalt eller andra moln plattformar.

- Information om hur du aktiverar Azure Arc-aktiverade servrar för flera virtuella Windows-eller Linux-datorer finns i [ansluta hybrid datorer till Azure i stor skala](../azure-arc/servers/onboard-service-principal.md).

### <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Publicera hybrid Kubernetes-kluster med ARC-aktiverad Kubernetes

Du kan ansluta ett Kubernetes-kluster som finns i din Azure VMware-lösnings miljö med Azure Arc-aktiverad Kubernetes. 

- Mer information finns i [skapa en Azure Arc-aktiverad onboarding-tjänstens huvud namn](../azure-arc/kubernetes/create-onboarding-service-principal.md).

### <a name="deploy-the-log-analytics-agent"></a>Distribuera Log Analytics agent

Virtuella Azure VMware-lösningar kan övervakas via Log Analytics agent (kallas även Microsoft Monitoring Agent (MMA) eller OMS Linux-agenten). Du har redan skapat en Log Analytics-arbetsyta när du aktiverar Azure Automation Uppdateringshantering.

- Distribuera Log Analytics-agenten med hjälp av [Azure Arc-aktiverade servrar stöd för VM-tillägg](../azure-arc/servers/manage-vm-extensions.md).

### <a name="enable-azure-monitor"></a>Aktivera Azure Monitor

Azure Monitor är en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Den kräver ingen distribution. Med Azure Monitor kan du övervaka gäst operativ systemets prestanda och identifiera och mappa program beroenden för Azure VMware-lösningar eller lokala virtuella datorer.

- Med Azure Monitor kan du samla in data från olika källor för att övervaka och analysera. Mer information finns i [källor för övervaknings data för Azure Monitor](../azure-monitor/platform/data-sources.md).

- Samla in olika typer av data för analys, visualisering och aviseringar. Mer information finns i [Azure Monitor data plattform](../azure-monitor/platform/data-platform.md).

- Om du vill konfigurera Azure Monitor med din Log Analytics arbets yta, se [konfigurera Log Analytics arbets yta för Azure Monitor for VMS](../azure-monitor/insights/vminsights-configure-workspace.md).

- Du kan skapa aviserings regler för att identifiera problem i miljön, t. ex. hög användning av resurser, saknade korrigeringar, ont om disk utrymme och pulsslag för dina virtuella datorer. Du kan också ställa in ett automatiserat svar på identifierade händelser genom att skicka en avisering till ITSM-verktyg (IT Service Management). Aviserings identifiering kan också skickas via e-post. Information om hur du skapar sådana regler finns i:
    - [Skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-metric.md).
    - [Skapa, Visa och hantera logg aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-log.md).
    - [Åtgärds regler](../azure-monitor/platform/alerts-action-rules.md) för att ange automatiserade åtgärder och meddelanden.
    - [Anslut Azure till ITSM-verktyg med anslutningsprogram för hantering av IT-tjänster (ITSM)](../azure-monitor/platform/itsmc-overview.md).
    
 ## <a name="next-steps"></a>Nästa steg

Nu när du har använt Azures inbyggda verktyg för att hantera dina virtuella datorer i Azure VMware-lösningen under hela livs cykeln, kanske du vill lära dig mer om:

- [Skydda dina virtuella datorer med Azure VMware-lösningen med Azure Security Center](azure-security-integration.md).
- Konfigurera [Azure Backup Server för Azure VMware-lösning](set-up-backup-server-for-azure-vmware-solution.md).
- [Integrera Azure VMware-lösningen i en hubb och eker-arkitektur](concepts-hub-and-spoke.md).
