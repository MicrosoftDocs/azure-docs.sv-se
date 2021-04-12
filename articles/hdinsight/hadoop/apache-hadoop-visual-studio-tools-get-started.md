---
title: Apache Hadoop & Visual Studio Data Lake-verktyg – Azure HDInsight
description: Lär dig hur du installerar och använder Data Lake verktyg för Visual Studio. Använd verktyget för att ansluta till Apache Hadoop kluster i Azure HDInsight och kör sedan Hive-frågor.
keywords: hadoop tools,hive query,visual studio,visual studio hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: c6969c3108fb1465a705d8e61b3a7bdf2083736a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865663"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Använd Data Lake verktyg för Visual Studio för att ansluta till Azure HDInsight och köra Apache Hive frågor

Lär dig hur du använder Microsoft Azure Data Lake och Stream Analytics verktyg för Visual Studio (Data Lake-verktyg). Använd verktyget för att ansluta till [Apache Hadoop kluster i Azure HDInsight](apache-hadoop-introduction.md) och skicka Hive-frågor.  

Mer information om hur du använder HDInsight finns i [Kom igång med HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Mer information om hur du ansluter till Apache Storm finns i [utveckla C#-topologier för Apache Storm med hjälp av data Lake verktyg](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Data Lake Tools för Visual Studio kan användas för att komma åt Azure Data Lake Analytics och HDInsight. Information om Data Lake Tools finns i [Utveckla U-SQL-skript med hjälp av Data Lake Tools för Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här artikeln och använda Data Lake verktyg för Visual Studio behöver du följande objekt:

* Ett Azure HDInsight-kluster. Information om hur du skapar ett HDInsight-kluster finns i [komma igång genom att använda Apache Hadoop i Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Om du vill köra interaktiva Apache Hive frågor behöver du ett [Interactive HDInsight](../interactive-query/apache-interactive-query-get-started.md) -kluster.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) är kostnads fri. Anvisningarna som visas här gäller för [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake-verktyg för Visual Studio  

Följ de anvisningar som krävs för att installera Data Lake verktyg för din version av Visual Studio:

* För Visual Studio 2017 eller Visual Studio 2019:

    Under installationen av Visual Studio ser du till att du tar med arbets belastningen **Azure Development** eller arbets belastningen **data lagring och bearbetning** .  

    För befintliga Visual Studio-installationer går du till IDE-meny raden och väljer **verktyg**  >  **Hämta verktyg och funktioner** för att öppna Visual Studio Installer. På fliken **arbets belastningar** väljer du minst **Azure Development** -arbetsbelastningen (under **webb & molnet**). Eller Välj arbets belastningen **data lagring och bearbetning** (under **andra verktyg**).

  :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png" alt-text="Val av arbets belastning, Visual Studio Installer" border="true":::

* För Visual Studio 2015:

    [Ladda ned data Lake verktyg](https://www.microsoft.com/download/details.aspx?id=49504). Välj den version av Data Lake Tools som matchar din version av Visual Studio.

## <a name="update-data-lake-tools-for-visual-studio"></a>Uppdatera Data Lake verktyg för Visual Studio  

Se sedan till att du uppdaterar Data Lake-verktyg till den senaste versionen.

1. Öppna Visual Studio.

2. I fönstret **Starta** väljer du **Fortsätt utan kod**.

3. I meny raden i Visual Studio IDE väljer du **tillägg**  >  **Hantera tillägg**.

4. Expandera noden **uppdateringar** i dialog rutan **Hantera tillägg** .

5. Om listan med tillgängliga uppdateringar innehåller **Azure Data Lake och Stream analys verktyg**, väljer du det. Välj sedan knappen **Uppdatera** . När dialog rutan **Hämta och installera** visas och försvinner, lägger Visual Studio till tillägget **Azure Data Lake och Stream analys verktyg** till uppdaterings schemat.

6. Stäng alla Visual Studio-fönster. Dialog rutan **installations program för vsix** visas.

7. Välj **licens** för att läsa licens villkoren och välj sedan **Stäng** för att återgå till dialog rutan **installations program för vsix** .

8. Välj **ändra**. Installationen av tilläggs uppdateringen påbörjas. Efter en stund ändras dialog rutan så att den gör ändringar. Välj **Stäng** och starta sedan om Visual Studio för att slutföra installationen.

> [!NOTE]  
> Du kan endast använda Data Lake Tools i version 2.3.0.0 eller senare för att ansluta till interaktiva frågekluster och köra interaktiva Hive-frågor.

## <a name="connect-to-azure-subscriptions"></a>Ansluta till Azure-prenumerationer

Du kan använda Data Lake verktyg för Visual Studio för att ansluta till dina HDInsight-kluster, utföra vissa grundläggande hanterings åtgärder och köra Hive-frågor.

> [!NOTE]  
> Information om hur du ansluter till ett allmänt Hadoop-kluster finns i [skriva och skicka Hive-frågor med Visual Studio](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio).

### <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

Så här ansluter du till din Azure-prenumeration:

1. Öppna Visual Studio.

2. I fönstret **Starta** väljer du **Fortsätt utan kod**.

3. I meny raden IDE väljer du **Visa**  >  **Server Explorer**.

4. I **Server Explorer** högerklickar du på **Azure**, väljer **Anslut till Microsoft Azure prenumeration** och slutför autentiseringsprocessen. Från **Server Explorer** expanderar du **Azure**  >  **HDInsight** för att visa en lista över befintliga HDInsight-kluster.

5. Om du inte har några kluster kan du skapa ett med hjälp av Azure Portal, Azure PowerShell eller HDInsight SDK. Mer information finns i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png" alt-text="HDInsight-kluster lista, Server Explorer, Visual Studio" border="true":::

6. Expandera ett HDInsight-kluster. Klustret innehåller noder för **Hive-databaser**. Ett standard lagrings konto, eventuella ytterligare länkade lagrings konton och **loggen för Hadoop-tjänsten**. Du kan expandera entiteterna ytterligare.

När du har anslutit till din Azure-prenumeration kan du utföra följande uppgifter.

### <a name="connect-to-azure-from-visual-studio"></a>Ansluta till Azure från Visual Studio

Så här ansluter du till Azure-portalen från Visual Studio:

1. I **Server Explorer** expanderar du **Azure**  >  **HDInsight** och väljer ditt kluster.

2. Högerklicka på ett HDInsight-kluster och välj **Hantera kluster i Azure Portal**.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Erbjud frågor och feedback från Visual Studio

Så här ställer du frågor och ger feedback från Visual Studio:

1. Välj **Azure**  >  **HDInsight** från Server Explorer.

2. Högerklicka på **HDInsight** och välj antingen **MSDN-forumet** för att ställa frågor eller **ge feedback** för att ge feedback.

## <a name="link-to-or-edit-a-cluster"></a>Länka till eller redigera ett kluster

> [!NOTE]
> För närvarande är den enda typen av HDInsight-kluster som du kan länka till en Hive-typ.

Så här länkar du ett HDInsight-kluster:

1. Högerklicka på **HDInsight** och välj **Länka ett HDInsight-kluster** för att Visa dialog rutan **Länka ett HDInsight-kluster** .

2. Ange en **anslutnings-URL** i formuläret `https://CLUSTERNAME.azurehdinsight.net` . **Kluster namnet** fylls i automatiskt med kluster namns delen i URL: en när du går till ett annat fält. Ange sedan ett **användar namn** och **lösen ord** och välj **Nästa**.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png" alt-text="Länka ett kluster, HDInsight, Visual Studio" border="true":::

3. Välj **Slutför**. Om kluster länkningen lyckas visas klustret under noden **HDInsight** .

Om du vill uppdatera ett länkat kluster högerklickar du på klustret och väljer **Redigera**. Sedan kan du uppdatera kluster informationen.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png" alt-text="Redigera ett länkat kluster, HDInsight, Visual Studio" border="true":::

## <a name="explore-linked-resources"></a>Utforska länkade resurser

Från Server Explorer kan du se standardkontot för lagring och eventuella länkade lagringskonton. Om du expanderar standardkontot för lagring kan du se containrarna på lagringskontot. Standardlagringskontot och standardcontainern är markerade.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png" alt-text="Data Lake verktyg för länkade Visual Studio-resurser i Server Explorer" border="true":::

Högerklicka på en behållare och välj **Visa behållare** för att Visa behållarens innehåll. När du har öppnat en behållare kan du använda verktygsfälts knapparna för att **Uppdatera** innehålls listan, **Ladda upp BLOB**, **ta bort valda blobbar**, **Öppna BLOB** och hämta (**Spara som**) valda blobbar.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png" alt-text="Behållare lista och blob-åtgärder, HDInsight-kluster, Visual Studio" border="true":::

## <a name="run-interactive-apache-hive-queries"></a>Kör interaktiva Apache Hives frågor

[Apache Hive](https://hive.apache.org) är en infrastruktur för informationslager som bygger på Hadoop. Hive används för att sammanfatta data, frågor och analys. Du kan använda Data Lake Tools för Visual Studio för att köra Hive-frågor från Visual Studio. Mer information om Hive finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md).

[Interaktiv fråga i Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) använder [Hive på LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) i Apache Hive 2,1. Interaktiv fråga ger interaktivitet till komplexa, data lager stils frågor på stora, lagrade data uppsättningar. Körning av Hive-frågor på interaktiva frågor är mycket snabbare än traditionella Hive-batchjobb. 

> [!NOTE]  
> Du kan bara köra interaktiva Hive-frågor när du ansluter till ett kluster med en [interaktiv HDInsight-fråga](../interactive-query/apache-interactive-query-get-started.md).

Du kan också använda Data Lake verktyg för Visual Studio för att se vad som finns i ett Hive-jobb. Data Lake Tools för Visual Studio samlar in och hämtar Yarn-loggarna för specifika Hive-jobb.

Välj  **Azure**  >  **HDInsight** från Server Explorer och välj ditt kluster.  Den här noden är start punkten i **Server Explorer** för de avsnitt som ska följas.

### <a name="view-hivesampletable"></a>Visa hivesampletable

Alla HDInsight-kluster har en standardformat Hive-tabell med namnet `hivesampletable` .  

Från klustret väljer du **Hive-databaser**  >  **standard**  >  **hivesampletable**.

* Så här visar du `hivesampletable` schemat:

    Expandera **hivesampletable**. Kolumnernas namn och data typer `hivesampletable` visas.

* Så här visar du `hivesampletable` data:

    Högerklicka på **hivesampletable** och välj **visa de översta 100 raderna**. Listan med 100-resultat visas i **Hive-tabellen: hivesampletable** -fönstret. Den här åtgärden motsvarar att köra följande Hive-fråga med hjälp av Hive ODBC-drivrutinen:

    `SELECT * FROM hivesampletable LIMIT 100`

    Du kan anpassa rad antalet genom att ändra **antalet rader**. Du kan välja 50, 100, 200 eller 1000 rader från den nedrullningsbara listan.

### <a name="create-hive-tables"></a>Skapa Hive-tabeller

Du kan använda det grafiska användargränssnittet eller Hive-frågor för att skapa en Hive-tabell. Information om hur du använder Hive-frågor finns i [skapa och köra Hive-frågor](#create-and-run-hive-queries).

1. Välj Hive-databaser som   >  **standard** från klustret.

2. Högerklicka på **standard** och välj **Skapa tabell**.

3. Konfigurera tabellen.

4. Klicka på knappen **Skapa tabell** för att skicka jobbet, vilket skapar den nya Hive-tabellen.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png" alt-text="Skapa tabell fönster, Hive, HDInsight-kluster, Visual Studio" border="true":::

### <a name="create-and-run-hive-queries"></a>Skapa och köra Hive-frågor

Det finns två sätt att skapa och köra Hive-frågor:

* Skapa ad hoc-frågor
* Skapa ett Hive-program

#### <a name="create-an-ad-hoc-query"></a>Skapa en ad hoc-fråga

Skapa och köra en ad hoc-fråga:

1. Högerklicka på det kluster där du vill köra frågan och välj **Skriv en Hive-fråga**.  

2. Ange en Hive-fråga.

    Hive-redigeraren stöder IntelliSense. Data Lake Tools för Visual Studio stöder inläsning av fjärrmetadata när du redigerar Hive-skript. Om du till exempel skriver visas `SELECT * FROM` alla föreslagna tabell namn i IntelliSense. När du anger ett tabellnamn visar IntelliSense en lista över kolumnnamnen. Verktygen stöder de flesta Hive DML-instruktioner, underfrågor och inbyggda UDF.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png" alt-text="IntelliSense-exempel 1, Hive ad-hoc-fråga, HDInsight-kluster, Visual Studio" border="true":::

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png" alt-text="IntelliSense-exempel 2, Hive ad-hoc-fråga, HDInsight-kluster, Visual Studio" border="true":::

    > [!NOTE]  
    > IntelliSense föreslår endast metadata för kluster som valts i verktygsfältet för HDInsight.

    Här är en exempel fråga som du kan använda:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Välj körnings läge:

    * **Interaktiv**  

        Välj **interaktiv** i den första List rutan och välj sedan **Kör**.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png" alt-text="Interaktivt läge, Hive ad-hoc-fråga, HDInsight-kluster, Visual Studio" border="true":::  

    * **Batch**  

        Välj **batch** i den första List rutan och välj sedan **Skicka**. Eller Välj den nedrullningsbara ikonen bredvid **Skicka** och välj **Avancerat**.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png" alt-text="Batch-läge, Hive ad-hoc-fråga, HDInsight-kluster, Visual Studio" border="true":::

        Om du väljer alternativet Avancerad överföring visas dialog rutan **Skicka skript** . Konfigurera **jobb namn**, **argument**, **ytterligare konfigurationer** och **status katalog** för skriptet.

        :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png" alt-text="Dialog rutan Skicka skript, Hive ad-hoc-fråga, HDInsight-kluster, Visual Studio" border="true":::

      > [!NOTE]  
      > Du kan inte skicka batchar till interaktiva Query-kluster.  Du måste använda interaktivt läge.

#### <a name="create-a-hive-application"></a>Skapa ett Hive-program

Så här skapar och kör du en Hive-lösning:

1. Välj **Arkiv**  >  **nytt**  >  **projekt** på Meny raden.

2. I fönstret **skapa ett nytt projekt** markerar du rutan Sök och skriver **Hive**. Välj sedan **Hive-program** och välj **Nästa**.

3. I fönstret **Konfigurera ditt nya projekt** anger du ett **projekt namn**, väljer eller skapar projekt **platsen** och väljer sedan **skapa**.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png" alt-text="Nytt Hive-program, konfigurera ditt nya projekt fönster, HDInsight Visual Studio" border="true":::

4. Gå till **Solution Explorer** och dubbelklicka på **Script.hql** för att öppna skriptet.

### <a name="view-job-summary-and-output"></a>Visa jobb Sammanfattning och utdata

Jobb sammanfattningen varierar något mellan **batch** -och **interaktivt** läge.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png" alt-text="Sammanfattning av Hive-jobb, fönster, batch och interaktivt läge, Visual Studio" border="true":::

Använd **uppdaterings** ikonen för att uppdatera statusen tills jobbets status ändras till **avslutad**.  

* För jobb information från **batch** -läge väljer du länkarna längst ned för att se **jobb frågan**, **jobbets utdata** eller **jobb loggen** eller för att **Visa garn loggar**.

* För jobb information från **interaktivt** läge, se fönstret **utdata** -och **HiveServer2** .

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png" alt-text="Hive-utdata för interaktiva jobb, HDInsight-kluster, Visual Studio" border="true":::

### <a name="view-job-graph"></a>Visa jobb diagram

För närvarande visas jobb diagram bara för Hive-jobb som använder Tez som körnings motor.  Information om hur du aktiverar Tez finns i [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md).  Se också [Använd Apache Tez i stället för kart minskning](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Om du vill visa alla operatorer i hörnen dubbelklickar du på hörnen i jobb diagrammet. Du kan också peka på en specifik operator för att se mer information om den.

Även om Tez har angetts som körnings motor kanske inte jobb diagrammet visas om inget Tez-program startas.  Den här situationen kan inträffa eftersom jobbet inte innehåller DML-instruktioner. Eller eftersom DML-instruktionerna kan returneras utan att starta ett Tez-program. Starta till exempel `SELECT * FROM table1` inte Tez-programmet.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png" alt-text="Apache Hive jobb diagram, Visual Studio" border="true":::

### <a name="view-task-execution-detail"></a>Visa information om körning av aktivitet

I jobb diagrammet kan du välja information om **aktivitets körning** för att få strukturerad och visualiserad information för Hive-jobb. Du kan också få mer jobb information. Om det uppstår prestandaproblem kan du använda vyn för att få mer information om problemet. Du kan till exempel hämta information om hur varje aktivitet fungerar och detaljerad information om varje aktivitet (Läs/skriv-, schema-/start-och slut tid med mera). Använd informationen för att finjustera jobbkonfigurationer eller systemarkitektur baserat på den visualiserade informationen.

:::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png" alt-text="Fönstret uppgifts körnings visning, Data Lake Visual Studio-verktyg" border="true":::

### <a name="view-hive-jobs"></a>Visa Hive-jobb

Du kan visa jobbfrågor, jobbutdata, jobbloggar och Yarn-loggar för Hive-jobb.

I den senaste versionen av verktygen kan du se vad som finns i dina Hive-jobb genom att samla in och Visa garn loggar. En Yarn-logg kan hjälpa dig att undersöka prestandaproblem. Mer information om hur HDInsight samlar in garn loggar finns i [Access Apache HADOOP garn program loggar](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Så här visar du Hive-jobb:

1. Högerklicka på ett HDInsight-kluster och välj **Visa jobb**.

    :::image type="content" source="./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png" alt-text="Visa jobb, Apache Hive, HDInsight-kluster, Visual Studio" border="true":::

    En lista visas över de Hive-jobb som körts på klustret.  

2. Välj ett jobb. I fönstret **Sammanfattning av Hive-jobb** väljer du någon av följande länkar:
    - **Jobbfråga**
    - **Jobbutdata**
    - **Jobblogg**  
    - **Garn logg**

## <a name="run-apache-pig-scripts"></a>Köra apache gris-skript

1. Välj **Arkiv**  >  **nytt**  >  **projekt** på Meny raden.

2. I fönstret **Starta** väljer du sökrutan och anger **gris**. Välj sedan **programmet gris** och välj **Nästa**.

3. I fönstret **Konfigurera ditt nya projekt** anger du ett **projekt namn** och väljer eller skapar en **plats** för projektet. Välj sedan **Skapa**.

4. I fönstret IDE- **Solution Explorer** dubbelklickar du på **skript. gris** för att öppna skriptet.

## <a name="feedback-and-known-issues"></a>Feedback och kända problem

* Ett problem där resultat som har startats med null-värden inte visas har åtgärdats. Om du är blockerad för det här problemet kan du kontakta supportgruppen.

* HQL-skriptet som Visual Studio skapar är kodat, beroende på användarens lokala region inställning. Skriptet kan inte köras korrekt om du laddar upp skriptet till ett kluster som en binär fil.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur använder Data Lake Tools för Visual Studio för att ansluta till HDInsight-kluster från Visual Studio. Du har också lärt dig hur du kör en Hive-fråga. 

* [Köra Apache Hive-frågor med hjälp av Data Lake Tools för Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Vad är Apache Hive och HiveQL på Azure HDInsight?](hdinsight-use-hive.md)
* [Skapa Apache Hadoop-kluster – mall](apache-hadoop-linux-tutorial-get-started.md)
* [Skicka Apache Hadoop jobb i HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analysera Twitter-data med Apache Hive och Apache Hadoop på HDInsight](../hdinsight-analyze-twitter-data-linux.md)