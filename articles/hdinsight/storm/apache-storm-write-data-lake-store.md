---
title: 'Självstudie: HDInsight Apache Storm till lagring – Azure/Data Lake'
description: Självstudie – lär dig hur du använder Apache Storm för att skriva till den HDFS-kompatibla lagringen för Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932557"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Självstudie: skriva till Apache Hadoop HDFS från Apache Storm på Azure HDInsight

Den här självstudien visar hur du använder Apache Storm för att skriva data till den HDFS-kompatibla lagring som används av Apache Storm i HDInsight. HDInsight kan använda både Azure Storage och Azure Data Lake Storage som HDFS-kompatibel lagring. Storm tillhandahåller en [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) -komponent som skriver data till HDFS. Det här dokumentet innehåller information om hur du skriver till valfri typ av lagring från HdfsBolt.

Den topologi som används i det här dokumentet förlitar sig på komponenter som ingår i storm på HDInsight. Det kan krävas ändringar för att fungera med Azure Data Lake Storage när det används med andra Apache Storm-kluster.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera klustret med skript åtgärd
> * Bygg och paketera topologin
> * Distribuera och köra topologin
> * Visa utdata
> * Stoppa topologin

## <a name="prerequisites"></a>Förutsättningar

* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt versions system för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta `wasb://` gäller Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Azure Storage är URI: n `wasbs://` .  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Exempelkonfiguration

Följande YAML är ett utdrag från `resources/writetohdfs.yaml` filen som ingår i exemplet. Den här filen definierar Storm-topologin med [flödes](https://storm.apache.org/releases/current/flux.html) ramverket för Apache storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Den här YAML definierar följande objekt:

* `syncPolicy`: Definierar när filer synkroniseras/töms i fil systemet. I det här exemplet var 1000 tupler.
* `fileNameFormat`: Definierar sökväg och fil namns mönster som ska användas vid skrivning av filer. I det här exemplet anges sökvägen vid körning med ett filter och fil namns tillägget är `.txt` .
* `recordFormat`: Definierar det interna formatet för de filer som skrivs. I det här exemplet är fälten avgränsade med `|` specialtecknet.
* `rotationPolicy`: Definierar när filer ska roteras. I det här exemplet utförs ingen rotation.
* `hdfs-bolt`: Använder de tidigare komponenterna som konfigurations parametrar för `HdfsBolt` klassen.

Mer information om flödes ramverket finns i [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) .

## <a name="configure-the-cluster"></a>Konfigurera klustret

Storm på HDInsight innehåller som standard inte de komponenter som `HdfsBolt` används för att kommunicera med Azure Storage eller data Lake Storage i Storms Klas Sök väg. Använd följande skript åtgärd för att lägga till dessa komponenter i `extlib` katalogen för storm i klustret:

| Egenskap | Värde |
|---|---|
|Skript typ |– Anpassad|
|Bash-skript-URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Node-typ (er) |Nimbus, ansvarig|
|Parametrar |Ingen|

Information om hur du använder det här skriptet med klustret finns i avsnittet [Anpassa HDInsight-kluster med skript åtgärder](./../hdinsight-hadoop-customize-cluster-linux.md) .

## <a name="build-and-package-the-topology"></a>Bygg och paketera topologin

1. Ladda ned exempel projektet från [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) till utvecklings miljön.

2. Ändra kataloger till roten för det nedladdade projektet från en kommando tolk, Terminal eller Shell-session. Använd följande kommando för att bygga och paketera topologin:

    ```cmd
    mvn compile package
    ```

    När bygget och packningen är slutfört finns det en ny katalog med namnet `target` , som innehåller en fil med namnet `StormToHdfs-1.0-SNAPSHOT.jar` . Den här filen innehåller den kompilerade topologin.

## <a name="deploy-and-run-the-topology"></a>Distribuera och köra topologin

1. Använd följande kommando för att kopiera topologin till HDInsight-klustret. Ersätt `CLUSTERNAME` med namnet på klustret.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. När överföringen är klar använder du följande för att ansluta till HDInsight-klustret med SSH. Ersätt `CLUSTERNAME` med namnet på klustret.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. När du är ansluten använder du följande kommando för att skapa en fil med namnet `dev.properties` :

    ```bash
    nano dev.properties
    ```

1. Använd följande text som `dev.properties` filens innehåll. Ändra efter behov baserat på ditt [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Om du vill spara filen använder du __CTRL + X__, sedan __Y__ och slutligen __RETUR__. Värdena i den här filen anger lagrings-URL: en och katalog namnet som data skrivs till.

1. Använd följande kommando för att starta topologin:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Det här kommandot startar topologin med flödes ramverket genom att skicka den till Nimbus-noden i klustret. Topologin definieras av filen som `writetohdfs.yaml` ingår i burken. `dev.properties`Filen skickas som ett filter och de värden som finns i filen läses av topologin.

## <a name="view-output-data"></a>Visa utdata

Om du vill visa data använder du följande kommando:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

En lista över de filer som skapas av den här topologin visas. I följande lista visas ett exempel på de data som returneras av föregående kommandon:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Stoppa topologin

Storm-topologier körs tills de stoppas, eller så tas klustret bort. Om du vill stoppa topologin använder du följande kommando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade HDInsight-klustret och eventuella andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer sedan __Resursgrupper__ för att visa listan med dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Apache Storm för att skriva data till det HDFS-kompatibla lagrings utrymmet som används av Apache Storm i HDInsight.

> [!div class="nextstepaction"]
> Upptäck andra [Apache Storm exempel för HDInsight](apache-storm-example-topology.md)