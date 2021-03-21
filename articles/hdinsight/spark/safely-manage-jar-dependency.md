---
title: Hantera jar-beroenden på ett säkert sätt – Azure HDInsight
description: Den här artikeln beskriver metod tips för att hantera ett JAR-beroende (Java Archive) för HDInsight-program.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: 9868e32665c89bbe9aadc06f1c2834704e6534e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942388"
---
# <a name="safely-manage-jar-dependencies"></a>Hantera Jar-beroenden säkert

Komponenter som är installerade i HDInsight-kluster har beroenden på bibliotek från tredje part. En speciell version av vanliga moduler som Guava refereras vanligt vis till av dessa inbyggda komponenter. När du skickar ett program med dess beroenden kan det orsaka en konflikt mellan olika versioner av samma modul. Om den komponent version som du refererar till i classpath först kan inbyggda komponenter utlösa undantag på grund av versions inkompatibilitet. Men om inbyggda komponenter matar in sina beroenden till Klasen först, kan ditt program utlösa fel som `NoSuchMethod` .

För att undvika versions konflikt bör du överväga att skugga dina program beroenden.

## <a name="what-does-package-shading-mean"></a>Vad betyder paket skuggning?
Skuggning är ett sätt att inkludera och byta namn på beroenden. De omplacerar klasserna och skriver om påverkade bytekod och resurser för att skapa en privat kopia av dina beroenden.

## <a name="how-to-shade-a-package"></a>Hur du skuggar ett paket?

### <a name="use-uber-jar"></a>Använda Uber-jar
Uber-jar är en enskild jar-fil som innehåller både programmet jar och dess beroenden. Beroendena i Uber-jar är som standard inte skuggade. I vissa fall kan detta medföra versions konflikter om andra komponenter eller program refererar till en annan version av dessa bibliotek. För att undvika detta kan du bygga en Uber-Jar-fil med vissa (eller alla) beroenden skuggade.

### <a name="shade-package-using-maven"></a>Skugga paket med maven
Maven kan bygga program skrivna både i Java och Scala. Maven-Shader-plugin-programmet kan hjälpa dig att skapa en skuggad Uber-jar enkelt.

Exemplet nedan visar en fil `pom.xml` som har uppdaterats för att skugga ett paket med maven-Shader-plugin.  XML-avsnittet `<relocation>…</relocation>` flyttar klasser från paketet `com.google.guava` till paketet `com.google.shaded.guava` genom att flytta motsvarande jar-filposter och skriva om den aktuella bytekod-filen.

När du har ändrat `pom.xml` kan du köra `mvn package` för att bygga den skuggade Uber-burken.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Skugga paket med SBT
SBT är också ett build-verktyg för Scala och Java. SBT har inte något skuggat plugin-program som maven-Shader-plugin. Du kan ändra `build.sbt` filen för att skugga paket. 

Om du till exempel vill skugga kan `com.google.guava` du lägga till nedanstående kommando i `build.sbt` filen:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Sedan kan du köra `sbt clean` och `sbt assembly` skapa den skuggade jar-filen. 

## <a name="next-steps"></a>Nästa steg

* [Använda HDInsight IntelliJ-verktyg](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

* [Skapa ett Scala maven-program för Spark i IntelliJ](./apache-spark-create-standalone-application.md)