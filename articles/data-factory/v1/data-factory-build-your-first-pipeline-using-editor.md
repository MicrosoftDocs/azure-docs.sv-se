---
title: Bygg din första data fabrik (Azure Portal)
description: I den här självstudien skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av Data Factory Editor i Azure Portal.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: 9794aa0750a886803aac3fec7622f6b3770acf9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785588"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Självstudie: skapa din första datafabrik med Azure-portalen
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Läs [Quickstart: Create a data factory using Azure Data Factory](../quickstart-create-data-factory-dot-net.md) (Snabbstart: Skapa en datafabrik med Data Factory) om du använder den aktuella versionen av Data Factory-tjänsten.

> [!WARNING]
> JSON-redigeraren i Azure Portal för redigering & distribuerar ADF v1-pipeliner inaktive ras den 31 juli 2019. Efter 31 juli 2019 kan du fortsätta att använda [ADF v1 PowerShell-cmdlets](/powershell/module/az.datafactory/), [ADF v1 .NET SDK](/dotnet/api/microsoft.azure.management.datafactories.models), [ADF v1 REST-API: er](/rest/api/datafactory/) för att skapa & distribuera dina ADF v1-pipeliner.

I den här artikeln får du lära dig hur du använder [Azure Portal](https://portal.azure.com/) till att skapa din första datafabrik. Om du vill gå igenom självstudien med andra verktyg/SDK:er kan du välja något av alternativen i listrutan. 

Pipelinen i den här självstudien har en aktivitet: en Azure HDInsight Hive-aktivitet. Aktiviteten kör ett Hive-skript i ett HDInsight-kluster som omvandlar indata till utdata. Denna pipeline är schemalagd att köras en gång i månaden mellan angivna start- och sluttider. 

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. En självstudiekurs om hur du kopierar data med Data Factory finns i [Tutorial: Copy data from Azure Blob Storage to Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Azure Blob Storage till Azure SQL Database).
> 
> En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer information finns i [schemaläggning och körning i Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Förutsättningar
Läs [översikten av självstudien](data-factory-build-your-first-pipeline.md) och följ anvisningarna i avsnittet ”Nödvändiga komponenter”.

Den här artikeln ger inte någon konceptuell översikt över Data Factory-tjänsten. Mer information om tjänsten finns i [Introduktion till Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Skapa en datafabrik
En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Ett exempel är en kopieringsaktivitet som kopierar data från en källa till ett måldatalager. Ett annat exempel är en HDInsight Hive-aktivitet som kör Hive-skript för omvandling av indata till utdata. 

Gör så här för att skapa en datafabrik:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **ny**  >  **data och analys**  >  **Data Factory**.

   ![Bladet Skapa](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. På bladet **Ny datafabrik**, under **Namn**, anger du **GetStartedDF**.

   ![Bladet Ny datafabrik](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Namnet på datafabriken måste vara globalt unikt. Om du får felet ”Datafabriksnamnet GetStartedDF är inte tillgängligt” ändrar du namnet på datafabriken. Du kan till exempel använda dittnamnGetStartedDF och skapa datafabriken igen. Mer information om namngivningsregler finns i [Data Factory: namngivningsregler](data-factory-naming-rules.md).
   >
   > Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och kan då bli synligt offentligt.
   >
   >
1. Under **Prenumeration** väljer du den Azure-prenumeration där du vill att datafabriken ska skapas.

1. Välj en befintlig resursgrupp eller skapa en ny. För självstudien skapar du en resurs grupp med namnet **ADFGetStartedRG**.

1. Under **Plats** väljer du en plats för datafabriken. Endast regioner som stöds av tjänsten Data Factory visas i listrutan.

1. Markera kryssrutan **Fäst på instrumentpanelen**.

1. Välj **Skapa**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory instanser måste du vara medlem i rollen [Data Factory Contributor](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på prenumerations-eller resurs grupps nivån.
   >
   >
1. På instrument panelen visas följande panel med statusen **distribuera Data Factory**:    

   ![Status för Data Factory-distribuering](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. När datafabriken har skapats visas sidan **Datafabrik** med innehållet i datafabriken.     

    ![Bladet Datafabrik](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Innan du skapar en pipeline i datafabriken måste du först skapa några Data Factory-entiteter. Först skapar du länkade tjänster som länkar datalager/databeräkningar till ditt datalager. Sedan definierar du in- och utdatauppsättningar för att representera in- och utdata i länkade datalager. Slutligen skapar du pipelinen med en aktivitet som använder dessa datauppsättningar.

## <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget länkar du ditt Azure Storage-konto och ett HDInsight-kluster på begäran till din datafabrik. In- och utdata för pipelinen i det här exemplet lagras i lagringskontot. En länkad HDInsight-tjänst används för att köra Hive-skriptet som anges i pipeline-aktiviteten i det här exemplet. Identifiera vilka [](data-factory-data-movement-activities.md) / [beräknings tjänster](data-factory-compute-linked-services.md) för data lager som används i ditt scenario. Länka sedan dessa tjänster till datafabriken genom att skapa länkade tjänster.  

### <a name="create-a-storage-linked-service"></a>Skapa en länkad lagringstjänst
I det här steget länkar du ditt lagringskonto till datafabriken. I den här självstudien använder du samma lagringskonto för att lagra indata/utdata och HQL-skriptfilen.

1. På bladet **Data factory** för **GetStartedDF** väljer du **Författare och distribution**. Du ser Data Factory Editor.

   ![Ikonen Författare och distribution](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. Välj **Nytt datalager** och välj **Azure Storage**.

   ![Bladet Nytt datalager](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. Du ser JSON-skriptet för att skapa en länkad lagringstjänst i redigeraren.

   ![Länkad lagringstjänst](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Ersätt **account name** med namnet på ditt lagringskonto. Ersätt **account key** med åtkomstnyckeln för lagringskontot. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

    ![Knappen Distribuera](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Fönstret Draft-1 stängs när den länkade tjänsten har distribuerats. Du ser **AzureStorageLinkedService** i trädvyn till vänster.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Skapa en länkad HDInsight-tjänst
I det här steget ska du länka ett HDInsight-kluster på begäran till datafabriken. HDInsight-klustret skapas automatiskt vid körning. När bearbetningen är klar och klustret varit inaktivt under en angiven tidsrymd tas det bort.

1. I redigerings programmet för Data Factory väljer du **mer**  >  **ny beräkning**  >  **på begäran HDInsight-kluster**.

    ![Ny beräkning](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. JSON-kodfragmentet beskriver de egenskaper som används för att skapa HDInsight-klustret på begäran.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

   | Egenskap | Beskrivning |
   |:--- |:--- |
   | clusterSize |Anger HDInsight-klustrets storlek. |
   | timeToLive | Anger inaktivitetstiden för HDInsight-klustret innan det tas bort. |
   | linkedServiceName | Anger lagringskontot som används för att spara loggarna som genereras av HDInsight. |

    Observera följande punkter:

     a. Data Factory skapar ett Linux-baserat HDInsight-kluster åt dig med JSON-egenskaperna. Mer information finns i [Länkad HDInsight-tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Du kan använda ett eget HDInsight-kluster i stället för ett HDInsight-kluster på begäran. Mer information finns i [Länkad HDInsight-tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. HDInsight-klustret skapar en standardcontainer i den bloblagring du angav i JSON-egenskapen (**linkedServiceName**). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med den länkade HDInsight-tjänsten på begäran skapas ett HDInsight-kluster varje gång en sektor bearbetas, såvida det inte finns ett befintligt aktivt kluster (**timeToLive**). Klustret tas bort automatiskt när bearbetningen är klar.

     Allteftersom fler sektorer bearbetas kan du se många containrar i din bloblagring. Om du inte behöver dem för felsökning av jobben kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: "ADF **yourdatafactoryname** - **linkedservicename**-datumtidsstämpel". Använd verktyg som [Azure Storage Explorer](https://storageexplorer.com/) till att ta bort containrar i din bloblagring.

     Mer information finns i [Länkad HDInsight-tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Välj **Distribuera** i kommandofältet för att distribuera den länkade tjänsten.

    ![Alternativet Distribuera](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. Kontrollera att du ser både **AzureStorageLinkedService** och **HDInsightOnDemandLinkedService** i trädvyn till vänster.

    ![Skärm bild som visar att AzureStorageLinkedService och HDInsightOnDemandLinkedService är kopplade samman.](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar som ska representera in- och utdata för Hive-bearbetning. Dessa datauppsättningar avser den AzureStorageLinkedService du skapade tidigare i självstudien. Den länkade tjänsten pekar på ett lagringskonto. Datauppsättningarna anger container, mapp och filnamn i lagringen som innehåller indata och utdata.   

### <a name="create-the-input-dataset"></a>Skapa indatauppsättningen
1. I Data Factory redigeraren väljer du **mer**  >  **ny data uppsättning**  >  **Azure Blob Storage**.

    ![Ny datauppsättning](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. I JSON-kodfragmentet skapar du en datauppsättning med namnet **AzureBlobInput** som representerar indata för en aktivitet i pipelinen. Dessutom kan du ange att indata finns i blobcontainern **adfgetstarted** och i mappen **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

   | Egenskap | Kapslad under | Beskrivning |
   |:--- |:--- |:--- |
   | typ | properties |Egenskapen type sätts till **AzureBlob** eftersom data finns i bloblagringen. |
   | linkedServiceName | format |Refererar till den AzureStorageLinkedService du skapade tidigare. |
   | folderPath | typeProperties | Anger vilken blobcontainer och mapp som innehåller indatablobar. | 
   | fileName | typeProperties |Den här egenskapen är valfri. Om du utelämnar den här egenskapen väljs alla filer från folderPath. I den här självstudien bearbetas bara filen input.log. |
   | typ | format |Loggfilerna är i textformat, så använd **TextFormat**. |
   | columnDelimiter | format |Kolumnerna i loggfilerna avgränsas med kommatecken (`,`). |
   | frekvens/intervall | availability |frequency sätts till **Month** (månad) och interval till **1**, vilket innebär att indatasektorerna är tillgängliga en gång i månaden. |
   | extern | properties | Den här egenskapen sätts till **true** om indata inte genereras i denna pipeline. I den här självstudien genereras inte input.log-filen i denna pipeline, så vi sätter egenskapen till **true**. |

    Mer information om de här JSON-egenskaperna finns i [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md#dataset-properties).

1. Välj **Distribuera** i kommandofältet för att distribuera den nyskapade datauppsättningen. Du ser datauppsättningen i trädvyn till vänster.

### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
Nu skapar du den utdatauppsättning som representerar de utdata som lagras i bloblagringen.

1. I Data Factory redigeraren väljer du **mer**  >  **ny data uppsättning**  >  **Azure Blob Storage**.

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. I JSON-kodfragmentet skapar du en datauppsättning som kallas **AzureBlobOutput** och anger strukturen för de data som produceras av Hive-skriptet. Dessutom anger du att resultaten lagras i blobcontainern **adfgetstarted** och i mappen **partitioneddata**. I avsnittet **availability** anges att utdatauppsättningen skapas månadsvis.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
    Mer information om de här egenskaperna finns i avsnittet Skapa datauppsättningen för indata. Du anger inte den egenskapen external för en utdatauppsättning, eftersom datauppsättningen produceras av Data Factory-tjänsten.

1. Välj **Distribuera** i kommandofältet för att distribuera den nyskapade datauppsättningen.

1. Kontrollera att datauppsättningen har skapats.

    ![Trädvy med länkade tjänster](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget ska du skapa din första pipeline med en HDInsight Hive-aktivitet. Indatasektorn är tillgänglig varje månad (frequency är Month, interval är 1). Utdatasektorn skapas varje månad. Egenskapen scheduler för aktiviteten har också satts till monthly. Inställningarna för utdatauppsättningen och aktivitetsschemaläggaren måste matcha. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. De egenskaper som användes i följande JSON-kodfragment beskrivs i slutet av det här avsnittet.

1. I Data Factory redigeraren väljer du **mer**  >  **ny pipeline**.

    ![Alternativet Ny pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1.

   > [!IMPORTANT]
   > Ersätt **storageaccountname** med namnet på ditt lagringskonto i JSON-kodfragmentet.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
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
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    I JSON-kodfragmentet skapar du en pipeline med en enda aktivitet, som använder Hive till att bearbeta data i ett HDInsight-kluster.

    Hive-skriptfilen **partitionweblogs.hql** lagras i lagringskontot, som anges med en scriptLinkedService som heter **AzureStorageLinkedService1**. Du hittar den i mappen **script** i containern **adfgetstarted**.

    Avsnittet **defines** används till att ange körningsinställningar som skickas till Hive-skriptet som Hive-konfigurationsvärden. Exempel är ${hiveconf:inputtable} och ${hiveconf:partitionedtable}.

    Egenskaperna **start** och **end** för pipelinen anger den aktiva perioden för pipelinen.

    I JSON för aktiviteten anger du att Hive-skriptet körs i den beräkning som anges av **linkedServiceName**: **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Mer information om de JSON-egenskaper som används i exemplet finns i avsnittet om Pipeline-JSON i [Pipelines and activities in Data Factory](data-factory-create-pipelines.md) (Pipeliner och aktiviteter i Data Factory).
   >
   >
1. Kontrollera följande:

   a. Att filen **input.log** finns i mappen **inputdata** i containern **adfgetstarted** i bloblagringen.

   b. Att filen **partitionweblogs.hql** finns i mappen **script** i containern **adfgetstarted** i bloblagringen. Om du inte ser de här filerna följer du stegen i avsnittet Nödvändiga komponenter i [Självstudie – översikt](data-factory-build-your-first-pipeline.md).

   c. Att du ersatt **storageaccountname** med namnet på ditt lagringskonto i JSON-pipelinen.

1. Välj **Distribuera** i kommandofältet för att distribuera pipelinen. Eftersom tiderna för **start** och **end** har angetts i dåtiden och **isPaused** har satts till **false** kommer pipelinen (aktiviteten i pipelinen) att köras omedelbart efter att du har distribuerat den.

1. Kontrollera att du ser pipelinen i trädvyn.

    ![Trädvy med pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Övervaka en pipeline
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Övervaka en pipeline i diagramvyn
1. På **Data Factory**-bladet väljer du **Diagram**.

    ![Ikonen Diagram](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. I **diagramvyn** visas en översikt över de pipelines och data uppsättningar som används i den här självstudien.

    ![Diagramvy](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. Högerklicka på pipelinen i diagrammet om du vill visa alla aktiviteter i pipelinen. Välj sedan **Öppna pipeline**.

    ![Menyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. Kontrollera att du ser **Hive-aktiviteten** i pipelinen.

    ![Vyn Öppna pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Om du vill gå tillbaka till den föregående vyn väljer du **Data Factory** i menyn längst upp.

1. I **diagramvyn** dubbelklickar du på data uppsättningen **AzureBlobInput**. Kontrollera att sektorn har statusen **Klar**. Det kan ta några minuter innan sektorn visas med statusen **Klar**. Om det inte händer trots att du har väntat ett tag, kontrollerar du om du har indatafilen (**input.log**) placerad i rätt container (**adfgetstarted**) och mapp (**inputdata**).

   ![Indatasektor med statusen Klar](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. Stäng bladet **AzureBlobInput**.

1. I **diagramvyn** dubbelklickar du på data uppsättningen **AzureBlobOutput**. Du ser den sektor som bearbetas för närvarande.

   ![Datauppsättning bearbetas](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. När bearbetningen är klar ser du sektorn med tillståndet **Klar**.

   ![Datauppsättning i tillståndet Klar](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > Det tar normalt ungefär 20 minuter att skapa ett HDInsight-kluster på begäran. Förvänta dig att det tar cirka 30 minuter för pipelinen att bearbeta sektorn.
   >
   >

1. När sektorn har statusen **Klar** kontrollerar du att utdata finns i mappen **partitioneddata** i containern **adfgetstarted** i bloblagringen.  

   ![Utdata](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. Välj sektorn om du vill se information om den på ett **Datasektor**-blad.

    ![Information om datasektor](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. Om du vill se mer information om en aktivitetskörning väljer du den i listan **Aktivitetskörningar**. (I det här scenariot är det en Hive-aktivitet.) Informationen visas på bladet **aktivitets körnings information** .   

    ![Fönstret Aktivitetskörningsinformation](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   Du kan se Hive-frågan som kördes och statusinformation i loggfilerna. Dessa loggar är användbara vid felsökning av eventuella problem.
   Mer information finns i [Övervaka och hantera pipelines med Azure-portalblad](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> Indatafilen tas bort när sektorn har bearbetats. Om du vill köra om sektorn eller göra självstudien igen laddar du därför upp indatafilen (**indata. log**) i mappen **inputdata** i behållaren **adfgetstarted** .
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Övervaka en pipeline med appen Övervaka och hantera
Du kan också använda appen Övervaka och hantera till att övervaka dina pipeliner. Mer information om hur du använder den här appen finns i [Övervaka och hantera Data Factory-pipeliner med appen Övervaka och hantera](data-factory-monitor-manage-app.md).

1. Välj panelen **Övervaka och hantera** på datafabrikens startsida.

    ![Ikonen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. Ändra **Starttid** och **Sluttid** i appen Övervaka och hantera så att de matchar starttid och sluttid för pipelinen. Välj **Använd**.

    ![Appen Övervaka och hantera](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. Välj ett aktivitetsfönster i listan **Aktivitetsfönster** om du vill se information om det.

    ![Lista med aktivitetsfönster](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Sammanfattning
I den här självstudien skapade du en datafabrik som bearbetar data genom att köra ett Hive-skript i ett Hadoop-kluster i HDInsight. Du utförde följande steg med hjälp av Data Factory Editor i Azure Portal:  

* Skapa en datafabrik.
* Skapa två länkade tjänster:
   * En länkad Storage-tjänst som länkar din bloblagring med in-/utdatafiler till datafabriken.
   * En länkad HDInsight-tjänst på begäran som länkar ett Hadoop-kluster i HDInsight på begäran till datafabriken. Data Factory skapar ett Hadoop-kluster i HDInsight i rätt tid för att bearbeta indata och skapa utdata.
* Skapa två datauppsättningar som beskriver in- och utdata för HDInsight Hive-aktiviteten i pipelinen.
* Skapa en pipeline med en HDInsight Hive-aktivitet.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en pipeline med en transformeringsaktivitet (HDInsight-aktivitet) som kör ett Hive-skript i ett HDInsight-kluster på begäran. Information om hur du använder en kopierings aktivitet för att kopiera data från Blob Storage till Azure SQL Database finns i [Självstudier: kopiera data från Blob Storage till SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Se även
| Avsnitt | Beskrivning |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipeliner och aktiviteter i Data Factory och hur du kan använda dem till att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs schemaläggning och körning av appmodellen i Data Factory. |
| [Övervaka och hantera pipeliner med hjälp av appen Övervaka och hantera](data-factory-monitor-manage-app.md) |I den här artikeln beskrivs hur du övervakar, hanterar och felsöker pipeliner med appen Övervaka och hantera. |