---
title: Övervaka Azure Functions med Azure Monitor loggar
description: Lär dig hur du använder Azure Monitor loggar med Azure Functions för att övervaka funktions körningar.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: b6ce9e77421df0563810fd7f8255720c1fd2d0c0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591084"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Övervaka Azure Functions med Azure Monitor loggar

Azure Functions erbjuder en integrering med [Azure Monitors loggar](../azure-monitor/logs/data-platform-logs.md) för att övervaka funktioner. Den här artikeln visar hur du konfigurerar Azure Functions att skicka systemgenererade och användarspecifika loggar till Azure Monitor loggar.

Azure Monitor loggar ger dig möjlighet att konsolidera loggar från olika resurser i samma arbets yta, där den kan analyseras med [frågor](../azure-monitor/logs/log-query-overview.md) för att snabbt hämta, konsolidera och analysera insamlade data.  Du kan skapa och testa frågor med [Log Analytics](../azure-monitor/logs/log-query-overview.md) i Azure Portal och sedan antingen analysera data med hjälp av dessa verktyg eller spara frågor för användning med [visualiseringar](../azure-monitor/visualizations.md) eller [varnings regler](../azure-monitor/alerts/alerts-overview.md).

Azure Monitor använder en version av [Kusto-frågespråket](/azure/kusto/query/) som används av Azure datautforskaren som är lämplig för enkla logg frågor, men även avancerade funktioner som agg regeringar, kopplingar och smart analys. Du kan snabbt lära dig frågespråket med [flera lektioner](../azure-monitor/logs/get-started-queries.md).

> [!NOTE]
> Integrering med Azure Monitor loggar finns för närvarande i en offentlig för hands version för v2-och v3-Function-appar som körs på Windows-förbrukning, Premium och dedikerade värd planer.

## <a name="setting-up"></a>Konfigurera

1. I avsnittet **övervakning** i din Function-app i [Azure Portal](https://portal.azure.com)väljer du **diagnostikinställningar** och väljer sedan **Lägg till diagnostisk inställning**.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Välj diagnostikinställningar":::

1. På sidan **diagnostikinställningar** under **kategori information** och **logg** väljer du **FunctionAppLogs**.

   **FunctionAppLogs** -tabellen innehåller de önskade loggarna.

1. Under **mål information** väljer **du skicka till Log Analytics**. Välj sedan **Log Analytics arbets ytan**. 

1. Ange ett **namn på diagnostikinställningar** och välj sedan **Spara**.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Lägg till en diagnostisk inställning":::

## <a name="user-generated-logs"></a>Loggar som skapats av användare

Om du vill skapa anpassade loggar använder du loggnings instruktionen som är specifik för ditt språk. Här är exempel kods tycken:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Skicka frågor till loggarna

Så här frågar du på genererade loggar:
 
1. Välj **diagnostikinställningar** från din Function-app. 

1. I listan **diagnostiska inställningar** väljer du den Log Analytics arbets yta som du konfigurerade för att skicka funktions loggarna till. 

1. På sidan **Log Analytics arbets yta** väljer du **loggar**.

   Azure Functions skriver alla loggar till tabellen **FunctionAppLogs** under **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Frågefönstret i Log Analytics arbets yta":::

Här följer några exempel frågor:

### <a name="all-logs"></a>Alla loggar

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Vissa funktions loggar

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Undantag

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Nästa steg

- Granska [Azure Functions översikt](functions-overview.md).
- Läs mer om [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md).
- Läs mer om [frågespråket](../azure-monitor/logs/get-started-queries.md).
