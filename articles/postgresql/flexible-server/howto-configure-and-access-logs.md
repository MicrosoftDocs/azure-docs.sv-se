---
title: Konfigurera och komma åt loggar – flexibel Server-Azure Database for PostgreSQL
description: Så här kommer du åt databas loggar för Azure Database for PostgreSQL-flexibel Server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 59a2ddcc68a7c5a3b6fa3a3b315f4294d1625204
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607413"
---
# <a name="configure-and-access-logs-in-azure-database-for-postgresql---flexible-server"></a>Konfigurera och få åtkomst till loggar i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

PostgreSQL-loggar finns på varje nod i en flexibel Server. Du kan leverera loggar till en lagrings Server eller till en Analytics-tjänst. Loggarna kan användas för att identifiera, felsöka och reparera konfigurations fel och underoptimala prestanda.

## <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Du kan aktivera diagnostikinställningar för postgres-servern med hjälp av Azure Portal, CLI, REST API och PowerShell. Den logg kategori som ska väljas är **PostgreSQLLogs**.

Så här aktiverar du resurs loggar med hjälp av Azure Portal:

1. I portalen går du till *diagnostikinställningar* i navigerings menyn på postgres-servern.
   
2. Välj *Lägg till diagnostisk inställning*.
   :::image type="content" source="media/howto-logging/diagnostic-settings.png" alt-text="Knappen Lägg till diagnostikinställningar":::

3. Ge den här inställningen ett namn. 

4. Välj önskad slut punkt (lagrings konto, händelsehubben, Log Analytics). 

5. Välj logg typen **PostgreSQLLogs**.
   :::image type="content" source="media/howto-logging/diagnostic-create-setting.png" alt-text="Välj PostgreSQL-loggar":::

7. Spara inställningen.

Om du vill aktivera resurs loggar med PowerShell, CLI eller REST API går du till artikeln [diagnostiska inställningar](../../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Åtkomst till resurs loggar

Hur du kommer åt loggarna beror på vilken slut punkt du väljer. Information om Azure Storage finns i artikeln [Logga lagrings konto](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Information om Event Hubs finns i artikeln [Stream Azure-loggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

För Azure Monitor loggar skickas loggar till den valda arbets ytan. Postgres-loggarna använder samlings läget **AzureDiagnostics** , så att de kan frågas från AzureDiagnostics-tabellen. Fälten i tabellen beskrivs nedan. Läs mer om frågor och aviseringar i Översikt över [Azure Monitor loggar frågor](../../azure-monitor/logs/log-query-overview.md) .

Följande är frågor som du kan försöka komma igång med. Du kan konfigurera aviseringar baserat på frågor.

Sök efter alla postgres-loggar för en viss server under den senaste dagen

```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Sök efter alla anslutnings försök för icke-localhost

```kusto
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```

Frågan ovan visar resultaten under de senaste 6 timmarna för alla postgres Server-loggningar i den här arbets ytan.

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Log Analytics-frågor](../../azure-monitor/logs/log-analytics-tutorial.md)
- Lär dig mer om [Azure Event Hub](../../event-hubs/event-hubs-about.md)