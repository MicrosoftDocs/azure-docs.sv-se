---
title: Övervaka SQL-distributioner med SQL Insights (förhands granskning)
description: Översikt över SQL Insights i Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: b5add466a60bc855e08917d02fecaf60a35deeb1
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889577"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Övervaka SQL-distributioner med SQL Insights (förhands granskning)
SQL Insights övervakar prestanda och hälsa för SQL-distributionerna.  Det kan hjälpa till att leverera förutsägbar prestanda och tillgänglighet för viktiga arbets belastningar som du har skapat runt en SQL-Server genom att identifiera Flask halsar och problem med prestanda. SQL Insights lagrar data i [Azure Monitor loggar](../logs/data-platform-logs.md), vilket gör det möjligt att leverera kraftfull agg regering och filtrering och analysera data trender över tid. Du kan visa dessa data från Azure Monitor i de vyer vi levererar som en del av det här erbjudandet och du kan gå direkt till loggdata för att köra frågor och analysera trender.

SQL Insights installerar inte något på dina SQL IaaS-distributioner. I stället används dedikerade virtuella datorer för att samla in data för både SQL-PaaS och SQL IaaS-distributioner.  Med SQL Insights Monitoring-profilen kan du hantera de data uppsättningar som ska samlas in baserat på typen av SQL, inklusive Azure SQL DB, Azure SQL-hanterad instans och SQL Server som körs på en virtuell Azure-dator.

## <a name="pricing"></a>Priser

Det finns ingen direkt kostnad för SQL Insights, men du debiteras för aktiviteten i Log Analytics-arbetsytan. SQL insikter debiteras enligt priserna som publiceras på [sidan Azure Monitor priser](https://azure.microsoft.com/pricing/details/monitor/):

- Data som matats in från agenter och lagras i arbets ytan.
- Aviserings regler baserade på loggdata.
- Meddelanden som skickas från varnings regler.

Logg storleken varierar beroende på sträng längden för de data som samlas in och kan öka med mängden databas aktivitet. 

## <a name="supported-versions"></a>Versioner som stöds 
SQL Insights stöder följande versioner av SQL Server:

- SQL Server 2012 och senare

SQL Insights stöder SQL Server som körs i följande miljöer:

- Azure SQL Database
- Hanterad Azure SQL-instans
- Virtuella Azure SQL-datorer
- Virtuella Azure-datorer

SQL Insights har inget stöd eller begränsat stöd för följande:

- SQL Server som körs på virtuella datorer utanför Azure stöds inte för närvarande.
- Azure SQL Database elastiska pooler: begränsat stöd under den offentliga för hands versionen. Kommer att stödjas fullt ut för allmän tillgänglighet.  
- Azure SQL Server-distributioner: som aktiv geo-DR, förhindrar de aktuella övervaknings agenterna distribution utan server från att gå till ström spar läge. Detta kan orsaka högre än förväntade kostnader från distributioner utan server.  
- Läsbara sekundär servrar: för närvarande stöds endast distributions typer med en skrivskyddad sekundär slut punkt (Affärskritisk eller skalning). När storskaliga distributioner stöder namngivna repliker, kommer vi att kunna stödja flera läsbara sekundära slut punkter för en enda logisk databas.  
- Azure Active-kataloger: för närvarande stöder vi bara SQL-inloggningar för övervaknings agenten. Vi planerar att stödja Azure Active-kataloger i en kommande version och har inget aktuellt stöd för SQL VM-autentisering med hjälp av aktiva kataloger på en eker-domänkontrollant.  


## <a name="open-sql-insights"></a>Öppna SQL Insights
Öppna SQL Insights genom att välja **SQL (för hands version)** i avsnittet **insikter** på **Azure Monitor** -menyn i Azure Portal. Klicka på en panel för att läsa in upplevelsen för den typ av SQL som du övervakar.

:::image type="content" source="media/sql-insights/portal.png" alt-text="SQL Insights i Azure Portal.":::


## <a name="enable-sql-insights"></a>Aktivera SQL Insights 
Se [Aktivera SQL Insights](sql-insights-enable.md) för den detaljerade proceduren för att aktivera SQL insikter utöver steg för fel sökning.


## <a name="data-collected-by-sql-insights"></a>Data som samlas in av SQL Insights

SQL Insights stöder endast fjärrmetod för övervakning av SQL. Vi installerar inga agenter på de virtuella datorer som kör SQL Server. En eller flera dedikerade övervaknings-VM: er krävs som vi använder för att fjärrsamla in data från dina SQL-resurser. 

Var och en av de virtuella datorerna som övervakar de virtuella datorerna kommer att ha [Azure Monitor-agenten](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) installerad tillsammans med WLI-tillägget. 

WLI-tillägget innehåller för öppen [källkod.](https://www.influxdata.com/time-series-platform/telegraf/)  Vi använder [data insamlings regler](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) för att konfigurera [SQLServer-plugin-programmet](https://www.influxdata.com/integration/microsoft-sql-server/) för att ange vilka data som ska samlas in från Azure SQL DB, Azure SQL-hanterad instans och SQL Server körs på en virtuell Azure-dator. 

Följande tabeller sammanfattar följande:

- Namnet på frågan i SQLServer teleympkvistar-plugin-programmet
- Dynamiska hanterade vyer frågans anrop
- Namn område som data visas under i *InsighstMetrics* -tabellen
- Om data samlas in som standard
- Hur ofta data samlas in som standard
 
Du kan ändra vilka frågor som ska köras och data insamlings frekvens när du skapar din övervaknings profil. 

### <a name="azure-sql-db-data"></a>Azure SQL DB-data 

| Frågenamn | DMV | Namnområde | Aktive rad som standard | Standard frekvens för samling |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | Inga | NA |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Ja | 60 sekunder |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Ja | 60 sekunder |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | Ja | 60 sekunder |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>sys. samling<br>sys. [database_service_objectives] | sqlserver_server_properties | Ja | 60 sekunder |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Ja | 60 sekunder |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Ja | 60 sekunder |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Ja | 60 sekunder |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Inga | NA |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Inga | NA  |

### <a name="azure-sql-managed-instance-data"></a>Azure SQL-hanterade instans data 

| Frågenamn | DMV | Namnområde | Aktive rad som standard | Standard frekvens för samling |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Ja | 60 sekunder |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Ja | 60 sekunder |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Ja | 60 sekunder |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Ja | 60 sekunder |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Ja | 60 sekunder |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Ja | 60 sekunder |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Ja | 60 sekunder |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Inga | NA |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Inga | NA |

### <a name="sql-server-data"></a>SQL Server data

| Frågenamn | DMV | Namnområde | Aktive rad som standard | Standard frekvens för samling |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Ja | 60 sekunder |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Ja | 60 sekunder | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Ja | 60 sekunder |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | Ja | 60 sekunder |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Ja | 60 sekunder |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | Inga | NA |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | Inga | NA |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Ja | 60 sekunder |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Ja | 60 sekunder |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 sekunder |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 sekunder |




## <a name="next-steps"></a>Nästa steg

Se [Aktivera SQL Insights](sql-insights-enable.md) för den detaljerade proceduren för att aktivera SQL Insights.
Se [vanliga frågor](../faq.md#sql-insights-preview) och svar om du vill ha vanliga frågor om SQL Insights.
