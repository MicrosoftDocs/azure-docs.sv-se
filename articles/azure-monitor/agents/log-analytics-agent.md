---
title: Översikt över Log Analytics agent
description: Det här avsnittet hjälper dig att förstå hur du samlar in data och övervakar datorer som finns i Azure, lokalt eller i en annan moln miljö med Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: 8b3b1d39766d556b820f21c1775210611b995d92
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199906"
---
# <a name="log-analytics-agent-overview"></a>Översikt över Log Analytics agent

Azure Log Analytics agent samlar in telemetri från virtuella Windows-och Linux-datorer i alla moln, lokala datorer och de som övervakas av [System Center Operations Manager](/system-center/scom/) och skickar insamlade data till din Log Analytics-arbetsyta i Azure Monitor. Log Analytics agenten stöder också insikter och andra tjänster i Azure Monitor som [VM Insights](../vm/vminsights-enable-overview.md), [Azure Security Center](../../security-center/index.yml)och [Azure Automation](../../automation/automation-intro.md). Den här artikeln innehåller en detaljerad översikt över agent-, system-och nätverks krav och distributions metoder.

> [!NOTE]
> Du kan också se Log Analytics-agenten som kallas Microsoft Monitoring Agent (MMA).

## <a name="comparison-to-azure-diagnostics-extension"></a>Jämförelse med Azure Diagnostics-tillägget
[Tillägget Azure Diagnostics](./diagnostics-extension-overview.md) i Azure Monitor kan också användas för att samla in övervaknings data från gäst operativ systemet på virtuella Azure-datorer. Du kan välja att använda antingen eller båda beroende på dina behov. En detaljerad jämförelse av de Azure Monitor agenterna finns i [Översikt över Azure Monitors agenter](../agents/agents-overview.md) . 

Viktiga skillnader att tänka på är:

- Azure-diagnostik-tillägget kan bara användas med virtuella Azure-datorer. Log Analytics agenten kan användas med virtuella datorer i Azure, andra moln och lokalt.
- Azure-diagnostik-tillägget skickar data till Azure Storage, [Azure Monitor mått](../essentials/data-platform-metrics.md) (endast Windows) och Event Hubs. Log Analytics agent skickar data till [Azure Monitor loggar](../logs/data-platform-logs.md).
- Log Analytics agent krävs för [lösningar](../monitor-reference.md#insights-and-core-solutions), VM- [insikter](../vm/vminsights-overview.md)och andra tjänster som [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Kostnader

Det kostar inget att Log Analytics agent, men du kan debiteras avgifter för inmatade data. Se [Hantera användning och kostnader med Azure Monitor loggar](../logs/manage-cost-storage.md) för detaljerad information om priser för data som samlas in i en Log Analytics arbets yta.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

 Se [operativ system som stöds](../agents/agents-overview.md#supported-operating-systems) för en lista över Windows-och Linux-versioner av operativ systemet som stöds av Log Analytics agenten. 

## <a name="data-collected"></a>Insamlade data

I följande tabell visas de typer av data som du kan konfigurera en Log Analytics arbets yta att samla in från alla anslutna agenter. Se [vad som övervakas av Azure Monitor?](../monitor-reference.md) för en lista med insikter, lösningar och andra lösningar som använder Log Analytics-agenten för att samla in andra typer av data.

| Datakälla | Beskrivning |
| --- | --- |
| [Händelse loggar i Windows](../agents/data-sources-windows-events.md) | Information som skickas till händelse loggnings systemet i Windows. |
| [Syslog](../agents/data-sources-syslog.md)                     | Information som skickas till händelse loggnings systemet i Linux. |
| [Prestanda](../agents/data-sources-performance-counters.md)  | Numeriska värden mäter prestanda för olika aspekter av operativ system och arbets belastningar. |
| [IIS-loggar](../agents/data-sources-iis-logs.md)                 | Användnings information för IIS-webbplatser som körs på gäst operativ systemet. |
| [Anpassade loggar](../agents/data-sources-custom-logs.md)           | Händelser från textfiler på både Windows-och Linux-datorer. |

## <a name="data-destinations"></a>Data destinationer

Log Analytics agent skickar data till en Log Analytics arbets yta i Azure Monitor. Windows-agenten kan vara multihomed för att skicka data till flera arbets ytor och System Center Operations Manager hanterings grupper. Linux-agenten kan bara skicka till ett enda mål, antingen en arbets yta eller en hanterings grupp.

## <a name="other-services"></a>Övriga tjänster

Agenten för Linux och Windows är inte bara för att ansluta till Azure Monitor. Andra tjänster som Azure Security Center och Azure Sentinel är beroende av agenten och dess anslutna Log Analytics arbets yta. Agenten stöder också Azure Automation som värd för Hybrid Runbook Worker-rollen och andra tjänster som [ändringsspårning](../../automation/change-tracking/overview.md), [uppdateringshantering](../../automation/update-management/overview.md)och [Azure Security Center](../../security-center/security-center-introduction.md). Mer information om Hybrid Runbook Worker-rollen finns i [Azure Automation hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md).  

## <a name="workspace-and-management-group-limitations"></a>Begränsningar för arbets yta och hanterings grupp

Mer information om hur du ansluter en agent till en Operations Manager hanterings grupp finns i [Konfigurera agent att rapportera till en Operations Manager hanterings grupp](../agents/agent-manage.md#configure-agent-to-report-to-an-operations-manager-management-group) .

* Windows-agenter kan ansluta till upp till fyra arbets ytor, även om de är anslutna till en System Center Operations Manager hanterings grupp.
* Linux-agenten stöder inte Multi-värdar och kan bara ansluta till en enda arbets yta eller hanterings grupp.

## <a name="security-limitations"></a>Säkerhets begränsningar

* Windows-och Linux-agenterna stöder [FIPS 140-standarden](/windows/security/threat-protection/fips-140-validation), men [andra typer av härdning kanske inte stöds](../agents/agent-linux.md#supported-linux-hardening).

## <a name="installation-options"></a>Installationsalternativ

Det finns flera metoder för att installera Log Analytics-agenten och ansluta datorn till Azure Monitor beroende på dina behov. I följande avsnitt visas möjliga metoder för olika typer av virtuella datorer.

> [!NOTE]
> Det finns inte stöd för att klona en dator med den Log Analytics agenten redan konfigurerad. Om agenten redan har associerats med en arbets yta fungerar det inte för "gyllene bilder".

### <a name="azure-virtual-machine"></a>Virtuell Azure-dator

- [VM Insights](../vm/vminsights-enable-overview.md) ger flera metoder som aktiverar agenter i stor skala. Detta omfattar installation av Log Analytics agent och beroende agent. 
- [Azure Security Center kan etablera Log Analytics-agenten](../../security-center/security-center-enable-data-collection.md) på alla virtuella Azure-datorer som stöds och eventuella nya som skapas om du aktiverar den för att övervaka säkerhets problem och hot.
- Log Analytics VM-tillägg för [Windows](../../virtual-machines/extensions/oms-windows.md) eller [Linux](../../virtual-machines/extensions/oms-linux.md) kan installeras med Azure Portal, Azure CLI, Azure PowerShell eller en Azure Resource Manager mall.
- Installera för enskilda virtuella Azure-datorer [manuellt från Azure Portal](../vm/quick-collect-azurevm.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

### <a name="windows-virtual-machine-on-premises-or-in-another-cloud"></a>Virtuell Windows-dator eller i ett annat moln

- Använd [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md) för att distribuera och hantera Log Analytics VM-tillägget.
- [Installera](../agents/agent-windows.md) agenten manuellt från kommando raden.
- Automatisera installationen med [Azure Automation DSC](../agents/agent-windows.md#install-agent-using-dsc-in-azure-automation).
- Använd en [Resource Manager-mall med Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win)

### <a name="linux-virtual-machine-on-premises-or-in-another-cloud"></a>Virtuella Linux-datorer lokalt eller i ett annat moln

- Använd [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md) för att distribuera och hantera Log Analytics VM-tillägget.
- [Installera](../vm/quick-collect-linux-computer.md) agenten manuellt genom att anropa ett omslutnings skript som finns på GitHub.
- Integrera [System Center Operations Manager](./om-agents.md) med Azure Monitor för att vidarebefordra insamlade data från Windows-datorer som rapporterar till en hanterings grupp.

## <a name="workspace-id-and-key"></a>Arbetsyte-ID och nyckel

Oavsett vilken installations metod som används, behöver du arbets ytans ID och nyckel för Log Analytics arbets ytan som agenten ska ansluta till. Välj arbets ytan från menyn **Log Analytics arbets ytor** i Azure Portal. Välj sedan **agent hantering** i avsnittet **Inställningar** . 

[![Arbetsyteinformation](media/log-analytics-agent/workspace-details.png)](media/log-analytics-agent/workspace-details.png#lightbox)

## <a name="tls-12-protocol"></a>TLS 1,2-protokoll

För att säkerställa säkerheten för data som överförs till Azure Monitor loggar, rekommenderar vi starkt att du konfigurerar agenten att använda minst Transport Layer Security (TLS) 1,2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte**.  Mer information finns i [skicka data på ett säkert sätt med TLS 1,2](../logs/data-security.md#sending-data-securely-using-tls-12). 

## <a name="network-requirements"></a>Nätverkskrav

Agenten för Linux och Windows kommunicerar utgående till Azure Monitor tjänsten via TCP-port 443. Om datorn ansluter via en brand vägg eller proxyserver för att kommunicera via Internet kan du läsa igenom kraven nedan för att förstå vilken nätverks konfiguration som krävs. Om dina IT-säkerhetsprinciper inte tillåter datorer i nätverket att ansluta till Internet, kan du konfigurera en [Log Analytics Gateway](gateway.md) och sedan Konfigurera agenten att ansluta via gatewayen för att Azure Monitor. Agenten kan sedan ta emot konfigurations information och skicka data som samlas in.

![Diagram över Log Analytics-agent kommunikation](./media/log-analytics-agent/log-analytics-agent-01.png)

I följande tabell visas den konfigurations information för proxy och brand vägg som krävs för att Linux-och Windows-agenter ska kunna kommunicera med Azure Monitor loggar.

### <a name="firewall-requirements"></a>Brandväggsförutsättningar

|Agentresurs|Portar |Riktning |Kringgå HTTPS-kontroll|
|------|---------|--------|--------|
|*.ods.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.oms.opinsights.azure.com |Port 443 |Utgående|Ja |  
|*.blob.core.windows.net |Port 443 |Utgående|Ja |
|*.azure-automation.net |Port 443 |Utgående|Ja |

För brand Väggs information som krävs för Azure Government, se [Azure Government hantering](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). 

Om du planerar att använda Azure Automation Hybrid Runbook Worker för att ansluta till och registrera med Automation-tjänsten för att använda Runbooks eller hanterings funktioner i din miljö, måste den ha åtkomst till port numret och de URL: er som beskrivs i [Konfigurera ditt nätverk för Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning).

### <a name="proxy-configuration"></a>Proxykonfiguration

Windows-och Linux-agenten stöder kommunikation antingen via en proxyserver eller Log Analytics Gateway för att Azure Monitor med HTTPS-protokollet.  Både anonym och grundläggande autentisering (användar namn/lösen ord) stöds.  För Windows-agenten som är ansluten direkt till tjänsten anges proxykonfigurationen under installationen eller [efter distributionen](../agents/agent-manage.md#update-proxy-settings) från kontroll panelen eller med PowerShell.  

För Linux-agenten anges proxyservern under installationen eller [efter installationen](../agents/agent-manage.md#update-proxy-settings) genom att ändra konfigurations filen proxy. conf. Konfiguration svärdet för Linux-agentens proxy har följande syntax:

`[protocol://][user:password@]proxyhost[:port]`

|Egenskap| Beskrivning |
|--------|-------------|
|Protokoll | https |
|användare | Valfritt användar namn för proxyautentisering |
|password | Valfritt lösen ord för proxyautentisering |
|proxyhost | Adress eller FQDN för proxyservern/Log Analytics Gateway |
|port | Valfritt port nummer för proxyservern/Log Analytics Gateway |

Exempelvis: `https://user01:password@proxy01.contoso.com:30443`

> [!NOTE]
> Om du använder specialtecken som " \@ " i ditt lösen ord får du ett anslutnings fel på grund av att värdet parsas felaktigt.  Undvik det här problemet genom att koda lösen ordet i URL: en med ett verktyg som [URLDecode](https://www.urldecoder.org/).  

## <a name="next-steps"></a>Nästa steg

* Granska [data källorna](../agents/agent-data-sources.md) för att förstå de data källor som är tillgängliga för att samla in data från Windows-eller Linux-systemet. 
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar. 
* Lär dig mer om att [övervaka lösningar](../insights/solutions.md) som lägger till funktioner i Azure Monitor och även samla in data i arbets ytan Log Analytics.
