---
title: Kabel data lösning i Azure Monitor | Microsoft Docs
description: Tråd data är konsoliderade nätverks-och prestanda data från datorer med Log Analytics agenter. Nätverksdata kombineras med dina loggdata, vilket hjälper dig att korrelera data.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732031"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Wire Data 2.0 (för hands version)-lösning i Azure Monitor (dras tillbaka)

![Wire Data-symbol](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>Lösningen för kabel data har ersatts med [VM-insikter](../vm/vminsights-overview.md) och [tjänstkarta lösning](../vm/service-map.md).  Båda använder Log Analytics agent och beroende agent för att samla in data om nätverks anslutningen i Azure Monitor.
>
>Stöd för kabel data lösning upphör den **31 mars 2022**.  Innan indragnings datumet kan befintliga kunder som använder lösningen för Wire Data 2.0 (för hands version) fortsätta att använda den.
>
>Nya och befintliga kunder bör installera [VM Insights](../vm/vminsights-enable-overview.md) -eller tjänstkarta- [lösningen](../vm/service-map.md).  De data uppsättningar för kartan som samlas in är jämförbara med data uppsättningen Wire Data 2.0 (för hands version).  VM Insights innehåller Tjänstkarta data uppsättning tillsammans med ytterligare prestanda data och funktioner för analys. Båda erbjudandena har [anslutningar till Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options).
 

Tråd data är sammanställda nätverks-och prestanda data som samlas in från Windows-anslutna och Linux-anslutna datorer med Log Analytics agent, inklusive de som övervakas av Operations Manager i din miljö. Nätverksdata kombineras med dina övriga loggdata, vilket hjälper dig att korrelera data.

Förutom Log Analytics-agenten använder enheten för data överföring Microsoft beroende agenter som du installerar på datorer i din IT-infrastruktur. Beroendeagenterna övervakar nätverksdata som skickas till och från dina datorer på nätverksnivåerna 2–3 i [OSI-modellen](https://en.wikipedia.org/wiki/OSI_model), inklusive de olika protokoll och portar som används. Data skickas sedan till Azure Monitor med hjälp av agenter.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migrera till Azure Monitor VM-insikter eller Tjänstkarta

I många fall ser vi att kunderna ofta har både Wire Data 2.0 (förhands granskning) och  [VM-insikter](../vm/vminsights-overview.md) eller [tjänstkarta-lösning](../vm/service-map.md) som redan är aktive rad på samma virtuella datorer.  Det innebär att ersättnings erbjudandet aktive ras på den virtuella datorn.  Du kan helt enkelt [ta bort Wire data 2.0 (för hands version)-lösningen från arbets ytan Log Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

Om du har virtuella datorer som bara har Wire Data 2.0 (förhands granskning) aktiverade på dem, kan du publicera de virtuella [datorerna i VM-insikter](../vm/vminsights-enable-overview.md) eller [tjänstkarta lösning](../vm/service-map.md) och sedan [ta bort Wire data 2.0 (för hands version)-lösningen från din Log Analytics-arbetsyta](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Migrera dina frågor till VMConnection-tabellen från Azure Monitor VM Insights

### <a name="agents-providing-data"></a>Agenter som tillhandahåller data

#### <a name="wire-data-20-query"></a>Wire Data 2.0 fråga

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>VM-insikter och Tjänstkarta fråga

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>IP-adresser för de agenter som tillhandahåller data

#### <a name="wire-data-20-query"></a>Wire Data 2.0 fråga

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM-insikter och Tjänstkarta fråga

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>All utgående kommunikation via fjärr-IP-adress

#### <a name="wire-data-20-query"></a>Wire Data 2.0 fråga

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM-insikter och Tjänstkarta fråga

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>Byte mottagna efter protokoll namn

#### <a name="wire-data-20-query"></a>Wire Data 2.0 fråga

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>VM-insikter och Tjänstkarta fråga

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Mängden nätverks trafik (i byte) per process

#### <a name="wire-data-20-query"></a>Wire Data 2.0 fråga

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>VM-insikter och Tjänstkarta fråga

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>Fler exempel frågor

Läs dokumentationen för [VM Insights-loggen och Sök](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) efter fler exempel frågor i dokumentationen för [VM Insights-avisering](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) .

## <a name="uninstall-wire-data-20-solution"></a>Avinstallera Wire Data 2.0 lösning

Om du vill avinstallera Wire Data 2.0 behöver du bara ta bort lösningen från Log Analytics-arbetsytan (er).  Detta leder till följande:

* det kabel Datahanterings paket som tas bort från de virtuella datorer som är anslutna till arbets ytan 
* data typen Wire data visas inte längre i din arbets yta

Följ [de här anvisningarna](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) för att ta bort lösningen för kabel data.

>[!NOTE]
>Om du har antingen Tjänstkarta-eller VM Insights-lösningen på din arbets yta tas inte hanterings paketet bort, eftersom dessa lösningar också använder detta hanterings paket.

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 hanterings paket

När Wire Data är aktiverat på en Log Analytics-arbetsyta, skickas ett hanteringspaket på 300 KB till alla Windows-servrar på arbetsytan. Om du använder System Center Operations Manager-agenter i en [ansluten hanteringsgrupp](../agents/om-agents.md), distribueras Dependency Monitor-hanteringspaketet från System Center Operations Manager. Om agenterna är direkt anslutna levererar Azure Monitor hanterings paketet.

Hanteringspaketet heter Microsoft.IntelligencePacks.ApplicationDependencyMonitor. Det skrivs till: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. Datakällan som hanteringspaketet använder är: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>Avinstallera beroende agenten

>[!NOTE]
>Om du planerar att ersätta lednings data med antingen Tjänstkarta eller VM-insikter bör du inte ta bort beroende agenten.

Använd följande avsnitt som hjälp för att ta bort beroende agenten.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Avinstallera beroende agenten i Windows

En administratör kan avinstallera beroende agenten för Windows via kontroll panelen.

En administratör kan också köra%program%\Microsoft-beroende Agent\Uninstall.exe för att avinstallera beroende agenten.

### <a name="uninstall-the-dependency-agent-on-linux"></a>Avinstallera beroende agenten på Linux

Om du vill avinstallera beroende agenten från Linux måste du ta bort själva agenten och anslutningen, som installeras automatiskt med agenten. Du kan avinstallera båda med hjälp av följande kommando:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Använda Wire Data 2.0-lösningen

På sidan **Översikt** för Log Analytics-arbetsytan i Azure Portal, klickar du på panelen **Wire Data 2.0** för att öppna instrumentpanelen för Wire Data. Instrumentpanelen innehåller bladen i följande tabell. Varje blad visar en lista med upp till tio objekt som matchar bladets kriterier för angivet omfång och tidsintervall. Du kan köra en loggsökning som returnerar alla poster genom att klicka på **Se alla** längst ned på bladet eller genom att klicka på bladrubriken.

| **Blad** | **Beskrivning** |
| --- | --- |
| Agenter som samlar in nätverkstrafik | Visar antalet agenter som samlar in nätverkstrafik och visar en lista med de främsta 10 datorerna som hämtar trafik. Klicka på siffran om du vill köra en loggsökning efter <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Klicka på en dator i listan om du vill köra en loggsökning som returnerar det totala antalet byte som har hämtats. |
| Lokala undernät | Visar antalet lokala undernät som agenterna har identifierat.  Klicka på siffran om du vill köra en loggsökning efter <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> som visar en lista över alla undernät med antalet byte som skickats i var och en. Klicka på ett undernät i listan om du vill köra en loggsökning som returnerar det totala antalet byte som har skickats i undernätet. |
| Protokoll på programnivå | Visar antalet protokoll på programnivå som används och som identifierats av agenterna. Klicka på siffran om du vill köra en loggsökning efter <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Klicka på ett protokoll om du vill köra en loggsökning som returnerar det totala antalet byte som har skickats med protokollet. |

![Wire Data-instrumentpanel](./media/wire-data/wire-data-dash.png)

Du kan använda bladet **Agenter som samlar in nätverkstrafik** för att avgöra hur mycket nätverksbandbredd som används av datorerna. Det här bladet kan hjälpa dig att enkelt hitta den _trafikintensivaste_ datorn i din miljö. Sådana datorer kan vara överbelastade, bete sig onormalt eller använda fler nätverksresurser än normalt.

![Skärm bild av bladet agenter som fångar in nätverks trafik på Wire Data 2.0 instrument panelen som visar nätverks bandbredden som används av varje dator.](./media/wire-data/log-search-example01.png)

På liknande sätt kan du använda bladet **Lokala undernät** till att avgöra hur mycket nätverkstrafik som passerar genom dina undernät. Användarna definierar ofta undernät runt kritiska områden för sina program. Det här bladet visar dessa områden.

![Skärm bild av bladet lokala undernät på Wire Data 2.0 instrument panelen som visar nätverks bandbredden som används av varje LocalSubnet.](./media/wire-data/log-search-example02.png)

Bladet **Protokoll på programnivå** är användbart eftersom det alltid är bra att veta vilka protokoll som används. Du kanske till exempel misstänker att SSH inte används i din nätverksmiljö. Genom att läsa den information som finns i bladet kan du snabbt få din misstanke bekräftad eller motbevisad.

![Skärm bild av bladet protokoll på program nivå på Wire Data 2.0 instrument panelen som visar den nätverks bandbredd som används av varje protokoll.](./media/wire-data/log-search-example03.png)

Det är också bra att veta om protokolltrafiken ökar eller minskar med tiden. Om till exempel mängden data som skickas av ett program ökar, kan det vara något som du bör vara medveten om eller vara uppmärksam på.

## <a name="input-data"></a>Indata

Wire-datan samlar in metadata om nätverkstrafik med hjälp av de agenter som du har aktiverat. Varje agent skickar data var 15:e sekund.

## <a name="output-data"></a>Utdata

En post av typen _WireData_ skapas för varje typ av indata. WireData-poster har egenskaper enligt följande tabell:

| Egenskap | Beskrivning |
|---|---|
| Dator | Namn på den dator där data samlades in |
| TimeGenerated | Tid för posten |
| LocalIP | IP-adressen för den lokala datorn |
| SessionState | Ansluten eller frånkopplad |
| ReceivedBytes | Mängden byte som tagits emot |
| ProtocolName | Namnet på det nätverksprotokoll som används |
| IPVersion | IP-version |
| Riktning | Inkommande eller utgående |
| MaliciousIP | IP-adressen för en känd skadlig källa |
| Allvarlighetsgrad | Allvarlighetsgrad för misstänkt skadlig programvara |
| RemoteIPCountry | Land/region för fjärr-IP-adressen |
| ManagementGroupName | Namn på Operations Manager-hanteringsgrupp |
| SourceSystem | Källa där data samlades in |
| SessionStartTime | Starttid för sessionen |
| SessionEndTime | Sluttid för sessionen |
| LocalSubnet | Undernät där data samlades in |
| LocalPortNumber | Lokalt portnummer |
| RemoteIP | Fjärr-IP-adress som används av fjärrdatorn |
| RemotePortNumber | Portnummer som används av fjärr-IP-adressen |
| SessionID | Ett unikt värde som identifierar kommunikationssessionen mellan två IP-adresser |
| SentBytes | Antal skickade byte |
| TotalBytes | Totalt antal byte som skickats under sessionen |
| ApplicationProtocol | Namnet på det nätverksprotokoll som används   |
| ProcessID | Windows process-ID |
| ProcessName | Sökväg och filnamn för processen |
| RemoteIPLongitude | IP-longitudvärde |
| RemoteIPLatitude | IP-latitudvärde |

## <a name="next-steps"></a>Nästa steg

- Se [distribuera VM Insights](./vminsights-enable-overview.md) för krav och metoder som aktiverar övervakning för dina virtuella datorer.
- [Sök i loggar](../logs/log-query-overview.md) för att se detaljerade sökposter för wire-data.
