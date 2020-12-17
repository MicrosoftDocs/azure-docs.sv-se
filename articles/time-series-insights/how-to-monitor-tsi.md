---
title: Övervaknings Time Series Insights | Microsoft Docs
description: Övervaka Time Series Insights för tillgänglighet, prestanda och drift.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: cff0c54cf5aa8854273be9502f5cf6df4e0a055b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632937"
---
# <a name="monitoring-time-series-insights"></a>Övervaknings Time Series Insights

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Time Series Insights och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data.

## <a name="monitor-overview"></a>Övervaka översikt

På sidan **Översikt** i Azure Portal för varje Time Series Insights-miljö finns en kort vy över resursanvändningen, till exempel antalet mottagna meddelanden och antalet byte som lagras. Den här informationen är användbar, men bara en liten del av övervaknings data är tillgängliga i det här fönstret. En del av dessa data samlas in automatiskt och är tillgängliga för analys så fort du skapar resursen. Du kan aktivera ytterligare typer av data insamling med en del konfiguration.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor

Time Series Insights skapar övervaknings data med [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview), som är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt.

Börja med artikeln [övervakning av Azure-resurser med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), vilket beskriver följande begrepp:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva specifika data som samlats in för Azure Time Series Insights. Dessa avsnitt innehåller också exempel på hur du konfigurerar data insamling och analyserar data med Azure-verktyg.

> [!TIP]
> För att förstå kostnader som är kopplade till Azure Monitor, se [användning och beräknade kostnader](../azure-monitor/platform/usage-estimated-costs.md). För att förstå hur lång tid det tar för dina data att visas i Azure Monitor, se [logg data](../azure-monitor/platform/data-ingestion-time.md)Inhämtnings tid.

## <a name="monitoring-data-from-azure-time-series-insights"></a>Övervaknings data från Azure Time Series Insights

Azure Time Series Insights samlar in samma typer av övervaknings data som andra Azure-resurser som beskrivs i [övervaknings data från Azure-resurser](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Se [Azure Time Series Insights övervaknings data referens](how-to-monitor-tsi-reference.md) för en detaljerad referens för de loggar och mått som du kan samla in.

## <a name="collection-and-routing"></a>Samling och routning

Plattforms mått samlas in och lagras automatiskt, men kan dirigeras till andra platser med hjälp av en diagnostisk inställning.

Resurs loggar samlas inte in och lagras förrän du skapar en diagnostisk inställning och dirigerar dem till en eller flera platser.
Mer information om hur du skapar en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](../azure-monitor/platform/diagnostic-settings.md) . När du skapar en diagnostisk inställning anger du vilka kategorier av loggar som ska samlas in.

Du kan samla in loggar från följande kategorier för Azure Time Series Insights:

   | Kategori | Beskrivning |
   |---|---|
   | Ingress  | Kategorin Ingressn spårar fel som inträffar i ingångs pipeline. Den här kategorin innehåller fel som uppstår när händelser tas emot (till exempel fel vid anslutning till en händelse källa) och bearbetning av händelser (till exempel fel vid parsning av en händelse nytto Last). |

## <a name="analyzing-metrics"></a>Analyserar mått

Du kan analysera mått för Azure Time Series Insights, tillsammans med mått från andra Azure-tjänster, genom att öppna mått från Azure Monitor-menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) .

En lista över de plattforms mått som samlas in finns i [övervaknings Azure Time Series Insights data referens](how-to-monitor-tsi-reference.md#metrics)

I det här exemplet visas antalet byte som tagits emot från alla händelse källor i din Azure Time Series Insightss miljö.

**Inkommande mottagna byte** i [ ![ Azure Time Series inkommande mottagna byte](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

Exemplet visar antalet byte som har bearbetats och är tillgängliga för frågan i din Azure Time Series Insightss miljö.

**Ingress lagrade byte** [ ![ Azure Time Series ingångs lagrade byte](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Analysera loggar
Du kan komma åt resurs loggar antingen som en BLOB i ett lagrings konto, som händelse data eller genom att logga analys frågor.

Data i Azure Monitor loggar lagras i tabeller som varje tabell har en egen uppsättning unika egenskaper.

Alla resurs loggar i Azure Monitor har samma fält följt av tjänstespecific-fält. Det gemensamma schemat beskrivs i [Azure Monitor resurs logg schema](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). En lista över typer av resurs loggar som samlas in för Azure Time Series Insights finns i [Azure Time Series Insights övervaknings data referens](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights lagrar data i följande tabeller.

| Tabell | Beskrivning |
|:---|:---|
| TSIIngress | Tabellen som lagrar data från ingångs kategorin. Kategorin Ingressn spårar fel som inträffar i ingångs pipeline. Den här kategorin innehåller fel som uppstår när händelser tas emot (till exempel fel vid anslutning till en händelse källa) och bearbetning av händelser (till exempel fel vid parsning av en händelse nytto Last).

Om du vill dirigera data till Azure Monitor loggar måste du skapa en diagnostisk inställning för att skicka resurs loggar eller plattforms mått till en Log Analytics arbets yta. Mer information finns i [insamling och routning](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing).

## <a name="sample-queries"></a>Exempel frågor

Följande är frågor som du kan använda för att övervaka din Azure Time Series Insights-miljö:

+ Hämta information om händelse källans anslutnings problem under de senaste fem dagarna:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Få information om ogiltiga meddelanden som tagits emot under de senaste fem dagarna:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Aviseringar

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem i systemet innan kunderna märker dem. Du kan ställa in aviseringar för [mått](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview), [loggar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)och [aktivitets loggen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Olika typer av aviseringar har fördelar och nack delar.

När du skapar en varnings regel baserat på plattforms mått bör du vara medveten om att för Time Series Insights plattforms mått som samlas in i antal enheter, kan vissa agg regeringar inte vara tillgängliga eller användbara.

## <a name="next-steps"></a>Nästa steg

* Se [Azure Time Series Insights övervaknings data referens](how-to-monitor-tsi-reference.md) för referenser till loggar och mått som skapats av Azure Time Series Insights.
* Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) .
