---
title: 'Självstudie: analysera Azure HDInsight Apache Spark data med Power BI'
description: Självstudie – Använd Microsoft Power BI för att visualisera Apache Spark data som lagras i HDInsight-kluster
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: a688f47e11e76f573ba2b99816e1fb402f4c50d2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870559"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Självstudie: Analysera Apache Spark-data med Power BI i HDInsight

I den här självstudien får du lära dig hur du använder Microsoft Power BI för att visualisera data i ett Apache Spark kluster i Azure HDInsight.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Visualisera Spark-data med Power BI

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Slutför artikeln [Självstudie: Läsa in data och köra frågor på ett Apache Spark-kluster i Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Valfritt: [Power BI utvärderings prenumeration](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Kontrollera datan

Den [Jupyter Notebook](https://jupyter.org/)-anteckningsbok som du skapade i [föregående självstudie](apache-spark-load-data-run-query.md) innehåller kod för att skapa en `hvac`-tabell. Den här tabellen baseras på CSV-filen som är tillgänglig på alla HDInsight Spark-kluster på `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv` . Följ stegen nedan för att verifiera datan.

1. Klistra in följande kod från Jupyter Notebook och tryck sedan på **SKIFT + RETUR**. Koden kontrollerar att tabellerna finns.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Utdata ser ut så här:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-show-tables.png" alt-text="Visa tabellerna i Spark" border="true":::

    Om du har stängt anteckningsboken innan du startar självstudien så har `hvactemptable` rensats, så den ingår inte i utdatan.  Endast Hive-tabeller som lagras i metaarkivet (anges med **False** under kolumnen **isTemporary**) kan nås från BI-verktygen. I den här självstudien kommer du ansluta till **hvac**-tabellen som du skapade.

2. Klistra in följande kod i en tom cell och tryck sedan på **SKIFT+RETUR**. Koden verifierar datan i tabellen.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Utdata ser ut så här:

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-select-limit.png" alt-text="Visa rader från hvac-tabellen i Spark" border="true":::

3. Välj **Stäng och stoppa** på anteckningsbokens **Arkiv**-meny. Stäng anteckningsboken för att frigöra resurser.

## <a name="visualize-the-data"></a>Visualisera datan

I det här avsnittet använder du Power BI för att skapa visualiseringar, rapporter och instrumentpaneler från Spark-klusterdata.

### <a name="create-a-report-in-power-bi-desktop"></a>Skapa en rapport i Power BI Desktop

De första stegen i att arbeta med Spark är att ansluta till klustret i Power BI Desktop, läsa in data från klustret och skapa en grundläggande visualisering baserat på dessa data.

1. Öppna Power BI Desktop. Stäng välkomst skärmen start sida om den öppnas.

2. På fliken **Start** går du till **Hämta data**  >  **mer..**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png " alt-text="Hämta data till Power BI Desktop från HDInsight Apache Spark" border="true":::er = "true"::

3. Ange `Spark` i sökrutan, välj **Azure HDInsight Spark** och välj sedan **Anslut**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png " alt-text="Hämta data till Power BI från Apache Spark bi" border="true":::er = "true"::

4. Ange din kluster-URL (i formuläret `mysparkcluster.azurehdinsight.net` ) i text rutan **Server** .

5. Under **data anslutnings läge** väljer du **DirectQuery**. Välj sedan **OK**.

    Du kan använda valfritt dataanslutningsläge med Spark. Om du använder DirectQuery visas ändringarna i rapporter utan att hela datamängden uppdateras. Om du importerar data måste du uppdatera datamängden för att se ändringarna. Mer information om hur och när du ska använda DirectQuery finns i [Använda DirectQuery i Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Ange inloggnings konto informationen för HDInsight och välj sedan **Anslut**. Standardkontonamnet är *admin*.

7. Välj `hvac` tabellen, vänta med att visa en förhands granskning av data och välj sedan **load**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png " alt-text="Användar namn och lösen ord för Spark-kluster" border="true":::d "Border =" true "::

    Power BI Desktop har den information som behövs för att ansluta till Spark-klustret och läsa in data från `hvac`-tabellen. Tabellen och dess kolumner visas i fönstret **Fält**.

8. Visualisera skillnaden mellan måltemperatur och faktisk temperatur för varje byggnad:

    1. I fönstret **VISUALISERINGAR** väljer du **Ytdiagram**.

    2. Dra fältet **BuildingID** till **Axel** och dra fälten **ActualTemp** och **TargetTemp** till **Värde**.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png " alt-text="Lägg till värde kolumner" border="true":::t = "Lägg till värde kolumner" kant = "true"::

        Diagrammet ser ut så här:

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png " alt-text="ytdiagram sum" border="true":::lt-text = "områdes diagram Summa" kant linje = "true"::

        Som standard visar visualiseringen summan för **ActualTemp** och **TargetTemp**. Välj nedpilen bredvid **ActualTemp** och **TragetTemp** i fönstret visualiseringar. du kan se **Sum** är markerat.

    3. Välj nedpilen bredvid **ActualTemp** och **TragetTemp** i fönstret visualiseringar och välj **genomsnitt** för att få ett genomsnitt av faktiska och mål temperatur för varje byggnad.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png " alt-text="medelvärde för värden" border="true":::t = "medelvärde för värden" Border = "true"::

        Din datavisualisering bör likna den på skärmbilden. Flytta markören över visualiseringen för att få verktygstips med relevanta data.

        :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png " alt-text="ytdiagram" border="true":::. png "Alt-text =" ytdiagram "Border =" true "::

9. Navigera till **fil**  >  **Spara**, ange namnet `BuildingTemperature` på filen och välj sedan **Spara**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publicera rapporten till Power BI-tjänsten (valfritt)

Med Power BI-tjänsten kan du dela rapporter och instrumentpaneler i din organisation. I det här avsnittet publicerar du först datamängden och rapporten. Sedan fäster du rapporten på en instrumentpanel. Instrument paneler används vanligt vis för att fokusera på en delmängd av data i en rapport. Du har bara en visualisering i rapporten, men det är fortfarande användbart att gå igenom stegen.

1. Öppna Power BI Desktop.

1. På fliken **Start** väljer du **publicera**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png " alt-text="Publicera från Power BI Desktop" border="true"::: Desktop "Border =" true "::

1. Välj en arbets yta att publicera din data uppsättning och rapportera till och välj sedan **Välj**. I följande bild är standardinställningen **Min arbetsyta** markerad.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png " alt-text="Välj arbets yta för att publicera data uppsättning och rapportera till" border="true":::UE "::

1. När publiceringen är klar väljer du **Öppna "BuildingTemperature. pbix" i Power BI**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png " alt-text="Publicera lyckade, klicka för att ange autentiseringsuppgifterna" border="true":::er = "true"::

1. I Power BI-tjänst väljer du **ange autentiseringsuppgifter**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png " alt-text="Ange autentiseringsuppgifter i Power BI-tjänst" border="true":::"Border =" true "::

1. Välj **Redigera autentiseringsuppgifter**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png " alt-text="Redigera autentiseringsuppgifter i Power BI-tjänst" border="true":::e "Border =" true "::

1. Ange inloggnings konto informationen för HDInsight och välj sedan **Logga in**. Standardkontonamnet är *admin*.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png " alt-text="Logga in på Spark-kluster" border="true"::: Spark-kluster "Border =" true "::

1. I det vänstra fönstret går du till **arbets ytor**  >  **Mina arbets ytans**  >  **rapporter** och väljer sedan **BuildingTemperature**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png " alt-text="Rapport som visas under rapporter i vänster fönster" border="true":::ordning = "true"::

    Du bör också se **BuildingTemperature** under **DATAMÄNGDER** i den vänstra rutan.

    Det visuella objektet som du skapade i Power BI Desktop är nu tillgängligt i Power BI-tjänsten.

1. Håll markören över visualiseringen och välj sedan Fäst ikonen i det övre högra hörnet.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png " alt-text="Rapport i Power BI-tjänsts" border="true":::tjänsten "Border =" true "::

1. Välj "ny instrument panel", ange namnet `Building temperature` och välj sedan **Fäst**.

    :::image type="content" source="./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png " alt-text="Fäst på ny instrument panel" border="true"::: till en ny instrument panel "Border =" true "::

1. I rapporten väljer **du gå till instrument panel**.

Ditt visuella objekt är fastsatt på instrumentpanelen. Du kan lägga till fler visuella objekt i rapporten och fästa dem på samma instrumentpanel. Mer information om rapporter och instrumentpaneler finns i [Rapporter i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) och [Instrumentpaneler i Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört vägledningen kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används.

Om du vill ta bort ett kluster läser du [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda Microsoft Power BI för att visualisera data i ett Apache Spark kluster i Azure HDInsight. Gå vidare till nästa artikel för att se hur du kan skapa ett maskin inlärnings program.

> [!div class="nextstepaction"]
> [Skapa ett Machine Learning-program](./apache-spark-ipython-notebook-machine-learning.md)