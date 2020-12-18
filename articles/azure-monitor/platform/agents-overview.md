---
title: Översikt över Azure Monitoring agents | Microsoft Docs
description: Den här artikeln innehåller en detaljerad översikt över tillgängliga Azure-agenter som stöder övervakning av virtuella datorer som finns i Azure eller hybrid miljö.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2020
ms.openlocfilehash: 8b9a79ee32d7a81fee64e40b85272877b7d87589
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674206"
---
# <a name="overview-of-azure-monitor-agents"></a>Översikt över Azure Monitor agenter

Virtuella datorer och andra beräknings resurser kräver en agent för att samla in övervaknings data som krävs för att mäta prestanda och tillgänglighet för gäst operativ system och arbets belastningar. Den här artikeln beskriver de agenter som används av Azure Monitor och hjälper dig att avgöra vilka du behöver för att uppfylla kraven för din specifika miljö.

> [!NOTE]
> Azure Monitor har för närvarande flera agenter på grund av den senaste konsolideringen av Azure Monitor och Log Analytics. Även om det kan överlappa sina funktioner, har var och en har unika funktioner. Beroende på dina krav kan du behöva en eller flera agenter på dina virtuella datorer. 

Du kan ha en specifik uppsättning krav som inte kan uppfyllas helt med en enda agent för en viss virtuell dator. Du kanske till exempel vill använda mått aviseringar som kräver Azure Diagnostics-tillägg, men som även vill använda funktionerna i Azure Monitor for VMs som kräver Log Analytics-agenten och beroende agenten. I sådana fall kan du använda flera agenter och det här är ett vanligt scenario för kunder som behöver funktioner från var och en.

## <a name="summary-of-agents"></a>Sammanfattning av agenter

Följande tabeller ger en snabb jämförelse mellan Azure Monitors agenter för Windows och Linux. Ytterligare information finns i avsnittet nedan.

> [!NOTE]
> Azure Monitor agenten är för närvarande en för hands version med begränsade funktioner. Den här tabellen kommer att uppdateras 

### <a name="windows-agents"></a>Windows-agenter

| | Azure Monitor Agent (förhands granskning) | Diagnostik<br>tillägg (WAD) | Log Analytics<br>agent | Beroende<br>agent |
|:---|:---|:---|:---|:---|
| **Miljöer som stöds** | Azure<br>Annat moln (Azure-båge)<br>Lokalt (Azure-båge)  | Azure | Azure<br>Annat moln<br>Lokal | Azure<br>Annat moln<br>Lokalt | 
| **Agent krav**  | Inga | Inga | Inga | Kräver Log Analytics agent |
| **Insamlade data** | Händelseloggar<br>Prestanda | Händelseloggar<br>ETW-händelser<br>Prestanda<br>Filbaserade loggar<br>IIS-loggar<br>.NET-app-loggar<br>Kraschdumpar<br>Loggar för agent-diagnostik | Händelseloggar<br>Prestanda<br>Filbaserade loggar<br>IIS-loggar<br>Insikter och lösningar<br>Övriga tjänster | Process beroenden<br>Mått för nätverks anslutning |
| **Data som skickas till** | Azure Monitor-loggar<br>Azure Monitor-statistik | Azure Storage<br>Azure Monitor-statistik<br>Händelsehubb | Azure Monitor-loggar | Azure Monitor-loggar<br>(via Log Analytics agent) |
| **Tjänster och**<br>**egenskaper**<br>**tillåtna** | Log Analytics<br>Måttutforskare | Måttutforskare | Azure Monitor för virtuella datorer<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | Azure Monitor för virtuella datorer<br>Tjänstkarta |

### <a name="linux-agents"></a>Linux-agenter

| | Azure Monitor Agent (förhands granskning) | Diagnostik<br>tillägg (LAD) | Teleympkvistar<br>agent | Log Analytics<br>agent | Beroende<br>agent |
|:---|:---|:---|:---|:---|:---|
| **Miljöer som stöds** | Azure<br>Annat moln (Azure-båge)<br>Lokalt (båg båge) | Azure | Azure<br>Annat moln<br>Lokal | Azure<br>Annat moln<br>Lokal | Azure<br>Annat moln<br>Lokalt |
| **Agent krav**  | Inga | Inga | Inga | Inga | Kräver Log Analytics agent |
| **Insamlade data** | Syslog<br>Prestanda | Syslog<br>Prestanda | Prestanda | Syslog<br>Prestanda| Process beroenden<br>Mått för nätverks anslutning |
| **Data som skickas till** | Azure Monitor-loggar<br>Azure Monitor-statistik | Azure Storage<br>Händelsehubb | Azure Monitor-statistik | Azure Monitor-loggar | Azure Monitor-loggar<br>(via Log Analytics agent) |
| **Tjänster och**<br>**egenskaper**<br>**tillåtna** | Log Analytics<br>Måttutforskare | | Måttutforskare | Azure Monitor för virtuella datorer<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | Azure Monitor för virtuella datorer<br>Tjänstkarta |


## <a name="azure-monitor-agent-preview"></a>Azure Monitor Agent (förhands granskning)
[Azure Monitor agenten](azure-monitor-agent-overview.md) är för närvarande en för hands version och kommer att ersätta Log Analytics agent och teleympkvistar-agenten för både virtuella Windows-och Linux-datorer. Den kan skicka data till både Azure Monitor loggar och Azure Monitor mått och använder [data insamlings regler (DCR)](data-collection-rule-overview.md) som ger en mer skalbar metod för att konfigurera data insamling och mål för varje agent.

Använd Azure Monitor-agenten om du behöver:

- Samla in gäst loggar och mått från alla virtuella datorer i Azure, i andra moln eller lokalt. (Azure-båge krävs för virtuella datorer utanför Azure.) 
- Skicka data till Azure Monitor loggar och Azure Monitor mått för analys med Azure Monitor. 
- Skicka data till Azure Storage för arkivering.
- Skicka data till tredje parts verktyg med [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Hantera säkerheten för dina virtuella datorer med hjälp av [Azure Security Center](../../security-center/security-center-introduction.md)  eller [Azure Sentinel](../../sentinel/overview.md). (Inte tillgängligt i för hands version.)

Begränsningar för Azure Monitor agenten är:

- För närvarande i offentlig för hands version. Se [aktuella begränsningar](azure-monitor-agent-overview.md#current-limitations) för en lista över begränsningar under den offentliga för hands versionen.

## <a name="log-analytics-agent"></a>Log Analytics-agent

[Log Analytics agent](log-analytics-agent.md) samlar in övervaknings data från gäst operativ systemet och arbets belastningar på virtuella datorer i Azure, andra moln leverantörer och lokalt. Den skickar data till en Log Analytics-arbetsyta. Log Analytics agenten är samma agent som används av System Center Operations Manager, och du kan använda datorer med fler Hem datorer för att kommunicera med hanterings gruppen och Azure Monitor samtidigt. Den här agenten krävs också av vissa insikter i Azure Monitor och andra tjänster i Azure.


> [!NOTE]
> Log Analytics agent för Windows kallas ofta Microsoft Monitoring Agent (MMA). Log Analytics agent för Linux kallas ofta OMS-agent.



Använd Log Analytics-agenten om du behöver:

* Samla in loggar och prestanda data från virtuella eller fysiska datorer i eller utanför Azure. 
* Skicka data till en Log Analytics arbets yta för att dra nytta av funktioner som stöds av [Azure Monitor loggar](data-platform-logs.md) som [logg frågor](../log-query/log-query-overview.md).
* Använd [Azure Monitor for VMS](../insights/vminsights-overview.md) som gör att du kan övervaka dina virtuella datorer i skala och övervaka deras processer och beroenden på andra resurser och externa processer..  
* Hantera säkerheten för dina virtuella datorer med hjälp av [Azure Security Center](../../security-center/security-center-introduction.md)  eller [Azure Sentinel](../../sentinel/overview.md).
* Använd [Azure Automation uppdaterings hantering](../../automation/update-management/overview.md), [Azure Automation tillstånds konfiguration](../../automation/automation-dsc-overview.md)eller [Azure Automation ändringsspårning och inventering](../../automation/change-tracking/overview.md) för att leverera omfattande hantering av dina virtuella Azure-datorer
* Använd olika [lösningar](../monitor-reference.md#insights-and-core-solutions) för att övervaka en viss tjänst eller ett visst program.

Begränsningar för Log Analytics agenten är:

- Det går inte att skicka data till Azure Monitor mått, Azure Storage eller Azure-Event Hubs.
- Svårt att konfigurera unika övervaknings definitioner för enskilda agenter.
- Svårt att hantera i skala eftersom varje virtuell dator har en unik konfiguration.

## <a name="azure-diagnostics-extension"></a>Azure Diagnostics-tillägg

[Azure-diagnostik-tillägget](diagnostics-extension-overview.md) samlar in övervaknings data från gäst operativ systemet och arbets belastningar för virtuella Azure-datorer och andra beräknings resurser. Den samlar främst in data i Azure Storage, men låter dig också definiera data mottagare för att skicka data till andra destinationer, till exempel Azure Monitor mått och Azure-Event Hubs.

Använd Azure Diagnostic Extension om du behöver:

- Skicka data till Azure Storage för att arkivera eller analysera dem med verktyg som [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Skicka data till [Azure Monitor mått](data-platform-metrics.md) för att analysera den med [Mät Utforskaren](metrics-getting-started.md) och dra nytta av funktioner som nästan real tids [mått varningar](./alerts-metric-overview.md) och [autoskalning](autoscale-overview.md) (endast Windows).
- Skicka data till tredje parts verktyg med [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md).
- Samla in [startdiagnostik](../../virtual-machines/troubleshooting/boot-diagnostics.md) för att undersöka start problem för virtuella datorer.

Begränsningar för Azure Diagnostics-tillägget är:

- Kan endast användas med Azure-resurser.
- Begränsad möjlighet att skicka data till Azure Monitor loggar.

## <a name="telegraf-agent"></a>Teleympkvistar-agent

[InfluxDatain-agenten](collect-custom-metrics-linux-telegraf.md) används för att samla in prestanda data från Linux-datorer för att Azure Monitor mått.

Använd teleympkvistar-agenten om du behöver:

* Skicka data till [Azure Monitor mått](data-platform-metrics.md) för att analysera den med [Mät Utforskaren](metrics-getting-started.md) och dra nytta av funktioner som nästan real tids [mått varningar](./alerts-metric-overview.md) och [autoskalning](autoscale-overview.md) (endast Linux). 



## <a name="dependency-agent"></a>Beroendeagent

Beroende agenten samlar in identifierade data om processer som körs på den virtuella datorn och externa process beroenden. 

Använd beroende agenten om du behöver:

* Använd kart funktionen [Azure Monitor for VMS](../insights/vminsights-overview.md) eller [Tjänstkartas](../insights/service-map.md) lösningen.

Tänk på följande när du använder beroende agenten:

- Beroende agenten kräver att Log Analytics Agent installeras på samma virtuella dator.
- På virtuella Linux-datorer måste Log Analytics Agent installeras före Azure Diagnostic-tillägget.

## <a name="virtual-machine-extensions"></a>Tillägg för virtuell dator

Log Analytics-tillägget för [Windows](../../virtual-machines/extensions/oms-windows.md) och [Linux](../../virtual-machines/extensions/oms-linux.md) installerar Log Analytics-agenten på virtuella Azure-datorer. Azure Monitor beroende tillägg för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) och [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) installerar beroende agenten på virtuella Azure-datorer. Detta är samma agenter som beskrivs ovan, men du kan hantera dem via [tillägg för virtuella datorer](../../virtual-machines/extensions/overview.md). Du bör använda tillägg för att installera och hantera agenterna när det är möjligt.


## <a name="supported-operating-systems"></a>Operativsystem som stöds
I följande tabeller visas de operativ system som stöds av Azure Monitors agenter. I dokumentationen för varje agent finns unika överväganden och för installations processen. Se dokumentationen för teleympkvistar för de operativ system som stöds. Alla operativ system antas vara x64. x86 stöds inte för något operativ system.

### <a name="windows"></a>Windows

| Drift system | Azure Monitor-agent | Log Analytics-agent | Beroendeagent | Diagnostics-tillägg | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(inklusive multi-session) och Pro<br>(Endast Server scenarier)  | X | X | X | X |
| Windows 8 Enterprise och Pro<br>(Endast Server scenarier)  |   | X | X |   |
| Windows 7 SP1<br>(Endast Server scenarier)                 |   | X | X |   |


### <a name="linux"></a>Linux

| Drift system | Azure Monitor-agent | Log Analytics-agent | Beroendeagent | Diagnostics-tillägg | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017,09                                     |   | X |   |   |
| CentOS Linux 8                                           |   | X |   |   |
| CentOS Linux 7                                           | X | X |   | X |
| CentOS Linux 7,8                                         | X | X | X | X |
| CentOS Linux 7,6                                         | X | X | X | X |
| CentOS Linux 6                                           |   | X |   |   |
| CentOS Linux 6.5 +                                        |   | X |   | X |
| Debian 10                                                | X |   |   |   |
| Debian 9                                                 | X | X | x | X |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1 +                                           |   |   |   | X |
| Oracle Linux 8                                           |   | X |   |   |
| Oracle Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           |   | X |   |   |
| Oracle Linux 6.4 +                                        |   | X |   | X |
| Red Hat Enterprise Linux Server 8                        |   | X |   |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        |   | X | X |   |
| Red Hat Enterprise Linux Server 6,7 +                     |   | X | X | X |
| SUSE Linux Enterprise Server 15,1                        |   | X |   |   |
| SUSE Linux Enterprise Server 15                          | X | X |   |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 20,04 LTS                                         |   | X |   |   |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14,04 LTS                                         |   | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>Stöd för linux-kernel i beroende agent
Eftersom beroende agenten fungerar på kernel-nivå är stödet också beroende av kernel-versionen. I följande tabell visas de viktigaste och lägre Linux OS-versionerna och de kernel-versioner som stöds för beroende agenten.

| Distribution | OS-version | Kernelversion |
|:---|:---|:---|
|  Red Hat Linux 7   | 7,6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7,4     | 3.10.0-693  |
| Red Hat Linux 6    | 6,10    | 2.6.32-754 |
|                    | 6,9     | 2.6.32 – 696  |
| CentOSPlus         | 6,10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6,9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 18,04   | 5.3.0 – 1020<br>5,0 (inkluderar Azure-justerad kernel)<br>4,18 *<br> 4,15* |
|                    | 16.04.3 | 4,15. * |
|                    | 16,04   | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP4 | 4,12. * (innehåller Azure-justerad kernel) |
|                                 | 12 SP3 | 4,4. * |
|                                 | 12 SP2 | 4,4. * |
| Debian                          | 9      | 4,9  | 


## <a name="next-steps"></a>Nästa steg

Få mer information om var och en av agenterna på följande sätt:

- [Översikt över Log Analytics agent](log-analytics-agent.md)
- [Översikt över Azure Diagnostics-tillägg](diagnostics-extension-overview.md)
- [Samla in anpassade mått för en virtuell Linux-dator med InfluxData-agenten för teleympkvistar](collect-custom-metrics-linux-telegraf.md)
