---
title: Skript åtgärd för python-paket med Jupyter på Azure HDInsight
description: Stegvisa instruktioner för hur du använder skript åtgärd för att konfigurera Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster för att använda externa python-paket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: 21423f7200d25c9a5b3b63d2371e56977cd9d227
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821383"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Hantera Python-miljön i Azure HDInsight på ett säkert sätt med skriptåtgärd

HDInsight har två inbyggda python-installationer i Spark-klustret, Anaconda python 2,7 och python 3,5. Kunder kan behöva anpassa python-miljön som att installera externa python-paket. Här visar vi bästa praxis för säker hantering av python-miljöer för Apache Spark kluster i HDInsight.

## <a name="prerequisites"></a>Förutsättningar

Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md). Om du inte redan har ett Spark-kluster i HDInsight kan du köra skript åtgärder när klustret skapas. Gå till dokumentationen om [hur du använder anpassade skript åtgärder](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Stöd för program med öppen källkod som används i HDInsight-kluster

Microsoft Azure HDInsights tjänsten använder en miljö med tekniker med öppen källkod som är utformad runt Apache Hadoop. Microsoft Azure ger en allmän support nivå för tekniker med öppen källkod. Mer information finns på [webbplatsen för vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq/). HDInsight-tjänsten ger ytterligare en nivå av stöd för inbyggda komponenter.

Det finns två typer av komponenter med öppen källkod som är tillgängliga i HDInsight-tjänsten:

|Komponent |Beskrivning |
|---|---|
|Inbyggd|Dessa komponenter är förinstallerade i HDInsight-kluster och tillhandahåller kärn funktioner i klustret. Till exempel, Apache Hadoop garn Resource Manager, Apache Hive frågespråket (HiveQL) och Mahout-biblioteket tillhör den här kategorin. En fullständig lista över kluster komponenter finns i [Nyheter i Apache Hadoop kluster versioner från HDInsight](../hdinsight-component-versioning.md).|
|Anpassad|Du, som användare av klustret, kan installera eller använda i din arbets belastning, vilken komponent som helst som är tillgänglig i communityn eller som du har skapat.|

> [!IMPORTANT]
> Komponenter som ingår i HDInsight-klustret stöds fullt ut. Microsoft Support hjälper till att isolera och lösa problem som rör dessa komponenter.
>
> Anpassade komponenter får kommersiellt rimlig support för att hjälpa dig att ytterligare felsöka problemet. Microsoft Support kanske kan lösa problemet, eller så kan de be dig att tillhandahålla tillgängliga kanaler för tekniken med öppen källkod där djupgående expertis för tekniken hittas. Det finns till exempel många community-platser som kan användas, till exempel: [Microsoft Q&en fråge sida för HDInsight](/answers/topics/azure-hdinsight.html) `https://stackoverflow.com` . Även Apache-projekt har projekt webbplatser `https://apache.org` .

## <a name="understand-default-python-installation"></a>Förstå den standardinställda python-installationen

HDInsight Spark-kluster skapas med Anaconda-installation. Det finns två python-installationer i klustret, Anaconda python 2,7 och python 3,5. I tabellen nedan visas standard inställningarna för python för Spark, livy och Jupyter.

|Inställning |Python 2,7|Python 3,5|
|----|----|----|
|Sökväg|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark-version|Standard är inställt på 2,7|Kan ändra konfigurationen till 3,5|
|Livy-version|Standard är inställt på 2,7|Kan ändra konfigurationen till 3,5|
|Jupyter|PySpark-kernel|PySpark3-kernel|

## <a name="safely-install-external-python-packages"></a>Installera externa python-paket på ett säkert sätt

HDInsight-kluster är beroende av den inbyggda python-miljön, både python 2,7 och python 3,5. Om du installerar anpassade paket direkt i de inbyggda standard miljöerna kan det leda till oväntade biblioteks ändringar. Och Bryt klustret ytterligare. För att på ett säkert sätt installera anpassade externa python-paket för dina Spark-program följer du stegen nedan.

1. Skapa en virtuell python-miljö med Conda. En virtuell miljö ger ett isolerat utrymme för dina projekt utan att behöva dela andra. När du skapar den virtuella python-miljön kan du ange python-version som du vill använda. Du måste fortfarande skapa en virtuell miljö även om du vill använda python 2,7 och 3,5. Det här kravet är att se till att klustrets standard miljö inte blir mer beständigt. Kör skript åtgärder på klustret för alla noder med skriptet nedan för att skapa en virtuell python-miljö.

    -   `--prefix` anger en sökväg där en virtuell Conda-miljö bor. Det finns flera konfigurationer som behöver ändras ytterligare utifrån den sökväg som anges här. I det här exemplet använder vi py35new, eftersom klustret redan har en befintlig virtuell miljö som heter py35.
    -   `python=` anger python-versionen för den virtuella miljön. I det här exemplet använder vi version 3,5, samma version som klustret som skapats i en. Du kan också använda andra python-versioner för att skapa den virtuella miljön.
    -   `anaconda` anger package_spec som Anaconda för att installera Anaconda-paket i den virtuella miljön.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Installera externa python-paket i den virtuella miljön som skapats om det behövs. Kör skript åtgärder på klustret för alla noder med skriptet nedan för att installera externa python-paket. Du måste ha behörigheten sudo här för att skriva filer till den virtuella miljömappen.

    Sök i [paket indexet](https://pypi.python.org/pypi) efter den fullständiga listan med tillgängliga paket. Du kan också hämta en lista över tillgängliga paket från andra källor. Du kan till exempel installera paket som gjorts tillgängliga via [Conda-falska](https://conda-forge.org/feedstocks/).

    Använd kommandot nedan om du vill installera ett bibliotek med den senaste versionen:

    - Använd Conda-kanal:

        -   `seaborn` är det paket namn som du vill installera.
        -   `-n py35new` Ange namnet på den virtuella miljön som du nyss skapade. Se till att ändra namnet på motsvarande sätt baserat på hur du skapar den virtuella miljön.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Eller Använd PyPi lagrings platsen, ändra `seaborn` och `py35new` motsvarande:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Använd kommandot nedan om du vill installera ett bibliotek med en angiven version:

    - Använd Conda-kanal:

        -   `numpy=1.16.1` är paket namnet och versionen som du vill installera.
        -   `-n py35new` Ange namnet på den virtuella miljön som du nyss skapade. Se till att ändra namnet på motsvarande sätt baserat på hur du skapar den virtuella miljön.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Eller Använd PyPi lagrings platsen, ändra `numpy==1.16.1` och `py35new` motsvarande:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    Om du inte känner till namnet på den virtuella miljön kan du använda SSH till Head-noden i klustret och köra `/usr/bin/anaconda/bin/conda info -e` för att visa alla virtuella miljöer.

3. Ändra Spark-och livy-konfiguration och peka på den virtuella miljön som skapats.

    1. Öppna Ambari UI, gå till sidan Spark2, fliken konfigurationer.

        ![Ändra Spark-och livy-konfiguration via Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Expandera Advanced livy2-kuvert, Lägg till under uttryck i nederkant. Om du har installerat den virtuella miljön med ett annat prefix ändrar du sökvägen på motsvarande sätt.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Ändra livy-konfiguration via Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Expandera Advanced spark2-kuvert, Ersätt den befintliga exporten PYSPARK_PYTHON-instruktionen längst ned. Om du har installerat den virtuella miljön med ett annat prefix ändrar du sökvägen på motsvarande sätt.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Ändra Spark-konfiguration via Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Spara ändringarna och starta om berörda tjänster. Dessa ändringar kräver en omstart av Spark2-tjänsten. Ambari UI upprättar en nödvändig omstart, klicka på Starta om för att starta om alla berörda tjänster.

        ![Starta om tjänster](./media/apache-spark-python-package-installation/ambari-restart-services.png)

    5. Ange två egenskaper till Spark-sessionen för att säkerställa att jobbet pekar på den uppdaterade Spark-konfigurationen: `spark.yarn.appMasterEnv.PYSPARK_PYTHON` och `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON` . 

        Använd den här terminalen eller en antecknings bok med `spark.conf.set` funktionen.

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Om du använder livy lägger du till följande egenskaper i begär ande texten:

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Om du vill använda den nya virtuella miljön på Jupyter. Ändra Jupyter-konfiguration och starta om Jupyter. Kör skript åtgärder på alla huvudnoder med nedanstående instruktion för att peka Jupyter mot den nya virtuella miljön som skapats. Se till att ändra sökvägen till det prefix du angav för den virtuella miljön. När du har kört den här skript åtgärden startar du om Jupyter-tjänsten via Ambari-ANVÄNDARGRÄNSSNITTET för att göra den här ändringen tillgänglig.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Du kan dubbelt kontrol lera python-miljön i Jupyter Notebook genom att köra nedanstående kod:

    ![Kontrol lera python-versionen i Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Kända problem

Det finns ett känt fel för Anaconda `4.7.11` -versionen, `4.7.12` och `4.8.0` . Om du ser att skript åtgärderna slutar svara vid `"Collecting package metadata (repodata.json): ...working..."` och slutar med `"Python script has been killed due to timeout after waiting 3600 secs"` . Du kan ladda ned [skriptet](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) och köra det som skript åtgärder på alla noder för att åtgärda problemet.

Du kan kontrol lera din Anaconda-version genom att använda SSH till noden kluster huvud och köra `/usr/bin/anaconda/bin/conda --v` .

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)
* [Externa paket med Jupyter-anteckningsböcker i Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
