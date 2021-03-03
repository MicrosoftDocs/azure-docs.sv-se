---
title: Mått i Azure Monitor | Microsoft Docs
description: Beskriver mått i Azure Monitor som är lätta att övervaka data som stöder nästan real tids scenarier.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8ecfd74a4d486a83add490501c2f7af4a4003b85
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700982"
---
# <a name="azure-monitor-metrics-overview"></a>Översikt över Azure Monitor mått
Azure Monitor mått är en funktion i Azure Monitor som samlar in numeriska data från [övervakade resurser](../monitor-reference.md) i en tids serie databas. Mått är numeriska värden som samlas in med jämna mellanrum och beskriver lite aspekt av ett system vid en viss tidpunkt. Mått i Azure Monitor är lätta och kan stödja scenarier i nära real tid som gör det särskilt användbart för aviseringar och snabb identifiering av problem. Du kan analysera dem interaktivt med Metrics Explorer och proaktivt meddelas via en avisering när ett värde korsar ett tröskelvärde eller visualisera dem i en arbets bok eller instrument panel.


> [!NOTE]
> Azure Monitor mått är en hälft av data plattformen som stöder Azure Monitor. Den andra är [Azure Monitor loggar](../logs/data-platform-logs.md) som samlar in och ordnar logg-och prestanda data och gör det möjligt att analysera med ett omfattande frågespråk. Mått är mer lätta än data i Azure Monitor loggar och kan stödja scenarier i nära real tid som gör dem särskilt användbara för avisering och snabb identifiering av problem. Mått kan bara lagra numeriska data i en viss struktur, medan loggar kan lagra olika typer av data med sin egen struktur. Du kan också utföra komplex analys på loggar data med hjälp av logg frågor som inte kan användas för analys av mät data.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Vad kan du göra med Azure Monitor mått?
I följande tabell visas de olika sätt som du kan använda mått i Azure Monitor.

|  |  |
|:---|:---|
| **Analysera** | Använd [Metrics Explorer](metrics-charts.md) för att analysera insamlade mått i ett diagram och jämföra mått från olika resurser. |
| **Varning** | Konfigurera en [regel för mått varningar](../alerts/alerts-metric.md) som skickar ett meddelande eller [automatiserar en åtgärd](../alerts/action-groups.md) när mått värdet korsar ett tröskelvärde. |
| **Visualisera** | Fäst ett diagram från metrics Explorer till en [Azure-instrumentpanel](../app/tutorial-app-dashboards.md).<br>Skapa en [arbets bok](../visualize/workbooks-overview.md) som ska kombineras med flera data uppsättningar i en interaktiv rapport. Exportera resultatet av en fråga till [Grafana](../visualize/grafana-plugin.md) för att dra nytta av dess instrument panel och kombinera med andra data källor. |
| **Automatisera** |  Använd [autoskalning](../autoscale/autoscale-overview.md) för att öka eller minska resurser baserat på ett mått värde som korsar ett tröskelvärde. |
| **Hämta** | Komma åt Mät värden från en kommando rad med  [PowerShell-cmdletar](/powershell/module/az.applicationinsights)<br>Använd [REST API](./rest-api-walkthrough.md)för att komma åt Mät värden från anpassade program.<br>Komma åt Mät värden från en kommando rad med  [CLI](/cli/azure/monitor/metrics). |
| **Export** | [Dirigera mått till loggar](./resource-logs.md#send-to-azure-storage) för att analysera data i Azure Monitor mått tillsammans med data i Azure Monitor loggar och lagra mått värden i mer än 93 dagar.<br>Strömma mått till en [Event Hub](./stream-monitoring-data-event-hubs.md) för att dirigera dem till externa system. |
| **Arkiv** | [Arkivera](./platform-logs-overview.md) prestanda-eller hälso historiken för din resurs för efterlevnad, granskning eller offline rapportering. |

![Mått översikt](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Datainsamling
Det finns tre grundläggande källor med mått som samlas in av Azure Monitor. När dessa mått samlas in i Azure Monitor Metric-databasen kan de utvärderas tillsammans oavsett källa.

**Azure-resurser**. Plattforms mått skapas av Azure-resurser och ger dig insyn i deras hälsa och prestanda. Varje typ av resurs skapar en [distinkt uppsättning mått](./metrics-supported.md) utan någon konfiguration som krävs. Plattforms mått samlas in från Azure-resurser med en minuters frekvens om inget annat anges i måttets definition. 

**Program**. Mått skapas av Application Insights för dina övervakade program och hjälper dig att identifiera prestanda problem och spåra trender i hur ditt program används. Detta inkluderar sådana värden som _svars tid_ och _webb läsar undantag_ i servern.

**Virtuella dator agenter**. Mått samlas in från gäst operativ systemet på en virtuell dator. Aktivera gäst operativ systemets mått för virtuella Windows-datorer med [Windows Diagnostic Extension (wad)](../agents/diagnostics-extension-overview.md) och för virtuella Linux-datorer med InfluxData f- [agenten](https://www.influxdata.com/time-series-platform/telegraf/).

**Anpassade mått**. Du kan definiera mått utöver de standard mått som är tillgängliga automatiskt. Du kan [definiera anpassade mått i ditt program](../app/api-custom-events-metrics.md) som övervakas av Application Insights eller skapa anpassade mått för en Azure-tjänst med hjälp av [anpassade Metrics-API: et](./metrics-store-custom-rest-api.md).

- Se [vad som övervakas av Azure Monitor?](../monitor-reference.md) för en fullständig lista över data källor som kan skicka data till Azure Monitor mått.

## <a name="metrics-explorer"></a>Måttutforskare
Använd [Metrics Explorer](metrics-charts.md) för att interaktivt analysera data i din mått databas och rita upp värdena för flera mått över tid. Du kan fästa diagrammen på en instrument panel för att visa dem med andra visualiseringar. Du kan också hämta mått med hjälp av [Azures övervaknings REST API](./rest-api-walkthrough.md).

![Metrics Explorer](media/data-platform-metrics/metrics-explorer.png)

- I [komma igång med Azure Monitor Metrics Explorer](./metrics-getting-started.md) kan du komma igång med hjälp av Metrics Explorer.

## <a name="data-structure"></a>Datastruktur
Data som samlas in av Azure Monitor mått lagras i en databas för tids serier som är optimerade för analys av tidsstämplade data. Varje uppsättning metriska värden är en tids serie med följande egenskaper:

* Tiden då värdet samlades in
* Resursen som värdet är associerat med
* Ett namn område som fungerar som en kategori för måttet
* Ett mått namn
* Själva värdet
* Vissa mått kan ha flera dimensioner enligt beskrivningen i [flerdimensionella mått](#multi-dimensional-metrics). Anpassade mått kan ha upp till 10 dimensioner.

## <a name="multi-dimensional-metrics"></a>Flerdimensionella mått
En av utmaningarna med mät data är att den ofta har begränsad information för att tillhandahålla kontext för insamlade värden. Azure Monitor åtgärdar denna utmaning med flerdimensionella mått. Måtten för ett mått är namn/värde-par som innehåller ytterligare data för att beskriva måttets värde. Till exempel kan ett mått på måttet som _är tillgängligt_ ha en dimension som heter _enhet_ med värdena _C:_, _D:_, vilket kan visa antingen tillgängligt disk utrymme på alla enheter eller för varje enhet individuellt.

Exemplet nedan visar två data uppsättningar för ett hypotetiskt mått som kallas _nätverks data flöde_. Den första data uppsättningen har inga dimensioner. Den andra data mängden visar värdena med två dimensioner, _IP-adress_ och _riktning_:

### <a name="network-throughput"></a>Nätverks data flöde

| Timestamp     | Mått värde |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1 331,8 kbps |
| 8/9/2017 8:15 | 1 141,4 kbps |
| 8/9/2017 8:16 | 1 110,2 kbps |

Detta icke-dimensionella mått kan bara svara på en grundläggande fråga som "vad hade mitt nätverks flöde vid en specifik tidpunkt?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Nätverks data flöde + två dimensioner ("IP" och "Direction")

| Timestamp     | Dimension "IP"   | Dimension "Direction" | Mått värde|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "Skicka"    | 646,5 kbps |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "ta emot" | 420,1 kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "Skicka"    | 150,0 kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "ta emot" | 115,2 kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "Skicka"    | 515,2 kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "ta emot" | 371,1 kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "Skicka"    | 155,0 kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "ta emot" | 100,1 kbps |

Det här måttet kan svara på frågor som "vad var nätverks data flödet för varje IP-adress?" och "hur mycket data har skickats jämfört med mottagen?" Flerdimensionella mått erbjuder ytterligare analytiska och diagnostiska värden jämfört med icke-dimensionella mått.

## <a name="retention-of-metrics"></a>Kvarhållning av mått
För de flesta resurser i Azure lagras måtten i 93 dagar. Det finns vissa undantag:

**Gäst operativ systemets mått**
-   **Klassiska gäst operativ systemets mått**. Detta är prestanda räknare som samlas in av [Windows Diagnostic-tillägget (wad)](../agents/diagnostics-extension-overview.md) eller [Linux Diagnostic Extension (lad)](../../virtual-machines/extensions/diagnostics-linux.md) och dirigeras till ett Azure Storage-konto. Kvarhållning för dessa mått är 14 dagar.
-   **Gäst operativ systemets mått skickas till Azure Monitor mått**. Detta är prestanda räknare som samlas in av [Windows Diagnostic-tillägget (wad)](../agents/diagnostics-extension-overview.md) och skickas till [Azure Monitor data mottagare](../agents/diagnostics-extension-overview.md#data-destinations)eller via [InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) -på Linux-datorer. Kvarhållning för dessa mått är 93 dagar.
-   **Gäst operativ system mått som samlats in av Log Analytics-agenten**. Detta är prestanda räknare som samlas in av Log Analytics agent och skickas till en Log Analytics arbets yta. Kvarhållning för dessa mått är 31 dagar och kan utökas upp till två år.

**Application Insights log-baserade mått**. 
- Efter scenen översätts [loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md) till logg frågor. Deras kvarhållning matchar kvarhållningen av händelser i underliggande loggar. För Application Insights resurser lagras loggar i 90 dagar.


> [!NOTE]
> Du kan [Skicka plattforms mått för Azure Monitor resurser till en Log Analytics arbets yta](./resource-logs.md#send-to-azure-storage) för långsiktiga trender.





## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Monitor data plattform](../data-platform.md).
- Lär dig mer om [loggdata i Azure Monitor](../logs/data-platform-logs.md).
- Lär dig mer om [övervaknings data som är tillgängliga](../agents/data-sources.md) för olika resurser i Azure.