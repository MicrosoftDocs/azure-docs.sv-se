---
title: 'Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Visual Studio '
description: I de här självstudierna skapar du en Azure Data Factory-pipeline med en kopieringsaktivitet genom att använda Visual Studio.
author: linda33wj
ms.service: data-factory
ms.custom: vs-azure
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f6ff31c3c0d37078f8dfbd934593adb4b6a6ef4b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377116"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Självstudie: Skapa en pipeline med en kopieringsaktivitet med hjälp av Visual Studio
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
> Den här artikeln gäller för version 1 av Data Factory. Läs [copy activity tutorial in version 2 documentation](../quickstart-create-data-factory-dot-net.md) (kopiera aktivitetssjälvstudien i dokumentationen för version 2) om du använder den aktuella versionen av Data Factory-tjänsten. 

I den här artikeln får du lära dig hur du använder Microsoft Visual Studio för att skapa en data fabrik med en pipeline som kopierar data från en Azure Blob Storage till Azure SQL Database. Om du inte har använt Azure Data Factory, bör du läsa igenom artikeln [Introduktion till Azure Data Factory](data-factory-introduction.md) innan du genomför den här självstudien.   

I den här självstudien får du skapa en pipeline i en aktivitet: kopieringsaktivitet. Kopieringsaktiviteten kopierar data från källans datalager till mottagarens datalager. En lista över datakällor som stöds som källor och mottagare finns i [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Aktiviteten drivs av en globalt tillgänglig tjänst som kan kopiera data mellan olika datalager på ett säkert, tillförlitligt och skalbart sätt. Mer information om kopierings aktiviteten finns i [aktiviteter för data förflyttning](data-factory-data-movement-activities.md).

En pipeline kan ha fler än en aktivitet. Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer information finns i [flera aktiviteter i en pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE] 
> Datapipelinen i den här självstudien kopierar data från ett källdatalager till ett måldatalager. Om du vill se en självstudie som visar hur du omvandlar data med Azure Data Factory går du till [Tutorial: Build a pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Självstudie: Bygg en pipeline för att omvandla data med Hadoop-kluster).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. Läs igenom artikeln [Självstudier – översikt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) och slutför de **nödvändiga** stegen.       
2. Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
3. Du måste ha följande installerat på datorn: 
   * Visual Studio 2013 eller Visual Studio 2015
   * Hämta Azure SDK för Visual Studio 2013 eller Visual Studio 2015. Gå till [Azures hämtningssida](https://azure.microsoft.com/downloads/) och klicka på **VS 2013** eller **VS 2015** i **.NET**-avsnittet.
   * Hämta det senaste Azure Data Factory-plugin-programmet för Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Du kan också uppdatera plugin-programmet genom att göra följande: i menyn klickar du på **verktyg**  ->  **tillägg och uppdateringar**  ->  **online**  ->  **Visual Studio-galleriet**  ->  **Microsoft Azure Data Factory verktyg för Visual Studio**  ->  **Update**.

## <a name="steps"></a>Steg

Här är de steg du utför som en del av de här självstudierna:

1. Skapa **länkade tjänster** i den här datafabriken. I det här steget kan du skapa två länkade tjänster: Azure Storage och Azure SQL-databas. 

    AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Du har skapat en container och överfört data till det här lagringskontot som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

    AzureSqlLinkedService länkar Azure SQL Database till data fabriken. Data som kopieras från blob-lagringen sparas i den här databasen. Du har skapat den SQL-tabellen i den här databasen som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).     

2. Skapa **datauppsättningar** för indata och utdata i datafabriken.  

    Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. Och en Azure Blob-datauppsättning anger vilken blobcontainer och mapp som innehåller data.  

    På samma sätt anger den länkade tjänsten Azure SQL Database den anslutnings sträng som Data Factory tjänsten använder vid körning för att ansluta till Azure SQL Database. Och utdatauppsättningen för SQL-tabellen anger tabellen i databasen som data kopieras till från blob-lagringen.

3. Skapa en **pipeline** i datafabriken. I det här steget kan du skapa en pipeline med en kopieringsaktivitet.   

    Kopierings aktiviteten kopierar data från en BLOB i Azure Blob Storage till en tabell i Azure SQL Database. Du kan använda en kopieringsaktivitet i en pipeline för att kopiera data från alla datakällor som stöds till ett mål som stöds. I avsnittet [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md#supported-data-stores-and-formats) finns en lista över datalager som stöds.

4. Skapa en Azure-**datafabrik** när du distribuerar Data Factory-enheter (länkade tjänster, datauppsättningar och tabeller och pipelines). 

## <a name="create-visual-studio-project"></a>Skapa Visual Studio-projekt

1. Starta **Visual Studio 2015**. Klicka på **Arkiv**, peka på **Nytt** och klicka på **Projekt**. Dialogrutan **Nytt projekt** visas.  

2. I dialogrutan **Nytt projekt** väljer du mallen **DataFactory** och klickar på **Tomt Data Factory-projekt**.  

    ![Dialogrutan Nytt projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Ange namnet på projektet, platsen för lösningen och namnet för lösningen och klicka sedan på **OK**.

    ![Solution Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Skapa länkade tjänster

Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I den här självstudiekursen kommer använder du inte någon beräkningstjänst, till exempel Azure HDInsight eller Azure Data Lake Analytics. Du använder två datalager av typen Azure Storage (källa) och Azure SQL Database (mål). 

Därför kan du skapa två länkade tjänster: AzureStorage och AzureSqlDatabase.  

AzureStorageLinkedService länkar ditt Azure Storage-konto till datafabriken. Använd det lagringskonto i vilket du skapade en container och laddade upp data under [förberedelsestegen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).   

Azure SQL-länkade tjänst länkar Azure SQL Database till data fabriken. Data som kopieras från blob-lagringen sparas i den här databasen. Du har skapat den tomma tabellen i den här databasen som en del av [förhandskraven](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Länkade tjänster länkar datalager eller beräkningstjänster till en Azure-datafabrik. I [stödda datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) står alla källor och mottagare som stöds av Kopiera aktivitet. Se [Beräkna länkade tjänster](data-factory-compute-linked-services.md) för att se listan över Compute Services som stöds av Data Factory. I den här självstudiekursen använder du ingen tjänst för beräkning. 

### <a name="create-the-azure-storage-linked-service"></a>Skapa den länkade Azure Storage-tjänsten

1. I **Solution Explorer** högerklickar du på **Länkade tjänster**, pekar på **Lägg till** och klickar på **Nytt objekt**.      

2. I dialogrutan **Lägg till nytt objekt** väljer du **Länkad Azure Storage-tjänst** i listan och klickar på **Lägg till**. 

    ![Ny länkad tjänst](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)

3. Ersätt `<accountname>` och `<accountkey>`* med namnet på ditt Azure Storage-konto och dess nyckel. 

    ![Länkad Azure Storage-tjänst](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Spara filen **AzureStorageLinkedService1.json**.

    Läs mer om JSON-egenskaper i den länkade tjänstdefinitionen i artikeln [Anslutningsapp för Azure Blob Storage](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-the-azure-sql-linked-service"></a>Skapa den länkade Azure SQL-tjänsten

1. Högerklicka på noden **Länkade tjänster** i **Solution Explorer** igen, peka på **Lägg till** och klicka på **Nytt objekt**. 
2. Den här gången väljer du **Länkad Azure SQL-tjänst** och klickar på **Lägg till**. 
3. Ersätt, , `<servername>` `<databasename>` `<username@servername>` , och `<password>` med namnen på din server, databas, användar konto och lösen ord iAzureSqlLinkedService1.jspå fil.    
4. Spara filen **AzureSqlLinkedService1.json**. 

    Mer information om de här JSON-egenskaperna finns i [Anslutningsapp för Azure SQL Database](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Skapa datauppsättningar

I föregående steg skapade du länkade tjänster för att länka ditt Azure Storage-konto och Azure SQL Database till din data fabrik. I det här steget definierar du två datauppsättningar – InputDataset och OutputDataset – som visar in- och utdata som lagras i de datalager som refereras till av AzureStorageLinkedService1 och AzureSqlLinkedService1.

Den länkade Azure storage-tjänsten anger anslutningssträngen som Data Factory-tjänsten använder vid körning för att ansluta till ditt Azure storage-konto. Och en indatauppsättning anger vilken blobcontainer och mapp som innehåller indata.  

På samma sätt anger den länkade tjänsten Azure SQL Database den anslutnings sträng som Data Factory tjänsten använder vid körning för att ansluta till Azure SQL Database. Och utdatauppsättningen (OutputDataset) för SQL-tabellen anger tabellen i databasen som data kopieras till från blob-lagringen. 

### <a name="create-input-dataset"></a>Skapa indatauppsättning

I det här steget skapar du en datauppsättning med namnet InputDataset som pekar på en blobfil (emp.ext) i rotmappen i en blobcontainer (adftutorial) i Azure Storage som representeras av den länkade tjänsten AzureStorageLinkedService1. Om du inte anger ett värde för filnamnet (eller hoppar över det), kommer data från alla blobbar i indatamappen att kopieras till målet. I den här kursen anger du ett värde för filnamnet. 

Här kan använda du termen ”tabeller” i stället för ”datauppsättningar”. En tabell är en rektangulär datauppsättning och är den enda typen av datauppsättning som stöds för tillfället. 

1. Högerklicka på **Tabeller** i **Solution Explorer**, peka på **Lägg till** och klicka på **Nytt objekt**.
2. I dialogrutan **Lägg till nytt objekt** väljer du **Azure-blobb** och klickar på **Lägg till**.   
3. Ersätt texten JSON med följande text och spara filen **AzureBlobLocation1.json**. 

   ```json   
   {
    "name": "InputDataset",
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
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
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
    | typ | Egenskapen Type anges till **AzureBlob** eftersom data finns i anAzure Blob Storage. |
    | linkedServiceName | Refererar till den **AzureStorageLinkedService** som youcreated tidigare. |
    | folderPath | Anger BLOB- **behållaren** och **mappens** thatcontains-blobar. I den här självstudien är adftutorial BLOB container andfolder är rotmappen. | 
    | fileName | Den här egenskapen är valfri. Om du utelämnar den här egenskapen plockas alla filesfrom som folderPath. I den här självstudien anges **emp.txt** för fil namnet, så att bara den filen hämtas för bearbetning. |
    | format -> typ |Indatafilen är i text format, så vi använder text **form**. |
    | columnDelimiter | Kolumnerna i indatafilen avgränsas av **commacharacter ( `,` )**. |
    | frekvens/intervall | Frekvensen är inställd på **timme** och intervall är setto **1**, vilket innebär att de angivna sektorerna är tillgängliga **varje timme**. I otherwords söker Data Factorys tjänsten efter indata varje timme i RootFolder för BLOB-containern (**adftutorial**) som du har angett. Den söker efter theData inom pipelinens start-och slut tider, inte före eller efter thesetimes.  |
    | extern | Den här egenskapen anges till **Sant** om data inte genereras bythis pipeline. Indata i den här självstudien finns i emp.txt-filen, som inte genereras av denna pipeline, så vi ställer in den här egenskapen på True. |

    Mer information om de här JSON-egenskaperna finns i artikeln [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md#dataset-properties).   

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata

I det här steget ska du skapa en utdatauppsättning med namnet **OutputDataset**. Den här data uppsättningen pekar på en SQL-tabell i Azure SQL Database representeras av **AzureSqlLinkedService1**. 

1. Högerklicka på **Tabeller** i **Solution Explorer** igen, peka på **Lägg till** och klicka på **Nytt objekt**.
2. I dialogrutan **Lägg till nytt objekt** väljer du **Azure SQL** och klickar på **Lägg till**. 
3. Ersätt texten JSON med följande JSON och spara filen **AzureSqlTableLocation1.json**.

    ```json
    {
        "name": "OutputDataset",
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
            "linkedServiceName": "AzureSqlLinkedService1",
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

## <a name="create-pipeline"></a>Skapa pipeline

I det här steget ska du skapa en pipeline med en **kopieringsaktivitet** som använder **InputDataset** som indata och **OutputDataset** som utdata.

Schemat styrs för närvarande av utdatamängd. I den här självstudiekursen är datamängden för utdata konfigurerad för att skapa ett segment en gång i timmen. Pipelinen har en starttid och sluttid som är en dag från varandra, vilket är 24 timmar. Därför produceras 24 segment för utdatauppsättningen av pipeline. 

1. Högerklicka på **Pipelines** i **Solution Explorer**, peka på **Lägg till** och klicka på **Nytt objekt**.  
2. Välj **Kopiera datapipeline** i dialogrutan **Lägg till nytt objekt** och klicka på **Lägg till**. 
3. Ersätt JSON med följande JSON och spara **CopyActivity1.jspå** filen.

   ```json
  {
   "name": "ADFTutorialPipeline",
   "properties": {
     "description": "Copy data from a blob to Azure SQL table",
     "activities": [
       {
         "name": "CopyFromBlobToSQL",
         "type": "Copy",
         "inputs": [
           {
             "name": "InputDataset"
           }
         ],
         "outputs": [
           {
             "name": "OutputDataset"
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
           "style": "StartOfInterval",
           "retry": 0,
           "timeout": "01:00:00"
         }
       }
     ],
     "start": "2017-05-11T00:00:00Z",
     "end": "2017-05-12T00:00:00Z",
     "isPaused": false
   }
  }
    ```   
   - I avsnittet Aktiviteter finns det bara en aktivitet vars **typ** anges till **Kopia**. Se artikeln [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) för information om kopieringsaktiviteten. I Data Factory-lösningar, kan du också använda [datatransformeringsaktiviteter](data-factory-data-transformation-activities.md).
   - Indata för aktiviteten är inställd på **InputDataset** och utdata för aktiviteten är inställd på **OutputDataset**. 
   - I avsnittet för **typeProperties** har **BlobSource** angetts som källtyp och **SqlSink** har angetts som mottagartyp. En fullständig lista över datakällor som stöds av kopieringsaktiviteten som källor och mottagare finns i [Datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Klicka på länken i tabellen om du vill veta hur du använder ett visst datalager som stöds som källa/mottagare.  
     
     Ersätt värdet i **start** egenskapen med den aktuella dagen och **slut** värdet med nästa dag. Du kan ange endast datumdelen och hoppa över tidsvärdet. Till exempel ”2016-02-03” som motsvarar ”2016-02-03T00:00:00Z”
     
     Både start- och slutdatum måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2016-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen. 
     
     Om du inte anger värdet för **slut** egenskapen, beräknas det som "**Start + 48 timmar**". Om du vill köra pipelinen på obestämd tid, anger du **9999-09-09** som värde för **slut** egenskapen.
     
     I det föregående exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.

     Beskrivningar av JSON-egenskaper i en pipeline-definition finns i artikeln [skapa pipelines](data-factory-create-pipelines.md). Beskrivningar av JSON-egenskaper i en kopieringsaktivitet-definition finns i artikeln [aktiviteter för dataflyttning](data-factory-data-movement-activities.md). Beskrivningar av JSON-egenskaper som stöds av BlobSource finns i artikeln [Azure Blob-anslutningsapp](data-factory-azure-blob-connector.md). Beskrivningar av JSON-egenskaper som stöds av SqlSink finns i artikeln [Azure SQL Database-anslutningsapp](data-factory-azure-sql-connector.md).

## <a name="publishdeploy-data-factory-entities"></a>Publicera/distribuera Data Factory-entiteter
I det här steget publicerar du Data Factory-enheter (länkade tjänster, datauppsättningar och pipeline) som du skapade tidigare. Du kan även ange namnet på en ny datafabrik som ska skapas för dessa enheter.  

1. I Solution Explorer högerklickar du på projektet och klickar sedan på **Publicera**. 
2. Om du ser dialogrutan **Logga in på ditt Microsoft-konto**, anger du dina autentiseringsuppgifter för det konto som har Azure-prenumerationen. Klicka sedan på **Logga in**.
3. Du bör se följande dialogruta:
   
   ![Dialogrutan Publicera](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. På sidan Konfigurera datafabrik går du igenom följande steg: 
   
   1. välj alternativet **Skapa ny Data Factory**.
   2. Ange **VSTutorialFactory** som **namn**.  
      
      > [!IMPORTANT]
      > Namnet på Azure Data Factory måste vara globalt unikt. Ändra namnet på datafabriken (till exempel yournameVSTutorialFactory) och försök att publicera igen, om du får ett felmeddelande om namnet på datafabriken när du publicerar. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.        
      > 
      > 
   3. Välj din Azure-prenumeration i fältet **Prenumeration**.
      
      > [!IMPORTANT]
      > Om du inte ser någon prenumeration kontrollerar du att du har loggat in med ett konto som är en administratör eller en medadministratör för prenumerationen.  
      > 
      > 
   4. Välj **resursgrupp** för datafabriken som ska skapas. 
   5. Välj **region** för datafabriken. Endast regioner som stöds av tjänsten Data Factory visas i listrutan.
   6. Klicka på **Nästa** för att växla till sidan **Publicera objekt**.
      
       ![Konfigurera Data Factory-sida](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. På sidan **Publicera objekt** kontrollerar du att alla datafabriksentiteter har valts. Klicka på **Nästa** för att växla till sidan **Sammanfattning**.
   
   ![Sidan Publish items (Publicera objekt)](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Granska sammanfattningen och klicka på **Nästa** för att starta distributionsprocessen och visa **Distributionsstatus**.
   
   ![Sidan Publish summary (Publicera översikt)](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. På sidan **Distributionsstatus** bör du se statusen för distributionen. Klicka på Slutför när distributionen är klar.
 
   ![Statussida för distribution](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Observera följande punkter: 

* Om du får felet: ”Den här prenumerationen har inte registrerats för användning av namnområdet Microsoft.DataFactory” gör du något av följande och försöker att publicera igen: 
  
  * I Azure PowerShell kör du följande kommando för att registrera Data Factory-providern. 

  ```powershell    
  Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
  Du kan köra följande kommando om du vill kontrollera att Data Factory-providern är registrerad. 
    
  ```powershell
  Get-AzResourceProvider
    ```
  * Logga in med Azure-prenumerationen i [Azure Portal](https://portal.azure.com) och navigera till ett Data Factory-blad (eller) skapa en datafabrik i Azure Portal. Med den här åtgärden registreras providern automatiskt.
* Namnet på datafabriken kan registreras som ett DNS-namn i framtiden och blir då synligt offentligt.

> [!IMPORTANT]
> Om du vill skapa Data Factory instanser måste du vara administratör/medadministratör för Azure-prenumerationen

## <a name="monitor-pipeline"></a>Övervaka pipeline
Gå till startsidan för din datafabrik:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Fler tjänster** i den vänstra menyn och på **Datafabriker**.

  ![Bläddra igenom datafabrikerna](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Börja skriva in namnet på din datafabrik.

  ![Namnet på datafabriken](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Klicka på din datafabrik i resultatlistan för att visa startsidan för din datafabrik.

  ![Datafabrikens startsida](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Se [Övervaka datauppsättningar och pipeline](data-factory-monitor-manage-pipelines.md) för instruktioner om hur övervakar en pipeline och datauppsättningar som du har skapat i den här självstudien. Visual Studio stöder för närvarande inte övervakning av Data Factory-pipelines. 

## <a name="summary"></a>Sammanfattning
I den här självstudien har du skapat en Azure-datafabrik för att kopiera data från en Azure-blob till Azure SQL Database. Du använde Visual Studio till att skapa datafabriken, länkade tjänster, datauppsättningar och en pipeline. Här är de avancerade steg som du utförde i självstudien:  

1. Du skapade en Azure **Data Factory**.
2. Du skapade **länkade tjänster**:
   1. En länkad **Azure Storage-** tjänst som länkar Azure Storage-kontot som innehåller indata.     
   2. En länkad **Azure SQL** -tjänst för att länka databasen som innehåller utdata. 
3. Du skapade **datauppsättningar** som beskriver indata och utdata för pipelines.
4. Du skapade en **pipeline** med en **kopieringsaktivitet** med **BlobSource** som källa och **SqlSink** som mottagare. 

Information om hur du använder en HDInsight Hive-aktivitet för att transformera data med hjälp av Azure HDInsight-kluster finns i [självstudie: utveckla din första pipeline för att transformera data med Hadoop-kluster](data-factory-build-your-first-pipeline.md).

Du kan länka två aktiviteter (köra en aktivitet efter en annan) genom att ställa in datauppsättningen för utdata för en aktivitet som den inkommande datauppsättningen för den andra aktiviteten. Mer detaljerad information finns i [Scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md) (Schemaläggning och utförande i Data Factory). 

## <a name="view-all-data-factories-in-server-explorer"></a>Visa datafabriker i Server Explorer
Det här avsnittet beskrivs hur du använder Server Explorer i Visual Studio för att visa alla datafabriker i din Azure-prenumeration och skapa ett Visual Studio-projekt utifrån en befintlig datafabrik. 

1. I **Visual Studio** klickar du på **Visa** i menyn och därefter på **Server Explorer**.
2. I fönstret Server Explorer expanderar du **Azure** och **Data Factory**. Om du ser **Logga in till Visual Studio** anger du det **konto** som är associerat med din Azure-prenumeration och klickar på **Fortsätt**. Ange **lösenordet** och klicka på **Logga in**. Visual Studio försöker hämta information om alla Azure-datafabriker i din prenumeration. Du ser statusen för den här åtgärden i fönstret **Uppgiftslista för Data Factory**.

    ![Server Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Skapa ett Visual Studio-projekt för en befintlig datafabrik

- Högerklicka på en datafabrik i Server Explorer och välj **Exportera Data Factory till nytt projekt** om du vill skapa ett Visual Studio-projekt som baseras på en befintlig datafabrik.

  ![Exportera datafabrik till ett VS-projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

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
              "value":  "Server=tcp:<logical SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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


## <a name="next-steps"></a>Nästa steg
I den här självstudien använde du Azure Blob Storage som käll data lager och Azure SQL Database som mål data lager i en kopierings åtgärd. Följande tabell innehåller en lista över datalager som stöds som källor och mål av kopieringsaktiviteten: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

För mer information om hur du kopierar data till/från ett datalager klickar du på länken för datalagret i tabellen.
