---
title: Azure Automation Hybrid Runbook Worker översikt
description: Den här artikeln innehåller en översikt över Hybrid Runbook Worker som du kan använda för att köra runbooks på datorer i ditt lokala datacenter eller din molnleverantör.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2bb178302d399805eb84b233060d5717e2dba8b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830564"
---
# <a name="hybrid-runbook-worker-overview"></a>Översikt över Hybrid Runbook Worker

Runbooks Azure Automation kanske inte har åtkomst till resurser i andra moln eller i din lokala miljö eftersom de körs på Azure-molnplattformen. Du kan använda Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på den dator som är värd för rollen och mot resurser i miljön för att hantera dessa lokala resurser. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera tilldelade datorer.

## <a name="runbook-worker-types"></a>Runbook Worker-typer

Det finns två typer av Runbook Workers – system och användare. I följande tabell beskrivs skillnaden mellan dem.

|Typ | Description |
|-----|-------------|
|**System** |Stöder en uppsättning dolda runbooks som används av Uppdateringshantering-funktionen som är utformad för att installera användar angivna uppdateringar på Windows- och Linux-datorer.<br> Den här Hybrid Runbook Worker är inte medlem i en Hybrid Runbook Worker-grupp och kör därför inte runbooks som är mål för en Runbook Worker-grupp. |
|**Användare** |Stöder användardefinierade runbooks som är avsedda att köras direkt på Windows- och Linux-datorn som är medlemmar i en eller flera Runbook Worker-grupper. |

En Hybrid Runbook Worker kan köras på antingen Windows- eller Linux-operativsystemet, och den här rollen är beroende av [att Log Analytics-agenten](../azure-monitor/agents/log-analytics-agent.md) rapporterar till en Azure Monitor [Log Analytics-arbetsyta](../azure-monitor/logs/design-logs-deployment.md). Arbetsytan är inte bara till för att övervaka datorn för det operativsystem som stöds, utan även för att ladda ned de komponenter som krävs för att installera Hybrid Runbook Worker.

När Azure Automation [Uppdateringshantering](./update-management/overview.md) har aktiverats konfigureras alla maskiner som är anslutna till Log Analytics-arbetsytan automatiskt som en Hybrid Runbook Worker. Information om hur du konfigurerar den som en användare av Windows Hybrid Runbook Worker finns i Distribuera en [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) och för Linux, se Distribuera [en Linux-Hybrid Runbook Worker](automation-linux-hrw-install.md).

## <a name="how-does-it-work"></a>Hur fungerar det?

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Varje användare Hybrid Runbook Worker är medlem i en Hybrid Runbook Worker grupp som du anger när du installerar arbetsrollen. En grupp kan innehålla en enda arbetsroll, men du kan inkludera flera arbetare i en grupp för hög tillgänglighet. Varje dator kan vara värd för en Hybrid Runbook Worker rapporterar till ett Automation-konto. du kan inte registrera Hybrid Worker över flera Automation-konton. En Hybrid Worker kan bara lyssna efter jobb från ett enda Automation-konto. För datorer som är värdar för systemet Hybrid Runbook Worker som hanteras Uppdateringshantering kan de läggas till i en Hybrid Runbook Worker grupp. Men du måste använda samma Automation-konto för både Uppdateringshantering och Hybrid Runbook Worker gruppmedlemskap.

När du startar en runbook på en Hybrid Runbook Worker anger du den grupp som den körs på. Varje arbetsroll i gruppen avsöker Azure Automation för att se om det finns några tillgängliga jobb. Om ett jobb är tillgängligt tar den första arbetsrollen som får jobbet det. Bearbetningstiden för jobbkön beror på hybrid worker-maskinvaruprofilen och belastningen. Du kan inte ange en viss arbetsroll. Hybrid Worker arbetar med en avsökningsmekanism (var 30:e sekund) och följer en ordning efter först-till-plats. Beroende på när ett jobb push-ades, hämtar den hybrid worker-robot som pingar Automation-tjänsten jobbet. En enskild Hybrid Worker kan normalt hämta fyra jobb per ping (det vill säga var 30:e sekund). Om din frekvens för att push-pusha jobb är högre än fyra per 30 sekunder finns det hög risk att en annan hybridarbetare i Hybrid Runbook Worker-gruppen hämtade jobbet.

En Hybrid Runbook Worker har inte många av [azures resursbegränsningar](automation-runbook-execution.md#runbook-execution-environment) för sandbox-miljön på diskutrymme, minne eller nätverkssocketar. [](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) Gränserna för en Hybrid Worker är bara relaterade till arbetarens egna resurser och [](automation-runbook-execution.md#fair-share) de är inte begränsade av tidsfördelningsgränsen som Azure-sandbox-miljöerna har.

Om du vill styra distributionen av runbooks på Hybrid Runbook Worker och när eller hur jobben utlöses kan du registrera Hybrid Worker mot olika Hybrid Runbook Worker grupper i ditt Automation-konto. Rikta jobben mot den specifika gruppen eller grupperna för att stödja din körningsordning.

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker installation

Processen för att installera en Hybrid Runbook Worker beror på operativsystemet. Tabellen nedan definierar distributionstyperna.

|Operativsystem  |Distributionstyper  |
|---------|---------|
|Windows     | [Automatiserad](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Manuell](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Den rekommenderade installationsmetoden för en Windows-dator är att använda en Azure Automation runbook för att helt automatisera processen med att konfigurera den. Om det inte är möjligt kan du följa en stegvis procedur för att manuellt installera och konfigurera rollen. För Linux-datorer kör du ett Python-skript för att installera agenten på datorn.

## <a name="network-planning"></a><a name="network-planning"></a>Planera för nätverk

Kontrollera [Azure Automation nätverkskonfiguration för](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) detaljerad information om portar, URL:er och annan nätverksinformation som krävs för Hybrid Runbook Worker.

### <a name="proxy-server-use"></a>Proxyserveranvändning

Om du använder en proxyserver för kommunikation mellan Azure Automation datorer som kör Log Analytics-agenten ser du till att lämpliga resurser är tillgängliga. Tidsgränsen för begäranden från Hybrid Runbook Worker- och Automation-tjänsterna är 30 sekunder. Efter tre försök misslyckas en begäran.

### <a name="firewall-use"></a>Brandväggsanvändning

Om du använder en brandvägg för att begränsa åtkomsten till Internet måste du konfigurera brandväggen så att den tillåter åtkomst. Om du använder Log Analytics-gatewayen som proxyserver måste du kontrollera att den är konfigurerad för Hybrid Runbook Workers. Se [Konfigurera Log Analytics-gatewayen för Automation Hybrid Runbook Workers.](../azure-monitor/agents/gateway.md)

### <a name="service-tags"></a>Tjänsttaggar

Azure Automation har stöd för tjänsttaggar för virtuella Azure-nätverk, från och med [tjänsttaggen GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Du kan använda tjänsttaggar för att definiera nätverksåtkomstkontroller [för nätverkssäkerhetsgrupper](../virtual-network/network-security-groups-overview.md#security-rules) [eller Azure Firewall](../firewall/service-tags.md). Tjänsttaggar kan användas i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttaggnamnet **GuestAndHybridManagement**  i rätt käll- eller målfält för en regel kan du tillåta eller neka trafiken för Automation-tjänsten. Den här tjänsttaggen stöder inte mer detaljerad kontroll genom att begränsa IP-intervall till en specifik region.

Tjänsttaggen för tjänsten Azure Automation endast IP-adresser som används för följande scenarier:

* Utlösa webhooks inifrån ditt virtuella nätverk
* Tillåt Hybrid Runbook Workers eller State Configuration agenter på ditt VNet för att kommunicera med Automation-tjänsten

>[!NOTE]
>Tjänsttaggen **GuestAndHybridManagement** stöder för närvarande inte runbook-jobbkörning i en Sandbox-miljö i Azure, endast direkt på en Hybrid Runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Stöd för effektnivå 5 (IL5)

Azure Automation Hybrid Runbook Worker kan användas i Azure Government för att stödja arbetsbelastningar på effektnivå 5 i någon av följande två konfigurationer:

* [Isolerad virtuell dator](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). När de distribueras använder de hela den fysiska värden för den datorn, vilket ger den nödvändiga isoleringsnivån som krävs för att stödja IL5-arbetsbelastningar.

* [Azure Dedicated Hosts](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), som tillhandahåller fysiska servrar som kan vara värdar för en eller flera virtuella datorer, dedikerade till en Azure-prenumeration.

>[!NOTE]
>Beräkningsisolering via Hybrid Runbook Worker är tillgängligt för Azure Commercial- och US Government-moln.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Uppdateringshantering adresser för Hybrid Runbook Worker

Förutom de standardadresser och portar som krävs för Hybrid Runbook Worker har Uppdateringshantering andra krav för nätverkskonfiguration som beskrivs i [avsnittet nätverksplanering.](./update-management/overview.md#ports)

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration på en Hybrid Runbook Worker

Du kan köra [Azure Automation State Configuration](automation-dsc-overview.md) på en Hybrid Runbook Worker. Om du vill hantera konfigurationen av servrar som stöder Hybrid Runbook Worker måste du lägga till servrarna som DSC-noder. Se [Aktivera datorer för hantering genom att Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Runbook Worker-gränser

Det maximala antalet Hybrid Worker per Automation-konto är 4 000 och gäller för båda systemadministratörerna & hybridarbetare. Om du har fler än 4 000 datorer att hantera rekommenderar vi att du skapar ett annat Automation-konto.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks på en Hybrid Runbook Worker

Du kan ha runbooks som hanterar resurser på den lokala datorn eller körs mot resurser i den lokala miljön där en användare Hybrid Runbook Worker distribueras. I det här fallet kan du välja att köra dina runbooks på Hybrid Worker i stället för i ett Automation-konto. Runbooks som körs på Hybrid Runbook Worker är identiska i struktur med de som du kör i Automation-kontot. Se [Köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker jobb

Hybrid Runbook Worker jobb körs under det lokala **systemkontot** i Windows eller [nxautomation-kontot](automation-runbook-execution.md#log-analytics-agent-for-linux) i Linux. Azure Automation hanterar jobb på Hybrid Runbook Workers på ett annat sätt än jobb som körs i Azure-sandbox-miljöerna. Se [Runbook-körningsmiljö](automation-runbook-execution.md#runbook-execution-environment).

Om Hybrid Runbook Worker värddatorn startas om startar alla runbook-jobb som körs om från början eller från den senaste kontrollpunkten för PowerShell Workflow-runbooks. När ett Runbook-jobb har startats om mer än tre gånger pausas det.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-behörigheter för en Hybrid Runbook Worker

Eftersom de har åtkomst till icke-Azure-resurser kan runbooks som körs på Hybrid Runbook Worker användare inte använda autentiseringsmekanismen som vanligtvis används av runbooks som autentiserar till Azure-resurser. En runbook tillhandahåller antingen sin egen autentisering till lokala resurser eller konfigurerar autentisering med hjälp av [hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) Du kan också ange ett Kör som-konto för att tillhandahålla en användarkontext för alla runbooks.

## <a name="view-system-hybrid-runbook-workers"></a>Visa systemet Hybrid Runbook Workers

När funktionen Uppdateringshantering har aktiverats på Windows- eller Linux-datorer kan du inventera listan över systemets Hybrid Runbook Workers-grupp i Azure Portal. Du kan visa upp till 2 000 arbetare i portalen genom att välja fliken **Systemhybridarbetare** från alternativet **Hybrid Workers-grupp** i det vänstra fönstret för det valda Automation-kontot.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Sidan Hybrid Worker-grupper för Automation-kontosystem" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Om du har fler än 2 000 hybridarbetare kan du köra följande PowerShell-skript för att hämta en lista över alla:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar runbooks för att automatisera processer i ditt lokala datacenter eller i en annan molnmiljö finns i [Run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

* Information om hur du felsöker Hybrid Runbook Workers finns i [Felsöka Hybrid Runbook Worker problem.](troubleshoot/hybrid-runbook-worker.md#general)
