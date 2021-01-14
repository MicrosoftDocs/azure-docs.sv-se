---
title: Översikt över Azure Automation Uppdateringshantering
description: Den här artikeln innehåller en översikt över den Uppdateringshantering funktionen som implementerar uppdateringar för dina Windows-och Linux-datorer.
services: automation
ms.subservice: update-management
ms.date: 01/13/2021
ms.topic: conceptual
ms.openlocfilehash: d66d4d32c788317d8b0781f9f24120fbce2f6f8f
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185622"
---
# <a name="update-management-overview"></a>Översikt över Uppdateringshantering

Du kan använda Uppdateringshantering i Azure Automation för att hantera operativ system uppdateringar för virtuella Windows-och Linux-datorer i Azure, i lokala miljöer och i andra moln miljöer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agent datorer och hantera processen för att installera nödvändiga uppdateringar för servrar.

> [!NOTE]
> Du kan inte använda en dator som kon figurer ATS med Uppdateringshantering för att köra anpassade skript från Azure Automation. Den här datorn kan bara köra det Microsoft-signerade uppdaterings skriptet.

> [!NOTE]
> För närvarande stöds inte aktivering av Uppdateringshantering direkt från en ARC-aktiverad server. Se [aktivera uppdateringshantering från ditt Automation-konto](../../automation/update-management/enable-from-automation-account.md) för att förstå kraven och hur du aktiverar för servern.

Om du vill hämta och installera tillgängliga *kritiska* uppdateringar och *säkerhets* korrigeringar automatiskt på den virtuella Azure-datorn kan du läsa om [Automatisk uppdatering av virtuella datorer](../../virtual-machines/windows/automatic-vm-guest-patching.md) för virtuella Windows-datorer.

Innan du distribuerar Uppdateringshantering och aktiverar dina datorer för hantering ska du se till att du förstår informationen i följande avsnitt.  

## <a name="about-update-management"></a>Om Uppdateringshantering

Datorer som hanteras av Uppdateringshantering förlitar sig på följande för att utföra utvärderingen och distribuera uppdateringar:

* [Log Analytics agent](../../azure-monitor/platform/log-analytics-agent.md) för Windows eller Linux
* Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
* Automation-Hybrid Runbook Worker (installeras automatiskt när du aktiverar Uppdateringshantering på datorn)
* Microsoft Update eller [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) för Windows-datorer
* Antingen en privat eller offentlig uppdaterings plats för Linux-datorer

I följande diagram illustreras hur Uppdateringshantering utvärderar och tillämpar säkerhets uppdateringar på alla anslutna Windows Server-och Linux-servrar på en arbets yta:

![Uppdateringshantering arbets flöde](./media/overview/update-mgmt-updateworkflow.png)

Uppdateringshantering kan användas för att distribuera internt till datorer i flera prenumerationer i samma klient organisation.

När ett paket har frigjorts tar det 2 till 3 timmar innan korrigeringen visas för Linux-datorer för utvärdering. För Windows-datorer tar det 12 till 15 timmar innan korrigeringen visas för utvärderingen när den har släppts. När en dator har slutfört en genomsökning för att uppdatera kompatibilitet, vidarebefordrar agenten informationen i bulk till Azure Monitor loggar. På en Windows-dator körs kompatibilitetskontroll var 12: e timme som standard. För en Linux-dator utförs genomsökningen varje timme som standard. Om den Log Analytics agenten startas om startas en kompatibilitetskontroll inom 15 minuter.

Förutom genomsöknings schemat startas genomsökningen efter uppdateringens kompatibilitet inom 15 minuter från det Log Analytics agenten startas om, innan installationen installeras och efter installationen av uppdateringen.

Uppdateringshantering rapporterar hur upp datorn är baserad på vilken källa du är konfigurerad att synkronisera med. Om Windows-datorn är konfigurerad för att rapportera till [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS), beroende på när WSUS senast synkroniserades med Microsoft Update, kan resultatet skilja sig från vad Microsoft Update visar. Detta är detsamma för Linux-datorer som kon figurer ATS för att rapportera till en lokal lagrings platsen i stället för till en offentlig lagrings platsen.

> [!NOTE]
> För att rapporten ska kunna rapporteras till tjänsten måste vissa URL: er och portar vara aktiverade för Uppdateringshantering. Mer information om dessa krav finns i [nätverks konfiguration](../automation-hybrid-runbook-worker.md#network-planning).

Du kan distribuera och installera program uppdateringar på datorer som kräver uppdateringarna genom att skapa en schemalagd distribution. Uppdateringar som klassificeras som valfria ingår inte i distributions omfånget för Windows-datorer. Endast nödvändiga uppdateringar ingår i distributions omfånget.

Den schemalagda distributionen definierar vilka mål datorer som får tillämpliga uppdateringar. Det gör det antingen genom att uttryckligen ange vissa datorer eller genom att välja en [dator grupp](../../azure-monitor/platform/computer-groups.md) som baseras på loggs ökningar av en viss uppsättning datorer (eller på en [Azure-fråga](query-logs.md) som dynamiskt väljer virtuella Azure-datorer baserat på angivna kriterier). Dessa grupper skiljer sig från [omfattnings konfigurationen](../../azure-monitor/insights/solution-targeting.md), som används för att styra målet för datorer som tar emot konfigurationen för att aktivera uppdateringshantering. Detta förhindrar att de utför och rapporterar uppdaterings efterlevnad och installerar godkända uppdateringar som krävs.

När du definierar en distribution anger du även ett schema för att godkänna och ange en tids period under vilken uppdateringar kan installeras. Den här perioden kallas underhålls perioden. En 20 minuters period i underhålls perioden är reserverad för omstarter, förutsatt att en krävs och du har valt lämpligt alternativ för omstart. Om korrigeringen tar längre tid än förväntat och det finns mindre än 20 minuter i underhålls perioden görs ingen omstart.

Uppdateringar installeras via runbooks i Azure Automation. Du kan inte visa dessa Runbooks och de kräver inte någon konfiguration. När en uppdaterings distribution skapas, skapar den ett schema som startar en huvud uppdaterings-Runbook vid den angivna tiden för de datorer som ingår. Huvud-Runbook startar en underordnad Runbook på varje agent för att installera nödvändiga uppdateringar.

Vid det datum och den tid som anges i uppdaterings distributionen kör mål datorerna distributionen parallellt. Före installationen körs en genomsökning för att verifiera att uppdateringarna fortfarande krävs. För WSUS-klientdatorer, om uppdateringarna inte är godkända i WSUS, Miss lyckas uppdaterings distributionen.

En dator som är registrerad för Uppdateringshantering i fler än en Log Analytics arbets yta (kallas även multihoming) stöds inte.

## <a name="clients"></a>Klienter

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I följande tabell visas de operativ system som stöds för uppdaterings bedömning och uppdatering. Uppdatering kräver en system Hybrid Runbook Worker, som installeras automatiskt när du aktiverar den virtuella datorn eller servern för hantering genom att Uppdateringshantering. Information om Hybrid Runbook Worker system krav finns i [distribuera en Windows-hybrid Runbook Worker](../automation-windows-hrw-install.md) och [distribuera en Linux-hybrid Runbook Worker](../automation-linux-hrw-install.md).

> [!NOTE]
> Uppdaterings utvärdering av Linux-datorer stöds bara i vissa regioner enligt listan i Automation-kontot och Log Analytics [mappnings tabellen](../how-to/region-mappings.md#supported-mappings)för arbets ytan.

|Operativsystem  |Kommentarer  |
|---------|---------|
|Windows Server 2019 (Data Center/Data Center Core/standard)<br>Windows Server 2016 (Data Center/Data Center Core/standard)<br>Windows Server 2012 R2 (Data Center/standard)<br>Windows Server 2012 |
|Windows Server 2008 R2 (RTM och SP1 standard)| Uppdateringshantering stöder utvärderingar och korrigeringar för det här operativ systemet. [Hybrid Runbook Worker](../automation-windows-hrw-install.md) stöds för Windows Server 2008 R2. |
|CentOS 6 och 7 (x64)      | Linux-agenter kräver åtkomst till ett uppdaterings lager. Klassificerings-baserad uppdatering kräver `yum` att returnera säkerhets data som CentOS inte har i sina RTM-versioner. Mer information om klassificerings-baserad uppdatering på CentOS finns i [uppdaterings klassificeringar i Linux](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6 och 7 (x64)     | Linux-agenter kräver åtkomst till ett uppdaterings lager.        |
|SUSE Linux Enterprise Server 12 (x64)     | Linux-agenter kräver åtkomst till ett uppdaterings lager.        |
|Ubuntu 14,04 LTS, 16,04 LTS och 18,04 LTS (x64)      |Linux-agenter kräver åtkomst till ett uppdaterings lager.         |

> [!NOTE]
> Skalnings uppsättningar för virtuella Azure-datorer kan hanteras via Uppdateringshantering. Uppdateringshantering fungerar på själva instanserna och inte på bas avbildningen. Du måste schemalägga uppdateringarna på ett stegvist sätt så att alla VM-instanser inte uppdateras samtidigt. Du kan lägga till noder för skalnings uppsättningar för virtuella datorer genom att följa stegen under [Lägg till en icke-Azure-dator i ändringsspårning och inventering](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory).

### <a name="unsupported-operating-systems"></a>Operativsystem som inte stöds

I följande tabell visas de operativ system som inte stöds av Uppdateringshantering:

|Operativsystem  |Kommentarer  |
|---------|---------|
|Windows-klient     | Klient operativ system (t. ex. Windows 7 och Windows 10) stöds inte.<br> Den rekommenderade metoden för Azure Windows Virtual Desktop (WVD)<br> för att hantera uppdateringar är [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) för hantering av klient datorer för Windows 10. |
|Windows Server 2016 Nano Server     | Stöds inte.       |
|Azure Kubernetes service-noder | Stöds inte. Använd korrigerings processen som beskrivs i [tillämpa säkerhets-och kernel-uppdateringar på Linux-noder i Azure Kubernetes service (AKS)](../../aks/node-updates-kured.md)|

### <a name="system-requirements"></a>Systemkrav

Följande information beskriver särskilda krav för operativ system. Ytterligare vägledning finns i [nätverks planering](#ports). För att förstå kraven för TLS 1,2, se [tls 1,2-tvång för Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Program varu krav:

- .NET Framework 4,6 eller senare krävs. ([Hämta .NET Framework](/dotnet/framework/install/guide-for-developers).
- Windows PowerShell 5,1 krävs ([Ladda ned Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616).)

Windows-agenter måste konfigureras för att kommunicera med en WSUS-server, eller de kräver åtkomst till Microsoft Update. För Hybrid datorer rekommenderar vi att du installerar Log Analytics agent för Windows genom att först ansluta datorn till [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md)och sedan använda Azure policy för att tilldela den inbyggda principen [distribuera Log Analytics agent till Windows Azure Arc-datorer](../../governance/policy/samples/built-in-policies.md#monitoring) . Alternativt, om du planerar att övervaka datorerna med Azure Monitor for VMs, använder du i stället [aktivera Azure Monitor for VMS](../../governance/policy/samples/built-in-initiatives.md#monitoring) initiativ.

Du kan använda Uppdateringshantering med Microsoft Endpoint Configuration Manager. Mer information om integrations scenarier finns i [integrera uppdateringshantering med Windows-slutpunkt Configuration Manager](mecmintegration.md). [Log Analytics agent för Windows](../../azure-monitor/platform/agent-windows.md) krävs för Windows-servrar som hanteras av-platser i din Configuration Manager-miljö.

Som standard är virtuella Windows-datorer som distribueras från Azure Marketplace inställda på att ta emot automatiska uppdateringar från Windows Update-tjänsten. Det här beteendet ändras inte när du lägger till virtuella Windows-datorer i din arbets yta. Om du inte aktivt hanterar uppdateringar med hjälp av Uppdateringshantering gäller standard beteendet (för att tillämpa uppdateringar automatiskt).

> [!NOTE]
> Du kan ändra grupprincip så att omstarter av datorn bara kan utföras av användaren, inte av systemet. Hanterade datorer kan fastna om Uppdateringshantering inte har behörighet att starta om datorn utan manuell interaktion från användaren. Mer information finns i [konfigurera grupprincip inställningar för automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Program varu krav:

- Datorn måste ha åtkomst till ett uppdaterings lager, antingen privat eller offentlig.
- TLS 1,1 eller TLS 1,2 krävs för att interagera med Uppdateringshantering.
- Python 2. x har installerats.

> [!NOTE]
> Uppdaterings utvärdering av Linux-datorer stöds bara i vissa regioner. Se tabellen Automation-konto och Log Analytics [mappningar](../how-to/region-mappings.md#supported-mappings)för arbets ytor.

För Hybrid datorer rekommenderar vi att du installerar Log Analytics agent för Linux genom att först ansluta datorn till [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md)och sedan använda Azure policy för att tilldela den inbyggda principen [distribuera Log Analytics agent till Linux Azure Arc-datorer](../../governance/policy/samples/built-in-policies.md#monitoring) . Alternativt, om du planerar att övervaka datorerna med Azure Monitor for VMs, använder du i stället [aktivera Azure Monitor for VMS](../../governance/policy/samples/built-in-initiatives.md#monitoring) initiativ.

Virtuella datorer som skapats från RHEL-avbildningar (på begäran Red Hat Enterprise Linux) som är tillgängliga på Azure Marketplace är registrerade för att få åtkomst till [Red Hat-(RHUI)](../../virtual-machines/workloads/redhat/redhat-rhui.md) som har distribuerats i Azure. Alla andra Linux-distributioner måste uppdateras från distributionens online-fillagringsplats genom att använda de metoder som stöds av distributionen.

## <a name="permissions"></a>Behörigheter

Om du vill skapa och hantera uppdaterings distributioner måste du ha vissa behörigheter. Mer information om dessa behörigheter finns i [rollbaserad åtkomst – uppdateringshantering](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Uppdateringshantering-komponenter

Uppdateringshantering använder resurserna som beskrivs i det här avsnittet. De här resurserna läggs automatiskt till ditt Automation-konto när du aktiverar Uppdateringshantering.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker grupper

När du har aktiverat Uppdateringshantering konfigureras en Windows-dator som är direkt ansluten till din Log Analytics arbets yta automatiskt som en system Hybrid Runbook Worker för att stödja Runbooks som stöder Uppdateringshantering.

Varje Windows-dator som hanteras av Uppdateringshantering visas i rutan hybrid Worker-grupper som en system hybrid Worker-grupp för Automation-kontot. I grupperna används `Hostname FQDN_GUID` namngivnings konventionen. Du kan inte rikta de här grupperna med Runbooks i ditt konto. Om du försöker, Miss lyckas försöket. De här grupperna är endast avsedda att stödja Uppdateringshantering. Mer information om hur du visar listan över Windows-datorer som kon figurer ATS som en Hybrid Runbook Worker finns i [Visa hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Du kan lägga till Windows-datorn till en användare Hybrid Runbook Workers grupp i ditt Automation-konto för att stödja Automation-runbooks om du använder samma konto för Uppdateringshantering och medlemskapet i Hybrid Runbook Worker gruppen. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

### <a name="management-packs"></a>Hanteringspaket

Om din Operations Manager hanterings grupp är [ansluten till en Log Analytics arbets yta](../../azure-monitor/platform/om-agents.md)installeras följande hanterings paket i Operations Manager. Dessa hanterings paket installeras också för Uppdateringshantering på direkt anslutna Windows-datorer. Du behöver inte konfigurera eller hantera dessa hanteringspaket.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Uppdatera distributions-MP

> [!NOTE]
> Om du har en Operations Manager 1807-eller 2019-hanteringsserver ansluten till en Log Analytics arbets yta med agenter som kon figurer ATS i hanterings gruppen för att samla in loggdata måste du åsidosätta parametern `IsAutoRegistrationEnabled` och ange den som sann i **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** -regeln.

Mer information om uppdateringar av hanterings paket finns i [anslut Operations Manager till Azure Monitor loggar](../../azure-monitor/platform/om-agents.md).

> [!NOTE]
> För att Uppdateringshantering helt hantera datorer med Log Analytics-agenten måste du uppdatera till Log Analytics agent för Windows eller Log Analytics agent för Linux. Information om hur du uppdaterar agenten finns i [så här uppgraderar du en Operations Manager-agent](/system-center/scom/deploy-upgrade-agents). I miljöer som använder Operations Manager måste du köra System Center Operations Manager 2012 R2 UR 14 eller senare.

## <a name="data-collection"></a>Datainsamling

### <a name="supported-sources"></a>Källor som stöds

I följande tabell beskrivs de anslutna källor som Uppdateringshantering stöder:

| Ansluten källa | Stöds | Description |
| --- | --- | --- |
| Windows-agenter |Yes |Uppdateringshantering samlar in information om system uppdateringar från Windows-agenter och startar sedan installationen av nödvändiga uppdateringar. |
| Linux-agenter |Yes |Uppdateringshantering samlar in information om system uppdateringar från Linux-agenter och startar sedan installationen av nödvändiga uppdateringar på distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Yes |Uppdateringshantering samlar in information om system uppdateringar från agenter i en ansluten hanterings grupp.<br/><br/>En direkt anslutning från Operations Manager agent till Azure Monitor loggar krävs inte. Data vidarebefordras från hanterings gruppen till Log Analytics-arbetsytan. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

Uppdateringshantering söker igenom hanterade datorer efter data med hjälp av följande regler. Det kan ta mellan 30 minuter och 6 timmar för instrument panelen att Visa uppdaterade data från hanterade datorer.

* Varje Windows-dator – Uppdateringshantering görs en genomsökning två gånger per dag för varje dator.

* Varje Linux-dator – Uppdateringshantering genomsöks varje timme.

Den genomsnittliga data användningen per Azure Monitor loggar för en dator som använder Uppdateringshantering är cirka 25 MB per månad. Det här värdet är bara en uppskattning och kan komma att ändras, beroende på din miljö. Vi rekommenderar att du övervakar din miljö för att hålla koll på din exakta användning. Mer information om hur du analyserar Azure Monitor loggar data användning finns i [Hantera användning och kostnad](../../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Planera för nätverk

Följande adresser krävs specifikt för Uppdateringshantering. Kommunikationen med de här adresserna sker via port 443.

|Azure, offentlig  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

När du skapar säkerhets regler för nätverks grupper eller konfigurerar Azure-brandväggen för att tillåta trafik till Automation-tjänsten och Log Analytics arbets ytan, använder du [service tag-](../../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** och **AzureMonitor**. Detta fören klar den löpande hanteringen av dina nätverks säkerhets regler. Om du vill ansluta till Automation-tjänsten från dina virtuella Azure-datorer på ett säkert och privat sätt kan du läsa [Använd Azure privat länk](../how-to/private-link-security.md). För att hämta den aktuella service tag-koden och intervall informationen som ska ingå som en del av dina lokala brand Väggs konfigurationer, se [nedladdnings bara JSON-filer](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

För Windows-datorer måste du också tillåta trafik till alla slut punkter som krävs av Windows Update. Du hittar en uppdaterad lista med nödvändiga slut punkter i [problem som rör http/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Om du har en lokal [Windows Update-Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)måste du också tillåta trafik till servern som anges i [WSUS-nyckeln](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

För Red Hat Linux-datorer, se [IP-adresser för RHUI Content Delivery servers](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) för obligatoriska slut punkter. Andra Linux-distributioner finns i leverantörens dokumentation.

Mer information om vilka portar som krävs för Hybrid Runbook Worker finns i [uppdateringshantering adresser för Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till Internet, kan du konfigurera en [Log Analytics Gateway](../../azure-monitor/platform/gateway.md) och sedan konfigurera datorn att ansluta via gatewayen för att Azure Automation och Azure Monitor.

## <a name="update-classifications"></a>Klassificering av uppdateringar

I följande tabell definieras de klassificeringar som Uppdateringshantering stöder för Windows-uppdateringar.

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar     | En uppdatering för ett särskilt problem som åtgärdar en kritisk, ej säkerhetsrelaterad bugg.        |
|Säkerhetsuppdateringar     | En uppdatering för en produktspecifik, säkerhetsrelaterad fråga.        |
|Samlade uppdateringar     | En kumulativ uppsättning snabb korrigeringar som är paketerade tillsammans för enkel distribution.        |
|Funktionspaket     | Nya produkt funktioner som distribueras utanför en produkt lansering.        |
|Service Pack     | En kumulativ uppsättning snabb korrigeringar som tillämpas på ett program.        |
|Definitionsuppdateringar     | En uppdatering av virus-eller andra definitionsfiler.        |
|Verktyg     | Ett verktyg eller en funktion som hjälper till att slutföra en eller flera uppgifter.        |
|Uppdateringar     | En uppdatering av ett program eller en fil som är installerad för närvarande.        |

Nästa tabell definierar de klassificeringar som stöds för Linux-uppdateringar.

|Klassificering  |Beskrivning  |
|---------|---------|
|Kritiska uppdateringar och säkerhetsuppdateringar     | Uppdateringar för ett enskilt problem eller ett produktspecifik, säkerhetsrelaterat problem.         |
|Övriga uppdateringar     | Alla andra uppdateringar som inte är kritiska eller som inte är av säkerhets uppdateringar.        |

>[!NOTE]
>Uppdaterings klassificering för Linux-datorer är bara tillgänglig när de används i de Azures offentliga moln regioner som stöds. När du använder Uppdateringshantering i följande nationella moln regioner:
>* Azure US Government
>* 21Vianet i Kina
>
> Det finns ingen klassificering av Linux-uppdateringar och rapporteras i kategorin **andra uppdateringar** . Uppdateringshantering använder data som publicerats av de distributioner som stöds, särskilt deras publicerade [oval](https://oval.mitre.org/) (öppna sårbarhets-och utvärderings språk) filer. Eftersom Internet åtkomst är begränsat från dessa nationella moln kan Uppdateringshantering inte komma åt och använda filerna.

För Linux kan Uppdateringshantering skilja mellan kritiska uppdateringar och säkerhets uppdateringar i molnet under klassificerings **säkerhet** och **andra**, samtidigt som du visar utvärderings data på grund av data berikning i molnet. Vid uppdatering Uppdateringshantering förlitar sig på klassificerings data som är tillgängliga på datorn. Till skillnad från andra distributioner har CentOS inte den här informationen tillgänglig i RTM-versionen. Om du har CentOS-datorer som har kon figurer ATS för att returnera säkerhets data för följande kommando kan Uppdateringshantering korrigeras baserat på klassificeringar.

```bash
sudo yum -q --security check-update
```

Det finns för närvarande ingen metod som stöds för att aktivera intern klassificerings data tillgänglighet på CentOS. För tillfället ges begränsad support till kunder som kanske har aktiverat den här funktionen på egen hand.

Om du vill klassificera uppdateringar i Red Hat Enterprise version 6 måste du installera plugin-programmet yum-Security. På Red Hat Enterprise Linux 7 är plugin-programmet redan en del av yum och det finns inget behov av att installera något. Mer information finns i följande artiklar om Red Hat- [kunskap](https://access.redhat.com/solutions/10021).

När du schemalägger en uppdatering så att den körs på en Linux-dator är det till exempel konfigurerat att bara installera uppdateringar som matchar **säkerhets** klassificeringen, uppdateringarna som installeras kan skilja sig från eller är en del av de uppdateringar som matchar den här klassificeringen. När en utvärdering av OS-uppdateringar väntar på att din Linux-dator utförs, kan du öppna filen för [sårbarhets-och utvärderings språk](https://oval.mitre.org/) (ovalen) som tillhandahålls av Linux distribution-leverantören genom att använda uppdateringshantering för klassificering.

Kategorisering görs för Linux-uppdateringar som **säkerhet** eller **andra** baserat på oval-filerna, som innehåller uppdateringar som löser säkerhets problem eller sårbarheter. Men när uppdaterings schemat körs körs den på Linux-datorn med lämplig Package Manager som YUM, APT eller ZYPPER för att installera dem. Paket hanteraren för Linux-distribution kan ha en annan mekanism för att klassificera uppdateringar, där resultatet kan skilja sig från de som hämtas från OVAL filen genom att Uppdateringshantering. Om du vill kontrol lera datorn manuellt och ta reda på vilka uppdateringar som är relevanta för din paket hanterare läser du [Felsöka distribution av Linux](../troubleshoot/update-management.md#updates-linux-installed-different)-uppdateringar.

## <a name="integrate-update-management-with-configuration-manager"></a>Integrera Uppdateringshantering med Configuration Manager

Kunder som har investerat i Microsoft Endpoint Configuration Manager för att hantera datorer, servrar och mobila enheter är också beroende av styrka och mognad för Configuration Manager för att hantera program uppdateringar. Information om hur du integrerar Uppdateringshantering med Configuration Manager finns i [integrera uppdateringshantering med Windows-slutpunkt Configuration Manager](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Uppdateringar från tredje part på Windows

Uppdateringshantering använder den lokalt konfigurerade uppdaterings databasen för att uppdatera Windows-system som stöds, antingen WSUS eller Windows Update. Med verktyg som [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) kan du importera och publicera anpassade uppdateringar med WSUS. Med det här scenariot kan Uppdateringshantering uppdatera datorer som använder Configuration Manager som uppdaterings lagrings plats med program vara från tredje part. Information om hur du konfigurerar Updates Publisher finns i [Installera Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

Här är hur du kan aktivera Uppdateringshantering och välja datorer som ska hanteras:

- Använda en Azure [Resource Manager-mall](enable-from-template.md) för att distribuera uppdateringshantering till ett nytt eller befintligt Automation-konto och Azure Monitor Log Analytics arbets ytan i din prenumeration. Den konfigurerar inte omfånget för datorer som ska hanteras. Detta utförs som ett separat steg när du har använt mallen.

- Från ditt [Automation-konto](enable-from-automation-account.md) för en eller flera Azure-datorer och icke-Azure-datorer, inklusive Arc-aktiverade servrar.

- Använda metoden **Enable-AutomationSolution** [Runbook](enable-from-runbook.md) .

- För en [vald virtuell Azure-dator](enable-from-vm.md) från sidan **virtuella datorer** i Azure Portal. Det här scenariot är tillgängligt för virtuella Linux-och Windows-datorer.

- För [flera virtuella Azure-datorer](enable-from-portal.md) genom att välja dem från sidan **virtuella datorer** i Azure Portal.

> [!NOTE]
> Uppdateringshantering måste länka en Log Analytics arbets yta till ditt Automation-konto. En slutgiltig lista över regioner som stöds finns i [mappningar för Azure-arbetsytor](../how-to/region-mappings.md). Region mappningarna påverkar inte möjligheten att hantera virtuella datorer i en separat region från ditt Automation-konto.

## <a name="next-steps"></a>Nästa steg

* Information om hur du arbetar med Uppdateringshantering finns i [Hantera uppdateringar för dina virtuella datorer](manage-updates-for-vm.md).

* Läs vanliga frågor om Uppdateringshantering i [Azure Automation vanliga frågor och svar](../automation-faq.md).
