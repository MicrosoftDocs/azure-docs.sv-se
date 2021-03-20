---
title: Utveckla och distribuera en modell med Azure Synapse Analytics – team data science process
description: Bygg och distribuera en maskin inlärnings modell med Azure Synapse Analytics med en offentligt tillgänglig data uppsättning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b638cb2b33f24220e7ceb852402862c707cc7bc6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93316008"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Team data science-processen i praktiken: använda Azure Synapse Analytics
I den här självstudien vägleder vi dig genom att skapa och distribuera en maskin inlärnings modell med Azure Synapse Analytics för en offentligt tillgänglig data uppsättning – [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/) -datauppsättningen. Den binära klassificerings modellen är konstruerad för att förutsäga om ett tips är betalt för en resa.  I modeller ingår klassificering av multiklass (oavsett om det finns ett tips) och regression (fördelningen för de belopp som betalas).

Proceduren följer TDSP-arbetsflödet [(Team data science process)](./index.yml) . Vi visar hur du konfigurerar en data vetenskaps miljö, hur du läser in data i Azure Synapse Analytics och hur du använder Azure Synapse Analytics eller en IPython-anteckningsbok för att utforska data-och ingenjörs funktionerna för att modellera. Sedan visar vi hur du skapar och distribuerar en modell med Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>NYC taxi TRIPs-datauppsättning
NYC taxi-resan består av cirka 20 GB komprimerade CSV-filer (~ 48 GB okomprimerat), och registrerar mer än 173 000 000 enskilda resor och de priser som betalas för varje resa. Varje rese post omfattar hämtnings-och DropOff platser och tidpunkter, anonymiserats Hack (driv rutins licens nummer) och numret på Medallion (Taxins unika ID). Data omfattar alla resor under året 2013 och tillhandahålls i följande två data uppsättningar för varje månad:

1. **trip_data.csv** -filen innehåller information om resan, till exempel antal passagerare, upphämtnings-och DropOff punkter, varaktighet för resan och rese längd. Här följer några exempel poster:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. **trip_fare.csv** -filen innehåller information om avgiften som har betalats för varje resa, till exempel betalnings typ, avgifts belopp, tillägg och skatter, tips och avgifter, samt totalt betalat belopp. Här följer några exempel poster:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Den **unika nyckel** som används för att ansluta rese \_ data och rese \_ pris består av följande tre fält:

* medallion,
* hacka \_ licens och
* upphämtnings- \_ DateTime.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Adressera tre typer av förutsägelse aktiviteter
Vi formulerar tre förutsägelse problem baserat på *tips \_ mängden* för att illustrera tre typer av modellerings aktiviteter:

1. **Binära klassificering**: för att förutsäga om ett tips har betalats för en resa, det vill säga ett *Tip- \_ belopp* som är större än $0 är ett positivt exempel, medan ett *Tip- \_ värde* på $0 är ett negativt exempel.
2. **Klassificering** av flera klasser: för att förutsäga det tips som du betalar för resan. Vi delar upp *Tip- \_ beloppet* i fem lager platser eller klasser:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Regressions uppgift**: för att förutsäga hur mycket tips du betalar för en resa.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurera Azure Data Science-miljön för avancerad analys
Följ dessa steg om du vill konfigurera din Azure Data Science-miljö.

**Skapa ditt eget Azure Blob Storage-konto**

* När du etablerar en egen Azure Blob-lagring väljer du en Geo-plats för Azure Blob Storage i eller så nära Central USA som möjligt till **södra centrala USA**, som är den plats där NYC taxi-data lagras. Data kommer att kopieras med AzCopy från den offentliga Blob storage-behållaren till en behållare i ditt eget lagrings konto. Ju närmare din Azure Blob-lagring är till södra centrala USA, desto snabbare kommer uppgiften (steg 4) att utföras.
* Om du vill skapa ett eget Azure Storage konto följer du stegen som beskrivs i [om Azure Storage-konton](../../storage/common/storage-account-create.md). Se till att anteckna värdena för följande autentiseringsuppgifter för lagrings kontot eftersom de kommer att behövas senare i den här genom gången.

  * **Lagrings konto namn**
  * **Lagrings konto nyckel**
  * **Behållar namn** (som du vill att data ska lagras i Azure Blob Storage)

**Etablera Azure Synapse Analytics-instansen.**
Följ dokumentationen i [create och fråga en Azure Synapse-analys i Azure Portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) för att etablera en Azure Synapse Analytics-instans. Se till att du gör ett format på följande autentiseringsuppgifter för Azure Synapse Analytics som ska användas i senare steg.

* **Server namn**: \<server Name> . Database.Windows.net
* **Namn på SQLDW (databas)**
* **Användarnamn**
* **Lösenord**

**Installera Visual Studio och SQL Server Data Tools.** Instruktioner finns i [komma igång med Visual Studio 2019 för Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Anslut till din Azure Synapse Analytics med Visual Studio.** Anvisningar finns i steg 1 & 2 i [Anslut till SQL Analytics i Azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Kör följande SQL-fråga på databasen som du skapade i din Azure Synapse Analytics (i stället för den fråga som anges i steg 3 i avsnittet Anslut) för att **skapa en huvud nyckel**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Skapa en Azure Machine Learning arbets yta under din Azure-prenumeration.** Instruktioner finns i [skapa en Azure Machine Learning-arbetsyta](../classic/create-workspace.md).

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Läs in data i Azure Synapse Analytics
Öppna en kommando konsol för Windows PowerShell. Kör följande PowerShell-kommandon för att ladda ned exempel-SQL-skriptfilerna som vi delar med dig på GitHub till en lokal katalog som du anger med parametern *-DestDir*. Du kan ändra värdet för parametern *-DestDir* till valfri lokal katalog. IF *-DestDir* inte finns kommer att skapas av PowerShell-skriptet.

> [!NOTE]
> Du kan behöva **köra som administratör** när du kör följande PowerShell-skript om din *DestDir* -katalog behöver administratörs behörighet för att skapa eller skriva till den.
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

Efter lyckad körning ändras din aktuella arbets katalog till *-DestDir*. Du bör kunna se skärmen som nedan:

![Aktuella ändringar i arbets katalogen][19]

I *DestDir* kör du följande PowerShell-skript i administratörs läge:

```azurepowershell
./SQLDW_Data_Import.ps1
```

När PowerShell-skriptet körs för första gången uppmanas du att ange informationen från din Azure Synapse-analys och ditt Azure Blob Storage-konto. När det här PowerShell-skriptet är klart för första gången, kommer autentiseringsuppgifterna som du har angett att skrivas till en konfigurations fil SQLDW. conf i den aktuella arbets katalogen. Den framtida körningen av denna PowerShell-skriptfil har alternativet att läsa alla nödvändiga parametrar från den här konfigurations filen. Om du behöver ändra vissa parametrar kan du välja att ange parametrarna på skärmen vid uppmaning genom att ta bort den här konfigurations filen och mata in parameter värden som en uppmaning eller ändra parametervärdena genom att redigera filen SQLDW. conf i katalogen *DestDir* .

> [!NOTE]
> För att undvika schema namns konflikter med de som redan finns i Azure Azure Synapse-analysen, när du läser parametrar direkt från filen SQLDW. conf, läggs ett 3-siffrigt slumptal till i schema namnet från filen SQLDW. conf som standard schema namn för varje körning. PowerShell-skriptet kan bli ombedd att ange ett schema namn: namnet kan anges på användarens eget gottfinnande.
>
>

Den här **PowerShell-skriptfilen** Slutför följande uppgifter:

* **Hämtar och installerar AzCopy**, om AzCopy inte redan har installerats

  ```azurepowershell
  $AzCopy_path = SearchAzCopy
  if ($AzCopy_path -eq $null){
         Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
      InstallAzCopy
      $AzCopy_path = SearchAzCopy
  }
      $env_path = $env:Path
      for ($i=0; $i -lt $AzCopy_path.count; $i++){
          if ($AzCopy_path.count -eq 1){
              $AzCopy_path_i = $AzCopy_path
          } else {
              $AzCopy_path_i = $AzCopy_path[$i]
          }
          if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
              Write-Host $AzCopy_path_i 'not in system path, add it...'
              [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
              $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
              $env_path = $env:Path
          }
  ```

* **Kopierar data till ditt privata Blob Storage-konto** från den offentliga blobben med AZCopy

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* **Läser in data med PolyBase (genom att köra LoadDataToSQLDW. SQL) i Azure Synapse Analytics** från ditt privata Blob Storage-konto med följande kommandon.

  * Skapa ett schema

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Skapa en databas med begränsade autentiseringsuppgifter

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Skapa en extern data källa för en Azure Storage-BLOB

    ```sql
    CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;

    CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;
    ```

  * Skapa ett externt fil format för en CSV-fil. Data är okomprimerade och fält åtskiljs med pipe-tecknet.

    ```sql
    CREATE EXTERNAL FILE FORMAT {csv_file_format}
    WITH
    (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS
        (
            FIELD_TERMINATOR ='','',
            USE_TYPE_DEFAULT = TRUE
        )
    )
    ;
    ```

  * Skapa externa pris-och rese tabeller för NYC taxi-datauppsättningen i Azure Blob Storage.

    ```sql
    CREATE EXTERNAL TABLE {external_nyctaxi_fare}
    (
        medallion varchar(50) not null,
        hack_license varchar(50) not null,
        vendor_id char(3),
        pickup_datetime datetime not null,
        payment_type char(3),
        fare_amount float,
        surcharge float,
        mta_tax float,
        tip_amount float,
        tolls_amount float,
        total_amount float
    )
    with (
        LOCATION    = ''/nyctaxifare/'',
        DATA_SOURCE = {nyctaxi_fare_storage},
        FILE_FORMAT = {csv_file_format},
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 12
    )

      CREATE EXTERNAL TABLE {external_nyctaxi_trip}
      (
             medallion varchar(50) not null,
             hack_license varchar(50)  not null,
             vendor_id char(3),
             rate_code char(3),
             store_and_fwd_flag char(3),
             pickup_datetime datetime  not null,
             dropoff_datetime datetime,
             passenger_count int,
             trip_time_in_secs bigint,
             trip_distance float,
             pickup_longitude varchar(30),
             pickup_latitude varchar(30),
             dropoff_longitude varchar(30),
             dropoff_latitude varchar(30)
      )
      with (
          LOCATION    = ''/nyctaxitrip/'',
          DATA_SOURCE = {nyctaxi_trip_storage},
          FILE_FORMAT = {csv_file_format},
          REJECT_TYPE = VALUE,
          REJECT_VALUE = 12
      )
    ```

    - Läs in data från externa tabeller i Azure Blob Storage till Azure Synapse Analytics

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_fare}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_fare}
      ;

      CREATE TABLE {schemaname}.{nyctaxi_trip}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_trip}
      ;
      ```

    - Skapa en exempel data tabell (NYCTaxi_Sample) och infoga data i den från att välja SQL-frågor för rese-och avgifts tabellerna. (Vissa steg i den här genom gången måste använda den här exempel tabellen.)

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_sample}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      (
          SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
          tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
          tip_class = CASE
                  WHEN (tip_amount = 0) THEN 0
                  WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                  WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                  WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                  ELSE 4
              END
          FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
          WHERE datepart("mi",t.pickup_datetime) = 1
          AND t.medallion = f.medallion
          AND   t.hack_license = f.hack_license
          AND   t.pickup_datetime = f.pickup_datetime
          AND   pickup_longitude <> ''0''
          AND   dropoff_longitude <> ''0''
      )
      ;
      ```

Den geografiska platsen för dina lagrings konton påverkar inläsnings tider.

> [!NOTE]
> Beroende på den geografiska platsen för ditt privata Blob Storage-konto kan processen med att kopiera data från en offentlig blob till ditt privata lagrings konto ta cirka 15 minuter, eller till och med längre, och processen för att läsa in data från ditt lagrings konto till Azure Azure Synapse Analytics kan ta 20 minuter eller längre.
>
>

Du måste bestämma vad du ska göra om du har dubbla käll-och målfiler.

> [!NOTE]
> Om CSV-filerna som ska kopieras från den offentliga blob-lagringen till ditt privata Blob Storage-konto redan finns i ditt privata Blob Storage-konto, kommer AzCopy att fråga efter om du vill skriva över dem. Om du inte vill skriva över dem skriver du in **n** när du uppmanas till det. Om du vill skriva över **alla** dessa, ange **en** när du uppmanas att göra det. Du kan också ange **y** för att skriva över. csv-filer individuellt.
>
>

![Utdata från AzCopy][21]

Du kan använda dina egna data. Om dina data finns på din lokala dator i ditt riktiga liv program kan du fortfarande använda AzCopy för att överföra lokala data till din privata Azure Blob Storage. Du behöver bara ändra **käll** platsen, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"` i AzCopy-kommandot för PowerShell-skriptfilen till den lokala katalog som innehåller dina data.

> [!TIP]
> Om dina data redan finns i din privata Azure Blob-lagring i ditt riktiga liv program, kan du hoppa över AzCopy-steget i PowerShell-skriptet och överföra data direkt till Azure Azure Synapse Analytics. Detta kräver ytterligare redigeringar av skriptet för att skräddarsy det till formatet på dina data.
>
>

Det här PowerShell-skriptet ansluts också i Azure Synapse Analytics-informationen till exempel filen för data utforskningar SQLDW_Explorations. SQL, SQLDW_Explorations. ipynb och SQLDW_Explorations_Scripts. py så att dessa tre filer kan provas direkt efter att PowerShell-skriptet har slutförts.

När körningen är klar visas skärmen som nedan:

![Utdata från en lyckad skript körning][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Data utforskning och funktions teknik i Azure Synapse Analytics
I det här avsnittet utför vi data utforsknings-och funktions skapande genom att köra SQL-frågor mot Azure Synapse Analytics direkt med **Visual Studio data tools**. Alla SQL-frågor som används i det här avsnittet finns i exempel skriptet som heter *SQLDW_Explorations. SQL*. Den här filen har redan laddats ned till din lokala katalog med PowerShell-skriptet. Du kan också hämta det från [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Men filen i GitHub har inte anslutet till Azure Synapse Analytics-information.

Anslut till din Azure Synapse Analytics med hjälp av Visual Studio med inloggnings namnet och lösen ordet för Azure Synapse Analytics och öppna **SQL-Object Explorer** för att bekräfta att databasen och tabellerna har importer ATS. Hämta filen *SQLDW_Explorations. SQL* .

> [!NOTE]
> Om du vill öppna PDW-Frågeredigeraren använder du kommandot **ny fråga** medan ditt PDW har valts i **SQL-Object Explorer**. Standard-SQL-Frågeredigeraren stöds inte av PDW.
>
>

Här är de typer av data utforsknings-och funktions skapande uppgifter som utförs i det här avsnittet:

* Utforska data distributioner av några fält i varierande tidsfönster.
* Undersök data kvaliteten på fälten longitud och Latitude.
* Generera binära och multiklassens klassificerings etiketter baserat på **tips \_ mängden**.
* Generera funktioner och beräkna/jämför rese avstånd.
* Gå med i de två tabellerna och extrahera ett slumpmässigt exempel som ska användas för att bygga modeller.

### <a name="data-import-verification"></a>Verifiering av data import
De här frågorna ger en snabb kontroll av antalet rader och kolumner i tabellerna som är ifyllda tidigare med polybases parallell Mass import,

--Rapport antal rader i tabell <nyctaxi_trip> utan tabells ökning

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

--Rapport antalet kolumner i tabell <nyctaxi_trip>

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Utdata:** Du bör få 173 179 759 rader och 14 kolumner.

### <a name="exploration-trip-distribution-by-medallion"></a>Undersökning: rese distribution per Medallion
I den här exempel frågan identifieras de medallions (taxi nummer) som genomförde mer än 100 resor inom en angiven tids period. Frågan skulle dra nytta av den partitionerade tabell åtkomsten eftersom den har ett villkor för det partition schema som användes vid **upphämtnings \_ datum**. Vid frågor till den fullständiga data uppsättningen används även den partitionerade tabellen och/eller index genomsökningen.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Utdata:** Frågan ska returnera en tabell med rader som anger 13 369-medallions (taxi) och antalet resor som slutförts i 2013. Den sista kolumnen innehåller antalet slutförda resor.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Undersökning: rese distribution av Medallion och hack_license
I det här exemplet identifieras medallions (taxi-nummer) och hack_licenses nummer (driv rutiner) som slutförde fler än 100 resor inom en angiven tids period.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Utdata:** Frågan ska returnera en tabell med 13 369 rader som anger 13 369 bilar/driv rutins-ID: n som har slutfört mer än 100 resor i 2013. Den sista kolumnen innehåller antalet slutförda resor.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Utvärdering av data kvalitet: verifiera poster med felaktig longitud och/eller latitud
Det här exemplet undersöker om något av fälten longitud och/eller latitud innehåller ett ogiltigt värde (radian grader måste vara mellan-90 och 90), eller ha koordinaterna (0, 0).

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Utdata:** Frågan returnerar 837 467 resor som har ogiltiga longitud-och/eller latitud-fält.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Undersökning: lutad eller överlutad TRIPs-distribution
I det här exemplet hittar du antalet resor som har lutats jämfört med det antal som inte lutades under en viss tids period (eller i den fullständiga data uppsättningen om det omfattar hela året som det ställs in här). Den här distributionen visar den binära etikett fördelningen som ska användas senare för binära klassificerings modellering.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Utdata:** Frågan ska returnera följande tips frekvenser för året 2013:90 447 622 lutad och 82 264 709 ej lutad.

### <a name="exploration-tip-classrange-distribution"></a>Undersökning: Tip-klass/intervall-distribution
Det här exemplet beräknar fördelningen av Tip-intervall under en viss tids period (eller i den fullständiga data uppsättningen om den täcker hela året). Den här fördelningen av etikett klasser kommer att användas senare för klassificerings modellering i multiklass.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

**Utdataparametrar**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Undersökning: beräkna och jämför rese avstånd
I det här exemplet konverteras upphämtnings-och DropOff longitud och latitud till SQL-geografiska punkter, vilket beräknar rese avståndet med hjälp av SQL geografi-punkter skillnad och returnerar ett slumpmässigt exempel av resultaten för jämförelse. Exemplet begränsar resultatet till giltiga koordinater endast med frågan om utvärdering av data som tidigare beskrivits.

```sql
/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO

SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
```

### <a name="feature-engineering-using-sql-functions"></a>Funktions teknik med hjälp av SQL Functions
Ibland kan SQL Functions vara ett effektivt alternativ för funktions teknik. I den här genom gången definierade vi en SQL-funktion för att beräkna det direkta avståndet mellan plock-och DropOff platserna. Du kan köra följande SQL-skript i **Visual Studio data tools**.

Här är SQL-skriptet som definierar avstånds funktionen.

```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function calculate the direct distance between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO
```

Här är ett exempel på hur du anropar funktionen för att generera funktioner i SQL-fr åga:

--Exempel fråga för att anropa funktionen för att skapa funktioner

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Utdata:** Den här frågan genererar en tabell (med 2 803 538 rader) med upphämtnings-och DropOff-latituder och longituder och motsvarande direkta avstånd i miles. Här följer resultaten för de första tre raderna:

| (Rad nummer) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |– 74,001083 |40,736622 |– 73,988953 |.7169601222 |
| 2 |40,715794 |– 74, 010635 |40,725338 |– 74,00399 |.7448343721 |
| 3 |40,761456 |– 73,999886 |40,766544 |– 73,988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>Förbereda data för modell utveckling
Följande fråga ansluter tabellerna **nyctaxi \_ rese** -och **nyctaxi- \_ pris** , genererar en binära klassificerings **etikett, en** **\_ klass** för klassificerings etiketter i flera klasser och extraherar ett exempel från den fullständiga sammanfogade data uppsättningen. Samplingen görs genom att en delmängd av resan hämtas baserat på hämtnings tiden.  Den här frågan kan kopieras och klistras in direkt i [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net) [Importera data][import-] datamodul för direkt data inmatning från SQL Database-instansen i Azure. Frågan utesluter poster med felaktiga koordinater (0, 0).

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
WHERE datepart("mi",t.pickup_datetime) = 1
AND   t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

När du är redo att fortsätta till Azure Machine Learning kan du antingen:

1. Spara den sista SQL-frågan för att extrahera och sampla data och kopiera – klistra in frågan direkt i en import data[-] modul i Azure Machine Learning, eller
2. Behåll de insamlade data som du planerar att använda för modell utveckling i en ny Azure Synapse Analytics-tabell och Använd den nya tabellen i modulen [Importera data][import-data] i Azure Machine Learning. PowerShell-skriptet i föregående steg har utfört den här åtgärden åt dig. Du kan läsa direkt från den här tabellen i modulen importera data.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Data utforskning och funktions teknik i IPython Notebook
I det här avsnittet ska vi utföra data utforskning och skapa funktioner med hjälp av både python-och SQL-frågor mot Azure Synapse Analytics som skapades tidigare. Ett exempel på en IPython Notebook med namnet **SQLDW_Explorations. ipynb** och en python-skriptfil **SQLDW_Explorations_Scripts. py** har laddats ned till din lokala katalog. De är också tillgängliga på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Dessa två filer är identiska i Python-skript. Du får en python-skriptfil om du inte har en IPython Notebook-Server. Dessa två exempel på python-filer är utformade under **python 2,7**.

Den Azure Synapse Analytics-information som krävs i den exempel IPython Notebook och den python-skriptfil som hämtats till den lokala datorn har redan kopplats av PowerShell-skriptet. De är körbara utan några ändringar.

Om du redan har konfigurerat en Azure Machine Learning arbets yta kan du ladda upp den IPython-anteckningsboken direkt till AzureML IPython Notebook service och börja köra den. Här följer stegen för att ladda upp till AzureML IPython Notebook service:

1. Logga in på din Azure Machine Learning arbets yta, klicka på **Studio "** längst upp" och klicka på **antecknings böcker** på vänster sida av webb sidan.

    ![Klicka på Studio och sedan på bärbara datorer][22]
2. Klicka på **nytt** i det vänstra nedre hörnet på webb sidan och välj **python 2**. Ange sedan ett namn för antecknings boken och klicka på kryss markeringen för att skapa den nya tomma IPython-anteckningsboken.

    ![Klicka på ny och välj python 2][23]
3. Klicka på **Jupyter** -symbolen i det vänstra övre hörnet av den nya IPython Notebook.

    ![Klicka på Jupyter symbol][24]
4. Dra och släpp exempel antecknings boken IPython till **träd** sidan i din azureml IPython Notebook-tjänst och klicka på **Ladda upp**. Sedan laddas IPython Notebook till AzureML IPython Notebook service.

    ![Klicka på överför][25]

Följande python-paket krävs för att köra IPython Notebook eller Python-skript filen. Om du använder AzureML IPython Notebook-tjänsten har dessa paket förinstallerats.

- Pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Här är den rekommenderade ordningen när du bygger avancerade analys lösningar på Azure Machine Learning med stora data:

* Läs i ett litet exempel på data till en minnes intern data ram.
* Utför vissa visualiseringar och utforskningar med hjälp av exempel data.
* Experimentera med funktions teknik med hjälp av exempel data.
* För större data utforskning, data manipulation och funktions teknik använder du python för att utfärda SQL-frågor direkt mot Azure Synapse Analytics.
* Bestäm vilken exempel storlek som passar bäst för Azure Machine Learning modell skapande.

Följande är några exempel på data visualisering, data visualisering och funktions teknik. Mer data utforskningar finns i den IPython Notebook och exempel filen python.

### <a name="initialize-database-credentials"></a>Initiera autentiseringsuppgifter för databas
Initiera inställningarna för databas anslutningen i följande variabler:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Skapa databas anslutning
Här är anslutnings strängen som skapar anslutningen till databasen.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Rapport antal rader och kolumner i tabell <nyctaxi_trip>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Totalt antal rader = 173179759
* Totalt antal kolumner = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Rapport antal rader och kolumner i tabell <nyctaxi_fare>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Totalt antal rader = 173179759
* Totalt antal kolumner = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Läs ett litet data exempel från Azure Synapse Analytics-databasen

```sql
t0 = time.time()

query = '''
    SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

Tiden för att läsa exempel tabellen är 14,096495 sekunder.
Antal rader och kolumner som hämtats = (1000, 21).

### <a name="descriptive-statistics"></a>Beskrivande statistik
Nu är du redo att utforska exempel data. Vi börjar med att titta på viss beskrivande statistik för **rese \_ avståndet** (eller andra fält som du väljer att ange).

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Visualisering: låddiagram: exempel
Härnäst ska vi titta på Box-området för resan för att visualisera quantiles.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Utdata i Box-ritning][1]

### <a name="visualization-distribution-plot-example"></a>Visualisering: exempel på distributions ritning
Visar en kurva som visualiserar fördelningen och ett histogram för avstånden för provs resan.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Utdata för distributions ritning][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisering: stapel-och linje diagram
I det här exemplet Binar vi rese avståndet till fem lager platser och visualiserar diskretisering-resultaten.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Vi kan rita över lager plats fördelningen ovan i ett stapel-eller linje diagram med:

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Utdata i Stolp området][3]

och

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Linje ritning, utdata][4]

### <a name="visualization-scatterplot-examples"></a>Visualisering: scatterplot-exempel
Vi visar punkt diagram mellan **rese \_ tiden \_ i \_ sekunder** och **rese \_ avstånd** för att se om det finns någon korrelation

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Scatterplot utdata från relationen mellan tid och avstånd][6]

På samma sätt kan vi kontrol lera förhållandet mellan **pris \_ kod** och **rese \_ avstånd**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Scatterplot utdata från relationen mellan kod och avstånd][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Data utforskning för exempel data med hjälp av SQL-frågor i IPython Notebook
I det här avsnittet ska vi utforska data distributioner med hjälp av de exempel data som finns kvar i den nya tabellen som vi skapade ovan. Liknande utforskningar kan utföras med hjälp av de ursprungliga tabellerna.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Undersökning: rapport antal rader och kolumner i exempel tabellen

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Undersökning: lutad/inte utlöst-distribution

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Undersökning: tips klass distribution

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Undersökning: Rita tips fördelningen efter klass

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![Rita #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Undersökning: daglig distribution av resor

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Undersökning: rese distribution per Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Undersökning: rese distribution av Medallion och hacka licens

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Undersökning: distribution av rese tid

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Undersökning: distribution av rese avstånd

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Undersökning: distribution av betalnings typ

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifiera den slutliga formen för bearbetas-tabellen

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Bygg modeller i Azure Machine Learning
Vi är nu redo att gå vidare till modell utveckling och modell distribution i [Azure Machine Learning](https://studio.azureml.net). Data är redo att användas i något av de förutsägelse problem som identifierats tidigare, nämligen:

1. **Binära klassificering**: för att förutsäga om ett tips har betalats för en resa.
2. **Klassificering** av flera klasser: för att förutsäga det tips som betalas, enligt de tidigare definierade klasserna.
3. **Regressions uppgift**: för att förutsäga hur mycket tips du betalar för en resa.

Om du vill påbörja modelleringen av modellering loggar du in på arbets ytan **Azure Machine Learning (klassisk)** . Om du ännu inte har skapat en Machine Learning-arbetsyta, se [skapa en Azure Machine Learning Studio (klassisk)-arbets yta](../classic/create-workspace.md).

1. För att komma igång med Azure Machine Learning, se [Vad är Azure Machine Learning Studio (klassisk)?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Logga in på [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net).
3. På Start sidan för Machine Learning Studio (klassisk) finns en mängd information, videor, självstudier, länkar till modulerna referens och andra resurser. Mer information om Azure Machine Learning finns i [Azure Machine Learning dokumentations Center](https://azure.microsoft.com/documentation/services/machine-learning/).

Ett typiskt utbildnings experiment består av följande steg:

1. Skapa ett **+ nytt** experiment.
2. Hämta data till Azure Machine Learning Studio (klassisk).
3. I förväg bearbeta, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela data i utbildning/validering/testning av data uppsättningar (eller ha separata data uppsättningar för var och en).
6. Välj en eller flera Machine Learning-algoritmer beroende på inlärnings problemet för att lösa problemet. Till exempel binära klassificering, klassificering av multiklass, regression.
7. Träna en eller flera modeller med hjälp av inlärnings data uppsättningen.
8. Räkna in verifierings data uppsättningen med hjälp av de utbildade modellerna.
9. Utvärdera modell (er) för att beräkna relevanta mått för inlärnings problemet.
10. Justera modell (er) och välj den bästa modellen som ska distribueras.

I den här övningen har vi redan utforskat och utformat data i Azure Synapse Analytics, och beslut om hur stor provtagnings storlek som skulle matas in i Azure Machine Learning Studio (klassisk). Här är proceduren för att bygga en eller flera av förutsägelse modellerna:

1. Hämta data till Azure Machine Learning Studio (klassisk) med hjälp av modulen [Importera data][import-data] som finns i avsnittet **data indata och utdata** . Mer information finns på referens sidan [Importera data][import-data] module.

    ![Importera data från Azure ML][17]
2. Välj **Azure SQL Database** som **data källa** i panelen **Egenskaper** .
3. Ange databasens DNS-namn i fältet **databas server namn** . Formatering `tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange **databas namnet** i motsvarande fält.
5. Ange *SQL-användarnamnet* i **serverns användar konto namn** och *lösen* ordet i **serverns användar konto lösen ord**.
7. I redigerings text områden för **databas fråga** klistrar du in frågan som extraherar de nödvändiga databas fälten (inklusive alla beräknade fält som etiketterna) och nedåt exempel data till önskad exempel storlek.

Ett exempel på ett binära klassificerings experiment som läser data direkt från Azure Synapse Analytics-databasen visas i bilden nedan (kom ihåg att ersätta tabell namnen nyctaxi_trip och nyctaxi_fare av schema namnet och de tabell namn som du använde i din genom gång). Liknande experiment kan konstrueras för klassificerings-och Regressions problem med multiklasser.

![Azure ML-tåg][10]

> [!IMPORTANT]
> I exemplen för att extrahera data och samplings frågor i föregående avsnitt, **ingår alla etiketter för de tre modell övningarna i frågan**. Ett viktigt (obligatoriskt) steg i varje modell övning är att **utesluta** onödiga etiketter för de andra två problemen och andra **mål läckor**. Om du t. ex. använder binär klassificering använder du etiketten **lutad** och utelämnar fält **Tip- \_ klassen**, **Tip- \_ beloppet** och **total \_ beloppet**. De sistnämnda är mål läckor eftersom de innebär att tipset betalas.
>
> Om du vill undanta onödiga kolumner eller mål läckor kan du använda modulen [Välj kolumner i data uppsättning][select-columns] eller [Redigera metadata][edit-metadata]. Mer information finns i avsnittet [Välj kolumner i data uppsättning][select-columns] och [Redigera metadata][edit-metadata] referens sidor.
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När modellen är klar kan du enkelt distribuera den som en webb tjänst direkt från experimentet. Mer information om hur du distribuerar Azure ML-webbtjänster finns i [distribuera en Azure Machine Learning-webbtjänst](../classic/deploy-a-machine-learning-web-service.md).

Om du vill distribuera en ny webb tjänst måste du:

1. Skapa ett poängsättnings experiment.
2. Distribuera webb tjänsten.

Om du vill skapa ett Poäng experiment från ett **färdigt** utbildnings experiment klickar du på **skapa Poäng experiment** i det nedre åtgärds fältet.

![Azure-Poäng][18]

Azure Machine Learning försöker skapa ett bedömnings experiment baserat på komponenterna i utbildnings experimentet. I synnerhet kommer det att:

1. Spara den tränade modellen och ta bort modulen modell utbildning.
2. Identifiera en logisk **indataport** som representerar det förväntade schemat för indata.
3. Identifiera en logisk **utdataport** som representerar det förväntade datautdata-schemat för webb tjänsten.

När Poäng experimentet har skapats granskar du resultaten och gör anpassningen efter behov. En typisk justering är att ersätta indata-datauppsättningen eller frågan med en som utesluter etikett fält, eftersom dessa etikett fält inte kommer att mappas till schemat vid anrop till tjänsten. Det är också en bra idé att minska storleken på data uppsättningen och/eller frågan till några få poster, tillräckligt för att ange schemat för indata. För utdataporten är det vanligt att undanta alla indatafält och bara ta med de **resultat etiketter** och **resultat** som visas i resultatet med hjälp av modulen [Välj kolumner i data uppsättning][select-columns] .

Ett exempel på ett bedömnings experiment visas i bilden nedan. När du är redo att distribuera klickar du på knappen **publicera webb tjänst** i det nedre åtgärds fältet.

![Azure ML-publicering][11]

## <a name="summary"></a>Sammanfattning
För att Sammanfattning det som vi har gjort i den här själv studie kursen har du skapat en Azure Data Science-miljö som arbetar med en stor offentlig data uppsättning, som tar den genom processen för team data vetenskap, allt från data förvärv till modell utbildning, och sedan till distributionen av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licens information
Den här exempel genom gången och dess tillhör ande skript och IPython-anteckningsböcker delas av Microsoft under MIT-licensen. Se LICENSE.txt-filen i katalogen i exempel koden på GitHub för mer information.

## <a name="references"></a>Referenser
- [Hämtnings sida för Andrés Monroy NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/)
- [NYC Taxit taxi data från Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [Forskning och statistik för NYC taxi och limousine provision](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data