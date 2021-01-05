---
title: Använd livy Spark för att skicka jobb till Spark-kluster i Azure HDInsight
description: Lär dig hur du använder Apache Spark REST API för att skicka Spark-jobb via fjärr anslutning till ett Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: b5795172517ba2e707d66ebe486c51d31575bd0d
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821884"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Använda REST-API:et för Apache Spark för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster

Lär dig hur du använder [Apache livy](https://livy.incubator.apache.org/), Apache Spark REST API, som används för att skicka Fjärrjobb till ett Azure HDInsight Spark-kluster. Detaljerad dokumentation finns i [Apache livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Du kan använda livy för att köra interaktiva Spark-gränssnitt eller skicka batch-jobb som ska köras i Spark. Den här artikeln pratar om hur du använder livy för att skicka batch-jobb. Kodfragmenten i den här artikeln använder en sväng för att göra REST API anrop till livy Spark-slutpunkten.

## <a name="prerequisites"></a>Förutsättningar

Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Skicka ett batch-jobb för Apache livy Spark

Innan du skickar ett batch-jobb måste du ladda upp programmet jar på kluster lagringen som är associerad med klustret. Du kan använda kommandoradsverktyget [AzCopy](../../storage/common/storage-use-azcopy-v10.md) till att göra detta. Det finns olika andra klienter som du kan använda för att överföra data. Det finns mer information om dem i [Ladda upp data för Apache Hadoop-jobb i HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Exempel

* Om jar-filen finns på kluster lagringen (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Om du vill skicka jar-filnamnet och klass namnet som en del av en indatafil (i det här exemplet input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Hämta information om livy Spark-batchar som körs i klustret

Syntax:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Exempel

* Om du vill hämta alla livy Spark-batchar som körs i klustret:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Om du vill hämta en specifik batch med ett angivet batch-ID

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Ta bort ett livy Spark batch-jobb

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Exempel

Ta bort ett batch-jobb med batch-ID `5` .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark och hög tillgänglighet

Livy ger hög tillgänglighet för Spark-jobb som körs i klustret. Här är några exempel.

* Om livy-tjänsten slutar att fungera när du har skickat ett jobb till ett Spark-kluster fortsätter jobbet att köras i bakgrunden. När livy är säkerhets kopie rad återställer den jobbets status och rapporterar det igen.
* Jupyter-anteckningsböcker för HDInsight drivs av livy i Server delen. Om en bärbar dator kör ett Spark-jobb och livy-tjänsten startas om fortsätter antecknings boken att köra kod cellerna.

## <a name="show-me-an-example"></a>Visa ett exempel

I det här avsnittet tittar vi på exempel för att använda livy Spark för att skicka batch-jobb, övervaka jobbets förlopp och ta sedan bort det. Programmet som vi använder i det här exemplet är det som utvecklades i artikeln [skapa ett fristående Scala-program och köra på HDInsight Spark-kluster](apache-spark-create-standalone-application.md). Stegen här förutsätts:

* Du har redan kopierat över Application jar till det lagrings konto som är associerat med klustret.
* Du har spiral installerat på den dator där du provar de här stegen.

Utför följande steg:

1. Ange miljövariabler för enkel användning. Det här exemplet baseras på en Windows-miljö och ändrar variabler efter behov för din miljö. Ersätt `CLUSTERNAME` och `PASSWORD` med lämpliga värden.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Kontrol lera att livy Spark körs i klustret. Vi kan göra det genom att hämta en lista med att köra batchar. Om du kör ett jobb med livy för första gången ska utdata returnera noll.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Du bör få utdata som liknar följande kodfragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observera att den sista raden i utdata visar **Total: 0**, vilket innebär att inga batchar körs.

1. Låt oss nu skicka ett batch-jobb. I följande kodfragment används en indatafil (input.txt) för att skicka jar-namnet och klass namnet som parametrar. Om du kör de här stegen från en Windows-dator rekommenderar vi att du använder en indatafil.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Parametrarna i filen **input.txt** definieras enligt följande:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observera hur den sista raden i utdata säger **status: Started**. Det heter också **ID: 0**. Här är **0** batch-ID.

1. Nu kan du hämta statusen för den här batchen med batch-ID: t.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Utdata visar nu **status: lyckades**, vilket tyder på att jobbet har slutförts.

1. Om du vill kan du nu ta bort batchen.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Du bör se utdata som liknar följande fragment:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Den sista raden i utdata visar att batchen har tagits bort. Om du tar bort ett jobb medan det körs, omsorg det också. Om du tar bort ett jobb som har slutförts, eller på annat sätt, tar bort jobb informationen helt.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Uppdateringar av livy-konfiguration från och med HDInsight 3,5-version

HDInsight 3,5-kluster och högre, som standard, inaktivera användning av lokala fil Sök vägar för att få åtkomst till exempel data-filer eller jar v7. Vi rekommenderar att du använder `wasbs://` sökvägen i stället för att komma åt jar v7 eller exempelfilerna från klustret.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Skicka livy-jobb för ett kluster i ett virtuellt Azure-nätverk

Om du ansluter till ett HDInsight Spark-kluster från en Azure-Virtual Network, kan du ansluta direkt till livy i klustret. I sådana fall är URL: en för livy-slutpunkten `http://<IP address of the headnode>:8998/batches` . Här är **8998** den port där livy körs på klustrets huvudnoden. Mer information om hur du ansluter till tjänster på icke-offentliga portar finns i [portar som används av Apache Hadoop Services i HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Nästa steg

* [Apache livy REST API-dokumentation](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)