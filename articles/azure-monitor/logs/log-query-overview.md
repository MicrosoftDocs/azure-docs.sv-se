---
title: Logg frågor i Azure Monitor
description: Referensinformation för Kusto-frågespråket som används av Azure Monitor. Innehåller ytterligare element som är speciella för Azure Monitor och element som inte stöds i Azure Monitor logg frågor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 529fc432bf8777ef7e2b527f08e9cb59e42bf156
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041033"
---
# <a name="log-queries-in-azure-monitor"></a>Logg frågor i Azure Monitor
Azure Monitor loggar baseras på Azure Datautforskaren, och logg frågor skrivs med samma KQL (Kusto Query Language). Detta är ett stort språk utformat för att vara lätt att läsa och redigera, så du bör kunna börja skriva frågor med några grundläggande rikt linjer.

Områden i Azure Monitor där du kommer att använda frågor är följande:

- [Log Analytics](../logs/log-analytics-overview.md). Huvud verktyg i Azure Portal för att redigera logg frågor och analysera resultaten interaktivt. Även om du planerar att använda en logg fråga någon annan stans i Azure Monitor, skriver du vanligt vis och testar den i Log Analytics innan du kopierar den till den slutgiltiga platsen.
- [Logga varnings regler](../alerts/alerts-overview.md). Identifiera problem med data i din arbets yta proaktivt.  Varje varnings regel baseras på en logg fråga som körs automatiskt med jämna mellanrum.  Resultaten kontrol leras för att avgöra om en avisering ska skapas.
- [Arbets böcker](../visualize/workbooks-overview.md). Inkludera resultaten av logg frågor med hjälp av olika visualiseringar i interaktiva visuella rapporter i Azure Portal.
- [Azure-instrumentpaneler](../visualize/tutorial-logs-dashboards.md). Fäst resultatet av en fråga i en Azure-instrumentpanel som gör att du kan visualisera logg-och mät data tillsammans och dela dem med andra Azure-användare.
- [Logic Apps](../logs/logicapp-flow-connector.md).  Använd resultatet av en logg fråga i ett automatiserat arbets flöde med hjälp av Logic Apps.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). Använd resultatet av en logg fråga i ett PowerShell-skript från en kommando rad eller en Azure Automation Runbook som använder Get-AzOperationalInsightsSearchResults.
- [API för Azure Monitor loggar](https://dev.loganalytics.io). Hämta loggdata från arbets ytan från valfri REST API-klient.  API-förfrågan innehåller en fråga som körs mot Azure Monitor för att avgöra vilka data som ska hämtas.

## <a name="getting-started"></a>Komma igång
Det bästa sättet att komma igång med att lära sig att skriva logg frågor med hjälp av KQL utnyttjar de tillgängliga självstudierna och exemplen.

- [Log Analytics själv studie](./log-analytics-tutorial.md) kurs om hur du använder funktionerna i Log Analytics som är det verktyg som du använder i Azure Portal för att redigera och köra frågor. Du kan också skriva enkla frågor utan att du behöver arbeta direkt med frågespråket. Om du inte har använt Log Analytics förut börjar du här så att du förstår verktyget som du kommer att använda med de andra självstudierna och exemplen.
- [KQL-självstudie](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) – guidad genom gång av grundläggande KQL-koncept och vanliga operatörer. Detta är den bästa platsen för att komma igång snabbt med själva språket och strukturen på logg frågor. 
- [Exempel frågor](../logs/example-queries.md) – Beskrivning av exempel frågor som är tillgängliga i Log Analytics. Du kan använda frågorna utan att ändra eller använda dem som exempel för att lära dig KQL.
- [Fråga exempel](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) – exempel frågor som illustrerar olika koncept.



## <a name="reference-documentation"></a>Referens dokumentation
[Dokumentation för KQL](/azure/data-explorer/kusto/query/) , inklusive referensen för alla kommandon och operatorer, finns i Azure datautforskaren-dokumentationen. Även om du får kunskap med KQL, använder du fortfarande regelbundet referensen för att undersöka nya kommandon och scenarier som du inte har använt tidigare.


## <a name="language-differences"></a>Språkskillnader
När Azure Monitor använder samma KQL som Azure Datautforskaren finns det vissa skillnader. I KQL-dokumentationen anges de operatörer som inte stöds av Azure Monitor eller som har olika funktioner. Operatorer som är speciella för Azure Monitor dokumenteras i Azure Monitor innehållet. I följande avsnitt finns en lista över skillnaderna mellan olika versioner av språket för snabb referens.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruktioner stöds inte i Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Frågeparametrar](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funktioner som inte stöds i Azure Monitor

* [kluster ()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [databas ()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatorer stöds inte i Azure Monitor

* [Anslutning mellan kluster](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plugin-program stöds inte i Azure Monitor

* [Python-plugin](/azure/kusto/query/pythonplugin)
* [sql_request-plugin-program](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Ytterligare operatörer i Azure Monitor
Följande operatörer stöder vissa Azure Monitor funktioner och är inte tillgängliga utanför Azure Monitor.

* [app ()](../logs/app-expression.md)
* [resurs ()](./resource-expression.md)
* [arbets yta ()](../logs/workspace-expression.md)

## <a name="next-steps"></a>Nästa steg
- Gå igenom en [själv studie kurs om att skriva frågor](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor).
- Få till gång till fullständig [referens dokumentation för Kusto-frågespråk](/azure/kusto/query/).