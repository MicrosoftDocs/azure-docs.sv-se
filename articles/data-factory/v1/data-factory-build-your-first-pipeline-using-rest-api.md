---
title: Bygg din första data fabrik (REST)
description: I den här självstudiekursen ska du skapa en Azure Data Factory-exempelpipeline med hjälp av REST-API:et för Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: tutorial
ms.date: 11/01/2017
ms.openlocfilehash: eb9ce65985521fb3f82032f04aec62538a157639
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785470"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Självstudier: Skapa din första Azure-datafabrik med hjälp av REST-API:et för Data Factory
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-mall](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [Quickstart: Create a data factory using Azure Data Factory](../quickstart-create-data-factory-rest-api.md) (Snabbstart: Skapa en datafabrik med Azure Data Factory) om du använder den aktuella versionen av Data Factory-tjänsten.

I den här artikeln använder du REST-API:et för Data Factory för att skapa din första Azure-datafabrik. Om du vill gå igenom självstudien med andra verktyg/SDK:er kan du välja något av alternativen i listrutan.

Pipeline i den här självstudiekursen har en aktivitet: **HDInsight Hive-aktivitet**. Aktiviteten kör ett Hive-skript i ett Azure HDInsight-kluster som omvandlar indata för till utdata. Denna pipeline är schemalagd att köras en gång i månaden mellan angivna start- och sluttider.

> [!NOTE]
> Den här artikeln beskriver inte hela REST-API:et. Omfattande dokumentation om REST API finns i [referensen för REST-API för Data Factory](/rest/api/datafactory/).
> 
> En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (Schemaläggning och utförande i Data Factory).


## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Läs igenom artikeln [Självstudier – översikt](data-factory-build-your-first-pipeline.md) och slutför de **nödvändiga** stegen.
* Installera [Curl](https://curl.haxx.se/dlwiz/) på din dator. Du kan använda verktyget CURL med REST-kommandon för att skapa en datafabrik.
* Gör följande genom att följa anvisningarna i [den här artikeln](../../active-directory/develop/howto-create-service-principal-portal.md):
  1. Skapa en webbapp med namnet **ADFGetStartedApp** i Azure Active Directory.
  2. Hämta ett **klient-ID** och en **hemlig nyckel**.
  3. Hämta **klient-ID**.
  4. Tilldela **ADFGetStartedApp**-programmet rollen som **Data Factory-deltagare**.
* Installera [Azure PowerShell](/powershell/azure/).
* Starta **PowerShell** och kör följande kommando. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
  1. Kör **Connect-AzAccount** och ange det användar namn och lösen ord som du använder för att logga in på Azure Portal.
  2. Kör **Get-AzSubscription** för att visa alla prenumerationer för det här kontot.
  3. Kör **Get-AzSubscription-SubscriptionName NameOfAzureSubscription | Set-AzContext** för att välja den prenumeration som du vill arbeta med. Ersätt **NameOfAzureSubscription** med namnet på din Azure-prenumeration.
* Skapa en Azure-resursgrupp med namnet **ADFTutorialResourceGroup** genom att köra följande kommando i PowerShell:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

   Vissa av stegen i den här självstudien förutsätter att du använder resursgruppen med namnet ADFTutorialResourceGroup. Om du använder en annan resursgrupp måste du använda namnet på din resursgrupp i stället för ADFTutorialResourceGroup i den här självstudiekursen.

## <a name="create-json-definitions"></a>Skapa JSON-definitioner
Skapa följande JSON-filer i mappen som curl.exe finns i.

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Namnet måste vara globalt unikt, så du kan behöva lägga till ett prefix eller suffix till ADFCopyTutorialDF för att göra det unikt.
>
>

```JSON
{
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Ersätt **accountname** och **accountkey** med namnet och nyckeln för ditt Azure-lagringskonto. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
>
>

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

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
| clusterSize |Storleken på HDInsight-klustret. |
| timeToLive |Anger inaktivitetstiden för HDInsight-klustret innan det tas bort. |
| linkedServiceName |Anger lagringskontot som används för att spara loggarna som genereras av HDInsight |

Observera följande punkter:

* Data Factory skapar ett **Linux-baserat** HDInsight-kluster med ovanstående JSON. Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.
* Du kan använda **ditt eget HDInsight-kluster** i stället för att använda ett HDInsight-kluster på begäran. Se [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) för mer information.
* HDInsight-klustret skapar en **standard behållare** i blob-lagringen som du angav i JSON (**linkedServiceName**). HDInsight tar inte bort den här containern när klustret tas bort. Det här beteendet är avsiktligt. Med en HDInsight-länkad tjänst på begäran skapas ett HDInsight-kluster varje gång en sektor bearbetas, såvida det inte finns ett befintligt live-kluster (**timeToLive**). Det raderas när bearbetningen är klar.

    Allteftersom fler sektorer bearbetas kan du se många containrar i ditt Azure Blob Storage. Om du inte behöver dem för att felsöka jobb, kan du ta bort dem för att minska lagringskostnaderna. Namnen på de här behållarna följer ett mönster: "ADF **yourdatafactoryname** - **linkedservicename**-datumtidsstämpel". Använd verktyg som [Microsoft Azure Storage Explorer](https://storageexplorer.com/) för att ta bort behållare i Azure Blob Storage.

Se [HDInsight-länkad tjänst på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) för mer information.

### <a name="inputdatasetjson"></a>inputdataset.json

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

JSON definierar en datauppsättning med namnet **AzureBlobInput**, som representerar indata för en aktivitet i pipelinen. Dessutom anger den att indata finns i blobcontainern **adfgetstarted** och i mappen **inputdata**.

Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

| Egenskap | Beskrivning |
|:--- |:--- |
| typ |Typegenskapen har angetts till AzureBlob eftersom det finns data i Azure Blob-lagringen. |
| linkedServiceName |refererar till den StorageLinkedService som du skapade tidigare. |
| fileName |Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I det här fallet bearbetas bara input.log. |
| typ |Loggfilerna är i textformat, så vi använder TextFormat. |
| columnDelimiter |kolumner i loggfilerna avgränsas med kommatecken () |
| frekvens/intervall |frekvensen är månad och intervallet är 1, vilket innebär att indatasektorerna är tillgängliga en gång i månaden. |
| extern |den här egenskapen anges som true om indatan inte skapades av Data Factory-tjänsten. |

### <a name="outputdatasetjson"></a>outputdataset.json

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

JSON definierar en datauppsättning med namnet **AzureBlobOutput**, som representerar utdata för en aktivitet i pipelinen. Dessutom anger den att resultaten lagras i blobcontainern **adfgetstarted** och i mappen **partitioneddata**. I avsnittet **tillgänglighet** anges att utdatauppsättningen skapas månadsvis.

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> Ersätt **storageaccountname** med namnet på ditt Azure-lagringskonto.
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

I JSON-kodfragmentet skapar du en pipeline med en enda aktivitet, som använder Hive för att bearbeta data i ett HDInsight-kluster.

Hive-skriptfilen **partitionweblogs.hql** lagras i Azure-lagringskontot (anges med scriptLinkedService, kallas **StorageLinkedService**), och i mappen **script** i containern **adfgetstarted**.

Avsnittet **defines** anger körningsinställningar som skickas till Hive-skriptet som Hive-konfigurationsvärden (t.ex. ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

Egenskaperna **start** och **slut** för pipelinen anger den aktiva perioden för pipelinen.

I JSON-aktiviteten anger du att Hive-skriptet körs i den beräkning som anges av **linkedServiceName** – **HDInsightOnDemandLinkedService**.

> [!NOTE]
> Information om JSON-egenskaperna som används i föregående exempel finns i avsnittet om Pipeline-JSON i [Pipelines and activities in Azure Data Factory](data-factory-create-pipelines.md) (Pipelines och aktiviteter i Azure Data Factory).
>
>

## <a name="set-global-variables"></a>Ange globala variabler
När du har ersatt värdena med dina egna kör du följande kommandon i Azure PowerShell:

> [!IMPORTANT]
> Anvisningar för hur du hämtar klient-ID, klienthemlighet, klientorganisations-ID och prenumerations-ID finns i [kravavsnittet](#prerequisites).
>
>

```powershell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>Autentisera med AAD

```powershell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget ska du skapa en Azure Data Factory-fabrik med namnet **FirstDataFactoryREST**. En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Det kan till exempel vara en kopieringsaktivitet som kopierar data från en källa till ett måldatalager och en Hive-aktivitet för HDInsight som kör Hive-skript för att transformera data. Skapa datafabriken genom att köra följande kommandon:

1. Tilldela kommandot till variabeln med namnet **cmd**.

    Bekräfta att namnet på datafabriken som du anger här (ADFCopyTutorialDF) matchar namnet i **datafactory.json**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datafabriken har skapats korrekt visas JSON för datafabriken i **resultatet**. Annars visas ett felmeddelande.

    ```powershell
    Write-Host $results
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om följande fel visas i resultatet: **Datafabriksnamnet ”FirstDataFactoryREST” är inte tillgängligt** följer du dessa steg:
  1. Ändra namnet (till exempel dittnamnFirstDataFactoryREST) i filen **datafactory.json**. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
  2. I det första kommandot där variabeln **$cmd** tilldelas ett värde, ersätter du FirstDataFactoryREST med det nya namnet och kör kommandot.
  3. Anropa REST-API:et genom att köra de följande två kommandona för att skapa datafabriken och skriva ut resultatet av åtgärden.
* Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen
* Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
* Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen:

  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad:
    ```powershell
    Get-AzResourceProvider
    ```
  * Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.

Du måste först skapa några Data Factory-entiteter innan du skapar en pipeline. Först skapar du länkade tjänster för att länka datalager/beräkningar till ditt datalager och definiera in- och utdatauppsättningar som representerar data i länkade datalager.

## <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget länkar du ditt Azure Storage-konto och ett Azure HDInsight-kluster på begäran till din datafabrik. In- och utdata för pipelinen i det här exemplet lagras i Azure Storage-kontot. En länkad HDInsight-tjänst används för att köra Hive-skriptet som anges i pipeline-aktiviteten i det här exemplet.

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure-lagringskonto till datafabriken. I den här självstudiekursen använder du samma Azure Storage-konto för att lagra indata/utdata och HQL-skriptfilen.

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om den länkade tjänsten har skapats korrekt visas JSON för den länkade tjänsten i **resultatet**. Annars visas ett felmeddelande.

    ```powershell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Skapa en Azure HDInsight-länkad tjänst
I det här steget ska du länka ett HDInsight-kluster på begäran till datafabriken. HDInsight-klustret skapas automatiskt vid körning och tas bort när bearbetningen är klar. Det är inaktivt under en angiven tidsrymd. Du kan använda ditt eget HDInsight-kluster i stället för att använda ett HDInsight-kluster på begäran. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för mer information.

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om den länkade tjänsten har skapats korrekt visas JSON för den länkade tjänsten i **resultatet**. Annars visas ett felmeddelande.

    ```powershell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du datauppsättningar som ska representera in- och utdata för Hive-bearbetning. Dessa datauppsättningar finns i den **StorageLinkedService** som du skapade tidigare i självstudien. Den länkade tjänsten pekar på ett Azure-lagringskonto och datauppsättningarna anger container, mapp och filnamn i det lagringsutrymme som innehåller indata och utdata.

### <a name="create-input-dataset"></a>Skapa indatauppsättning
I det här steget skapar du indatauppsättningen som ska representera indata som lagras i Azure Blob Storage.

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.

    ```powershell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
I det här steget skapar du utdatauppsättningen som ska representera utdata som lagras i Azure Blob Storage.

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.

    ```powershell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du din första pipeline med en **HDInsightHive**-aktivitet. Indatasektorn är tillgänglig månadsvis (frequency: Month, Interval: 1), utdatasektorn skapas varje månad och schemaegenskapen för aktiviteten har också inställningen Month. Inställningarna för utdatauppsättningen och aktivitetsschemaläggaren måste matcha. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen.

Kontrollera att du ser filen **input.log** i mappen **adfgetstarted/inputdata** i Azure-blobblagringen och kör följande kommando för att distribuera pipelinen. Eftersom tiderna för **start** och **slut** har angetts tidigare och **isPaused** har angetts till false, kommer pipelinen (aktiviteten i pipelinen) köras omedelbart efter att du har distribuerat.

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.

    ```powershell
    Write-Host $results
    ```
4. Grattis, du har skapat din första pipeline med Azure PowerShell!

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda REST-API:et för Data Factory för att övervaka sektorer som genereras av pipelinen.

```powershell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> Att skapa ett HDInsight-kluster på begäran kan ta lite längre tid (cirka 20 minuter). Därför förväntar sig pipelinen att ta **cirka 30 minuter** att bearbeta sektorn.
>
>

Kör Invoke-Command och nästa kommando tills du ser sektorn med tillståndet **Ready** eller **Failed**. När sektorn har statusen Klar, kontrollerar du mappen **partitioneddata** i containern **adfgetstarted** i bloblagringen för utdatan.  Det kan ta lite längre tid att skapa ett HDInsight-kluster på begäran.

![utdata](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> Indatafilen tas bort när sektorn har bearbetats. Om du vill köra sektorn eller gå igenom självstudien igen överför du därför indatafilen (input.log) till indatamappen i containern adfgetstarted.
>
>

Du kan också använda Azure-portalen för att övervaka sektorer och felsöka eventuella problem. Mer information finns i [Övervaka pipelines med hjälp av Azure-portalen](data-factory-monitor-manage-pipelines.md).

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
| [Referens för REST-API:et för Data Factory](/rest/api/datafactory/) |Se den omfattande dokumentationen för Data Factory-cmdletar |
| [Pipelines](data-factory-create-pipelines.md) |I den här artikeln beskriver vi pipelines och aktiviteter i Azure Data Factory och hur du kan använda dem för att konstruera datadrivna arbetsflöden från slutpunkt till slutpunkt för ditt scenario eller ditt företag. |
| [Datauppsättningar](data-factory-create-datasets.md) |I den här artikeln förklaras hur datauppsättningar fungerar i Azure Data Factory. |
| [Schemaläggning och körning](data-factory-scheduling-and-execution.md) |I den här artikeln beskrivs aspekter för schemaläggning och körning av Azure Data Factory-programmodellen. |
| [Övervaka och hantera pipelines med övervakningsappen](data-factory-monitor-manage-app.md) |Den här artikeln beskriver hur du övervakar, hanterar och felsöker pipelines med övervaknings- och hanteringsappen. |
