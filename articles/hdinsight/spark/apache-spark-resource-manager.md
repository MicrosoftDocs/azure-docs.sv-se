---
title: Hantera resurser för Apache Spark kluster i Azure HDInsight
description: Lär dig hur du hanterar resurser för Spark-kluster på Azure HDInsight för bättre prestanda.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/12/2021
ms.openlocfilehash: 43e46c1b5e720e4f9f46f8d3198fea7124d8f5ba
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132213"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Hantera resurser för Apache Spark kluster i Azure HDInsight

Lär dig hur du kommer åt gränssnitten som [Apache Ambari](https://ambari.apache.org/) ui, [Apache Hadoop garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) UI och [Spark historik servern](./apache-azure-spark-history-server.md) som är kopplad till ditt [Apache Spark](https://spark.apache.org/) -kluster och hur du finjusterar kluster konfigurationen för optimala prestanda.

## <a name="open-the-spark-history-server"></a>Öppna Spark-historik servern

Spark-historik Server är webb gränssnittet för slutförd och körning av Spark-program. Det är en utökning av Spark: s webb gränssnitt. Fullständig information finns i [Spark historik Server](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Öppna garn gränssnittet

Du kan använda garn gränssnittet för att övervaka program som för närvarande körs i Spark-klustret.

1. Öppna Spark-klustret från [Azure Portal](https://portal.azure.com/). Mer information finns i [lista och Visa kluster](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Välj **garn** från **kluster instrument paneler**. När du uppmanas till det anger du administratörs behörighet för Spark-klustret.

    ![Starta garn gränssnitt](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Du kan också starta garn gränssnittet från Ambari-ANVÄNDARGRÄNSSNITTET. I Ambari-användargränssnittet navigerar du till snabb länkar till **garn**  >  **länkar**  >  **Active**  >  **Resource Manager UI**.

## <a name="optimize-clusters-for-spark-applications"></a>Optimera kluster för Spark-program

De tre nyckel parametrarna som kan användas för Spark-konfiguration, beroende på program krav `spark.executor.instances` ,, `spark.executor.cores` och `spark.executor.memory` . En utförar är en process som startas för ett Spark-program. Den körs på Worker-noden och ansvarar för att utföra uppgifterna för programmet. Standard antalet körningar och utförar storlekarna för varje kluster beräknas baserat på antalet arbetsnoder och storleken på arbets noden. Den här informationen lagras i `spark-defaults.conf` kluster huvudnoderna.

De tre konfigurations parametrarna kan konfigureras på kluster nivå (för alla program som körs i klustret) eller också kan de anges för varje enskilt program.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändra parametrarna med Ambari-ANVÄNDARGRÄNSSNITTET

1. Från Ambari-användargränssnittet navigerar du till **Spark2**  >  **configs**  >  **anpassade Spark2-defaults**.

    ![Ange parametrar med anpassad Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Ange parametrar med anpassad Ambari")

1. Standardvärdena är lämpliga för att köra fyra Spark-program samtidigt i klustret. Du kan ändra dessa värden från användar gränssnittet, som visas på följande skärm bild:

    ![Ange parametrar med Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Ange parametrar med Ambari")

1. Välj **Spara** för att spara konfigurations ändringarna. Längst upp på sidan uppmanas du att starta om alla berörda tjänster. Välj **starta om**.

    ![Starta om tjänster](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter Notebook

För program som körs i Jupyter Notebook kan du använda `%%configure` Magic för att göra konfigurationen ändringar. Vi rekommenderar att du gör sådana ändringar i början av programmet innan du kör den första kod cellen. Detta säkerställer att konfigurationen tillämpas på livy-sessionen när den skapas. Om du vill ändra konfigurationen i ett senare skede i programmet måste du använda- `-f` parametern. Men genom att göra detta går all status i programmet förlorade.

Följande fragment visar hur du ändrar konfigurationen för ett program som körs i Jupyter.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Konfigurations parametrar måste skickas in som en JSON-sträng och måste finnas på nästa rad efter Magic, som visas i kolumnen exempel.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändra parametrarna för ett program som har skickats med Spark-Submit

Följande kommando är ett exempel på hur du ändrar konfigurations parametrar för ett batch-program som skickas med `spark-submit` .

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändra parametrarna för ett program som skickas med hjälp av sväng

Följande kommando är ett exempel på hur du ändrar konfigurations parametrar för ett batch-program som skickas med hjälp av sväng.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

> [!Note]
> Kopiera JAR-filen till klustrets lagrings konto. Kopiera inte JAR-filen direkt till Head-noden.

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Ändra dessa parametrar på en spark Thrift-Server

Spark Thrift-servern ger JDBC/ODBC-åtkomst till ett Spark-kluster och används för att betjäna Spark SQL-frågor. Verktyg som Power BI, Tableau och så vidare använder du ODBC-protokoll för att kommunicera med Spark Thrift-servern för att köra Spark SQL-frågor som ett Spark-program. När ett Spark-kluster skapas startas två instanser av Spark Thrift-servern, en på varje Head-nod. Varje Spark Thrift-Server visas som ett Spark-program i garn gränssnittet.

Spark Thrift-servern använder Spark Dynamic utförar-allokering och därför `spark.executor.instances` används den inte. I stället använder Spark Thrift-servern `spark.dynamicAllocation.maxExecutors` och `spark.dynamicAllocation.minExecutors` för att ange antalet utförar. Konfigurations parametrarna `spark.executor.cores` och `spark.executor.memory` används för att ändra storleken på utförar. Du kan ändra dessa parametrar så som visas i följande steg:

* Expandera kategorin **Advanced spark2-Thrift-sparkconf** för att uppdatera parametrarna `spark.dynamicAllocation.maxExecutors` och `spark.dynamicAllocation.minExecutors` .

    ![Konfigurera Spark Thrift-Server](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Konfigurera Spark Thrift-Server")

* Expandera kategorin **Custom spark2-Thrift-sparkconf** för att uppdatera parametrarna `spark.executor.cores` och `spark.executor.memory` .

    ![Konfigurera Spark Thrift Server parameter](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Konfigurera Spark Thrift Server parameter")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Ändra driv rutins minnet för Spark Thrift-servern

Spark Thrift-serverns driv rutins minne har kon figurer ATS till 25% av Head-nodens RAM-storlek, förutsatt att Head-nodens totala RAM-storlek är större än 14 GB. Du kan använda Ambari-ANVÄNDARGRÄNSSNITTET för att ändra driv Rutinens minnes konfiguration, som visas på följande skärm bild:

I Ambari-användargränssnittet navigerar du till **Spark2**  >  **configs**  >  **Advanced Spark2-kuvert**. Ange sedan värdet för **spark_thrift_cmd_opts**.

## <a name="reclaim-spark-cluster-resources"></a>Frigör Spark kluster resurser

På grund av Spark Dynamic Allocation är de enda resurserna som används av Thrift-servern resurserna för de två program huvuden. Om du vill frigöra resurserna måste du stoppa de Thrift-Server tjänster som körs i klustret.

1. I Ambari-ANVÄNDARGRÄNSSNITTET väljer du **Spark2** i det vänstra fönstret.

2. På nästa sida väljer du **Spark2 Thrift-servrar**.

    ![Starta om Thrift server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Starta om Thrift server1")

3. Du bör se de två huvudnoderna som Spark2 Thrift-servern körs på. Välj en av huvudnoderna.

    ![Starta om Thrift server2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Starta om Thrift server2")

4. Nästa sida visar en lista över alla tjänster som körs på den huvudnoden. Välj den nedrullningsbara knappen bredvid Spark2 Thrift server i listan och välj sedan **stoppa**.

    ![Starta om Thrift Server3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Starta om Thrift Server3")
5. Upprepa de här stegen även på andra huvudnoden.

## <a name="restart-the-jupyter-service"></a>Starta om Jupyter-tjänsten

Starta Ambari-webbgränssnittet som visas i början av artikeln. Välj **Jupyter** i det vänstra navigerings fönstret, Välj **tjänst åtgärder** och välj sedan **starta om alla**. Detta startar Jupyter-tjänsten på alla huvudnoderna.

![Starta om Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Starta om Jupyter")

## <a name="monitor-resources"></a>Övervaka resurser

Starta det garn användar gränssnitt som visas i början av artikeln. I tabellen kluster mått överst på skärmen, kontrol lera värdena för **använt minne** och kolumner i **Total** mängd minne. Om de två värdena stängs kanske det inte finns tillräckligt med resurser för att starta nästa program. Detsamma gäller för de **virtuella kärnor som används** och **virtuella kärnor totala** kolumner. I huvudvyn kan det också vara en indikation på att det inte finns tillräckligt med resurser för att starta, om ett program finns i ett **godkänt** tillstånd och inte går över till att **köras** eller inte är i **fel** tillstånd.

![Resurs gräns](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Resurs gräns")

## <a name="kill-running-applications"></a>Avsluta program som körs

1. Välj **Kör** i den vänstra panelen i garn gränssnittet. I listan över program som körs anger du vilket program som ska avlivas och väljer **ID**.

    ![Kill-APP1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Kill-APP1")

2. Välj **Avsluta program** i det övre högra hörnet och välj sedan **OK**.

    ![Kill-APP2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Kill-APP2")

## <a name="see-also"></a>Se även

* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>För data analyser

* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program insiktering telemetri data analys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>För Apache Spark utvecklare

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)
