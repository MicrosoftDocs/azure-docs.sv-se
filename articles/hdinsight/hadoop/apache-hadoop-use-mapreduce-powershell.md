---
title: Använda MapReduce och PowerShell med Apache Hadoop – Azure HDInsight
description: Lär dig hur du använder PowerShell för att fjärrköra MapReduce-jobb med Apache Hadoop på HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939683"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Kör MapReduce-jobb med Apache Hadoop på HDInsight med hjälp av PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell för att köra ett MapReduce-jobb i ett Hadoop i HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Se [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* PowerShell- [modulen för AZ](/powershell/azure/) är installerad.

## <a name="run-a-mapreduce-job"></a>Kör ett MapReduce-jobb

Azure PowerShell tillhandahåller *cmdletar* som gör att du kan fjärrköra MapReduce-jobb i HDInsight. Internt gör PowerShell REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (tidigare kallat Templeton) som körs i HDInsight-klustret.

Följande cmdletar används när du kör MapReduce-jobb i ett fjärran slutet HDInsight-kluster.

|Cmdlet | Beskrivning |
|---|---|
|Connect-AzAccount|Autentiserar Azure PowerShell till din Azure-prenumeration.|
|New-AzHDInsightMapReduceJobDefinition|Skapar en ny *jobb definition* med hjälp av den angivna MapReduce-informationen.|
|Start-AzHDInsightJob|Skickar jobb definitionen till HDInsight och startar jobbet. Ett *jobb* objekt returneras.|
|Wait-AzHDInsightJob|Använder jobbobjektet för att kontrol lera jobbets status. Den väntar tills jobbet har slutförts eller tills vänte tiden har överskridits.|
|Get-AzHDInsightJobOutput|Används för att hämta utdata för jobbet.|

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb i HDInsight-klustret.

1. Använd en redigerare och spara följande kod som **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Öppna en ny **Azure PowerShell** kommando tolk. Ändra kataloger till **mapreducejob.ps1** filens plats och Använd sedan följande kommando för att köra skriptet:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    När du kör skriptet uppmanas du att ange namnet på HDInsight-klustret och kluster inloggningen. Du kan också uppmanas att autentisera till din Azure-prenumeration.

3. När jobbet har slutförts visas utdata som liknar följande text:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Utdata indikerar att jobbet har slutförts.

    > [!NOTE]  
    > Om **ExitCode** är ett annat värde än 0, se [fel sökning](#troubleshooting).

    I det här exemplet lagras även hämtade filer till en **output.txt** -fil i den katalog som du kör skriptet från.

### <a name="view-output"></a>Visa utdata

Om du vill se de ord och antal som genereras av jobbet öppnar du **output.txt** -filen i en text redigerare.

> [!NOTE]  
> Utdatafilerna för ett MapReduce-jobb är oföränderliga. Så om du kör det här exemplet igen måste du ändra namnet på utdatafilen.

## <a name="troubleshooting"></a>Felsökning

Visa fel för jobbet om ingen information returneras när jobbet har slutförts. Om du vill visa fel information för det här jobbet lägger du till följande kommando i slutet av **mapreducejob.ps1** -filen. Spara sedan filen och kör skriptet igen.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Denna cmdlet returnerar den information som skrevs till STDERR när jobbet körs.

## <a name="next-steps"></a>Nästa steg

Som du kan se är Azure PowerShell ett enkelt sätt att köra MapReduce-jobb i ett HDInsight-kluster, övervaka jobb statusen och hämta utdata. Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)