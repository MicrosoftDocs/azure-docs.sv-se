---
title: Migrera databaser till SQL-hanterad instans med hjälp av logg återuppspelnings tjänsten
description: Lär dig hur du migrerar databaser från SQL Server till SQL-hanterad instans med hjälp av logg återuppspelnings tjänsten
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 74403b7ec1469ce7cdaadc9931eb5ac95f55f6f5
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096844"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>Migrera databaser från SQL Server till SQL-hanterad instans med hjälp av logg uppspelnings tjänsten (för hands version)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

I den här artikeln beskrivs hur du manuellt konfigurerar migrering av databasen från SQL Server 2008-2019 till SQL-hanterad instans med hjälp av logg uppspelnings tjänsten (LRS) för närvarande i offentlig för hands version. Det här är en moln tjänst som är aktive rad för hanterad instans baserat på SQL Server logg överförings teknik. LRS bör användas i fall när det finns komplexa anpassade migreringar och hybrid arkitekturer, när mer kontroll behövs, när det finns liten tolerans för stillestånds tid, eller när det inte går att använda tjänsten Azure Data Migration service (DMS).

Både DMS och LRS använder samma underliggande teknik för migrering och samma API: er. När du släpper LRS aktiverar vi ytterligare komplexa anpassade migreringar och hybrid arkitektur mellan lokal. SQL Server-och SQL-hanterade instanser.

## <a name="when-to-use-log-replay-service"></a>När du ska använda logg tjänsten för repetition

I de fall då [Azure DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) inte kan användas för migrering kan LRS Cloud Service användas direkt med PowerShell-, CLI-cmdletar eller API för att manuellt bygga och dirigera databas migreringar till SQL-hanterad instans. 

Du kanske vill överväga att använda LRS Cloud service i följande fall:
- Det krävs mer kontroll för ditt databas migreringsjobb
- Det finns lite tolerans för stillestånds tid vid migrering start punkt
- Det går inte att installera DMS-filen i din miljö
- Den körbara DMS-filen har inte fil åtkomst till databas säkerhets kopior
- Ingen åtkomst till värd operativ systemet är tillgänglig eller saknar administratörs behörighet
- Det går inte att öppna nätverks portar från din miljö till Azure
- Säkerhets kopieringar lagras direkt till Azure Blob Storage med till URL-alternativet
- Det finns ett behov av att använda differentiella säkerhets kopior

> [!NOTE]
> Rekommenderat automatiskt sätt att migrera databaser från SQL Server till SQL-hanterad instans använder Azure DMS. Den här tjänsten använder samma moln tjänst för LRS i Server delen med logg överföring i NORECOVERY-läge. Du bör överväga att manuellt använda LRS för att dirigera migreringar i fall när Azure DMS inte har fullt stöd för dina scenarier.

## <a name="how-does-it-work"></a>Så fungerar det

Att skapa en anpassad lösning med hjälp av LRS för att migrera databaser till molnet kräver flera Dirigerings steg som visas i diagrammet och beskrivs i tabellen nedan.

Migreringen består av att göra fullständiga säkerhets kopieringar av databasen SQL Server med kontroll summan aktive rad och kopiera säkerhetskopieringsfiler till Azure Blob Storage. LRS används för att återställa säkerhetskopierade filer från Azure Blob Storage till SQL-hanterad instans. Azure Blob Storage används som mellanliggande lagring mellan SQL Server och SQL-hanterad instans.

LRS övervakar Azure Blob Storage för alla nya differentiella eller loggar säkerhets kopior som lagts till när den fullständiga säkerhets kopieringen har återställts och återställer automatiskt nya filer som läggs till. Förloppet för säkerhets kopierings filer som återställs på SQL-hanterad instans kan övervakas med hjälp av tjänsten och processen kan också avbrytas om det behövs.

LRS kräver inte en speciell namngivnings konvention för säkerhets kopiering eftersom den söker igenom alla filer som placerats på Azure Blob Storage och den konstruerar säkerhets kopierings kedjan från att bara läsa fil huvudena. Databaserna har statusen "återställning" under migreringsprocessen, eftersom de återställs i [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) -läge och kan inte användas för läsning eller skrivning förrän migreringen har slutförts fullständigt. 

Vid migreringen av flera databaser måste säkerhets kopior för varje databas placeras i en separat mapp på Azure Blob Storage. LRS måste startas separat för varje databas och olika sökvägar för att separata Azure Blob Storage-mappar måste anges. 

LRS kan startas i Autoavsluta eller kontinuerligt läge. När du startar i Autoavsluta-läge, slutförs migreringen automatiskt när det senaste säkerhets kopierings fil namnet har återställts. När den startas i kontinuerligt läge återställer tjänsten kontinuerligt alla nya säkerhetskopierade filer som lagts till, och migreringen slutförs endast på den manuella start punkt. Det rekommenderas att den manuella start punkt körs först efter att den slutgiltiga logg säkerhets kopieringen har utförts och visats som återställd på SQL-hanterad instans. Det sista steget i Start punkt gör att databasen är online och tillgänglig för Läs-och skriv användning på SQL-hanterad instans.

När LRS har stoppats, antingen automatiskt vid automatisk komplettering eller manuellt på Start punkt, går det inte att återuppta återställnings processen för en databas som har kopplats online på SQL-hanterad instans. Om du vill återställa ytterligare säkerhetskopieringsfiler när migreringen har slutförts via Autoavsluta, eller manuellt på Start punkt, måste databasen tas bort och hela säkerhets kopierings kedjan måste återställas från början genom att du startar om LRS.

   :::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Åtgärder för att logga in Replay-tjänstens dirigering förklaras för SQL-hanterad instans" border="false":::
    
| Åtgärd | Information |
| :----------------------------- | :------------------------- |
| **1. Kopiera säkerhets kopior av databasen från SQL Server till Azure Blob Storage**. | – Kopiera fullständiga, differentiella och logga säkerhets kopior från SQL Server till Azure Blob Storage-behållare med hjälp av [AzCopy](/azure/storage/common/storage-use-azcopy-v10)eller [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br />-Använd alla fil namn, eftersom LRS inte kräver någon speciell fil namngivnings konvention.<br />– Vid migreringen av flera databaser krävs en separat mapp för varje databas. |
| **2. Starta LRS-tjänsten i molnet**. | – Tjänsten kan startas med ett val av cmdlet: ar: <br /> PowerShell [-Start – azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_start-cmdlet: ar](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start). <br /> -Starta LRS separat för varje databas som pekar på en annan mapp för säkerhets kopiering på Azure Blob Storage. <br />-När tjänsten har startats kommer tjänsten att ta säkerhets kopior från Azure Blob Storage-behållaren och börja återställa dem på SQL-hanterad instans.<br /> – Om LRS startades i kontinuerligt läge, kommer tjänsten att se om det finns nya filer som har laddats upp till mappen, så att alla loggar som baseras på LSN-kedjan appliceras kontinuerligt tills tjänsten stoppas. |
| **2,1. övervaka åtgärds förloppet**. | -Förlopp för återställnings åtgärden kan övervakas med val av-eller-cmdlet: ar: <br /> PowerShell [Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_show-cmdlet: ar](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show). |
| **2,2. Stop\abort åtgärden vid behov**. | Om migreringsprocessen måste avbrytas kan åtgärden stoppas med ett val av cmdlet: ar: <br /> PowerShell [STOP-azsqlinstancedatabaselogreplay]/PowerShell/module/AZ.SQL/Stop-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) -cmdlet: ar. <br /><br />– Detta innebär att databasen som återställs på SQL-hanterad instans tas bort. <br />-En gång som har stoppats går det inte att återuppta LRS för en databas. Migreringsprocessen måste startas om från början. |
| **3. start punkt till molnet när det är klart**. | -Stoppa programmet och arbets belastningen. Ta den senaste säkerhets kopieringen av log-änden och ladda upp den till Azure Blob Storage.<br /> -Slutför start punkt genom att initiera åtgärden LRS Complete med ett val av cmdlet: ar: <br />PowerShell [Complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI- [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) -cmdlet: ar. <br /><br />– Detta gör att LRS-tjänsten stoppas och att databasen är online för Läs-och skriv användning på SQL-hanterad instans.<br /> -Peka kopplings strängen för programmet från SQL Server till SQL-hanterad instans. |

## <a name="requirements-for-getting-started"></a>Krav för att komma igång

### <a name="sql-server-side"></a>SQL Server sida
- SQL Server 2008-2019
- Fullständig säkerhets kopiering av databaser (en eller flera filer)
- Differentiell säkerhets kopiering (en eller flera filer)
- Logg säkerhets kopia (delas inte upp för transaktions logg filen)
- **Kontroll Summa måste vara aktive rad** för säkerhets kopieringar (obligatoriskt)

### <a name="azure-side"></a>Azure-sida
- PowerShell-AZ. SQL-modul version 2.16.0 eller högre ([Installera](https://www.powershellgallery.com/packages/Az.Sql/)eller använd Azure [Cloud Shell](/azure/cloud-shell/))
- CLI-version 2.19.0 eller senare ([Installera](/cli/azure/install-azure-cli))
- Azure Blob Storage-behållare etablerades
- SAS-säkerhetstoken med **Läs** -och **lista** endast behörigheter som skapats för Blob storage-behållaren

### <a name="migrating-multiple-databases"></a>Migrera flera databaser
- Säkerhetskopierade filer för olika databaser måste placeras i separata mappar på Azure Blob Storage.
- LRS måste startas separat för varje databas som pekar på en lämplig mapp på Azure Blob Storage.
- LRS har stöd för upp till 100 samtidiga återställnings processer per enskild SQL-hanterad instans.

### <a name="azure-rbac-permissions-required"></a>Azure RBAC-behörigheter krävs
Att köra LRS via de tillhandahållna klienterna kräver någon av följande Azure-roller:
- Prenumerations ägarens roll eller
- Rollen [hanterad instans deltagare](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) eller
- Anpassad roll med följande behörighet:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Bästa praxis

Följande rekommenderas för bästa praxis:
- Kör [Data Migration Assistant](/sql/dma/dma-overview) för att verifiera att dina databaser är redo att migreras till SQL-hanterad instans. 
- Dela fullständiga och differentiella säkerhets kopieringar i flera filer i stället för en enda fil.
- Aktivera komprimering av säkerhets kopiering.
- Använd Cloud Shell för att köra skript eftersom det alltid kommer att uppdateras till de senaste cmdletarna som släpps.
- Planera för att slutföra migreringen inom 47 timmar sedan LRS-tjänsten har startats. Detta är en Respitperiod som förhindrar systemhanterade program varu korrigeringar när LRS har startats.

> [!IMPORTANT]
> - Databasen som återställs med LRS kan inte användas förrän migreringen har slutförts.
> - Skrivskyddad åtkomst till databaser under migreringen stöds inte av LRS.
> - När migreringen har slutförts slutförs migreringsprocessen eftersom LRS inte stöder Restore Resume.

## <a name="steps-to-execute"></a>Steg att köra

### <a name="make-backups-on-the-sql-server"></a>Säkerhetskopiera SQL Server

Säkerhets kopieringar på SQL Server kan göras med något av följande två alternativ:

- Säkerhetskopiera till den lokala disk lagringen och ladda upp filer till Azure Blob Storage, om din miljö är begränsad till direkt säkerhets kopiering till Azure Blob Storage.
- Säkerhetskopiera direkt till Azure Blob Storage med "till URL"-alternativet i T-SQL, om din miljö och dina säkerhets metoder gör det möjligt att göra det. 

Ange databaser som du vill migrera till det fullständiga återställnings läget för att tillåta logg säkerhets kopieringar.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Om du vill göra full, diff och logg säkerhets kopiering av databasen manuellt i den lokala lagrings platsen använder du det exempel på T-SQL-skript som finns nedan. Se till att alternativet kontroll summa är aktiverat eftersom det är ett obligatoriskt krav för LRS.

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>Skapa Azure Blob Storage

Azure Blob Storage används som ett mellanliggande lagrings utrymme för säkerhets kopior mellan SQL Server-och SQL-hanterade instanser. Följ dessa steg om du vill skapa ett nytt lagrings konto och en BLOB-behållare i lagrings kontot:

1. [Skapa ett lagringskonto](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Crete en BLOB-behållare](../../storage/blobs/storage-quickstart-blobs-portal.md) i lagrings kontot

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>Kopiera säkerhets kopior från SQL Server till Azure Blob Storage

Några av följande metoder kan användas för att överföra säkerhets kopior till blob-lagringen i Migrera databaser till en hanterad instans med hjälp av LRS:
- Använda [AzCopy](/azure/storage/common/storage-use-azcopy-v10)eller [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer) för att överföra säkerhets kopior till en BLOB-behållare.
- Använda Storage Explorer i Azure Portal.

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>Gör säkerhets kopieringar från SQL Server direkt till Azure Blob Storage

Om din företags-och nätverks princip tillåter det, är det alternativa sättet att göra säkerhets kopior från SQL Server direkt till Azure Blob Storage med alternativet SQL Server inbyggd [säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Om du kan aktivera det här alternativet behöver du inte göra säkerhets kopior på den lokala lagringen och ladda upp dem till Azure Blob Storage.

Den här åtgärden kräver att SAS-autentiseringstoken genereras för Azure-Blob Storage och att token måste importeras till SQL Server för det första steget. Det andra steget är att säkerhetskopiera med alternativet "till URL" i T-SQL. Se till att alla säkerhets kopior görs med alternativet CHEKSUM aktiverat.

Exempel kod som gör säkerhets kopieringar till Azure Blob Storage anges nedan för referens. Det här exemplet innehåller inte instruktioner om hur du importerar SAS-token. Detaljerade anvisningar, inklusive hur du skapar och importerar SAS-token till SQL Server finns i följande självstudie: [Använd Azure Blob Storage-tjänsten med SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>Generera Azure Blob Storage SAS-autentisering för LRS

Azure Blob Storage används som ett mellanliggande lagrings utrymme för säkerhets kopior mellan SQL Server-och SQL-hanterade instanser. SAS-autentiseringstoken med list-och Läs behörighet måste genereras för användning av LRS-tjänsten. Detta gör att LRS-tjänsten kan komma åt Azure-Blob Storage och använda säkerhetskopieringsfilerna för att återställa dem på SQL-hanterad instans. Följ de här stegen för att generera SAS-autentisering för LRS-användning:

1. Få åtkomst till Storage Explorer från Azure Portal

2. Expandera BLOB-behållare

3. Högerklicka på BLOB-behållaren och välj Hämta signatur för delad åtkomst

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Logga repetitions tjänst – Hämta signatur för delad åtkomst":::

4. Välj tidsintervall för förfallo tid för token. Se till att token är giltig för migreringens varaktighet.

5. Välj tidszon för token-UTC eller lokal tid

   - Tids zonen för token och din SQL-hanterade instans kan ha fel matchnings fel. Se till att SAS-token har lämplig giltighets tid för tids zoner. Ange om möjligt tids zonen till en tidigare och senare tid för det planerade migrerings fönstret.

6. Välj läsa och lista endast behörigheter

   - Inga andra behörigheter måste väljas eller annars kommer LRS inte att kunna starta. Detta säkerhets krav är avsiktligt.

7. Klicka på knappen Skapa

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Logga repetitions tjänst – generera SAS-autentiseringstoken":::

   SAS-autentisering kommer att skapas med den giltighets tid som du har angett tidigare. Du behöver URI-versionen av den token som genereras – som visas på skärm bilden nedan.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Logg repetitions tjänst – kopiera signaturen för URI-delad åtkomst":::

### <a name="copy-parameters-from-sas-token-generated"></a>Kopiera parametrar från SAS-token som genererats

För att kunna använda SAS-token korrekt för att starta LRS måste vi förstå dess struktur. URI: n för den SAS-token som genereras består av två delar:
- StorageContainerUri och 
- StorageContainerSasToken, åtskiljt med ett frågetecken (?) som visas på bilden nedan.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Generera SAS-autentiserings-URI-exempel för logg uppspelning" border="false":::

- Den första delen börjar med "https://" tills frågetecknet (?) används för parametern StorageContainerURI som matas in som inmatad till LRS. Detta ger LRS information om mappen där säkerhetskopierade databasfiler lagras.
- Den andra delen, som börjar efter frågetecknet (?), i exemplet "SP =" och hela vägen till slutet av strängen är StorageContainerSasToken-parameter. Detta är den faktiska signerade autentiseringstoken som är giltig under den angivna tids perioden. Den här delen behöver inte nödvändigt vis börja med "SP =" som det visas och att ditt ärende kan skilja sig.

Kopiera parametrar enligt följande:

1. Kopiera den första delen av token från https://hela vägen tills frågetecknet (?) och Använd den som StorageContainerUri-parameter i PowerShell eller CLI för att starta LRS, som visas på skärm bilden nedan.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Logga StorageContainerUri-parameter för logg uppspelnings tjänsten":::

2. Kopiera den andra delen av token från frågetecknet (?), hela vägen till slutet av strängen och Använd den som StorageContainerSasToken-parameter i PowerShell eller CLI för att starta LRS, som visas på skärm bilden nedan.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Logga StorageContainerSasToken-parameter för logg uppspelnings tjänsten":::

> [!IMPORTANT]
> - Behörigheter för SAS-token för Azure Blob Storage behöver läsas och lista. Om andra behörigheter beviljas för SAS-autentiseringstoken, kommer starten av LRS-tjänsten inte att fungera. Dessa säkerhets krav är avsiktliga.
> - Token måste ha lämplig giltighets tid. Se till att tids zonerna mellan token och den hanterade instansen beaktas.
> - Se till att StorageContainerUri-parametern för PowerShell eller CLI kopieras från URI: n för den genererade token, med början från https://tills frågorna märks (?). Lägg inte till frågetecknet.
> Se till att StorageContainerSasToken-parametern för PowerShell för CLI kopieras från URI: n för den genererade token, med början från frågetecknet (?) till slutet av strängen. Lägg inte till frågetecknet.

### <a name="log-in-to-azure-and-select-subscription"></a>Logga in på Azure och välj prenumeration

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

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Starta LRS i läget komplettera automatiskt – CLI-exempel:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Starta LRS i kontinuerligt läge

Starta LRS i kontinuerligt läge – PowerShell-exempel:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Starta LRS i kontinuerligt läge – CLI-exempel:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>Skript LRS startar i kontinuerligt läge

PowerShell-och CLI-klienter för att starta LRS i kontinuerligt läge är synkrona. Det innebär att klienterna väntar på API-svar för att rapportera om det lyckas eller om det inte går att starta jobbet. Under den här tiden returnerar kommandot inte kontrollen tillbaka till kommando tolken. Om du använder skript för migreringen och behöver kommandot LRS start för att göra kontrollen tillbaka omedelbart för att fortsätta med resten av skriptet kan du köra PowerShell som bakgrunds jobb med-AsJob-växel. Exempel:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

När du startar ett bakgrunds jobb returnerar ett Job-objekt omedelbart, även om jobbet tar lång tid att slutföra. Du kan fortsätta att arbeta i sessionen utan avbrott medan jobbet körs. Mer information om hur du kör PowerShell som ett bakgrunds jobb finns i [PowerShell start-Job-](/powershell/module/microsoft.powershell.core/start-job#description) dokumentationen.

På samma sätt kan du starta ett CLI-kommando på Linux som bakgrunds process genom att använda et-tecknet (&) i slutet av Start kommandot LRS.

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> När LRS har startats kommer alla systemhanterade program varu korrigeringar att stoppas under de kommande 47 timmarna. Vid överföring av det här fönstret stoppar nästa automatiska program varu korrigering automatiskt den pågående LRS. I sådana fall kan migreringen inte återupptas och måste startas om från början. 

## <a name="monitor-the-migration-progress"></a>Övervaka förloppet för migreringen

Använd följande PowerShell-kommando för att övervaka återställnings processen för migrering:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Använd följande CLI-kommando om du vill övervaka återställnings processen för migrering:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Stoppa migreringen

Om du behöver stoppa migreringen använder du följande cmdletar. Om du stoppar migreringen tas återställnings databasen på SQL-hanterad instans bort på grund av att det inte går att återuppta migreringen.

Använd följande PowerShell-kommando om du vill stop\abort migreringsprocessen:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Om du vill stop\abort migreringsprocessen använder du följande CLI-kommando:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Slutför migreringen (kontinuerligt läge)

Om LRS startas i kontinuerligt läge när du har kontrollerat att alla säkerhets kopior har återställts, kommer initieringen av start punkt att slutföra migreringen. När Start punkt har slutförts migreras databasen och är klar för Läs-och skriv åtkomst.

För att slutföra migreringsprocessen i LRS kontinuerligt läge, använder du följande PowerShell-kommando:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

För att slutföra migreringsprocessen i LRS kontinuerligt läge, använder du följande CLI-kommando:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Funktionella begränsningar

Funktionella begränsningar för logg uppspelnings tjänsten (LRS) är:
- Databasen som återställs kan inte användas för skrivskyddad åtkomst under migreringsprocessen.
- Systemhanterade program uppdateringar kommer att blockeras i 47 timmar sedan start av LRS. När den här tids perioden har löpt ut kommer nästa program varu uppdatering att stoppa LRS. I sådana fall måste LRS startas om från början.
- LRS kräver att databaser på SQL Server ska säkerhets kopie ras med alternativet kontroll Summa aktiverat.
- SAS-token som används av LRS måste genereras för hela Azure Blob Storage-behållaren och måste ha läs-och list behörigheter.
- Säkerhetskopierade filer för olika databaser måste placeras i separata mappar på Azure Blob Storage.
- LRS måste startas separat för varje databas som pekar på separata mappar med säkerhetskopierade filer på Azure Blob Storage.
- LRS har stöd för upp till 100 samtidiga återställnings processer per enskild SQL-hanterad instans.

## <a name="troubleshooting"></a>Felsökning

När du har startat LRS använder du övervaknings-cmdletarna (Get-azsqlinstancedatabaselogreplay eller az_sql_midb_log_replay_show) för att se status för åtgärden. Om några av de vanligaste problemen efter en viss tid LRS Miss lyckas med ett fel kan du söka efter några av de vanligaste problemen:
- Finns det redan en databas med samma namn på SQL MI som du försöker migrera från SQL Server? Lös konflikten genom att byta namn på en av databaserna.
- Har säkerhets kopian av databasen SQL Server gjorts med hjälp av alternativet för **kontroll Summa** ?
- Är behörigheterna för SAS-token **Read** **och endast** för LRS-tjänsten?
- Har SAS-token för LRS kopierats från och med frågetecknet "?" med innehåll som börjar på liknande sätt som "sa = 2020-02-10..."? 
- Är den giltighets tid för SAS- **token** som kan användas för tids perioden för att starta och slutföra migreringen? Det kan vara fel matchningar på grund av olika **tids zoner** som används för SQL-hanterad instans och SAS-token. Försök att återskapa SAS-token med att förlänga giltighets tiden för tids perioden före och efter det aktuella datumet.
- Är databas namnet, resurs gruppens namn och namnet på den hanterade instansen rätt stavat?
- Om LRS startades i Autoavsluta-läge, var ett giltigt fil namn för den senaste säkerhets kopierings filen som angetts?

## <a name="next-steps"></a>Nästa steg
- Läs mer om [att migrera SQL Server till SQL-hanterad instans](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Läs mer om [skillnader mellan SQL Server och Azure SQL-hanterad instans](transact-sql-tsql-differences-sql-server.md).
- Lär dig mer om [metod tips för kostnads-och storleks arbets belastningar som migrerats till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
