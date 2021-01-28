---
title: Skapa Java-MapReduce för Apache Hadoop – Azure HDInsight
description: Lär dig hur du använder Apache Maven för att skapa ett Java-baserat MapReduce-program och sedan köra det med Hadoop på Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 7c1a6623883cbee46ba98982808f3c392dc50ffa
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946656"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>Utveckla Java MapReduce-program för Apache Hadoop i HDInsight

Lär dig hur du använder Apache Maven för att skapa ett Java-baserat MapReduce-program och sedan kör det med Apache Hadoop på Azure HDInsight.

## <a name="prerequisites"></a>Förutsättningar

* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Apache maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt versions system för Java-projekt.

## <a name="configure-development-environment"></a>Konfigurera utvecklingsmiljön

Miljön som används för den här artikeln var en dator som kör Windows 10. Kommandona kördes i en kommando tolk och de olika filerna redigerades med anteckningar. Ändra detta för din miljö.

I en kommando tolk anger du följande kommandon för att skapa en fungerande miljö:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

1. Ange följande kommando för att skapa ett Maven-projekt med namnet **wordcountjava**:

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    Det här kommandot skapar en katalog med namnet som anges av `artifactID` parametern (**wordcountjava** i det här exemplet). Den här katalogen innehåller följande objekt:

    * `pom.xml` – [Projekt objekts modellen (POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html) som innehåller information och konfigurations information som används för att bygga projektet.
    * src\main\java\org\apache\hadoop\examples: innehåller din program kod.
    * src\test\java\org\apache\hadoop\examples: innehåller tester för ditt program.

1. Ta bort den genererade exempel koden. Ta bort de genererade test-och programfilerna `AppTest.java` och `App.java` genom att ange följande kommandon:

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Uppdatera projekt objekts modellen

En fullständig referens till pom.xml-filen finns i https://maven.apache.org/pom.html . Öppna `pom.xml` genom att ange kommandot nedan:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Lägg till beroenden

I `pom.xml` lägger du till följande text i `<dependencies>` avsnittet:

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

Detta definierar nödvändiga bibliotek (listade i &lt; artifactId \> ) med en angiven version (visas i &lt; version \> ). Vid kompileringen laddas dessa beroenden ned från standard lagrings platsen för maven. Du kan använda Sök funktionen för [maven-lagringsplatsen](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) för att visa mer.

Talar om för `<scope>provided</scope>` maven att dessa beroenden inte ska paketeras med programmet, som de tillhandahålls av HDInsight-klustret vid körnings tillfället.

> [!IMPORTANT]
> Den version som används måste matcha den version av Hadoop som finns på klustret. Mer information om versioner finns i [versions](../hdinsight-component-versioning.md) dokumentet för HDInsight-komponenten.

### <a name="build-configuration"></a>Skapa konfiguration

Med maven-plugin-program kan du anpassa projektets Bygg steg. Det här avsnittet används för att lägga till plugin-program, resurser och andra Bygg konfigurations alternativ.

Lägg till följande kod i `pom.xml` filen och spara och stäng sedan filen. Texten måste vara inuti `<project>...</project>` taggarna i filen, till exempel mellan `</dependencies>` och `</project>` .

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

I det här avsnittet konfigureras Apache maven compiler-plugin och Apache maven Shader. Compiler-plugin-programmet används för att kompilera topologin. Skugga-plugin-programmet används för att förhindra licens duplicering i JAR-paketet som skapats av Maven. Det här plugin-programmet används för att förhindra fel vid körning av "dubbla licensfiler" vid körning i HDInsight-klustret. Att använda maven-Shader-plugin med `ApacheLicenseResourceTransformer` implementeringen förhindrar felet.

Maven-Shader-plugin-programmet producerar också en Uber-jar som innehåller alla beroenden som krävs av programmet.

Spara filen `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Skapa MapReduce-programmet

1. Ange kommandot nedan för att skapa och öppna en ny fil `WordCount.java` . Välj **Ja** vid prompten om du vill skapa en ny fil.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. Kopiera och klistra sedan in Java-koden nedan i den nya filen. Stäng sedan filen.

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    Observera att paket namnet är `org.apache.hadoop.examples` och att klass namnet är `WordCount` . Du använder dessa namn när du skickar MapReduce-jobbet.

## <a name="build-and-package-the-application"></a>Bygg och paketera programmet

Från `wordcountjava` katalogen använder du följande kommando för att bygga en jar-fil som innehåller programmet:

```cmd
mvn clean package
```

Det här kommandot rensar alla tidigare build-artefakter, laddar ned eventuella beroenden som inte redan har installerats och sedan skapar och paketerar programmet.

När kommandot har slutförts `wordcountjava/target` innehåller katalogen en fil med namnet `wordcountjava-1.0-SNAPSHOT.jar` .

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar`Filen är en uberjar som inte bara innehåller WORDCOUNT-jobbet, utan även beroenden som krävs för jobbet vid körning.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Överför JAR-och körnings jobb (SSH)

Följande steg används `scp` för att kopiera jar till den primära huvudnoden i din Apache HBase i HDInsight-klustret. `ssh`Kommandot används sedan för att ansluta till klustret och köra exemplet direkt på Head-noden.

1. Överför jar till klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-kluster namn och ange sedan följande kommando:

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Anslut till klustret. Ersätt `CLUSTERNAME` med ditt HDInsight-kluster namn och ange sedan följande kommando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från SSH-sessionen använder du följande kommando för att köra MapReduce-programmet:

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    Det här kommandot startar WordCount MapReduce-programmet. Indatafilen är `/example/data/gutenberg/davinci.txt` och utdata-katalogen är `/example/data/wordcountout` . Både indatafilen och utdata lagras i standard lagrings utrymmet för klustret.

1. När jobbet har slutförts använder du följande kommando för att visa resultaten:

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Du bör få en lista över ord och antal, med värden som liknar följande text:

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du utvecklar ett Java-MapReduce-jobb. Se följande dokument för andra sätt att arbeta med HDInsight.

* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hdinsight-use-mapreduce.md)
* [Java-utvecklingscenter](https://azure.microsoft.com/develop/java/)