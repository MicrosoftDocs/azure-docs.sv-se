---
title: Kör en Databricks Notebook med aktiviteten
description: Lär dig hur du kan använda Databricks Notebook-aktiviteten i Azure Data Factory för att köra en Databricks Notebook mot Databricks-jobbklustret.
ms.service: data-factory
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 03/12/2018
ms.openlocfilehash: 20253954035798187f28c18c8207c114d27bc9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374090"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Kör en Databricks Notebook med Databricks Notebook-aktiviteten i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Azure Portal för att skapa en Azure Data Factory-pipeline som kör en Databricks Notebook mot Databricks-jobbklustret. Den skickar även Azure Data Factory-parametrar till Databricks Notebook under körning.

I den här självstudiekursen får du göra följande:

  - Skapa en datafabrik.

  - Skapa en pipeline som använder en Databricks Notebook-aktivitet.

  - Utlös en pipelinekörning.

  - Övervaka pipelinekörningen.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Förutsättningar

  - **Azure Databricks arbets yta**. [Skapa en Databricks-arbetsyta](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) eller Använd en befintlig. Du skapar en Python Notebook i Azure Databricks-arbetsytan. Sedan kör du denna notebook och skickar parametrar till den med hjälp av Azure Data Factory.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1.  Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.

1.  Välj **Skapa en resurs** på den vänstra menyn, välj **Analys** och välj sedan **Data Factory**.

    ![Skapa en ny datafabrik](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  I fönstret **Ny datafabrik** anger du **ADFTutorialDataFactory** under **Namn**.

    Namnet på Azure Data Factory måste vara *globalt unikt*. Om du ser följande fel ska du ändra namnet på datafabriken. (Använd till exempel **\<yourname\> ADFTutorialDataFactory**). Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](./naming-rules.md).

    ![Ange ett namn för den nya datafabriken](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**.

1.  Gör något av följande för **Resursgrupp**:
    
    - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.
    
    - Välj **Skapa ny** och ange namnet på en resursgrupp.

    Några av stegen i den här snabb starten förutsätter att du använder namnet **ADFTutorialResourceGroup** för resurs gruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

1.  För **Version** väljer du **V2**.

1.  För **Plats** väljer du en plats för datafabriken.

    Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). De datalager (som Azure Storage och Azure SQL Database) och beräkningar (som HDInsight) som används i Data Factory kan finnas i andra regioner.
1.  Välj **Skapa**.


1.  När den har skapats visas sidan **data fabrik** . Välj ikonen **Författare och övervakare** för att starta programmet Data Factory UI på en separat flik.

    ![Starta användargränssnittsprogrammet för datafabriken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster

I det här avsnittet skapar du en Databricks-länkad tjänst. Den här länkade tjänsten innehåller anslutningsinformation till Databricks-klustret:

### <a name="create-an-azure-databricks-linked-service"></a>Skapa en länkad Azure Databricks-tjänst

1.  På sidan **Nu sätter vi igång** växlar du till fliken **Redigera** i den vänstra panelen.

    ![Redigera den nya länkade tjänsten](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Välj **Anslutningar** längst ned i fönstret och sedan **+Ny**.
    
    ![Skapa en ny anslutning](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  I fönstret **Ny länkad tjänst** väljer du **Compute** \> **Azure Databricks** och klickar på **Fortsätt**.
    
    ![Ange en länkad Databricks-tjänst](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):
    
    1.  Som **namn** anger du **_AzureDatabricks \_ LinkedService_**
    
    1.  Välj lämplig **Databricks-arbetsyta** som du ska köra din notebook i

    1.  För **Välj kluster** väljer du **Nytt jobbkluster**
    
    1.  För **Domän/Region** bör informationen fyllas i automatiskt

    1.  Generera en **Åtkomsttoken** från Azure Databricks-arbetsplatsen. Du hittar anvisningar [här](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  För **Klusterversion** väljer du **4.2** (med Apache Spark 2.3.1, Scala 2.11)

    1.  För **Klusternodtyp** väljer du **Standard\_D3\_v2** under kategorin **Generell användning (HDD)** för den här självstudien. 
    
    1.  För **Arbetare** anger du **2**.
    
    1.  Välj **Slutför**

        ![Slutför skapandet av den länkade tjänsten](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Skapa en pipeline

1.  Välj **+** knappen (plus) och välj sedan **pipeline** på menyn.

    ![Knappar för att skapa en ny pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Skapa en **parameter** som ska användas i **pipelinen**. Senare kan du skicka den här parametern till Databricks Notebook-aktiviteten. I den tomma pipelinen klickar du på fliken **Parametrar**, sedan på **Ny** och ger den namnet ”**namn**”.

    ![Skapa en ny parameter](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Skapa namnparametern](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Gå till verktygsfältet **Aktiviteter** och expandera **Databricks**. Dra aktiviteten **Notebook** från verktygsfältet **Aktiviteter** till pipelinedesignytan.

    ![Dra denna notebook till designytan](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Utför följande steg i egenskaperna för **Databricks** **Notebook**-aktivitetsfönstret längst ned:

    a. Växla till fliken **Azure Databricks**.

    b. Välj **AzureDatabricks\_LinkedService** (som du skapade i den föregående proceduren).

    c. Växla till fliken **Inställningar**

    c. Bläddra och välj en Databricks **Notebook-sökväg**. Nu ska vi skapa en notebook och ange sökvägen här. Du kan hämta Notebook-sökvägen genom att följa de kommande stegen.

       1. Starta din Azure Databricks-arbetsyta

       1. Skapa en **Ny mapp** i arbetsplatsen och ge den namnet **adftutorial**.

          ![Skapa en ny mapp](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Skapa en ny Notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (python), låt oss kalla den till min **bärbara dator** under mappen **adftutorial** Klicka på **skapa.**

          ![Skapa en ny notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Ange egenskaperna för denna nya notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. I din nyligen skapade notebook, ”mynotebook”, lägger du till följande kod:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input&quot;, &quot;&quot;,&quot;")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Skapa widgetar för parametrar](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. **Notebook-sökvägen** är i det här fallet **/adftutorial/mynotebook**

1.  Växla tillbaka till **redigeringsverktyget för Data Factory-användargränssnittet**. Gå till fliken **inställningar** under **Aktiviteten Notebook1**.

    a.  **Lägg till parameter** i Notebook-aktiviteten. Du använder samma parameter som du lade till tidigare i **Pipeline**.

       ![Lägg till parameter](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Namnge parametern som **inmatad** och ange värdet som uttrycks **\@ pipeline (). Parameters. name**.

1.  Verifiera pipelinen genom att välja knappen **Verifiera** i verktygsfältet. Stäng verifierings fönstret genom att välja **\>\>** knappen (högerpil).

    ![Verifiera pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Välj **Publicera alla**. Data Factory-gränssnittet publicerar entiteter (länkade tjänster och pipelines) till Azure Data Factory-tjänsten.

    ![Publicera de nya datafabriksentiteterna](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning

Välj **Utlös** i verktygsfältet och sedan **Trigger Now** (Utlös nu).

![Välj kommandot Utlös nu](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

Dialogrutan **Kör pipeline** efterfrågar parametern **namn**. Använd **/path/filename** som den här parametern. Klicka på **Slutför.**

![Ange ett värde för namnparametrarna](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1.  Växla till fliken **övervaka** . Bekräfta att du ser en pipeline-körning. Det tar cirka 5–8 minuter att skapa ett Databricks-jobbkluster där en notebook körs.

    ![Övervaka pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Klicka på **Uppdatera** då och då så att du ser pipelinekörningens status.

1.  Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna väljer på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Åtgärd**.

    ![Visa aktivitetskörningar](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Du kan gå tillbaka till vyn över pipelinekörningar genom att välja länken **Pipelines**.

## <a name="verify-the-output"></a>Verifiera utdata

Du kan logga in på **Azure Databricks-arbetsytan**, gå till **Kluster** och sedan se status för **jobbet**: *väntar på att köras, körs eller har avslutats*.

![Visa jobbklustret och jobbet](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Du kan klicka på **jobbnamnet** och navigera för att se ytterligare information. Om körningen lyckas kan du validera parametrarna som skickats samt utdata för en Python Notebook.

![Visa körningsinformation och utdata](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Nästa steg

Pipelinen i det här exemplet utlöser en Databricks Notebook-aktivitet och skickar en parameter till den. Du har lärt dig att:

  - Skapa en datafabrik.

  - Skapa en pipeline som använder en Databricks Notebook-aktivitet.

  - Utlös en pipelinekörning.

  - Övervaka pipelinekörningen.