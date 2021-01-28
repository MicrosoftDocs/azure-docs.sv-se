---
title: Java-användardefinierad funktion (UDF) med Apache Hive Azure HDInsight
description: Lär dig hur du skapar en Java-baserad användardefinierad funktion (UDF) som fungerar med Apache Hive. I det här exemplet kan UDF konvertera en tabell med text strängar till gemener.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 11/20/2019
ms.openlocfilehash: bf9c2ea544c6b510a0507b6b020f0eae0b101dc7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946562"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Använda en Java UDF med Apache Hive i HDInsight

Lär dig hur du skapar en Java-baserad användardefinierad funktion (UDF) som fungerar med Apache Hive. Java UDF i det här exemplet konverterar en tabell med text strängar till gemener.

## <a name="prerequisites"></a>Förutsättningar

* Ett Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Apache maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt versions system för Java-projekt.
* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta skulle vara wasb://för Azure Storage, abfs://för Azure Data Lake Storage Gen2 eller adl://för Azure Data Lake Storage Gen1. Om säker överföring har Aktiver ATS för Azure Storage är URI: n `wasbs://` .  Se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

* En text redigerare eller Java IDE

    > [!IMPORTANT]  
    > Om du skapar python-filer på en Windows-klient måste du använda en redigerare som använder LF som linje slut. Om du inte är säker på om ditt redigerings program använder LF eller CRLF, kan du läsa mer i avsnittet [fel sökning](#troubleshooting) för hur du tar bort CR-tecknen.

## <a name="test-environment"></a>Test miljö

Miljön som används för den här artikeln var en dator som kör Windows 10.  Kommandona kördes i en kommando tolk och de olika filerna redigerades med anteckningar. Ändra detta för din miljö.

I en kommando tolk anger du följande kommandon för att skapa en fungerande miljö:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Skapa ett exempel på Java UDF

1. Skapa ett nytt Maven-projekt genom att ange följande kommando:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    Det här kommandot skapar en katalog med namnet `exampleudf` , som innehåller maven-projektet.

2. När projektet har skapats tar du bort den `exampleudf/src/test` katalog som skapades som en del av projektet genom att ange följande kommando:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Öppna `pom.xml` genom att ange kommandot nedan:

    ```cmd
    notepad pom.xml
    ```

    Ersätt sedan den befintliga `<dependencies>` posten med följande XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Dessa poster anger vilken version av Hadoop och Hive som ingår i HDInsight 3,6. Du kan hitta information om de versioner av Hadoop och Hive som medföljer HDInsight från [versions](../hdinsight-component-versioning.md) dokumentet för HDInsight-komponenten.

    Lägg till ett `<build>` avsnitt före `</project>` raden i slutet av filen. Det här avsnittet ska innehålla följande XML:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
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
        </plugins>
    </build>
    ```

    Dessa poster definierar hur projektet ska skapas. Mer specifikt är den version av Java som används i projektet och hur du skapar en uberjar för distribution till klustret.

    Spara filen när ändringarna har gjorts.

4. Ange kommandot nedan för att skapa och öppna en ny fil `ExampleUDF.java` :

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Kopiera och klistra sedan in Java-koden nedan i den nya filen. Stäng sedan filen.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Den här koden implementerar en UDF som accepterar ett sträng värde och returnerar en gemen version av strängen.

## <a name="build-and-install-the-udf"></a>Bygg och installera UDF

I kommandona nedan ersätter `sshuser` du med det faktiska användar namnet om det skiljer sig. Ersätt `mycluster` med det faktiska kluster namnet.

1. Kompilera och paketera UDF genom att ange följande kommando:

    ```cmd
    mvn compile package
    ```

    Det här kommandot skapar och paketerar UDF- `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar` filen i-filen.

2. Använd `scp` kommandot för att kopiera filen till HDInsight-klustret genom att ange följande kommando:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Anslut till klustret med SSH genom att ange följande kommando:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. Kopiera jar-filen till HDInsight-lagring från den öppna SSH-sessionen.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Använd UDF från Hive

1. Starta Beeline-klienten från SSH-sessionen genom att ange följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Det här kommandot förutsätter att du har använt standard **administratören** för inloggnings kontot för klustret.

2. När du kommer till `jdbc:hive2://localhost:10001/>` prompten anger du följande för att lägga till UDF-filen i Hive och exponera den som en funktion.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Använd UDF för att konvertera värden som hämtas från en tabell till gemener.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    Den här frågan väljer tillstånd från tabellen, konverterar strängen till gemener och visar dem sedan tillsammans med det oförändrade namnet. Utdata ser ut ungefär som i följande text:

    ```output
    +---------------+---------------+--+
    |  exampleudf   |     state     |
    +---------------+---------------+--+
    | california    | California    |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | pennsylvania  | Pennsylvania  |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | colorado      | Colorado      |
    | utah          | Utah          |
    | utah          | Utah          |
    | colorado      | Colorado      |
    +---------------+---------------+--+
    ```

## <a name="troubleshooting"></a>Felsökning

När du kör Hive-jobbet kan du komma över ett fel som liknar följande text:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Det här problemet kan bero på att raden slutar i python-filen. Många Windows-redigerare använder som standard CRLF som linje slut, men Linux-program förväntar sig vanligt vis LF.

Du kan använda följande PowerShell-uttryck för att ta bort CR-tecknen innan du laddar upp filen till HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Nästa steg

Andra sätt att arbeta med Hive finns i [använda Apache Hive med HDInsight](hdinsight-use-hive.md).

Mer information om Hive User-Defined Functions finns i avsnittet [Apache Hive operatörer och User-Defined funktioner](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) i Hive wiki på Apache.org.