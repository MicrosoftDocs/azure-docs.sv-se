---
title: Övervaka och finjustera – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver övervaknings-och justerings funktionerna i Azure Database for PostgreSQL-enskild server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 7ccd01f8c351ac2a7326a3b893bd78d14599db50
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588308"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Övervaka och finjustera Azure Database for PostgreSQL-enskild server
Genom att övervaka data om dina servrar kan du felsöka och optimera för din arbets belastning. Azure Database for PostgreSQL innehåller olika övervaknings alternativ för att ge inblick i serverns beteende.

## <a name="metrics"></a>Mått
Azure Database for PostgreSQL tillhandahåller olika mått som ger inblick i funktionerna i resurserna som stöder PostgreSQL-servern. Varje mått genereras med en minuters frekvens och har upp till [93 dagars historik](../azure-monitor/essentials/data-platform-metrics.md#retention-of-metrics). Du kan konfigurera aviseringar för måtten. Steg för steg-anvisningar finns i [så här konfigurerar du aviseringar](howto-alert-on-metric.md). Andra uppgifter är att ställa in automatiserade åtgärder, utföra avancerad analys och lagrings historik. Mer information finns i [Översikt över Azure Metrics](../azure-monitor/data-platform.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database for PostgreSQL:

|Metric|Mått visnings namn|Enhet|Description|
|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procent andelen CPU som används.|
|memory_percent|Minnes procent|Procent|Procent andelen minne som används.|
|io_consumption_percent|I/o procent|Procent|Procent andelen av IO som används. (Gäller inte för Basic-nivå servrar.)|
|storage_percent|Lagrings procent|Procent|Procent andelen lagring som används av serverns högsta värde.|
|storage_used|Använt lagrings utrymme|Byte|Mängden lagring som används. Lagrings utrymmet som används av tjänsten kan omfatta databasfilerna, transaktions loggarna och Server loggarna.|
|storage_limit|Lagrings gräns|Byte|Det maximala lagrings utrymmet för den här servern.|
|serverlog_storage_percent|Server logg lagrings procent|Procent|Procent andelen Server logg lagring som används av serverns maximala Server logg lagring.|
|serverlog_storage_usage|Server logg lagring används|Byte|Mängden Server logg lagring som används.|
|serverlog_storage_limit|Server logg lagrings gräns|Byte|Den maximala Server logg lagringen för den här servern.|
|active_connections|Aktiva anslutningar|Antal|Antalet aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Antal|Antalet etablerade anslutningar som misslyckades.|
|network_bytes_egress|Nätverk – utgående|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk – inkommande|Byte|Nätverk i över aktiva anslutningar.|
|backup_storage_used|Lagring av säkerhets kopior som används|Byte|Mängden lagring av säkerhets kopior som används. Det här måttet representerar summan av lagrings utrymme som förbrukas av alla fullständiga säkerhets kopior av databasen, differentiella säkerhets kopior och logg säkerhets kopior som bevaras baserat på den kvarhållna säkerhets kopie perioden som angetts för servern. Säkerhets kopierings frekvensen är service som hanteras och beskrivs i [artikeln begrepp](concepts-backup.md). För Geo-redundant lagring är lagrings utrymmet för säkerhets kopiering två gånger för det lokalt redundanta lagrings utrymmet.|
|pg_replica_log_delay_in_bytes|Maximal fördröjning mellan repliker|Byte|Fördröjningen i byte mellan den primära och den mest isoleringade repliken. Detta mått är bara tillgängligt på den primära servern.|
|pg_replica_log_delay_in_seconds|Replik fördröjning|Sekunder|Tiden sedan den senaste återspelade transaktionen. Det här måttet är endast tillgängligt för replik servrar.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera loggning på servern. Dessa resurs loggar kan skickas till [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md), Event Hubs och ett lagrings konto. Läs mer om loggning på sidan [Server loggar](concepts-server-logs.md) .

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) håller reda på frågornas prestanda över tid, inklusive körnings statistik för frågor och vänta-händelser. Funktionen fortsätter att köra prestanda information för kör tid i en system databas med namnet **azure_sys** under schemat för query_store. Du kan styra insamling och lagring av data via olika konfigurations rattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) arbetar tillsammans med Query Store för att tillhandahålla visualiseringar som är tillgängliga från Azure Portal. Med de här diagrammen kan du identifiera viktiga frågor som påverkar prestanda. Query Performance Insight kan nås från avsnittet **support och fel sökning** på din Azure Database for PostgreSQL servers Portal sida.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [prestanda rekommendationer](concepts-performance-recommendations.md) identifierar möjligheter att förbättra arbets Belastningens prestanda. Prestanda rekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestandan för dina arbets belastningar. För att skapa index rekommendationer tar funktionen hänsyn till olika databas egenskaper, inklusive schema och arbets belastningen som rapporteras av Frågearkivet. När du har implementerat en prestanda rekommendation bör kunderna testa prestanda för att utvärdera effekten av dessa ändringar. 

## <a name="planned-maintenance-notification"></a>Meddelande om planerat underhåll

Med [planerade underhålls aviseringar](./concepts-planned-maintenance-notification.md) kan du få aviseringar om kommande planerat underhåll till din Azure Database for PostgreSQL-enskild server. Dessa meddelanden är integrerade med [service Health](../service-health/overview.md) planerat underhåll och gör att du kan visa alla schemalagda underhåll för dina prenumerationer på ett och samma ställe. Den hjälper också till att skala meddelandet till rätt mål grupper för olika resurs grupper, eftersom du kan ha olika kontakter som är ansvariga för olika resurser. Du får ett meddelande om kommande underhåll 72 timmar före händelsen.

Lär dig mer om hur du konfigurerar aviseringar i dokumentet om [planerade underhålls meddelanden](./concepts-planned-maintenance-notification.md) .

## <a name="next-steps"></a>Nästa steg
- Se [hur du ställer in aviseringar](howto-alert-on-metric.md) för vägledning om hur du skapar en avisering på ett mått.
- Mer information om hur du får åtkomst till och exporterar mått med hjälp av Azure Portal, REST API eller CLI finns i [Översikt över Azure mått](../azure-monitor/data-platform.md)
- Läs vår blogg om [bästa praxis för att övervaka servern](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
- Lär dig mer om [planerade underhålls meddelanden](./concepts-planned-maintenance-notification.md) i Azure Database for PostgreSQL-enskild server.