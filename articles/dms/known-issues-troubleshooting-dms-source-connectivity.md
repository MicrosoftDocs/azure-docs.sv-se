---
title: Problem med att ansluta käll databaser
titleSuffix: Azure Database Migration Service
description: Lär dig mer om fel sökning av kända problem/fel som är kopplade till att ansluta Azure Database Migration Service till käll databaser.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: edc420cb1e79ed6d99a55524764cb164bd2edaf5
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641342"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>Felsöka DMS-fel vid anslutning till källdatabaser

Följande artikel innehåller information om hur du kan åtgärda eventuella problem som kan uppstå när du ansluter Azure Database Migration Service (DMS) till käll databasen. Varje avsnitt nedan relaterar till en speciell typ av käll databas som visar felet som kan uppstå tillsammans med information och länkar till information om fel sökning av anslutningen.

## <a name="sql-server"></a>SQL Server

Eventuella problem som är kopplade till att ansluta till en käll SQL Server databas och hur du kan adressera dem finns i följande tabell.

| Fel         | Information om orsak och fel sökning |
| ------------- | ------------- |
| SQL-anslutning misslyckades. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrol lera att instans namnet är rätt och att SQL Server har kon figurer ATS för att tillåta fjärr anslutningar.<br> | Det här felet uppstår om tjänsten inte kan hitta käll servern. För att lösa problemet kan du läsa artikeln [fel vid anslutning till käll SQL Server när du använder dynamisk port eller namngiven instans](./known-issues-troubleshooting-dms.md#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance). |
| **Fel 53** – SQL-anslutning misslyckades. (Även för felkoder 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | Det här felet uppstår om tjänsten inte kan ansluta till käll servern. För att åtgärda problemet, se följande resurser och försök sedan igen. <br><br>  [Interaktiv användar guide för fel sökning av anslutnings problem](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Förutsättningar för att migrera SQL Server till Azure SQL Database](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) <br><br> [Krav för migrering av SQL Server till en hanterad Azure SQL-instans](./pre-reqs.md#prerequisites-for-migrating-sql-server-to-azure-sql-managed-instance) |
| **Fel 18456** -inloggningen misslyckades.<br> | Det här felet uppstår om tjänsten inte kan ansluta till käll databasen med de angivna T-SQL-autentiseringsuppgifterna. Åtgärda problemet genom att kontrol lera de angivna autentiseringsuppgifterna. Du kan också referera till [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) eller till fel söknings dokumenten som anges i anteckningen under den här tabellen och sedan försöka igen. |
| Ett felaktigt AccountName-värde har {0} angetts. Förväntat format för AccountName är DomainName\UserName<br> | Det här felet uppstår om användaren väljer Windows-autentisering men anger användar namnet i ett ogiltigt format. För att åtgärda problemet anger du antingen användar namn i rätt format för Windows-autentisering eller väljer **SQL-autentisering**. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

Potentiella problem som är kopplade till att ansluta till en källa AWS RDS MySQL-databas och hur du kan adressera dem finns i följande tabell.

| Fel         | Information om orsak och fel sökning |
| ------------- | ------------- |
| **Fel [2003]**[HY000]-anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC x. x (w) driv rutin] Det går inte att ansluta till MySQL-server på {Server} (10060) | Det här felet uppstår om MySQL ODBC-drivrutinen inte kan ansluta till käll servern. Du löser problemet genom att läsa fel söknings dokumenten som anges i anteckningen under den här tabellen och sedan försöka igen.<br> |
| **Fel [2005]**[HY000]-anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC x. x (w) driv rutin] okänd MySQL-server värd {Server} | Det här felet uppstår om tjänsten inte kan hitta käll värden på RDS. Problemet kan bero på att källan inte finns eller på att det finns ett problem med RDS-infrastrukturen. Du löser problemet genom att läsa fel söknings dokumenten som anges i anteckningen under den här tabellen och sedan försöka igen.<br> |
| **Fel [1045]**[HY000]-anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC x. x (w)-driv rutin] åtkomst nekad för användaren {user} @ {server} (med lösen ord: Ja) | Det här felet uppstår om MySQL ODBC-drivrutinen inte kan ansluta till käll servern på grund av ogiltiga autentiseringsuppgifter. Verifiera de autentiseringsuppgifter som du har angett. Om problemet kvarstår kontrollerar du att käll datorn har rätt autentiseringsuppgifter. Du kan behöva återställa lösen ordet i-konsolen. Om du fortfarande stöter på problemet går du till fel söknings dokumenten som anges i anteckningen under den här tabellen och försöker sedan igen.<br> |
| **Fel [9002]**[HY000]-anslutningen misslyckades. FEL [HY000] [MySQL] [ODBC x. x (w) driv rutin] anslutnings strängen kanske inte är rätt. Besök portalen för referenser.| Det här felet uppstår om anslutningen inte fungerar på grund av ett problem med anslutnings strängen. Kontrol lera att den angivna anslutnings strängen är giltig. Du löser problemet genom att läsa fel söknings dokumenten som anges i anteckningen under den här tabellen och sedan försöka igen.<br> |
| **Fel i binär loggning. Variabeln binlog_format har värdet {Value}. Ändra den till ' rad '.** | Det här felet uppstår om det uppstår ett fel i binär loggning. variabeln binlog_format har fel värde. Åtgärda problemet genom att ändra binlog_format i parameter grupp till rad och sedan starta om instansen. Mer information finns i avsnittet om [alternativ för binär loggning och variabler](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) eller [AWS RDS MySQL-databas logg fil dokumentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html).<br> |

> [!NOTE]
> Mer information om fel sökning av problem som rör anslutning till en källa AWS RDS MySQL-databas finns i följande resurser:
> *    [Fel sökning av problem med Amazon RDS-anslutning](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> *    [Hur gör jag för att lösa problem med att ansluta till min Amazon RDS Database-instans?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS-PostgreSQL

Potentiella problem som är kopplade till att ansluta till en källa AWS RDS PostgreSQL-databas och hur du kan adressera dem finns i följande tabell.

| Fel         | Information om orsak och fel sökning |
| ------------- | ------------- |
| **Fel [101]**[08001]-anslutningen misslyckades. FELET [08001] har upphört att gälla. | Det här felet uppstår om postgres-drivrutinen inte kan ansluta till käll servern. Du löser problemet genom att läsa fel söknings dokumenten som anges i anteckningen under den här tabellen och sedan försöka igen. |
| **Fel: parametern wal_level har värdet {Value}. Ändra den till "logisk" för att tillåta replikering.** | Det här felet uppstår om parametern wal_level har fel värde. Åtgärda problemet genom att ändra rds.logical_replication i parameter grupp till 1 och sedan starta om instansen. Mer information finns i [krav för att migrera till Azure postgresql med hjälp av DMS](./tutorial-postgresql-azure-postgresql-online.md#prerequisites) eller [POSTGRESQL på Amazon RDS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html). |

> [!NOTE]
> Mer information om fel sökning av problem som rör anslutning till en AWS RDS PostgreSQL-databas finns i följande resurser:
> *    [Fel sökning av problem med Amazon RDS-anslutning](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> *    [Hur gör jag för att lösa problem med att ansluta till min Amazon RDS Database-instans?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

Potentiella problem som är kopplade till att ansluta till en källa AWS RDS SQL Server-databasen och hur du kan adressera dem finns i följande tabell.

| Fel         | Information om orsak och fel sökning |
| ------------- | ------------- |
| **Fel 53** – SQL-anslutning misslyckades. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrol lera att instans namnet är rätt och att SQL Server har kon figurer ATS för att tillåta fjärr anslutningar. (provider: Named Pipes-Provider, fel: 40-Det gick inte att öppna en anslutning till SQL Server | Det här felet uppstår om tjänsten inte kan ansluta till käll servern. Du löser problemet genom att läsa fel söknings dokumenten som anges i anteckningen under den här tabellen och sedan försöka igen. |
| **Fel 18456** -inloggningen misslyckades. Inloggningen misslyckades för användaren {user} | Det här felet uppstår om tjänsten inte kan ansluta till käll databasen med de T-SQL-autentiseringsuppgifter som angetts. Åtgärda problemet genom att kontrol lera de angivna autentiseringsuppgifterna. Du kan också referera till [MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) eller till fel söknings dokumenten som anges i anteckningen under den här tabellen och försöka igen. |
| **Fel 87** – anslutnings strängen är inte giltig. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrol lera att instans namnet är rätt och att SQL Server har kon figurer ATS för att tillåta fjärr anslutningar. (provider: SQL-nätverks gränssnitt, fel: 25-anslutnings strängen är inte giltig) | Det här felet uppstår om tjänsten inte kan ansluta till käll servern på grund av en ogiltig anslutnings sträng. Åtgärda problemet genom att kontrol lera den angivna anslutnings strängen. Om problemet kvarstår läser du fel söknings dokumenten som anges i kommentaren under den här tabellen och försöker sedan igen. |
| **Fel – Server certifikatet är inte betrott.** En anslutning har upprättats till servern, men ett fel uppstod under inloggnings processen. (provider: SSL-Provider, fel: 0-certifikat kedjan utfärdades av en utfärdare som inte är betrodd.) | Det här felet uppstår om certifikatet som används inte är betrott. För att åtgärda problemet måste du hitta ett certifikat som kan vara betrott och sedan aktivera det på servern. Alternativt kan du välja alternativet förtroende certifikat när du ansluter. Använd endast den här åtgärden om du är bekant med certifikatet som används och du litar på det. <br> TLS-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte stark säkerhet – de är mottagliga för man-in-the-middle-attacker. Förlita dig inte på TLS med självsignerade certifikat i en produktions miljö eller på servrar som är anslutna till Internet. <br> Mer information finns i [använda SSL med en Microsoft SQL Server databas instans](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) eller [Självstudier: migrera fjärr skrivbords SQL Server till Azure med hjälp av DMS](./index.yml). |
| **Fel 300** – användaren har inte de behörigheter som krävs. Behörighet för att Visa SERVER tillstånd nekades för objektet {Server}, databasen {Database} | Det här felet uppstår om användaren inte har behörighet att utföra migreringen. Information om hur du löser problemet finns i [Granting Server Permissions – Transact-SQL](/sql/t-sql/statements/grant-server-permissions-transact-sql) eller [självstudie: migrera RDS-SQL Server till Azure med hjälp av DMS](./index.yml) för mer information. |

> [!NOTE]
> Mer information om fel sökning av problem som rör anslutning till en källa AWS RDS SQL Server finns i följande resurser:
>
> *    [Lösa anslutningsfel till SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [Hur gör jag för att lösa problem med att ansluta till min Amazon RDS Database-instans?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>Kända problem

* [Kända problem/migrerings begränsningar med online-migreringar till Azure SQL Database](./index.yml)
* [Kända problem/migrerings begränsningar med online-migreringar till Azure Database for MySQL](./known-issues-azure-mysql-online.md)
* [Kända problem/migrerings begränsningar med online-migreringar till Azure Database for PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Nästa steg

* Visa artikeln [Azure Database migration service PowerShell](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0&preserve-view=true#data_migration).
* Se artikeln [så här konfigurerar du Server parametrar i Azure Database for MySQL med hjälp av Azure Portal](../mysql/howto-server-parameters.md).
* Se artikeln [Översikt över krav för att använda Azure Database migration service](./pre-reqs.md).
* Se [vanliga frågor och svar om hur du använder Azure Database migration service](./faq.md).
