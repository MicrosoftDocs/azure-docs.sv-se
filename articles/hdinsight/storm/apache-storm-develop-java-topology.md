---
title: Apache Storm exempel på Java-topologi – Azure HDInsight
description: Lär dig hur du skapar Apache Storm-topologier i Java genom att skapa ett exempel på ord räknings topologi.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020,devx-track-java
ms.date: 04/27/2020
ms.openlocfilehash: 620a4e1627b25af22db68173f35924376e26f5f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929130"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Skapa en Apache Storm-topologi i Java

Lär dig hur du skapar en Java-baserad topologi för Apache Storm. Du skapar en Storm-topologi som implementerar ett ord räknings program. Du använder Apache Maven för att bygga och paketera projektet. Sedan kan du lära dig hur du definierar topologin med hjälp av Apache Storm flödes ramverket.

När du har slutfört stegen i det här dokumentet kan du distribuera topologin till Apache Storm i HDInsight.

> [!NOTE]  
> En slutförd version av storm Topology-exemplen som skapats i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount) .

## <a name="prerequisites"></a>Förutsättningar

* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* [Apache maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt versions system för Java-projekt.

## <a name="test-environment"></a>Test miljö

Miljön som används för den här artikeln var en dator som kör Windows 10.  Kommandona kördes i en kommando tolk och de olika filerna redigerades med anteckningar.

I en kommando tolk anger du följande kommandon för att skapa en fungerande miljö:

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

Ange följande kommando för att skapa ett Maven-projekt med namnet **WORDCOUNT**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

Det här kommandot skapar en katalog med namnet `WordCount` på den aktuella platsen, som innehåller ett Basic Maven-projekt. Det andra kommandot ändrar den aktuella arbets katalogen till `WordCount` . Det tredje kommandot skapar en ny katalog, `resources` som kommer att användas senare.  `WordCount`Katalogen innehåller följande objekt:

* `pom.xml`: Innehåller inställningar för maven-projektet.
* `src\main\java\com\microsoft\example`: Innehåller din program kod.
* `src\test\java\com\microsoft\example`: Innehåller tester för ditt program.  

### <a name="remove-the-generated-example-code"></a>Ta bort den genererade exempel koden

Ta bort de genererade test-och programfilerna `AppTest.java` och `App.java` genom att ange följande kommandon:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Lägg till maven-databaser

HDInsight baseras på Hortonworks Data Platform (HDP), så vi rekommenderar att du använder Hortonworks-lagringsplatsen för att hämta beroenden för dina Apache Storm-projekt.  

Öppna `pom.xml` genom att ange kommandot nedan:

```cmd
notepad pom.xml
```

Lägg sedan till följande XML efter `<url>https://maven.apache.org</url>` raden:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>Lägg till egenskaper

Med maven kan du definiera värden på projekt nivå som kallas egenskaper. I `pom.xml` lägger du till följande text efter `</repositories>` raden:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

Du kan nu använda det här värdet i andra avsnitt i `pom.xml` . När du till exempel anger versionen av storm-komponenter kan du använda `${storm.version}` i stället för hårdkoda ett värde.

## <a name="add-dependencies"></a>Lägg till beroenden

Lägg till ett beroende för Storm-komponenter. I `pom.xml` lägger du till följande text i `<dependencies>` avsnittet:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

Vid kompileringen använder maven den här informationen för att leta upp `storm-core` i maven-lagringsplatsen. Den söker först i databasen på den lokala datorn. Om filerna inte finns där, hämtar maven dem från den offentliga maven-lagringsplatsen och lagrar dem i den lokala lagrings platsen.

> [!NOTE]  
> Lägg märke till `<scope>provided</scope>` raden i det här avsnittet. Den här inställningen anger att maven ska utesluta **Storm-Core** från alla jar-filer som skapas, eftersom det tillhandahålls av systemet.

## <a name="build-configuration"></a>Skapa konfiguration

Med maven-plugin-program kan du anpassa projektets Bygg steg. Till exempel hur projektet kompileras eller hur det paketeras i en JAR-fil. I `pom.xml` lägger du till följande text direkt ovanför `</project>` raden.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

Det här avsnittet används för att lägga till plugin-program, resurser och andra Bygg konfigurations alternativ. En fullständig referens till `pom.xml` filen finns i [https://maven.apache.org/pom.html](https://maven.apache.org/pom.html) .

### <a name="add-plug-ins"></a>Lägg till plugin-program

* **Exec maven-plugin**

    För Apache Storm topologier som implementeras i Java är [exec maven-pluginprogrammet](https://www.mojohaus.org/exec-maven-plugin/) användbart eftersom det gör att du enkelt kan köra topologin lokalt i utvecklings miljön. Lägg till följande i `<plugins>` avsnittet i `pom.xml` filen för att ta med plugin-programmet exec maven:

    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
        <executions>
            <execution>
                <goals>
                    <goal>exec</goal>
                </goals>
            </execution>
        </executions>
        <configuration>
            <executable>java</executable>
            <includeProjectDependencies>true</includeProjectDependencies>
            <includePluginDependencies>false</includePluginDependencies>
            <classpathScope>compile</classpathScope>
            <mainClass>${storm.topology}</mainClass>
            <cleanupDaemonThreads>false</cleanupDaemonThreads>
        </configuration>
    </plugin>
    ```

* **Apache maven compiler-plugin**

    Ett annat användbart plugin-program är [`Apache Maven Compiler Plugin`](https://maven.apache.org/plugins/maven-compiler-plugin/) , som används för att ändra Compilation-alternativ. Ändra den Java-version som maven använder för källan och målet för ditt program.

  * För HDInsight __3,4 eller tidigare__ anger du käll-och mål-Java-versionen till __1,7__.

  * För HDInsight __3,5__ anger du käll-och mål-Java-versionen till __1,8__.

  Lägg till följande text i `<plugins>` avsnittet i `pom.xml` filen för att inkludera Apache maven compiler-plugin-programmet. Det här exemplet anger 1,8, så målets HDInsight-version är 3,5.

  ```xml
  <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
            <source>1.8</source>
            <target>1.8</target>
    </configuration>
  </plugin>
  ```

### <a name="configure-resources"></a>Konfigurera resurser

I avsnittet resurser kan du inkludera icke-kod resurser, till exempel konfigurationsfiler som krävs av komponenter i topologin. I det här exemplet lägger du till följande text i `<resources>` avsnittet i `pom.xml` filen. Spara sedan och stäng filen.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
            <include>log4j2.xml</include>
    </includes>
</resource>
```

Det här exemplet lägger till resurs katalogen i projekt roten ( `${basedir}` ) som en plats som innehåller resurser och innehåller filen med namnet `log4j2.xml` . Den här filen används för att konfigurera vilken information som loggas av topologin.

## <a name="create-the-topology"></a>Skapa topologin

En Java-baserad Apache Storm-topologi består av tre komponenter som du måste redigera (eller referens) som ett beroende.

* **Kanaler**: läser data från externa källor och utvärderar data strömmar i topologin.

* **Bultar**: bearbetar data strömmar som har avsänts av kanaler eller andra bultar och avger en eller flera strömmar.

* **Topologi**: definierar hur kanaler och bultarna är ordnade och tillhandahåller start punkten för topologin.

### <a name="create-the-spout"></a>Skapa kanalen

För att minska kraven för att konfigurera externa data källor, avger följande kanalen bara slumpmässiga meningar. Det är en modifierad version av en kanalen som finns i [exemplen Storm-starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).  Även om den här topologin använder en kanalen kan andra ha flera som matar in data från olika källor i topologin.`.`

Ange kommandot nedan för att skapa och öppna en ny fil `RandomSentenceSpout.java` :

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

Kopiera och klistra sedan in Java-koden nedan i den nya filen.  Stäng sedan filen.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

import java.util.Map;
import java.util.Random;

//This spout randomly emits sentences
public class RandomSentenceSpout extends BaseRichSpout {
  //Collector used to emit output
  SpoutOutputCollector _collector;
  //Used to generate a random number
  Random _rand;

  //Open is called when an instance of the class is created
  @Override
  public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
  //Set the instance collector to the one passed in
    _collector = collector;
    //For randomness
    _rand = new Random();
  }

  //Emit data to the stream
  @Override
  public void nextTuple() {
  //Sleep for a bit
    Utils.sleep(100);
    //The sentences that are randomly emitted
    String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
        "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
    //Randomly pick a sentence
    String sentence = sentences[_rand.nextInt(sentences.length)];
    //Emit the sentence
    _collector.emit(new Values(sentence));
  }

  //Ack is not implemented since this is a basic example
  @Override
  public void ack(Object id) {
  }

  //Fail is not implemented since this is a basic example
  @Override
  public void fail(Object id) {
  }

  //Declare the output fields. In this case, an sentence
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("sentence"));
  }
}
```

> [!NOTE]  
> Ett exempel på en kanalen som läser från en extern data källa finns i något av följande exempel:
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): ett exempel på en kanalen som läser från Twitter.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): en kanalen som läser från Kafka.

### <a name="create-the-bolts"></a>Skapa bultarna

Bultar hanterar data bearbetningen. Bultar kan göra vad som helst, t. ex. beräkning, persistence eller pratar med externa komponenter. Den här topologin använder två bultar:

* **SplitSentence**: delar upp meningarna som avsänts av **RandomSentenceSpout** i enskilda ord.

* **WORDCOUNT**: räknar hur många gånger varje ord har inträffat.

#### <a name="splitsentence"></a>SplitSentence

Ange kommandot nedan för att skapa och öppna en ny fil `SplitSentence.java` :

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

Kopiera och klistra sedan in Java-koden nedan i den nya filen.  Stäng sedan filen.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class SplitSentence extends BaseBasicBolt {

  //Execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //Get the sentence content from the tuple
    String sentence = tuple.getString(0);
    //An iterator to get each word
    BreakIterator boundary=BreakIterator.getWordInstance();
    //Give the iterator the sentence
    boundary.setText(sentence);
    //Find the beginning first word
    int start=boundary.first();
    //Iterate over each word and emit it to the output stream
    for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
      //get the word
      String word=sentence.substring(start,end);
      //If a word is whitespace characters, replace it with empty
      word=word.replaceAll("\\s+","");
      //if it's an actual word, emit it
      if (!word.equals("")) {
        collector.emit(new Values(word));
      }
    }
  }

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

Ange kommandot nedan för att skapa och öppna en ny fil `WordCount.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

Kopiera och klistra sedan in Java-koden nedan i den nya filen.  Stäng sedan filen.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>Definiera topologin

Topologin kopplar ihop kanaler och bultarna i ett diagram. Diagrammet definierar hur data flödar mellan komponenterna. Den innehåller också parallella tips som Storm använder när de skapar instanser av komponenterna i klustret.

Följande bild är ett grundläggande diagram över komponenterna i den här topologin.

![diagram över arrangemanget kanaler och bultar](./media/apache-storm-develop-java-topology/word-count-topology1.png)

Implementera topologin genom att ange kommandot nedan för att skapa och öppna en ny fil `WordCountTopology.java` :

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

Kopiera och klistra sedan in Java-koden nedan i den nya filen.  Stäng sedan filen.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

import com.microsoft.example.RandomSentenceSpout;

public class WordCountTopology {

  //Entry point for the topology
  public static void main(String[] args) throws Exception {
  //Used to build the topology
    TopologyBuilder builder = new TopologyBuilder();
    //Add the spout, with a name of 'spout'
    //and parallelism hint of 5 executors
    builder.setSpout("spout", new RandomSentenceSpout(), 5);
    //Add the SplitSentence bolt, with a name of 'split'
    //and parallelism hint of 8 executors
    //shufflegrouping subscribes to the spout, and equally distributes
    //tuples (sentences) across instances of the SplitSentence bolt
    builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
    //Add the counter, with a name of 'count'
    //and parallelism hint of 12 executors
    //fieldsgrouping subscribes to the split bolt, and
    //ensures that the same word is sent to the same instance (group by field 'word')
    builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

    //new configuration
    Config conf = new Config();
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

    //If there are arguments, we are running on a cluster
    if (args != null && args.length > 0) {
      //parallelism hint to set the number of workers
      conf.setNumWorkers(3);
      //submit the topology
      StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
    }
    //Otherwise, we are running locally
    else {
      //Cap the maximum number of executors that can be spawned
      //for a component to 3
      conf.setMaxTaskParallelism(3);
      //LocalCluster is used to run locally
      LocalCluster cluster = new LocalCluster();
      //submit the topology
      cluster.submitTopology("word-count", conf, builder.createTopology());
      //sleep
      Thread.sleep(10000);
      //shut down the cluster
      cluster.shutdown();
    }
  }
}
```

### <a name="configure-logging"></a>Konfigurera loggning

Storm använder [Apache log4j 2](https://logging.apache.org/log4j/2.x/) för att logga information. Om du inte konfigurerar loggning, avger topologin diagnostikinformation. Du styr vad som loggas genom att skapa en fil med namnet `log4j2.xml` i `resources` katalogen genom att ange följande kommando:

```cmd
notepad resources\log4j2.xml
```

Kopiera och klistra sedan in XML-texten nedan i den nya filen.  Stäng sedan filen.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
</Configuration>
```

Den här XML-koden konfigurerar en ny loggare för `com.microsoft.example` klassen, som innehåller komponenterna i den här exempel sto pol Ogin. Nivån är inställd på spårning för den här loggaren, som samlar in all loggnings information som har avsänts av komponenter i den här topologin.

I `<Root level="error">` avsnittet konfigureras rot nivån för loggning (allting Not in `com.microsoft.example` ) för att endast Logga fel information.

Mer information om hur du konfigurerar loggning för log4j 2 finns i [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html) .

> [!NOTE]  
> Storm version 0.10.0 och högre använder Log4J 2. x. Äldre versioner av storm använde log4j 1. x, som använde ett annat format för logg konfiguration. Information om den äldre konfigurationen finns i [https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat](https://cwiki.apache.org/confluence/display/LOGGINGLOG4J/Log4jXmlFormat) .

## <a name="test-the-topology-locally"></a>Testa topologin lokalt

När du har sparat filerna använder du följande kommando för att testa topologin lokalt.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

När den körs visar topologin start information. Följande text är ett exempel på resultatet av ordet Count:

```output
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow
```

I den här exempel loggen visas att ordet och har genererats 113 gånger. Antalet fortsätter att öka så länge topologin körs. Den här ökningen beror på att kanalen kontinuerligt avger samma meningar.

Det finns ett intervall på 5 sekunder mellan utsläpp av ord och antal. **WORDCOUNT** -komponenten har kon figurer ATS för att endast generera information när en Ticket tas emot. Den begär att Ticket-tupler bara levereras var femte sekund.

## <a name="convert-the-topology-to-flux"></a>Konvertera topologin till flöde

[Flöde](https://storm.apache.org/releases/2.0.0/flux.html) är ett nytt ramverk som är tillgängligt med storm 0.10.0 och högre. Med flöde kan du skilja konfigurationen från implementeringen. Dina komponenter definieras fortfarande i Java, men topologin definieras med hjälp av en YAML-fil. Du kan paketera en standard definition av topologin med ditt projekt eller använda en fristående fil när du skickar topologin. När du skickar topologin till storm använder du miljövariabler eller konfigurationsfiler för att fylla i YAML.

YAML-filen definierar de komponenter som ska användas för topologin och data flödet mellan dem. Du kan inkludera en YAML-fil som en del av jar-filen. Du kan också använda en extern YAML-fil.

Mer information om flöde finns i [flödes ramverket https://storm.apache.org/releases/current/flux.html) (](https://storm.apache.org/releases/current/flux.html).

> [!WARNING]  
> På grund av ett [fel https://issues.apache.org/jira/browse/STORM-2055) (](https://issues.apache.org/jira/browse/STORM-2055) med storm 1.0.1 kan du behöva installera en [Storm utvecklings miljö](https://storm.apache.org/releases/current/Setting-up-development-environment.html) för att köra flödes topologier lokalt.

1. Tidigare `WordCountTopology.java` definierade topologi, men det behövs inte med flöde. Ta bort filen med följande kommando:

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

1. Ange kommandot nedan för att skapa och öppna en ny fil `topology.yaml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    Kopiera och klistra sedan in texten nedan i den nya filen.  Stäng sedan filen.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
           topology.workers: 1     # Hint for the number of workers to create
  
    spouts:                 # Spout definitions
    - id: "sentence-spout"
           className: "com.microsoft.example.RandomSentenceSpout"
           parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
           className: "com.microsoft.example.SplitSentence"
           parallelism: 1

    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 10
           parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
           from: "sentence-spout"       # The stream emitter
           to: "splitter-bolt"          # The stream consumer
           grouping:                    # Grouping type
             type: SHUFFLE

    - name: "Splitter -> Counter"
           from: "splitter-bolt"
           to: "counter-bolt"
           grouping:
             type: FIELDS
             args: ["word"]           # field(s) to group on
    ```

1. Ange kommandot nedan för att öppna `pom.xml` för att göra de beskrivna revisionerna nedan:

    ```cmd
    notepad pom.xml
    ```

   1. Lägg till följande nya beroende i `<dependencies>` avsnittet:

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   1. Lägg till följande plugin-program i `<plugins>` avsnittet. Det här plugin-programmet hanterar skapandet av ett paket (jar-fil) för projektet och tillämpar vissa transformeringar som är speciella för flöden när du skapar paketet.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   1. I avsnittet exec maven-plugin navigerar du till `<configuration>`  >  `<mainClass>` och ändrar `${storm.topology}` till `org.apache.storm.flux.Flux` . Med den här inställningen kan flöden hantera körning av topologin lokalt under utveckling.

   1. I `<resources>` avsnittet lägger du till följande i `<includes>` . Den här XML-filen innehåller YAML-filen som definierar topologin som en del av projektet.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>Testa flödes miljön lokalt

1. Ange följande kommando för att kompilera och köra flödes miljön med maven:

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > Om din topologi använder Storm 1.0.1-bitar, Miss lyckas det här kommandot. Det här felet orsakas av [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055) . Installera i stället [storm i utvecklings miljön](https://storm.apache.org/releases/current/Setting-up-development-environment.html) och Använd följande steg:
    >
    > Om du har [installerat storm i utvecklings miljön](https://storm.apache.org/releases/current/Setting-up-development-environment.html)kan du använda följande kommandon i stället:
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local`Parametern kör topologin i lokalt läge i utvecklings miljön. `-R /topology.yaml`Parametern använder `topology.yaml` fil resursen från jar-filen för att definiera topologin.

    När den körs visar topologin start information. Följande text är ett exempel på utdata:

    ```
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    ```

    Det finns en fördröjning på 10 sekunder mellan batchar med loggad information.

2. Skapa en ny topologi yaml från projektet.

    1. Ange kommandot nedan för att öppna `topology.xml` :

    ```cmd
    notepad resources\topology.yaml
    ```

    1. Sök efter följande avsnitt och ändra värdet för `10` till `5` . Den här ändringen ändrar intervallet mellan sändning av ord antal från 10 sekunder till 5.  

    ```yaml
    - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
             - 5
           parallelism: 1  
    ```

    1. Spara filen som `newtopology.yaml` .

3. Kör topologin genom att ange följande kommando:

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    Eller, om du har storm i utvecklings miljön:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

    Det här kommandot använder `newtopology.yaml` som topologin definition. Eftersom vi inte inkluderade `compile` parametern, använder maven den version av projektet som skapades i föregående steg.

    När topologin startar bör du märka att tiden mellan genererade batchar har ändrats för att återspegla värdet i `newtopology.yaml` . Så du kan se att du kan ändra konfigurationen genom en YAML-fil utan att behöva kompilera om topologin.

Mer information om dessa och andra funktioner i flödes ramverket finns i [flöde ( https://storm.apache.org/releases/current/flux.html) ](https://storm.apache.org/releases/current/flux.html).

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) är en abstraktion på hög nivå som tillhandahålls av storm. Den stöder tillstånds känslig bearbetning. Den främsta fördelen med Trident är att det garanterar att alla meddelanden som anges i topologin bara bearbetas en gång. Utan att använda Trident kan topologin bara garantera att meddelanden bearbetas minst en gång. Det finns även andra skillnader, till exempel inbyggda komponenter som kan användas i stället för att skapa bultar. Bultar ersätts av mindre generiska komponenter, till exempel filter, projektioner och funktioner.

Det går att skapa Trident-program med Maven-projekt. Du använder samma grundläggande steg som beskrivs tidigare i den här artikeln – endast koden är annorlunda. Det går inte heller att använda Trident (för närvarande) med flödes ramverket.

Mer information om Trident finns i [Översikt över Trident API](https://storm.apache.org/releases/current/Trident-API-Overview.html).

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du skapar en Apache Storm topologi med Java. Lär dig nu att:

* [Distribuera och hantera Apache Storm-topologier i HDInsight](apache-storm-deploy-monitor-topology-linux.md)

* [Utveckla topologier med Python](apache-storm-develop-python-topology.md)

Du kan hitta fler exempel Apache Storm topologier genom [att besöka exempel topologier för Apache storm i HDInsight](apache-storm-example-topology.md).