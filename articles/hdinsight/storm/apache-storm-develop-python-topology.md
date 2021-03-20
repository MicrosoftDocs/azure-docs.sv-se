---
title: Apache Storm med python-komponenter – Azure HDInsight
description: Lär dig hur du skapar en Apache Storm-topologi som använder python-komponenter i Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-python
ms.date: 12/16/2019
ms.openlocfilehash: e28d21ed71cf5f485165c639a8bd519b3a2736e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928996"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Utveckla Apache Storm topologier med python i HDInsight

Lär dig hur du skapar en [Apache Storm](https://storm.apache.org/) -topologi som använder python-komponenter. Apache Storm stöder flera språk, även för att du ska kunna kombinera komponenter från flera språk i en topologi. [Flödes](https://storm.apache.org/releases/current/flux.html) ramverket (lanserades med storm 0.10.0) gör att du enkelt kan skapa lösningar som använder python-komponenter.

> [!IMPORTANT]  
> Informationen i det här dokumentet har testats med storm på HDInsight 3,6.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Storm kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **kluster typ**.

* En lokal Storm utvecklings miljö (valfritt). En lokal Storm-miljö behövs bara om du vill köra topologin lokalt. Mer information finns i [Konfigurera en utvecklings miljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2,7 eller högre](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt versions system för Java-projekt.

## <a name="storm-multi-language-support"></a>Storm stöd för flera språk

Apache Storm har utformats för att fungera med komponenter som skrivits med valfritt programmeringsspråk. Komponenterna måste förstå hur man arbetar med Thrift-definitionen för storm. För python tillhandahålls en modul som en del av Apache Storm projektet som gör det enkelt att använda storm. Du hittar den här modulen på [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) .

Storm är en Java-process som körs på Java Virtual Machine (JVM). Komponenter som skrivs på andra språk körs som under processer. Storm kommunicerar med dessa under processer med hjälp av JSON-meddelanden som skickats över STDIN/STDOUT. Mer information om kommunikation mellan komponenter finns i dokumentationen för [multi-lang-protokollet](https://storm.apache.org/releases/current/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python med flödes ramverket

Med flödes ramverket kan du definiera Storm-topologier separat från komponenterna. Flödes ramverket använder YAML för att definiera Storm-topologin. Följande text är ett exempel på hur du refererar till en python-komponent i YAML-dokumentet:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Klassen `FluxShellSpout` används för att starta `sentencespout.py` skriptet som implementerar kanalen.

Sänd flödet förväntar sig att python-skripten ska finnas i `/resources` katalogen i JAR-filen som innehåller topologin. I det här exemplet lagras python-skripten i `/multilang/resources` katalogen. `pom.xml`Innehåller den här filen med följande XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Som tidigare nämnts finns det en `storm.py` fil som implementerar Thrift-definitionen för storm. Flödes ramverket innehåller `storm.py` automatiskt när projektet skapas, så du behöver inte oroa dig för att inkludera det.

## <a name="build-the-project"></a>Bygga projektet

1. Hämta projektet från [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) .

1. Öppna en kommando tolk och navigera till projekt roten: `hdinsight-python-storm-wordcount-master` . Ange följande kommando:

    ```cmd
    mvn clean compile package
    ```

    Det här kommandot skapar en `target/WordCount-1.0-SNAPSHOT.jar` fil som innehåller den kompilerade topologin.

## <a name="run-the-storm-topology-on-hdinsight"></a>Kör Storm-topologin på HDInsight

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att kopiera `WordCount-1.0-SNAPSHOT.jar` filen till din storm på HDInsight-kluster. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. När filen har laddats upp ansluter du till klustret med hjälp av SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från SSH-sessionen använder du följande kommando för att starta topologin i klustret:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    När den har startats körs en Storm-topologi tills den har stoppats.

1. Använd Storm-ANVÄNDARGRÄNSSNITTET för att Visa topologin i klustret. Storm-ANVÄNDARGRÄNSSNITTET finns på `https://CLUSTERNAME.azurehdinsight.net/stormui` . Ersätt `CLUSTERNAME` med ditt kluster namn.

1. Stoppa Storm-topologin. Använd följande kommando för att stoppa topologin i klustret:

    ```bash
    storm kill wordcount
    ```

    Du kan också använda Storm-ANVÄNDARGRÄNSSNITTET. Under **topologins åtgärder** för topologin väljer du **Kill**.

## <a name="run-the-topology-locally"></a>Kör topologin lokalt

Använd följande kommando för att köra topologin lokalt:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Det här kommandot kräver en lokal Storm utvecklings miljö. Mer information finns i [Konfigurera en utvecklings miljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

När topologin startar avger den information till den lokala konsolen som liknar följande text:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Om du vill stoppa topologin använder du __CTRL + C__.

## <a name="next-steps"></a>Nästa steg

Se följande dokument för andra sätt att använda python med HDInsight: [använda python-användardefinierade funktioner (UDF) i Apache gris och Apache Hive](../hadoop/python-udf-hdinsight.md).