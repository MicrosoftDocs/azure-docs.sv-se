---
title: Använda MapReduce och sväng med Apache Hadoop i HDInsight – Azure
description: Lär dig hur du fjärrköra MapReduce-jobb med Apache Hadoop i HDInsight med hjälp av sväng.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: e90dc2c7220caf5bd72b7086adc275934652e150
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939700"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Kör MapReduce-jobb med Apache Hadoop på HDInsight med REST

Lär dig hur du använder Apache Hive WebHCat-REST API för att köra MapReduce-jobb på en Apache Hadoop på HDInsight-kluster. Sväng används för att demonstrera hur du kan interagera med HDInsight genom att använda RAW HTTP-begäranden för att köra MapReduce-jobb.

> [!NOTE]  
> Om du redan är bekant med att använda Linux-baserade Hadoop-servrar, men du är nybörjare på HDInsight, kan du läsa [vad du behöver veta om Linux-baserade Apache Hadoop på HDInsight-](../hdinsight-hadoop-linux-information.md) dokument.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Se [skapa Apache Hadoop kluster med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Antingen:
  * Windows PowerShell eller,
  * [Sväng](https://curl.haxx.se/) med [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Kör ett MapReduce-jobb

> [!NOTE]  
> När du använder en sväng eller någon annan REST-kommunikation med WebHCat måste du autentisera begär Anden genom att ange användar namnet och lösen ordet för HDInsight-klustret. Du måste använda kluster namnet som en del av den URI som används för att skicka begär anden till servern.
>
> REST API skyddas med hjälp av [grundläggande åtkomst-autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). Du bör alltid göra begär Anden genom att använda HTTPS för att säkerställa att dina autentiseringsuppgifter skickas på ett säkert sätt till servern.

### <a name="curl"></a>Curl

1. Ange variablerna nedan för enkel användning. Det här exemplet baseras på en Windows-miljö och ändras efter behov för din miljö.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Använd följande kommando från en kommandorad för att verifiera att du kan ansluta till ditt HDInsight-kluster:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    De parametrar som används i det här kommandot är följande:

   * **-u**: anger det användar namn och lösen ord som används för att autentisera begäran
   * **-G**: visar att den här åtgärden är en get-begäran

   I början av URI: n, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` är samma för alla begär Anden.

    Du får ett svar som liknar följande JSON:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Om du vill skicka ett MapReduce-jobb använder du följande kommando. Ändra sökvägen till **JQ** efter behov.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Slutet av URI: n (/MapReduce/jar) anger WebHCat att den här begäran startar ett MapReduce-jobb från en klass i en jar-fil. De parametrar som används i det här kommandot är följande:

   * **-d**: `-G` används inte, så standardvärdet för begäran till post-metoden. `-d` anger de data värden som skickas med begäran.
     * **User.name**: användaren som kör kommandot
     * **jar**: platsen för den jar-fil som innehåller klassen som ska köras
     * **klass**: klassen som innehåller MapReduce-logiken
     * **arg**: argumenten som ska skickas till MapReduce-jobbet. I det här fallet är indatafilen och den katalog som används för utdata

    Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrol lera jobbets status: `job_1415651640909_0026` .

1. Använd följande kommando för att kontrol lera jobbets status. Ersätt värdet för `JOBID` med det **faktiska** värdet som returnerades i föregående steg. Ändra plats för **JQ** efter behov.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Ange variablerna nedan för enkel användning. Ersätt `CLUSTERNAME` med det faktiska kluster namnet. Kör kommandot och ange lösen ordet för kluster inloggning när du uppmanas till det.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Använd följande kommando för att kontrol lera att du kan ansluta till ditt HDInsight-kluster:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Du får ett svar som liknar följande JSON:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Om du vill skicka ett MapReduce-jobb använder du följande kommando:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Slutet av URI: n (/MapReduce/jar) anger WebHCat att den här begäran startar ett MapReduce-jobb från en klass i en jar-fil. De parametrar som används i det här kommandot är följande:

    * **User.name**: användaren som kör kommandot
    * **jar**: platsen för den jar-fil som innehåller klassen som ska köras
    * **klass**: klassen som innehåller MapReduce-logiken
    * **arg**: argumenten som ska skickas till MapReduce-jobbet. I det här fallet är indatafilen och den katalog som används för utdata

   Det här kommandot ska returnera ett jobb-ID som kan användas för att kontrol lera jobbets status: `job_1415651640909_0026` .

1. Använd följande kommando för att kontrol lera jobbets status:

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing

    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

### <a name="both-methods"></a>Båda metoderna

1. Om jobbet har slutförts är det tillstånd som returneras `SUCCEEDED` .

1. När jobbets tillstånd har ändrats till `SUCCEEDED` kan du hämta resultatet från Azure Blob Storage. Den `statusdir` parameter som skickas med frågan innehåller platsen för utdatafilen. I det här exemplet är platsen `/example/curl` . Den här adressen lagrar utdata från jobbet i kluster standard lagringen på `/example/curl` .

Du kan visa och hämta dessa filer med hjälp av [Azure CLI](/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI för att arbeta med Azure Blob Storage finns i [snabb start: skapa, ladda ned och lista blobar med Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Nästa steg

Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

Mer information om REST-gränssnittet som används i den här artikeln finns i referens för [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
