---
title: Översikt över Azure Diagnostics-tillägg
description: Använd Azure Diagnostics för fel sökning, mäta prestanda, övervakning, trafik analys i moln tjänster, virtuella datorer och Service Fabric
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: f3cde32178449169b07f57d4abbc346d8ca89df4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623662"
---
# <a name="azure-diagnostics-extension-overview"></a>Översikt över Azure Diagnostics-tillägg
Azure-diagnostik tillägget är en [agent i Azure Monitor](../agents/agents-overview.md) som samlar in övervaknings data från gäst operativ systemet i Azure Compute-resurser, inklusive virtuella datorer. Den här artikeln innehåller en översikt över Azure-diagnostik-tillägget, inklusive de funktioner som stöds och alternativ för installation och konfiguration. 

> [!NOTE]
> Azure-diagnostik tillägget är en av de agenter som är tillgängliga för att samla in övervaknings data från gäst operativ systemet för beräknings resurser. Se [Översikt över Azure Monitor agenter ](../agents/agents-overview.md) för en beskrivning av de olika agenterna och vägledningen för att välja lämpliga agenter för dina behov.

## <a name="primary-scenarios"></a>Primära scenarier
De primära scenarierna som hanteras av tillägget för diagnostik är:

- Samla in gäst mått i Azure Monitor Mät värden.
- Skicka gäst loggar och mått till Azure Storage för arkivering.
- Skicka gäst loggar och mått till Azure Event Hub för att skicka dem utanför Azure.


## <a name="comparison-to-log-analytics-agent"></a>Jämförelse med Log Analytics agent
Log Analytics agenten i Azure Monitor kan också användas för att samla in övervaknings data från gäst operativ systemet för virtuella datorer. Du kan välja att använda antingen eller båda beroende på dina behov. En detaljerad jämförelse av de Azure Monitor agenterna finns i [Översikt över Azure Monitors agenter](../agents/agents-overview.md) . 

Viktiga skillnader att tänka på är:

- Azure-diagnostik-tillägget kan bara användas med virtuella Azure-datorer. Log Analytics agenten kan användas med virtuella datorer i Azure, andra moln och lokalt.
- Azure-diagnostik-tillägget skickar data till Azure Storage, [Azure Monitor mått](../platform/data-platform-metrics.md) (endast Windows) och Event Hubs. Log Analytics agent samlar in data till [Azure Monitor loggar](../platform/data-platform-logs.md).
- Log Analytics agent krävs för [lösningar](../monitor-reference.md#insights-and-core-solutions), [Azure Monitor for VMS](../insights/vminsights-overview.md)och andra tjänster som [Azure Security Center](../../security-center/index.yml).

## <a name="costs"></a>Kostnader
Det kostar inget för Azure Diagnostic-tillägget, men du kan debiteras avgifter för inmatade data. Kontrol lera [Azure Monitor prissättningen](https://azure.microsoft.com/pricing/details/monitor/) för målet där du samlar in data.

## <a name="data-collected"></a>Insamlade data
I följande tabeller visas de data som kan samlas in av tillägget Windows och Linux-diagnostik.

### <a name="windows-diagnostics-extension-wad"></a>Windows Diagnostics-tillägg (WAD)

| Datakälla | Description |
| --- | --- |
| Händelse loggar i Windows   | Händelser från händelse loggen i Windows. |
| Prestandaräknare | Numeriska värden mäter prestanda för olika aspekter av operativ system och arbets belastningar. |
| IIS-loggar             | Användnings information för IIS-webbplatser som körs på gäst operativ systemet. |
| Program loggar     | Spåra meddelanden som skrivits av ditt program. |
| .NET EventSource-loggar |Kod skrivnings händelser med hjälp av .NET [EventSource](/dotnet/api/system.diagnostics.tracing.eventsource) -klassen |
| [Manifestbaserade ETW-loggar](/windows/desktop/etw/about-event-tracing) |ETW (Event Tracing for Windows) händelser som genererats av en process. |
| Krasch dum par (loggar)   | Information om processens tillstånd om ett program kraschar. |
| Filbaserade loggar    | Loggar som skapats av ditt program eller din tjänst. |
| Diagnostikloggar för agent | Information om Azure-diagnostik sig själv. |


### <a name="linux-diagnostics-extension-lad"></a>LAD (Linux Diagnostics Extension)

| Datakälla | Description |
| --- | --- |
| Syslog | Händelser som skickas till Linux Event Logging-systemet.   |
| Prestandaräknare  | Numeriska värden mäter prestanda för olika aspekter av operativ system och arbets belastningar. |
| Loggfiler | Poster som skickas till en filbaserad logg.  |

## <a name="data-destinations"></a>Data destinationer
Azure Diagnostic-tillägget för både Windows och Linux samlar alltid in data i ett Azure Storage-konto. Se [Installera och konfigurera Windows Azure Diagnostics Extension (wad)](diagnostics-extension-windows-install.md) och [Använd Linux-diagnostiskt tillägg för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md) för en lista med vissa tabeller och blobbar där dessa data samlas in.

Konfigurera en eller flera *data mottagare* för att skicka data till andra ytterligare mål. I följande avsnitt listas de handfat som är tillgängliga för tillägget Windows och Linux-diagnostik.

### <a name="windows-diagnostics-extension-wad"></a>Windows Diagnostics-tillägg (WAD)

| Mål | Description |
|:---|:---|
| Azure Monitor-statistik | Samla in prestanda data till Azure Monitor mått. Se [Skicka gäst operativ system mått till Azure Monitor Metric-databasen](../platform/collect-custom-metrics-guestos-resource-manager-vm.md).  |
| Händelsehubbar | Använd Azure Event Hubs för att skicka data utanför Azure. Se [strömmande Azure-diagnostik data till Event Hubs](diagnostics-extension-stream-event-hubs.md) |
| Azure Storage blobbar | Skriv till data till blobbar i Azure Storage förutom tabeller. |
| Application Insights | Samla in data från program som körs i den virtuella datorn till Application Insights integrera med andra program övervakning. Se [Skicka diagnostikdata till Application Insights](diagnostics-extension-to-application-insights.md). |

Du kan också samla in WAD-data från lagring till en Log Analytics arbets yta för att analysera den med Azure Monitor loggar även om Log Analytics agent vanligt vis används för den här funktionen. Den kan skicka data direkt till en Log Analytics-arbetsyta och har stöd för lösningar och insikter som ger ytterligare funktioner.  Se [samla in Azure-diagnostikloggar från Azure Storage](../essentials/diagnostics-extension-logs.md). 


### <a name="linux-diagnostics-extension-lad"></a>LAD (Linux Diagnostics Extension)
LAD skriver data till tabeller i Azure Storage. Det stöder sinkarna i följande tabell.

| Mål | Description |
|:---|:---|
| Händelsehubbar | Använd Azure Event Hubs för att skicka data utanför Azure. |
| Azure Storage blobbar | Skriv till data till blobbar i Azure Storage förutom tabeller. |
| Azure Monitor-statistik | Installera teleympkvistar-agenten förutom LAD. Se [samla in anpassade mått för en virtuell Linux-dator med InfluxData-agenten för teleympkvistar](../platform/collect-custom-metrics-linux-telegraf.md).


## <a name="installation-and-configuration"></a>Installation och konfiguration
Diagnostiskt tillägg implementeras som ett [tillägg för virtuella datorer](../../virtual-machines/extensions/overview.md) i Azure, så det stöder samma installations alternativ med Resource Manager-mallar, POWERSHELL och CLI. Se tillägg [för virtuella datorer och funktioner för](../../virtual-machines/extensions/features-windows.md) [tillägg och funktioner för Windows och virtuella datorer för Linux](../../virtual-machines/extensions/features-linux.md) för allmän information om hur du installerar och underhåller tillägg för virtuella datorer.

Du kan också installera och konfigurera både Windows-och Linux-diagnostik i Azure Portal under **diagnostikinställningar** i avsnittet **övervakning** på menyn på den virtuella datorn.

I följande artiklar finns information om hur du installerar och konfigurerar Diagnostics-tillägget för Windows och Linux.

- [Installera och konfigurera Windows Azure Diagnostics-tillägget (WAD)](diagnostics-extension-windows-install.md)
- [Använda Linux-diagnostiktillägget för att övervaka mått och loggar](../../virtual-machines/extensions/diagnostics-linux.md)

## <a name="other-documentation"></a>Annan dokumentation

###  <a name="azure-cloud-service-classic-web-and-worker-roles"></a>Webb-och arbets roller i Azure Cloud Service (klassisk)
- [Introduktion till moln tjänst övervakning](../../cloud-services/cloud-services-how-to-monitor.md)
- [Aktivera Azure-diagnostik i Azure Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics.md)
- [Application Insights för Azure Cloud Services](../app/cloudservices.md)<br>[Spåra flödet av ett Cloud Services program med Azure-diagnostik](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md) 

### <a name="azure-service-fabric"></a>Azure Service Fabric
- [Övervaka och diagnostisera tjänster i en konfiguration för utveckling lokalt](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

## <a name="next-steps"></a>Nästa steg


* Lär dig att [använda prestanda räknare i Azure-diagnostik](../../cloud-services/diagnostics-performance-counters.md).
* Om du har problem med att starta eller hitta dina data i Azure Storage-tabeller läser du [felsöka Azure-diagnostik](diagnostics-extension-troubleshooting.md)
