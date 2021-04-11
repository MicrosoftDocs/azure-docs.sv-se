---
title: 'Självstudie: använda R i en spark Compute-kontext i Azure HDInsight'
description: Självstudie – kom igång med R och Spark på ett Azure HDInsight Machine Learning Services-kluster.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 07c004fa5f03c53ed0778613a0c16a0ba2aff11f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869505"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Självstudie: använda R i en spark Compute-kontext i Azure HDInsight

Den här självstudien innehåller stegvisa instruktioner för att använda R-funktionerna i Apache Spark som körs på ett Azure HDInsight Machine Learning Services-kluster.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ladda ned exempel data till lokal lagring
> * Kopiera data till standard lagring
> * Konfigurera en data uppsättning
> * Skapa data källor
> * Skapa en beräknings kontext för Spark
> * Anpassa en linjär modell
> * Använda sammansatta XDF-filer
> * Konvertera XDF till CSV

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure HDInsight Machine Learning Services-kluster. Gå till [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ml-tjänster** för **kluster typ**.

## <a name="connect-to-rstudio-server"></a>Anslut till RStudio Server

RStudio-servern körs på klustrets Edge-nod. Gå till följande plats (där *kluster* namn i URL: en är namnet på HDInsight-Machine Learning Services-klustret som du har skapat):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Första gången du loggar in autentiseras du två gånger. Vid den första autentiseringen anger du användar namn och lösen ord för kluster administratören (Standardvärdet är *admin*). Vid den andra autentiseringen anger du SSH-användarnamn och-lösen ord (Standardvärdet är *sshuser*). Efterföljande inloggningar kräver bara SSH-autentiseringsuppgifter.

## <a name="download-the-sample-data-to-local-storage"></a>Ladda ned exempel data till lokal lagring

*Data uppsättningen för flyg bolagets 2012-datauppsättning* består av 12 kommaavgränsade filer som innehåller information om ankomst-och avgångs tider för alla kommersiella flygningar i USA för året 2012. Den här data uppsättningen är stor, med över 6 000 000 observationer.

1. Initiera några miljövariabler. Ange följande kod i RStudio Server-konsolen:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. I den högra rutan väljer du fliken **miljö** . Variablerna visas under **värden**.

    :::image type="content" source="./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png" alt-text="Webb konsol för HDInsight R Studio" border="true":::

1. Skapa en lokal katalog och hämta exempel data. I RStudio anger du följande kod:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Hämtningen ska vara slutförd om 9,5 minuter.

## <a name="copy-the-data-to-default-storage"></a>Kopiera data till standard lagring

Platsen för Hadoop Distributed File System (HDFS) har angetts med `airDataDir` variabeln. I RStudio anger du följande kod:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Steget bör vara klart om 10 sekunder.

## <a name="set-up-a-dataset"></a>Konfigurera en data uppsättning

1. Skapa ett fil system objekt som använder standardvärdena. I RStudio anger du följande kod:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Eftersom de ursprungliga CSV-filerna har hellre svårhanterligt variabel namn, anger du en *colInfo* -lista för att göra dem mer hanterbara. I RStudio anger du följande kod:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Skapa data källor

I en spark Compute-kontext kan du skapa data källor med hjälp av följande funktioner:

|Funktion | Beskrivning |
|---------|-------------|
|`RxTextData` | En kommaavgränsad text data källa. |
|`RxXdfData` | Data i data fil formatet XDF. I RevoScaleR ändras fil formatet XDF för Hadoop för att lagra data i en sammansatt uppsättning filer i stället för en enda fil. |
|`RxHiveData` | Genererar ett data käll objekt för Hive.|
|`RxParquetData` | Genererar ett Parquet-data käll objekt.|
|`RxOrcData` | Genererar ett Orc-data käll objekt.|

Skapa ett [RxTextData](/machine-learning-server/r-reference/revoscaler/rxtextdata) -objekt med hjälp av de filer som du kopierade till HDFS. I RStudio anger du följande kod:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Skapa en beräknings kontext för Spark

Om du vill läsa in data och köra analyser på arbetsnoder ställer du in beräknings kontexten i skriptet på [rxspark beräkningskontexten](/machine-learning-server/r-reference/revoscaler/rxspark). I det här sammanhanget distribuerar R-funktioner automatiskt arbets belastningen för alla arbetsnoder, utan inbyggt krav på hantering av jobb eller kön. Beräknings kontexten Spark upprättas via `RxSpark` eller `rxSparkConnect()` för att skapa Spark Compute-kontexten och används `rxSparkDisconnect()` för att återgå till en lokal beräknings kontext. I RStudio anger du följande kod:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Anpassa en linjär modell

1. Använd funktionen [rxLinMod](/machine-learning-server/r-reference/revoscaler/rxlinmod) för att anpassa en linjär modell med `airDS` data källan. I RStudio anger du följande kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Det här steget bör vara slutfört på 2 till 3 minuter.

1. Granska resultaten. I RStudio anger du följande kod:

    ```R
    summary(delayArr)
    ```

    Du bör se följande resultat:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Resultaten visar att du har bearbetat alla data, 6 000 000 observationer, med alla CSV-filer i den angivna katalogen. Eftersom du har angett `cube = TRUE` har du en uppskattad koefficient för varje veckodag (och inte spärren).

## <a name="use-composite-xdf-files"></a>Använda sammansatta XDF-filer

Som du har sett kan du analysera CSV-filer direkt med R på Hadoop. Men du kan göra analysen snabbare om du lagrar data i ett mer effektivt format. Fil formatet R XDF är effektivt, men det har ändrats något för HDFS så att enskilda filer förblir i ett enda HDFS-block. (HDFS-blockets storlek varierar från installationen till installationen, men är vanligt vis antingen 64 MB eller 128 MB.) 

När du använder [rxImport](/machine-learning-server/r-reference/revoscaler/rximport) på Hadoop för att skapa en uppsättning sammansatta XDF-filer, anger du en `RxTextData` data källa, till exempel `AirDS` som indata och en `RxXdfData` data källa med fil systemet INställt på ett HDFS-filsystem som argument för utdatafilen. Du kan sedan använda `RxXdfData` objektet som data argument i efterföljande R-analyser.

1. Definiera ett `RxXdfData` objekt. I RStudio anger du följande kod:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Ange en block storlek på 250000 rader och ange att vi ska läsa alla data. I RStudio anger du följande kod:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importera data med hjälp av `rxImport` . I RStudio anger du följande kod:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Det här steget bör utföras på några minuter.

1. Beräkna samma linjära modell igen med den nya, snabbare data källan. I RStudio anger du följande kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Steget måste vara klart på mindre än en minut.

1. Granska resultaten. Resultatet bör vara detsamma som från CSV-filerna. I RStudio anger du följande kod:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Konvertera XDF till CSV

### <a name="in-a-spark-context"></a>I en spark-kontext

Om du har konverterat CSV-filerna till XDF-filformatet för ökad effektivitet medan du kör analyserna, men nu vill konvertera data tillbaka till CSV, kan du göra det med hjälp av [rxDataStep](/machine-learning-server/r-reference/revoscaler/rxdatastep).

Skapa en mapp med CSV-filer genom att först skapa ett `RxTextData` objekt med hjälp av ett katalog namn som fil argumentet. Objektet representerar den mapp där CSV-filerna ska skapas. Den här katalogen skapas när du kör `rxDataStep` . Peka sedan på det här `RxTextData` objektet i `outFile` argumentet för `rxDataStep` . Varje CSV som skapas namnges baserat på katalog namnet och följt av ett tal.

Anta att du vill skriva ut en mapp med CSV-filer i HDFS från den `airDataXdf` sammansatta XDF när du har utfört Logistisk regression och förutsägelse, så att de nya CSV-filerna innehåller förväntade värden och rester. I RStudio anger du följande kod:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Det här steget bör vara klart om 2,5 minuter.

Den `rxDataStep` skrev ut en CSV-fil för varje XDFD-fil i den sammansatta XDF-filen. Detta är standard beteendet för att skriva CSV-filer från sammansatta XDF-filer till HDFS när beräknings kontexten är inställd på `RxSpark` .

### <a name="in-a-local-context"></a>I ett lokalt sammanhang

När du har utfört dina analyser kan du ändra din beräknings kontext till `local` för att dra nytta av två argument i `RxTextData` som ger dig något mer kontroll när du skriver CSV-filer till HDFS: `createFileSet` och `rowsPerOutFile` . När du ställer in `createFileSet` på `TRUE` , skrivs en mapp med CSV-filer till den katalog som du anger. När du ställer in `createFileSet` på `FALSE` , skrivs en enskild CSV-fil. Du kan ange det andra argumentet, `rowsPerOutFile` till ett heltal för att ange hur många rader som ska skrivas till varje CSV-fil när `createFileSet` är `TRUE` .

I RStudio anger du följande kod:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Det här steget bör vara klart om 10 minuter.

När du använder en `RxSpark` beräknings kontext, `createFileSet` har standardvärdet `TRUE` och `rowsPerOutFile` har ingen påverkan. Om du vill skapa en enkel CSV eller anpassa antalet rader per fil kan du utföra `rxDataStep` i en `local` beräknings kontext (data kan fortfarande finnas i HDFS).

## <a name="final-steps"></a>De sista stegen

1. Rensa data. I RStudio anger du följande kod:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Stoppa Remote Spark-programmet. I RStudio anger du följande kod:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Avsluta R-sessionen. I RStudio anger du följande kod:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört självstudien kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används.

Om du vill ta bort ett kluster läser du [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder R Functions i Apache Spark som körs på ett HDInsight-Machine Learning Services-kluster. Mer information finns i följande artiklar:

* [Beräknings kontext alternativ för ett Azure HDInsight Machine Learning Services-kluster](r-server-compute-contexts.md)
* [R-funktioner för Spark på Hadoop](/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)