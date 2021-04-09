---
title: 'Transformera data med hjälp av spark i Azure Data Factory '
description: Den här självstudiekursen innehåller stegvisa instruktioner för hur du transformerar data genom att använda en Spark-aktivitet i Azure Data Factory.
ms.service: data-factory
ms.topic: tutorial
author: nabhishek
ms.author: abnarain
ms.date: 01/10/2018
ms.openlocfilehash: 2e2a50a96402f01fe914c79d5257fc5bb4dc57a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377796"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Transformera data i molnet genom att använda Spark-aktivitet i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Azure-portalen till att skapa Azure Data Factory-pipeline. Pipelinen transformerar data med en Spark-aktivitet och en länkad Azure HDInsight-tjänst på begäran. 

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa en pipeline som använder en Spark-aktivitet.
> * Utlös en pipelinekörning.
> * Övervaka pipelinekörningen.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure Storage-konto**. Du skapar ett Python-skript och en indatafil, och laddar upp dem till Azure Storage. Utdata från Spark-programmet lagras på det här lagringskontot. Spark-klustret på begäran använder samma lagringskonto som den primära lagringen.  

> [!NOTE]
> HdInsight stöder endast lagringskonton för generell användning med standardnivån. Kontrollera att kontot inte är ett lagringskonto enbart för premium- eller bloblagring.

* **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Ladda upp Python-skriptet till ditt Blob Storage-konto
1. Skapa en Python-fil med namnet **WordCount_Spark.py** med följande innehåll: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. Ersätt *&lt; storageAccountName &gt;* med namnet på ditt Azure Storage-konto. Spara sedan filen. 
1. Skapa en container med namnet **adftutorial** i Azure Blob Storage om den inte redan finns. 
1. Skapa en mapp med namnet **spark**.
1. Skapa en undermapp med namnet **script** under mappen **spark**. 
1. Överför filen **WordCount_Spark.py** till undermappen **script**. 


### <a name="upload-the-input-file"></a>Överföra indatafilen
1. Skapa en fil med namnet **minecraftstory.txt** med lite text. Spark-programmet räknar antalet ord i texten. 
1. Skapa en undermapp med namnet **inputfiles** i mappen **spark**. 
1. Ladda upp filen **minecraftstory.txt** till undermappen **inputfiles**. 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Välj **Nytt** på den vänstra menyn, välj **Data och analys** och välj sedan **Data Factory**. 
   
   ![Valet Data Factory i fönstret Nytt](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. I fönstret **Ny datafabrik** anger du **ADFTutorialDataFactory** under **Namn**. 
      
   ![Fönstret Ny datafabrik](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara *globalt unikt*. Om du ser följande fel ska du ändra namnet på datafabriken. (Använd till exempel **&lt; dittnamn &gt; ADFTutorialDataFactory**). Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](naming-rules.md).
  
   ![Fel när ett namn inte är tillgängligt](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**. 
1. Gör något av följande för **Resursgrupp**:
     
   - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan. 
   - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
   Några av stegen i den här snabb starten förutsätter att du använder namnet **ADFTutorialResourceGroup** för resurs gruppen. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
1. För **Version** väljer du **V2**.
1. För **Plats** väljer du en plats för datafabriken. 

   Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). De datalager (som Azure Storage och Azure SQL Database) och beräkningar (som HDInsight) som används i Data Factory kan finnas i andra regioner.

1. Välj **Skapa**.

1. När den har skapats visas sidan **data fabrik** . Välj ikonen **Författare och övervakare** för att starta programmet Data Factory UI på en separat flik.

    ![Startsidan för datafabriken med panelen Författare och övervakare](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du skapar två länkade tjänster i det här avsnittet: 
    
- En **Azure Storage länkad tjänst** som länkar ett Azure Storage-konto till data fabriken. Den här lagringen används av HDInsight-kluster på begäran. Den innehåller också Spark-skriptet som ska köras. 
- En **länkad HDInsight-tjänst på begäran**. Azure Data Factory skapar automatiskt ett HDInsight-kluster och kör Spark-programmet. HDInsight-klustret tas bort när det har varit inaktivt under en förinställd tid. 

### <a name="create-an-azure-storage-linked-service"></a>Skapa en länkad Azure Storage-tjänst

1. På sidan **Nu sätter vi igång** växlar du till fliken **Redigera** i den vänstra panelen. 

   ![Sidan ”Nu sätter vi igång”](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. Välj **Anslutningar** längst ned i fönstret och sedan **+Ny**. 

   ![Knappar för att skapa en ny anslutning](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Data Store** > **Azure Blob Storage** och klickar på **Fortsätt**. 

   ![Välj panelen ”Azure Blob Storage”](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. För **Lagringskontonamn** väljer du namnet i listan och väljer sedan **Spara**. 

   ![Fält för att ange namnet på lagringskontot](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Skapa en på begäran länkad HDInsight-tjänst

1. Välj knappen **+Ny** igen för att skapa ytterligare en länkad tjänst. 
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Compute** > **Azure HDInsight** och sedan **Fortsätt**. 

   ![Välja fönstret ”Azure HDInsight”](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

   a. Som **Namn** anger du **AzureHDInsightLinkedService**.
   
   b. För **Typ** kontrollerar du att **HDInsight på begäran** är valt.
   
   c. För **Azure Storage länkad tjänst** väljer du **AzureBlobStorage1**. Du skapade den här länkade tjänsten tidigare. Ange rätt namn här om du tidigare använde ett annat namn. 
   
   d. För **Klustertyp** väljer du **spark**.
   
   e. För **ID för tjänstens huvudnamn** anger du det ID för tjänsten huvudman som har behörighet att skapa ett HDInsight-kluster. 
   
      Tjänstens huvudnamn måste vara medlem i rollen Deltagare för prenumerationen eller resursgruppen som klustret har skapats i. Mer information finns i [Create an Azure Active Directory application and service principal](../active-directory/develop/howto-create-service-principal-portal.md) (Skapa ett Azure Active Directory-program och ett huvudnamn för tjänsten). **Tjänstens huvud namn-ID** motsvarar *program-ID: t* och en **huvud nyckel för tjänsten** motsvarar värdet för en *klient hemlighet*.
   
   f. Ange nyckeln i **Nyckel för tjänstens huvudnamn**. 
   
   ex. Välj samma resursgrupp som du använde när du skapade datafabriken i **Resursgrupp**. Spark-klustret skapas i den här resursgruppen. 
   
   h. Öppna **OS-typ**.
   
   i. Ange ett namn som **klusteranvändarnamn**. 
   
   j. Ange användarens **klusterlösenord**. 
   
   k. Välj **Slutför**. 

   ![Inställningar för länkad HDInsight-tjänst](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight begränsar hur många kärnor du kan använda i varje Azure-region som stöds. För den länkade HDInsight-tjänsten på begäran skapas HDInsight-klustret på samma Azure Storage-plats som används för primär lagring. Se till att du har tillräckligt med kärnkvoter så att klustret kan skapats på rätt sätt. Mer information finns i [Set up clusters in HDInsight with Hadoop, Spark, Kafka, and more](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) (Konfigurera kluster i HDInsight med Hadoop, Spark, Kafka med mera). 

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **+** knappen (plus) och välj sedan **pipeline** på menyn.

   ![Knappar för att skapa en ny pipeline](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. Gå till verktygsfältet **Aktiviteter** och expandera **HDInsight**. Dra aktiviteten **Spark** från verktygsfältet **Aktiviteter** till pipelinedesignytan. 

   ![Dra Spark-aktiviteten](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. Utför följande steg i egenskaperna för **Spark**-aktivitetsfönstret längst ned: 

   a. Växla till fliken **HDI-kluster** .
   
   b. Välj **AzureHDInsightLinkedService** (som du skapade i föregående procedur). 
        
   ![Ange den länkade HDInsight-tjänsten](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. Växla till fliken **Skript/Jar** och utför följande steg: 

   a. För **länkad jobb tjänst** väljer du **AzureBlobStorage1**.
   
   b. Välj **Bläddra i lagring**.

   ![Ange Spark-skriptet på fliken ”Skript/Jar”](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Bläddra till mappen **adftutorial/spark/script**, välj **WordCount_Spark.py** och välj sedan **Slutför**.      

1. Verifiera pipelinen genom att välja knappen **Verifiera** i verktygsfältet. Klicka på **>>** knappen (högerpil) för att stänga verifierings fönstret. 
    
   ![knappen Verifiera](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. Välj **Publicera alla**. Data Factory-gränssnittet publicerar entiteter (länkade tjänster och pipelines) till Azure Data Factory-tjänsten. 
    
   ![knappen Publicera alla](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning
Välj **Lägg till utlösare** i verktygsfältet och välj sedan **Utlös nu**. 

![knapparna Utlös och Utlös nu](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Växla till fliken **övervaka** . Bekräfta att du ser en pipeline-körning. Det tar ungefär 20 minuter att skapa ett Spark-kluster. 
   
1. Klicka på **Uppdatera** då och då så att du ser pipelinekörningens status. 

   ![Flik för att övervaka pipelinekörningar med knapp för att uppdatera](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna väljer på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Åtgärd**.

   ![Status för pipelinekörning](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   Du kan gå tillbaka till pipeline-körningarna genom att välja länken **alla pipeline-körningar** överst.

   ![vyn Aktivitetskörningar](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Verifiera utdata
Verifiera att utdatafilen har skapats i mappen spark/otuputfiles/wordcount för containern adftutorial. 

![Plats för utdatafilen](./media/tutorial-transform-data-spark-portal/verity-output.png)

Filen bör ha alla ord från indatafilen samma antal gånger som ordet förekommer i filen. Exempel: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet transformerar data med en Spark-aktivitet och en länkad HDInsight-tjänst på begäran. Du har lärt dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa en pipeline som använder en Spark-aktivitet.
> * Utlös en pipelinekörning.
> * Övervaka pipelinekörningen.

Om du vill hur du transformerar data genom att köra ett Hive-skript i ett Azure HDInsight-kluster i ett virtuellt nätverk går du vidare till nästa självstudie: 

> [!div class="nextstepaction"]
> [Självstudie: Transformera data med Hive i Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md).





