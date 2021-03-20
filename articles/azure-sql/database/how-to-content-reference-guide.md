---
title: Konfigurera & hantera innehålls referens
description: Hitta en referens till innehåll som lär dig att konfigurera och hantera Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 01/14/2020
ms.openlocfilehash: 19cec1deeef344ea5897061db0fec88066b90b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791774"
---
# <a name="configure-and-manage-content-reference---azure-sql-database"></a>Konfigurera och hantera innehålls referens – Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln hittar du en innehålls referens för olika guider, skript och förklaringar som kan hjälpa dig att hantera och konfigurera din Azure SQL Database. 

## <a name="load-data"></a>Läsa in data

- [Migrera till SQL Database](migrate-to-database-from-sql-server.md)
- Lär dig hur du [hanterar SQL Database efter migreringen](manage-data-after-migrating-to-database.md).
- [Kopiera en databas](database-copy.md)
- [Importera en databas från en BACPAC-fil](database-import.md)
- [Exportera en databas till en BACPAC-fil](database-export.md)
- [Läs in data med BCP](../load-from-csv-with-bcp.md)
- [Läs in data med ADF](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="configure-features"></a>Konfigurera funktioner

- [Konfigurera Azure Active Directory-autentisering (Azure AD)](authentication-aad-configure.md)
- [Konfigurera villkorlig åtkomst](conditional-access-configure.md)
- [Flerfaktorautentisering för Azure AD](authentication-mfa-ssms-overview.md)
- [Konfigurera Multi-Factor Authentication](authentication-mfa-ssms-configure.md)
- [Konfigurera temporal bevarandeprincip](temporal-tables-retention-policy.md)
- [Konfigurera TDE med BYOK](transparent-data-encryption-byok-configure.md)
- [Rotera TDE BYOK-nycklar](transparent-data-encryption-byok-key-rotation.md)
- [Ta bort TDE-skydd](transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurera minnesintern OLTP](../in-memory-oltp-configure.md)
- [Konfigurera Azure Automation](automation-manage.md)
- [Konfigurera](replication-to-sql-database.md) Transaktionsreplikering för att replikera ditt datum mellan databaser.
- [Konfigurera hot identifiering](threat-detection-configure.md) för att låta Azure SQL Database identifiera misstänkta aktiviteter som SQL-inmatning eller åtkomst från misstänkta platser.
- [Konfigurera dynamisk data maskning](dynamic-data-masking-configure-portal.md) för att skydda känsliga data.
- [Konfigurera kvarhållning av säkerhets kopior](long-term-backup-retention-configure.md) för en databas om du vill behålla dina säkerhets kopior på Azure Blob Storage. 
- [Konfigurera geo-replikering](active-geo-replication-overview.md) för att behålla en replik av databasen i en annan region.
- [Konfigurera säkerhet för geo-Replicas](active-geo-replication-security-configure.md).

## <a name="monitor-and-tune-your-database"></a>Övervaka och finjustera databasen

- [Manuell inställning](performance-guidance.md)
- [Använda DMV:er för att övervaka prestanda](monitoring-with-dmvs.md)
- [Använda Query Store för att övervaka prestanda](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Aktivera automatisk justering](automatic-tuning-enable.md) för att låta Azure SQL Database optimera arbets Belastningens prestanda.
- [Aktivera e-postaviseringar för automatisk justering](automatic-tuning-email-notifications-configure.md) för att få information om justerings rekommendationer.
- [Använd prestanda rekommendationer](database-advisor-find-recommendations-portal.md) och optimera databasen.
- [Skapa aviseringar](alerts-insights-configure-portal.md) för att få meddelanden från Azure SQL Database.
- [Felsök anslutningen](troubleshoot-common-errors-issues.md) om du märker några anslutnings problem mellan programmen och-databasen. Du kan också använda [Resource Health för anslutnings problem](resource-health-to-troubleshoot-connectivity.md).
- [Felsöka prestanda med Intelligent Insights](intelligent-insights-troubleshoot-performance.md)
- [Hantera fil utrymme](file-space-manage.md) för att övervaka lagrings användningen i databasen.
- [Använda Intelligent Insights-diagnostikloggar](intelligent-insights-use-diagnostics-log.md)
- [Övervaka minnesintern OLTP-plats](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Utökade händelser

- [Utökade händelser](xevent-db-diff-from-svr.md)
- [Lagra utökade händelser i händelse filen](xevent-code-event-file.md)
- [Lagra utökade händelser i ringbufferten](xevent-code-ring-buffer.md)

## <a name="query-distributed-data"></a>Fråga distribuerade data

- [Fråga lodrätt partitionerade data](elastic-query-getting-started-vertical.md) över flera databaser.
- [Rapport över skalade data nivåer](elastic-query-horizontal-partitioning.md).
- [Fråga över tabeller med olika scheman](elastic-query-vertical-partitioning.md).

### <a name="data-sync"></a>Datasynkronisering

- [SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
- [Datasynkroniseringsagent](sql-data-sync-agent-overview.md)
- [Replikera schemaändringar](sql-data-sync-update-sync-schema.md)
- [Övervaka med OMS](./monitor-tune-overview.md)
- [Metodtips för Data Sync](sql-data-sync-best-practices.md)
- [Felsöka Data Sync](sql-data-sync-troubleshoot.md)

## <a name="elastic-database-jobs"></a>Elastic Database-jobb

- [Skapa och hantera](elastic-jobs-powershell-create.md) Elastic Database jobb med PowerShell.
- [Skapa och hantera](elastic-jobs-tsql-create-manage.md) Elastic Database jobb med hjälp av Transact-SQL.
- [Migrera från gammalt elastiskt jobb](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Horisontell partitionering av databas

- [Uppgradera klient biblioteket för Elastic Database](elastic-scale-upgrade-client-library.md).
- [Skapa shardade-app](elastic-scale-get-started.md).
- [Fråga vågrätt shardade data](elastic-query-getting-started.md).
- Köra [multi-Shard-frågor](elastic-scale-multishard-querying.md).
- [Flytta shardade-data](elastic-scale-configure-deploy-split-and-merge.md).
- [Konfigurera säkerhet](elastic-scale-split-merge-security-configuration.md) i Database Shards.
- [Lägg till en Shard](elastic-scale-add-a-shard.md) i den aktuella uppsättningen databas Shards.
- [Åtgärda problem med Shard Map](elastic-database-recovery-manager.md).
- [Migrera SHARDADE DB](elastic-convert-to-use-elastic-tools.md).
- [Skapa räknare](elastic-database-perf-counters.md).
- [Använd Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) för att fråga shardade-data.
- [Använd dapper Framework](elastic-scale-working-with-dapper.md) för att fråga shardade-data.

## <a name="develop-applications"></a>Utveckla program

- [Anslutningsmöjligheter](connect-query-content-reference-guide.md#libraries)
- [Använda Spark-anslutningsappen](spark-connector.md)
- [Autentisera app](application-authentication-get-client-id-keys.md)
- [Använd batching för bättre prestanda](../performance-improve-use-batching.md)
- [Vägledning för anslutningar](troubleshoot-common-connectivity-issues.md)
- [DNS-alias](dns-alias-overview.md)
- [Konfigurera DNS-alias PowerShell](dns-alias-powershell-create.md)
- [Portar – ADO.NET](adonet-v12-develop-direct-route-ports.md)
- [C och C ++](develop-cplusplus-simple.md)
- [Excel](connect-excel.md)

## <a name="design-applications"></a>Utforma program

- [Utforma för haveriberedskap](designing-cloud-solutions-for-disaster-recovery.md)
- [Utforma för elastiska pooler](disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Utforma för appuppgraderingar](manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-software-as-a-service-saas-applications"></a>Designa SaaS-program (program vara som en tjänst) för flera innehavare

- [SaaS-designmönster](saas-tenancy-app-design-patterns.md)
- [SaaS-videoindexerare](saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Säkerhet för SaaS-app](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [instruktions guider för Azure SQL-hanterad instans](../managed-instance/how-to-content-reference-guide.md)