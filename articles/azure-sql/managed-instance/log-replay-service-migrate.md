---
title: Migrera databaser till SQL-hanterad instans med hjälp av logg återuppspelnings tjänsten
description: Lär dig hur du migrerar databaser från SQL Server till SQL-hanterad instans med hjälp av logg återuppspelnings tjänsten
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 02/17/2021
ms.openlocfilehash: 7892b1fe0fcad77d1fde8b44f4a8745b5c7dd334
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654595"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service"></a>Migrera databaser från SQL Server till SQL-hanterad instans med hjälp av logg återuppspelnings tjänsten
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

I den här artikeln beskrivs hur du konfigurerar Database-migrering manuellt från SQL Server 2008-2019 till SQL-hanterad instans med hjälp av logg uppspelnings tjänsten (LRS). Det här är en moln tjänst som är aktive rad för hanterad instans baserat på SQL Server logg överförings teknik utan återställnings läge. LRS bör användas i fall då data migration service (DMS) inte kan användas, när mer kontroll behövs eller om det finns lite tolerans för stillestånds tid.

## <a name="when-to-use-log-replay-service"></a>När du ska använda logg tjänsten för repetition

I de fall då [Azure DMS](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance) inte kan användas för migrering kan LRS Cloud Service användas direkt med PowerShell-, CLI-cmdletar eller API för att manuellt bygga och dirigera databas migreringar till SQL-hanterad instans. 

Du kanske vill överväga att använda LRS Cloud service i följande fall:
- Det krävs mer kontroll för ditt databas migreringsjobb
- Det finns lite tolerans för stillestånds tid vid migrering start punkt
- Det går inte att installera DMS-filen i din miljö
- Den körbara DMS-filen har inte fil åtkomst till databas säkerhets kopior
- Ingen åtkomst till värd operativ systemet är tillgänglig eller saknar administratörs behörighet

> [!NOTE]
> Rekommenderat automatiskt sätt att migrera databaser från SQL Server till SQL-hanterad instans använder Azure DMS. Den här tjänsten använder samma moln tjänst för LRS på Server sidan med logg överföring utan återställnings läge. Du bör överväga att manuellt använda LRS för att dirigera migreringar i fall när Azure DMS inte har fullt stöd för dina scenarier.

## <a name="how-does-it-work"></a>Så fungerar det

Att skapa en anpassad lösning med hjälp av LRS för att migrera en databas till molnet kräver flera Dirigerings steg som visas i diagrammet och beskrivs i tabellen nedan.

Migreringen gör fullständiga databas säkerhets kopieringar på SQL Server och kopierar säkerhetskopieringsfiler till Azure Blob Storage. LRS används för att återställa säkerhetskopierade filer från Azure Blob Storage till SQL-hanterad instans. Azure Blob Storage används som en mellanliggande lagring mellan SQL Server och SQL-hanterad instans.

LRS kommer att övervaka Azure Blob Storage för alla nya differentiella eller logg säkerhets kopior som lagts till när den fullständiga säkerhets kopieringen har återställts och återställer automatiskt nya filer som läggs till. Förloppet för säkerhets kopierings filer som återställs på SQL-hanterad instans kan övervakas med hjälp av tjänsten och processen kan också avbrytas om det behövs. Databaser som återställs under migreringsprocessen är i ett återställnings läge och kan inte användas för att läsa eller skriva tills processen har slutförts.

LRS kan startas i Autoavsluta eller kontinuerligt läge. När den startats i Autoavsluta-läge slutförs migreringen automatiskt när den senaste säkerhets kopian som angetts har återställts. När den startas i kontinuerligt läge återställer tjänsten kontinuerligt alla nya säkerhetskopierade filer som lagts till, och migreringen slutförs endast på den manuella start punkt. Det slutliga start punkt-steget gör databaser tillgängliga för Läs-och skriv användning på SQL-hanterad instans. 

  ![Åtgärder för att logga in Replay-tjänstens dirigering förklaras för SQL-hanterad instans](./media/log-replay-service-migrate/log-replay-service-conceptual.png)

| Åtgärd | Information |
| :----------------------------- | :------------------------- |
| **1. Kopiera säkerhets kopior av databasen från SQL Server till Azure Blob Storage**. | – Kopiera fullständiga, differentiella och logga säkerhets kopior från SQL Server till Azure Blob Storage med hjälp av [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) eller [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />– Vid migreringen av flera databaser krävs en separat mapp för varje databas. |
| **2. Starta LRS-tjänsten i molnet**. | – Tjänsten kan startas med ett val av cmdlet: ar: <br /> PowerShell [-Start – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_start-cmdlet: ar](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /><br />-När tjänsten har startats kommer tjänsten att ta säkerhets kopior från Azure Blob Storage och börja återställa dem på SQL-hanterad instans. <br /> – När alla inlednings vis överförda säkerhets kopior återställs, kommer tjänsten att titta efter eventuella nya filer som laddats upp till mappen och kommer kontinuerligt att tillämpa loggar som baseras på LSN-kedjan tills tjänsten har stoppats. |
| **2,1. övervaka åtgärds förloppet**. | -Förlopp för återställnings åtgärden kan övervakas med val av-eller-cmdlet: ar: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_show-cmdlet: ar](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. Stop\abort åtgärden vid behov**. | Om migreringsprocessen måste avbrytas kan åtgärden stoppas med ett val av cmdlet: ar: <br /> PowerShell [-stopp – azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_stop](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) -cmdlet: ar. <br /><br />-Detta leder till att den databas som återställs på SQL-hanterad instans tas bort. <br />-En gång som har stoppats kan LRS inte fortsätta för en databas. Migreringsprocessen måste startas om från början. |
| **3. start punkt till molnet när det är klart**. | – När alla säkerhets kopior har återställts till SQL-hanterad instans slutför du Start punkt genom att initiera åtgärden LRS Complete med ett val av API-anrop eller cmdlet: ar: <br />PowerShell [Complete-azsqlinstancedatabaselogreplay](https://docs.microsoft.com/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_complete](https://docs.microsoft.com/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) -cmdlet: ar. <br /><br />– Detta gör att LRS-tjänsten stoppas och att databasen på den hanterade instansen återställs. <br />-Peka kopplings strängen för programmet från SQL Server till SQL-hanterad instans. <br />-On-completion-databasen är tillgänglig för R/W-åtgärder i molnet. |

## <a name="requirements-for-getting-started"></a>Krav för att komma igång

### <a name="sql-server-side"></a>SQL Server sida
- SQL Server 2008-2019
- Fullständig säkerhets kopiering av databaser (en eller flera filer)
- Differentiell säkerhets kopiering (en eller flera filer)
- Logg säkerhets kopia (delas inte upp för transaktions logg filen)
- **Kontroll Summa måste vara aktive rad** som obligatorisk

### <a name="azure-side"></a>Azure-sida
-   PowerShell-AZ. SQL-modul version 2.16.0 eller högre ([Installera](https://www.powershellgallery.com/packages/Az.Sql/)eller använd Azure [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/))
-   CLI-version 2.19.0 eller senare ([Installera](https://docs.microsoft.com/cli/azure/install-azure-cli))
-   Azure-Blob Storage etablerad
-   SAS-säkerhetstoken med **Läs** -och **lista** endast behörigheter som skapats för Blob Storage

## <a name="best-practices"></a>Bästa praxis

Följande rekommenderas för bästa praxis:
- Kör [Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview) för att verifiera att dina databaser inte kommer att migreras till SQL-hanterad instans. 
- Dela fullständiga och differentiella säkerhets kopieringar i flera filer i stället för en enda fil.
- Aktivera komprimering av säkerhets kopiering.
- Använd Cloud Shell för att köra skript eftersom det alltid kommer att uppdateras till de senaste cmdletarna som släpps.
- Planera för att slutföra migreringen inom 47 timmar sedan LRS-tjänsten har startats.

> [!IMPORTANT]
> - Databasen som återställs med LRS kan inte användas förrän migreringen har slutförts. Detta beror på att den underliggande tekniken är logg överföring utan återställnings läge.
> - Vänte läge för logg överföring stöds inte av LRS på grund av versions skillnader mellan SQL-hanterad instans och senaste SQL Server version på marknaden.

## <a name="steps-to-execute"></a>Steg att köra

## <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Kopiera säkerhets kopior från SQL Server till Azure Blob Storage

Följande två metoder kan användas för att kopiera säkerhets kopior till blob-lagringen i Migrera databaser till en hanterad instans med hjälp av LRS:
- Använda SQL Server inbyggd funktion [för säkerhets kopiering till URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url) .
- Kopiera säkerhets kopiorna till BLOB-behållaren med [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)eller [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer). 

## <a name="create-azure-blob-and-sas-authentication-token"></a>Skapa Azure blob och SAS-autentiseringstoken

Azure Blob Storage används som ett mellanliggande lagrings utrymme för säkerhets kopior mellan SQL Server och SQL-hanterad instans. Följ de här stegen för att skapa en Azure Blob Storage-behållare:

1. [Skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Crete en BLOB-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) i lagrings kontot

När en BLOB-behållare har skapats genererar du SAS-autentiseringstoken med Läs-och list behörigheter endast genom att följa dessa steg:

1. Åtkomst till lagrings konto med hjälp av Azure Portal
2. Navigera till Storage Explorer
3. Expandera BLOB-behållare
4. Högerklicka på BLOB-behållaren
5. Välj Hämta signatur för delad åtkomst
6. Välj tidsintervall för förfallo tid för token. Se till att token är giltig för migreringens varaktighet.
7. Se till att Läs-och list behörigheter är markerade
8. Klicka på skapa
9. Kopiera token från och med "sa =" i URI: n för användning i din kod

> [!IMPORTANT]
> Behörigheter för SAS-token för Azure Blob Storage behöver bara läsas och lista. Om det finns andra behörigheter som har beviljats för SAS-autentiseringstoken, kommer starten av LRS-tjänsten inte att fungera. Dessa säkerhets krav är avsiktliga.

## <a name="log-in-to-azure-and-select-subscription"></a>Logga in på Azure och välj prenumeration

Använd följande PowerShell-cmdlet för att logga in på Azure:

```powershell
Login-AzAccount
```

Välj lämplig prenumeration där din SQL-hanterade instans finns med hjälp av följande PowerShell-cmdlet:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Starta migreringen

Migreringen startas genom att starta LRS-tjänsten. Tjänsten kan startas i Autoavsluta eller kontinuerligt läge. När den startats i Autoavsluta-läge slutförs migreringen automatiskt när den senaste säkerhets kopian som angetts har återställts. Det här alternativet kräver att Start kommandot anger fil namnet för den senaste säkerhets kopierings filen. När LRS startas i kontinuerligt läge återställer tjänsten kontinuerligt alla nya säkerhetskopierade filer som läggs till, och migreringen slutförs endast på den manuella start punkt. 

### <a name="start-lrs-in-autocomplete-mode"></a>Starta LRS i Autoavsluta-läge

Om du vill starta LRS-tjänsten i Autoavsluta-läge, använder du följande PowerShell-eller CLI-kommandon. Ange det senaste säkerhets kopierings fil namnet med parametern-LastBackupName. När du återställer det senaste säkerhets kopierings fil namnet initierar tjänsten automatiskt en start punkt.

Starta LRS i Autoavsluta-läge – PowerShell-exempel:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoComplete -LastBackupName "last_backup.bak"
```

Starta LRS i läget komplettera automatiskt – CLI-exempel:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Starta LRS i kontinuerligt läge

Starta LRS i kontinuerligt läge – PowerShell-exempel:

```powershell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://test.blob.core.windows.net/testing" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Starta LRS i kontinuerligt läge – CLI-exempel:

```cli
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://test.blob.core.windows.net/testing"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

> [!IMPORTANT]
> När LRS har startats kommer alla systemhanterade program varu korrigeringar att stoppas under de kommande 47 timmarna. Vid överföring av det här fönstret stoppar nästa automatiska program varu korrigering automatiskt den pågående LRS. I sådana fall kan migreringen inte återupptas och måste startas om från början. 

## <a name="monitor-the-migration-progress"></a>Övervaka förloppet för migreringen

Använd följande PowerShell-kommando för att övervaka återställnings processen för migrering:

```powershell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Använd följande CLI-kommando om du vill övervaka återställnings processen för migrering:

```cli
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Stoppa migreringen

Om du behöver stoppa migreringen använder du följande cmdletar. Om du stoppar migreringen tas återställnings databasen på SQL-hanterad instans bort på grund av att det inte går att återuppta migreringen.

Använd följande PowerShell-kommando om du vill stop\abort migreringsprocessen:

```powershell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Om du vill stop\abort migreringsprocessen använder du följande CLI-kommando:

```cli
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Slutför migreringen (kontinuerligt läge)

Om LRS startas i kontinuerligt läge när du har kontrollerat att alla säkerhets kopior har återställts, kommer initieringen av start punkt att slutföra migreringen. När Start punkt har slutförts migreras databasen och är klar för Läs-och skriv åtkomst.

För att slutföra migreringsprocessen i LRS kontinuerligt läge, använder du följande PowerShell-kommando:

```powershell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" -LastBackupName "last_backup.bak"
```

För att slutföra migreringsprocessen i LRS kontinuerligt läge, använder du följande CLI-kommando:

```cli
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [att migrera SQL Server till SQL-hanterad instans](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Läs mer om [skillnader mellan SQL Server och Azure SQL-hanterad instans](transact-sql-tsql-differences-sql-server.md).
- Lär dig mer om [metod tips för kostnads-och storleks arbets belastningar som migrerats till Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
