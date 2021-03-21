---
title: Bygg din första data fabrik (Visual Studio)
description: I den här självstudien skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av Visual Studio.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: vs-azure
ms.date: 01/22/2018
ms.openlocfilehash: 2ae8e24b10a6661f5a18f3d04e243232436b2911
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100377456"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Självstudiekurs: Skapa en datafabrik med hjälp av Visual Studio
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [Quickstart: Create a data factory using Azure Data Factory](../quickstart-create-data-factory-dot-net.md) (Snabbstart: Skapa en datafabrik med Azure Data Factory) om du använder den aktuella versionen av Data Factory-tjänsten.

Den här självstudien visar hur du skapar en Azure Data Factory med hjälp av Visual Studio. Du skapar ett Visual Studio-projekt med Data Factory-projektmallen, definierar Data Factory-enheter (länkade tjänster, datamängder och pipeline) i JSON-format och publicerar/distribuerar sedan dessa enheter till molnet. 

Pipeline i den här självstudiekursen har en aktivitet: **HDInsight Hive-aktivitet**. Aktiviteten kör ett Hive-skript i ett Azure HDInsight-kluster som omvandlar indata för till utdata. Denna pipeline är schemalagd att köras en gång i månaden mellan angivna start- och sluttider. 

> [!NOTE]
> Den här självstudiekursen visar inte hur du kopiera data med hjälp av Azure Data Factory. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
> 
> En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Schemaläggning och utförande i Data Factory).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>Genomgång: Skapa och publicera datafabriksentiteter
Här är de steg du utför i självstudiekursen:

1. Skapa två länkade tjänster: **AzureStorageLinkedService1** och **HDInsightOnDemandLinkedService1**. 
   
    I den här självstudiekursen är både indata och utdata för Hive-aktiviteten i samma Azure Blob Storage. Du kan använda HDInsight-kluster på begäran för att bearbeta befintliga indata för att generera utdata. HDInsight-kluster på begäran skapas automatiskt åt dig av Azure Data Factory vid körning när indata är redo att bearbetas. Du måste länka dina datalager eller beräkningar till din datafabrik så att Data Factory-tjänsten kan ansluta till dem vid körning. Du ska alltså länka ditt Azure Storage-konto till datafabriken med hjälp av AzureStorageLinkedService1 och länka ett HDInsight-kluster på begäran med hjälp av HDInsightOnDemandLinkedService1. När du publicerar kan du ange namnet på datafabriken som ska skapas eller en befintlig datafabrik.  
2. Skapa två datauppsättningar: **InputDataset** och **OutputDataset**, som visar de in- och utdata som lagras i Azure Blob Storage. 
   
    Dessa datauppsättningsdefinitioner avser den länkade Azure Storage-tjänsten som du skapade i föregående steg. För InputDataset anger du blobcontainern (adfgetstarted) och den mapp (inptutdata) som innehåller en blob med indata. För OutputDataset anger du blobcontainern (adfgetstarted) och den mapp (partitioneddata) som innehåller en blob med utdata. Du kan också ange andra egenskaper som struktur, tillgänglighet och princip.
3. Skapa en pipeline med namnet **MyFirstPipeline**. 
  
    Pipelinen i den här självstudiekursen har en aktivitet: **HDInsight Hive-aktivitet**. Den här aktiviteten omvandlar indata till utdata genom att köra Hive-skript på ett HDInsight-kluster på begäran. Mer information om Hive-aktiviteter finns i [Hive-aktivitet](data-factory-hive-activity.md) 
4. Skapa en datafabrik med namnet **DataFactoryUsingVS**. Distribuera en datafabrik och alla Data Factory-enheter (länkade tjänster, tabeller och pipelinen).
5. När du har publicerat kan du använda bladen på Azure Portal och övervaknings- och hanteringsappen för att övervaka pipelinen. 
  
### <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Läs igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutför de **nödvändiga** stegen. Du kan också välja alternativet **Översikt och förutsättningar** i listrutan längst upp för att gå till artikeln. När du har slutfört förutsättningarna kan du gå tillbaka till den här artikeln genom att välja alternativet **Visual Studio** i listrutan.
2. Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.  
3. Du måste ha följande installerat på datorn:
   * Visual Studio 2013 eller Visual Studio 2015
   * Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
   * Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Du kan också uppdatera plugin-programmet genom att göra följande: i menyn klickar du på **verktyg**  ->  **tillägg och uppdateringar**  ->  **online**  ->  **Visual Studio-galleriet**  ->  **Microsoft Azure Data Factory verktyg för Visual Studio**  ->  **Update**.

Nu ska vi använda Visual Studio för att skapa en Azure Data Factory.

### <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt
1. Starta **Visual Studio 2013** eller **Visual Studio 2015**. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**. Dialogrutan **Nytt projekt** visas.  
2. I dialogrutan **Nytt projekt** väljer du mallen **DataFactory** och klickar på **Tomt Data Factory-projekt**.   

    ![Dialogrutan Nytt projekt](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Ange ett **namn** för projektet, en **plats** och ett namn för **lösningen**. Klicka på **OK**.

    ![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget kan du skapa två länkade tjänster: **Azure Storage** och **HDInsight på begäran**. 

Den länkade Azure Storage-tjänsten länkar ditt Azure Storage-konto till datafabriken genom att tillhandahålla anslutningsinformation. Data Factory-tjänsten använder anslutningssträngen från inställningen för den länkade tjänsten för att ansluta till Azure Storage vid körning. Det här lagringsutrymmet innehåller indata och utdata för pipelinen och Hive-skriptfilen som används av Hive-aktiviteten. 

Med den länkade tjänsten HDInsight på begäran skapas HDInsight-klustret automatiskt vid körning när indata är redo att bearbetas. Klustret tas bort när bearbetningen är klar. Det är inaktivt under en angiven tidsrymd. 

> [!NOTE]
> Du kan skapa en datafabrik genom att ange dess namn och inställningar när du publicerar din Data Factory-lösning.

#### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
1. Högerklicka på **Länkade tjänster** i Solution Explorer, peka på **Lägg till** och klicka på **Nytt objekt**.      
2. I dialogrutan **Lägg till nytt objekt** väljer du **Länkad Azure Storage-tjänst** i listan och klickar på **Lägg till**.
    ![Skärm bild som visar Azure Storage länkade tjänsten i listan.](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. Byt ut `<accountname>` och `<accountkey>` mot namnet på ditt Azure-lagringskonto och dess nyckel. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
    ![Länkad Azure Storage-tjänst](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. Spara filen **AzureStorageLinkedService1.json**.

#### <a name="create-azure-hdinsight-linked-service"></a>Skapa en Azure HDInsight-länkad tjänst
1. I **Solution Explorer** högerklickar du på **Länkade tjänster**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Länkad HDInsight-tjänst på begäran** och klicka på **Lägg till**.
3. Ersätt **JSON** med följande JSON:

     ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
        "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

    Egenskap | Beskrivning
    -------- | ----------- 
    ClusterSize | Anger HDInsight Hadoop-klustrets storlek.
    TimeToLive | Anger inaktivitetstiden för HDInsight-klustret innan det tas bort.
    linkedServiceName | Anger lagringskontot som används för att spara loggarna som genereras av HDInsight Hadoop-klustret. 

    > [!IMPORTANT]
    > HDInsight-klustret skapar en **standardcontainer** i den bloblagring som du angav i JSON (linkedServiceName). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med den länkade tjänsten HDInsight på begäran skapas ett HDInsight-kluster varje gång en sektor bearbetas, såvida det inte finns ett befintligt live-kluster (timeToLive). Klustret tas bort automatiskt när bearbetningen är klar.
    > 
    > Allteftersom fler sektorer bearbetas kan du se många containrar i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här containrarna följer ett mönster: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Använd verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/) för att ta bort behållare i Azure Blob Storage.

    Mer information om JSON-egenskaper finns i artikeln [Länkade tjänster för Compute](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
4. Spara filen **HDInsightOnDemandLinkedService1.json**.

### <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar som ska representera in- och utdata för Hive-bearbetning. Dessa datauppsättningar avser den **AzureStorageLinkedService1** som du skapade tidigare i den här självstudien. Den länkade tjänsten pekar på ett Azure-lagringskonto och datauppsättningarna anger container, mapp och filnamn i det lagringsutrymme som innehåller indata och utdata.   

#### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. I **Solution Explorer** högerklickar du på **Tabeller**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Azure-blobb** i listan och ändra namnet på filen till **InputDataSet.json**. Klicka på **Lägg till**.
3. Ersätt **JSON** i redigeraren med följande JSON-kodavsnitt:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Det här JSON-kodfragmentet definierar en datauppsättning med namnet **AzureBlobInput** som representerar indata för Hive-aktiviteten i pipelinen. Du anger att indata finns i blobcontainern `adfgetstarted` och i mappen `inputdata`.

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

    Egenskap | Beskrivning |
    -------- | ----------- |
    typ |Typegenskapen har angetts till **AzureBlob** eftersom det finns data i Azure Blob Storage.
    linkedServiceName | Refererar till AzureStorageLinkedService1 som du skapade tidigare.
    fileName |Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I det här fallet bearbetas bara input.log.
    typ | Loggfilerna är i textformat, så vi använder TextFormat. |
    columnDelimiter | kolumner i loggfilerna avgränsas med kommatecken (`,`)
    frekvens/intervall | frekvensen är månad och intervallet är 1, vilket innebär att indatasektorerna är tillgängliga en gång i månaden.
    extern | Den här egenskapen anges som true om indata för aktiviteten inte skapades av pipelinen. Den här egenskapen anges endast för indatauppsättningar. Ange alltid true för indatauppsättningen för den första aktiviteten.
4. Spara filen **InputDataset.json**.

#### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
Nu skapar du den utdatauppsättning som representerar de utdata som lagras i Azure Blob Storage.

1. I **Solution Explorer** högerklickar du på **tabeller**, pekar på **Lägg till** och klickar på **nytt objekt**.
2. Välj **Azure-blobb** i listan och ändra namnet på filen till **OutputDataset.json**. Klicka på **Lägg till**.
3. Ersätt **JSON** i redigeraren med följande JSON:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }
    ```
    Det här JSON-kodfragmentet definierar en datauppsättning med namnet **AzureBlobOutput** som representerar utdata för Hive-aktiviteten i pipelinen. Du anger att utdata som genereras av Hive-aktiviteten finns i blobcontainern `adfgetstarted` och i mappen `partitioneddata`. 
    
    I avsnittet **tillgänglighet** anges att utdatauppsättningen skapas månadsvis. Utdatauppsättningen styr schemat för pipelinen. Pipelinen körs varje månad mellan dess start- och sluttider. 

    Se **Skapa datauppsättning för indata** för beskrivningar av dessa egenskaper. Du anger inte den externa egenskapen för en utdatauppsättning, eftersom datauppsättningen produceras av pipelinen.
4. Spara filen **OutputDataset.json**.

### <a name="create-pipeline"></a>Skapa pipeline
Du har skapat den länkade Azure Storage-tjänsten och in- och utdatauppsättningar. Nu ska du skapa en pipeline med en **HDInsightHive**-aktivitet. **Indata** för Hive-aktiviteten är inställd på **AzureBlobInput** och **utdata** är inställd på **AzureBlobOutput**. En sektor av indatauppsättningen är tillgänglig varje månad (frekvens: Månad, intervall: 1), och utdatasektorn produceras också varje månad. 

1. I **Solution Explorer** högerklickar du på **Pipelines**, pekar på **Lägg till** och klickar på **Nytt objekt**.
2. Välj **Pipeline för Hive-transformering** i listan och klicka på **Lägg till**.
3. Ersätt **JSON** med följande kodfragment:

    > [!IMPORTANT]
    > Ersätt `<storageaccountname>` med namnet på ditt lagringskonto.

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService1",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > Ersätt `<storageaccountname>` med namnet på ditt lagringskonto.

    JSON-kodfragmentet definierar en pipeline som består av en enskild aktivitet (Hive-aktivitet). Aktiviteten kör ett Hive-skript för att bearbeta indata på ett HDInsight-kluster på begäran för att producera utdata. I aktivitetsavsnittet i JSON för pipelinen finns bara en aktivitet i matrisen med typen **HDInsightHive**. 

    I typegenskaperna som är specifika för HDInsight Hive-aktiviteten anger du vilken länkad Azure Storage-tjänst som har Hive-skriptfilen, sökvägen till skriptfilen och parametrar till skriptfilen. 

    Hive-skriptfilen **partitionweblogs.hql** lagras på Azure-lagringskontot (anges med scriptLinkedService), och i mappen `script` i containern `adfgetstarted`.

    Avsnittet `defines` används för att ange körningsinställningar som skickas till Hive-skriptet som Hive-konfigurationsvärden (till exempel `${hiveconf:inputtable}`, `${hiveconf:partitionedtable})`.

    Egenskaperna **start** och **slut** för pipelinen anger den aktiva perioden för pipelinen. Du har konfigurerat datauppsättningen som produceras varje månad. Därför produceras bara en sektor av pipelinen (eftersom månaden är densamma i start- och slutdatumen).

    I JSON-aktiviteten anger du att Hive-skriptet körs i den beräkning som anges av **linkedServiceName** – **HDInsightOnDemandLinkedService**.
4. Spara filen **HiveActivity1.json**.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Lägg till partitionweblogs.hql och input.log som ett beroende
1. Högerklicka på **Beroenden** i fönstret **Solution Explorer**, peka på **Lägg till** och klicka på **Befintligt objekt**.  
2. Gå till **C:\ADFGettingStarted** och markera filerna **partitionweblogs.hql**, **input.log**. Klicka på **Lägg till**. Du har skapat dessa två filer som en del av förutsättningarna från [Självstudier – översikt](data-factory-build-your-first-pipeline.md).

När du publicerar lösningen i nästa steg laddas filen **partitionweblogs.hql** upp till **skript**-mappen i blobcontainern `adfgetstarted`.   

### <a name="publishdeploy-data-factory-entities"></a>Publicera/distribuera Data Factory-entiteter
I det här steget publicerar du datafabriksentiteter (länkade tjänster, datauppsättningar och pipeline) i projektet till Azure Data Factory-tjänsten. Vid publiceringen anger du namnet på datafabriken. 

1. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**.
2. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
3. Du bör se följande dialogruta:

   ![Dialogrutan Publicera](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. På sidan **Konfigurera datafabrik** går du igenom följande steg:

    ![Publicera – nya datafabriksinställningar](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. välj alternativet **Skapa ny Data Factory**.
   2. Ange ett unikt **namn** för datafabriken. Exempel: **DataFactoryUsingVS09152016**. Namnet måste vara globalt unikt.
   3. Välj rätt prenumeration i fältet **Prenumeration**. 
        > [!IMPORTANT]
        > Om du inte ser någon prenumeration kontrollerar du att du har loggat in med ett konto som är en administratör eller en medadministratör för prenumerationen.
   4. Välj **resursgrupp** för datafabriken som ska skapas.
   5. Välj **region** för datafabriken.
   6. Klicka på **Nästa** för att växla till sidan **Publicera objekt**. (Tryck på **TAB** för att flytta ut från namnfältet om knappen **Nästa** är inaktiverad.)

      > [!IMPORTANT]
      > Om du får felet **Datafabriksnamnet ”DataFactoryUsingVS” är inte tillgängligt** när du publicerar, ändrar du namnet (till exempel yournameDataFactoryUsingVS). Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.   
1. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.

    ![Sidan Publish items (Publicera objekt)](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.

    ![Sammanfattningssida](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.

Viktiga saker att observera:

- Om du får felet: **den här prenumerationen är inte registrerad för att använda namn området Microsoft. DataFactory**, gör något av följande och försök att publicera igen:
    - I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern.
        ```powershell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad.

        ```powershell
        Get-AzResourceProvider
        ```
    - Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.
- Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
- Om du vill skapa Data Factory-instanser måste du vara administratör eller medadministratör för Azure-prenumerationen

### <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget övervakar du pipelinen med hjälp av datafabrikens diagramvy. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Övervaka pipeline med diagramvyn
1. Logga in på [Azure Portal](https://portal.azure.com/) genom att göra följande:
   1. Klicka på **Fler tjänster** och på **Datafabriker**.
       
        ![Bläddra igenom datafabrikerna](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Välj namnet på din datafabrik (till exempel: **DataFactoryUsingVS09152016**) från listan med datafabriker.
   
       ![Välj din datafabrik](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. På startsidan för din datafabrik klickar du på **Diagram**.

    ![Ikonen Diagram](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. I diagramvyn visas en översikt över pipelines och datauppsättningar som används i den här självstudien.

    ![Diagramvy](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. Högerklicka på pipelinen i diagrammet om du vill visa alla aktiviteter i pipelinen. Klicka sedan på Öppna pipeline.

    ![Menyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. Kontrollera att du ser HDInsightHive-aktiviteten i pipelinen.

    ![Vyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Om du vill gå tillbaka till den föregående vyn klickar du på **Datafabrik** i adressfältmenyn längst upp.
6. Dubbelklicka på datauppsättningen **AzureBlobInput** i **diagramvyn**. Kontrollera att sektorn har statusen **Klar**. Det kan ta några minuter innan sektorn visas med statusen Klar. Om det inte händer trots att du har väntat ett tag, kontrollerar du att du har indatafilen (input.log) placerad i rätt container (`adfgetstarted`) och mapp (`inputdata`). Och se till att egenskapen **external** för indatauppsättningen är inställd på **true**. 

   ![Indatasektor med statusen Klar](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. Klicka på **X** för att stänga bladet **AzureBlobInput**.
8. Dubbelklicka på datauppsättningen **AzureBlobOutput** i **diagramvyn**. Den sektor som för närvarande bearbetas visas.

   ![Skärm bild som visar AzureBlobOutput-datauppsättningen.](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. När bearbetningen är klar visas sektorn med statusen **Klar**.

   > [!IMPORTANT]
   > Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter). Därför förväntar sig pipelinen att ta **cirka 30 minuter** att bearbeta sektorn.  
   
    ![Datamängd](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. När sektorn har statusen **Redo**, kontrollerar du mappen `partitioneddata` i containern `adfgetstarted` i ditt Blob Storage för utdata.  

    ![utdata](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Klicka på sektorn om du vill se information om den i ett **Datasektor**-blad.

    ![Information om datasektorn](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Klicka på en aktivitet som körs i **Lista med aktivitetskörningar** för att se information om en aktivitetskörning (Hive-aktivitet i vårt exempel) i ett fönster med **Aktivitetskörningsinformation**. 
  
    ![Aktivitetskörningsinformation](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    Du kan se Hive-frågan som kördes och statusinformation i loggfilerna. Dessa loggar är användbara vid felsökning av eventuella problem.  

Se [övervaka data uppsättningar och pipeline](data-factory-monitor-manage-pipelines.md) för instruktioner om hur du använder Azure Portal för att övervaka pipeline och data uppsättningar som du har skapat i den här självstudien.

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>Övervaka pipeline med övervaknings- och hanteringsappen
Du kan också använda övervaknings- och hanteringsprogrammet till att övervaka dina pipelines. Se [Övervaka och hantera Azure Data Factory-pipelines med övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md) för mer information om att använda programmet.

1. Klicka på ikonen Övervaka och hantera.

    ![Ikonen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. Du bör se programmet Övervaka och hantera. Ändra **Starttid** och **Sluttid** till att matcha starttid (04-01-2016 12:00:00) och sluttid (04-02-2016 12:00:00) i pipelinen. Klicka sedan på **Tillämpa**.

    ![Appen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Om du vill visa information om ett aktivitetsfönster väljer du det i listan **Aktivitetsfönster**.
    ![Aktivitetsfönsterinformation](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen laddar du därför upp indatafilen (input.log) till mappen `inputdata` i containern`adfgetstarted`.

### <a name="additional-notes"></a>Ytterligare information
- En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Till exempel, en kopieringsaktivitet som kopierar data från en källa till en måldatalagring och en HDInsight Hive-aktivitet som kör ett Hive-skript som omvandlar indata. I [stödda datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) står alla källor och mottagare som stöds av Kopiera aktivitet. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för att se listan över Compute Services som stöds av Data Factory.
- Länkade tjänster länkar data lager eller beräknings tjänster till en Azure Data Factory. I [stödda datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) står alla källor och mottagare som stöds av Kopiera aktivitet. Se [Länkade tjänster för Compute](data-factory-compute-linked-services.md) för att se listan över Compute Services som stöds av Data Factory och [transformeringsaktiviteter](data-factory-data-transformation-activities.md) som kan köras på dem.
- Se [Flytta data från/till Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) för mer information om JSON-egenskaper som används i definitionen för den länkade Azure Storage-tjänsten.
- Du kan använda ditt eget HDInsight-kluster i stället för att använda ett HDInsight-kluster på begäran. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för mer information.
-  Data Factory skapar ett **Linux-baserat** HDInsight-kluster åt dig med ovanstående JSON. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
- HDInsight-klustret skapar en **standardcontainer** i den bloblagring som du angav i JSON (linkedServiceName). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med den länkade tjänsten HDInsight på begäran skapas ett HDInsight-kluster varje gång en sektor bearbetas, såvida det inte finns ett befintligt live-kluster (timeToLive). Klustret tas bort automatiskt när bearbetningen är klar.
    
    Allteftersom fler sektorer bearbetas kan du se många containrar i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här containrarna följer ett mönster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Använd verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/) för att ta bort behållare i Azure Blob Storage.
- För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. 
- Den här självstudiekursen visar inte hur du kopiera data med hjälp av Azure Data Factory. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).


## <a name="use-server-explorer-to-view-data-factories"></a>Använda Server Explorer för att visa datafabriker
1. I **Visual Studio** klickar du på **Visa** i menyn och därefter på **Server Explorer**.
2. I fönstret Server Explorer expanderar du **Azure** och **Data Factory**. Om du ser **Logga in till Visual Studio** anger du det **konto** som är associerat med din Azure-prenumeration och klickar på **Fortsätt**. Ange **lösenordet** och klicka på **Logga in**. Visual Studio försöker hämta information om alla Azure-datafabriker i din prenumeration. Du ser statusen för den här åtgärden i fönstret **Uppgiftslista för Data Factory**.

    ![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Du kan också högerklicka på en datafabrik och välja **Exportera Data Factory till nytt projekt** om du vill skapa ett Visual Studio-projekt som baseras på en befintlig datafabrik.

    ![Exportera datafabrik](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Uppdatera Data Factory-verktyg för Visual Studio
Om du vill uppdatera Azure Data Factory-verktyg för Visual Studio går du igenom följande steg:

1. Klicka på **Verktyg** i menyn och välj **Tillägg och uppdateringar**.
2. Välj **Uppdateringar** i den vänstra rutan och välj sedan **Visual Studio-galleriet**.
3. Välj **Azure Data Factory-verktyg för Visual Studio** och klicka på **Uppdatera**. Om du inte ser den här posten har du redan den senaste versionen av verktygen.

## <a name="use-configuration-files"></a>Använda konfigurationsfiler
Du kan använda konfigurationsfiler i Visual Studio för att konfigurera egenskaper för länkade tjänster/tabeller/pipelines olika för varje miljö.

Fundera på följande JSON-definition för en länkad Azure Storage-tjänst. Att ange **connectionString** med olika värden för accountname och accountkey baseras på vilken miljö (Utveckling/Test/Produktion) som du distribuerar Data Factory-entiteter till. Du kan göra detta genom att använda separata konfigurationsfiler för varje miljö.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Lägga till en konfigurationsfil
Lägg till en konfigurationsfil för varje miljö genom att utföra följande steg:   

1. Högerklicka på Data Factory-projektet i Visual Studio-lösningen, peka på **Lägg till** och klicka på **Nytt objekt**.
2. Välj **Config** i listan med installerade mallar till vänster, välj **Konfigurationsfil**, ange ett **namn** för konfigurationsfilen och klicka på **Lägg till**.

    ![Lägga till en konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Lägg till konfigurationsparametrar och deras värden i följande format:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Det här exemplet anger egenskapen connectionString för en länkad Azure Storage-tjänst och en Azure SQL-länkad tjänst. Observera att syntaxen för att ange namnet är [JsonPath](https://goessner.net/articles/JsonPath/).   

    Om JSON har en egenskap med en värdematris enligt följande kod:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Konfigurera egenskaper enligt följande konfigurationsfil (använd nollbaserad indexering):

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Egenskapsnamn med blanksteg
Om ett egenskapsnamn innehåller blanksteg, använder du hakparenteser enligt följande exempel (databasservernamn):

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Distribuera lösningen med en konfiguration
När du publicerar Azure Data Factory-entiteter i VS, kan du ange den konfiguration som du vill använda för att publicera åtgärden.

Publicera entiteter i Azure Data Factory-projekt med hjälp av en konfigurationsfil:   

1. Högerklicka på Data Factory-projektet och visa dialogrutan **Publicera objekt** genom att klicka på **Publicera**.
2. Välj en befintlig datafabrik eller ange värden för att skapa en datafabrik på sidan **Konfigurera datafabrik**. Klicka på **Nästa**.   
3. På sidan **Publicera objekt**: Här visas en listruta med tillgängliga konfigurationer för fältet **Välj distributionskonfiguration**.

    ![Välj konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Välj den **konfigurationsfil** som du vill använda och klicka på **Nästa**.
5. Kontrollera att du ser namnet på JSON-filen på sidan **Sammanfattning** och klicka på **Nästa**.
6. Klicka på **Slutför** när distributionen är klar.

När du distribuerar används värden från konfigurationsfilen till att ange värden för egenskaper i JSON-filerna innan entiteterna distribueras till Azure Data Factory-tjänsten.   

## <a name="use-azure-key-vault"></a>Använda Azure Key Vault
Det är inte tillrådligt och ofta mot säkerhetsprincipen införa känsliga data, till exempel anslutningssträngar, i kod-databasen. Se exemplet [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFSecurePublish) på GitHub att lära dig om att lagra känslig information i Azure Key Vault och använda den när du publicerar Data Factory-entiteter. Secure Publish-tillägget för Visual Studio gör det möjligt att lagra hemligheter i Key Vault och endast specificera referenser till dessa i konfigurationer för länkade tjänster/distributioner. Dessa referenser matchas när du publicerar Data Factory-entiteter i Azure. Dessa filer kan sedan skickas till källdatabasen utan visa hemligheter.

## <a name="summary"></a>Sammanfattning
I den här självstudien har du skapat en Azure Data Factory för att bearbeta data genom att köra Hive-skript på ett HDInsight Hadoop-kluster. Du utförde följande steg med hjälp av Data Factory-redigeraren i Azure Portal:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade två **länkade tjänster**:
   1. En länkad **Azure Storage-** tjänst som länkar din Azure Blob-lagring med in-/utdatafiler till datafabriken.
   2. En länkad **Azure HDInsight**-tjänst på begäran som länkar ett Hadoop-kluster i HDInsight på begäran till datafabriken. Azure Data Factory skapar ett Hadoop-kluster i HDInsight i rätt tid för att bearbeta indata och skapa utdata.
3. Du skapade två **datauppsättningar** som beskriver in- och utdata för Hive-aktiviteten för HDInsight i pipelinen.
4. Du skapade en **pipeline** med en **HDInsight Hive**-aktivitet.  

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en pipeline med en transformeringsaktivitet (HDInsight-aktivitet) som kör ett Hive-skript på ett HDInsight-kluster på begäran. Information om hur du använder en kopierings aktivitet för att kopiera data från en Azure-blob till Azure SQL finns i [Självstudier: kopiera data från en Azure-blob till Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 


## <a name="see-also"></a>Se även

| Avsnitt | Beskrivning |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Datatransformeringsaktiviteter](data-factory-data-transformation-activities.md) |Den här artikeln innehåller en lista med de datatransformeringsaktiviteter (till exempel HDInsight Hive-transformeringen som du använde i självstudien) som stöds av Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |
