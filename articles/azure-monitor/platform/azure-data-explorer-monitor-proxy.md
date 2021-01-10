---
title: Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)
description: Använd Azure Datautforskaren för att utföra kors produkt frågor mellan Azure Datautforskaren, Log Analytics arbets ytor och klassiska Application Insights program i Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 8942735ed65f8aa0cf6d315568e00412adcb353a
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060545"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Fråga efter data i Azure Monitor med Azure Datautforskaren (förhands granskning)

Azure Datautforskaren stöder kors tjänst frågor mellan Azure Datautforskaren, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)och [Log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). Du kan sedan fråga din Log Analytics/Application Insights-arbetsyta med Azure Datautforskaren-verktyg och se den i en kors tjänst fråga. Artikeln visar hur du skapar en kors tjänst fråga och hur du lägger till arbets ytan Log Analytics/Application Insights till Azure Datautforskaren Web UI.

Azure Datautforskaren Cross service-frågor Flow: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure Data Explorer-proxyläge.":::

> [!NOTE]
> * Möjligheten att fråga Azure Monitor data från Azure Datautforskaren, antingen direkt från Azure Datautforskaren klient verktyg eller indirekt genom att köra en fråga på ett Azure Datautforskaren-kluster, är i förhands gransknings läge.
>* Kontakta vår [fråga om kors tjänster](mailto:adxproxy@microsoft.com) med eventuella frågor.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Lägga till en arbets yta Log Analytics/Application Insights till Azure Datautforskaren-klient verktyg

1. Verifiera att ditt Azure Datautforskaren interna kluster (till exempel *Hjälp* kluster) visas på den vänstra menyn innan du ansluter till Log Analytics eller Application Insights klustret.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure Datautforskaren internt kluster.":::

 I Azure Datautforskaren UI ( https://dataexplorer.azure.com/clusters) väljer du **Lägg till kluster**.

2. I fönstret **Lägg till kluster** lägger du till URL: en för La-eller AI-klustret.

    * För LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * För AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Välj **Lägg till**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Lägg till kluster.":::
 
>[!NOTE]
>Om du lägger till en anslutning till fler än en Log Analytics/Application Insights-arbetsyta ska du ange ett annat namn. Annars har de samma namn i det vänstra fönstret.

 När anslutningen har upprättats visas din Log Analytics eller Application Insights arbets yta i det vänstra fönstret med ditt interna Azure Datautforskaren-kluster.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics och Azure Datautforskaren-kluster.":::
 
> [!NOTE]
> Antalet Azure Monitor arbets ytor som kan mappas är begränsat till 100.

## <a name="create-queries-using-azure-monitor-data"></a>Skapa frågor med hjälp av Azure Monitor data

Du kan köra frågorna med hjälp av klient verktyg som har stöd för Kusto-frågor, till exempel: Kusto Explorer, Azure Datautforskaren Web UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, lins, REST API.

> [!NOTE]
> Fråge funktionen för kors tjänster används endast för data hämtning. Mer information finns i [funktions stöd](#function-supportability).

> [!TIP]
> * Databas namnet måste ha samma namn som den resurs som anges i kors tjänst frågan. Namnen är skiftlägeskänsliga.
> * I kors kluster frågor kontrollerar du att namngivningen av Application Insights appar och Log Analytics arbets ytor är korrekt.
> * Om namnen innehåller specialtecken ersätts de av URL-kodning i kors tjänst frågan.
> * Om namnen innehåller tecken som inte uppfyller [KQL-ID: n](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), ersätts de av bindestrecks **-** tecknet.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Direkt fråga på din Log Analytics eller Application Insights arbets ytor från Azure Datautforskaren-klient verktyg

Kör frågor på din Log Analytics eller Application Insights arbets ytor. Kontrol lera att din arbets yta är markerad i det vänstra fönstret.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Fråga Log Analytics-arbetsyta.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Kors fråga för din Log Analytics eller Application Insights och Azure Datautforskaren inbyggda kluster

När du kör frågor för kors kluster tjänst kontrollerar du att ditt Azure Datautforskaren inbyggda kluster är markerat i den vänstra rutan. Följande exempel visar hur du kombinerar Azure Datautforskaren Cluster-tabeller [med hjälp av union](/azure/data-explorer/kusto/query/unionoperator) med Log Analytics-arbetsytan.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Kors tjänst fråga från Azure-Datautforskaren.":::

Om du använder [ `join` operatorn](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator)i stället för union kan [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) du behöva köra den på ett internt Azure datautforskaren-kluster.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Koppla data från ett Azure Datautforskaren-kluster i en klient organisation med en Azure Monitor-resurs i en annan

Frågor över flera klienter mellan-tjänsterna stöds inte. Du är inloggad på en enda klient för att köra frågan som sträcker sig över båda resurserna.

Om Azure Datautforskaren-resursen finns i klient organisationen "A" och Log Analytics arbets ytan är i klient organisationen "B" använder du någon av följande två metoder:

1. Med Azure Datautforskaren kan du lägga till roller för huvud konton i olika klienter. Lägg till ditt användar-ID i klienten ' B ' som en behörig användare i Azure Datautforskaren-klustret. Verifiera att egenskapen *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* i Azure datautforskaren-klustret innehåller klienten ' B '. Kör kors frågan fullständigt i klient organisationen ' B '.

2. Använd [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) för att projicera Azure Monitor resursen i klient organisationen "A".
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ansluta till Azure Datautforskaren-kluster från olika klienter

Kusto Explorer loggar automatiskt in till den klient som användar kontot ursprungligen tillhör. För att få åtkomst till resurser i andra klienter med samma användar konto måste det `tenantId` uttryckligen anges i anslutnings strängen: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Funktions stöd

Azure Datautforskaren Cross service-frågor stöder funktioner för både Application Insights och Log Analytics.
Den här funktionen gör det möjligt för kors kluster frågor att referera till en Azure Monitor tabell funktion direkt.
Följande kommandon stöds med kors tjänst frågan:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Följande bild visar ett exempel på att fråga en tabell funktion från Azure Datautforskaren Web UI.
Om du vill använda funktionen kör du namnet i frågefönstret.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Fråga en tabell funktion från Azure Datautforskaren Web UI.":::

## <a name="additional-syntax-examples"></a>Exempel på ytterligare syntax

Följande syntax är tillgängliga när du anropar Log Analytics-eller Application Insights-kluster:

|Beskrivning av syntax  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Databas i ett kluster som bara innehåller den definierade resursen i den här prenumerationen (**rekommenderas för kors kluster frågor**) |   kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Kluster som innehåller alla appar/arbets ytor i den här prenumerationen    |     kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Kluster som innehåller alla appar/arbets ytor i prenumerationen och som är medlemmar i den här resurs gruppen    |   kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Kluster som bara innehåller den definierade resursen i den här prenumerationen      |    kluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  kluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [data strukturen för Log Analytics arbets ytor och Application Insights](data-platform-logs.md).
- Lär dig att [skriva frågor i Azure datautforskaren](https://docs.microsoft.com/azure/data-explorer/write-queries).