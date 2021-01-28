---
title: Felsöka Spark-jobb med IntelliJ Azure Toolkit (för hands version) – HDInsight
description: Vägledning för att använda HDInsight-verktyg i Azure Toolkit for IntelliJ för att felsöka program
keywords: Felsöka fjärran IntelliJ, fjärrfelsökning IntelliJ, SSH, IntelliJ, HDInsight, felsöka IntelliJ, fel sökning
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 5abbb26e7582d8ddabb73f3a178cf6d87e24db21
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942538"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Fel Spark fel sökning av jobb med Azure Toolkit for IntelliJ (för hands version)

Den här artikeln innehåller stegvisa instruktioner om hur du använder HDInsight-verktyg i [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) att köra **fel söknings** program av Spark-fel.

## <a name="prerequisites"></a>Förutsättningar

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). Den här kursen använder Java version 8.0.202.
  
* IntelliJ idé. Den här artikeln använder [INTELLIJ idé community ver. 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Se [Installera Azure Toolkit för IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* Anslut till ditt HDInsight-kluster. Se [ansluta till ditt HDInsight-kluster](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage Explorer. Se [Ladda ned Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Skapa ett projekt med fel söknings mall

Skapa ett Spark-2.3.2-projekt om du vill fortsätta fel sökningen, vidta fel söknings exempel fil för aktiviteten i det här dokumentet.

1. Öppna IntelliJ IDEA. Öppna fönstret **nytt projekt** .

   a. Välj **Azure Spark/HDInsight** i den vänstra rutan.

   b. Välj **Spark-projekt med fel söknings exempel för aktivitets fel (för hands version) (för hands version) (Scala)** från huvud fönstret.

     ![IntelliJ skapa ett fel söknings projekt](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Välj **Nästa**.

2. Utför följande steg i fönstret **nytt projekt** :

   ![IntelliJ nytt projekt Välj Spark-version](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Ange ett projekt namn och en projekt plats.

   b. I list rutan **Project SDK** väljer du **Java 1,8** för **Spark 2.3.2** -kluster.

   c. I list rutan **Spark-version** väljer du **Spark 2.3.2 (Scala 2.11.8)**.

   d. Välj **Slutför**.

3. Välj **src**  >  **main**-  >  **Scala** för att öppna din kod i projektet. I det här exemplet används skriptet **AgeMean_Div ()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Köra ett Spark-Scala/Java-program i ett HDInsight-kluster

Skapa ett Spark-Scala/Java-program och kör sedan programmet på ett Spark-kluster genom att utföra följande steg:

1. Klicka på **Lägg till konfiguration** för att öppna fönstret **Kör/Felsök konfigurationer** .

   ![HDI IntelliJ Lägg till konfiguration](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. I dialog rutan **Kör/Felsök konfigurationer** väljer du plus tecknet ( **+** ). Välj sedan alternativet **Apache Spark på HDInsight** .

   ![IntelliJ Lägg till ny konfiguration](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Växla till **fjärran sluten på kluster** -fliken. Ange information om **namn**, **Spark-kluster** och **huvud klass namn**. Våra verktyg stöder fel sökning med **körningar**. **NumExectors** är standardvärdet 5, och du har inte angett fler än 3. Du kan minska körnings tiden genom att lägga till **Spark. garn. maxAppAttempts** i **jobb konfigurationerna** och ange värdet till 1. Spara konfigurationen genom att klicka på **OK** .

   ![IntelliJ kör felsöknings konfiguration ny](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Konfigurationen sparas nu med det namn du angav. Välj konfigurations namnet om du vill visa konfigurations informationen. Välj **Redigera konfigurationer** om du vill göra ändringar.

5. När du har slutfört konfigurations inställningarna kan du köra projektet mot fjärrklustret.

   ![IntelliJ Felsök fjärran sluten Spark-jobb, knapp](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Du kan kontrol lera program-ID: t från fönstret utdata.

   ![Fjärrkörning av IntelliJ debug-jobb fjärrstyrt resultat](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Ladda ned misslyckad jobb profil

Om det inte går att skicka jobb kan du hämta den misslyckade jobb profilen till den lokala datorn för ytterligare fel sökning.

1. Öppna **Microsoft Azure Storage Explorer**, leta upp HDInsight-kontot för klustret för det misslyckade jobbet, ladda ned de misslyckade jobb resurserna från motsvarande plats: **\hdp\spark2-Events \\ . Spark \\ \<application ID> -Failure** till en lokal mapp. I fönstret **aktiviteter** visas hämtnings förloppet.

   ![Azure Storage Explorer nedladdnings problem](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure Storage Explorer hämtningen lyckades](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurera lokal fel söknings miljö och Felsök vid fel

1. Öppna det ursprungliga projektet eller skapa ett nytt projekt och koppla det till den ursprungliga käll koden. Endast Spark-2.3.2 version stöds för fel sökning för tillfället.

1. I IntelliJ idé skapar du ett **Spark-fel fel söknings** konfigurations fil, väljer du filen för dokument hantering från tidigare hämtade jobb resurser för **fel kontexten för fel kontexten för Spark-jobbet** .

   ![konfiguration av Crete-problem](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Klicka på knappen lokal körning i verktygsfältet. felet visas i körnings fönstret.

   ![Kör-Failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Kör-Failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Ange Bryt punkt som loggen anger och klicka sedan på knappen lokal fel sökning för att utföra lokal fel sökning precis som dina normala Scala-/Java-projekt i IntelliJ.

1. Om projektet har slutförts efter fel sökning kan du skicka det misslyckade jobbet till Spark på HDInsight-klustret igen.

## <a name="next-steps"></a><a name="seealso"></a>Nästa steg

* [Översikt: Felsöka Apache Spark program](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demo

* Skapa Scala-projekt (video): [skapa Apache Spark Scala-program](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Fjärrfelsökning (video): [använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via fjärr anslutning i ett HDInsight-kluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att analysera skapande temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](./apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använd Azure Toolkit for IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda HDInsight-verktyg för IntelliJ med begränsat läge för Hortonworks](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Skapa Apache Spark-program med hjälp av HDInsight-verktyg i Azure Toolkit for Eclipse](./apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)