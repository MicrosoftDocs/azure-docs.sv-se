---
title: Felsöka problem med Apache Spark kluster i Azure HDInsight
description: Lär dig mer om problem som rör Apache Spark kluster i Azure HDInsight och hur du arbetar runt dem.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 65163225853037ac4cb97a02c0bdaf554b509fb1
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822207"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Kända problem för Apache Spark kluster i HDInsight

Det här dokumentet håller reda på alla kända problem för den offentliga för hands versionen av HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache livy läcker interaktiv session
När [Apache livy](https://livy.incubator.apache.org/) startas om (från [Apache Ambari](https://ambari.apache.org/) eller på grund av en omstart av den virtuella datorn av huvudnoden 0), läcker en interaktiv session. Därför kan nya jobb fastna i godkänd status.

**Minskning**

Använd följande procedur för att lösa problemet:

1. SSH till huvudnoden. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Kör följande kommando för att hitta program-ID: n för de interaktiva jobb som har startats via livy.

   ```bash
   yarn application –list
   ```

    Standard jobb namnen kommer att vara livy om jobben startades med en livy-interaktiv session utan att några explicita namn angavs. För livy-sessionen som startades av [Jupyter Notebook](https://jupyter.org/)börjar jobb namnet med `remotesparkmagics_*` .

3. Kör följande kommando för att avsluta dessa jobb.

   ```bash
   yarn application –kill <Application ID>
   ```

Nya jobb börjar köras.

## <a name="spark-history-server-not-started"></a>Spark-historik servern har inte startats
Spark historik server startas inte automatiskt när ett kluster har skapats.  

**Minskning**

Starta historik servern manuellt från Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Behörighets problem i Spark logg katalog
hdiuser hämtar följande fel när ett jobb skickas med Spark-Submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

Och ingen driv rutins logg skrivs.

**Minskning**

1. Lägg till hdiuser i Hadoop-gruppen.
2. Ge 777-behörigheter på/var/log/Spark när klustret har skapats.
3. Uppdatera Spark-händelseloggen med Ambari som en katalog med 777-behörigheter.  
4. Kör Spark-Submit som sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark-Phoenix koppling stöds inte

HDInsight Spark-kluster stöder inte Spark-Phoenix-anslutningen.

**Minskning**

Du måste använda Spark-HBase Connector i stället. Anvisningar finns i [använda Spark-HBase Connector](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problem som rör Jupyter-anteckningsböcker

Nedan följer några kända problem som rör Jupyter-anteckningsböcker.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Antecknings böcker med icke-ASCII-tecken i fil namn

Använd inte icke-ASCII-tecken i Jupyter Notebook fil namn. Om du försöker ladda upp en fil via Jupyter-ANVÄNDARGRÄNSSNITTET, som har ett fil namn som inte är ASCII, Miss lyckas det utan något fel meddelande. Jupyter tillåter inte att du laddar upp filen, men det returnerar inte något synligt fel.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Fel vid inläsning av antecknings böcker med större storlekar

Du kan se ett fel **`Error loading notebook`** när du läser in antecknings böcker som är större i storlek.  

**Minskning**

Om du får det här felet betyder det inte att dina data är skadade eller går förlorade.  Dina antecknings böcker finns fortfarande på disk i `/var/lib/jupyter` och du kan använda SSH i klustret för att få åtkomst till dem. Mer information finns i [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

När du har anslutit till klustret med SSH kan du kopiera dina antecknings böcker från klustret till din lokala dator (med hjälp av SCP eller WinSCP) som en säkerhets kopia för att förhindra förlust av viktiga data i antecknings boken. Du kan sedan använda SSH-tunnlar i din huvudnoden vid Port 8001 för att få åtkomst till Jupyter utan att gå via gatewayen.  Därifrån kan du ta bort utdatan från antecknings boken och spara den igen för att minimera storleken på antecknings boken.

Om du vill förhindra att det här felet uppstår i framtiden måste du följa rekommendationerna nedan:

* Det är viktigt att hålla nere storleken på bärbara datorer. Alla utdata från dina Spark-jobb som skickas tillbaka till Jupyter sparas i antecknings boken.  Det är en bra idé att använda Jupyter i allmänhet för att undvika att köra `.collect()` stora RDD eller dataframes. om du i stället vill titta på en RDDs innehåll kan du överväga att köra `.take()` eller `.sample()` så att dina utdata inte blir för stora.
* När du sparar en antecknings bok rensar du också alla utdata celler för att minska storleken.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Den första starten av Notebook tar längre tid än förväntat

Den första kod instruktionen i Jupyter Notebook med Spark Magic kan ta mer än en minut.  

**Skriftliga**

Detta inträffar eftersom när den första kod cellen körs. I bakgrunden kommer detta att initiera sessionens konfiguration och Spark-, SQL-och Hive-kontexterna anges. När dessa kontexter har ställts in körs den första instruktionen och detta ger intryck av att instruktionen tog lång tid att slutföra.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter Notebook tids gräns vid skapande av sessionen

När Spark-klustret har slut på resurser, kommer Spark-och PySpark-kernelerna i Jupyter Notebook att försöka skapa sessionen.

**Åtgärder**

1. Frigör några resurser i Spark-klustret genom att:

   * Stoppa andra Spark-anteckningsböcker genom att gå till menyn Stäng och stoppa eller genom att klicka på Stäng av i anteckningsbok-Utforskaren.
   * Stoppa andra Spark-program från garn.

2. Starta om den antecknings bok som du försökte starta. Det finns tillräckligt med resurser för att skapa en session nu.

## <a name="see-also"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
