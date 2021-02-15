---
title: Flytta data – Data Management Gateway
description: Konfigurera en datagateway för att flytta data mellan lokala platser och molnet. Använd Data Management Gateway i Azure Data Factory för att flytta dina data.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
robots: noindex
ms.openlocfilehash: 53fce1744ccbf4289b2415e926e084c90d708a13
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380295"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Flytta data mellan lokala källor och molnet med Data Management Gateway
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [Kopiera data mellan lokalt och molnet med Data Factory](../tutorial-hybrid-copy-powershell.md).

Den här artikeln innehåller en översikt över data integrering mellan lokala data lager och moln data lager med hjälp av Data Factory. Den bygger på artikeln [data förflyttnings aktiviteter](data-factory-data-movement-activities.md) och andra data Factory Core-koncept artiklar: [data uppsättningar](data-factory-create-datasets.md) och [pipeliner](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Gateway för datahantering
Du måste installera Data Management Gateway på den lokala datorn för att kunna flytta data till/från ett lokalt data lager. Gatewayen kan installeras på samma dator som data lagret eller på en annan dator så länge som gatewayen kan ansluta till data lagret.

> [!IMPORTANT]
> Se [Data Management Gateway](data-factory-data-management-gateway.md) artikel för information om data Management Gateway.

Följande genom gång visar hur du skapar en data fabrik med en pipeline som flyttar data från en lokal **SQL Server** -databas till en Azure Blob Storage. Som en del av den här genomgången kommer du att installera och konfigurera Data Management Gateway på din dator.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Genom gång: Kopiera lokala data till molnet
I den här genom gången utför du följande steg:

1. Skapa en datafabrik.
2. Skapa en data Management Gateway.
3. Skapa länkade tjänster för käll-och mottagar data lager.
4. Skapa data uppsättningar som representerar indata och utdata.
5. Skapa en pipeline med en kopieringsaktivitet för att flytta data.

## <a name="prerequisites-for-the-tutorial"></a>Krav för självstudien
Innan du påbörjar den här genom gången måste du ha följande krav:

* **Azure-prenumeration**.  Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto på ett par minuter. Mer information finns i artikeln om [kostnads fri utvärdering](https://azure.microsoft.com/pricing/free-trial/) .
* **Azure Storage konto**. Du använder Blob Storage som data lager för **destination/mottagare** i den här självstudien. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).
* **SQL Server**. Du använder en SQL Server databas som **käll** data lager i den här självstudien.

## <a name="create-data-factory"></a>Skapa en datafabrik
I det här steget använder du Azure Portal för att skapa en Azure Data Factory-instans med namnet **ADFTutorialOnPremDF**.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapa en resurs**, klicka på **information + analys** och klicka på **Data Factory**.

   ![Nytt->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. På sidan **ny data fabrik** anger du **ADFTutorialOnPremDF** som namn.

    ![Lägg till i Start Sidan](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: **data fabriks namnet "ADFTutorialOnPremDF" är inte tillgängligt**, ändrar du namnet på data fabriken (till exempel yournameADFTutorialOnPremDF) och försöker skapa igen. Använd det här namnet i stället för ADFTutorialOnPremDF medan du utför återstående steg i den här självstudien.
   >
   > Namnet på data fabriken kan registreras som ett **DNS-** namn i framtiden och blir därför synligt offentligt.
   >
   >
4. Välj den **Azure-prenumeration** där du vill att datafabriken ska skapas.
5. Välj befintlig **resursgrupp** eller skapa en resursgrupp. För självstudien skapar du en resurs grupp med namnet: **ADFTutorialResourceGroup**.
6. Klicka på **skapa** på sidan **ny data fabrik** .

   > [!IMPORTANT]
   > Om du vill skapa Data Factory-instanser måste du vara medlem i [Data Factory-deltagarrollen](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på gruppnivå/resursgrupp.
   >
   >
7. När du har skapat filen visas **Data Factory** sidan som visas i följande bild:

   ![Data Factory start sida](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Skapa Gateway
1. På sidan **Data Factory** klickar du på **Redigera och distribuerar** panel för att starta **redigeraren** för data fabriken.

    ![Ikonen Författare och distribution](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. I Data Factory redigeraren klickar du på **... Mer** information i verktygsfältet och klicka sedan på **ny datagateway**. Du kan också högerklicka på **Datagateways** i trädvyn och klicka på **ny data Gateway**.

   ![Ny datagateway i verktygsfältet](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. På sidan **skapa** anger du **adftutorialgateway** som **namn** och klickar på **OK**.     

    ![Sidan skapa Gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > I den här genom gången skapar du den logiska gatewayen med bara en nod (lokal Windows-dator). Du kan skala ut en gateway för data hantering genom att associera flera lokala datorer med gatewayen. Du kan skala upp genom att öka antalet data förflyttnings jobb som kan köras samtidigt på en nod. Den här funktionen är också tillgänglig för en logisk Gateway med en enda nod. Mer information finns i avsnittet [skala data Management Gateway i Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artikeln.  
4. På sidan **Konfigurera** klickar du på **installera direkt på den här datorn**. Den här åtgärden hämtar installations paketet för gatewayen, installerar, konfigurerar och registrerar gatewayen på datorn.  

   > [!NOTE]
   > Använd Internet Explorer eller en Microsoft ClickOnce-kompatibel webbläsare.
   >
   > Om du använder Chrome går du till [Chrome-webbarkivet](https://chrome.google.com/webstore/), söker med nyckelordet "ClickOnce", väljer ett av ClickOnce-tilläggen och installerar det.
   >
   > Gör samma sak för Firefox (installera tillägg). Klicka på knappen **Öppna meny** i verktygsfältet (**tre horisontella linjer** i det övre högra hörnet), klicka på **tillägg**, Sök med nyckelordet "ClickOnce", Välj ett av ClickOnce-tilläggen och installera det.    
   >
   >

    ![Gateway – konfigurera sida](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    På det här sättet är det enklaste sättet att ladda ned, installera, konfigurera och registrera gatewayen i ett enda steg. Du kan se **Microsoft Data Management Gateway Configuration Manager** -programmet är installerat på datorn. Du kan också hitta den körbara **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft datahantering Gateway\2.0\Shared**.

    Du kan också hämta och installera Gateway manuellt genom att använda länkarna på den här sidan och registrera den med hjälp av den nyckel som visas i text rutan **ny nyckel** .

    Se [Data Management Gateway](data-factory-data-management-gateway.md) artikel för all information om gatewayen.

   > [!NOTE]
   > Du måste vara administratör på den lokala datorn för att kunna installera och konfigurera Data Management Gateway har lyckats. Du kan lägga till ytterligare användare i den lokala Windows-gruppen **Data Management Gateway användare** . Medlemmarna i den här gruppen kan använda Data Management Gateway Configuration Manager verktyget för att konfigurera gatewayen.
   >
   >
5. Vänta några minuter eller vänta tills du ser följande meddelande:

    ![Gateway-installationen lyckades](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Starta **Data Management Gateway Configuration Manager** program på datorn. I fönstret **Sök** skriver du **Data Management Gateway** för att komma åt det här verktyget. Du kan också hitta den körbara **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft datahantering Gateway\2.0\Shared**

    ![Gateway-Configuration Manager](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Bekräfta att du ser `adftutorialgateway is connected to the cloud service` meddelandet. Statusfältet längst ned visas **som är anslutna till moln tjänsten** tillsammans med en **grön bock markering**.

    På fliken **Start** kan du också utföra följande åtgärder:

   * **Registrera** en gateway med en nyckel från Azure Portal med hjälp av knappen Registrera.
   * **Stoppa** data Management Gateway värd tjänsten som körs på din Gateway-dator.
   * **Schemalägg uppdateringar** som ska installeras vid en angiven tidpunkt på dagen.
   * Visa när gatewayen **senast uppdaterades**.
   * Ange vid vilken tidpunkt som en uppdatering av gatewayen kan installeras.
8. Växla till fliken **Inställningar** . Certifikatet som anges i avsnittet **certifikat** används för att kryptera/dekryptera autentiseringsuppgifter för det lokala data lager som du anger på portalen. valfritt Klicka på **ändra** om du vill använda ditt eget certifikat i stället. Som standard använder gatewayen det certifikat som genereras automatiskt av tjänsten Data Factory.

    ![Konfiguration av Gateway-certifikat](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Du kan också utföra följande åtgärder på fliken **Inställningar** :

   * Visa eller exportera certifikatet som används av gatewayen.
   * Ändra den HTTPS-slutpunkt som används av gatewayen.    
   * Ange en HTTP-proxy som ska användas av gatewayen.     
9. valfritt Växla till fliken **diagnostik** , markera alternativet **Aktivera utförlig loggning** om du vill aktivera utförlig loggning som du kan använda för att felsöka eventuella problem med gatewayen. Du hittar loggnings informationen i **Loggboken** under noden **program-och tjänst loggar**  ->  **Data Management Gateway** .

    ![Fliken Diagnostik](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Du kan också utföra följande åtgärder på fliken **diagnostik** :

   * Använd avsnittet **Testa anslutning** till en lokal data källa med hjälp av gatewayen.
   * Klicka på **Visa loggar** om du vill se data Management Gateway loggen i ett Loggbokens fönster.
   * Klicka på **skicka loggar** för att ladda upp en zip-fil med loggar av de senaste sju dagarna till Microsoft för att under lätta fel sökning av problem.
10. På fliken **diagnostik** i avsnittet **Testa anslutning** väljer du **SQLServer** för typen av data lager. Ange namnet på databas servern, namnet på databasen, ange autentiseringstyp, Ange användar namn och lösen ord och klicka på **testa** för att testa om gatewayen kan ansluta till databasen.
11. Växla till webbläsaren och klicka på **OK** i **Azure Portal** på sidan **Konfigurera** och på sidan **ny data Gateway** .
12. Du bör se **adftutorialgateway** under **datagateways** i trädvyn till vänster.  Om du klickar på den bör du se tillhör ande JSON.

## <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster: **AzureStorageLinkedService** och **SqlServerLinkedService**. **SqlServerLinkedService** länkar en SQL Server-databas och den länkade **AzureStorageLinkedService** -tjänsten länkar ett Azure Blob-lager till data fabriken. Du skapar en pipeline senare i den här genom gången som kopierar data från SQL Server-databasen till Azure Blob Store.

#### <a name="add-a-linked-service-to-a-sql-server-database"></a>Lägg till en länkad tjänst i en SQL Server databas
1. I **Data Factory redigeraren** klickar du på **nytt data lager** i verktygsfältet och väljer **SQL Server**.

   ![Ny SQL Server länkad tjänst](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. I **JSON-redigeraren** till höger utför du följande steg:

   1. För **gatewayName** anger du **adftutorialgateway**.    
   2. Utför följande steg i **ConnectionString**:    

      1. För **Server Namn anger** du namnet på den server som är värd för SQL Server databasen.
      2. För **databasename** anger du namnet på databasen.
      3. Klicka på knappen **kryptera** i verktygsfältet. Du ser programmet för hantering av autentiseringsuppgifter.

         ![Autentiseringshanteraren-program](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. I dialog rutan **ange autentiseringsuppgifter** anger du autentiseringstyp, användar namn och lösen ord och klickar på **OK**. Om anslutningen lyckas lagras de krypterade autentiseringsuppgifterna i JSON och dialog rutan stängs.
      5. Stäng den tomma webb läsar fliken som startade dialog rutan om den inte automatiskt stängs och återgå till fliken med Azure Portal.

         På gateway-datorn **krypteras** dessa autentiseringsuppgifter med hjälp av ett certifikat som tjänsten Data Factory äger. Om du vill använda det certifikat som är kopplat till Data Management Gateway i stället, se ange autentiseringsuppgifter på ett säkert sätt.    
   3. Klicka på **distribuera** i kommando fältet för att distribuera den länkade tjänsten SQL Server. Du bör se den länkade tjänsten i trädvyn.

      ![SQL Server länkad tjänst i trädvyn](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Lägg till en länkad tjänst för ett Azure Storage-konto
1. I **Data Factory redigeraren** klickar du på **nytt data lager** i kommando fältet och sedan på **Azure Storage**.
2. Ange namnet på ditt Azure Storage-konto för **konto namnet**.
3. Ange nyckeln för ditt Azure Storage-konto för **konto nyckeln**.
4. Klicka på **distribuera** för att distribuera **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget kan du skapa indata och utdata datauppsättningar som representerar inkommande och utgående data för kopieringen (lokal SQL Server-databas = > Azure Blob Storage). Innan du skapar datauppsättningar gör du följande steg (detaljerade anvisningar kommer i listan):

* Skapa en tabell med namnet **emp** i SQL Server-databasen du la till som en länkad tjänst till datafabriken och infoga ett par exempelposter i tabellen.
* Skapa en blobcontainer med namnet **adftutorial** i Azure Blob Storage-kontot du la till som en länkad tjänst i datafabriken.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Förbered lokala SQL Server för självstudien
1. I databasen som du angav för SQL Server länkade tjänsten (**SqlServerLinkedService**) använder du följande SQL-skript för att skapa **EMP** -tabellen i-databasen.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Infoga vissa exempel i tabellen:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Skapa indatauppsättning

1. I **Data Factory redigeraren** klickar du på **... Mer**, klicka på **ny data uppsättning** i kommando fältet och klicka på **SQL Server tabell**.
2. Ersätt JSON i den högra rutan med följande text:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```       
   Observera följande punkter:

   * **typ** är inställd på **SqlServerTable**.
   * **TableName** har angetts till **EMP**.
   * **linkedServiceName** har angetts till **SqlServerLinkedService** (du skapade den här länkade tjänsten tidigare i den här genom gången.).
   * För en indata-datauppsättning som inte genereras av en annan pipeline i Azure Data Factory måste du ange **external** till **True**. Den anger att indata skapas utanför Azure Data Factorys tjänsten. Du kan också ange externa data principer med **extern Aldata** -elementet i **princip** avsnittet.    

   Mer information om JSON-egenskaper finns i [Flytta data till/från SQL Server](data-factory-sqlserver-connector.md) .
3. Distribuera data uppsättningen genom att klicka på **distribuera** i kommando fältet.  

### <a name="create-output-dataset"></a>Skapa datauppsättning för utdata

1. I **Data Factory redigeraren** klickar du på **ny data uppsättning** i kommando fältet och sedan på **Azure Blob Storage**.
2. Ersätt JSON i den högra rutan med följande text:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Observera följande punkter:

   * **typ** är inställd på **AzureBlob**.
   * **linkedServiceName** har angetts till **AzureStorageLinkedService** (du skapade den här länkade tjänsten i steg 2).
   * **folderPath** är inställt på **adftutorial/outfromonpremdf** där outfromonpremdf är mappen i adftutorial-behållaren. Skapa behållaren **adftutorial** om den inte redan finns.
   * **Tillgängligheten** anges till **varje timme** (**frekvens** inställd på **timme** och **intervall** inställd på **1**).  Data Factorys tjänsten genererar en utgående data sektor varje timme i tabellen **EMP** i Azure SQL Database.

   Om du inte anger ett **fil namn** för en **utdataparameter**, får de genererade filerna i **folderPath** namnet i följande format: `Data.<Guid>.txt` (till exempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Ange **folderPath** och **fileName** dynamiskt utifrån **SliceStart**-tiden med hjälp av partitionedBy-egenskapen. I följande exempel använder folderPath Year, Month och Day från SliceStart (starttiden för den sektor som bearbetas) och fileName använder Hour från SliceStart. Om exempelvis en sektor produceras 2014-10-20T08:00:00, anges folderName till wikidatagateway/wikisampledataout/2014/10/20 och fileName anges till 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Mer information om JSON-egenskaper finns i [Flytta data till/från Azure Blob Storage](data-factory-azure-blob-connector.md) .
3. Distribuera data uppsättningen genom att klicka på **distribuera** i kommando fältet. Bekräfta att du ser båda data uppsättningarna i trädvyn.  

## <a name="create-pipeline"></a>Skapa pipeline
I det här steget skapar du en **pipeline** med en **kopierings aktivitet** som använder **EmpOnPremSQLTable** som indata och **OutputBlobTable** som utdata.

1. I Data Factory redigeraren klickar du på **... Mer** och klicka på **ny pipeline**.
2. Ersätt JSON i den högra rutan med följande text:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
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
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Ersätt värdet i **start** egenskapen med den aktuella dagen och **slut** värdet med nästa dag.
   >
   >

   Observera följande punkter:

   * I avsnittet aktiviteter finns det bara en aktivitet vars **typ** är inställd på **Kopiera**.
   * **Indata** för aktiviteten är inställd på **EmpOnPremSQLTable** och **utdata** för aktiviteten är inställd på **OutputBlobTable**.
   * I avsnittet **typeProperties** anges **SqlSource** som typ av **källa** och **BlobSink** har angetts som **mottagar typ**.
   * SQL-fråga `select * from emp` har angetts för egenskapen **SqlReaderQuery** för **SqlSource**.

   Både start- och slutdatum måste vara i [ISO-format](https://en.wikipedia.org/wiki/ISO_8601). Exempel: 2014-10-14T16:32:41Z. **Sluttiden** är valfri, men vi använder den i den här självstudiekursen.

   Om du inte anger värdet för **slut** egenskapen, beräknas det som "**Start + 48 timmar**". Om du vill köra pipelinen på obestämd tid, anger du **9/9/9999** som värde för **slut** egenskapen.

   Du definierar hur länge data sektorerna ska bearbetas utifrån de **tillgänglighets** egenskaper som definierats för varje Azure Data Factory data uppsättning.

   I exemplet finns det 24 datasektorer eftersom varje datasektor skapas varje timme.        
3. Klicka på **distribuera** i kommando fältet för att distribuera data uppsättningen (tabellen är en rektangulär data uppsättning). Bekräfta att pipelinen visas i trädvyn under noden **pipelines** .  
4. Klicka nu på **X** två gånger för att stänga sidan för att återgå till **Data Factory** sidan för **ADFTutorialOnPremDF**.

**Grattis!** Du har skapat en Azure-datafabrik, länkade tjänster, data uppsättningar och en pipeline och schemalagt pipelinen.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Visa datafabriken i en diagramvy
1. I **Azure Portal** klickar du på **diagram** panelen på Start sidan för data fabriken **ADFTutorialOnPremDF** . :

    ![Diagram länk](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Du bör se ett diagram som liknar följande bild:

    ![Diagramvy](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Du kan zooma in, zooma ut, zooma till 100%, zooma för att passa, placera pipelines och data uppsättningar automatiskt och Visa härkomst information (markerar överordnade och underordnade objekt till markerade objekt).  Du kan dubbelklicka på ett objekt (data uppsättning för indata/utdata eller pipelinen) om du vill se egenskaper för det.

## <a name="monitor-pipeline"></a>Övervaka pipeline
I det här steget använder du Azure Portal för att övervaka vad som händer i en Azure Data Factory. Du kan också använda PowerShell-cmdletar för att övervaka datauppsättningar och pipelines. Mer information om övervakning finns i [övervaka och hantera pipeliner](data-factory-monitor-manage-pipelines.md).

1. I diagrammet dubbelklickar du på **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable-segment](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Observera att alla data sektorer är i **klart** läge eftersom pipeline-varaktigheten (start tid till slut tid) redan har passerat. Det beror också på att du har infogat data i SQL Server-databasen och att det är där som helst. Bekräfta att inga sektorer visas i avsnittet **problem sektorer** längst ned. Om du vill visa alla sektorer klickar du på **Visa mer** längst ned i listan över segment.
3. Nu klickar du på **OutputBlobTable** på sidan **data uppsättningar** .

    ![OputputBlobTable-segment](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klicka på valfri data sektor i listan så visas sidan **data sektor** . Du ser aktivitets körningar för sektorn. Du ser bara en aktivitets körning vanligt vis.  

    ![Bladet data sektor](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Om sektorn inte har statusen **Klar** kan du se sektorer uppströms som inte är klara och som blockerar aktuell sektor från att köras i listan **Sektorer uppströms som inte är klara**.
5. Klicka på **aktiviteten kör** i listan längst ned för att se **aktivitets körnings information**.

   ![Sidan körnings information för aktivitet](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Du kan se information som till exempel data flöde, varaktighet och den gateway som används för att överföra data.
6. Klicka på **X** för att stänga alla sidor tills du
7. gå tillbaka till start sidan för **ADFTutorialOnPremDF**.
8. valfritt Klicka på **pipeliner**, klicka på **ADFTutorialOnPremDF** och gå igenom inmatnings tabeller (**förbrukade**) eller utdata-datauppsättningar (**producerade**).
9. Använd verktyg som [Microsoft Storage Explorer](https://storageexplorer.com/) för att kontrol lera att en BLOB/fil skapas för varje timme.

   ![Azure Storage Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Nästa steg
* Se [Data Management Gateway](data-factory-data-management-gateway.md) artikel för all information om data Management Gateway.
* Se [Kopiera data från Azure blob till Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för att lära dig mer om hur du använder kopierings aktivitet för att flytta data från ett käll data lager till ett data lager för mottagare.
