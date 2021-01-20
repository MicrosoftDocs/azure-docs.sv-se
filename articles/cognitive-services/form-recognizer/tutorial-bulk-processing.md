---
title: 'Självstudie: extrahera formulär data i bulk med hjälp av Azure Data Factory-formulär tolken'
titleSuffix: Azure Cognitive Services
description: Konfigurera Azure Data Factory aktiviteter för att utlösa utbildning och körning av formulär igenkännings modeller för att digitalisera en stor efter släpning av dokument.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 1780aebc113fa68a9a89cfce9fd67c9b5911fc58
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606075"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>Självstudie: extrahera formulär data i bulk med Azure Data Factory

I den här självstudien ska vi titta på hur du använder Azure-tjänster för att mata in en stor batch med formulär i digitala media. I den här självstudien visas hur du automatiserar data inmatningen från en Azure Data Lake av dokument till en Azure SQL-databas. Du kan snabbt träna modeller och bearbeta nya dokument med några få klick.

## <a name="business-need"></a>Affärs behov

De flesta organisationer är nu medvetna om hur värdefull data de har i olika format (PDF, bilder, videor). De söker efter bästa praxis och de mest kostnads effektiva sätten att digitalisera dessa till gångar.

Dessutom har våra kunder ofta olika typer av formulär som kommer från sina många klienter och kunder. Till skillnad från [snabb starterna](./quickstarts/client-library.md)visar den här självstudien hur du automatiskt tränar en modell med nya och olika typer av formulär med hjälp av en metadata driven metod. Om du inte har en befintlig modell för den aktuella formulär typen skapar systemet en åt dig och ger dig modell-ID. 

Genom att extrahera data från formulär och kombinera dem med befintliga operativa system och informations lager, kan företag få insikter och leverera mervärde till kunder och affärs användare.

Med Azures formulär igenkänning kan vi hjälpa organisationer att utnyttja sina data, automatisera processer (faktura betalningar, skatte bearbetning osv.), Spara pengar och tid och få bättre data exakthet.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Konfigurera Azure Data Lake för att lagra dina formulär
> * Använd en Azure Database för att skapa en parametrization-tabell
> * Använd Azure Key Vault för att lagra känsliga autentiseringsuppgifter
> * Träna din formulär igenkännings modell i en Databricks Notebook
> * Extrahera formulär data med en Databricks Notebook
> * Automatisera formulär träning och extrahering med Azure Data Factory

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats väljer **du gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En uppsättning minst fem formulär av samma typ. Det bästa är att det här arbets flödet är avsett att stödja stora uppsättningar dokument. Se [skapa en tränings data uppsättning](./build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här självstudien kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2128080).

## <a name="project-architecture"></a>Projekt arkitektur 

Det här projektet är en uppsättning Azure Data Factory pipelines för att utlösa python-anteckningsböcker som tränar, analyserar och extraherar data från dokument i ett Azure Data Lake lagrings konto.

Formulär tolkens REST-AP kräver vissa parametrar som inmatade. Av säkerhets skäl lagras vissa av dessa parametrar i en Azure Key Vault, medan andra mindre känsliga parametrar, till exempel lagrings-blobens mappnamn, lagras i en Parameterisering-tabell i en Azure SQL-databas.

Data tekniker eller data experter fyller i en rad i parameter tabellen för typ av formulär som ska analyseras. Sedan använder de Azure Data Factory för att iterera över listan över identifierade formulär typer och skicka relevanta parametrar till en Databricks Notebook för att träna eller träna formulär igenkännings modeller. En Azure-funktion kan också användas här.

Azure Databricks Notebook använder sedan de tränade modellerna för att extrahera formulär data och det datumet exporteras till en Azure SQL-databas.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="projekt arkitektur":::


## <a name="set-up-azure-data-lake"></a>Konfigurera Azure Data Lake

Din efter släpning av formulär kan finnas i din lokala miljö eller på en (s) FTP-server. I den här självstudien används formulär i ett Azure Data Lake gen 2-lagrings konto. Du kan överföra filer där du använder Azure Data Factory, Azure Storage Explorer eller AzCopy. Data uppsättningarna utbildning och Poäng kan finnas i olika behållare, men inlärnings data uppsättningarna för alla formulär typer måste finnas i samma behållare (även om de kan finnas i olika mappar).

Om du vill skapa en ny Data Lake följer du instruktionerna i [skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/create-data-lake-storage-account).

## <a name="create-a-parameterization-table"></a>Skapa en Parameterisering-tabell

Nu ska vi skapa en metadata-tabell i en Azure SQL Database. Den här tabellen innehåller icke-känsliga data som krävs av formulär tolken REST API. När det finns en ny typ av formulär i vår data uppsättning, infogar vi en ny post i den här tabellen och utlöser en pipeline för utbildning och poäng (som ska implementeras senare).

Följande fält kommer att användas i tabellen:

* **form_description**: det här fältet krävs inte som en del av utbildningen. Den innehåller en beskrivning av typen av formulär som vi tränar modellen för (till exempel "klient A-formulär", "hotell B Forms").
* **training_container_name**: det här fältet är namnet på lagrings konto behållaren där vi har lagrat inlärnings data uppsättningen. Det kan vara samma behållare som **scoring_container_name**.
* **training_blob_root_folder**: mappen i lagrings kontot där vi lagrar filerna för modellens utbildning.
* **scoring_container_name**: det här fältet är namnet på den lagrings konto behållare där vi har lagrat de filer som du vill extrahera nyckel värdes paren från. Det kan vara samma behållare som **training_container_name**.
* **scoring_input_blob_folder**: mappen på lagrings kontot där vi lagrar filerna för att extrahera data från.
* **model_id**: ID för den modell som vi vill omträna. För den första körningen måste värdet vara-1, vilket gör att en utbildnings antecknings bok kan skapa en ny anpassad modell att träna. Den utbildnings antecknings boken returnerar det nyligen skapade modell-ID: t till Azure Data Factory-instansen och, med hjälp av en lagrad procedur aktivitet, uppdaterar vi det här värdet i Azure SQL-databasen.

  När du vill mata in en ny formulär typ måste du manuellt återställa modell-ID: t-1 innan du tränar modellen.

* **file_type**: formulär typerna som stöds är `application/pdf` , `image/jpeg` , `image/png` och `image/tif` .

  Om du har formulär av olika filtyper måste du ändra det här värdet och **model_id** när du tränar en ny formulär typ.
* **form_batch_group_id**: över tid kan du ha flera formulär typer som du tränar mot samma modell. Med **form_batch_group_id** kan du ange alla formulär typer som har varit utbildning med en speciell modell.

### <a name="create-the-table"></a>Skapa tabellen

[Skapa en Azure SQL Database](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)och kör sedan följande SQL-skript i [Frågeredigeraren](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal) för att skapa den nödvändiga tabellen.

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Kör följande skript för att skapa proceduren för att automatiskt uppdatera **model_id** när den har tränats.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Använd Azure Key Vault för att lagra känsliga autentiseringsuppgifter

Av säkerhets skäl vill vi inte lagra viss känslig information i Parameterisering-tabellen i Azure SQL-databasen. Vi lagrar känsliga parametrar som Azure Key Vault hemligheter.

### <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

[Skapa en Key Vault-resurs](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Gå sedan till Key Vault resursen när den har skapats och välj **hemligheter** i avsnittet **Inställningar** för att lägga till parametrarna.

Ett nytt fönster visas, Välj **generera/importera**. Ange namnet på parametern och dess värde och klicka på Skapa. Gör detta för följande parametrar:

* **CognitiveServiceEndpoint**: slut punkts-URL: en för formulärets tolknings-API.
* **CognitiveServiceSubscriptionKey**: åtkomst nyckeln för formulär igenkännings tjänsten. 
* **StorageAccountName**: det lagrings konto där träning-datauppsättningen och formulären som vi vill extrahera nyckel/värde-par från lagras. Om de finns i olika konton anger du var och en av deras konto namn som separata hemligheter. Kom ihåg att träning-datauppsättningarna måste finnas i samma behållare för alla formulär typer, men de kan finnas i olika mappar.
* **StorageAccountSasKey**: signaturen för delad åtkomst (SAS) för lagrings kontot. Hämta SAS-URL: en genom att gå till lagrings resursen och välja fliken **Storage Explorer** . Navigera till din behållare, högerklicka och välj **Hämta signatur för delad åtkomst**. Det är viktigt att hämta SAS för din behållare, inte för själva lagrings kontot. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och klicka på **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Det bör ha formatet: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Träna din formulär igenkännings modell i en Databricks Notebook

Du använder Azure Databricks för att lagra och köra python-koden som samverkar med formulär tolknings tjänsten.

### <a name="create-a-notebook-in-databricks"></a>Skapa en antecknings bok i Databricks

[Skapa en Azure Databricks resurs](https://ms.portal.azure.com/#create/Microsoft.Databricks) i Azure Portal. Navigera till resursen när den har skapats och starta arbets ytan.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Skapa en hemlig omfattning som backas upp av Azure Key Vault

Om du vill referera till hemligheterna i Azure Key Vault vi skapade ovan måste du skapa en hemlig omfattning i Databricks. Följ stegen i [skapa ett hemligt Azure Key Vault hemligt omfång](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Skapa ett Databricks-kluster

Ett kluster är en samling av Databricks-beräknings resurser. Så här skapar du ett kluster:

1. Klicka på knappen **kluster** i sid panelen.
1. På sidan **kluster** klickar du på **skapa kluster**.
1. På sidan **skapa kluster** anger du ett kluster namn och väljer **7,2 (Scala 2,12, Spark 3.0.0)** i list rutan Databricks runtime version.
1. Klicka på **Create Cluster** (Skapa kluster).

### <a name="write-a-settings-notebook"></a>Skriv en antecknings bok för inställningar

Nu är du redo att lägga till python-anteckningsböcker. Börja med att skapa en antecknings bok med namnet **Inställningar**. den här antecknings boken kommer att tilldela värdena i Parameterisering-tabellen till variabler i skriptet. Värdena kommer senare att skickas in som parametrar genom att Azure Data Factory. Vi tilldelar också värden från hemligheterna i Key Vault till variabler. 

1. Om du vill skapa antecknings boken för **Inställningar** klickar du på knappen **arbets yta** i den nya fliken, klickar på list rutan och väljer **skapa** och sedan **Notebook**.
1. I popup-fönstret anger du det namn som du vill ge antecknings boken och väljer **python** som standard språk. Välj ditt Databricks-kluster och välj **skapa**.
1. I den första notebook-cellen hämtar vi de parametrar som skickas av Azure Data Factory.

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. I den andra cellen hämtar vi hemligheter från Key Vault och tilldelar dem till variabler.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Skriv en utbildnings antecknings bok

Nu när du har slutfört **inställningarna** för Notebook, kan vi skapa en bärbar dator för att träna modellen. Som vi nämnt ovan kommer vi att använda filer som lagras i en mapp i ett Azure Data Lake gen 2 lagrings konto (**training_blob_root_folder**). Mappnamnet har skickats in som en variabel. Varje uppsättning formulär typer kommer att finnas i samma mapp och när vi loopar över parameter tabellen tränar vi modellen med alla formulär typerna. 

1. Skapa en ny antecknings bok med namnet **TrainFormRecognizer**. 
1. I den första cellen kör du inställnings antecknings boken:

    ```python
    %run "./Settings"
    ```

1. I nästa cell tilldelar du variabler från **inställnings** filen och tränar modellen dynamiskt för varje formulär typ, och använder koden i [rest-startsnabbet](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. Det sista steget i övnings processen är att hämta inlärnings resultatet i JSON-format.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>Extrahera formulär data med en bärbar dator

### <a name="mount-the-azure-data-lake-storage"></a>Montera Azure Data Lake lagring

Nästa steg är att räkna med de olika formulär som vi har använt den tränade modellen. Vi monterar Azure Data Lake lagrings kontot i Databricks och refererar till monteringen under inmatnings processen.

Precis som i övnings skedet använder vi Azure Data Factory för att anropa extraheringen av nyckel/värde-par från formulären. Vi går igenom formulären i de mappar som anges i parameter tabellen.

1. Nu ska vi skapa antecknings boken för att montera lagrings kontot i Databricks. Vi kallar IT- **MountDataLake**. 
1. Du måste anropa **inställningarna** för den bärbara datorn först:

    ```python
    %run "./Settings"
    ```

1. I den andra cellen definierar vi variabler för de känsliga parametrarna, som vi hämtar från våra Key Vault hemligheter.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Nu ska vi försöka demontera lagrings kontot om det var tidigare monterat.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Slutligen monterar vi lagrings kontot.


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > Vi har bara monterat utbildnings lagrings kontot &mdash; i det här fallet, träna-filerna och de filer som vi vill extrahera nyckel/värde-par från finns i samma lagrings konto. Om beräknings-och utbildnings lagrings kontona är olika måste du montera båda lagrings kontona här. 

### <a name="write-the-scoring-notebook"></a>Skriv bedömnings antecknings boken

Nu kan vi skapa en bedömnings antecknings bok. På samma sätt som den bärbara datorn för utbildning kommer vi att använda filer som lagras i mappar i Azure Data Lake lagrings konto som vi precis har monterat. Mappnamnet skickas som en variabel. Vi går igenom alla formulär i den angivna mappen och extraherar nyckel-värdeparen från dem. 

1. Skapa en ny antecknings bok och anropa IT- **ScoreFormRecognizer**. 
1. Kör **inställningarna** och **MountDataLake** Notebooks.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Lägg sedan till följande kod som anropar [analys](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) -API: et.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. I nästa cell får du resultatet från extraheringen av nyckel/värde-par. Resultatet visas i den här cellen. Eftersom vi vill att resultatet i JSON-format ska bearbetas i Azure SQL Database eller Cosmos DB, skriver vi resultatet till en. JSON-fil. Namnet på utdatafilen blir namnet på den returnerade filen, sammanfogat med "_output.jspå". Filen kommer att lagras i samma mapp som käll filen.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Utför fil skrivnings proceduren i en ny cell:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Automatisera utbildning och poäng med Azure Data Factory

Det enda återstående steget är att konfigurera tjänsten Azure Data Factory (ADF) för att automatisera inlärnings-och Poäng processerna. Börja med att följa stegen under [skapa en data fabrik](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). När du har skapat en ADF-resurs måste du skapa tre pipelines: en för utbildning och två för poängsättning (förklaras nedan).

### <a name="training-pipeline"></a>Utbildnings pipeline

Den första aktiviteten i utbildnings pipelinen är en sökning för att läsa och returnera värdena i Parameterisering-tabellen i Azure SQL-databasen. Eftersom alla data uppsättningar för träning kommer att finnas i samma lagrings konto och behållare (men eventuellt olika mappar) behåller vi standardvärdet för attributet **endast första raden** i Sök aktivitets inställningarna. För varje typ av formulär att träna modellen mot, tränar vi modellen med alla filer i **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="utbildnings pipeline i Data Factory":::

Den lagrade proceduren tar två parametrar: **model_id** och **form_batch_group_id**. Koden för att returnera modell-ID från Databricks Notebook är `dbutils.notebook.exit(model_id)` och koden för att läsa koden i den lagrade procedur aktiviteten i Data Factory är `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Poäng förlopp

För att extrahera nyckel/värde-par kommer vi att söka igenom alla mappar i Parameterisering-tabellen och, för varje mapp, extrahera nyckel/värde-par för alla filer i den. Från och med idag stöder ADF inte kapslade slingor. Istället ska vi skapa två pipeliner. Den första pipelinen kommer att göra sökningen från tabellen parameterisering och skicka mapplistan som en parameter till den andra pipelinen.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="första bedömnings pipelinen i Data Factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="första Poäng pipelinen i Data Factory, information":::

Den andra pipelinen använder en GetMeta-aktivitet för att hämta listan över filer i mappen och skicka den som en parameter till bedömnings Databricks Notebook.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="pipeline för andra poäng i Data Factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="pipeline för andra poäng i Data Factory, information":::

### <a name="specify-a-degree-of-parallelism"></a>Ange en grad av parallellitet

I båda pipelinen för utbildning och Poäng kan du ange graden av parallellitet för att bearbeta flera formulär samtidigt.

Så här anger du graden av parallellitet i ADF-pipeline:

* Välj aktiviteten för aktiviteter.
* Avmarkera den **sekventiella** rutan.
* Ange graden av parallellitet i text rutan **batch-antal** . Vi rekommenderar ett maximalt antal batchar på 15 för poängen.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="konfiguration av parallellitet för poängsättnings aktivitet i ADF":::

## <a name="how-to-use"></a>Använd så här

Nu har du en automatiserad pipeline för att digitalisera din efter släpning av formulär och köra lite analys ovanpå den. När du lägger till nya formulär av en välbekant typ i en befintlig lagringsmapp, behöver du bara köra poängsättnings-pipelinen igen och de kommer att uppdatera alla dina utdatafiler, inklusive utdatafiler för de nya formulären. 

Om du lägger till nya former av en ny typ måste du också överföra en data uppsättning för utbildning till lämplig behållare. Lägg sedan till en ny rad i Parameterisering-tabellen och ange platserna för de nya dokumenten och deras utbildnings data uppsättning. Ange värdet-1 för **model_ID** för att indikera att en ny modell måste tränas för dessa formulär. Kör sedan utbildnings pipelinen i ADF. Den kommer att läsas från tabellen, träna en modell och skriva över modell-ID: t i tabellen. Sedan kan du anropa Poäng förloppet för att börja skriva utdatafilerna.

## <a name="next-steps"></a>Nästa steg

I den här självstudien ställer du in Azure Data Factory pipelines för att utlösa utbildning och körning av formulär igenkännings modeller för att digitalisera en stor efter släpning av filer. Nu ska du utforska formulärets tolknings-API för att se vad mer du kan göra med det.

* [Formulär igenkännings REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
