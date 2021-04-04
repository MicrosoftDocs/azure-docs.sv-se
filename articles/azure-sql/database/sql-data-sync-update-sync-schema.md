---
title: Automatisera replikeringen av schema ändringar i SQL Data Sync
description: Lär dig hur du automatiserar replikeringen av schema ändringar i Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790193"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatisera replikeringen av schema ändringar i Azure SQL Data Sync
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

SQL Data Sync låter användarna synkronisera data mellan databaser i Azure SQL Database och SQL Server instanser i en riktning eller i båda riktningarna. En av de aktuella begränsningarna i SQL Data Sync saknar stöd för replikering av schema ändringar. Varje gång du ändrar tabellens schema måste du tillämpa ändringarna manuellt på alla slut punkter, inklusive hubben och alla medlemmar, och sedan uppdatera synkroniseringsschemat.

Den här artikeln introducerar en lösning för att automatiskt replikera schema ändringar till alla SQL Data Sync-slutpunkter.

1. Den här lösningen använder en DDL-utlösare för att spåra schema ändringar.
1. Utlösaren infogar schema ändrings kommandona i en spårnings tabell.
1. Den här spårnings tabellen synkroniseras till alla slut punkter med hjälp av tjänsten Data Sync.
1. DML-utlösare efter infogning används för att tillämpa schema ändringarna på de andra slut punkterna.

I den här artikeln används ALTER TABLE som ett exempel på en schema ändring, men den här lösningen fungerar även för andra typer av schema ändringar.

> [!IMPORTANT]
> Vi rekommenderar att du läser den här artikeln noggrant, i synnerhet avsnitt om [fel sökning](#troubleshoot) och [andra överväganden](#other), innan du börjar implementera automatisk replikering av schema ändringar i din synkroniserade miljö. Vi rekommenderar också att du läser [Sync-data i flera moln och lokala databaser med SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md). Vissa databas åtgärder kan bryta lösningen som beskrivs i den här artikeln. Ytterligare domän kunskaper om SQL Server och Transact-SQL kan krävas för att felsöka problemen.

![Automatisera replikeringen av schema ändringar](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Konfigurera automatisk replikering av schema ändringar

### <a name="create-a-table-to-track-schema-changes"></a>Skapa en tabell för att spåra schema ändringar

Skapa en tabell för att spåra schema ändringar i alla databaser i Sync-gruppen:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Den här tabellen innehåller en identitets kolumn för att spåra ordningen på schema ändringar. Du kan lägga till fler fält för att logga mer information om det behövs.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Skapa en tabell för att spåra historiken för schema ändringar

På alla slut punkter skapar du en tabell för att spåra ID för det senast tillämpade schema ändrings kommandot.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Skapa en ALTER TABLE DDL-utlösare i databasen där schema ändringar görs

Skapa en DDL-utlösare för ALTER TABLE-åtgärder. Du behöver bara skapa den här utlösaren i databasen där schema ändringar görs. För att undvika konflikter kan du bara tillåta schema ändringar i en databas i en Sync-grupp.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

Utlösaren infogar en post i tabellen schema ändrings spårning för varje ALTER TABLE-kommando. I det här exemplet läggs ett filter till för att undvika att replikera schema ändringar som görs under schema **DataSync**, eftersom de är mest sannolika av tjänsten Data Sync. Lägg till fler filter om du bara vill replikera vissa typer av schema ändringar.

Du kan också lägga till fler utlösare för att replikera andra typer av schema ändringar. Skapa till exempel CREATE_PROCEDURE, ALTER_PROCEDURE och DROP_PROCEDURE utlösare för att replikera ändringar till lagrade procedurer.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Skapa en utlösare på andra slut punkter för att tillämpa schema ändringar under infogning

Den här utlösaren kör kommandot schema ändring när den synkroniseras till andra slut punkter. Du måste skapa den här utlösaren på alla slut punkter, förutom den där schema ändringar görs (det vill säga i databasen där DDL-utlösaren `AlterTableDDLTrigger` skapas i föregående steg).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Den här utlösaren körs efter infogningen och kontrollerar om det aktuella kommandot ska köras nästa. Kod logiken ser till att ingen schema ändrings instruktion hoppas över och alla ändringar tillämpas även om infogningen är i rätt ordning.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Synkronisera spårnings tabellen för schema ändringar till alla slut punkter

Du kan synkronisera spårnings tabellen för schema ändringar till alla slut punkter med hjälp av den befintliga Sync-gruppen eller en ny Sync-grupp. Se till att ändringarna i spårnings tabellen kan synkroniseras till alla slut punkter, särskilt när du använder synkronisering med en riktning.

Synkronisera inte tabellen ändra historik för schema, eftersom tabellen behåller olika tillstånd för olika slut punkter.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Använda schema ändringarna i en synkroniserad grupp

Endast schema ändringar som gjorts i databasen där DDL-utlösaren skapas replikeras. Schema ändringar som gjorts i andra databaser replikeras inte.

När schema ändringarna har repliker ATS till alla slut punkter måste du också vidta ytterligare åtgärder för att uppdatera synkroniseringsschemat för att starta eller stoppa synkroniseringen av de nya kolumnerna.

#### <a name="add-new-columns"></a>Lägg till nya kolumner

1.  Gör schema ändringen.

1.  Undvik data förändringar där de nya kolumnerna är inblandade tills du har slutfört steget som skapar utlösaren.

1.  Vänta tills schema ändringarna tillämpas på alla slut punkter.

1.  Uppdatera databasschemat och Lägg till den nya kolumnen i synkroniseringsschemat.

1.  Data i den nya kolumnen synkroniseras under nästa synkronisering.

#### <a name="remove-columns"></a>Ta bort kolumner

1.  Ta bort kolumnerna från synkroniseringsschemat. Datasynkroniseringen stoppar synkronisering av data i dessa kolumner.

1.  Gör schema ändringen.

1.  Uppdatera databasschemat.

#### <a name="update-data-types"></a>Uppdatera data typer

1.  Gör schema ändringen.

1.  Vänta tills schema ändringarna tillämpas på alla slut punkter.

1.  Uppdatera databasschemat.

1.  Om de nya och gamla data typerna inte är helt kompatibla, till exempel om du ändrar från `int` till `bigint` Sync kan Miss lyckas innan stegen som skapar utlösarna har slutförts. Synkroniseringen lyckas efter ett nytt försök.

#### <a name="rename-columns-or-tables"></a>Byt namn på kolumner eller tabeller

Att byta namn på kolumner eller tabeller gör att datasynkroniseringen slutar fungera. Skapa en ny tabell eller kolumn, Fyll i data och ta sedan bort den gamla tabellen eller kolumnen i stället för att byta namn.

#### <a name="other-types-of-schema-changes"></a>Andra typer av schema ändringar

För andra typer av schema ändringar – till exempel att skapa lagrade procedurer eller släppa ett index, krävs inte synkroniseringsschemat.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Felsöka automatisk replikering av schema ändringar

Den replikeringsfrekvens som beskrivs i den här artikeln upphör att fungera i vissa situationer, till exempel om du har gjort en schema ändring i en lokal databas som inte stöds i Azure SQL Database. I så fall Miss lyckas synkroniseringen av schema ändrings spårnings tabellen. Du behöver åtgärda det här problemet manuellt:

1.  Inaktivera DDL-utlösaren och Undvik eventuella ytterligare schema ändringar tills problemet har åtgärd ATS.

1.  I slut punkts databasen där problemet inträffar inaktiverar du efter INFOGNINGs utlösaren på slut punkten där schema ändringen inte kan göras. Den här åtgärden tillåter att kommandot schema ändring synkroniseras.

1.  Utlös synkronisering för att synkronisera tabellen schema ändrings spårning.

1.  I slut punkts databasen där problemet inträffar frågar du tabellen schema ändrings historik för att hämta ID för senast använda schema ändrings kommando.

1.  Fråga spårnings tabellen för schema ändring om du vill visa alla kommandon med ett ID som är större än det ID-värde som du hämtade i föregående steg.

    a.  Ignorera de kommandon som inte kan köras i slut punkts databasen. Du måste hantera inkonsekvenser i schemat. Återställ de ursprungliga schema ändringarna om inkonsekvensen påverkar ditt program.

    b.  Tillämpa de kommandon som ska användas manuellt.

1.  Uppdatera tabellen schema ändrings historik och ange det senast använda ID: t till rätt värde.

1.  Kontrol lera om schemat är uppdaterat.

1.  Återaktivera aktiveringen av utlösaren efter INFOGNING inaktive rad i det andra steget.

1.  Återaktivera DDL-utlösaren inaktive rad i det första steget.

Om du vill rensa posterna i tabellen schema ändrings spårning, använder du DELETE i stället för TRUNKERA. Dirigera aldrig om identitets kolumnen i tabellen schema ändrings spårning med hjälp av DBCC CHECKIDENT. Du kan skapa nya spårnings tabeller för schema ändringar och uppdatera tabell namnet i DDL-utlösaren om omdirigering krävs.

## <a name="other-considerations"></a><a name="other"></a> Andra överväganden

-   Databas användare som konfigurerar nav-och medlems databaser måste ha tillräcklig behörighet för att köra kommandona för schema ändringar.

-   Du kan lägga till fler filter i DDL-utlösaren för att endast replikera schema ändringar i valda tabeller eller åtgärder.

-   Du kan bara göra schema ändringar i databasen där DDL-utlösaren skapas.

-   Om du gör en ändring i en SQL Server databas kontrollerar du att schema ändringen stöds i Azure SQL Database.

-   Om schema ändringar görs i andra databaser än databasen där DDL-utlösaren skapas replikeras inte ändringarna. För att undvika det här problemet kan du skapa DDL-utlösare för att blockera ändringar på andra slut punkter.

-   Om du behöver ändra schemat för schema ändrings spårnings tabellen inaktiverar du DDL-utlösaren innan du gör ändringen och tillämpar sedan ändringarna manuellt på alla slut punkter. Att uppdatera schemat i en efter INFOGNINGs utlösare i samma tabell fungerar inte.

-   Dirigera inte om kolumnen identitet med hjälp av DBCC CHECKIDENT.

-   Använd inte TRUNKERA för att rensa data i spårnings tabellen för schema ändringar.

## <a name="next-steps"></a>Nästa steg

Mer information om SQL Data Sync finns i:

-   Översikt – [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
-   Konfigurera Data Sync
    - I portalen – [Självstudier: konfigurera SQL Data Sync för att synkronisera data mellan Azure SQL Database och SQL Server](sql-data-sync-sql-server-configure.md)
    - Med PowerShell
        -  [Använd PowerShell för att synkronisera mellan flera databaser i Azure SQL Database](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Använda PowerShell för att synkronisera mellan en databas i Azure SQL Database och en databas i en SQL Server instans](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Datasynkroniseringsagent – [Datasynkroniseringsagent för Azure SQL Data Sync](sql-data-sync-agent-overview.md)
-   Metodtips – [Metodtips för Azure SQL Data Sync](sql-data-sync-best-practices.md)
-   Övervaka [SQL Data Sync med Azure Monitor loggar](./monitor-tune-overview.md)
-   Felsökning – [Felsöka problem med Azure SQL Data Sync]()
-   Uppdatera synkroniseringsschemat
    -   Med PowerShell – [Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp](scripts/update-sync-schema-in-sync-group.md)