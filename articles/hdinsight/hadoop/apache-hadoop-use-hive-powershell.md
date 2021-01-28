---
title: Använda Apache Hive med PowerShell i HDInsight – Azure
description: Använd PowerShell för att köra Apache Hive frågor i Apache Hadoop i Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 43cac81717027b39210d6cd9cb9ad0baa596f50d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943190"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Kör Apache Hive frågor med PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Det här dokumentet innehåller ett exempel på hur du använder Azure PowerShell för att köra Apache Hive frågor i ett Apache Hadoop på HDInsight-kluster.

> [!NOTE]  
> Det här dokumentet innehåller ingen detaljerad beskrivning av vad HiveQL-satserna som används i exemplen gör. Information om HiveQL som används i det här exemplet finns i [använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* PowerShell- [modulen för AZ](/powershell/azure/) är installerad.

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

Azure PowerShell tillhandahåller *cmdletar* som gör att du kan köra Hive-frågor på HDInsight på distans. Internt gör cmdletarna REST-anrop till [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) i HDInsight-klustret.

Följande cmdletar används vid körning av Hive-frågor i ett fjärran slutet HDInsight-kluster:

* `Connect-AzAccount`: Autentiserar Azure PowerShell till din Azure-prenumeration.
* `New-AzHDInsightHiveJobDefinition`: Skapar en *jobb definition* med hjälp av de angivna HiveQL-satserna.
* `Start-AzHDInsightJob`: Skickar jobb definitionen till HDInsight och startar jobbet. Ett *jobb* objekt returneras.
* `Wait-AzHDInsightJob`: Använder jobbobjektet för att kontrol lera jobbets status. Den väntar tills jobbet har slutförts eller tills vänte tiden har överskridits.
* `Get-AzHDInsightJobOutput`: Används för att hämta utdata för jobbet.
* `Invoke-AzHDInsightHiveJob`: Används för att köra HiveQL-instruktioner. Denna cmdlet förhindrar att frågan slutförs och returnerar sedan resultaten.
* `Use-AzHDInsightCluster`: Anger det aktuella klustret som ska användas för `Invoke-AzHDInsightHiveJob` kommandot.

Följande steg visar hur du använder dessa cmdlets för att köra ett jobb i HDInsight-klustret:

1. Använd en redigerare och spara följande kod som `hivejob.ps1` .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Öppna en ny **Azure PowerShell** kommando tolk. Ändra kataloger till `hivejob.ps1` filens plats och Använd sedan följande kommando för att köra skriptet:

    ```azurepowershell
    .\hivejob.ps1
    ```

    När skriptet körs uppmanas du att ange kluster namnet och autentiseringsuppgifterna för HTTPS/Cluster-administratörskontot. Du kan också uppmanas att logga in på din Azure-prenumeration.

3. När jobbet har slutförts returneras information som liknar följande text:

    ```output
    Display the standard output...
    2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
    2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
    2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
    ```

4. Som tidigare nämnts `Invoke-Hive` kan användas för att köra en fråga och vänta på svaret. Använd följande skript för att se hur Invoke-Hive fungerar:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Utdata ser ut som följande text:

    ```output
    2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
    2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
    2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
    ```

   > [!NOTE]  
   > Du kan använda Azure PowerShell **hit-Strings-** cmdlet eller HiveQL-skriptfiler för längre HiveQL frågor. Följande fragment visar hur du använder `Invoke-Hive` cmdleten för att köra en HiveQL-skript fil. Skript filen HiveQL måste överföras till wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Mer information om **här – strängar** finns [här – strängar](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#here-strings).

## <a name="troubleshooting"></a>Felsökning

Visa fel loggarna om ingen information returneras när jobbet har slutförts. Om du vill visa fel information för det här jobbet lägger du till följande i slutet av `hivejob.ps1` filen, sparar det och kör det igen.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Denna cmdlet returnerar den information som skrivs till STDERR under jobb bearbetningen.

## <a name="summary"></a>Sammanfattning

Som du kan se är Azure PowerShell ett enkelt sätt att köra Hive-frågor i ett HDInsight-kluster, övervaka jobb statusen och hämta utdata.

## <a name="next-steps"></a>Nästa steg

Allmän information om Hive i HDInsight:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
