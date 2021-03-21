---
title: Konfigurera & hantera innehålls referens
titleSuffix: Azure SQL Managed Instance
description: En referens guide för innehåll som lär dig hur du konfigurerar och hanterar Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 04/16/2019
ms.openlocfilehash: b7f2f060f32cf3bf92660a2e8b75a45b7ec76b82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779772"
---
# <a name="azure-sql-managed-instance-content-reference"></a>Innehålls referens för Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

I den här artikeln hittar du en innehålls referens till olika guider, skript och förklaringar som hjälper dig att hantera och konfigurera en hanterad Azure SQL-instans.

## <a name="load-data"></a>Läsa in data

- [Migrera till Azure SQL-hanterad instans](migrate-to-instance-from-sql-server.md): Lär dig mer om den rekommenderade migreringsprocessen och verktyg för migrering till Azure SQL-hanterad instans.
- [MIGRERA TDE-certifikat till Azure SQL Managed instance](tde-certificate-migrate.md): om SQL Server databasen skyddas med transparent data kryptering (TDE) måste du migrera certifikatet som SQL-hanterad instans kan använda för att dekryptera den säkerhets kopia som du vill återställa i Azure.
- [Importera en databas från en BACPAC-fil](../database/database-import.md)
- [Exportera en databas till en BACPAC-fil](../database/database-export.md)
- [Läs in data med BCP](../load-from-csv-with-bcp.md)
- [Läs in data med Azure Data Factory](../../data-factory/connector-azure-sql-database.md?toc=/azure/sql-database/toc.json)

## <a name="network-configuration"></a>Konfiguration av nätverk

- [Bestäm under näts storlek](vnet-subnet-determine-size.md): eftersom det inte går att ändra storlek på under nätet när SQL-hanterad instans har distribuerats måste du beräkna vilka IP-adressintervall som krävs för antalet och typerna av hanterade instanser som du planerar att distribuera till under nätet. 
- [Skapa ett nytt VNet och undernät](virtual-network-subnet-create-arm-template.md): konfigurera det virtuella nätverket och under nätet enligt [nätverks kraven](connectivity-architecture-overview.md#network-requirements). 
- [Konfigurera ett befintligt VNet och undernät](vnet-existing-add-subnet.md): kontrol lera nätverks kraven och konfigurera ditt befintliga virtuella nätverk och undernät för att distribuera SQL-hanterad instans. 
- [Konfigurera anpassad DNS](custom-dns-configure.md): Konfigurera anpassad DNS för att bevilja extern resurs åtkomst till anpassade domäner från SQL-hanterad instans via en länkad server med DB mail-profiler. 
- [Synkronisera nätverks konfiguration](azure-app-sync-network-configuration.md): uppdatera nätverks konfigurations planen om du inte kan upprätta en anslutning när du [har integrerat din app med ett virtuellt Azure-nätverk](../../app-service/web-sites-integrate-with-vnet.md).
- [Hitta IP-adressen för hanterings slut punkten](management-endpoint-find-ip-address.md): Bestäm den offentliga slut punkten som SQL-hanterad instans använder i hanterings syfte. 
- [Verifiera det inbyggda brand Väggs skyddet](management-endpoint-verify-built-in-firewall.md): kontrol lera att SQL-hanterad instans endast tillåter trafik på nödvändiga portar och andra inbyggda brand Väggs regler. 
- [Anslut program](connect-application-instance.md): Lär dig mer om olika mönster för att ansluta program till SQL-hanterad instans.

## <a name="feature-configuration"></a>Funktions konfiguration

- [Konfigurera Azure AD-autentisering](../database/authentication-aad-configure.md)
- [Konfigurera villkorlig åtkomst](../database/conditional-access-configure.md)
- [Flerfaktorautentisering för Azure AD](../database/authentication-mfa-ssms-overview.md)
- [Konfigurera multifaktorautentisering](../database/authentication-mfa-ssms-configure.md)
- [Konfigurera en princip för temporal bevarande](../database/temporal-tables-retention-policy.md)
- [Konfigurera TDE med BYOK](../database/transparent-data-encryption-byok-configure.md)
- [Rotera TDE BYOK-nycklar](../database/transparent-data-encryption-byok-key-rotation.md)
- [Ta bort ett TDE-skydd](../database/transparent-data-encryption-byok-remove-tde-protector.md)
- [Konfigurera minnesintern OLTP](../in-memory-oltp-configure.md)
- [Konfigurera Azure Automation](../database/automation-manage.md)
- Med [Transaktionsreplikering kan du](replication-between-two-instances-configure-tutorial.md) replikera data mellan hanterade instanser eller från SQL Server lokalt till SQL-hanterad instans och vice versa.
- [Konfigurera hot identifiering](threat-detection-configure.md) – [hot identifiering](../database/threat-detection-overview.md) är en inbyggd funktion för Azure SQL-hanterad instans som identifierar olika potentiella attacker som SQL-inmatning eller åtkomst från misstänkta platser. 
- Genom att [skapa aviseringar](alerts-create.md) kan du ställa in aviseringar på övervakade mått, till exempel processor användning, användning av lagrings utrymme, IOPS och andra för SQL-hanterad instans. 

## <a name="monitoring-and-tuning"></a>Övervakning och justering

- [Manuell inställning](../database/performance-guidance.md)
- [Använda DMV:er för att övervaka prestanda](../database/monitoring-with-dmvs.md)
- [Använd Query Store för att övervaka prestanda](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)
- [Felsöka prestanda med Intelligent Insights](../database/intelligent-insights-troubleshoot-performance.md)
- [Använd loggen för Intelligent Insights diagnostik](../database/intelligent-insights-use-diagnostics-log.md)
- [Övervaka In-Memory OLTP-utrymme](../in-memory-oltp-monitor-space.md)

### <a name="extended-events"></a>Utökade händelser

- [Utökade händelser](../database/xevent-db-diff-from-svr.md)
- [Lagra utökade händelser i en händelse fil](../database/xevent-code-event-file.md)
- [Lagra utökade händelser i en ringbufferten](../database/xevent-code-ring-buffer.md)

### <a name="alerting"></a>Aviseringar

- [Skapa aviseringar på en hanterad instans](alerts-create.md)

## <a name="operations"></a>Operations

- [Användarinitierad manuell redundansväxling på SQL Managed Instance](user-initiated-failover.md)

## <a name="develop-applications"></a>Utveckla program

- [Anslutningsmöjligheter](../database/connect-query-content-reference-guide.md#libraries)
- [Använda Spark-anslutningsappen](../../cosmos-db/spark-connector.md)
- [Autentisera en app](../database/application-authentication-get-client-id-keys.md)
- [Använd batching för bättre prestanda](../performance-improve-use-batching.md)
- [Vägledning för anslutningar](../database/troubleshoot-common-connectivity-issues.md)
- [DNS-alias](../database/dns-alias-overview.md)
- [Konfigurera ett DNS-alias med hjälp av PowerShell](../database/dns-alias-powershell-create.md)
- [Portar – ADO.NET](../database/adonet-v12-develop-direct-route-ports.md)
- [C och C ++](../database/develop-cplusplus-simple.md)
- [Excel](../database/connect-excel.md)

## <a name="design-applications"></a>Utforma program

- [Utforma för haveriberedskap](../database/designing-cloud-solutions-for-disaster-recovery.md)
- [Utforma för elastiska pooler](../database/disaster-recovery-strategies-for-applications-with-elastic-pool.md)
- [Utforma för appuppgraderingar](../database/manage-application-rolling-upgrade.md)

### <a name="design-multi-tenant-saas-applications"></a>Designa SaaS-program för flera innehavare

- [SaaS-designmönster](../database/saas-tenancy-app-design-patterns.md)
- [SaaS-videoindexerare](../database/saas-tenancy-video-index-wingtip-brk3120-20171011.md)
- [Säkerhet för SaaS-app](../database/saas-tenancy-elastic-tools-multi-tenant-row-level-security.md)

## <a name="next-steps"></a>Nästa steg

Kom igång genom att [Distribuera SQL-hanterad instans](instance-create-quickstart.md).