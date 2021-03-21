---
title: Migrera data från Amazon S3 till Azure Data Lake Storage Gen2
description: Lär dig hur du använder en lösnings mall för att migrera data från Amazon S3 genom att använda en extern kontroll tabell för att lagra en partitionstabell på AWS S3 med Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/07/2019
ms.openlocfilehash: c1fd4cb248abdc219c6ee5d098e10c329826c160
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362003"
---
# <a name="migrate-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>Migrera data från Amazon S3 till Azure Data Lake Storage Gen2

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Använd mallarna för att migrera petabyte av data som består av hundratals miljoner filer från Amazon S3 till Azure Data Lake Storage Gen2. 

 > [!NOTE]
 > Om du vill kopiera små data volymer från AWS S3 till Azure (till exempel mindre än 10 TB) är det mer effektivt och enkelt att använda [Azure Data Factory kopiera data-verktyget](copy-data-tool.md). Mallen som beskrivs i den här artikeln är mer än vad du behöver.

## <a name="about-the-solution-templates"></a>Om Solution templates

Data partition rekommenderas särskilt när du migrerar mer än 10 TB data. Om du vill partitionera data kan du använda "prefix"-inställningen för att filtrera mapparna och filerna på Amazon S3 efter namn och sedan kan varje ADF-kopierings jobb kopiera en partition i taget. Du kan köra flera jobb för ADF-kopiering samtidigt för bättre data flöde.

Datamigreringen kräver vanligt vis historisk datamigrering vid en tidpunkt och synkroniserar regelbundet ändringarna från AWS S3 till Azure. Det finns två mallar nedan, där en mall omfattar historisk datamigrering vid en tidpunkt och en annan mall omfattar synkronisering av ändringarna från AWS S3 till Azure.

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För att mallen ska migrera historiska data från Amazon S3 till Azure Data Lake Storage Gen2

Den här mallen (*Mallnamn: Migrera historiska data från AWS S3 till Azure Data Lake Storage Gen2*) förutsätter att du har skrivit en partitionerad lista i en extern kontroll tabell i Azure SQL Database. Därför används en *Lookup* -aktivitet för att hämta en lista över partitioner från den externa kontroll tabellen, iterera över varje partition och göra varje jobb i utskriften kopiera en partition i taget. När ett kopierings jobb har slutförts används aktiviteten *lagrad procedur* för att uppdatera statusen för kopiering av varje partition i kontroll tabellen.

Mallen innehåller fem aktiviteter:
- **Lookup** hämtar de partitioner som inte har kopierats till Azure Data Lake Storage Gen2 från en extern kontroll tabell. Tabell namnet är *s3_partition_control_table* och frågan för att läsa in data från tabellen är *"SELECT PARTITIONPREFIX from s3_partition_control_table WHERE SuccessOrFailure = 0"*.
- Med den här **listan får du** en lista över partitionerna från *söknings* aktiviteten och upprepar varje partition till *TriggerCopy* -aktiviteten. Du kan ställa in *batchCount* att köra flera jobb för ADF-kopiering samtidigt. Vi har angett 2 i den här mallen.
- **ExecutePipeline** kör *CopyFolderPartitionFromS3* -pipeline. Anledningen till att vi skapar en till pipeline för att göra varje kopierings jobb kopiera en partition beror på att det gör att du enkelt kan köra om det misslyckade kopierings jobbet för att läsa in den aktuella partitionen igen från AWS S3. Alla andra kopierings jobb som läser in andra partitioner påverkas inte.
- **Copy** kopierar varje partition från AWS S3 till Azure Data Lake Storage Gen2.
- **SqlServerStoredProcedure** uppdaterar status för kopiering av varje partition i kontroll tabellen.

Mallen innehåller två parametrar:
- **AWS_S3_bucketName** är ditt Bucket-namn på AWS S3 där du vill migrera data från. Om du vill migrera data från flera buckets på AWS S3 kan du lägga till en kolumn i den externa kontroll tabellen för att lagra Bucket-namnet för varje partition och även uppdatera din pipeline för att hämta data från den kolumnen.
- **Azure_Storage_fileSystem** är fil namns namnet på Azure Data Lake Storage Gen2 dit du vill migrera data till.

### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För mallen för att kopiera ändrade filer enbart från Amazon S3 till Azure Data Lake Storage Gen2

Den här mallen (*Mallnamn: kopiera delta data från AWS S3 till Azure Data Lake Storage Gen2*) använder LastModifiedTime för varje fil för att kopiera nya eller uppdaterade filer enbart från AWS S3 till Azure. Tänk på att om dina filer eller mappar redan har tidspartitioner ATS med timeslice-information som en del av fil-eller mappnamnet på AWS S3 (till exempel/YYYY/MM/DD/file.csv), kan du gå till den här [självstudien](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) för att få en mer bra metod för att hämta nya filer med stegvisa inläsningar. Den här mallen förutsätter att du har skrivit en partitionerad lista i en extern kontroll tabell i Azure SQL Database. Därför används en *Lookup* -aktivitet för att hämta en lista över partitioner från den externa kontroll tabellen, iterera över varje partition och göra varje jobb i utskriften kopiera en partition i taget. När varje kopierings jobb börjar kopiera filerna från AWS S3 förlitar sig det på egenskapen LastModifiedTime för att identifiera och kopiera endast nya eller uppdaterade filer. När ett kopierings jobb har slutförts används aktiviteten *lagrad procedur* för att uppdatera statusen för kopiering av varje partition i kontroll tabellen.

Mallen innehåller sju aktiviteter:
- **Lookup** hämtar partitionerna från en extern kontroll tabell. Tabell namnet är *s3_partition_delta_control_table* och frågan för att läsa in data från tabellen är *"Select distinct PartitionPrefix from s3_partition_delta_control_table"*.
- Med den här **listan får du** en lista över partitionerna från *söknings* aktiviteten och upprepar varje partition till *TriggerDeltaCopy* -aktiviteten. Du kan ställa in *batchCount* att köra flera jobb för ADF-kopiering samtidigt. Vi har angett 2 i den här mallen.
- **ExecutePipeline** kör *DeltaCopyFolderPartitionFromS3* -pipeline. Anledningen till att vi skapar en till pipeline för att göra varje kopierings jobb kopiera en partition beror på att det gör att du enkelt kan köra om det misslyckade kopierings jobbet för att läsa in den aktuella partitionen igen från AWS S3. Alla andra kopierings jobb som läser in andra partitioner påverkas inte.
- **Lookup** hämtar den senaste körnings tiden för kopierings jobb från den externa kontroll tabellen så att de nya eller uppdaterade filerna kan identifieras via LastModifiedTime. Tabell namnet är *s3_partition_delta_control_table* och frågan för att läsa in data från tabellen är *"Select Max (JobRunTime) som LastModifiedTime från s3_partition_delta_control_table där PartitionPrefix =" @ {pipeline (). Parameters. prefixStr} "och SuccessOrFailure = 1"*.
- **Kopiera** kopierar bara nya eller ändrade filer för varje partition från AWS S3 till Azure Data Lake Storage Gen2. Egenskapen för *modifiedDatetimeStart* anges till den senaste körnings tiden för kopierings jobb. Egenskapen för *modifiedDatetimeEnd* har angetts till den aktuella kopierings jobb körnings tiden. Tänk på att tiden tillämpas på UTC-tidszonen.
- **SqlServerStoredProcedure** uppdaterar status för kopiering av varje partition och kopierings körnings tid i kontroll tabellen när den lyckas. Kolumnen i SuccessOrFailure har angetts till 1.
- **SqlServerStoredProcedure** uppdaterar status för kopiering av varje partition och kopierings körnings tid i kontroll tabellen när den Miss lyckas. Kolumnen i SuccessOrFailure har angetts till 0.

Mallen innehåller två parametrar:
- **AWS_S3_bucketName** är ditt Bucket-namn på AWS S3 där du vill migrera data från. Om du vill migrera data från flera buckets på AWS S3 kan du lägga till en kolumn i den externa kontroll tabellen för att lagra Bucket-namnet för varje partition och även uppdatera din pipeline för att hämta data från den kolumnen.
- **Azure_Storage_fileSystem** är fil namns namnet på Azure Data Lake Storage Gen2 dit du vill migrera data till.

## <a name="how-to-use-these-two-solution-templates"></a>Använda de här två lösnings mallarna

### <a name="for-the-template-to-migrate-historical-data-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För att mallen ska migrera historiska data från Amazon S3 till Azure Data Lake Storage Gen2 

1. Skapa en kontroll tabell i Azure SQL Database för att lagra listan med partitioner för AWS S3. 

    > [!NOTE]
    > Tabell namnet är s3_partition_control_table.
    > Schemat för kontroll tabellen är PartitionPrefix och SuccessOrFailure, där PartitionPrefix är prefixlängden i S3 för att filtrera mapparna och filerna i Amazon S3 efter namn och SuccessOrFailure är status för kopiering av varje partition: 0 innebär att den här partitionen inte har kopierats till Azure och 1 innebär att partitionen har kopierats till Azure.
    > Det finns fem partitioner definierade i kontroll tabellen och standard status för kopiering av varje partition är 0.

    ```sql
    CREATE TABLE [dbo].[s3_partition_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [SuccessOrFailure] [bit] NULL
    )

    INSERT INTO s3_partition_control_table (PartitionPrefix, SuccessOrFailure)
    VALUES
    ('a', 0),
    ('b', 0),
    ('c', 0),
    ('d', 0),
    ('e', 0);
    ```

2. Skapa en lagrad procedur på samma Azure SQL Database för kontroll tabell. 

    > [!NOTE]
    > Namnet på den lagrade proceduren är sp_update_partition_success. Den kommer att anropas av SqlServerStoredProcedure-aktiviteten i din ADF-pipeline.

    ```sql
    CREATE PROCEDURE [dbo].[sp_update_partition_success] @PartPrefix varchar(255)
    AS
    BEGIN
    
        UPDATE s3_partition_control_table
        SET [SuccessOrFailure] = 1 WHERE [PartitionPrefix] = @PartPrefix
    END
    GO
    ```

3. Gå till **Azure Data Lake Storage Gen2 mallen migrera historiska data från AWS S3 till** . Ange anslutningarna till den externa kontroll tabellen, AWS S3 som data källans lager och Azure Data Lake Storage Gen2 som mål arkiv. Tänk på att den externa kontroll tabellen och den lagrade proceduren refererar till samma anslutning.

    ![Skärm bild som visar migrera historiska data från AWS S3 till Azure Data Lake Storage Gen2 mall.](media/solution-template-migration-s3-azure/historical-migration-s3-azure1.png)

4. Välj **Använd den här mallen**.

    ![Skärm bild som visar knappen Använd den här mallen.](media/solution-template-migration-s3-azure/historical-migration-s3-azure2.png)
    
5. Du ser 2 pipeliner och 3 data uppsättningar skapades, som du ser i följande exempel:

    ![Skärm bild som visar de två pipelinen och tre data uppsättningar som har skapats med hjälp av mallen.](media/solution-template-migration-s3-azure/historical-migration-s3-azure3.png)

6. Gå till pipelinen "BulkCopyFromS3" och välj **Debug** och ange **parametrarna**. Välj sedan **Slutför**.

    ![Skärm bild som visar var du väljer Felsök och anger parametrarna innan du väljer Slutför.](media/solution-template-migration-s3-azure/historical-migration-s3-azure4.png)

7. Du ser resultat som liknar följande exempel:

    ![Skärm bild som visar de returnerade resultaten.](media/solution-template-migration-s3-azure/historical-migration-s3-azure5.png)


### <a name="for-the-template-to-copy-changed-files-only-from-amazon-s3-to-azure-data-lake-storage-gen2"></a>För mallen för att kopiera ändrade filer enbart från Amazon S3 till Azure Data Lake Storage Gen2

1. Skapa en kontroll tabell i Azure SQL Database för att lagra listan med partitioner för AWS S3. 

    > [!NOTE]
    > Tabell namnet är s3_partition_delta_control_table.
    > Schemat för kontroll tabellen är PartitionPrefix, JobRunTime och SuccessOrFailure, där PartitionPrefix är inställningen för prefixet i S3 för att filtrera mapparna och filerna i Amazon S3 efter namn, JobRunTime är datetime-värdet när kopiering av jobb körs och SuccessOrFailure är status för att kopiera varje partition: 0 innebär att den här partitionen inte har kopierats till Azure och 1 innebär att partitionen har kopierats till Azure.
    > Det finns fem partitioner definierade i kontroll tabellen. Standardvärdet för JobRunTime kan vara den tid då en historisk datamigrering startar. I ADF Copy-aktiviteten kopieras filerna på AWS S3 som senast ändrades efter den tiden. Standard status för kopiering av varje partition är 1.

    ```sql
    CREATE TABLE [dbo].[s3_partition_delta_control_table](
        [PartitionPrefix] [varchar](255) NULL,
        [JobRunTime] [datetime] NULL,
        [SuccessOrFailure] [bit] NULL
        )

    INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
    VALUES
    ('a','1/1/2019 12:00:00 AM',1),
    ('b','1/1/2019 12:00:00 AM',1),
    ('c','1/1/2019 12:00:00 AM',1),
    ('d','1/1/2019 12:00:00 AM',1),
    ('e','1/1/2019 12:00:00 AM',1);
    ```

2. Skapa en lagrad procedur på samma Azure SQL Database för kontroll tabell. 

    > [!NOTE]
    > Namnet på den lagrade proceduren är sp_insert_partition_JobRunTime_success. Den kommer att anropas av SqlServerStoredProcedure-aktiviteten i din ADF-pipeline.

    ```sql
        CREATE PROCEDURE [dbo].[sp_insert_partition_JobRunTime_success] @PartPrefix varchar(255), @JobRunTime datetime, @SuccessOrFailure bit
        AS
        BEGIN
            INSERT INTO s3_partition_delta_control_table (PartitionPrefix, JobRunTime, SuccessOrFailure)
            VALUES
            (@PartPrefix,@JobRunTime,@SuccessOrFailure)
        END
        GO
    ```


3. Gå till **Kopiera delta-data från AWS S3 till Azure Data Lake Storage Gen2** -mallen. Ange anslutningarna till den externa kontroll tabellen, AWS S3 som data källans lager och Azure Data Lake Storage Gen2 som mål arkiv. Tänk på att den externa kontroll tabellen och den lagrade proceduren refererar till samma anslutning.

    ![Skapa en ny anslutning](media/solution-template-migration-s3-azure/delta-migration-s3-azure1.png)

4. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-migration-s3-azure/delta-migration-s3-azure2.png)
    
5. Du ser 2 pipeliner och 3 data uppsättningar skapades, som du ser i följande exempel:

    ![Granska pipelinen](media/solution-template-migration-s3-azure/delta-migration-s3-azure3.png)

6.  Gå till pipeline "DeltaCopyFromS3" och välj **Felsök** och ange **parametrarna**. Välj sedan **Slutför**.

    ![Klicka på * * Felsök * *](media/solution-template-migration-s3-azure/delta-migration-s3-azure4.png)

7. Du ser resultat som liknar följande exempel:

    ![Granska resultatet](media/solution-template-migration-s3-azure/delta-migration-s3-azure5.png)

8. Du kan också kontrol lera resultatet från kontroll tabellen med en fråga *"Välj * från s3_partition_delta_control_table"*. resultatet ser ut ungefär som i följande exempel:

    ![Skärm bild som visar resultatet från kontroll tabellen efter att du kört frågan.](media/solution-template-migration-s3-azure/delta-migration-s3-azure6.png)
    
## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera containrar](solution-template-copy-files-multiple-containers.md)
- [Flytta filer](solution-template-move-files.md)
