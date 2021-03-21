---
title: Felsöka Apache Spark i Azure HDInsight
description: Få svar på vanliga frågor om att arbeta med Apache Spark och Azure HDInsight.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: af488cd253e8a8ebedd838aa5286185ea556f69d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942506"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Felsöka Apache Spark med Azure HDInsight

Lär dig mer om de vanligaste problemen och deras lösningar när du arbetar med Apache Spark nytto laster i [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hur gör jag för att konfigurera ett Apache Spark-program med Apache Ambari i kluster?

Konfigurations värden för Spark kan justeras för att undvika ett Apache Spark program `OutofMemoryError` undantag. Följande steg visar standard konfigurations värden för Spark i Azure HDInsight:

1. Logga in på Ambari på `https://CLUSTERNAME.azurehdidnsight.net` med dina autentiseringsuppgifter för klustret. Den första skärmen visar en översikts instrument panel. Det finns små kosmetiska skillnader mellan HDInsight 3,6 och 4,0.

1. Navigera till **Spark2**-  >  **konfigurationer**.

    ![Välj fliken konfigurationer](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. I listan med konfigurationer väljer och expanderar du **Custom-spark2-defaults**.

1. Leta efter den värde inställning som du behöver justera, till exempel **spark.executor. Memory**. I det här fallet är värdet för **9728m** för högt.

    ![Välj Custom-Spark-defaults](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Ange värdet till den rekommenderade inställningen. Värdet **2048** rekommenderas för den här inställningen.

1. Spara värdet och spara konfigurationen. Välj **Spara**.

    ![Ändra värdet till 2048](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Skriv en anteckning om konfigurations ändringarna och välj sedan **Spara**.

    ![Ange en anteckning om de ändringar du har gjort](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Du får ett meddelande om eventuella konfigurationer behöver åtgärdas. Anteckna objekten och välj **Fortsätt ändå**.

    ![Välj Fortsätt ändå](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. När en konfiguration sparas uppmanas du att starta om tjänsten. Välj **starta om**.

    ![Välj Starta om](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Bekräfta omstarten.

    ![Välj bekräfta omstart av alla](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Du kan granska processerna som kör.

    ![Granska processer som körs](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Du kan lägga till konfigurationer. I listan med konfigurationer väljer du **Custom-spark2-defaults** och väljer sedan **Lägg till egenskap**.

    ![Välj Lägg till egenskap](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definiera en ny egenskap. Du kan definiera en enskild egenskap med hjälp av en dialog ruta för vissa inställningar som datatyp. Du kan också definiera flera egenskaper med en definition per rad.

    I det här exemplet definieras egenskapen **Spark. driver. Memory** med värdet **4G**.

    ![Definiera ny egenskap](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Spara konfigurationen och starta sedan om tjänsten enligt beskrivningen i steg 6 och 7.

Dessa ändringar är hela klustret men kan åsidosättas när du skickar Spark-jobbet.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hur gör jag för att konfigurerar du ett Apache Spark program genom att använda en Jupyter Notebook på kluster?

I den första cellen i Jupyter Notebook, efter **%% Configure** -direktivet, anger du Spark-konfigurationerna i giltigt JSON-format. Ändra de faktiska värdena efter behov:

![Lägg till en konfiguration](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hur gör jag för att konfigurera ett Apache Spark-program med Apache Livy i kluster?

Skicka Spark-programmet till livy med hjälp av en REST-klient som vändning. Använd ett kommando som liknar följande. Ändra de faktiska värdena efter behov:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hur gör jag för att konfigurera ett Apache Spark-program med spark-submit i kluster?

Starta Spark-Shell med hjälp av ett kommando som liknar följande. Ändra det faktiska värdet för konfigurationerna vid behov:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Mer att läsa

[Apache Spark att skicka jobb på HDInsight-kluster](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* [Översikt över Spark-minnes hantering](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Felsöka Spark-program i HDInsight-kluster](/archive/blogs/azuredatalake/spark-debugging-101).

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).