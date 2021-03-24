---
title: Felsöka Apache Spark-jobb som körs i Azure HDInsight
description: Använd garn gränssnitt, Spark UI och Spark historik Server för att spåra och felsöka jobb som körs på ett Spark-kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 0dd250f0a8f67d7e370b8ff453e9cff4d88b7896
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866105"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Felsöka Apache Spark-jobb som körs i Azure HDInsight

I den här artikeln får du lära dig att spåra och felsöka Apache Spark jobb som körs på HDInsight-kluster. Fel sökning med Apache Hadoop garn gränssnittet, Spark-ANVÄNDARGRÄNSSNITTET och Spark-historik servern. Du startar ett Spark-jobb med en bärbar dator som är tillgänglig med Spark-klustret, **Machine Learning: förutsägande analys av livsmedels inspektions data med MLLib**. Använd följande steg för att spåra ett program som du har skickat med någon annan metod, till exempel **Spark-Submit**.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Du bör ha börjat köra antecknings boken, **[Machine Learning: förutsägande analys av livsmedels inspektions data med MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Följ länken om du vill ha mer information om hur du kör den här antecknings boken.  

## <a name="track-an-application-in-the-yarn-ui"></a>Spåra ett program i garn gränssnittet

1. Starta garn gränssnittet. Välj **garn** under **kluster instrument paneler**.

    :::image type="content" source="./media/apache-spark-job-debugging/launch-apache-yarn-ui.png" alt-text="Azure Portal starta garn gränssnittet" border="true":::

   > [!TIP]  
   > Du kan också starta garn gränssnittet från Ambari-ANVÄNDARGRÄNSSNITTET. Starta Ambari-ANVÄNDARGRÄNSSNITTET genom att välja **Ambari Home** under **kluster instrument paneler**. Från Ambari-användargränssnittet navigerar du till  >  **snabb länkar** för garn > Active Resource Manager-> **Resource Manager-användargränssnittet**.

2. Eftersom du startade Spark-jobbet med Jupyter-anteckningsböcker har programmet namnet **remotesparkmagics** (namnet på alla program som startas från antecknings böckerna). Välj program-ID: t mot program namnet för att få mer information om jobbet. Den här åtgärden startar programvisningen.

    :::image type="content" source="./media/apache-spark-job-debugging/find-application-id1.png" alt-text="Spark-historik Server hitta Spark-programid" border="true":::

    För sådana program som startas från Jupyter Notebooks **körs** alltid statusen tills du avslutar antecknings boken.

3. Från programvyn kan du öka detalj nivån ytterligare för att ta reda på de behållare som är kopplade till programmet och loggarna (STDOUT/STDERR). Du kan också starta Spark-ANVÄNDARGRÄNSSNITTET genom att klicka på länken som motsvarar **spårnings-URL: en**, som visas nedan.

    :::image type="content" source="./media/apache-spark-job-debugging/download-container-logs.png" alt-text="Spark historik Server Hämta behållar loggar" border="true":::

## <a name="track-an-application-in-the-spark-ui"></a>Spåra ett program i Spark-ANVÄNDARGRÄNSSNITTET

I Spark-ANVÄNDARGRÄNSSNITTET kan du öka detalj nivån i Spark-jobben som har skapats av det program som du startade tidigare.

1. Starta Spark-ANVÄNDARGRÄNSSNITTET från vyn program genom att välja länken mot **spårnings-URL: en**, som visas i skärmdumpen ovan. Du kan se alla Spark-jobb som startas av programmet som körs i Jupyter Notebook.

    :::image type="content" source="./media/apache-spark-job-debugging/view-apache-spark-jobs.png" alt-text="Fliken jobb i Spark historik Server" border="true":::

2. Välj fliken **körningar** om du vill se bearbetnings-och lagrings information för varje utförar. Du kan också hämta anrops stacken genom att välja länken **tråd dum par** .

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-executors.png" alt-text="Fliken körningar av Spark historik Server" border="true":::

3. Välj fliken **steg** för att se de steg som är kopplade till programmet.

    :::image type="content" source="./media/apache-spark-job-debugging/view-apache-spark-stages.png " alt-text="Fliken Server faser i Spark-historik" border="true":::

    Varje steg kan ha flera aktiviteter som du kan använda för att Visa körnings statistik, som du ser nedan.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-details.png " alt-text="Fliken Detaljer för Server etapper för Spark-historik" border="true":::

4. På sidan information om scenen kan du starta DAG visualisering. Expandera länken **dag visualisering** överst på sidan, som du ser nedan.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png" alt-text="Visa DAG visualisering för Spark-faser" border="true":::

    DAG eller Direct Aclyic Graph representerar de olika stegen i programmet. Varje blå ruta i diagrammet representerar en spark-åtgärd som anropas från programmet.

5. På sidan information om scenen kan du också starta vyn tids linje för program. Expandera länken för **händelsens tids linje** överst på sidan, som du ser nedan.

    :::image type="content" source="./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png" alt-text="Visa händelse tids linje för Spark-faser" border="true":::

    Den här bilden visar Spark-händelserna i form av en tids linje. Vyn tids linje är tillgänglig på tre nivåer, mellan jobb, inom ett jobb och inom ett steg. Bilden ovan fångar vyn tids linje för ett angivet Stadium.

   > [!TIP]  
   > Om du markerar kryss rutan **Aktivera zoomning** kan du rulla åt vänster och höger i vyn tids linje.

6. Andra flikar i Spark-ANVÄNDARGRÄNSSNITTET ger också värdefull information om Spark-instansen.

   * Fliken lagring – om ditt program skapar en RDD kan du hitta information på fliken lagring.
   * Fliken miljö – den här fliken innehåller användbar information om din spark-instans, till exempel:
     * Scala-version
     * Händelse logg katalog som är kopplad till klustret
     * Antal utförar-kärnor för programmet

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Hitta information om slutförda jobb med Spark historik Server

När ett jobb har slutförts sparas informationen om jobbet på Spark-historik servern.

1. Om du vill starta Spark-historik servern väljer du **Spark historik Server** under **kluster instrument paneler** på sidan **Översikt** .

    :::image type="content" source="./media/apache-spark-job-debugging/launch-spark-history-server.png " alt-text="Azure Portal starta Spark-historik Server" border="true":::

   > [!TIP]  
   > Du kan också starta Spark-historik serverns användar gränssnitt från Ambari-ANVÄNDARGRÄNSSNITTET. Om du vill starta Ambari-ANVÄNDARGRÄNSSNITTET väljer du **Ambari start** under **kluster instrument paneler** på bladet översikt. I Ambari-användargränssnittet navigerar du till **Spark2**  >  **Quick Links**  >  **Spark2 History Server UI**.

2. Alla slutförda program visas. Välj ett program-ID för att öka detalj nivån i ett program för mer information.

    :::image type="content" source="./media/apache-spark-job-debugging/view-completed-applications.png " alt-text="Spark-historik Server slutförde program" border="true":::

## <a name="see-also"></a>Se även

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Felsöka Apache Spark jobb med utökad Spark-historik Server](apache-azure-spark-history-server.md)
* [Felsöka Apache Spark program med Azure Toolkit for IntelliJ via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
