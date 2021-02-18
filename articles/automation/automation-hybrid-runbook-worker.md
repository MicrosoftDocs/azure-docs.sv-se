---
title: Översikt över Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller en översikt över Hybrid Runbook Worker som du kan använda för att köra Runbooks på datorer i ditt lokala data Center eller en moln leverantör.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: c95ccb5ea1a23e8173d58bd3a18490e9b8e630e4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581264"
---
# <a name="hybrid-runbook-worker-overview"></a>Översikt över Hybrid Runbook Worker

Runbooks i Azure Automation kanske inte har åtkomst till resurser i andra moln eller i din lokala miljö eftersom de körs på Azures moln plattform. Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera tilldelade datorer.

## <a name="runbook-worker-types"></a>Runbook Worker-typer

Det finns två typer av Runbook-arbetare – system och användare. I följande tabell beskrivs skillnaden mellan dem.

|Typ | Description |
|-----|-------------|
|**System** |Stöder en uppsättning dolda Runbooks som används av Uppdateringshantering-funktionen som är utformad för att installera användardefinierade uppdateringar på Windows-och Linux-datorer.<br> Den här typen av Hybrid Runbook Worker är inte medlem i en Hybrid Runbook Worker grupp och kör därför inte Runbooks som är riktade till en Runbook Worker-grupp. |
|**Användare** |Stöder användardefinierade Runbooks som är avsedda att köras direkt på den Windows-och Linux-dator som är medlemmar i en eller flera Runbook Worker-grupper. |

En Hybrid Runbook Worker kan köras antingen på Windows eller Linux-operativsystemet, och den här rollen förlitar sig på den [Log Analytics agent](../azure-monitor/agents/log-analytics-agent.md) som rapporterar till en Azure Monitor [Log Analytics arbets yta](../azure-monitor/logs/design-logs-deployment.md). Arbets ytan kan inte bara övervaka datorn för det operativ system som stöds, utan även för att ladda ned de komponenter som krävs för att installera Hybrid Runbook Worker.

När Azure Automation [uppdateringshantering](./update-management/overview.md) är aktive rad konfigureras alla datorer som är anslutna till din Log Analytics-arbetsyta automatiskt som en system hybrid Runbook Worker. Om du vill konfigurera den som en användares Windows-Hybrid Runbook Worker, se [distribuera en windows hybrid Runbook Worker](automation-windows-hrw-install.md) och för Linux, se [distribuera en Linux-hybrid Runbook Worker](automation-linux-hrw-install.md).

## <a name="how-does-it-work"></a>Hur fungerar det?

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Varje användar Hybrid Runbook Worker är medlem i en Hybrid Runbook Worker grupp som du anger när du installerar arbetaren. En grupp kan innehålla en enda anställd, men du kan inkludera flera arbetare i en grupp för hög tillgänglighet. Varje dator kan vara värd för en Hybrid Runbook Worker rapportering till ett Automation-konto. Du kan inte registrera hybrid Worker över flera Automation-konton. En hybrid Worker kan bara lyssna efter jobb från ett enda Automation-konto. För datorer som är värdar för system hybrid Runbook Worker som hanteras av Uppdateringshantering kan de läggas till i en Hybrid Runbook Worker grupp. Men du måste använda samma Automation-konto för både Uppdateringshantering och Hybrid Runbook Worker grupp medlemskapet.

När du startar en Runbook på en användar Hybrid Runbook Worker anger du den grupp som den körs på. Varje anställd i gruppen avsöker Azure Automation för att se om några jobb är tillgängliga. Om ett jobb är tillgängligt tar den första arbets tagaren att hämta jobbet. Bearbetnings tiden för jobb kön beror på maskin varu profilen för Hybrid Worker och belastningen. Du kan inte ange en viss arbetare. Hybrid Worker fungerar med en avsöknings funktion (var 30:30:30 sekunder) och följer den första och den första. Beroende på när ett jobb har överförts, beroende på om hybrid Worker-pingar i automatiserings tjänsten, hämtar jobbet. En enda hybrid Worker kan vanligt vis hämta fyra jobb per ping (det vill säga var 30: e sekund). Om din takt med att skicka jobb är högre än fyra per 30 sekunder, finns det en hög möjlighet till en annan hybrid Worker i Hybrid Runbook Worker gruppen som har hämtat jobbet.

En Hybrid Runbook Worker har inte många av [Azures begränsade](automation-runbook-execution.md#runbook-execution-environment) resurs [gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) på disk utrymme, minne eller nätverks platser. Gränserna för en hybrid Worker är bara relaterat till arbetarnas egna resurser och de är inte begränsade till den [verkliga delnings](automation-runbook-execution.md#fair-share) tiden som Azure-sandbox har.

För att kontrol lera distributionen av Runbooks i hybrid Runbook Worker och när eller hur jobben utlöses, kan du registrera hybrid Worker mot olika Hybrid Runbook Worker grupper i ditt Automation-konto. Rikta jobben mot den specifika gruppen eller grupperna för att kunna stödja din körning.

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker installation

Processen för att installera en användar Hybrid Runbook Worker är beroende av operativ systemet. I tabellen nedan definieras distributions typerna.

|Operativsystem  |Distributions typer  |
|---------|---------|
|Windows     | [Automatiserad](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Manuell](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Den rekommenderade installations metoden för en Windows-dator är att använda en Azure Automation Runbook för att helt automatisera processen med att konfigurera den. Om det inte är möjligt kan du följa en steg-för-steg-procedur för att manuellt installera och konfigurera rollen. För Linux-datorer kör du ett Python-skript för att installera agenten på datorn.

## <a name="network-planning"></a><a name="network-planning"></a>Planera för nätverk

Kontrol lera [Azure Automation nätverks konfiguration](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) för detaljerad information om portarna, URL: erna och andra nätverks uppgifter som krävs för Hybrid Runbook Worker.

### <a name="proxy-server-use"></a>Använd proxyserver

Om du använder en proxyserver för kommunikation mellan Azure Automation och datorer som kör Log Analytics agent, måste du se till att lämpliga resurser är tillgängliga. Tids gränsen för begär Anden från Hybrid Runbook Worker-och automation-tjänsterna är 30 sekunder. Efter tre försök Miss lyckas en begäran.

### <a name="firewall-use"></a>Brand Väggs användning

Om du använder en brand vägg för att begränsa åtkomsten till Internet, måste du konfigurera brand väggen för att tillåta åtkomst. Om du använder Log Analytics gateway som proxy kontrollerar du att den har kon figurer ATS för Hybrid Runbook Worker. Se [konfigurera Log Analytics Gateway för Automation hybrid Runbook Worker](../azure-monitor/agents/gateway.md).

### <a name="service-tags"></a>Tjänsttaggar

Azure Automation stöder service tag-Taggar i Azure Virtual Network, från och med service tag- [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet **GuestAndHybridManagement**  i rätt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för Automation-tjänsten. Den här Service tag-koden har inte stöd för att tillåta mer detaljerad kontroll genom att begränsa IP-intervallen till en speciell region.

Service Tag-numret för Azure Automation-tjänsten tillhandahåller endast IP-adresser som används för följande scenarier:

* Utlös webhookar inifrån ditt virtuella nätverk
* Tillåt hybrid Runbook-arbetare eller tillstånds konfigurations agenter på ditt VNet för att kommunicera med Automation-tjänsten

>[!NOTE]
>Service tag- **GuestAndHybridManagement** har för närvarande inte stöd för körning av Runbook-jobb i en Azure-sandbox, bara direkt på en hybrid Runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Stöd för påverkan på nivå 5 (IL5)

Azure Automation Hybrid Runbook Worker kan användas i Azure Government för att stödja påverkan på nivå 5 i någon av följande två konfigurationer:

* [Isolerad virtuell dator](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). När de distribueras använder de hela den fysiska värden för den datorn, vilket ger den nödvändiga isolerings nivån som krävs för att stödja IL5-arbetsbelastningar.

* [Dedikerade Azure-värdar](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), som tillhandahåller fysiska servrar som kan vara värdar för en eller flera virtuella datorer, dedikerade till en Azure-prenumeration.

>[!NOTE]
>Beräknings isolering med Hybrid Runbook Worker-rollen är tillgänglig för moln för kommersiella och amerikanska myndigheter i Azure.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Uppdateringshantering adresser för Hybrid Runbook Worker

Förutom de standard adresser och portar som krävs för Hybrid Runbook Worker, har Uppdateringshantering andra nätverks konfigurations krav som beskrivs i avsnittet [nätverks planering](./update-management/overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation tillstånds konfiguration på en Hybrid Runbook Worker

Du kan köra [Azure Automation tillstånds konfiguration](automation-dsc-overview.md) på en hybrid Runbook Worker. Om du vill hantera konfigurationen av servrar som stöder Hybrid Runbook Worker måste du lägga till servrarna som DSC-noder. Se [Aktivera datorer för hantering genom att Azure Automation tillstånds konfiguration](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Runbook Worker-gränser

Det maximala antalet Hybrid Worker grupper per Automation-konto är 4000 och kan användas för både system & användare hybrid arbetare. Om du har fler än 4 000 datorer för att hantera rekommenderar vi att du skapar ett annat Automation-konto.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks på en Hybrid Runbook Worker

Du kan ha Runbooks som hanterar resurser på den lokala datorn eller köra mot resurser i den lokala miljön där en användar Hybrid Runbook Worker distribueras. I så fall kan du välja att köra dina runbooks på Hybrid Worker i stället för i ett Automation-konto. Runbooks som körs på en Hybrid Runbook Worker är identiska i strukturen för de som du kör i Automation-kontot. Se [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker jobb

Hybrid Runbook Worker jobb körs under det lokala **system** kontot på Windows eller [nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux) i Linux. Azure Automation hanterar jobb i hybrid Runbook Worker på olika sätt från jobb som körs i Azure-sandbox. Se [körnings miljö för Runbook](automation-runbook-execution.md#runbook-execution-environment).

Om den Hybrid Runbook Worker värddatorn startar om, körs pågående Runbook-jobb från början eller från den senaste kontroll punkten för PowerShell Workflow-Runbooks. När ett Runbook-jobb har startats om fler än tre gånger pausas det.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-behörigheter för en Hybrid Runbook Worker

Eftersom de använder icke-Azure-resurser kan Runbooks som körs på en användare Hybrid Runbook Worker inte använda den autentiseringsmekanism som vanligt vis används av Runbooks som autentiserar sig för Azure-resurser. En Runbook ger antingen sin egen autentisering till lokala resurser eller konfigurerar autentisering med [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Du kan också ange ett Kör som-konto för att tillhandahålla en användar kontext för alla Runbooks.

## <a name="view-system-hybrid-runbook-workers"></a>Visa system hybrid Runbook Worker

När Uppdateringshantering funktionen är aktive rad på Windows-eller Linux-datorer kan du inventera listan över system hybrid Runbook Worker-grupper i Azure Portal. Du kan visa upp till 2 000 arbetare i portalen genom att välja fliken **hybrid** arbetare i alternativet hybrid Worker i **gruppen alternativ hybrid arbetare** från den vänstra rutan för det valda Automation-kontot.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Sidan för Hybrid arbets grupper i Automation-konto" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Om du har mer än 2 000 hybrid arbetare kan du köra följande PowerShell-skript för att hämta en lista över alla:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker dina hybrid Runbook Worker finns i [felsöka hybrid Runbook Worker problem](troubleshoot/hybrid-runbook-worker.md#general).
