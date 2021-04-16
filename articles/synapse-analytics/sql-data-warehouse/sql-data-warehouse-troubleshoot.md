---
title: Felsöka dedikerad SQL-pool (tidigare SQL DW)
description: Felsöka dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: af653585ec1b57b5fd697dc755e495a96e04e677
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565414"
---
# <a name="troubleshooting-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Felsöka dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics

Den här artikeln innehåller vanliga felsökningsproblem i dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics.

## <a name="connecting"></a>Ansluta

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Inloggningen misslyckades för användaren NT AUTHORITY\ANONYMOUS LOGON. (Microsoft SQL Server, Fel: 18456) | Det här felet uppstår när en Azure AD-användare försöker ansluta till huvuddatabasen, men inte har någon användare i huvuddatabasen.  Du kan åtgärda problemet genom att antingen ange den dedikerade SQL-poolen (tidigare SQL DW) som du vill ansluta till vid anslutningen eller lägga till användaren i huvuddatabasen.  Mer [information finns](sql-data-warehouse-overview-manage-security.md) i artikeln Säkerhetsöversikt. |
| Serverns huvudnamn MyUserName kan inte komma åt huvuddatabasen i den aktuella säkerhetskontexten. Det går inte att öppna användarens standarddatabas. Det gick inte att logga in. Inloggningen misslyckades för användaren MyUserName. (Microsoft SQL Server, Fel: 916) | Det här felet uppstår när en Azure AD-användare försöker ansluta till huvuddatabasen, men inte har någon användare i huvuddatabasen.  Du kan åtgärda problemet genom att antingen ange den dedikerade SQL-poolen (tidigare SQL DW) som du vill ansluta till vid anslutningen eller lägga till användaren i huvuddatabasen.  Mer [information finns](sql-data-warehouse-overview-manage-security.md) i artikeln Säkerhetsöversikt. |
| CTAIP-fel                                                  | Det här felet kan inträffa när en inloggning har skapats på SQL Database huvuddatabasen, men inte i den specifika SQL-databasen.  Om du stöter på det här felet kan du ta en titt på [artikeln Säkerhetsöversikt.](sql-data-warehouse-overview-manage-security.md)  Den här artikeln förklarar hur du skapar en inloggning och användare i huvuddatabasen och hur du sedan skapar en användare i en SQL-databas. |
| Blockerad av brandvägg                                          | Dedikerad SQL-pool (tidigare SQL DW) skyddas av brandväggar för att säkerställa att endast kända IP-adresser har åtkomst till en databas. Brandväggarna är säkra som standard, vilket innebär att du uttryckligen måste aktivera OCH IP-adress eller adressintervall innan du kan ansluta.  Om du vill konfigurera brandväggen för åtkomst följer du stegen i [Konfigurera serverbrandväggsåtkomst](create-data-warehouse-portal.md) för klientens IP-adress i [etableringsanvisningarna.](create-data-warehouse-portal.md) |
| Det går inte att ansluta med verktyg eller drivrutin                           | Dedikerad SQL-pool (tidigare SQL DW) rekommenderar att du använder [SSMS,](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) [SSDT för Visual Studio](sql-data-warehouse-install-visual-studio.md), eller [sqlcmd för](sql-data-warehouse-get-started-connect-sqlcmd.md) att fråga dina data. Mer information om drivrutiner och anslutning till Azure Synapse finns i [artiklarna Drivrutiner för Azure Synapse](sql-data-warehouse-connection-strings.md) och Anslut till [Azure Synapse.](sql-data-warehouse-connect-overview.md) |

## <a name="tools"></a>Verktyg

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio Azure AD-användare saknas i Objektutforskaren           | Detta är ett känt problem.  Som en tillfällig lösning kan du visa användarna i [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Se [Autentisering för Azure Synapse mer](sql-data-warehouse-authentication.md) information om hur du använder Azure Active Directory dedikerad SQL-pool (tidigare SQL DW). |
| Manuell skriptning, användning av skriptguiden eller anslutning via SSMS går långsamt, svarar inte eller producerar fel | Se till att användarna har skapats i huvuddatabasen. I skriptalternativ kontrollerar du också att motorversionen är inställd på "Microsoft Azure Synapse Analytics Edition" och att motortypen är "Microsoft Azure SQL Database". |
| Det går inte att generera skript i SSMS                               | Det går inte att generera ett skript för dedikerad SQL-pool (tidigare SQL DW) om alternativet "Generera skript för beroende objekt" är inställt på "Sant". Som en tillfällig lösning måste användarna manuellt gå till **Verktyg -> Alternativ ->SQL Server Object Explorer -> Skapa skript** för beroende alternativ och ange till falskt |

## <a name="data-ingestion-and-preparation"></a>Förberedelse och inhämtning av data

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Om du exporterar tomma strängar med HJÄLP av CETAS resulterar det i NULL-värden i Parquet- och ORC-filer. Observera att om du exporterar tomma strängar från kolumner med NOT NULL-begränsningar resulterar CETAS i avvisade poster och exporten kan eventuellt misslyckas. | Ta bort tomma strängar eller den stötande kolumnen i SELECT-instruktionen för din CETAS. |
| Det går inte att läsa in ett värde utanför intervallet 0–127 i en tinyint-kolumn för Filformatet Parquet och ORC. | Ange en större datatyp för målkolumnen.           |

## <a name="performance"></a>Prestanda

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Felsökning av frågeprestanda                            | Om du försöker felsöka en viss fråga börjar du med [Lär dig hur du övervakar dina frågor.](sql-data-warehouse-manage-monitor.md#monitor-query-execution) |
| TempDB-utrymmesproblem | [Övervaka TempDB-utrymmesanvändning.](sql-data-warehouse-manage-monitor.md#monitor-tempdb)  Vanliga orsaker till att TempDB-utrymmet tar slut är:<br>– Det finns inte tillräckligt med resurser som allokerats till frågan, vilket gör att data spills till TempDB.  Se [Arbetsbelastningshantering](resource-classes-for-workload-management.md) <br>– Statistik saknas eller är in date, vilket orsakar överdriven dataförflyttning.  Se [Underhålla tabellstatistik](sql-data-warehouse-tables-statistics.md) för mer information om hur du skapar statistik<br>– TempDB-utrymme allokeras per servicenivå.  [Om du skalar din dedikerade SQL-pool (tidigare SQL DW)](sql-data-warehouse-manage-compute-overview.md#scaling-compute) till en högre DWU-inställning allokeras mer TempDB-utrymme.|
| Dåliga frågeprestanda och planer beror ofta på statistik som saknas | Den vanligaste orsaken till dåliga prestanda är brist på statistik i dina tabeller.  Se [Underhålla tabellstatistik](sql-data-warehouse-tables-statistics.md) för mer information om hur du skapar statistik och varför de är viktiga för dina prestanda. |
| Låg samtidighet/frågor i kö                             | Det [är viktigt att](resource-classes-for-workload-management.md) förstå arbetsbelastningshantering för att förstå hur du balanserar minnesallokering med samtidighet. |
| Så här implementerar du metodtips                              | Den bästa platsen att börja lära sig sätt att förbättra frågeprestanda är den dedikerade artikeln om metodtips för [SQL-pool (tidigare SQL DW).](sql-data-warehouse-best-practices.md) |
| Förbättra prestanda med skalning                      | Ibland är lösningen för att förbättra prestandan att helt enkelt lägga till mer beräkningskraft i dina frågor genom att skala din dedikerade [SQL-pool (tidigare SQL DW).](sql-data-warehouse-manage-compute-overview.md) |
| Dåliga frågeprestanda på grund av dålig indexkvalitet     | Vissa gånger kan frågor bli långsammare på grund av [låg kolumnlagringsindexkvalitet](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality).  I den här artikeln finns mer information och hur du [återskapar index för att förbättra segmentkvaliteten.](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) |

## <a name="system-management"></a>Systemhantering

| Problem                                                        | Lösning                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: Det gick inte att utföra åtgärden eftersom servern skulle överskrida den tillåtna kvoten för databastransaktionsenhet på 45 000. | Minska [DWU för](what-is-a-data-warehouse-unit-dwu-cdwu.md) databasen som du försöker skapa eller begär [en kvotökning.](sql-data-warehouse-get-started-create-support-ticket.md) |
| Undersöka utrymmesutnyttjande                              | Se [Tabellstorlekar för](sql-data-warehouse-tables-overview.md#table-size-queries) att förstå systemets utrymmesanvändning. |
| Hjälp med att hantera tabeller                                    | Se artikeln [Tabellöversikt](sql-data-warehouse-tables-overview.md) för hjälp med att hantera dina tabeller.  Den här artikeln innehåller även länkar till [](sql-data-warehouse-tables-distribute.md)mer detaljerade ämnen som [Tabelldatatyper,](sql-data-warehouse-tables-data-types.md)Distribuera en tabell, [Indexera](sql-data-warehouse-tables-index.md)en tabell, [Partitionera](sql-data-warehouse-tables-partition.md)en tabell, [Underhålla tabellstatistik](sql-data-warehouse-tables-statistics.md) och [Temporära tabeller.](sql-data-warehouse-tables-temporary.md) |
| Transparent datakryptering (TDE) uppdateras inte i Azure Portal | Du kan visa tillståndet för TDE via [PowerShell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). |

## <a name="differences-from-sql-database"></a>Skillnader från SQL Database

| Problem                                 | Lösning                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| Funktioner SQL Database stöds inte     | Se [Tabellfunktioner som inte stöds.](sql-data-warehouse-tables-overview.md#unsupported-table-features) |
| Datatyper som SQL Database stöds inte   | Se [Datatyper som inte stöds.](sql-data-warehouse-tables-data-types.md#identify-unsupported-data-types)        |
| Begränsningar för lagrad procedur          | Se [Begränsningar för lagrad](sql-data-warehouse-develop-stored-procedures.md#limitations) procedur för att förstå några av begränsningarna med lagrade procedurer. |
| UDF:er stöder inte SELECT-instruktioner | Det här är en aktuell begränsning för våraudf:er.  Se [CREATE FUNCTION](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för den syntax som vi stöder. |
| sp_rename (förhandsversion) för kolumner fungerar inte med scheman utanför *dbo* | Det här är en aktuell begränsning i Synapse sp_rename [(förhandsversion) för kolumner](/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Kolumner i objekt som inte ingår i *dbo-schemat* kan byta namn via en CTAS till en ny tabell. |

## <a name="next-steps"></a>Nästa steg

Här är några andra resurser som du kan prova för mer hjälp med att hitta en lösning på problemet.

* [Bloggar](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Funktionsbegäranden](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videor](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md)
* [Frågesida&Microsoft Q&](/answers/topics/azure-synapse-analytics.html)
* [Stack Overflow forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)