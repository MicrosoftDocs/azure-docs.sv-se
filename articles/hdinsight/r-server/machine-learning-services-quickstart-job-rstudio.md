---
title: 'Snabb start: RStudio Server & ML-tjänster för R-Azure HDInsight'
description: I snabb starten kör du ett R-skript i ett ML Services-kluster i Azure HDInsight med hjälp av RStudio Server.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 8abd485bff885868d7865cf273cb161974106013
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104862841"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Snabb start: köra ett R-skript i ett ML Services-kluster i Azure HDInsight med RStudio-Server

Med ML-tjänster i Azure HDInsight kan R-skript använda Apache Spark och Apache Hadoop MapReduce för att köra distribuerade beräkningar. ML-tjänster styr hur anrop utförs genom att ange beräknings kontexten. Edge-noden i ett kluster är en praktisk plats för att ansluta till klustret och köra R-skript. Med en Edge-nod kan du välja att köra de parallella distribuerade funktionerna i RevoScaleR över kärnorna i Edge-nodens Server. Du kan också köra dem på noderna i klustret genom att använda RevoScaleR för att minska eller Apache Spark Compute-kontexter.

I den här snabb starten får du lära dig hur du kör ett R-skript med RStudio-server som visar hur du använder Spark för distribuerade R-beräkningar. Du definierar en beräknings kontext för att utföra beräkningar lokalt på en Edge-nod och återigen distribuerade över noderna i HDInsight-klustret.

## <a name="prerequisite"></a>Förutsättning

Ett ML Services-kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj ml- **tjänster** för **kluster typ**.

## <a name="connect-to-rstudio-server"></a>Anslut till RStudio Server

RStudio-servern körs på klustrets Edge-nod. Gå till följande URL där `CLUSTERNAME` är namnet på det ml Services-kluster som du skapade:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Första gången du loggar in måste du autentisera dig två gånger. För den första autentiseringen anger du inloggning och lösen ord för kluster administratören. standard är `admin` . För den andra verifierings frågan anger du SSH-inloggning och lösen ord. standard är `sshuser` . Efterföljande inloggningar kräver bara SSH-autentiseringsuppgifter.

När du är ansluten, bör din skärm likna följande skärmbild:

:::image type="content" source="./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png" alt-text="Översikt över R Studio-webbkonsoler" border="true":::

## <a name="use-a-compute-context"></a>Använda en beräkningskontext

1. Från RStudio Server använder du följande kod för att läsa in exempel data till standard lagringen för HDInsight:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Det här steget kan ta cirka 8 minuter att slutföra.

1. Skapa viss data information och definiera två data källor. Ange följande kod i RStudio:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Kör en logistik regression över data med hjälp av den **lokala** beräknings kontexten. Ange följande kod i RStudio:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Beräkningarna bör slutföras på ungefär 7 minuter. Du bör se utdata som slutar med rader som liknar följande kodfragment:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Kör samma logistiska regression med **Spark** -kontexten. Spark-kontexten distribuerar bearbetningen mellan alla arbetsnoder i HDInsight-klustret. Ange följande kod i RStudio:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Beräkningarna bör utföras på ungefär 5 minuter.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabb starten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster läser du [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du kör ett R-skript med RStudio-servern som demonstreras med Spark för distribuerade R-beräkningar.  Gå vidare till nästa artikel för att lära dig vilka alternativ som är tillgängliga för att ange om och hur körningen ska vara parallell över kärnor i Edge-noden eller HDInsight-klustret.

> [!div class="nextstepaction"]
>[Alternativ för beräknings kontext för ML-tjänster i HDInsight](./r-server-compute-contexts.md)

> [!NOTE]
> Den här sidan beskriver funktionerna i RStudio-programvaran. Microsoft Azure HDInsight är inte kopplad till RStudio, Inc.
