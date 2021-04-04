---
title: Flytta data till en SQL Server virtuell dator – team data science process
description: Flytta data från flata filer eller från lokala SQL Server till SQL Server på den virtuella Azure-datorn.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c80a90b07e25942e751d52cafa47f6e3e94852ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320343"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Flytta data till SQL Server på en virtuell Azure-dator

Den här artikeln beskriver alternativen för att flytta data antingen från flata filer (CSV-eller TSV-format) eller från en lokal SQL Server till SQL Server på en virtuell Azure-dator. Dessa uppgifter för att flytta data till molnet ingår i team data vetenskaps processen.

Ett avsnitt som beskriver alternativ för att flytta data till en Azure SQL Database för Machine Learning finns i [Flytta data till en Azure SQL Database för Azure Machine Learning](move-sql-azure.md).

I följande tabell sammanfattas alternativen för att flytta data till SQL Server på en virtuell Azure-dator.

| <b>KÄLLICENSSERVERN</b> | <b>MÅL: SQL Server på virtuell Azure-dator</b> |
| --- | --- |
| <b>Flat fil</b> |1. <a href="#insert-tables-bcp">kommando rads verktyget Mass kopiering (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Mass infogning av SQL-fråga </a><br> 3. <a href="#sql-builtin-utilities">grafiska inbyggda verktyg i SQL Server</a> |
| <b>Lokala SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">distribuera en SQL Server-databas till en Microsoft Azure-guide för virtuella datorer</a><br> 2. <a href="#export-flat-file">Exportera till en platt fil </a><br> 3. <a href="#sql-migration">guiden SQL Database migrering </a> <br> 4. <a href="#sql-backup">säkerhetskopiera och återställa databasen </a><br> |

Det här dokumentet förutsätter att SQL-kommandon körs från SQL Server Management Studio eller Visual Studio Databasutforskare.

> [!TIP]
> Alternativt kan du använda [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) för att skapa och schemalägga en pipeline som kommer att flytta data till en SQL Server VM på Azure. Mer information finns i [Kopiera data med Azure Data Factory (kopierings aktivitet)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Förutsättningar
Den här självstudien förutsätter att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* Ett **Azure Storage-konto**. Du använder ett Azure Storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure Storage-konto går du till artikeln [skapa ett lagrings konto](../../storage/common/storage-account-create.md) . När du har skapat lagrings kontot måste du skaffa den konto nyckel som används för att få åtkomst till lagringen. Se [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
* Etablerade **SQL Server på en virtuell Azure-dator**. Instruktioner finns i [Konfigurera en virtuell Azure SQL Server-dator som en IPython Notebook-Server för avancerad analys](../data-science-virtual-machine/overview.md).
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a> Flytta data från en platt fil källa till SQL Server på en virtuell Azure-dator
Om dina data finns i en platt fil (ordnade i ett rad-eller kolumn format) kan du flytta dem till SQL Server VM på Azure via följande metoder:

1. [Kommando rads verktyget för Mass kopiering (BCP)](#insert-tables-bcp)
2. [Mass infogning av SQL-fråga](#insert-tables-bulkquery)
3. [Grafiska inbyggda verktyg i SQL Server (import/export, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Kommando rads verktyget för Mass kopiering (BCP)
BCP är ett kommando rads verktyg som installeras med SQL Server och är ett av de snabbaste sätten att flytta data. Den fungerar på alla tre SQL Server varianter (lokala SQL Server, SQL Azure och SQL Server VM på Azure).

> [!NOTE]
> **Var ska mina data vara för BCP?**  
> Även om det inte krävs, har filer som innehåller källdata som finns på samma dator som mål SQL Server möjliggör snabbare överföringar (nätverks hastighet jämfört med den lokala diskens IO-hastighet). Du kan flytta de flata filerna som innehåller data till den dator där SQL Server installeras med olika fil kopierings verktyg som [AzCopy](../../storage/common/storage-use-azcopy-v10.md), [Azure Storage Explorer](https://storageexplorer.com/) eller Windows Copy/klistra in via Remote Desktop Protocol (RDP).
>
>

1. Se till att databasen och tabellerna skapas på mål SQL Servers databasen. Här är ett exempel på hur du gör med `Create Database` `Create Table` kommandona och:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Generera format filen som beskriver schemat för tabellen genom att utfärda följande kommando från kommando raden på den dator där BCP är installerat.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Infoga data i databasen med kommandot BCP, som ska fungera från kommando raden när SQL Server installeras på samma dator:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Optimera BCP-infogningar** Mer information om hur du optimerar sådana infogningar finns i följande artikel [rikt linjer för att optimera Mass import](/previous-versions/sql/sql-server-2008-r2/ms177445(v=sql.105)) .
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>Parallella infogningar för snabbare data förflyttning
Om de data som du flyttar är stora kan du påskynda det genom att samtidigt köra flera BCP-kommandon parallellt i ett PowerShell-skript.

> [!NOTE]
> **Big data inmatning** För att optimera data inläsningen för stora och väldigt stora data uppsättningar, partitionerar du dina logiska och fysiska databas tabeller med flera fil grupper och partitionstabeller. Mer information om hur du skapar och läser in data för att partitionera tabeller finns i [SQL partition tables Parallel load](parallel-load-sql-partitioned-tables.md).
>
>

Följande exempel på PowerShell-skript visar parallella infogningar med BCP:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>Mass infogning av SQL-fråga
[Mass infogning av SQL-fråga](/sql/t-sql/statements/bulk-insert-transact-sql) kan användas för att importera data till databasen från rad/kolumnbaserade filer (de typer som stöds finns i avsnittet[förbereda data för Mass export eller import (SQL Server)](/sql/relational-databases/import-export/prepare-data-for-bulk-export-or-import-sql-server)).

Här följer några exempel kommandon för Mass infogning:  

1. Analysera dina data och ange anpassade alternativ innan du importerar för att säkerställa att SQL Server-databasen antar samma format för särskilda fält som datum. Här är ett exempel på hur du ställer in datum formatet som år-månad-dag (om dina data innehåller datumet i formatet år-månad-dag):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Importera data med Mass import instruktioner:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Inbyggda verktyg i SQL Server
Du kan använda SQL Server Integration Services (SSIS) för att importera data till SQL Server VM på Azure från en platt fil.
SSIS finns i två Studio miljöer. Mer information finns i avsnittet om [integrerings tjänster (SSIS) och Studio miljöer](/sql/integration-services/integration-services-ssis-development-and-management-tools):

* Mer information om SQL Server Data Tools finns i [Microsoft SQL Server data verktyg](/sql/ssdt/download-sql-server-data-tools-ssdt)  
* Mer information om guiden Importera/exportera finns i [SQL Server import-och export guiden](/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Flytta data från lokala SQL Server till SQL Server på en virtuell Azure-dator
Du kan också använda följande migrerings strategier:

1. [Distribuera en SQL Server-databas till en Microsoft Azure VM-guide](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportera till flat fil](#export-flat-file)
3. [Guiden SQL Database migrering](#sql-migration)
4. [Säkerhetskopiera och återställa databas](#sql-backup)

Vi beskriver vart och ett av följande alternativ:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Distribuera en SQL Server-databas till en Microsoft Azure VM-guide
Att **distribuera en SQL Server databas till en Microsoft Azure VM-guide** är ett enkelt och rekommenderat sätt att flytta data från en lokal SQL Server-instans till SQL Server på en virtuell Azure-dator. Detaljerade anvisningar och en beskrivning av andra alternativ finns i [Migrera en databas till SQL Server på en virtuell Azure-dator](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md).

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportera till flat fil
Du kan använda olika metoder för att massredigera data från en lokal SQL Server som dokumenteras i avsnittet [Mass import och export av data (SQL Server)](/sql/relational-databases/import-export/bulk-import-and-export-of-data-sql-server) . Det här dokumentet kommer att gälla för Mass kopierings programmet (BCP) som exempel. När data har exporter ATS till en platt fil kan den importeras till en annan SQL Server med Mass import.

1. Exportera data från lokala SQL Server till en fil med hjälp av BCP-verktyget enligt följande

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Skapa databasen och tabellen på SQL Server VM på Azure med hjälp av `create database` och `create table` för tabell schemat som exporterades i steg 1.
3. Skapa en format fil för att beskriva tabell schemat för de data som exporteras/importeras. Information om format filen beskrivs i [skapa en format fil (SQL Server)](/sql/relational-databases/import-export/create-a-format-file-sql-server).

    Formatera fil generering när BCP körs från SQL Server datorn

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`

    Formatera fil generering när BCP fjärrkörs mot en SQL Server

    `bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n`
4. Använd någon av de metoder som beskrivs i avsnittet [Flytta data från fil källan](#filesource_to_sqlonazurevm) för att flytta data i flata filer till en SQL Server.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>Guiden SQL Database migrering
Med guiden för att [migrera SQL Server Database](https://sqlazuremw.codeplex.com/) kan du använda ett användarvänligt sätt för att flytta data mellan två SQL Server-instanser. Det gör att användaren kan mappa data schemat mellan källor och mål tabeller, välja kolumn typer och andra funktioner. Den använder Mass kopiering (BCP) under försättsblad. En skärm bild av välkomst skärmen för guiden SQL Database migrering visas nedan.  

![Guiden SQL Server migrering][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Säkerhetskopiera och återställa databas
SQL Server stöder:

1. [Databas säkerhets kopierings-och återställnings funktioner](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) (både till en lokal fil eller BACPAC exporteras till BLOB) och [data skikts program](/sql/relational-databases/data-tier-applications/data-tier-applications) (med BACPAC).
2. Möjlighet att direkt skapa SQL Server virtuella datorer på Azure med en kopierad databas eller kopiera till en befintlig databas i SQL Database. Mer information finns i [använda guiden Kopiera databas](/sql/relational-databases/databases/use-the-copy-database-wizard).

En skärm bild av alternativen för databas säkerhets kopiering/återställning från SQL Server Management Studio visas nedan.

![SQL Server import verktyg][1]

## <a name="resources"></a>Resurser
[Migrera en databas till SQL Server på en virtuell Azure-dator](../../azure-sql/virtual-machines/windows/migrate-to-vm-from-sql-server.md)

[Översikt över SQL Server på Azure Virtual Machines](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png