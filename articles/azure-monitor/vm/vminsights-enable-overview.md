---
title: Aktivera översikt över VM Insights
description: Lär dig hur du distribuerar och konfigurerar VM Insights. Ta reda på system kraven.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 661c65f33f0c65f2f7ccd038afdffbf3c8e241c9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719703"
---
# <a name="enable-vm-insights-overview"></a>Aktivera översikt över VM Insights

Den här artikeln innehåller en översikt över tillgängliga alternativ för att aktivera VM-insikter för att övervaka hälso tillstånd och prestanda för följande:

- Virtuella Azure-datorer 
- Azure Virtual Machine Scale Sets
- Virtuella hybrid datorer som är anslutna till Azure Arc
- Lokala virtuella datorer
- Virtuella datorer som finns i en annan moln miljö.  

Så här konfigurerar du VM Insights:

* Aktivera en enskild virtuell Azure-dator, skalnings uppsättning för virtuella Azure-datorer eller en Azure Arc-dator genom att välja **insikter** direkt från menyn i Azure Portal.
* Aktivera flera virtuella Azure-datorer, Azure Virtual Machines eller Azure Arc-datorer med hjälp av Azure Policy. Den här metoden säkerställer att de nödvändiga beroendena är installerade och korrekt konfigurerade för befintliga och nya virtuella datorer och skalnings uppsättningar. Icke-kompatibla virtuella datorer och skalnings uppsättningar rapporteras, så du kan välja om du vill aktivera dem och åtgärda dem.
* Aktivera flera virtuella Azure-datorer, virtuella Azure Arc-datorer, skalnings uppsättningar för virtuella Azure-datorer eller Azure Arc-datorer över en angiven prenumeration eller resurs grupp med hjälp av PowerShell.
* Aktivera VM Insights för att övervaka virtuella datorer eller fysiska datorer som finns i företagets nätverk eller i annan moln miljö.

## <a name="supported-machines"></a>Datorer som stöds
VM Insights stöder följande datorer:

- Virtuell Azure-dator
- Skalnings uppsättning för virtuella Azure-datorer
- Hybrid virtuell dator som är ansluten till Azure-bågen


## <a name="supported-azure-arc-machines"></a>Azure Arc-datorer som stöds
VM Insights är tillgängligt för Azure Arc-aktiverade servrar i regioner där Arc-tillägget är tillgängligt. Du måste köra version 0,9 eller senare av Arc-agenten.

| Ansluten källa | Stöds | Beskrivning |
|:--|:--|:--|
| Windows-agenter | Ja | Tillsammans med [Log Analytics agent för Windows](../agents/log-analytics-agent.md), behöver Windows-agenter beroende agenten. Mer information finns i [operativ system som stöds](../agents/agents-overview.md#supported-operating-systems). |
| Linux-agenter | Ja | Tillsammans med [Log Analytics-agenten för Linux](../agents/log-analytics-agent.md)behöver Linux-agenterna beroende agenten. Mer information finns i [operativ system som stöds](#supported-operating-systems). |
| System Center Operations Manager-hanteringsgrupp | Inga | |

## <a name="supported-operating-systems"></a>Operativsystem som stöds

VM Insights stöder alla operativ system som stöder Log Analytics agent och beroende agent. Se [Översikt över Azure Monitor agenter ](../agents/agents-overview.md#supported-operating-systems) för en fullständig lista.

> [!IMPORTANT]
> Tjänsten VM Insights-gäst hälsa har mer begränsat stöd för operativ system medan den är i offentlig för hands version. Se [Aktivera VM Insights-gäst hälsa (för hands version)](../vm/vminsights-health-enable.md) för en detaljerad lista.

Se följande lista över överväganden för Linux-stöd för den beroende agent som stöder VM-insikter:

- Endast standardversioner och SMP Linux-kernelversioner stöds.
- Icke-standardutgåvor av kernel, till exempel PAE (Physical Address Extension) och Xen, stöds inte för någon Linux-distribution. Till exempel stöds inte ett system med versions strängen *2.6.16.21-0,8-xen* .
- Anpassade kärnor, inklusive omkompileringar av standard kärnor, stöds inte.
- För Debian-distributioner som inte är version 9,4 stöds inte kart funktionen och prestanda funktionen är bara tillgänglig på Azure Monitor-menyn. Den är inte tillgänglig direkt från den vänstra rutan i den virtuella Azure-datorn.
- CentOSPlus-kärnan stöds.
- Linux-kärnan måste korrigeras för SPECTRE-problemet. Kontakta din leverantör av Linux-distribution för mer information.
## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
VM-insikter kräver en Log Analytics-arbetsyta. Mer information och krav för den här arbets ytan finns i [konfigurera Log Analytics arbets yta för VM-insikter](vminsights-configure-workspace.md) .
## <a name="agents"></a>Agenter
VM Insights kräver att följande två agenter installeras på varje virtuell dator eller skalnings uppsättning för virtuella datorer som ska övervakas. Om du vill publicera resursen installerar du dessa agenter och ansluter dem till arbets ytan.  Se [nätverks krav](../agents/log-analytics-agent.md#network-requirements) för nätverks kraven för dessa agenter.

- [Log Analytics agent](../agents/log-analytics-agent.md). Samlar in händelser och prestanda data från den virtuella datorn eller skalnings uppsättningen för den virtuella datorn och levererar den till Log Analytics arbets ytan. Distributions metoder för Log Analytics-agenten på Azure-resurser använder VM-tillägget för [Windows](../../virtual-machines/extensions/oms-windows.md) och [Linux](../../virtual-machines/extensions/oms-linux.md).
- Beroende agent. Samlar in identifierade data om processer som körs på den virtuella datorn och externa process beroenden, vilka används av [Map-funktionen i VM-insikter](../vm/vminsights-maps.md). Beroende agenten använder den Log Analytics agenten för att leverera data till Azure Monitor. Distributions metoder för beroende agenten på Azure-resurser använder VM-tillägget för [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) och [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> Log Analytics agenten är samma agent som används av System Center Operations Manager. VM Insights kan övervaka agenter som också övervakas av Operations Manager om de är direkt anslutna och du installerar beroende agenten på dem. Agenter som är anslutna till Azure Monitor via en anslutning till en [hanterings grupp](../tform/../agents/om-agents.md) kan inte övervakas av VM Insights.

Följande är flera metoder för att distribuera dessa agenter. 

| Metod | Beskrivning |
|:---|:---|
| [Azure-portalen](../vm/vminsights-enable-portal.md) | Installera båda agenterna på en enskild virtuell dator, skalnings uppsättning för virtuella datorer eller hybrid virtuella datorer som är anslutna till Azure-bågen. |
| [Mallar för Resurshanteraren](../vm/vminsights-enable-resource-manager.md) | Installera båda agenterna med någon av de metoder som stöds för att distribuera en Resource Manager-mall, inklusive CLI och PowerShell. |
| [Azure Policy](../vm/vminsights-enable-policy.md) | Tilldela Azure Policy initiativ för att automatiskt installera agenterna när en virtuell dator eller skalnings uppsättning för virtuell dator skapas. |
| [Manuell installation](../vm/vminsights-enable-hybrid.md) | Installera agenterna i gäst operativ systemet på datorer som ligger utanför Azure, inklusive i ditt data Center eller i andra moln miljöer. |


## <a name="network-requirements"></a>Nätverkskrav

- Se [nätverks krav](../agents/log-analytics-agent.md#network-requirements) för nätverks kraven för den Log Analytics agenten.
- Beroende agenten kräver en anslutning från den virtuella datorn till 169.254.169.254-adressen. Detta är Azure metadata service-slutpunkten. Se till att brand Väggs inställningarna tillåter anslutningar till den här slut punkten.


## <a name="management-packs"></a>Hanteringspaket
När en Log Analytics arbets yta har kon figurer ATS för VM-insikter, vidarebefordras två hanterings paket till alla Windows-datorer som är anslutna till arbets ytan. Hanterings paketen heter *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* och *Microsoft. IntelligencePacks. VMInsights* och skrivs till *%program%\Microsoft Monitoring Agent\Agent\Health service State\Management Packs*. 

Data källan som används av *ApplicationDependencyMonitor* -hanterings paketet är **% Program Files%\Microsoft Monitoring Agent\Agent\Health service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. Data källan som används av *VMInsights* -hanterings paketet är *% Program Files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Diagnostik-och användnings data

Microsoft samlar automatiskt in användnings-och prestanda data via din användning av tjänsten Azure Monitor. Microsoft använder dessa data för att förbättra tjänstens kvalitet, säkerhet och integritet. 

För att tillhandahålla exakta och effektiva fel söknings funktioner innehåller kart funktionen information om konfigurationen av program varan. Datan innehåller information, till exempel operativ system och version, IP-adress, DNS-namn och arbets Stations namn. Microsoft samlar inte in namn, adresser eller annan kontakt information.

Mer information om insamling och användning av data finns i [sekretess policy för Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

Information om hur du använder funktionen för prestanda övervakning finns i [Visa prestanda för VM Insights](../vm/vminsights-performance.md). Information om hur du visar identifierade program beroenden finns i [Visa översikt över VM-insikter](../vm/vminsights-maps.md).
