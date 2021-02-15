---
title: Transformera data med Hive i Azure Virtual Network att använda Azure Portal
description: Den här självstudiekursen innehåller stegvisa instruktioner för hur du transformerar data genom att använda en Hive-aktivitet i Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/04/2018
ms.openlocfilehash: 4c8ae67720cf6ac9d577286898b95cdd10f38152
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377966"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>Transformera data i Azure Virtual Network att använda Hive-aktivitet i Azure Data Factory med hjälp av Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Azure Portal för att skapa en Data Factory-pipeline som transformerar data med en Hive-aktivitet på ett HDInsight-kluster som finns i Azure Virtual Network (VNet). I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa en lokal Integration Runtime
> * Skapa länkade Azure Storage- och Azure HDInsight-tjänster
> * Skapa en pipeline med Hive-aktivitet.
> * Utlös en pipelinekörning.
> * Övervaka pipelinekörningen 
> * Verifiera utdata

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Storage konto**. Du skapar ett hive-skript och överför det till Azure Storage. Hive-skriptets utdata lagras på det här lagringskontot. I det här exemplet använder HDInsight-klustret det här Azure Storage-kontot som primär lagring. 
- **Azure-Virtual Network.** Om du inte har något virtuellt Azure-nätverk skapar du det genom att följa [de här instruktionerna](../virtual-network/quick-create-portal.md). I det här exemplet är HDInsight i ett virtuellt Azure-nätverk. Här är en exempelkonfiguration av Azure Virtual Network. 

    ![Skapa det virtuella nätverket](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **HDInsight-kluster.** Skapa ett HDInsight-kluster och anslut det till det virtuella nätverket som du skapade i föregående steg genom att följa stegen i den här artikeln: [Extend Azure HDInsight using an Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md) (Utöka HDInsight med ett Azure Virtual Network). Här är en exempelkonfiguration av HDInsight i ett virtuellt nätverk. 

    ![HDInsight i ett virtuellt nätverk](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps).
- **En virtuell dator**. Skapa en virtuell Azure-dator och koppla den till samma virtuella nätverk som innehåller ditt HDInsight-kluster. Läs mer i informationen om att [skapa virtuella datorer](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Överföra Hive-skriptet till ditt Blob Storage-konto

1. Skapa en Hive SQL-fil med namnet **hivescript.hql** med följande innehåll:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Skapa en container med namnet **adftutorial** i Azure Blob Storage om den inte finns.
3. Skapa en mapp med namnet **hivescripts**.
4. Ladda upp filen **hivescript.hql** till undermappen **hivescripts**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Logga in på [Azure Portal](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. På sidan **Ny datafabrik** anger du **ADFTutorialHiveFactory** som **namn**. 
      
     ![Sidan Ny datafabrik](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnMyAzureSsisDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
    *Data fabriks namnet "MyAzureSsisDataFactory" är inte tillgängligt*
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan. 
   - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
     Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
4. Välj **V2** för **versionen**.
5. Välj **plats** för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrument panelen visas följande panel med status: **distribuerar Data Factory**. 

     ![panelen distribuerar datafabrik](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
    ![Datafabrikens startsida](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.
11. På sidan **kom igång** växlar du till fliken **Redigera** på den vänstra panelen som på följande bild: 

    ![Fliken Redigera](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime
Eftersom Hadoop-klustret är inne i ett virtuellt nätverk måste du installera en lokal integreringskörning i samma virtuella nätverk. I det här avsnittet skapar du en ny virtuell dator, ansluter den till samma virtuella nätverk och installerar en lokal IR på den. Med en lokal IR kan Data Factory-tjänsten skicka bearbetningsbegäranden till en databearbetningstjänst som HDInsight inne i ett virtuellt nätverk. Den gör också så att du kan flytta data till och från datalager inne i ett virtuellt nätverk till Azure. Du använder också en lokal IR när datalagret eller databearbetningstjänsten finns i en lokal miljö. 

1. I användargränssnittet för Azure Data Factory klickar du på **Anslutningar** längst ned i fönstret, växlar till fliken med **integreringskörningar** och klickar på **+ Ny** i verktygsfältet. 

   ![Meny för ny integreringskörning](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. I installationsfönstret för **Integration Runtime** väljer du alternativet **Perform data movement and dispatch activities to external computes** (Utför dataflytt och skicka aktiviteter till externa databearbetningstjänster) och klickar på **Nästa**. 

   ![Välj alternativet för att flytta data och skicka aktiviteter](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Välj **Privat nätverk** och klicka på **Nästa**.
    
   ![Välj privat nätverk](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Ange **MySelfHostedIR** som **namn** och klicka på **Nästa**. 

   ![Ange namn på integreringskörning](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Kopiera **autentiseringsnyckeln** för integreringskörningen genom att klicka på kopieringsknappen och spara den. Lämna fönstret öppet. Du kan använda den här nyckeln till att registrera den IR som installerats på en virtuell dator. 

   ![Kopiera autentiseringsnyckeln](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Installera IR på en virtuell dator

1. Ladda ned [integration runtime med egen värd](https://www.microsoft.com/download/details.aspx?id=39717) till den virtuella Azure-datorn. Använd den **autentiseringsnyckel** som hämtades i föregående steg för att manuellt registrera integration runtime med egen värd. 

    ![Registrera Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. När den lokala integreringskörningen har registrerats ser du följande meddelande. 
   
    ![Registered successfully (Registrerat)](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Klicka på **Starta Konfigurationshanteraren**. Du ser följande sida när noden är ansluten till molntjänsten: 
   
    ![Node is connected (Noden är ansluten)](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Lokal IR i användargränssnittet för Azure Data Factory

1. I **användargränssnittet för Azure Data Factory** bör du se namnet på den lokala virtuella datorn och dess status.

   ![Befintliga lokala noder](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Klicka på **Slutför** för att stänga installationsfönstret för **Integration Runtime**. Den lokala integreringskörningen visas i listan med integreringskörningar.

   ![Lokal IR i listan](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Skapa länkade tjänster

Du skapar och distribuerar två länkade tjänster i det här avsnittet:
- Den **länkade Azure Storage-tjänsten** som länkar ditt Azure Storage-konto till datafabriken. Den här lagringen är den som primärt används av ditt HDInsight-kluster. I det här fallet använder du det här Azure Storage-kontot för att lagra Hive-skriptet och dess utdata.
- En **länkad HDInsight-tjänst**. Azure Data Factory skickar Hive-skriptet till det här HDInsight-klustret för körning.

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst

1. Växla till fliken **Länkade tjänster** och klicka på **Ny**.

   ![Knapp för ny länkad tjänst](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 

   ![Välj Azure Blob Storage](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **AzureStorageLinkedService** som **namn**.
    2. Välj **MySelfHostedIR** för **Connect via integration runtime** (Anslut via Integration Runtime).
    3. Välj ditt Azure Storage-konto för **lagrings kontots namn**. 
    4. Testa anslutningen till lagringskontot genom att klicka på **Testa anslutning**.
    5. Klicka på **Spara**.
   
        ![Ange Azure Blob Storage-konto](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Skapa länkad HDInsight-tjänst

1. Klicka på **Ny** igen för att skapa ytterligare en länkad tjänst. 
    
   ![Knapp för ny länkad tjänst](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Växla till fliken **Compute** (Beräkna), välj **Azure HDInsight** och klicka på **Fortsätt**.

    ![Välj Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **AzureHDInsightLinkedService** som **namn**.
    2. Välj **Bring your own HDInsight** (Använd egen HDInsight). 
    3. Välj ditt HDInsight-kluster för **Hdi cluster** (HDI-kluster). 
    4. Ange **användarnamnet** för HDInsight-klustret.
    5. Ange **lösenordet** för användaren. 
    
        ![Azure HDInsight-inställningar](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

Den här artikeln förutsätter att du har åtkomst till klustret via internet. Till exempel att du kan ansluta till klustret i `https://clustername.azurehdinsight.net`. Den här adressen använder den offentliga gatewayen, som inte är tillgänglig om du har använt nätverkssäkerhetsgrupper (NSG:er) eller användardefinierade vägar (UDR:er) för att begränsa åtkomst från internet. För att Data Factory ska kunna skicka jobb till HDInsight-klustret i Azure Virtual Network måste du konfigurera ditt Azure Virtual Network så att URL-adressen kan matchas med gatewayens privata IP-adress som används av HDInsight.

1. Från Azure-portalen öppnar du det virtuella nätverket som HDInsight finns i. Öppna nätverksgränssnittet med namnet som börjar med `nic-gateway-0`. Skriv ned dess privata IP-adress. Till exempel 10.6.0.15. 
2. Om din Azure Virtual Network har en DNS-server uppdaterar du DNS-posten så HDInsight-klustrets URL `https://<clustername>.azurehdinsight.net` kan matchas mot `10.6.0.15`. Om du inte har någon DNS-server i Azure Virtual Network kan du tillfälligt lösa detta genom att redigera värdfilen (C:\Windows\System32\drivers\etc) för alla virtuella datorer som är registrerade som lokala integreringskörningsnoder genom att lägga till en post som ser ut ungefär så här: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Skapa en pipeline 
I det här steget kan du skapa en ny pipeline med en Hive-aktivitet. Aktiviteten kör Hive-skript för att returnera data från en exempeltabell och spara dem till en sökväg som du har definierat.

Observera följande punkter:

- **scriptPath** pekar på sökvägen till Hive-skriptet i Azure Storage-kontot du använde för MyStorageLinkedService. Sökvägen är skiftlägeskänslig.
- **Utdata** är ett argument som används i Hive-skriptet. Använd formatet `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` för att peka på en befintlig mapp i Azure Storage. Sökvägen är skiftlägeskänslig. 

1. I användargränssnittet för Data Factory klickar du på **+ (plustecknet)** i det vänstra fönstret och klickar på **Pipeline**. 

    ![Meny för ny pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. I verktygslådan **Aktiviteter** visar du **HDInsight** och drar och släpper **Hive**-aktiviteten till pipelinedesignytan. 

    ![dra och släppa Hive-aktivitet](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. I egenskapsfönstret växlar du till fliken **HDI Cluster** (HDI-kluster) och väljer **AzureHDInsightLinkedService** för **HDInsight Linked Service**.

    ![Välja länkad HDInsight-tjänst](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Växla till fliken **Skript** och gör följande: 

    1. Välj **AzureStorageLinkedService** för **Skriptlänkad tjänst**. 
    2. För **Filsökväg** klickar du på **Bläddra i lagring**. 
 
        ![Bläddra i lagring](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. I fönstret **Choose a file or folder** (Välj en fil eller mapp) navigerar du till mappen **hivescripts** i containern **adftutorial** och väljer **hivescript.hql**. Klicka sedan på **Slutför**.  
        
        ![Välja en fil eller mapp](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Bekräfta att det står **adftutorial/hivescripts/hivescript.hql** som **filsökväg**.

        ![Skriptinställningar](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. Visa avsnittet **Avancerat** på fliken **Skript**. 
    6. Klicka på **Auto-fill from script** (Fyll i automatiskt från skript) för **Parametrar**. 
    7. Ange värdet för parametern **Utdata** i följande format: `wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Exempel: `wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Skriptargument](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Om du vill publicera artefakter till Data Factory klickar du på **Publicera**.

    ![Skärm bild som visar alternativet att publicera till en Data Factory.](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning

1. Verifiera först pipelinen genom att klicka på knappen **Verifiera** i verktygsfältet. Stäng fönstret **Pipeline Validation Output** (Resultat av pipelineverifiering) genom att klicka på **högerpil (>>)**. 

    ![Verifiera pipeline](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Om du vill utlösa en pipelinekörning klickar du på Utlösare i verktygsfältet och klickar på Trigger Now (Utlös nu). 

    ![Utlös nu](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Växla till fliken **Övervaka** till vänster. En pipelinekörning visas i listan **Pipeline Runs** (Pipelinekörningar). 

    ![Övervaka pipelinekörningar](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Om du vill uppdatera listan klickar du på **Uppdatera**.
4. Om du vill visa aktivitetskörningar som är associerade med pipelinekörningarna klickar du på **View activity runs** (Visa aktivitetskörningar) i kolumnen **Action** (Åtgärd). Övriga åtgärdslänkar är till för att stoppa pipelinen respektive köra den på nytt. 

    ![Visa aktivitetskörningar](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Du ser bara en aktivitetskörning eftersom det bara finns en aktivitet i pipelinen av typen **HDInsightHive**. Om du vill växla tillbaka till föregående vy klickar du på **Pipeliner** längst upp.

    ![Aktivitetskörningar](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Bekräfta att du ser en utdatafil i **outputfolder** för containern **adftutorial**. 

    ![Utdatafil](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen fick du: 

> [!div class="checklist"]
> * Skapa en datafabrik. 
> * Skapa en lokal Integration Runtime
> * Skapa länkade Azure Storage- och Azure HDInsight-tjänster
> * Skapa en pipeline med Hive-aktivitet.
> * Utlös en pipelinekörning.
> * Övervaka pipelinekörningen 
> * Verifiera utdata

Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Förgrening och länkning av Data Factory-kontrollflöde](tutorial-control-flow-portal.md)