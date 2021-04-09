---
title: 'Azure Toolkit: Felsöka Apache Spark appar via fjärr anslutning – Azure HDInsight'
description: Lär dig hur du använder HDInsight-verktyg i Azure Toolkit for IntelliJ för att fjärrfelsöka Spark-program som körs på HDInsight-kluster via VPN.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/28/2017
ms.openlocfilehash: e6ed52f89c38a9c4c5476bd1db0f5a67326954e4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866275"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via fjärr anslutning i HDInsight via VPN

Vi rekommenderar att felsöka [Apache Spark](https://spark.apache.org/) program via en fjärr anslutning via SSH. Instruktioner finns i [fjärrfelsökning Apache Spark program i ett HDInsight-kluster med Azure Toolkit for IntelliJ via SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md).

Den här artikeln innehåller stegvisa instruktioner om hur du använder HDInsight-verktygen i Azure Toolkit for IntelliJ att skicka ett Spark-jobb på ett HDInsight Spark-kluster och sedan felsöka det via fjärr anslutning från den station ära datorn. För att slutföra dessa uppgifter måste du utföra följande steg på hög nivå:

1. Skapa ett virtuellt Azure-nätverk för plats-till-plats eller punkt-till-plats. Stegen i det här dokumentet förutsätter att du använder ett plats-till-plats-nätverk.
1. Skapa ett Spark-kluster i HDInsight som är en del av det virtuella plats-till-plats-nätverket.
1. Kontrol lera anslutningen mellan kluster huvud noden och skriv bordet.
1. Skapa ett Scala-program i IntelliJ-idén och konfigurera det för fjärrfelsökning.
1. Kör och Felsök programmet.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Mer information finns i [få en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Ett Apache Spark kluster i HDInsight**. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java Development Kit**. Du kan installera det från [Oracle-webbplatsen](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* **INTELLIJ idé**. Den här artikeln använder version 2017,1. Du kan installera det från [JetBrains-webbplatsen](https://www.jetbrains.com/idea/download/).
* **HDInsight-verktyg i Azure Toolkit for IntelliJ**. HDInsight Tools för IntelliJ är tillgängliga som en del av Azure Toolkit for IntelliJ. Anvisningar om hur du installerar Azure Toolkit finns i [installera Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Logga in på din Azure-prenumeration från IntelliJ-idén**. Följ anvisningarna i [använda Azure Toolkit for IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster](apache-spark-intellij-tool-plugin.md).
* **Undantags lösning**. När du kör Spark Scala-programmet för fjärrfelsökning på en Windows-dator kan du få ett undantag. Detta undantag beskrivs i [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356) och inträffar på grund av en WinUtils.exe-fil som saknas i Windows. För att undvika det här felet måste du ladda ned [Winutils.exe](https://github.com/steveloughran/winutils) till en plats, till exempel **C:\WinUtils\bin**. Lägg till en **HADOOP_HOME** miljö variabel och ange sedan värdet för variabeln till **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Steg 1: skapa ett virtuellt Azure-nätverk

Följ instruktionerna från följande länkar för att skapa ett virtuellt Azure-nätverk och kontrol lera anslutningen mellan din station ära dator och det virtuella nätverket:

* [Skapa ett VNet med en VPN-anslutning från plats till plats med hjälp av Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Skapa ett VNet med en plats-till-plats-VPN-anslutning med PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Steg 2: skapa ett HDInsight Spark-kluster

Vi rekommenderar att du även skapar ett Apache Spark kluster i Azure HDInsight som är en del av det virtuella Azure-nätverket som du har skapat. Använd den information som är tillgänglig i [skapa Linux-baserade kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Som en del av valfri konfiguration väljer du det virtuella Azure-nätverk som du skapade i föregående steg.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Steg 3: kontrol lera anslutningen mellan kluster huvud noden och skriv bordet

1. Hämta IP-adressen för Head-noden. Öppna Ambari-ANVÄNDARGRÄNSSNITTET för klustret. Välj **instrument panel** på kluster bladet.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png" alt-text="Välj instrument panel i Apache Ambari" border="true":::

1. Välj **värdar** i AMBARI-användargränssnittet.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png" alt-text="Välj värdar i Apache Ambari" border="true":::

1. Du ser en lista över Head-noder, arbetsnoder och Zookeeper-noder. Huvudnoderna har ett **HN***-prefix. Välj den första Head-noden.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png" alt-text="Hitta Head-noden i Apache Ambari" border="true":::

1. I fönstret **Sammanfattning** längst ned på sidan som öppnas kopierar du **IP-adressen** för Head-noden och **värd namnet**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png" alt-text="Hitta IP-adressen i Apache Ambari" border="true":::

1. Lägg till IP-adressen och värd namnet för Head-noden i **värd** filen på den dator där du vill köra och fjärrfelsöka Spark-jobbet. Detta gör att du kan kommunicera med Head-noden genom att använda IP-adressen, samt värd namnet.

   a. Öppna en anteckningar-fil med förhöjd behörighet. Från menyn **Arkiv** väljer du **Öppna** och letar reda på platsen för värd filen. På en Windows-dator är platsen **C:\Windows\System32\Drivers\etc\hosts**.

   b. Lägg till följande information i **hosts** -filen:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. På den dator som du anslöt till det virtuella Azure-nätverket som används av HDInsight-klustret kontrollerar du att du kan pinga huvudnoderna med hjälp av IP-adressen, samt värd namnet.

1. Använd SSH för att ansluta till klustrets huvud nod genom att följa anvisningarna i [ansluta till ett HDInsight-kluster med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Pinga IP-adressen för den station ära datorn från kluster Head-noden. Testa anslutningen till båda IP-adresserna som tilldelats datorn:

    - En för nätverks anslutningen
    - Ett för det virtuella Azure-nätverket

1. Upprepa stegen för den andra Head-noden.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Steg 4: skapa ett Apache Spark Scala-program med hjälp av HDInsight-verktyg i Azure Toolkit for IntelliJ och konfigurera det för fjärrfelsökning

1. Öppna IntelliJ-idé och skapa ett nytt projekt. Gör följande i dialogrutan **Nytt projekt**:

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png" alt-text="Välj den nya projekt mal len i IntelliJ-idén" border="true":::

    a. Välj **HDInsight**  >  **Spark på HDInsight (Scala)**.

    b. Välj **Nästa**.
1. Gör följande i dialog rutan nästa **nya projekt** och välj sedan **Slutför**:

    - Ange ett projektnamn och en plats.

    - I listrutan **Projekt-SDK** väljer du **Java 1.8** för Spark 2.x-klustret, eller **Java 1.7** för Spark 1.x-klustret.

    - I list rutan **Spark-version** integrerar guiden skapa Scala-projekt rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.0.2 (Scala 2.11.8)**.
  
   :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png" alt-text="Välj Project SDK och Spark-version" border="true":::
  
1. Spark-projektet skapar automatiskt en artefakt åt dig. Gör så här om du vill visa artefakten:

    a. Välj **projekt struktur** på **Arkiv** -menyn.

    b. I dialog rutan **projekt struktur** väljer du **artefakter** för att Visa standard artefakten som skapas. Du kan också skapa en egen artefakt genom att välja plus tecknet ( **+** ).

   :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png" alt-text="IntelliJ idé artefakter skapa jar" border="true":::

1. Lägg till bibliotek i projektet. Gör följande om du vill lägga till ett bibliotek:

    a. Högerklicka på projekt namnet i projekt trädet och välj sedan **Öppna inställningar för modul**.

    b. I dialog rutan **projekt struktur** väljer du **bibliotek**, väljer **+** symbolen () och väljer sedan **från maven**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png" alt-text="Bibliotek för IntelliJ idé hämtning" border="true":::

    c. I dialog rutan **Ladda ned bibliotek från maven-lagringsplats** söker du efter och lägger till följande bibliotek:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Kopiera `yarn-site.xml` och `core-site.xml` från kluster huvud-noden och Lägg till dem i projektet. Använd följande kommandon för att kopiera filerna. Du kan använda [Cygwin](https://cygwin.com/install.html) för att köra följande `scp` kommandon för att kopiera filerna från klustrets huvud noder:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Eftersom vi redan har lagt till IP-adressen och värd namnen för kluster Head-noden för värd filen på Skriv bordet kan vi använda `scp` kommandona på följande sätt:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Om du vill lägga till filerna i projektet kopierar du dem under mappen **/src** i projekt trädet, till exempel `<your project directory>\src` .

1. Uppdatera `core-site.xml` filen för att göra följande ändringar:

   a. Ersätt den krypterade nyckeln. `core-site.xml`Filen innehåller den krypterade nyckeln till det lagrings konto som är associerat med klustret. I den `core-site.xml` fil som du har lagt till i projektet ersätter du den krypterade nyckeln med den faktiska lagrings nyckeln som är kopplad till standard lagrings kontot. Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Ta bort följande poster från `core-site.xml` :

    ```xml
    <property>
            <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
            <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
    </property>

    <property>
            <name>fs.azure.shellkeyprovider.script</name>
            <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
    </property>

    <property>
            <name>net.topology.script.file.name</name>
            <value>/etc/hadoop/conf/topology_script.py</value>
    </property>
    ```

   c. Spara filen.

1. Lägg till huvud klassen för ditt program. I **Project Explorer** högerklickar du på **src**, pekar på **ny** och väljer sedan Scala- **klass**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png" alt-text="IntelliJ idé Välj huvud klass" border="true":::

1. I dialog rutan **Skapa ny Scala-klass** anger du ett namn, väljer **objekt** i rutan **typ** och väljer sedan **OK**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png" alt-text="IntelliJ idé skapa ny Scala-klass" border="true":::

1. I `MyClusterAppMain.scala` filen klistrar du in följande kod. Den här koden skapar Spark-kontexten och öppnar en `executeJob` metod från `SparkSample` objektet.

    ```scala
    import org.apache.spark.{SparkConf, SparkContext}

    object SparkSampleMain {
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("SparkSample")
                                    .set("spark.hadoop.validateOutputSpecs", "false")
        val sc = new SparkContext(conf)

        SparkSample.executeJob(sc,
                            "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                            "wasb:///HVACOut")
        }
    }
    ```

1. Upprepa steg 8 och 9 om du vill lägga till ett nytt Scala-objekt med namnet `*SparkSample` . Lägg till följande kod i den här klassen. Den här koden läser data från HVAC.csv (tillgängliga i alla HDInsight Spark-kluster). Den hämtar de rader som bara har en siffra i den sjunde kolumnen i CSV-filen och skriver sedan utdata till **/HVACOut** under standard lagrings behållaren för klustret.

    ```scala
    import org.apache.spark.SparkContext

    object SparkSample {
        def executeJob (sc: SparkContext, input: String, output: String): Unit = {
        val rdd = sc.textFile(input)

        //find the rows which have only one digit in the 7th column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
        println(s)

        rdd1.saveAsTextFile(output)
        //rdd1.collect().foreach(println)
         }
    }
    ```

1. Upprepa steg 8 och 9 för att lägga till en ny klass som heter `RemoteClusterDebugging` . Den här klassen implementerar Spark test ramverket som används för att felsöka programmen. Lägg till följande kod i `RemoteClusterDebugging` klassen:

    ```scala
        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }
    ```

     Det finns några viktiga saker att tänka på:

      * För `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")` kontrollerar du att Spark Assembly jar är tillgänglig på kluster lagringen på den angivna sökvägen.
      * För `setJars` anger du den plats där ARTEFAKT burken ska skapas. Normalt är det `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` .

1. I `*RemoteClusterDebugging` -klassen högerklickar du på `test` nyckelordet och väljer sedan **skapa RemoteClusterDebugging-konfiguration**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png" alt-text="IntelliJ idé att skapa en fjärran sluten konfiguration" border="true":::

1. I dialog rutan **skapa RemoteClusterDebugging konfiguration** anger du ett namn för konfigurationen och väljer sedan **test typ** som **testnamn**. Lämna alla andra värden som standardinställningar. Tryck på **Tillämpa** och välj sedan **OK**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png" alt-text="Skapa RemoteClusterDebugging-konfiguration" border="true":::

1. Nu bör du se en nedrullningsbar listruta för **fjärrkörning** i meny raden.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png" alt-text="IntelliJ den nedrullningsbara listan fjärrkörning" border="true":::

## <a name="step-5-run-the-application-in-debug-mode"></a>Steg 5: kör programmet i fel söknings läge

1. Öppna `SparkSample.scala` och skapa en Bryt punkt bredvid i ditt IntelliJ-idé projekt `val rdd1` . På popup-menyn **skapa Bryt punkt för** väljer du **rad i funktionen executeJob**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png" alt-text="IntelliJ idé Lägg till en Bryt punkt" border="true":::

1. Om du vill köra programmet väljer du knappen **Felsök körning** bredvid List rutan **fjärrkörnings** konfiguration.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png" alt-text="IntelliJ idé Välj knappen Felsök körning" border="true":::

1. När program körningen når Bryt punkten visas fliken **fel sökare** i det nedre fönstret.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png" alt-text="IntelliJ idé Visa fliken fel sökare" border="true":::

1. Om du vill lägga till en Watch väljer du **+** ikonen ().

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png" alt-text="IntelliJ-felsökning – Lägg till Watch-variabel" border="true":::

    I det här exemplet skapades programmet innan variabeln `rdd1` skapades. Med den här klockan kan vi se de fem första raderna i variabeln `rdd` . Välj **Retur**.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png" alt-text="IntelliJ köra programmet i fel söknings läge" border="true":::

    Det du ser i föregående bild är att vid körning kan du fråga terabyte med data och felsöka hur ditt program fortskrider. I de utdata som visas i föregående bild kan du till exempel se att den första raden i utdata är en rubrik. Baserat på dessa utdata kan du ändra program koden för att hoppa över rubrik raden, om det behövs.

1. Nu kan du välja ikonen för att **fortsätta** med att köra programmet.

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png" alt-text="IntelliJ idé Välj återuppta program" border="true":::

1. Om programmet har slutförts bör du se utdata som liknar följande:

    :::image type="content" source="./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png" alt-text="IntelliJ idé fel söknings konsolens utdata" border="true":::

## <a name="next-steps"></a><a name="seealso"></a>Nästa steg

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
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Skapa Apache Spark-program med hjälp av HDInsight-verktyg i Azure Toolkit for Eclipse](./apache-spark-eclipse-tool-plugin.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i ett Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Spåra och felsöka jobb som körs på ett Apache Spark kluster i HDInsight](apache-spark-job-debugging.md)