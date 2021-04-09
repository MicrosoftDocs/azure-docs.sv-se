---
title: Övervakning och prestandajustering
description: En översikt över funktionerna för övervakning och prestanda justering och metoder i Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 03/17/2021
ms.openlocfilehash: 4c9edaea75215bc4b471cd2e0ac50c4e69546bb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592142"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Övervakning och prestandajustering för Azure SQL Database och Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Om du vill övervaka prestanda för en databas i Azure SQL Database och Azure SQL-hanterad instans, börjar du med att övervaka de processor-och IO-resurser som används av arbets belastningen i förhållande till den nivå av databas prestanda som du valde när du väljer en viss tjänst nivå och prestanda nivå. För att åstadkomma detta genererar Azure SQL Database och Azure SQL-hanterad instans resurs mått som kan visas i Azure Portal eller genom att använda något av dessa SQL Server hanterings verktyg: [Azure Data Studio](/sql/azure-data-studio/what-is) eller [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database innehåller ett antal databas rådgivare för att tillhandahålla smarta rekommendationer för prestanda justering och automatiska justerings alternativ för att förbättra prestandan. Dessutom visar Query Performance Insight information om de frågor som ansvarar för den mest CPU-och i/o-användningen för enskilda databaser och databaser i pooler.

Azure SQL Database och Azure SQL-hanterad instans tillhandahåller avancerade övervaknings-och justerings funktioner som stöds av artificiell intelligens för att hjälpa dig att felsöka och maximera prestandan för dina databaser och lösningar. Du kan välja att konfigurera [strömnings exporten](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) av dessa [intelligent Insights](intelligent-insights-overview.md) och andra databas resurs loggar och mät värden till en av flera destinationer för förbrukning och analys, särskilt med hjälp av [SQL Analytics](../../azure-monitor/insights/azure-sql.md). Azure SQL-analys är en avancerad lösning för moln övervakning för att övervaka prestanda för alla dina databaser i stor skala och över flera prenumerationer i en enda vy. För en lista över loggar och mått som du kan exportera, se [diagnostisk telemetri för export](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

SQL Server har egna funktioner för övervakning och diagnostik som SQL Database och SQL-hanterad instans använder, till exempel [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) och [vyer för dynamisk hantering (DMV: er)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views). Se [övervakning med DMV: er](monitoring-with-dmvs.md) för skript för att övervaka ett antal prestanda problem.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Övervaknings-och justerings funktioner i Azure Portal

Azure SQL Database och Azure SQL-hanterad instans tillhandahåller övervakning av resurs mått i Azure Portal. Azure SQL Database tillhandahåller databas rådgivare och Query Performance Insight ger rekommendationer för att ställa frågor och fråga prestanda analys. I Azure Portal kan du aktivera automatisk justering för [logiska SQL-servrar](logical-servers.md) och deras enskilda databaser och databaser i pooler.

> [!NOTE]
> Databaser med mycket låg användning kan visas i portalen med mindre än faktisk användning. På grund av hur telemetri genereras när ett Double-värde konverteras till närmaste heltal, avrundas vissa användnings belopp mindre än 0,5 till 0, vilket leder till att den utgivna telemetri förloras. Mer information finns i [mått för låg databas och elastisk pool till noll](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="monitor-with-sql-insights"></a>Övervaka med SQL Insights

[Azure Monitor SQL Insights](../../azure-monitor/insights/sql-insights-overview.md) är ett verktyg för att övervaka Azure SQL-hanterade instanser, Azure SQL-databaser och SQL Server instanser i virtuella Azure SQL-datorer. Den här tjänsten använder en fjärragent för att samla in data från DMV: er (Dynamic Management views) och dirigera data till Azure Log Analytics, där de kan övervakas och analyseras. Du kan visa dessa data från [Azure Monitor](../../azure-monitor/overview.md) i angivna vyer eller komma åt loggdata direkt för att köra frågor och analysera trender. Om du vill börja använda Azure Monitor SQL Insights, se [Aktivera SQL Insights](../../azure-monitor/insights/sql-insights-enable.md).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Resurs övervakning av Azure SQL Database och Azure SQL-hanterad instans

Du kan snabbt övervaka olika resurs mått i Azure Portal i vyn **mått** . Med dessa mått kan du se om en databas når 100% av processor-, minnes-eller IO-resurser. Hög DTU-eller processor procent, samt hög IO-procent, anger att arbets belastningen kan kräva fler CPU-eller i/o-resurser. Det kan också indikera frågor som måste optimeras.

  ![Resurs mått](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Databas rådgivare i Azure SQL Database

Azure SQL Database innehåller [databas rådgivare](database-advisor-implement-performance-recommendations.md) som ger rekommendationer för prestanda justering för enskilda databaser och databaser i pooler. Dessa rekommendationer är tillgängliga i Azure Portal och med [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor). Du kan också aktivera [Automatisk justering](automatic-tuning-overview.md) så att Azure SQL Database automatiskt kan implementera dessa justerings rekommendationer.

### <a name="query-performance-insight-in-azure-sql-database"></a>Query Performance Insight i Azure SQL Database

[Query Performance Insight](query-performance-insight-use.md) visar prestanda i Azure Portal för de vanligaste och mest använda frågorna för enskilda databaser och databaser i pooler.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Mått för låg databas och elastisk pool avrundas till noll

Från och med september 2020 kan databaser med mycket låg användning visas i portalen med mindre än faktisk användning. På grund av hur telemetri genereras när ett Double-värde konverteras till närmaste heltal, avrundas vissa användnings belopp mindre än 0,5 till 0, vilket leder till att den utgivna telemetri förloras.

Exempel: Överväg ett fönster med en minut med följande fyra data punkter: 0,1, 0,1, 0,1, 0,1, dessa låga värden avrundas nedåt till 0, 0, 0 och visar genomsnittet på 0. Om någon av data punkterna är större än 0,5, till exempel: 0,1, 0,1, 0,9, 0,1, rundas de av till 0, 0, 0 och visar ett medeltal av 0,25.

Berörda databas mått:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Berörda mått för elastisk pool:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Generera intelligenta utvärderingar av prestanda problem

[Intelligent Insights](intelligent-insights-overview.md) för Azure SQL Database och Azure SQL-hanterad instans använder inbyggd intelligens för att kontinuerligt övervaka databas användningen via artificiell intelligens och identifiera störande händelser som orsakar dåliga prestanda. Intelligent Insights identifierar automatiskt prestanda problem med databaser baserat på vänte tider, fel eller tids gränser för frågekörning. När den har identifierats utförs en detaljerad analys som genererar en resurs logg (kallas SQLInsights) med en [intelligent utvärdering av problemen](intelligent-insights-troubleshoot-performance.md). Den här utvärderingen består av en rotor Saks analys av databasens prestanda problem och, om möjligt, rekommendationer för prestanda förbättringar.

Intelligent Insights är en unik funktion i Azure inbyggda intelligens som ger följande värde:

- Proaktiv övervakning
- Skräddarsydda prestanda insikter
- Tidig identifiering av databas prestanda försämring
- Rotor Saks analys av problem som upptäckts
- Rekommendationer för prestanda förbättring
- Skala ut kapacitet på hundratals tusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägande kostnaden

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Aktivera strömnings export av mått och resurs loggar

Du kan aktivera och konfigurera den [strömmande exporten av Diagnostic-telemetri](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) till en av flera destinationer, inklusive intelligent Insights resurs loggen. Använd [SQL Analytics](../../azure-monitor/insights/azure-sql.md) och andra funktioner för att använda denna ytterligare diagnostiska telemetri för att identifiera och lösa prestanda problem.

Du konfigurerar diagnostikinställningar till att strömma kategorier av mått och resurs loggar för enskilda databaser, databaser i pooler, elastiska pooler, hanterade instanser och instans databaser till någon av följande Azure-resurser.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics arbets yta i Azure Monitor

Du kan strömma mått och resurs loggar till en [Log Analytics arbets yta i Azure Monitor](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Data som strömmas här kan användas av [SQL Analytics](../../azure-monitor/insights/azure-sql.md), som är en övervaknings lösning för endast molnet som ger intelligent övervakning av dina databaser som innehåller prestanda rapporter, aviseringar och rekommendationer. Data som strömmas till en Log Analytics arbets yta kan analyseras med andra övervaknings data som samlas in och du kan också använda andra Azure Monitor funktioner, till exempel aviseringar och visualiseringar.

### <a name="azure-event-hubs"></a>Azure Event Hubs

Du kan strömma mått och resurs loggar till [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs). Att strömma Diagnostic-telemetri till Event Hubs för att tillhandahålla följande funktioner:

- **Strömma loggar till tredjepartssystem för loggning och telemetri**

  Strömma alla dina mått och resurs loggar till en enda händelse hubb för att skicka loggdata till en SIEM-eller Log Analytics-verktyg från tredje part.
- **Skapa en anpassad plattform för telemetri och loggning**

  Med händelse hubbarna med hög skalbarhet för publicerings prenumerationer kan du samla in mått och resurs loggar på ett flexibelt sätt i en anpassad telemetri-plattform. Mer information finns i [utforma och ändra storlek på en plattform för global skalnings telemetri på Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Visa tjänstens hälsa genom att strömma data till Power BI**

  Använd Event Hubs, Stream Analytics och Power BI för att omvandla dina diagnostikdata till nära real tids insikter om dina Azure-tjänster. Mer information om den här lösningen finns i [Stream Analytics och Power BI: en analys instrument panel i real tid för att strömma data](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .

### <a name="azure-storage"></a>Azure Storage

Strömma mått och resurs loggar till [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Använd Azure Storage för att arkivera stora mängder diagnostiska telemetri för en bråkdel av kostnaden för de föregående två strömnings alternativen.

## <a name="use-extended-events"></a>Använda utökade händelser 

Dessutom kan du använda [utökade händelser](/sql/relational-databases/extended-events/extended-events) i SQL Server för avancerad övervakning och fel sökning. Arkitekturen för utökade händelser gör det möjligt för användare att samla in så mycket eller så lite data som behövs för att felsöka eller identifiera ett prestanda problem. Information om hur du använder utökade händelser i Azure SQL Database finns i [utökade händelser i Azure SQL Database](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om intelligenta prestanda rekommendationer för enskilda databaser och grupperade databaser finns i [prestanda rekommendationer för Database Advisor](database-advisor-implement-performance-recommendations.md).
- Mer information om automatisk övervakning av databas prestanda med automatiserad diagnostik och rotor Saks analys av prestanda problem finns i [Azure SQL intelligent Insights](intelligent-insights-overview.md).