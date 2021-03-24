---
title: Hantera Spark-programberoenden i Azure HDInsight
description: Den här artikeln innehåller en introduktion till hur du hanterar Spark-beroenden i HDInsight Spark-kluster för PySpark-och Scala-program.
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: c950903522d42b3c279cb89f3a6031043fd49bf3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868808"
---
# <a name="manage-spark-application-dependencies"></a>Hantera Spark-programberoenden

I den här artikeln får du lära dig hur du hanterar beroenden för dina Spark-program som körs på HDInsight. Vi omfattar både Scala och PySpark i Spark-program och kluster omfång.

Använd snabb Länkar för att gå till avsnittet baserat på ditt användar ärende:
* [Konfigurera en spark-jobb jar-beroenden med Jupyter Notebook](#use-jupyter-notebook)
* [Konfigurera ett Spark-jobb jar-beroenden med hjälp Azure Toolkit for IntelliJ](#use-azure-toolkit-for-intellij)
* [Konfigurera jar-beroenden för Spark-kluster](#jar-libs-for-cluster)
* [Hantera Jar-beroenden säkert](#safely-manage-jar-dependencies)
* [Konfigurera ett Spark-jobb python-paket med Jupyter Notebook](#use-jupyter-notebook-1)
* [Hantera python-paket för Spark-kluster på ett säkert sätt](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Jar-libs för ett Spark-jobb
### <a name="use-jupyter-notebook"></a>Använd Jupyter Notebook
När en spark-session startar i Jupyter Notebook på Spark-kernel för Scala, kan du konfigurera paket från:

* [Maven-lagringsplats](https://search.maven.org/)eller community-paket som har bidragit till [Spark-paket](https://spark-packages.org/).
* Jar-filer som lagras i klustrets primära lagring.

Du använder `%%configure` Magic för att konfigurera antecknings boken för att använda ett externt paket. I antecknings böcker som använder externa paket, se till att du anropar `%%configure` Magic i den första kod cellen. Detta säkerställer att kerneln har kon figurer ATS för att använda paketet innan sessionen startar.

>
>[!IMPORTANT]  
>Om du glömmer att konfigurera kärnan i den första cellen kan du använda `%%configure` med `-f` -parametern, men den startar om sessionen och all förloppet går förlorad.

**Exempel på paket från maven-lagringsplatsen eller Spark-paket**

När du har hittat paketet från maven-lagringsplatsen samlar du in **värdena för**, **ArtifactId** och **version**. Sammanfoga de tre värdena, avgränsade med kolon (**:**).

   :::image type="content" source="./media/apache-spark-manage-dependencies/spark-package-schema.png " alt-text="Sammanfoga paket schema schema" border="true":::vbildningspaket schema "Border =" true "::

Kontrol lera att värdena som du samlar in matchar klustret. I det här fallet använder vi Spark Cosmos DB Connector-paketet för Scala 2,11 och Spark 2,3 för HDInsight 3,6 Spark-kluster. Om du inte är säker kan du köra `scala.util.Properties.versionString` i Code-cellen på Spark-kärnan för att hämta klustrets Scala-version. Kör `sc.version` för att hämta kluster Spark-version.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Exempel på jar v7 som lagras i Primär lagring**

Använd [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för JAR-filer på klustrets primära lagring. Detta `wasb://` gäller Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Azure Storage eller Data Lake Storage Gen2, skulle URI: n vara `wasbs://` eller `abfss://` . Se [säker överföring](../../storage/common/storage-require-secure-transfer.md).

Använd kommaavgränsad lista över jar-sökvägar för flera jar-filer, globs tillåts. Jar v7 finns på driv rutins-och utförar-classpath.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

När du har konfigurerat externa paket kan du köra importera i Code-cellen för att kontrol lera om paketen har placerats på rätt sätt.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Använd Azure Toolkit for IntelliJ
[Azure Toolkit for IntelliJ-plugin](./apache-spark-intellij-tool-plugin.md) innehåller gränssnitts upplevelse för att skicka Spark Scala-program till ett HDInsight-kluster. Den innehåller `Referenced Jars` och `Referenced Files` Egenskaper för att konfigurera jar libs-sökvägar när du skickar Spark-programmet. Se mer information om [hur du använder Azure Toolkit for IntelliJ plugin-programmet för HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="Dialog rutan för Spark-sändning" border="true":::

## <a name="jar-libs-for-cluster"></a>Jar-libs för kluster
I vissa fall kanske du vill konfigurera jar-beroenden på kluster nivå så att alla program kan konfigureras med samma beroenden som standard. Metoden är att lägga till jar-sökvägar till Spark-drivrutinen och utförar-klassens sökväg.

1. Kör nedan exempel skript åtgärder för att kopiera jar-filer från primär lagring `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` till kluster lokalt fil system `/usr/libs/sparklibs` . Steget krävs eftersom Linux använder `:` sig av för att avgränsa klass Sök vägs listan, men HDInsight stöder bara lagrings Sök vägar med schema som `wasb://` . Sökvägen till fjärrlagringsplatsen fungerar inte korrekt om du lägger till den direkt i klass Sök vägen.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Ändra konfiguration av Spark-tjänsten från Ambari för att uppdatera klass Sök vägen. Gå till **Ambari > Spark > config > anpassade Spark2-standardvärden**. **Lägg till egenskap** enligt följande. Använd `:` för att avgränsa sökvägar om du har fler än en sökväg som ska läggas till. Globs är tillåtna.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   :::image type="content" source="./media/apache-spark-manage-dependencies/change-spark-default-config.png " alt-text="Ändra Spark standard config" border="true":::ULT config "Border =" true "::

3. Spara de ändrade konfigurationerna och starta om tjänster som påverkas.

   :::image type="content" source="./media/apache-spark-manage-dependencies/restart-impacted-services.png " alt-text="Starta om tjänster" border="true":::valda Services "Border =" true "::

Du kan automatisera stegen med [skript åtgärder](../hdinsight-hadoop-customize-cluster-linux.md). Skript åtgärder för [att lägga till anpassade Hive-bibliotek](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) är en korrekt referens. När du ändrar konfiguration av Spark-tjänsten ska du kontrol lera att du använder Ambari-API: er i stället för att ändra konfigurationsfilerna direkt. 

## <a name="safely-manage-jar-dependencies"></a>Hantera Jar-beroenden säkert
HDInsight-kluster har inbyggda jar-beroenden och uppdateringar för dessa JAR-versioner sker från tid till tid. För att undvika versions konflikter mellan inbyggda jar v7 och jar v7 som du använder som referens, bör du överväga att [skugga dina program beroenden](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Python-paket för ett Spark-jobb
### <a name="use-jupyter-notebook"></a>Använd Jupyter Notebook
HDInsight Jupyter Notebook PySpark-kernel stöder inte installation av python-paket från PyPi eller Anaconda-paketets lagrings plats direkt. Om du har `.zip` , `.egg` eller `.py` beroenden och vill referera till dem för en spark-session, följer du stegen nedan:

1. Kör nedan exempel skript åtgärder för att `.zip` Kopiera `.egg` eller `.py` filer från primär lagring `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` till kluster lokalt fil system `/usr/libs/pylibs` . Steget krävs eftersom Linux använder `:` sig av för att avgränsa Sök vägs listan, men HDInsight stöder bara lagrings Sök vägar med schema som `wasb://` . Sökvägen till fjärrlagringsplatsen fungerar inte som den ska när du använder `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. Kör följande kod i en Code-cell med PySpark-kernel i din bärbara dator:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Kör `import` för att kontrol lera om dina paket har inkluderats korrekt.  

## <a name="python-packages-for-cluster"></a>Python-paket för kluster
Du kan installera python-paket från Anaconda till klustret med kommandot Conda via skript åtgärder. De installerade paketen finns på kluster nivå och gäller för alla program. 

HDInsight Spark-kluster har två inbyggda python-installationer, Anaconda python 2,7 och Anaconda python 3,5. Om du vill veta mer om standardinställningarna för python för tjänster och hur du på ett säkert sätt installerar externa python-paket utan att behöva bryta klustret, se mer information i [säker hantering av python-beroenden för klustret](./apache-spark-python-package-installation.md).
