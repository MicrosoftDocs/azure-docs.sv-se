---
title: 'Azure Toolkit for IntelliJ: Spark-app – HDInsight'
description: Använd Azure Toolkit for IntelliJ för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: 0b2c2a91ba9d029bcf24b0980d0a2339194e6d8a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931031"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>Använd Azure Toolkit for IntelliJ för att skapa Apache Spark-program för HDInsight-kluster

Den här artikeln visar hur du utvecklar Apache Spark program på Azure HDInsight med hjälp av **Azure Toolkit** -plugin-programmet för IntelliJ IDE. [Azure HDInsight](../hdinsight-overview.md) är en hanterad analys tjänst med öppen källkod i molnet. Med tjänsten kan du använda ramverk med öppen källkod som Hadoop, Apache Spark, Apache Hive och Apache Kafka.

Du kan använda **Azure Toolkit** -plugin-programmet på några få sätt:

* Utveckla och skicka in ett Scala Spark-program till ett HDInsight Spark-kluster.
* Få åtkomst till Azure HDInsight Spark kluster resurser.
* Utveckla och kör ett Scala Spark-program lokalt.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Använd plugin-programmet Azure Toolkit for IntelliJ
> * Utveckla Apache Spark program
> * Skicka ett program till Azure HDInsight-kluster

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  I den här artikeln används Java version 8.0.202.

* IntelliJ idé. I den här artikeln används [INTELLIJ idé community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Se [Installera Azure Toolkit för IntelliJ](/azure/developer/java/toolkit-for-intellij/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installera plugin-programmet Scala för IntelliJ IDEA

Steg för att installera Scala-plugin-programmet:

1. Öppna IntelliJ IDEA.

2. På Välkomst skärmen navigerar du till **Konfigurera**  >  **plugin** -program för att öppna **plugin** -fönstret.

    ![IntelliJ idé aktiverar Scala-plugin-programmet](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. Välj **Installera** för det Scala-plugin-program som visas i det nya fönstret.  

    ![IntelliJ-idé installerar Scala-plugin-programmet](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Du måste starta om IDE när plugin-programmet har installerats.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Spark Scala-program för ett HDInsight Spark-kluster

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

2. Välj **Azure Spark/HDInsight** i den vänstra rutan.

3. Välj **Spark-projekt (Scala)** i huvudfönstret.

4. Välj något av följande alternativ i list rutan **build-verktyg** :
   * **Maven** för guidestöd när du skapar Scala-projekt.
   * **SBT** för att hantera beroenden när du skapar Scala-projektet.

     ![Dialog rutan IntelliJ idé New Project](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Välj **Nästa**.

6. I fönstret **Nytt projekt** anger du följande information:  

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Projektnamn| Ange ett namn.  I den här artikeln används `myApp` .|  
    |Projektplats| Ange den plats där du vill spara projektet.|
    |Projekt-SDK| Det här fältet kan vara tomt vid din första användning av idén.  Välj **Nytt...** och navigera till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.3.0 (Scala 2.11.8)**.|

    ![Välja Apache Spark SDK](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. Välj **Slutför**.  Det kan ta några minuter innan projektet blir tillgängligt.

8. Spark-projektet skapar automatiskt en artefakt åt dig. Gör så här om du vill visa artefakten:

   a. I meny raden navigerar du till **fil**  >  **projekt struktur...**.

   b. I fönstret **projekt struktur** väljer du **artefakter**.  

   c. Välj **Avbryt**  när du har visat artefakten.

      ![Artefakt information i dialog rutan](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. Lägg till program käll koden genom att göra följande:

    a. Från projekt navigerar du till **Mittprog**  >  **src**  >  **main**  >  **Scala**.  

    b. Högerklicka på **Scala** och navigera sedan till den **nya**  >  **Scala-klassen**.

   ![Kommandon för att skapa en Scala-klass från Project](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. I dialog rutan **Skapa ny Scala-klass** anger du ett namn, väljer **objekt** i list rutan **typ** och väljer sedan **OK**.

     ![Dialog rutan skapa ny Scala-klass](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Filen **MyApp. Scala** öppnas sedan i huvud vyn. Ersätt standard koden med koden som finns nedan:  

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object myApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("myApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV file
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }

    }
    ```

    Koden läser data från HVAC.csv (tillgängliga på alla HDInsight Spark-kluster), hämtar de rader som bara har en siffra i den sjunde kolumnen i CSV-filen och skriver utdata till `/HVACOut` under standard lagrings behållaren för klustret.

## <a name="connect-to-your-hdinsight-cluster"></a>Anslut till ditt HDInsight-kluster

Användaren kan antingen [Logga in i Azure-prenumerationen](#sign-in-to-your-azure-subscription)eller [Länka ett HDInsight-kluster](#link-a-cluster). Använd Ambari användar namn/lösen ord eller domänanslutna autentiseringsuppgifter för att ansluta till ditt HDInsight-kluster.

### <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

1. Gå till **Visa**-  >  **verktyget Windows**  >  **Azure Explorer** från meny raden.

   ![IntelliJ idé visar Azure Explorer](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. I Azure Explorer högerklickar du på **Azure** -noden och väljer sedan **Logga** in.

   ![IntelliJ idé Explorer högerklickar du på Azure](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. I dialog rutan **Azure-inloggning** väljer du **enhets inloggning** och väljer sedan **Logga** in.

    ![Inloggning "för IntelliJ idé Azure-inloggnings enhet](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. I dialog rutan **Azure-enhets inloggning** klickar du på **Kopiera&öppna**.

   !["IntelliJ idé Azure Device inloggning"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. I webb läsar gränssnittet klistrar du in koden och klickar sedan på **Nästa**.

   !["Microsoft Ange kod dialog ruta för HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. Ange dina autentiseringsuppgifter för Azure och stäng sedan webbläsaren.

   !["Microsoft ange e-postdialog för HDI"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. När du har loggat in visar dialog rutan **Välj prenumerationer** alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna. Välj din prenumeration och välj sedan knappen **Välj** .

    ![Dialogrutan Select Subscriptions (Välj prenumerationer)](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. I **Azure Explorer** expanderar du **HDInsight** för att se de HDInsight Spark-kluster som finns i dina prenumerationer.

    ![IntelliJ idé Azure Explorer Main View](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. Om du vill visa de resurser (till exempel lagrings konton) som är associerade med klustret kan du ytterligare expandera en nod för kluster namn.

    ![Lagrings konton i Azure Explorer](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>Länka ett kluster

Du kan länka ett HDInsight-kluster med hjälp av Apache Ambari Managed användar namn. På samma sätt kan du länka med hjälp av domänen och användar namnet, till exempel, för ett domänanslutet HDInsight-kluster `user1@contoso.com` . Du kan också länka livy service-kluster.

1. Gå till **Visa**-  >  **verktyget Windows**  >  **Azure Explorer** från meny raden.

1. I Azure Explorer högerklickar du på noden **HDInsight** och väljer sedan **Länka ett kluster**.

   ![Snabb meny för Azure Explorer Link Cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. De tillgängliga alternativen i fönstret **Länka ett kluster** kan variera beroende på vilket värde du väljer i list rutan **länk resurs typ** .  Ange värdena och välj sedan **OK**.

    * **HDInsight-kluster**  
  
        |Egenskap |Värde |
        |----|----|
        |Länka resurs typ|Välj **HDInsight-kluster** i den nedrullningsbara listan.|
        |Kluster namn/URL| Ange kluster namn.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange kluster användar namn, standard är administratör.|
        |Lösenord| Ange ett lösen ord för användar namnet.|

        ![IntelliJ idé länka ett kluster dialog ruta](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy-tjänst**  
  
        |Egenskap |Värde |
        |----|----|
        |Länka resurs typ|Välj **livy-tjänst** i list rutan.|
        |Livy-slutpunkt| Ange livy-slutpunkt|
        |Klusternamn| Ange kluster namn.|
        |Garn slut punkt|Valfritt.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange kluster användar namn, standard är administratör.|
        |Lösenord| Ange ett lösen ord för användar namnet.|

        ![IntelliJ idé livy kluster dialog ruta](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Du kan se det länkade klustret från **HDInsight** -noden.

   ![Azure Explorer-länkade cluster1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. Du kan också ta bort länken mellan ett kluster och **Azure Explorer**.

   ![Azure Explorer-olänkat kluster](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Köra ett Spark Scala-program på ett HDInsight Spark-kluster

När du har skapat ett Scala-program kan du skicka det till klustret.

1. Från projekt navigerar du till **Mittprog**  >  **src**  >  **main**  >  **Scala**  >  **Mittprog**.  Högerklicka på **MyApp** och välj **Skicka Spark-program** (det kommer förmodligen att finnas längst ned i listan).

      ![Kommandot Skicka Spark-program till HDInsight](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. I dialog rutan **Skicka Spark-program** väljer du **1. Spark på HDInsight**.

3. I fönstret **Redigera konfiguration** anger du följande värden och väljer sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Spark-kluster (endast Linux)|Välj det HDInsight Spark-kluster som du vill köra ditt program på.|
    |Välj en artefakt som ska skickas|Lämna standardvärdet.|
    |Huvud klass namn|Standardvärdet är huvud klassen från den valda filen. Du kan ändra klassen genom att välja ellipsen (**...**)  och välja en annan klass.|
    |Jobb konfiguration|Du kan ändra standard nycklar och-värden. Mer information finns i [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html).|
    |Kommando rads argument|Du kan ange argument avgränsade med blank steg för huvud klassen om det behövs.|
    |Refererade jar v7 och refererade filer|Du kan ange sökvägar för refererade jar v7 och filer om det finns några. Du kan också bläddra i filer i det virtuella Azure-filsystemet, som för närvarande endast stöder ADLS gen 2-kluster. Mer information: [Apache Spark konfiguration](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Se även [hur du överför resurser till kluster](../../storage/blobs/storage-quickstart-blobs-storage-explorer.md).|
    |Jobb överförings lagring|Expandera för att visa fler alternativ.|
    |Lagringstyp|Välj **Använd Azure Blob för att överföra** från List rutan.|
    |Lagringskonto|Ange ditt lagrings konto.|
    |Lagrings nyckel|Ange din lagrings nyckel.|
    |Lagrings behållare|Välj din lagrings behållare i list rutan när **lagrings kontot** och **lagrings nyckeln** har angetts.|

    ![Dialog rutan för Spark-sändning](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Välj **SparkJobRun** för att skicka projektet till det valda klustret. I fliken **fjärr-Spark-jobb i kluster** visas jobb körnings förloppet längst ned. Du kan stoppa programmet genom att klicka på den röda knappen.

    ![Apache Spark sändnings fönster](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Felsöka Apache Spark program lokalt eller via fjärr anslutning i ett HDInsight-kluster

Vi rekommenderar också ett annat sätt att skicka Spark-programmet till klustret. Du kan göra detta genom att ange parametrarna i parametrarna för **körning/fel söknings konfiguration** IDE. Se [felsöka Apache Spark program lokalt eller via fjärr anslutning i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Få åtkomst till och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit for IntelliJ

Du kan utföra olika åtgärder med hjälp av Azure Toolkit for IntelliJ.  De flesta åtgärder startas från **Azure Explorer**.  Gå till **Visa**-  >  **verktyget Windows**  >  **Azure Explorer** från meny raden.

### <a name="access-the-job-view"></a>Öppna vyn jobb

1. Gå till **HDInsight**-  >  \<Your Cluster>  >  **jobb** från Azure Explorer.

    ![IntelliJ för Azure Explorer-jobb](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. I den högra rutan visar fliken **Spark Job View** alla program som kördes på klustret. Välj namnet på det program som du vill visa mer information om.

    ![Spark-jobb Visa programinformation](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. Om du vill visa grundläggande jobb information som körs, hovrar du över jobb diagrammet. Om du vill visa diagram stegen och information som varje jobb genererar väljer du en nod i jobb diagrammet.

    ![Spark-jobb Visa information om jobb steg](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Om du vill visa ofta använda loggar som *driv rutin stderr*, *driv rutin STDOUT* och *katalog information* väljer du fliken **logg** .

    ![Logg information för Spark-jobb](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

5. Du kan visa gränssnittet Spark-historik och garn gränssnittet (på program nivå). Välj en länk överst i fönstret.

### <a name="access-the-spark-history-server"></a>Få åtkomst till Spark historik Server

1. I Azure Explorer expanderar du **HDInsight**, högerklickar på ditt Spark-kluster och väljer sedan **öppna gränssnittet Spark-historik**.  
2. När du uppmanas till det anger du klustrets autentiseringsuppgifter för administratörer, som du angav när du konfigurerade klustret.

3. På instrument panelen för Spark-historiken kan du använda program namnet för att leta efter programmet som du precis har kört. I föregående kod ställer du in program namnet med hjälp av `val conf = new SparkConf().setAppName("myApp")` . Ditt Spark-programnamn är **MyApp**.

### <a name="start-the-ambari-portal"></a>Starta Ambari-portalen

1. I Azure Explorer expanderar du **HDInsight**, högerklickar på ditt Spark-kluster och väljer sedan **Öppna kluster hanteringsportal (Ambari)**.  

2. När du uppmanas till det anger du administratörens autentiseringsuppgifter för klustret. Du angav dessa autentiseringsuppgifter under kluster konfigurations processen.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer

Som standard visar Azure Toolkit for IntelliJ Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill få åtkomst till.  

1. I Azure Explorer högerklickar du på noden **Azure** -rotnod och väljer sedan **Välj prenumerationer**.  

2. I fönstret **Välj prenumerationer** avmarkerar du kryss rutorna bredvid de prenumerationer som du inte vill komma åt och väljer sedan **Stäng**.

## <a name="spark-console"></a>Spark-konsol

Du kan köra Spark Local Console (Scala) eller köra Spark livy Interactive Session Console (Scala).

### <a name="spark-local-consolescala"></a>Spark-lokal konsol (Scala)

Se till att du har uppfyllt kraven för WINUTILS.EXE.

1. Gå till meny raden och navigera till **Kör**  >  **Redigera konfigurationer...**.

2. I fönstret **Kör/Felsök-konfigurationer** går du till vänster-fönstret och navigerar till **Apache Spark på HDInsight**  >  **[Spark på HDInsight] MyApp**.

3. Välj fliken i huvud fönstret **`Locally Run`** .

4. Ange följande värden och välj sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Jobbets huvud klass|Standardvärdet är huvud klassen från den valda filen. Du kan ändra klassen genom att välja ellipsen (**...**)  och välja en annan klass.|
    |Miljövariabler|Se till att värdet för HADOOP_HOME är korrekt.|
    |WINUTILS.exe plats|Se till att sökvägen är korrekt.|

    ![Konfiguration av lokal konsol uppsättning](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Från projekt navigerar du till **Mittprog**  >  **src**  >  **main**  >  **Scala**  >  **Mittprog**.  

6. I meny raden navigerar du till **verktyg**  >  **Spark-konsolen**  >  **Kör Spark Local Console (Scala)**.

7. Sedan kan två dialog rutor visas för att fråga dig om du vill åtgärda beroenden automatiskt. I så fall väljer du **automatisk korrigering**.

    ![IntelliJ idé Spark, Auto Fix dialog1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ idé Spark, Auto Fix Dialog2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. Konsolen bör se ut ungefär som på bilden nedan. I konsol fönstret typ `sc.appName` , och tryck sedan på CTRL + RETUR.  Resultatet visas. Du kan avsluta den lokala konsolen genom att klicka på röd knapp.

    ![Resultat av IntelliJ idén-lokal konsol](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark livy interaktiv Session Console (Scala)

1. Gå till meny raden och navigera till **Kör**  >  **Redigera konfigurationer...**.

2. I fönstret **Kör/Felsök-konfigurationer** går du till vänster-fönstret och navigerar till **Apache Spark på HDInsight**  >  **[Spark på HDInsight] MyApp**.

3. Välj fliken i huvud fönstret **`Remotely Run in Cluster`** .

4. Ange följande värden och välj sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Spark-kluster (endast Linux)|Välj det HDInsight Spark-kluster som du vill köra ditt program på.|
    |Huvud klass namn|Standardvärdet är huvud klassen från den valda filen. Du kan ändra klassen genom att välja ellipsen (**...**)  och välja en annan klass.|

    ![Konfiguration av interaktiv konsol uppsättning](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Från projekt navigerar du till **Mittprog**  >  **src**  >  **main**  >  **Scala**  >  **Mittprog**.  

6. I meny raden navigerar du till **verktyg**  >  **Spark-konsolen**  >  **Kör Spark livy Interactive Session Console (Scala)**.

7. Konsolen bör se ut ungefär som på bilden nedan. I konsol fönstret typ `sc.appName` , och tryck sedan på CTRL + RETUR.  Resultatet visas. Du kan avsluta den lokala konsolen genom att klicka på röd knapp.

    ![Resultat av IntelliJ-idé interaktiv konsol](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Skicka markering till Spark-konsolen

Det är praktiskt att du kan förutse skript resultatet genom att skicka kod till den lokala konsolen eller livy-Scala (Interactive Session Console). Du kan markera en kod i Scala-filen och högerklicka på **Skicka markering till Spark-konsolen**. Den valda koden kommer att skickas till-konsolen. Resultatet kommer att visas efter koden i-konsolen. -Konsolen kontrollerar felen om de är befintliga.  

   ![Skicka markering till Spark-konsolen](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>Integrera med HDInsight Identity Broker (HIB)

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>Ansluta till ditt HDInsight ESP-kluster med ID-Broker (HIB)

Du kan följa de vanliga stegen för att logga in på Azure-prenumerationen för att ansluta till ditt HDInsight ESP-kluster med ID Broker (HIB). När du har loggat in visas kluster listan i Azure Explorer. Mer information finns i [ansluta till ditt HDInsight-kluster](#connect-to-your-hdinsight-cluster).

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Köra ett Spark Scala-program på ett HDInsight ESP-kluster med ID-Broker (HIB)

Du kan följa de normala stegen för att skicka jobb till HDInsight ESP-kluster med ID-Broker (HIB). Mer information hittar du i [köra ett Spark Scala-program på ett HDInsight Spark-kluster](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster) .

Vi överför de nödvändiga filerna till en mapp med namnet med ditt inloggnings konto, och du kan se uppladdnings Sök vägen i konfigurations filen.

   ![Ladda upp sökväg i konfigurationen](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>Spark-konsolen på ett HDInsight ESP-kluster med ID-Broker (HIB)

Du kan köra Spark Local Console (Scala) eller köra Spark livy Interactive Session Console (Scala) på ett HDInsight ESP-kluster med ID Broker (HIB). Mer information hittar du i [Spark-konsolen](#spark-console) .

   > [!NOTE]  
   > För HDInsight ESP-kluster med ID Broker (HIB) kan du [Länka ett kluster](#link-a-cluster) och [Felsöka Apache Spark program via fjärr anslutning](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster) som för närvarande inte stöds.

## <a name="reader-only-role"></a>Roll som endast läsare

När användare skickar jobb till ett kluster med endast läsar roll behörighet krävs Ambari-autentiseringsuppgifter.

### <a name="link-cluster-from-context-menu"></a>Länka kluster från snabb menyn

1. Logga in med roll kontot endast läsare.

2. Expandera **HDInsight** i **Azure Explorer** för att Visa HDInsight-kluster som finns i din prenumeration. Kluster som har marker ATS med **rollen "roll: läsare"** har bara behörighet som endast läsare.

    ![IntelliJ Azure Explorer-roll: läsare '](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. Högerklicka på klustret med roll behörigheten endast läsare. Välj **länka det här klustret** från snabb menyn för att länka klustret. Ange Ambari användar namn och lösen ord.

    ![IntelliJ Azure Explorer länka det här klustret](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. Om klustret har länkats kommer HDInsight att uppdateras.
   Klustrets steg kommer att länkas.
  
    ![IntelliJ Azure Explorer-länkad dialog ruta](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Länka kluster genom att expandera jobb-noden

1. Klicka på noden **jobb** , fönstret **åtkomst nekad åtkomst till kluster jobb** .

2. Klicka på **länka det här klustret** för att länka klustret.

    ![dialog rutan neka åtkomst till kluster jobb](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Länka kluster från fönstret kör/Felsök konfigurationer

1. Skapa en HDInsight-konfiguration. Välj sedan **fjärran sluten i kluster**.

2. Välj ett kluster som har behörighet för endast att läsa roller för **Spark-kluster (endast Linux)**. Varnings meddelandet visas. Du kan klicka på **Länka** klustret om du vill länka klustret.

   ![IntelliJ idé att köra/felsöka konfiguration skapa](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>Visa lagrings konton

* För kluster med behörighet för endast läsare, klickar du på noden **lagrings konton** , fönstret **åtkomst nekas för lagring** . Öppna Storage Explorer genom att klicka på **öppna Azure Storage Explorer** .

   !["IntelliJ idé Storage Access nekad"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![IntelliJ idé åtkomst nekad-knapp](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* För länkade kluster klickar du på noden **lagrings konton** , fönstret **lagrings åtkomst nekad** öppnas. Öppna Storage Explorer genom att klicka på **öppna Azure Storage** .

   !["IntelliJ idé Storage Access Denied2"](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ idé Storage Access Denied2-knappen](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konvertera befintliga IntelliJ idé-program till att använda Azure Toolkit for IntelliJ

Du kan konvertera befintliga Spark Scala-program som du skapade i IntelliJ-idén så att de är kompatibla med Azure Toolkit for IntelliJ. Du kan sedan använda plugin-programmet för att skicka in programmen till ett HDInsight Spark-kluster.

1. Öppna den associerade filen för ett befintligt Spark Scala-program som har skapats med IntelliJ-idén `.iml` .

2. På rotnivå är ett **modul** -element som följande text:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
    ```

   Redigera elementet som ska läggas till `UniqueKey="HDInsightTool"` så att **module** -elementet ser ut som följande text:

    ```xml
    <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
    ```

3. Spara ändringarna. Ditt program bör nu vara kompatibelt med Azure Toolkit for IntelliJ. Du kan testa det genom att högerklicka på projekt namnet i projektet. På popup-menyn finns nu alternativet **Skicka Spark-program till HDInsight**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet, tar du bort det kluster som du skapade med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan med HDInsight-kluster som visas väljer du **...** bredvid det kluster som du skapade för den här artikeln.

1. Välj **Ta bort**. Välj **Ja**.

![Azure Portal tar bort HDInsight-kluster](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder plugin-programmet Azure Toolkit for IntelliJ för att utveckla Apache Spark program som skrivits i [Scala](https://www.scala-lang.org/). Skickade sedan dem till ett HDInsight Spark-kluster direkt från IntelliJ-Integrated Development Environment (IDE). Gå vidare till nästa artikel för att se hur de data som du har registrerat i Apache Spark kan hämtas till ett BI-analysverktyg såsom Power BI.

> [!div class="nextstepaction"]
> [Analysera Apache Spark data med Power BI](apache-spark-use-bi-tools.md)