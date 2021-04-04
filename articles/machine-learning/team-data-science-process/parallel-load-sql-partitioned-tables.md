---
title: Parallell Mass import av data i SQL partition-tabeller – team data science process
description: Bygg partitionerade tabeller för snabb parallell Mass import av data till en SQL Server databas.
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
ms.openlocfilehash: 456e881d84697f4542f972ac0798cc95a3455b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322411"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Bygg och optimera tabeller för snabb parallell import av data till en SQL Server på en virtuell Azure-dator

Den här artikeln beskriver hur du skapar partitionerade tabeller för snabb parallell Mass import av data till en SQL Server databas. För stor data inläsning/överföring till en SQL-databas kan du importera data till SQL-databasen och efterföljande frågor kan förbättras med hjälp av *partitionerade tabeller och vyer*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Skapa en ny databas och en uppsättning fil grupper
* [Skapa en ny databas](/sql/t-sql/statements/create-database-transact-sql), om den inte redan finns.
* Lägg till databas fil grupper i databasen, som innehåller de partitionerade fysiska filerna. 
* Detta kan göras med [skapa databas](/sql/t-sql/statements/create-database-transact-sql) om det redan finns en ny databas eller [Alter Database](/sql/t-sql/statements/alter-database-transact-sql-set-options) .
* Lägg till en eller flera filer (efter behov) i varje databas fil grupp.
  
  > [!NOTE]
  > Ange mål fil gruppen som innehåller data för den här partitionen och de fysiska databasens fil namn (n) där filgrupps data lagras.
  > 
  > 

I följande exempel skapas en ny databas med tre fil grupper förutom den primära databasen och logg grupper som innehåller en fysisk fil i varje. Databasfilerna skapas i standardSQL Server-datamappen som kon figurer ATS i SQL Server-instansen. Mer information om standard Sök vägarna finns i [fil platser för standard-och namngivna instanser av SQL Server](/sql/sql-server/install/file-locations-for-default-and-named-instances-of-sql-server).

```sql
   DECLARE @data_path nvarchar(256);
   SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

   EXECUTE ('
      CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
```

## <a name="create-a-partitioned-table"></a>Skapa en partitionerad tabell
Om du vill skapa partitionerade tabeller enligt data schemat, som mappas till databas fil grupper som skapats i föregående steg, måste du först skapa en partitions funktion och ett schema. När data Mass importer ATS till partitionerade tabeller, distribueras posterna bland fil grupperna enligt ett partitionsschema, enligt beskrivningen nedan.

### <a name="1-create-a-partition-function"></a>1. skapa en partitions funktion
[Skapa en partitions funktion](/sql/t-sql/statements/create-partition-function-transact-sql) Den här funktionen definierar det värde/gränser som ska inkluderas i varje enskild partitionstabell, till exempel för att begränsa partitioner efter månad (vissa \_ datetime \_ -fält) under året 2013:
  
```sql
   CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
      AS RANGE RIGHT FOR VALUES (
         '20130201', '20130301', '20130401',
         '20130501', '20130601', '20130701', '20130801',
         '20130901', '20131001', '20131101', '20131201' )
```

### <a name="2-create-a-partition-scheme"></a>2. skapa ett partitionsschema
[Skapa ett partitionsschema](/sql/t-sql/statements/create-partition-scheme-transact-sql). Det här schemat mappar varje partitions intervall i partitionsfunktionen till en fysisk filgrupp, till exempel:
  
```sql
      CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
```
 
Kör följande fråga för att kontrol lera de intervall som används i varje partition enligt funktion/schema:
  
```sql
   SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
   FROM sys.partition_functions AS pfun
   INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
   INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
   WHERE pfun.name = <DatetimeFieldPFN>
```

### <a name="3-create-a-partition-table"></a>3. skapa en partitionstabell
[Skapa partitionerade tabeller](/sql/t-sql/statements/create-table-transact-sql)enligt ditt data schema och ange det partitionsschema och det begränsnings fält som används för att partitionera tabellen, till exempel:
  
```sql
   CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)
```

Mer information finns i [skapa partitionerade tabeller och index](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Mass import av data för varje enskild partitionstabell

* Du kan använda BCP, BULK INSERT eller andra metoder som [SQL Server migreringsguiden](https://sqlazuremw.codeplex.com/). Det tillhandahållna exemplet använder BCP-metoden.
* Ändra [databasen](/sql/t-sql/statements/alter-database-transact-sql-set-options) för att ändra transaktions loggnings schema till BULK_LOGGED för att minimera kostnaderna för loggning, till exempel:
  
   ```sql
      ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
   ```
* Starta Mass import åtgärderna parallellt för att påskynda inläsningen av data. Tips om hur du påskyndar Mass import av Big data i SQL Server-databaser finns [i load 1 TB på mindre än 1 timme](/archive/blogs/sqlcat/load-1tb-in-less-than-1-hour).

Följande PowerShell-skript är ett exempel på en parallell data inläsning med BCP.

```powershell
    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date
```

## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Skapa index för att optimera kopplingar och fråga prestanda
* Om du extraherar data för modellering från flera tabeller skapar du index på anslutnings nycklarna för att förbättra kopplingens prestanda.
* [Skapa index](/sql/t-sql/statements/create-index-transact-sql) (klustrade eller icke-klustrade) för att rikta in samma filgrupp för varje partition, till exempel:
  
```sql
   CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
--  or,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
 ```
 
  > [!NOTE]
  > Du kan välja att skapa index före Mass import av data. Skapandet av index gör att data inläsningen saktas ned.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Avancerad analys process och teknik i åtgärds exempel
För en fullständig genom gång av exempel som använder team data science-processen med en offentlig data uppsättning, se [team data science process i praktiken: använda SQL Server](sql-walkthrough.md).