---
title: Säkerhetskopiera och återställa databaser – Azure SQL Edge
description: Lär dig mer om säkerhets kopierings-och återställnings funktioner i Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 114be810ea50f984c3211291691b4c4dd45ac2c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395248"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>Säkerhetskopiera och återställa databaser i Azure SQL Edge 

Azure SQL Edge bygger på de senaste versionerna av Microsoft SQL Database-motorn. Den ger liknande säkerhets kopierings-och återställnings databas funktioner som är tillgängliga i SQL Server på Linux och SQL Server som körs i behållare. Säkerhets kopierings-och återställnings komponenten är ett viktigt skydd för att skydda data som lagras i dina Azure SQL Edge-databaser. 

För att minimera risken för katastrof data förlust bör du säkerhetskopiera databaserna regelbundet för att bevara ändringar i dina data regelbundet. Med en välplanerad strategi för säkerhetskopiering och återställning kan du skydda databaser mot dataförlust på grund av en mängd olika fel. Testa strategin genom att återställa en uppsättning säkerhets kopior och sedan återställa databasen så att du kan förbereda dig för att effektivt svara på en katastrof.

Om du vill läsa mer om varför säkerhets kopieringarna är viktiga, se [säkerhets kopiering och återställning av SQL Server databaser](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/).

Med Azure SQL Edge kan du säkerhetskopiera till och återställa från både lokal lagring och Azure-blobbar. Mer information finns i [SQL Server säkerhets kopiering och återställning med Azure Blob Storage](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/) och [SQL Server säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

## <a name="back-up-a-database-in-azure-sql-edge"></a>Säkerhetskopiera en databas i Azure SQL Edge

Azure SQL Edge stöder samma säkerhets kopierings typer som SQL Server. En fullständig lista finns i [Översikt över säkerhets kopiering](/sql/relational-databases/backup-restore/backup-overview-sql-server/).

> [!IMPORTANT] 
> Databaser som skapats i Azure SQL Edge använder enkel återställnings modell som standard. Därför kan du inte utföra säkerhets kopior av loggen på dessa databaser. Om du behöver göra detta behöver du en administratör för att ändra databas återställnings modellen till den fullständiga återställnings modellen. En fullständig lista över återställnings modeller som stöds av SQL Server finns i [Översikt över återställnings modell](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov).

### <a name="back-up-to-local-disk"></a>Säkerhetskopiera till en lokal disk

I följande exempel använder du `BACKUP DATABASE` Transact-SQL-kommandot för att skapa en databas säkerhets kopia i behållaren. I det här exemplet skapar du en ny mapp med namnet *Backup* för att lagra säkerhetskopieringsfilerna.

1. Skapa en mapp för säkerhets kopiorna. Kör det här kommandot på den värd där din Azure SQL Edge-behållare körs. I följande kommando ersätter du **<AzureSQLEdge_Container_Name>** med namnet på Azure SQL Edge-behållaren i distributionen.

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. Anslut till Azure SQL Edge-instansen med hjälp av SQL Server Management Studio (SSMS) eller genom att använda Azure Data Studio. Kör `BACKUP DATABASE` kommandot för att göra en säkerhets kopia av din användar databas. I följande exempel tar du säkerhets kopian av *IronOreSilicaPrediction* -databasen.

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. När du har kört kommandot, och om säkerhets kopian av databasen lyckas, visas meddelanden som liknar följande i avsnittet resultat i SSMS eller Azure Data Studio.

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="back-up-to-url"></a>Säkerhetskopiera till URL

Azure SQL Edge stöder säkerhets kopiering till både sid-blobar och block-blobar. Mer information finns i [säkerhetskopiera för att blockera BLOB vs Page BLOB](/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob). I följande exempel säkerhets kopie ras databasen *IronOreSilicaPrediction* till en Block-Blob. 

1. Om du vill konfigurera säkerhets kopieringar för att blockera blobbar måste du först skapa en signatur för signatur för delad åtkomst (SAS) som du kan använda för att skapa en SQL Server autentiseringsuppgift på Azure SQL Edge. Skriptet skapar en SAS som är associerad med en lagrad åtkomst princip. Mer information finns i [signaturer för delad åtkomst, del 1: förstå SAS-modellen](../storage/common/storage-sas-overview.md). Skriptet skriver också det T-SQL-kommando som krävs för att skapa autentiseringsuppgiften på SQL Server. Följande skript förutsätter att du redan har en Azure-prenumeration med ett lagrings konto och en lagrings behållare för säkerhets kopiorna.

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    När skriptet har körts kopierar du `CREATE CREDENTIAL` kommandot till ett frågefönster. Anslut sedan till en instans av SQL Server och kör kommandot för att skapa autentiseringsuppgiften med SAS.

2. Anslut till Azure SQL Edge-instansen med hjälp av SSMS eller Azure Data Studio och skapa sedan autentiseringsuppgiften med hjälp av kommandot från föregående steg. Se till att ersätta `CREATE CREDENTIAL` kommandot med det faktiska resultatet från föregående steg.

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. Följande kommando tar en säkerhets kopia av *IronOreSilicaPrediction* till Azure Storage-behållaren.

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restore-a-database-in-azure-sql-edge"></a>Återställa en databas i Azure SQL Edge

I Azure SQL Edge kan du återställa från en lokal disk, en nätverks plats eller ett Azure Blob Storage-konto. Mer information om återställning och återställning i SQL Server finns i [Översikt över återställning och återställning](/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server). En översikt över den enkla återställnings modellen i SQL Server finns i [fullständig databas återställning (enkel återställnings modell)](/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model).

> [!IMPORTANT] 
> Databaser som har skapats i Azure SQL Edge kan inte återställas på en instans av Microsoft SQL Server eller Azure SQL. Dessutom kan en databas som skapas på Microsoft SQL Server eller Azure SQL återställas på Azure SQL Edge, förutsatt att databasen inte innehåller några av de funktioner som inte stöds av Azure SQL Edge. 

### <a name="restore-from-a-local-disk"></a>Återställa från en lokal disk

I det här exemplet används den *IronOreSilicaPrediction* -säkerhetskopiering som du gjorde i föregående exempel. Nu ska du återställa den som en ny databas med ett annat namn.

1. Om säkerhets kopian av databasen inte redan finns i behållaren, kan du använda följande kommando för att kopiera filen till behållaren. I följande exempel förutsätts att säkerhets kopian finns på den lokala värden och kopieras till mappen/var/opt/MSSQL/backup i en Azure SQL Edge-behållare med namnet *SQL1*.

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. Anslut till SQL Edge-instansen i Azure med hjälp av SSMS eller Azure Data Studio för att köra kommandot Restore. I följande exempel återställs **IronOrePredictDB. bak** för att skapa en ny databas, **IronOreSilicaPrediction_2**.

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. När du har kört kommandot Restore, och om återställningen lyckades, visas meddelanden som liknar följande i fönstret utdata. 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>Återställ från URL

Azure SQL Edge stöder också återställning av en databas från ett Azure Storage-konto. Du kan återställa från antingen block-blobbar eller säkerhets kopior av Page blob. I följande exempel återställs säkerhets kopian av databasen *IronOreSilicaPrediction_2020_04_16. bak* -databasen på en block-BLOB för att skapa databasen *IronOreSilicaPrediction_3*.

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```