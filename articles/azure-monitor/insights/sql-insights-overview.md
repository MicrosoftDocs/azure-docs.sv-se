---
title: Övervaka SQL-distributioner med SQL Insights (förhands granskning)
description: Översikt över SQL Insights i Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567839"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Övervaka SQL-distributioner med SQL Insights (förhands granskning)
SQL Insights är en omfattande lösning för att övervaka en produkt i [Azure SQL-serien](../../azure-sql/index.yml). SQL Insights använder vyer med [dynamisk hantering](../../azure-sql/database/monitoring-with-dmvs.md) för att visa de data du behöver för att övervaka hälsan, diagnostisera problem och justera prestanda.  

SQL insikter utför all övervakning via fjärr anslutning. Övervaknings agenter på dedikerade virtuella datorer ansluter till dina SQL-resurser och fjärrsamlar data. Insamlade data lagras i [Azure Monitor loggar](../logs/data-platform-logs.md), vilket möjliggör enkel agg regerings-, filtrerings-och trend analys. Du kan visa insamlade data från mallen SQL Insights- [arbetsbok](../visualize/workbooks-overview.md), eller så kan du gå direkt till data med hjälp av [logg frågor](../logs/get-started-queries.md).

## <a name="pricing"></a>Priser
Det finns ingen direkt kostnad för SQL insikter. Alla kostnader uppkommer av de virtuella datorer som samlar in data, Log Analytics arbets ytor som lagrar data och eventuella varnings regler som kon figurer ATS för data. 

**Virtuella datorer**

För virtuella datorer debiteras du baserat på prissättningen som publicerats på [prissättnings sidan för virtuella datorer](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/). Antalet virtuella datorer som krävs varierar beroende på antalet anslutnings strängar som du vill övervaka. Vi rekommenderar att du allokerar 1 virtuell dator med storlek Standard_B2s för varje 100-anslutningssträng. Mer information finns i [krav för virtuella Azure-datorer](sql-insights-enable.md#azure-virtual-machine-requirements) .

**Log Analytics-arbetsytor**

För Log Analytics arbets ytor debiteras du utifrån den prissättning som publicerats på sidan för [Azure Monitor prissättning](https://azure.microsoft.com/pricing/details/monitor/). Log Analytics arbets ytor som används av SQL Insights debiteras kostnader för data inmatning, data lagring och (valfritt) data export. De exakta avgifterna varierar beroende på mängden data som hämtas, behålls och exporteras. Mängden data kommer senare att variera beroende på databas aktivitet och de samlings inställningar som definierats i dina [övervaknings profiler](sql-insights-enable.md#create-sql-monitoring-profile).

**Aviseringsregler**

För varnings regler i Azure Monitor debiteras du baserat på prissättningen som publicerats på [sidan med priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Om du väljer att [skapa aviseringar med SQL Insights](sql-insights-alerts.md)debiteras du för eventuella varnings regler som skapats och eventuella meddelanden som skickas.

## <a name="supported-versions"></a>Versioner som stöds 
SQL Insights stöder följande versioner av SQL Server:
- SQL Server 2012 och senare

SQL Insights stöder SQL Server som körs i följande miljöer:
- Azure SQL Database
- Hanterad Azure SQL-instans
- SQL Server på Azure-Virtual Machines (SQL Server som körs på virtuella datorer som är registrerade med SQL-providern för [virtuella](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) datorer)
- Virtuella Azure-datorer (SQL Server som körs på virtuella datorer som inte har registrerats med SQL-providern för [virtuella](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) datorer)

SQL Insights har inget stöd eller begränsat stöd för följande:
- **Icke-Azure-instanser**: SQL Server som körs på virtuella datorer utanför Azure stöds inte
- **Azure SQL Database elastiska pooler**: det går inte att samla in mått för elastiska pooler. Mått kan inte samlas in för databaser i elastiska pooler.
- **Azure SQL Database låga tjänste nivåer**: måtten kan inte samlas in för databaser på [tjänst nivåerna](../../azure-sql/database/resource-limits-dtu-single-databases.md) Basic, S0, S1 och S2
- **Azure SQL Database Server lös nivå**: statistik kan samlas in för databaser med hjälp av Server lös beräknings nivån. Processen med att samla in mått återställer dock timern för automatisk paus, vilket hindrar databasen från att ange ett automatiskt pausat tillstånd
- **Sekundära repliker**: mått kan bara samlas in för en enda sekundär replik per databas. Om en databas har mer än 1 sekundär replik kan endast 1 övervakas.
- **Autentisering med Azure Active Directory**: den enda metoden för [autentisering](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) som stöds för övervakning är SQL-autentisering. För SQL Server på virtuella Azure-datorer stöds inte autentisering med Active Directory på en anpassad domänkontrollant.  

## <a name="open-sql-insights"></a>Öppna SQL Insights
Öppna SQL Insights genom att välja **SQL (för hands version)** i avsnittet **insikter** på **Azure Monitor** -menyn i Azure Portal. Klicka på en panel för att läsa in upplevelsen för den typ av SQL som du övervakar.

:::image type="content" source="media/sql-insights/portal.png" alt-text="SQL Insights i Azure Portal.":::

## <a name="enable-sql-insights"></a>Aktivera SQL Insights 
Se [Aktivera SQL Insights](sql-insights-enable.md) för instruktioner om hur du aktiverar SQL Insights.

## <a name="troubleshoot-sql-insights"></a>Felsöka SQL Insights 
Mer information om hur du felsöker SQL Insights finns i [FELSÖKA SQL Insights](sql-insights-troubleshoot.md) .

## <a name="data-collected-by-sql-insights"></a>Data som samlas in av SQL Insights
SQL insikter utför all övervakning via fjärr anslutning. Vi installerar inga agenter på de virtuella datorer som kör SQL Server. 

SQL Insights använder dedikerade virtuella datorer för att samla in data från dina SQL-resurser via en fjärr anslutning. För varje virtuell dator som övervakas får [Azure Monitor-agenten](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) och WLI-tillägget för arbets belastnings tjänsten installerat. WLI-tillägget innehåller för öppen [källkod.](https://www.influxdata.com/time-series-platform/telegraf/) SQL Insights använder [data insamlings regler](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) för att ange data insamlings inställningarna för teleympkvistar: s [SQL Server-plugin](https://www.influxdata.com/integration/microsoft-sql-server/).

Olika data uppsättningar är tillgängliga för Azure SQL Database, Azure SQL-hanterad instans och SQL Server. Tabellerna nedan beskriver tillgängliga data. Du kan anpassa vilka data uppsättningar som ska samlas in och hur ofta de ska samlas in när du [skapar en övervaknings profil](sql-insights-enable.md#create-sql-monitoring-profile).

Tabellerna nedan innehåller följande kolumner:
- **Eget namn**: namnet på frågan som visas på Azure Portal när du skapar en övervaknings profil
- **Konfigurations namn**: namnet på frågan som visas på Azure Portal när du redigerar en övervaknings profil
- **Namnrymd**: namnet på frågan som hittades i en Log Analytics-arbetsyta. Den här identifieraren visas i tabellen **InsighstMetrics** i `Namespace` egenskapen i `Tags` kolumnen
- **DMV: er**: de dynamiska hanterade vyerna som används för att skapa data uppsättningen
- **Aktive rad som standard**: om data samlas in som standard
- **Standard frekvens för samling**: hur ofta data samlas in som standard

### <a name="data-for-azure-sql-database"></a>Data för Azure SQL Database 
| Eget namn | Konfigurations namn | Namnområde | Dynamiska hanteringsvyer (DMV) | Aktive rad som standard | Standard frekvens för samling |
|:---|:---|:---|:---|:---|:---|
| Väntan på DB | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | Inga | NA |
| Status för DBO-väntan | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Ja | 60 sekunder |
| Minnesstyrningar | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Ja | 60 sekunder |
| I/O för databas | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Ja | 60 sekunder |
| Serveregenskaper | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>sys. samling<br>sys. [database_service_objectives] | Ja | 60 sekunder |
| Prestandaräknare | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Ja | 60 sekunder |
| Resurs statistik | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Ja | 60 sekunder |
| Resursstyrning | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Ja | 60 sekunder |
| Begäranden | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Inga | NA |
| Schemaläggare| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Inga | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Data för Azure SQL-hanterad instans 

| Eget namn | Konfigurations namn | Namnområde | Dynamiska hanteringsvyer (DMV) | Aktive rad som standard | Standard frekvens för samling |
|:---|:---|:---|:---|:---|:---|
| Väntestatistik | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Ja | 60 sekunder |
| Minnesstyrningar | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Ja | 60 sekunder |
| I/O för databas | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Ja | 60 sekunder |
| Serveregenskaper | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Ja | 60 sekunder |
| Prestandaräknare | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Ja | 60 sekunder |
| Resurs statistik | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Ja | 60 sekunder |
| Resursstyrning | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Ja | 60 sekunder |
| Begäranden | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Inga | NA |
| Schemaläggare | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Inga | NA |

### <a name="data-for-sql-server"></a>Data för SQL Server

| Eget namn | Konfigurations namn | Namnområde | Dynamiska hanteringsvyer (DMV) | Aktive rad som standard | Standard frekvens för samling |
|:---|:---|:---|:---|:---|:---|
| Väntestatistik | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Ja | 60 sekunder | 
| Minnesstyrningar | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Ja | 60 sekunder |
| I/O för databas | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Ja | 60 sekunder |
| Serveregenskaper | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Ja | 60 sekunder |
| Prestandaräknare | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Ja | 60 sekunder |
| Volym utrymme | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Ja | 60 sekunder |
| SQL Server CPU | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Ja | 60 sekunder |
| Schemaläggare | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | Inga | NA |
| Begäranden | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | Inga | NA |
| Tillgänglighets replik tillstånd | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | Inga | 60 sekunder |
| Tillgänglighets databas repliker | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | Inga | 60 sekunder |

## <a name="next-steps"></a>Nästa steg

- Se [Aktivera SQL Insights](sql-insights-enable.md) för instruktioner om hur du aktiverar SQL Insights
- Se [vanliga frågor](../faq.md#sql-insights-preview) och svar om du vill ha vanliga frågor om SQL Insights
