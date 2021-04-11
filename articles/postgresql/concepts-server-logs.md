---
title: Loggar – Azure Database for PostgreSQL-enskild server
description: Beskriver loggning av konfiguration, lagring och analys i Azure Database for PostgreSQL-enskild server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 31b3c0a1894280e90f696f6c107b67a3af7b1880
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605356"
---
# <a name="logs-in-azure-database-for-postgresql---single-server"></a>Loggar i Azure Database for PostgreSQL-enskild server

Med Azure Database for PostgreSQL kan du konfigurera och få åtkomst till postgres standard loggar. Loggarna kan användas för att identifiera, felsöka och reparera konfigurations fel och underoptimala prestanda. Loggnings information som du kan konfigurera och komma åt innehåller fel, fråga efter information, autovakuum-poster, anslutningar och kontroll punkter. (Åtkomst till transaktions loggar är inte tillgänglig).

Gransknings loggning görs tillgängligt via ett postgres-tillägg, pgaudit. Mer information finns i artikeln [gransknings begrepp](concepts-audit.md) .


## <a name="configure-logging"></a>Konfigurera loggning 
Du kan konfigurera postgres standard inloggning på servern med hjälp av loggnings server parametrarna. På varje Azure Database for PostgreSQL-Server `log_checkpoints` och `log_connections` är aktiverat som standard. Det finns ytterligare parametrar som du kan justera så att de passar dina loggnings behov: 

:::image type="content" source="./media/concepts-server-logs/log-parameters.png" alt-text="Parametrar för Azure Database for PostgreSQL-loggning":::

Om du vill veta mer om postgres logg parametrar går du [till inloggnings-och](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) inloggnings [avsnitt i](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) postgres-dokumentationen. De flesta, men inte alla, postgres loggnings parametrar är tillgängliga för konfiguration i Azure Database for PostgreSQL.

Information om hur du konfigurerar parametrar i Azure Database for PostgreSQL finns i [Portal dokumentationen](howto-configure-server-parameters-using-portal.md) eller CLI- [dokumentationen](howto-configure-server-parameters-using-cli.md). 

> [!NOTE]
> Att konfigurera en stor mängd loggar, till exempel loggning av uttryck, kan lägga till avsevärd prestanda. 

## <a name="access-log-files"></a>Åtkomst till. log-filer
Standard logg formatet i Azure Database for PostgreSQL är. log. En exempel rad från den här loggen ser ut så här:

```
2019-10-14 17:00:03 UTC-5d773cc3.3c-LOG: connection received: host=101.0.0.6 port=34331 pid=16216
```

Azure Database for PostgreSQL tillhandahåller en kortsiktig lagrings plats för. log-filerna. En ny fil börjar var 1 timme eller 100 MB, beroende på vilket som kommer först. Loggar läggs till den aktuella filen när de genereras från postgres.  

Du kan ställa in kvarhållningsperioden för den här kortsiktiga logg lagringen med hjälp av `log_retention_period` parametern. Standardvärdet är 3 dagar och maxvärdet är 7 dagar. Lagrings platsen på kort sikt kan innehålla upp till 1 GB loggfiler. Efter 1 GB tas de äldsta filerna, oavsett kvarhållningsperioden, bort för att göra plats för nya loggar. 

För längre kvarhållning av loggar och logg analys kan du hämta. log-filerna och flytta dem till en tjänst från tredje part. Du kan hämta filerna med hjälp av [Azure Portal](howto-configure-server-logs-in-portal.md), [Azure CLI](howto-configure-server-logs-using-cli.md). Du kan också konfigurera Azure Monitor diagnostikinställningar som automatiskt gör dina loggar (i JSON-format) till platser på längre sikt. Läs mer om det här alternativet i avsnittet nedan. 

Du kan sluta generera. log-filer genom att ange parametern `logging_collector` till av. Stänger av. generering av logg filen rekommenderas om du använder Azure Monitor diagnostikinställningar. Den här konfigurationen minskar prestanda påverkan för ytterligare loggning.

## <a name="resource-logs"></a>Resursloggar

Azure Database for PostgreSQL är integrerat med Azure Monitor diagnostikinställningar. Med diagnostikinställningar kan du skicka postgres-loggar i JSON-format till Azure Monitor loggar för analys och aviseringar, Event Hubs för strömning och Azure Storage för arkivering. 

> [!IMPORTANT]
> Den här Diagnostic-funktionen för Server loggar är bara tillgänglig i Generell användning och minnesoptimerade [pris nivåer](concepts-pricing-tiers.md).


### <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar

Du kan aktivera diagnostikinställningar för postgres-servern med hjälp av Azure Portal, CLI, REST API och PowerShell. Den logg kategori som ska väljas är **PostgreSQLLogs**. (Det finns andra loggar som du kan konfigurera om du använder [query Store](concepts-query-store.md).)

Så här aktiverar du resurs loggar med hjälp av Azure Portal:

   1. I portalen går du till *diagnostikinställningar* i navigerings menyn på postgres-servern.
   2. Välj *Lägg till diagnostisk inställning*.
   3. Ge den här inställningen ett namn. 
   4. Välj önskad slut punkt (lagrings konto, händelsehubben, Log Analytics). 
   5. Välj logg typen **PostgreSQLLogs**.
   7. Spara inställningen.

Om du vill aktivera resurs loggar med PowerShell, CLI eller REST API går du till artikeln [diagnostiska inställningar](../azure-monitor/essentials/diagnostic-settings.md) .

### <a name="access-resource-logs"></a>Åtkomst till resurs loggar

Hur du kommer åt loggarna beror på vilken slut punkt du väljer. Information om Azure Storage finns i artikeln [Logga lagrings konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Information om Event Hubs finns i artikeln [Stream Azure-loggar](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

För Azure Monitor loggar skickas loggar till den valda arbets ytan. Postgres-loggarna använder samlings läget **AzureDiagnostics** , så att de kan frågas från AzureDiagnostics-tabellen. Fälten i tabellen beskrivs nedan. Läs mer om frågor och aviseringar i Översikt över [Azure Monitor loggar frågor](../azure-monitor/logs/log-query-overview.md) .

Följande är frågor som du kan försöka komma igång med. Du kan konfigurera aviseringar baserat på frågor.

Sök efter alla postgres-loggar för en viss server under den senaste dagen
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where Category == "PostgreSQLLogs"
| where TimeGenerated > ago(1d) 
```

Sök efter alla anslutnings försök för icke-localhost
```
AzureDiagnostics
| where Message contains "connection received" and Message !contains "host=127.0.0.1"
| where Category == "PostgreSQLLogs" and TimeGenerated > ago(6h)
```
Frågan ovan visar resultaten under de senaste 6 timmarna för alla postgres Server-loggningar i den här arbets ytan.

### <a name="log-format"></a>Logg format

I följande tabell beskrivs fälten för **PostgreSQLLogs** -typen. Beroende på vilken slut punkt för utdata du väljer kan de fält som ingår och i vilken ordning de visas variera. 

|**Fält** | **Beskrivning** |
|---|---|
| TenantId | Ditt klient-ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | Tidstämpel när loggen registrerades i UTC |
| Typ | Loggens typ. Alltid `AzureDiagnostics` |
| SubscriptionId | GUID för den prenumeration som servern tillhör |
| ResourceGroup | Namnet på den resurs grupp som servern tillhör |
| ResourceProvider | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | Resurs-URI |
| Resurs | Namnet på servern |
| Kategori | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| Nivå | Loggnings nivå, exempel: logg, fel, meddelande |
| Meddelande | Primärt logg meddelande | 
| Domain | Server version, exempel: postgres-10 |
| Detalj | Sekundärt logg meddelande (om tillämpligt) |
| ColumnName | Namnet på kolumnen (om tillämpligt) |
| SchemaName | Schemats namn (om tillämpligt) |
| DatatypeName | Namnet på data typen (om tillämpligt) |
| LogicalServerName | Namnet på servern | 
| _ResourceId | Resurs-URI |
| Prefix | Logg radens prefix |


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om att komma åt loggar från [Azure Portal](howto-configure-server-logs-in-portal.md) eller [Azure CLI](howto-configure-server-logs-using-cli.md).
- Läs mer om [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).
- Läs mer om [gransknings loggar](concepts-audit.md)