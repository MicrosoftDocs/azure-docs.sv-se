---
title: Loggar – Azure Database for PostgreSQL-flexibel Server
description: Beskriver loggning av konfiguration, lagring och analys i Azure Database for PostgreSQL-flexibel Server
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e09c01fcfb9c4725ac169151e85c8b030d8bb18c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606393"
---
# <a name="logs-in-azure-database-for-postgresql---flexible-server"></a>Loggar i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Med Azure Database for PostgreSQL kan du konfigurera och komma åt postgres standard loggar. Loggarna kan användas för att identifiera, felsöka och reparera konfigurations fel och underoptimala prestanda. Loggnings information som du kan konfigurera och komma åt innehåller fel, fråga efter information, autovakuum-poster, anslutningar och kontroll punkter. (Åtkomst till transaktions loggar är inte tillgänglig).

Gransknings loggning görs tillgängligt via ett postgres-tillägg `pgaudit` . Mer information finns i artikeln [gransknings begrepp](concepts-audit.md) .

## <a name="configure-logging"></a>Konfigurera loggning

Du kan konfigurera postgres standard inloggning på servern med hjälp av loggnings server parametrarna. Om du vill veta mer om postgres logg parametrar går du [till inloggnings-och](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHEN) inloggnings [avsnitt i](https://www.postgresql.org/docs/current/runtime-config-logging.html#RUNTIME-CONFIG-LOGGING-WHAT) postgres-dokumentationen. De flesta, men inte alla, postgres loggnings parametrar är tillgängliga för konfiguration i Azure Database for PostgreSQL.

Information om hur du konfigurerar parametrar i Azure Database for PostgreSQL finns i [Portal dokumentationen](howto-configure-server-parameters-using-portal.md) eller CLI- [dokumentationen](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> Att konfigurera en stor mängd loggar, till exempel loggning av uttryck, kan lägga till avsevärd prestanda. 

## <a name="accessing-logs"></a>Komma åt loggar

Azure Database for PostgreSQL är integrerat med Azure Monitor diagnostikinställningar. Med diagnostikinställningar kan du skicka postgres-loggar i JSON-format till Azure Monitor loggar för analys och aviseringar, Event Hubs för strömning och Azure Storage för arkivering. 

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

- Läs mer om hur du [konfigurerar och använder loggar](howto-configure-and-access-logs.md).
- Läs mer om [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/).
- Läs mer om [gransknings loggar](concepts-audit.md)
