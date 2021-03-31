---
title: 'Självstudie: använda REST API för att skapa en Azure Data Factory pipeline '
description: I den här självstudien använder du REST API för att skapa en Azure Data Factory-pipeline med en kopierings aktivitet för att kopiera data från en Azure Blob Storage till Azure SQL Database.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7488834252dcd4e231c2d91a1435838befe7b1d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377031"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Självstudier: Använd REST API för att skapa ett Azure Data Factory-pipeline för att kopiera data 
> [!div class="op_single_selector"]
> * [Översikt och förutsättningar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Guiden Kopiera](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-mall](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET-API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-rest-api.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten. 

I den här artikeln får du lära dig hur du använder REST API för att skapa en data fabrik med en pipeline som kopierar data från en Azure Blob Storage till Azure SQL Database. Om du inte har använt Azure Data Factory, bör du läsa igenom artikeln [Introduktion till Azure Data Factory](data-factory-introduction.md) innan du genomför den här självstudien.   

I den här självstudien får du skapa en pipeline i en aktivitet: kopieringsaktivitet. Kopieringsaktiviteten kopierar data från källans datalager till mottagarens datalager. En lista över datakällor som stöds som källor och mottagare finns i [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Mer information om kopierings aktiviteten finns i [aktiviteter för data förflyttning](data-factory-data-movement-activities.md).

En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer information finns i [flera aktiviteter i en pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Den här artikeln beskriver inte hela REST-API:et för Data Factory. Omfattande dokumentation om Data Factory-cmdlets finns i [referensen för REST-API:et för Data Factory](/rest/api/datafactory/).
>  
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Gå igenom [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) och slutför de **nödvändiga** stegen.
* Installera [Curl](https://curl.haxx.se/dlwiz/) på din dator. Du kan använda verktyget Curl med REST-kommandon för att skapa en datafabrik. 
* Gör följande genom att följa anvisningarna i [den här artikeln](../../active-directory/develop/howto-create-service-principal-portal.md): 
  1. Skapa en webbapp med namnet **ADFCopyTutorialApp** i Azure Active Directory.
  2. Hämta ett **klient-ID** och en **hemlig nyckel**. 
  3. Hämta **klient-ID**. 
  4. Tilldela **ADFCopyTutorialApp**-programmet rollen som **Data Factory-deltagare**.  
* Installera [Azure PowerShell](/powershell/azure/).  
* Starta **PowerShell** och kör följande kommando. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.
  
  1. Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in på Azure-portalen:
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. Kör följande kommando för att visa alla prenumerationer för det här kontot:

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **&lt; NameOfAzureSubscription** &gt; med namnet på din Azure-prenumeration. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. Skapa en Azure-resursgrupp med namnet **ADFTutorialResourceGroup** genom att köra följande kommando i PowerShell:  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      Om resursgruppen redan finns anger du om du vill uppdatera den (Y) eller lämna den som den är (N). 
     
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
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Ersätt **accountname** och **accountkey** med namnet och nyckeln för ditt Azure-lagringskonto. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

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

Mer information om egenskaper som JSON finns i [Azure Storage länkade tjänster](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.json
> [!IMPORTANT]
> Ersätt **servername**, **databasename**, **username** och **Password** med namnet på servern, namnet på SQL-databasen, användar kontot och lösen ordet för kontot.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Mer information om egenskaper som JSON finns i [Azure SQL länkade tjänster](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
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
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

| Egenskap | Beskrivning |
|:--- |:--- |
| typ | Typegenskapen har angetts till **AzureBlob** eftersom det finns data i Azure Blob-lagringen. |
| linkedServiceName | Refererar till **AzureStorageLinkedService** som du skapade tidigare. |
| folderPath | Anger BLOB- **behållaren** och **mappen** som innehåller blobar för indataportar. I den här självstudiekursen adftutorial är blobcontainern och -mappen rotmappen. | 
| fileName | Den här egenskapen är valfri. Om du tar bort egenskapen kommer alla filer från folderPath hämtas. I den här självstudiekursen har angetts **emp.txt** som filnamn så att endast den filen hämtas för bearbetning. |
| format -> typ |Indatafilen är i textformat, så vi använder **TextFormat**. |
| columnDelimiter | Kolumner i loggfilerna avgränsas med **kommatecken (`,`)**. |
| frekvens/intervall | Frekvensen är **timme** och intervallet är **1**, vilket innebär att indatasektorerna är tillgängliga en gång i **timmen**. Det betyder att tjänsten Data Factory söker efter indata varje timme i rotmappen för den angivna blobcontainern (**adftutorial**). Den söker data i pipelinens start- och sluttider och inte före eller efter dessa tider.  |
| extern | Den här egenskapen anges som **true** om indatan inte skapades av denna pipeline. Inkommande data i den här självstudien finns i filen emp.txt som genereras av denna pipeline, så vi ställer in den här egenskapen på true. |

Mer information om de här JSON-egenskaperna finns i artikeln [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
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
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "emp"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Följande tabell innehåller beskrivningar av de JSON-egenskaper som användes i kodfragmentet:

| Egenskap | Beskrivning |
|:--- |:--- |
| typ | Egenskapen Type anges till **AzureSqlTable** eftersom data kopieras till en tabell i Azure SQL Database. |
| linkedServiceName | Refererar till **AzureSqlLinkedService** som du skapade tidigare. |
| tableName | Ange **tabellen** dit data kopieras. | 
| frekvens/intervall | Frekvensen är inställd på **timme** och intervallet är **1**, vilket innebär att utdatasegment produceras **varje timme** mellan pipelinens start- och sluttider, inte före eller efter dessa tider.  |

Det finns tre kolumner – **ID**, **FirstName** och **LastName** – i emp-tabellen i databasen. ID är en identitetskolumn, så du anger bara **FirstName** och **LastName** här.

Mer information om de här JSON-egenskaperna finns i artikeln [Azure SQL-anslutningsapp](data-factory-azure-sql-connector.md#dataset-properties).

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL Database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

Observera följande punkter:

- I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. I Data Factory-lösningar, kan du också använda [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).
- Indata för aktiviteten är inställd på **AzureBlobInput** och utdata för aktiviteten är inställd på **AzureSqlOutput**. 
- I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. En fullständig lista över datakällor som stöds av kopieringsaktiviteten som källor och mottagare finns i [Datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Klicka på länken i tabellen om du vill veta hur du använder ett visst datalager som stöds som källa/mottagare.  
 
Ersätt värdet i **start** egenskapen med den aktuella dagen och **slut** värdet med nästa dag. Du kan ange endast datumdelen och hoppa över tidsvärdet. Till exempel ”2017-02-03” som motsvarar ”2017-02-03T00:00:00Z”
 
Både start- och slutdatum måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2016-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen. 
 
Om du inte anger värdet för **slut** egenskapen, beräknas det som "**Start + 48 timmar**". Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut** egenskapen.
 
I det föregående exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.

Beskrivningar av JSON-egenskaper i en pipeline-definition finns i artikeln [skapa pipelines](data-factory-create-pipelines.md). Beskrivningar av JSON-egenskaper i en kopieringsaktivitet-definition finns i artikeln [aktiviteter för dataflyttning](data-factory-data-movement-activities.md). Beskrivningar av JSON-egenskaper som stöds av BlobSource finns i artikeln [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md). Beskrivningar av JSON-egenskaper som stöds av SqlSink finns i artikeln [Azure SQL Database-anslutningsapp](data-factory-azure-sql-connector.md).

## <a name="set-global-variables"></a>Ange globala variabler
När du har ersatt värdena med dina egna kör du följande kommandon i Azure PowerShell:

> [!IMPORTANT]
> Anvisningar för hur du hämtar klient-ID, klienthemlighet, klientorganisations-ID och prenumerations-ID finns i [kravavsnittet](#prerequisites).   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Kör följande kommando efter att du har uppdaterat namnet på datafabriken som du använder: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Autentisera med AAD
Kör följande kommando för att autentisera med Azure Active Directory (AAD): 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget ska du skapa en Azure Data Factory med namnet **ADFCopyTutorialDF**. En datafabrik kan ha en eller flera pipelines. En pipeline kan innehålla en eller flera aktiviteter. Till exempel en kopieringsaktivitet som kopierar data från ett källdatalager till ett måldatalager. En Hive-aktivitet för HDInsight för att köra Hive-skript som transformerar indata till produktutdata. Skapa datafabriken genom att köra följande kommandon: 

1. Tilldela kommandot till variabeln med namnet **cmd**. 
   
    > [!IMPORTANT]
    > Bekräfta att namnet på datafabriken som du anger här (ADFCopyTutorialDF) matchar namnet i **datafactory.json**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datafabriken har skapats korrekt visas JSON för datafabriken i **resultatet**. Annars visas ett felmeddelande.  
   
    ```
    Write-Host $results
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du ser felet i resultatet: **data fabriks namnet "ADFCopyTutorialDF" är inte tillgängligt**, utför följande steg:  
  
  1. Ändra namnet (till exempel dittnamnADFCopyTutorialDF) i filen **datafactory.json**.
  2. I det första kommandot där variabeln **$cmd** tilldelas ett värde ersätter du ADFCopyTutorialDF med det nya namnet och kör kommandot. 
  3. Anropa REST-API:et genom att köra de följande två kommandona för att skapa datafabriken och skriva ut resultatet av åtgärden. 
     
     Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.
* Om du vill skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen
* Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och blir då synligt offentligt.
* Om du får felet: ”**Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory**” gör du något av följande och försöker att publicera igen: 
  
  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern: 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.

Du måste först skapa några Data Factory-entiteter innan du skapar en pipeline. Först skapar du länkade tjänster för att länka käll- och måldatalager till ditt datalager. Sedan definierar du in- och utdatauppsättningar för att representera data i länkade datalager. Slutligen skapar du pipelinen med en aktivitet som använder dessa datauppsättningar.

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här självstudiekursen kommer använder du inte någon beräkningstjänst, till exempel Azure HDInsight eller Azure Data Lake Analytics. Du använder två datalager av typen Azure Storage (källa) och Azure SQL Database (mål). Därför kan du skapa två länkade tjänster som heter AzureStorageLinkedService och AzureSqlLinkedService av typerna: AzureStorage och AzureSqlDatabase.  

AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Använd det lagringskonto i vilket du skapade en container och laddade upp data under [förberedelsestegen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

AzureSqlLinkedService länkar Azure SQL Database till data fabriken. Data som kopieras från blob-lagringen sparas i den här databasen. Du har skapat den tomma tabellen i den här databasen som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).  

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst
I det här steget länkar du ditt Azure-lagringskonto till datafabriken. Du anger namnet och nyckeln för Azure Storage-kontot i det här avsnittet. Se [Länkad Azure Storage-tjänst](data-factory-azure-blob-connector.md#azure-storage-linked-service) om du vill ha information om JSON-egenskaper som används för att definiera en länkad Azure Storage-tjänst.  

1. Tilldela kommandot till variabeln med namnet **cmd**. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om den länkade tjänsten har skapats korrekt visas JSON för den länkade tjänsten i **resultatet**. Annars visas ett felmeddelande.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Skapa en länkad Azure SQL-tjänst
I det här steget länkar du Azure SQL Database till din data fabrik. Du anger det logiska SQL Server-namnet, databas namnet, användar namnet och användar lösen ordet i det här avsnittet. Se [Länkad Azure SQL-tjänst](data-factory-azure-sql-connector.md#linked-service-properties) om du vill ha information om JSON-egenskaper som används för att definiera en länkad Azure SQL-tjänst.

1. Tilldela kommandot till variabeln med namnet **cmd**. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azuresqllinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om den länkade tjänsten har skapats korrekt visas JSON för den länkade tjänsten i **resultatet**. Annars visas ett felmeddelande.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar
I föregående steg skapade du länkade tjänster för att länka ditt Azure Storage-konto och Azure SQL Database till din data fabrik. I det här steget definierar du två datauppsättningar, AzureBlobInput och AzureSqlOutput, som visar in- och utdata som lagras i de datalager som refereras till av AzureStorageLinkedService och AzureSqlLinkedService.

Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. Och en Blob-datauppsättning (AzureBlobInput) anger vilken blobcontainer och mapp som innehåller data.  

På samma sätt anger den länkade tjänsten Azure SQL Database den anslutnings sträng som Data Factory tjänsten använder vid körning för att ansluta till Azure SQL Database. Och utdatauppsättningen (OutputDataset) för SQL-tabellen anger tabellen i databasen som data kopieras till från blob-lagringen. 

### <a name="create-input-dataset"></a>Skapa indatauppsättning
I det här steget skapar du en datauppsättning med namnet AzureBlobInput som pekar på en blobfil (emp.ext) i rotmappen i en blobcontainer (adftutorial) i Azure Storage som representeras av den länkade tjänsten AzureStorageLinkedService. Om du inte anger ett värde för filnamnet (eller hoppar över det), kommer data från alla blobbar i indatamappen att kopieras till målet. I den här kursen anger du ett värde för filnamnet. 

1. Tilldela kommandot till variabeln med namnet **cmd**. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata
Den länkade tjänsten Azure SQL Database anger anslutnings strängen som Data Factory tjänsten använder vid körning för att ansluta till Azure SQL Database. Och utdatauppsättningen (OutputDataset) för SQL-tabellen som du skapade i det här steget anger tabellen i databasen som data kopieras till från blob-lagringen.

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget ska du skapa en pipeline med en **kopieringsaktivitet** som använder **AzureBlobInput** som indata och **AzureSqlOutput** som utdata.

Schemat styrs för närvarande av utdatamängd. I den här självstudiekursen är datamängden för utdata konfigurerad för att skapa ett segment en gång i timmen. Pipelinen har en starttid och sluttid som är en dag från varandra, vilket är 24 timmar. Därför produceras 24 segment för utdatauppsättningen av pipeline. 

1. Tilldela kommandot till variabeln med namnet **cmd**.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Kör kommandot med **Invoke-Command**.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Granska resultaten. Om datauppsättningen har skapats korrekt visas JSON för datauppsättningen i **resultatet**. Annars visas ett felmeddelande.  

    ```PowerShell   
    Write-Host $results
    ```

**Grattis!** Du har skapat en Azure-datafabrik med en pipeline som kopierar data från Azure-Blob Storage till Azure SQL Database.

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget ska du använda REST-API:et för Data Factory för att övervaka sektorer som genereras av pipelinen.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Kontrollera att start- och sluttider som anges i följande kommando matchar start- och sluttider för pipeline. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Kör Invoke-Command och nästa kommando tills du ser sektorn med tillståndet **Ready** eller **Failed**. När sektorn har statusen klar kontrollerar du tabellen **EMP** i Azure SQL Database för utdata. 

För varje sektor kopieras två rader med data från käll filen till tabellen EMP i Azure SQL Database. Därför finns det 24 nya poster i emp-tabellen när alla sektorer har bearbetats (statusen Ready). 

## <a name="summary"></a>Sammanfattning
I den här självstudien använde du REST API för att skapa en Azure Data Factory för att kopiera data från en Azure-blob till Azure SQL Database. Här är de avancerade steg som du utförde i självstudien:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:
   1. En länkad Azure Storage-tjänst för att länka Azure Storage-kontot som innehåller indata.     
   2. En länkad Azure SQL-tjänst för att länka databasen som innehåller utdata. 
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med en kopieringsaktivitet med BlobSource som källa och SqlSink som mottagare. 

## <a name="next-steps"></a>Nästa steg
I den här självstudien använde du Azure Blob Storage som käll data lager och Azure SQL Database som mål data lager i en kopierings åtgärd. Följande tabell innehåller en lista över datalager som stöds som källor och mål av kopieringsaktiviteten: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

För mer information om hur du kopierar data till/från ett datalager klickar du på länken för datalagret i tabellen.
