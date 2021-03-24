---
title: 'Azure Toolkit for Eclipse: skapa Scala-appar för HDInsight Spark'
description: Använd HDInsight-verktyg i Azure Toolkit for Eclipse för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster direkt från de Sol förmörkelse som IDE.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/13/2019
ms.openlocfilehash: e4f19afc0edf0f43b64548ae1c93ed5ff8dfa8a1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866581"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Använd Azure Toolkit for Eclipse för att skapa Apache Spark-program för ett HDInsight-kluster

Använd HDInsight-verktyg i Azure Toolkit [för att](https://www.eclipse.org/) utveckla [Apache Spark](https://spark.apache.org/) program som är skrivna i [Scala](https://www.scala-lang.org/) och skicka dem till ett Azure HDInsight Spark-kluster direkt från de Sol förmörkelse IDE. Du kan använda plugin-programmet för HDInsight-verktyg på ett par olika sätt:

* För att utveckla och skicka ett Scala Spark-program på ett HDInsight Spark-kluster.
* För att få åtkomst till Azure HDInsight Spark kluster resurser.
* För att utveckla och köra ett Scala Spark-program lokalt.

## <a name="prerequisites"></a>Förutsättningar

* Apache Spark kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* [Sol FÖRMÖRKELSE IDE](https://www.eclipse.org/downloads/). I den här artikeln används Sol förmörkelse IDE för Java-utvecklare.

## <a name="install-required-plug-ins"></a>Installera nödvändiga plugin-program

### <a name="install-azure-toolkit-for-eclipse"></a>Installera Azure Toolkit for Eclipse

Installations anvisningar finns i [installera Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse/installation).

### <a name="install-the-scala-plug-in"></a>Installera plugin-programmet Scala

När du öppnar Sol förmörkelse identifierar HDInsight-verktyg automatiskt om du har installerat Scala-plugin-programmet. Välj **OK** för att fortsätta och följ sedan anvisningarna för att installera plugin-programmet från Marketplace för Sol förmörkelse. Starta om IDE när installationen är klar.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png" alt-text="Automatisk installation av Scala-plugin-programmet" border="true":::

### <a name="confirm-plug-ins"></a>Bekräfta plugin-program

1. Navigera till **under** Marketplace för under  >  **förmörkelse...**.

1. Välj fliken **installerad** .

1. Du bör se minst:
    * Azure Toolkit for Eclipse \<version> .
    * Scala IDE \<version> .

## <a name="sign-in-to-your-azure-subscription"></a>Logga in på din Azure-prenumeration

1. Starta Sol förmörkelse IDE.

1. Navigera till **fönster**  >   **Visa**  >  **annan vy...**  >  **Logga in..**.

1. I dialog rutan **Visa vy** går du till **Azure**  >  **Azure Explorer** och väljer sedan **Öppna**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png" alt-text="Apache Spark Sol förmörkelse Visa vy" border="true":::

1. I **Azure Explorer** högerklickar du på **Azure** -noden och väljer sedan **Logga** in.

1. I dialog rutan **Azure-inloggning** väljer du autentiseringsmetod, sedan **Logga in** och slutför inloggnings processen.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png" alt-text="Apache Spark Sol förmörkelse Azure-tecken" border="true":::

1. När du har loggat in visas alla Azure-prenumerationer som är kopplade till autentiseringsuppgifterna i dialog rutan **prenumerationer** . Tryck på **Välj** för att stänga dialog rutan.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png" alt-text="Dialog rutan Välj prenumerationer" border="true":::

1. Från **Azure Explorer** navigerar du till **Azure**  >   **HDInsight** för att se HDInsight Spark-kluster under din prenumeration.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png" alt-text="HDInsight Spark-kluster i Azure Explorer3" border="true":::

1. Du kan ytterligare expandera noden kluster namn för att se de resurser (till exempel lagrings konton) som är associerade med klustret.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png" alt-text="Expandera ett kluster namn om du vill se resurser" border="true":::

## <a name="link-a-cluster"></a>Länka ett kluster

Du kan länka ett vanligt kluster med Ambari-hanterade användar namn. På samma sätt kan du länka med hjälp av domänen och användar namnet, till exempel, för ett domänanslutet HDInsight-kluster `user1@contoso.com` .

1. I **Azure Explorer** högerklickar du på **HDInsight** och väljer **Länka ett kluster**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png" alt-text="Meny för länk kluster i Azure Explorer" border="true":::

1. Ange **kluster namn**, **användar namn** och **lösen ord** och välj sedan **OK**. Du kan också ange lagrings konto, lagrings nyckel och välja lagrings behållare för lagrings Utforskaren som ska fungera i den vänstra trädvyn

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png" alt-text="Dialog rutan länka nytt HDInsight-kluster" border="true":::

   > [!NOTE]  
   > Vi använder den länkade lagrings nyckeln, användar namnet och lösen ordet om klustret både är inloggat i Azure-prenumerationen och länkade ett kluster.
   > :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png" alt-text="Lagrings konton i Azure Explorer" border="true":::
   >
   > För endast tangent bords användare, när det aktuella fokus ligger på **lagrings nyckeln**, måste du använda **CTRL + TABB** för att fokusera på nästa fält i dialog rutan.

1. Du kan se det länkade klustret under **HDInsight**. Nu kan du skicka ett program till det här länkade klustret.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png" alt-text="Azure Explorer HDI-länkat kluster" border="true":::

1. Du kan också ta bort länken mellan ett kluster och **Azure Explorer**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png" alt-text="Azure Explorer-olänkat kluster" border="true":::

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Konfigurera ett Spark Scala-projekt för ett HDInsight Spark-kluster

1. Välj **Arkiv**  >  **nytt** projekt från den här IDE-arbetsytan  >  **.**...

1. I guiden **nytt projekt** väljer du **HDInsight Project**  >  **Spark på HDInsight (Scala)**. Välj sedan **Nästa**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png" alt-text="Välja Scala-projekt (Spark on HDInsight)" border="true":::

1. Ange följande värden i dialog rutan **nytt HDInsight Scala-projekt** och välj sedan **Nästa**:
   * Ange ett namn för projektet.
   * I **JRE** -ytan kontrollerar du att **Använd en körnings miljö JRE** är inställt på **Java-1,7** eller senare.
   * I **biblioteket för Spark-bibliotek** kan du välja **Använd Maven för att konfigurera Spark SDK** -alternativet.  Vårt verktyg integrerar rätt version för Spark SDK och Scala SDK. Du kan också välja **Lägg till Spark SDK manuellt** , hämta och lägga till Spark SDK genom att manuellt.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png" alt-text="Dialog rutan nytt HDInsight Scala-projekt" border="true":::

1. Granska informationen i nästa dialog ruta och välj sedan **Slutför**.

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Scala-program för ett HDInsight Spark-kluster

1. Expandera projektet som du skapade tidigare i **Package Explorer**. Högerklicka på **src**, Välj **ny**  >  **annan..**..

1. I dialog rutan **Välj en guide** väljer du **Scala-guider**  >  **Scala objekt**. Välj sedan **Nästa**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png" alt-text="Välj en guide skapa ett Scala-objekt" border="true":::

1. I dialog rutan **Skapa ny fil** anger du ett namn för objektet och väljer sedan **Slutför**. En text redigerare öppnas.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png" alt-text="Guiden ny fil skapa ny fil" border="true":::

1. I text redigeraren ersätter du det aktuella innehållet med koden nedan:

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasbs:///HVACOut")
        }
    }
    ```

1. Kör programmet på ett HDInsight Spark-kluster:

   a. I Package Explorer högerklickar du på projekt namnet och väljer **Skicka Spark-program till HDInsight**.

   b. Ange följande värden i dialog rutan **Spark-överföring** och välj sedan **Skicka**:

   * För **kluster namn** väljer du det HDInsight Spark-kluster som du vill köra ditt program på.
   * Välj en artefakt från Sol förmörkelse-projektet eller Välj en från en hård disk. Standardvärdet beror på vilket objekt du högerklickar på från Package Explorer.
   * I list rutan för **huvud klass namn** visas alla objekt namn från projektet i guiden Skicka. Välj eller ange en som du vill köra. Om du har valt en artefakt från en hård disk måste du ange huvud klass namnet manuellt. 
   * Eftersom program koden i det här exemplet inte kräver några kommando rads argument eller referens jar v7 eller filer, kan du lämna de återstående text rutorna tomma.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png" alt-text="Dialog rutan Apache Spark sändning" border="true":::

1. Fliken **Spark-överföring** ska börja visa förloppet. Du kan stoppa programmet genom att välja knappen röd i fönstret **Spark-överföring** . Du kan också visa loggarna för det här specifika programmet genom att välja ikonen för jordglob (med den blå rutan i bilden).

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png" alt-text="Apache Spark sändnings fönster" border="true":::

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Få åtkomst till och hantera HDInsight Spark-kluster med hjälp av HDInsight-verktyg i Azure Toolkit for Eclipse

Du kan utföra olika åtgärder med hjälp av HDInsight-verktyg, inklusive åtkomst till jobbets utdata.

### <a name="access-the-job-view"></a>Öppna vyn jobb

1. I **Azure Explorer** expanderar du **HDInsight**, sedan Spark-klustrets namn och väljer sedan **jobb**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png" alt-text="Nod för jobb visning i Azure Explorer" border="true":::

1. Välj noden **jobb** . Om Java-versionen är lägre än **1,8** så påminnelser HDInsight-verktyg automatiskt att du installerar E-plugin-programmet **(FX)** . Välj **OK** för att fortsätta och följ sedan guiden för att installera den från Marketplace för Sol förmörkelse och starta om Sol förmörkelse.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png" alt-text="Installera ett plugin-program som saknas E-klipp (FX)" border="true":::

1. Öppna vyn jobb från noden **jobb** . I den högra rutan visar fliken **Spark Job View** alla program som kördes på klustret. Välj namnet på det program som du vill visa mer information om.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png" alt-text="Apache Sol förmörkelse Visa information om jobb loggar" border="true":::

   Du kan sedan vidta någon av följande åtgärder:

   * Hovra över jobb diagrammet. Den visar grundläggande information om jobbet som körs. Välj jobb diagrammet och du kan se de faser och information som varje jobb genererar.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png" alt-text="Information om Apache Spark jobb diagram steg" border="true":::

   * Välj fliken **logg** om du vill visa vanliga loggar, inklusive **driv rutin stderr**, **driv Rutins-STDOUT** och **katalog information**.

     :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png" alt-text="Apache Spark Sol förmörkelse jobb logg information" border="true":::

   * Öppna gränssnittet Spark-historik och Apache Hadoop garn gränssnitt (på program nivå) genom att välja hyperlänkarna överst i fönstret.

### <a name="access-the-storage-container-for-the-cluster"></a>Åtkomst till lagrings behållaren för klustret

1. I Azure Explorer expanderar du **HDInsight** -rotnoden för att se en lista över HDInsight Spark-kluster som är tillgängliga.

1. Expandera kluster namnet om du vill se lagrings kontot och standard lagrings behållaren för klustret.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png" alt-text="Lagrings konto och standard lagrings behållare" border="true":::

1. Välj det lagrings behållar namn som är associerat med klustret. I den högra rutan dubbelklickar du på mappen **HVAC** . Öppna en **av filerna för att se** resultatet av programmet.

### <a name="access-the-spark-history-server"></a>Få åtkomst till Spark historik Server

1. I Azure Explorer högerklickar du på namnet på ditt Spark-kluster och väljer sedan **Öppna användar gränssnitt för Spark-historik**. När du uppmanas till det anger du administratörens autentiseringsuppgifter för klustret. Du angav dessa när du etablerade klustret.

1. På instrument panelen för Spark-historiken använder du program namnet för att leta efter programmet som du precis har kört. I föregående kod ställer du in program namnet med hjälp av `val conf = new SparkConf().setAppName("MyClusterApp")` . Därför var ditt Spark-programnamn **MyClusterApp**.

### <a name="start-the-apache-ambari-portal"></a>Starta Apache Ambari-portalen

1. I Azure Explorer högerklickar du på namnet på ditt Spark-kluster och väljer sedan **Öppna kluster hanteringsportal (Ambari)**.

1. När du uppmanas till det anger du administratörens autentiseringsuppgifter för klustret. Du angav dessa när du etablerade klustret.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer

Som standard visar HDInsight-verktyget i Azure Toolkit for Eclipse Spark-klustren från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill använda för att komma åt klustret.

1. I Azure Explorer högerklickar du på noden **Azure** -rotnod och väljer sedan **Hantera prenumerationer**.

1. I dialog rutan avmarkerar du kryss rutorna för den prenumeration som du inte vill komma åt och väljer sedan **Stäng**. Du kan också välja **Logga ut** om du vill logga ut från din Azure-prenumeration.

## <a name="run-a-spark-scala-application-locally"></a>Köra ett Spark Scala-program lokalt

Du kan använda HDInsight-verktyg i Azure Toolkit for Eclipse för att köra Spark Scala-program lokalt på din arbets Station. Normalt behöver dessa program inte åtkomst till kluster resurser, till exempel en lagrings behållare, och du kan köra och testa dem lokalt.

### <a name="prerequisite"></a>Förutsättning

När du kör det lokala Spark Scala-programmet på en Windows-dator kan du få ett undantag som beskrivs i [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356). Detta undantag beror på att **WinUtils.exe** saknas i Windows.

För att lösa det här felet måste du [Winutils.exe](https://github.com/steveloughran/winutils) till en plats som **C:\WinUtils\bin** och sedan lägga till miljövariabeln **HADOOP_HOME** och ange värdet för variabeln till **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Köra ett lokalt Spark Scala-program

1. Starta Sol förmörkelse och skapa ett projekt. I dialog rutan **nytt projekt** gör du följande val och väljer sedan **Nästa**.

1. I guiden **nytt projekt** väljer du **HDInsight Project**  >  **Spark på lokalt HDInsight-testexempel (Scala)**. Välj sedan **Nästa**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png" alt-text="Nytt projekt väljer en guide dialog ruta" border="true":::

1. Om du vill ange projekt information följer du steg 3 till 6 i det tidigare avsnittet [Konfigurera ett Spark Scala-projekt för ett HDInsight Spark-kluster](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Mallen lägger till en exempel kod (**LogQuery**) under **src** -mappen som du kan köra lokalt på datorn.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/local-scala-application.png" alt-text="Plats för LogQuery-lokalt Scala-program" border="true":::

1. Högerklicka på **LogQuery. Scala** och välj **Kör som**  >  **1 Scala-program**. Utdata som detta visas på fliken **konsol** :

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png" alt-text="Spark-programmets lokala körnings resultat" border="true":::

## <a name="reader-only-role"></a>Roll som endast läsare

När användare skickar jobb till ett kluster med endast läsar roll behörighet krävs Ambari-autentiseringsuppgifter.

### <a name="link-cluster-from-context-menu"></a>Länka kluster från snabb menyn

1. Logga in med roll kontot endast läsare.

2. Expandera **HDInsight** i **Azure Explorer** för att Visa HDInsight-kluster som finns i din prenumeration. Kluster som har marker ATS med **rollen "roll: läsare"** har bara behörighet som endast läsare.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png" alt-text="HDInsight Spark-kluster i Azure Explorer-roll läsaren" border="true":::

3. Högerklicka på klustret med roll behörigheten endast läsare. Välj **länka det här klustret** från snabb menyn för att länka klustret. Ange Ambari användar namn och lösen ord.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png" alt-text="HDInsight Spark-kluster i Azure Explorer-länk" border="true":::

4. Om klustret har länkats kommer HDInsight att uppdateras.
   Klustrets steg kommer att länkas.
  
    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png" alt-text="HDInsight Spark-kluster i Azure Explorer länkade" border="true":::

### <a name="link-cluster-by-expanding-jobs-node"></a>Länka kluster genom att expandera jobb-noden

1. Klicka på noden **jobb** , fönstret **åtkomst nekad åtkomst till kluster jobb** .

2. Klicka på **länka det här klustret** för att länka klustret.

    :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png" alt-text="HDInsight Spark-kluster i Azure Explorer9" border="true":::

### <a name="link-cluster-from-spark-submission-window"></a>Länka kluster från Spark-fönstret

1. Skapa ett HDInsight-projekt.

2. Högerklicka på paketet. Välj sedan **Skicka Spark-program till HDInsight**.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png" alt-text="HDInsight Spark-kluster i Azure Explorer skicka" border="true":::

3. Välj ett kluster som har behörighet för endast läsare för **kluster namn**. Varnings meddelandet visas. Du kan klicka på **Länka** klustret om du vill länka klustret.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png" alt-text="HDInsight Spark-kluster i Azure Explorer länka detta" border="true":::

### <a name="view-storage-accounts"></a>Visa lagrings konton

* För kluster med behörighet för endast läsare, klickar du på noden **lagrings konton** , fönstret **åtkomst nekas för lagring** .

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png" alt-text="HDInsight Spark-kluster i Azure Explorer-lagring" border="true":::

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png" alt-text="HDInsight Spark-kluster i Azure Explorer nekas" border="true":::

* För länkade kluster klickar du på noden **lagrings konton** , fönstret **lagrings åtkomst nekad** öppnas.

   :::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png" alt-text="HDInsight Spark-kluster i Azure Explorer denied2" border="true":::

## <a name="known-problems"></a>Kända problem

När du använder **Länka ett kluster** rekommenderar vi att du anger autentiseringsuppgifter för lagring.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png" alt-text="länka kluster med överlappande autentiseringsuppgifter för lagring" border="true":::

Det finns två lägen för att skicka jobben. Om du har angett autentiseringsuppgifter för lagring kommer batch-läget att användas för att skicka jobbet. Annars kommer interaktivt läge att användas. Om klustret är upptaget kan du få felet nedan.

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png " alt-text="Sol förmörkelse får ett fel meddelande när klustret är upptaget" border="true":::

:::image type="content" source="./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png " alt-text="Sol förmörkelse får ett fel meddelande när klustrets upptagna garn" border="true":::

## <a name="see-also"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använd Azure Toolkit for IntelliJ för att skapa och skicka Spark Scala-program](apache-spark-intellij-tool-plugin.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via VPN](./apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använd Azure Toolkit for IntelliJ för att felsöka Apache Spark program via SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)