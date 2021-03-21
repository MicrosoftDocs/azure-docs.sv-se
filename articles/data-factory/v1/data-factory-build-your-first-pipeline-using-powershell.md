---
title: Bygg din första data fabrik (PowerShell)
description: I den här självstudien skapar du ett exempel på en Azure Data Factory-pipeline med hjälp av Azure PowerShell.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: 60fb3a8c1fed8e8005ba0b8f67865a7d39c754d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392059"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Självstudier: Skapa din första Azure-datafabrik med Azure PowerShell
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [Quickstart: Create a data factory using Azure Data Factory](../quickstart-create-data-factory-powershell.md) (Snabbstart: Skapa en datafabrik med Azure Data Factory) om du använder den aktuella versionen av Data Factory-tjänsten.

I den här artikeln får du lära dig hur du använder Azure PowerShell till att skapa din första Azure-datafabrik. Om du vill gå igenom självstudien med andra verktyg/SDK:er kan du välja något av alternativen i listrutan.

Pipeline i den här självstudiekursen har en aktivitet: **HDInsight Hive-aktivitet**. Aktiviteten kör ett Hive-skript i ett Azure HDInsight-kluster som omvandlar indata för till utdata. Denna pipeline är schemalagd att köras en gång i månaden mellan angivna start- och sluttider. 

> [!NOTE]
> Datapipelinen i den här självstudien transformerar indata för att generera utdata. Den kopierar inte data från ett källdatalager till ett måldatalager. En självstudiekurs om hur du kopierar data med Azure Data Factory finns i [Tutorial: Copy data from Blob Storage to SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) (Självstudie: Kopiera data från Blob Storage till SQL Database).
> 
> En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Schemaläggning och utförande i Data Factory).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Läs igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutför de **nödvändiga** stegen.
* Följ instruktionerna i artikeln [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/) för att installera den senaste versionen av Azure PowerShell på datorn.
* (valfritt) Den här artikeln beskriver inte alla Data Factory-cmdletar. Se [Cmdlet-referens för Data Factory](/powershell/module/az.datafactory) för omfattande dokumentation om Data Factory-cmdletar.

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure PowerShell till att skapa en Azure Data Factory med namnet **FirstDataFactoryPSH**. En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Till exempel, en kopieringsaktivitet som kopierar data från en källa till en måldatalagring och en HDInsight Hive-aktivitet som kör ett Hive-skript som omvandlar indata. Låt oss börja med att skapa datafabriken i det här steget.

1. Starta Azure PowerShell och kör följande kommando. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
   * Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen.
     ```PowerShell
     Connect-AzAccount
     ```    
   * Kör följande kommando för att visa alla prenumerationer för det här kontot.
     ```PowerShell
     Get-AzSubscription  
     ```
   * Kör följande kommando för att välja den prenumeration som du vill arbeta med. Den här prenumerationen ska vara samma som den du använde i Azure-portalen.
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. Skapa en Azure-resursgrupp med namnet **ADFTutorialResourceGroup** genom att köra följande kommando:
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Vissa av stegen i den här självstudien förutsätter att du använder resursgruppen med namnet ADFTutorialResourceGroup. Om du använder en annan resursgrupp måste du använda den i stället för ADFTutorialResourceGroup i den här självstudiekursen.
3. Kör cmdleten **New-AzDataFactory** som skapar en data fabrik med namnet **FirstDataFactoryPSH**.

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet **Datafabriksnamnet ”FirstDataFactoryPSH” är inte tillgängligt** ändrar du namnet (exempelvis till yournameFirstDataFactoryPSH). Använd det här namnet i stället för ADFTutorialFactoryPSH när du utför stegen i självstudien. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
* Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen
* Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
* Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen:

  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.

Du måste först skapa några Data Factory-entiteter innan du skapar en pipeline. Du skapar först länkade tjänster för att länka datalager/beräkningar till ditt datalager, sedan definierar du de indata- och utdatauppsättningar som ska representera in- och utdata i länkade datalager och därefter skapar du pipelinen med en aktivitet som använder dessa datauppsättningar.

## <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget länkar du ditt Azure Storage-konto och ett Azure HDInsight-kluster på begäran till din datafabrik. In- och utdata för pipelinen i det här exemplet lagras i Azure Storage-kontot. En länkad HDInsight-tjänst används för att köra Hive-skriptet som anges i pipeline-aktiviteten i det här exemplet. Identifiera vilka datalager/beräkningstjänster som används i scenariot och länka dessa tjänster till datafabriken genom att skapa länkade tjänster.

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure-lagringskonto till datafabriken. Du använder samma Azure Storage-konto för att lagra indata/utdata och HQL-skriptfilen.

1. Skapa en JSON-fil med namnet StorageLinkedService.json i mappen C:\ADFGetStarted med följande innehåll. Skapa mappen ADFGetStarted om den inte redan finns.

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
    Ersätt **konto namnet** med namnet på ditt Azure Storage konto och **konto nyckeln** med åtkomst nyckeln för Azure Storage-kontot. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
2. Växla till mappen ADFGetStarted i Azure PowerShell.
3. Du kan använda cmdleten **New-AzDataFactoryLinkedService** som skapar en länkad tjänst. Den här cmdleten och andra Data Factory-cmdlets som du använder i den här självstudien kräver att du skickar värden för parametrarna *ResourceGroupName* och *DataFactoryName* . Du kan också använda **Get-AzDataFactory** för att hämta ett **DataFactory** -objekt och skicka objektet utan att skriva *ResourceGroupName* och *DataFactoryName* varje gång du kör en cmdlet. Kör följande kommando för att tilldela utdata från cmdleten **Get-AzDataFactory** till en **$DF** -variabel.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Kör nu cmdleten **New-AzDataFactoryLinkedService** som skapar den länkade **StorageLinkedService** -tjänsten.

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Om du inte hade kör cmdleten **Get-AzDataFactory** och tilldelat utdata till variabeln **$DF** måste du ange värden för parametrarna *ResourceGroupName* och *DataFactoryName* på följande sätt.

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Om du stänger Azure PowerShell mitt i själv studie kursen måste du köra cmdleten **Get-AzDataFactory** nästa gång du startar Azure PowerShell för att slutföra självstudien.

### <a name="create-azure-hdinsight-linked-service"></a>Skapa en Azure HDInsight-länkad tjänst
I det här steget ska du länka ett HDInsight-kluster på begäran till datafabriken. HDInsight-klustret skapas automatiskt vid körning och tas bort när bearbetningen är klar. Det är inaktivt under en angiven tidsrymd. Du kan använda ditt eget HDInsight-kluster i stället för att använda ett HDInsight-kluster på begäran. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för mer information.

1. Skapa en JSON-fil med namnet **HDInsightOnDemandLinkedService**.json i mappen **C:\ADFGetStarted** med följande innehåll.

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
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

   | Egenskap | Beskrivning |
   |:--- |:--- |
   | clusterSize |Anger HDInsight-klustrets storlek. |
   | timeToLive |Anger inaktivitetstiden för HDInsight-klustret innan det tas bort. |
   | linkedServiceName |Anger lagringskontot som används för att spara loggarna som genereras av HDInsight |

    Observera följande punkter:

   * Data Factory skapar ett **Linux-baserat** HDInsight-kluster åt dig med JSON. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
   * Du kan använda **ditt eget HDInsight-kluster** i stället för att använda ett HDInsight-kluster på begäran. Se [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för mer information.
   * HDInsight-klustret skapar en **standard behållare** i blob-lagringen som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor bearbetas, såvida det inte finns ett befintligt Live-kluster (**TimeToLive**). Klustret tas bort automatiskt när bearbetningen är klar.

       Allteftersom fler sektorer bearbetas kan du se många containrar i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: "ADF **yourdatafactoryname** - **linkedservicename**-datumtidsstämpel". Använd verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/) för att ta bort behållare i Azure Blob Storage.

     Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
2. Kör cmdleten **New-AzDataFactoryLinkedService** som skapar den länkade tjänsten med namnet HDInsightOnDemandLinkedService.
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar som ska representera in- och utdata för Hive-bearbetning. Dessa datauppsättningar finns i den **StorageLinkedService** som du skapade tidigare i självstudien. Den länkade tjänsten pekar på ett Azure-lagringskonto och datauppsättningarna anger container, mapp och filnamn i det lagringsutrymme som innehåller indata och utdata.

### <a name="create-input-dataset"></a>Skapa indatauppsättning
1. Skapa en JSON-fil med namnet **InputTable.json** i mappen **C:\ADFGetStarted** med följande innehåll:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
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
    JSON definierar en datauppsättning med namnet **AzureBlobInput**, som representerar indata för en aktivitet i pipelinen. Dessutom anger den att indata finns i blobcontainern **adfgetstarted** och i mappen **inputdata**.

    Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

   | Egenskap | Beskrivning |
   |:--- |:--- |
   | typ |Typegenskapen har angetts till AzureBlob eftersom det finns data i Azure Blob-lagringen. |
   | linkedServiceName |refererar till den StorageLinkedService som du skapade tidigare. |
   | fileName |Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I det här fallet bearbetas bara input.log. |
   | typ |Loggfilerna är i textformat, så vi använder TextFormat. |
   | columnDelimiter |kolumner i loggfilerna avgränsas med kommatecken (,). |
   | frekvens/intervall |frekvensen är månad och intervallet är 1, vilket innebär att indatasektorerna är tillgängliga en gång i månaden. |
   | extern |den här egenskapen anges som true om indatan inte skapades av Data Factory-tjänsten. |
2. I Azure PowerShell kör du följande kommando för att skapa Data Factory-datauppsättningen:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
Nu skapar du den utdatauppsättning som representerar de utdata som lagras i Azure Blob Storage.

1. Skapa en JSON-fil med namnet **OutputTable.json** i mappen **C:\ADFGetStarted** med följande innehåll:

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
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
    JSON definierar en datauppsättning med namnet **AzureBlobOutput**, som representerar utdata för en aktivitet i pipelinen. Dessutom anger den att resultaten lagras i blobcontainern **adfgetstarted** och i mappen **partitioneddata**. I avsnittet **tillgänglighet** anges att utdatauppsättningen skapas månadsvis.
2. I Azure PowerShell kör du följande kommando för att skapa Data Factory-datauppsättningen:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du din första pipeline med en **HDInsightHive**-aktivitet. Indatasektorn är tillgänglig månadsvis (frequency: Month, Interval: 1), utdatasektorn skapas varje månad och schemaegenskapen för aktiviteten har också inställningen Month. Inställningarna för utdatauppsättningen och aktivitetsschemaläggaren måste matcha. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. I slutet av det här avsnittet beskrivs de egenskaper som användes i följande JSON.

1. Skapa en JSON-fil med namnet MyFirstPipelinePSH.json i mappen C:\ADFGetStarted med följande innehåll:

   > [!IMPORTANT]
   > Ersätt **storageaccountname** med namnet på ditt lagringskonto i JSON.
   >
   >

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
                        "scriptLinkedService": "StorageLinkedService",
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

    Hive-skriptfilen, **skriptfilen partitionweblogs. HQL**, lagras i Azure Storage konto (anges av scriptLinkedService, kallas **StorageLinkedService**) och i mappen **script** i behållaren **adfgetstarted**.

    Avsnittet **Definierar** används för att ange körningsinställningar som kommer att skickas till Hive-skriptet som Hive-konfigurationsvärden (t.ex. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    Egenskaperna **start** och **slut** för pipelinen anger den aktiva perioden för pipelinen.

    I JSON-aktiviteten anger du att Hive-skriptet körs i den beräkning som anges av **linkedServiceName** – **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Information om JSON-egenskaper som används i exemplet finns i avsnittet om Pipeline-JSON i [Pipelines and activities in Azure Data Factory](data-factory-create-pipelines.md) (Pipelines och aktiviteter i Azure Data Factory).

2. Kontrollera att du ser filen **input.log** i mappen **adfgetstarted/inputdata** i Azure-blobblagringen och kör följande kommando för att distribuera pipelinen. Eftersom tiderna för **start** och **slut** har angetts tidigare och **isPaused** har angetts till false, kommer pipelinen (aktiviteten i pipelinen) köras omedelbart efter att du har distribuerat.

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Grattis, du har skapat din första pipeline med Azure PowerShell!

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget använder du Azure PowerShell till att övervaka vad som händer i en Azure Data Factory.

1. Kör **Get-AzDataFactory** och tilldela utdata till en **$DF** variabel.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. Kör **Get-AzDataFactorySlice** för att få information om alla segment i **EmpSQLTable**, vilket är den utgående tabellen i pipelinen.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Observera att StartDateTime som du anger här är samma starttid som angavs i din pipeline-JSON. Här är exempel på utdata:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Kör **Get-AzDataFactoryRun** för att hämta information om aktivitets körningar för en speciell sektor.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Här är exempel på utdata: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Du kan köra denna cmdlet tills du ser att sektorn har statusen **Klar** eller **Misslyckades**. När sektorn har statusen Klar, kontrollerar du mappen **partitioneddata** i containern **adfgetstarted** i bloblagringen för utdatan.  Det kan ta lite längre tid att skapa ett HDInsight-kluster på begäran.

    ![utdata](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter). Därför förväntar sig pipelinen att ta **cirka 30 minuter** att bearbeta sektorn.
>
> Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen överför du därför indatafilen (input.log) till indatamappen i containern adfgetstarted.
>
>

## <a name="summary"></a>Sammanfattning
I den här självstudien skapade du en Azure-datafabrik som bearbetar data genom att köra ett Hive-skript i ett Hadoop-kluster i HDInsight. Du utförde följande steg med hjälp av Data Factory-redigeraren i Azure Portal:

1. Du skapade en Azure **Data Factory**.
2. Du skapade två **länkade tjänster**:
   1. En länkad **Azure Storage-** tjänst som länkar din Azure Blob-lagring med in-/utdatafiler till datafabriken.
   2. En länkad **Azure HDInsight**-tjänst på begäran som länkar ett Hadoop-kluster i HDInsight på begäran till datafabriken. Azure Data Factory skapar ett Hadoop-kluster i HDInsight i rätt tid för att bearbeta indata och skapa utdata.
3. Du skapade två **datauppsättningar** som beskriver in- och utdata för Hive-aktiviteten för HDInsight i pipelinen.
4. Du skapade en **pipeline** med en **HDInsight Hive**-aktivitet.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en pipeline med en transformeringsaktivitet (HDInsight-aktivitet) som kör ett Hive-skript på ett Azure HDInsight-kluster på begäran. Om du vill se hur du använder en kopieringsaktivitet till att kopiera data från en Azure-blobb till Azure SQL kan du läsa mer i [Självstudie: Kopiera data från en Azure-blobb till Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Se även

| Avsnitt | Beskrivning |
|:--- |:--- |
| [Cmdlet-referens för Data Factory](/powershell/module/az.datafactory) |Se den omfattande dokumentationen för Data Factory-cmdletar |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |
