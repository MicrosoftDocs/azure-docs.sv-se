---
title: Använda Apache Hadoop Hive med en sväng i HDInsight – Azure
description: Lär dig att fjärrskicka Apache gris-jobb till Azure HDInsight med hjälp av sväng.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 124661c57f779b9f8a639debcc093ed15e717694
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946470"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Kör Apache Hive frågor med Apache Hadoop i HDInsight med REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Lär dig hur du använder WebHCat-REST API för att köra Apache Hive frågor med Apache Hadoop på Azure HDInsight-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Hadoop kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En REST-klient. I det här dokumentet används [Invoke-webbegäran](/powershell/module/microsoft.powershell.utility/invoke-webrequest) på Windows PowerShell och [sväng](https://curl.haxx.se/) på [bash](/windows/wsl/install-win10).

* Om du använder bash behöver du också JQ, en JSON-processor på kommando raden.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

## <a name="base-uri-for-rest-api"></a>Bas-URI för REST API

Bas Uniform Resource Identifier (URI) för REST API på HDInsight är `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , där `CLUSTERNAME` är namnet på klustret.  Kluster namn i URI: er är Skift läges **känsliga**.  Kluster namnet i det fullständigt kvalificerade domän namnet (FQDN) i URI: n () är Skift läges okänsligt `CLUSTERNAME.azurehdinsight.net` , men andra förekomster i URI: n är Skift läges känsliga.

## <a name="authentication"></a>Autentisering

När du använder en sväng eller någon annan REST-kommunikation med WebHCat, måste du autentisera förfrågningarna genom att ange användar namn och lösen ord för HDInsight-klustrets administratör. REST API skyddas via [grundläggande autentisering](https://en.wikipedia.org/wiki/Basic_access_authentication). För att se till att dina autentiseringsuppgifter skickas på ett säkert sätt till servern ska du alltid göra begär Anden med hjälp av säker HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Installations program (bevara autentiseringsuppgifter)

Behåll dina autentiseringsuppgifter för att undvika att behöva ange dem igen för varje exempel.  Kluster namnet kommer att bevaras i ett separat steg.

**A. bash**  
Redigera skriptet nedan genom att ersätta `PASSWORD` med det faktiska lösen ordet.  Ange sedan kommandot.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Kör koden nedan och ange dina autentiseringsuppgifter i popup-fönstret:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifiera korrekt bokstäver-kluster namn

Det faktiska Skift läget i kluster namnet kan skilja sig från förväntat, beroende på hur klustret skapades.  Stegen här visar det faktiska Skift läget och lagrar det sedan i en variabel för alla senare exempel.

Redigera skripten nedan och Ersätt `CLUSTERNAME` med ditt kluster namn. Ange sedan kommandot. (Kluster namnet för FQDN är inte Skift läges känsligt.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

1. Du kan kontrol lera att du kan ansluta till ditt HDInsight-kluster med något av följande kommandon:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Du får ett svar som liknar följande text:

    ```json
    {"status":"ok","version":"v1"}
    ```

    De parametrar som används i det här kommandot är följande:

    * `-u` – Användar namnet och lösen ordet som används för att autentisera begäran.
    * `-G` -Visar att denna begäran är en GET-åtgärd.

1. Början av webb adressen, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` är detsamma för alla begär Anden. Sökvägen, `/status` anger att begäran ska returnera statusen WebHCat (kallas även Templeton) för servern. Du kan också begära Hive-versionen genom att använda följande kommando:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Den här begäran returnerar ett svar som liknar följande text:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Använd följande för att skapa en tabell med namnet **log4jLogs**:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Den här begäran använder POST-metoden som skickar data som en del av begäran till REST API. Följande data värden skickas med begäran:

     * `user.name` – Användaren som kör kommandot.
     * `execute` -HiveQL-uttryck som ska köras.
     * `statusdir` – Den katalog som status för det här jobbet skrivs till.

   Dessa instruktioner utför följande åtgärder:

   * `DROP TABLE` – Om tabellen redan finns tas den bort.
   * `CREATE EXTERNAL TABLE` -Skapar en ny extern tabell i Hive. Externa tabeller lagrar bara tabell definitionen i Hive. Data finns kvar på den ursprungliga platsen.

     > [!NOTE]  
     > Externa tabeller bör användas när du förväntar dig att underliggande data ska uppdateras av en extern källa. Till exempel en automatiserad data överförings process eller en annan MapReduce-åtgärd.
     >
     > Att släppa en extern tabell tar **inte** bort data, endast tabell definitionen.

   * `ROW FORMAT` – Hur data formateras. Fälten i varje logg skiljs åt med ett blank steg.
   * `STORED AS TEXTFILE LOCATION` – Var data lagras (exempel/data-katalogen) och att de lagras som text.
   * `SELECT` – Väljer antalet rader där kolumnen **T4** innehåller värdet **[Error]**. Den här instruktionen returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.

     > [!NOTE]  
     > Observera att blank stegen mellan HiveQL-uttryck ersätts med ett `+` tecken när det används med en sväng. Citerade värden som innehåller ett blank steg, t. ex. avgränsare, ska inte ersättas med `+` .

      Det här kommandot returnerar ett jobb-ID som kan användas för att kontrol lera jobbets status.

1. Använd följande kommando för att kontrol lera jobbets status:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

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

    Om jobbet har slutförts har statusen **slutförts**.

1. När jobbets tillstånd har ändrats till **lyckades** kan du hämta resultatet från Azure Blob Storage. Den `statusdir` parameter som skickades med frågan innehåller platsen för utdatafilen, i det här fallet `/example/rest` . Den här adressen lagrar utdata i `example/curl` katalogen i klustrets standard lagring.

    Du kan visa och hämta dessa filer med hjälp av [Azure CLI](/cli/azure/install-azure-cli). Mer information om hur du använder Azure CLI med Azure Storage finns i [använda Azure CLI med Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md) -dokument.

## <a name="next-steps"></a>Nästa steg

Information om andra sätt att arbeta med Hadoop i HDInsight:

* [Använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Mer information om de REST API som används i det här dokumentet finns i [referens](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) dokumentet för WebHCat.