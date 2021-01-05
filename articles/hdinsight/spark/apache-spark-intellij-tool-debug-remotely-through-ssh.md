---
title: 'Azure Toolkit for IntelliJ: Felsöka Spark-appar med SSH-HDInsight'
description: Stegvisa anvisningar om hur du använder HDInsight-verktyg i Azure Toolkit for IntelliJ att felsöka program via fjärr anslutning i HDInsight-kluster via SSH
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 7a2044e9bb0110eda666d12e29a715547381e2be
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822343"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Felsöka Apache Spark program i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH

Den här artikeln innehåller steg-för-steg-anvisningar om hur du använder HDInsight-verktyg i [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij) för att felsöka program via en fjärr anslutning i ett HDInsight-kluster. Om du vill felsöka ditt projekt kan du även visa [fel söknings programmet HDInsight Spark med Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) video.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Se [skapa ett Apache Spark-kluster](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* För Windows-användare: när du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag, enligt beskrivningen i [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Undantaget beror på att WinUtils.exe saknas i Windows.

    Lös problemet genom att ladda ned [Winutils.exe](https://github.com/steveloughran/winutils) till en plats, till exempel **C:\WinUtils\bin**. Lägg sedan till miljövariabeln **HADOOP_HOME** och ange värdet för variabeln till **C:\WinUtils**.

* [INTELLIJ idé](https://www.jetbrains.com/idea/download/#section=windows) (community-versionen är kostnads fri.)

* [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* [Scala-plugin-programmet för IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Skapa ett Spark Scala-program

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

1. Välj **Apache Spark/HDInsight** i det vänstra fönstret.

1. Välj **Spark-projekt med exempel (Scala)** från huvud fönstret.

1. Från listrutan **Byggverktyg** väljer du något av följande:

    * **Maven** för guidestöd när du skapar Scala-projekt.
    * **SBT** för att hantera beroenden när du skapar Scala-projektet.

     ![IntelliJ skapa nytt projekt Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Välj **Nästa**.

1. I nästa **nya projekt** fönster anger du följande information:

    |Egenskap |Beskrivning |
    |---|---|
    |Projektnamn|Ange ett namn. Det här går igenom användningen `myApp` .|
    |Projekt plats|Ange önskad plats för att spara projektet.|
    |Projekt-SDK|Om det är tomt väljer du **ny...** och navigerar till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2. x.**.. I det här exemplet används **Spark 2.3.0 (Scala 2.11.8)**.|

   ![IntelliJ nytt projekt Välj Spark-version](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Välj **Slutför**. Det kan ta några minuter innan projektet blir tillgängligt. Se det nedre högra hörnet för att fortsätta.

1. Expandera ditt projekt och gå till   >  **huvud** exemplet för src-  >  **Scala**  >  . Dubbelklicka på **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Utför lokal körning

1. Från **SparkCore_WasbIOTest** -skriptet högerklickar du på skript redigeraren och väljer sedan alternativet **Kör SparkCore_WasbIOTest** för att utföra lokal körning.

1. När den lokala körningen är klar kan du se utdatafilen Spara till din aktuella standard för Project Explorer- **data**  >  ****.

    ![IntelliJ-projektets lokala körnings resultat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Våra verktyg har ställt in standard konfigurationen för lokal körning automatiskt när du utför den lokala körningen och lokal fel sökning. Öppna konfigurationen **[Spark på HDInsight] XXX** i det övre högra hörnet, så kan du se **[Spark på HDInsight] XXX** redan har skapats under **Apache Spark i HDInsight**. Växla till fliken **lokalt körning** .

    ![IntelliJ kör felsöka konfigurationer lokal körning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Miljövariabler](#prerequisites): om du redan har konfigurerat system miljö variabeln **HADOOP_HOME** till **C:\WinUtils**, kan den automatiskt identifiera att du inte behöver lägga till manuellt.
    - [WinUtils.exe plats](#prerequisites): om du inte har angett system miljö variabeln kan du hitta platsen genom att klicka på dess knapp.
    - Du behöver bara välja något av två alternativ och de behövs inte på MacOS och Linux.

1. Du kan också ange konfigurationen manuellt innan du utför lokal körning och lokal fel sökning. Välj plus tecknet () i föregående skärm bild **+** . Välj sedan alternativet **Apache Spark på HDInsight** . Ange information om **namn**, **huvud klass namn** som ska sparas och klicka sedan på knappen lokal körning.

## <a name="perform-local-debugging"></a>Utför lokal fel sökning

1. Öppna **SparkCore_wasbloTest** -skriptet, ange Bryt punkter.

1. Högerklicka på skript redigeraren och välj sedan alternativet **Felsök [Spark på HDInsight] XXX** för att utföra lokal fel sökning.

## <a name="perform-remote-run"></a>Utför fjärrkörning

1. Navigera för att **köra**  >  **Redigera konfigurationer...** Från den här menyn kan du skapa eller redigera konfigurationer för fjärrfelsökning.

1. I dialog rutan **Kör/Felsök konfigurationer** väljer du plus tecknet ( **+** ). Välj sedan alternativet **Apache Spark på HDInsight** .

   ![IntelliJ Lägg till ny konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Växla till **fjärran sluten på kluster** -fliken. Ange information om **namn**, **Spark-kluster** och **huvud klass namn**. Klicka sedan på **Avancerad konfiguration (fjärrfelsökning)**. Våra verktyg stöder fel sökning med **körningar**. Standardvärdet är 5 i **numExectors**. Du är bättre på att inte ange högre än 3.

   ![IntelliJ kör fel söknings konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. I delen **Avancerad konfiguration (Remote-felsökning)** väljer du **Aktivera Spark-fjärrfelsökning**. Ange SSH-användarnamnet och ange ett lösen ord eller Använd en privat nyckel fil. Om du vill utföra fjärrfelsökning måste du ange den. Du behöver inte ange det om du bara vill använda fjärran sluten.

   ![IntelliJ Avancerad konfiguration aktivera Spark-fjärrfelsökning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Konfigurationen sparas nu med det namn du angav. Välj konfigurations namnet om du vill visa konfigurations informationen. Välj **Redigera konfigurationer** om du vill göra ändringar.

1. När du har slutfört konfigurations inställningarna kan du köra projektet mot fjärrklusteret eller utföra fjärrfelsökning.

   ![IntelliJ Felsök fjärran sluten Spark-jobb, knapp](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klicka på knappen **Koppla från** om du inte vill att sändnings loggarna ska visas i den vänstra panelen. Den körs dock fortfarande på Server delen.

   ![Fjärrkörning av IntelliJ debug-jobb fjärrstyrt resultat](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Utför fjärrfelsökning

1. Konfigurera Bryt punkter och klicka sedan på ikonen **fjärrfelsökning** . Skillnaden med fjärrsändning är att SSH username/Password måste konfigureras.

   ![Fel söknings ikon för IntelliJ-felsökning för fjärr Spark-jobb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. När program körningen når Bryt punkten ser du fliken **driv rutin** och två **utförar** -flikar i **fel söknings** fönstret. Välj ikonen **återuppta program** för att fortsätta att köra koden, som sedan når nästa Bryt punkt. Du måste växla till rätt **utförar** -flik för att hitta mål utförar för fel sökning. Du kan visa körnings loggarna på motsvarande **konsol** flik.

   ![IntelliJ Felsök fjärr Spark-jobb fel söknings flik](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Utför fjärrfelsökning och åtgärda fel

1. Konfigurera två Bryt punkter och välj sedan **fel söknings** ikonen för att starta fjärrfelsökning.

1. Koden stannar vid den första Bryt punkten och parameter-och variabel informationen visas i fönstret **variabler** .

1. Välj ikonen **återuppta program** för att fortsätta. Koden stoppas vid den andra punkten. Undantaget upptäcktes som förväntat.

   ![Fel sökning av IntelliJ för fjärrspark-jobb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Välj ikonen **återuppta program** igen. I fönstret **HDInsight Spark-sändning** visas fel meddelandet "Det gick inte att köra jobb".

   ![Fel sökning av IntelliJ för fjärrkörning av Spark-jobb](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Om du vill uppdatera variabelvärdet dynamiskt genom att använda IntelliJ fel söknings funktion väljer du **Felsök** igen. Fönstret **variabler** visas igen.

1. Högerklicka på målet på fliken **Felsök** och välj sedan **Ange värde**. Ange sedan ett nytt värde för variabeln. Spara värdet genom att välja **RETUR** .

   ![IntelliJ fel söknings värde för fjärrspark jobb uppsättning](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Välj ikonen för att fortsätta **köra programmet.** Den här gången har inget undantag påträffats. Du kan se att projektet har körts utan undantag.

   ![IntelliJ Felsök fjärr Spark-jobb utan undantag](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Nästa steg

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

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
* [Skapa Apache Spark-program med hjälp av HDInsight-verktyg i Azure Toolkit for Eclipse](./apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-klustret för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)