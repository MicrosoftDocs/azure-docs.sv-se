---
title: 'Självstudie: extrahera formulär data i bulk med hjälp av Azure Data Factory-formulär tolken'
titleSuffix: Azure Cognitive Services
description: Konfigurera Azure Data Factory aktiviteter för att utlösa utbildning och körning av formulär igenkännings modeller och digitalisera en stor efter släpning av dokument.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: d0c95312e1794e2f78bbbef217ef5530a993146d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040914"
---
# <a name="tutorial-extract-form-data-in-bulk-by-using-azure-data-factory"></a>Självstudie: extrahera formulär data i bulk genom att använda Azure Data Factory

I den här självstudien ska vi titta på hur du använder Azure-tjänster för att mata in en stor batch med formulär i digitala media. I självstudien visas hur du automatiserar data inmatningen från en Azure Data Lake-dokument till en Azure SQL-databas. Du kan snabbt träna modeller och bearbeta nya dokument med några få klick.

## <a name="business-need"></a>Affärs behov

De flesta organisationer känner nu till värdet för de data de har i olika format (PDF, bilder, videor). De söker efter bästa praxis och de mest kostnads effektiva sätten att digitalisera dessa till gångar.

Våra kunder har dessutom ofta olika typer av formulär som kommer från sina många klienter och kunder. Till skillnad från [snabb starterna](./quickstarts/client-library.md)visar den här självstudien hur du automatiskt tränar en modell med nya och olika typer av formulär genom att använda en metadata driven metod. Om du inte har en befintlig modell för den aktuella formulär typen skapar systemet ett och ger dig modell-ID. 

Genom att extrahera data från formulär och kombinera dem med befintliga operativa system och informations lager, kan företag få insikter och leverera mervärde till kunder och affärs användare.

Azure formulär tolken hjälper organisationer att använda sina data, automatisera processer (faktura betalningar, skatte bearbetning osv.), Spara pengar och tid och få bättre data exakthet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Azure Data Lake för att lagra dina formulär.
> * Använd en Azure Database för att skapa en parametrization-tabell.
> * Använd Azure Key Vault för att lagra känsliga autentiseringsuppgifter.
> * Träna din formulär igenkännings modell i en Azure Databricks Notebook.
> * Extrahera formulär data med hjälp av en Databricks Notebook.
> * Automatisera yrkesutbildning och extrahering av formulär med hjälp av Azure Data Factory.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. [Skapa ett kostnads fritt](https://azure.microsoft.com/free/cognitive-services/).
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När resursen har distribuerats väljer du **Gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du ska klistra in nyckeln och slut punkten i koden senare i den här snabb starten.
    * Du kan använda den kostnads fria pris nivån (F0) för att testa tjänsten. Du kan sedan uppgradera senare till en betald nivå för produktion.
* En uppsättning minst fem formulär av samma typ. Det bästa är att det här arbets flödet är avsett att stödja stora uppsättningar dokument. Se [utveckla en data uppsättning för utbildning](./build-training-data-set.md) för tips och alternativ för att sätta samman din utbildnings data uppsättning. I den här självstudien kan du använda filerna i mappen träna i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2128080).

## <a name="project-architecture"></a>Projekt arkitektur 

Det här projektet är en uppsättning Azure Data Factory pipelines för att utlösa python-anteckningsböcker som tränar, analyserar och extraherar data från dokument i ett Azure Data Lake lagrings konto.

Formulär tolken REST API kräver vissa parametrar som inmatade. Av säkerhets skäl lagras vissa av dessa parametrar i ett Azure Key Vault. Andra, mindre känsliga parametrar, som lagrings-blobens mappnamn, kommer att lagras i en Parameterisering-tabell i en Azure SQL-databas.

För varje typ av formulär som ska analyseras fyller data tekniker eller data experter en rad i parameter tabellen. De använder sedan Azure Data Factory för att iterera över listan över identifierade formulär typer och skicka relevanta parametrar till en Databricks Notebook för att träna eller träna formulär igenkännings modeller. En Azure-funktion kan också användas här.

Azure Databricks Notebook använder sedan de tränade modellerna för att extrahera formulär data. Den exporterar dessa data till en Azure SQL-databas.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="Diagram som visar projekt arkitekturen.":::


## <a name="set-up-azure-data-lake"></a>Konfigurera Azure Data Lake

Din efter släpning av formulär kan finnas i din lokala miljö eller på en (s) FTP-server. I den här självstudien används formulär i ett Azure Data Lake Storage Gen2-konto. Du kan överföra dina filer dit med hjälp av Azure Data Factory, Azure Storage Explorer eller AzCopy. Data uppsättningarna utbildning och Poäng kan finnas i olika behållare, men inlärnings data uppsättningarna för alla formulär typer måste finnas i samma behållare. (De kan finnas i olika mappar.)

Om du vill skapa en ny data Lake följer du instruktionerna i [skapa ett lagrings konto som ska användas med Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Skapa en Parameterisering-tabell

Nu ska vi skapa en metadata-tabell i en Azure SQL-databas. Den här tabellen innehåller icke-känsliga data som krävs av formulär tolken REST API. När det finns en ny typ av formulär i data uppsättningen infogar vi en ny post i den här tabellen och utlöser kurserna för utbildning och poäng. (Vi kommer att implementera dessa pipelines senare.)

Dessa fält kommer att användas i tabellen:

* **form_description**. Krävs inte som en del av utbildningen. Det här fältet innehåller en beskrivning av typen av formulär som vi tränar modellen för (till exempel "klient A-formulär", "hotell B Forms").
* **training_container_name**. Namnet på lagrings konto behållaren där vi har lagrat inlärnings data uppsättningen. Det kan vara samma behållare som **scoring_container_name**.
* **training_blob_root_folder**. Mappen inom lagrings kontot där vi lagrar filerna för modellens utbildning.
* **scoring_container_name**. Namnet på lagrings konto behållaren där vi har lagrat filerna som vi vill extrahera nyckel-/värdepar från. Det kan vara samma behållare som **training_container_name**.
* **scoring_input_blob_folder**. Mappen på lagrings kontot där vi lagrar filerna för att extrahera data från.
* **model_id**. ID för den modell som vi vill omträna. För den första körningen måste värdet vara-1, vilket gör att den bärbara utbildningen skapar en ny anpassad modell att träna. Den utbildnings antecknings boken returnerar det nya modell-ID: t till Azure Data Factory-instansen. Genom att använda en lagrad procedur aktivitet uppdaterar vi det här värdet i Azure SQL-databasen.

  När du vill mata in en ny formulär typ måste du manuellt återställa modell-ID: t-1 innan du tränar modellen.

* **file_type**. Formulär typer som stöds är `application/pdf` ,, `image/jpeg` `image/png` och `image/tif` .

  Om du har formulär i andra filtyper måste du ändra det här värdet och **model_id** när du tränar en ny formulär typ.
* **form_batch_group_id**. Med tiden kan du ha flera formulär typer som du tränar mot samma modell. I fältet **form_batch_group_id** kan du ange alla formulär typer som har varit utbildning med en speciell modell.

### <a name="create-the-table"></a>Skapa tabellen


[Skapa en Azure SQL-databas](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)och kör sedan det här SQL-skriptet i [Frågeredigeraren](../../azure-sql/database/connect-query-portal.md) för att skapa den nödvändiga tabellen:


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

Kör det här skriptet för att skapa proceduren som automatiskt uppdaterar **model_id** efter att den tränats.

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

Ett nytt fönster visas. Välj **generera/importera**. Ange namnet på parametern och dess värde och välj sedan **skapa**. Slutför de här stegen för följande parametrar:

* **CognitiveServiceEndpoint**. Slut punkts-URL: en för formulärets tolknings-API.
* **CognitiveServiceSubscriptionKey**. Åtkomst nyckeln för formulär igenkännings tjänsten. 
* **StorageAccountName**. Lagrings kontot där träning-datauppsättningen och de formulär som du vill extrahera nyckel-/värdepar från lagras. Om dessa objekt finns i olika konton anger du varje konto namn som en separat hemlighet. Kom ihåg att träning-datauppsättningarna måste finnas i samma behållare för alla formulär typer. De kan finnas i olika mappar.
* **StorageAccountSasKey**. Lagrings kontots signatur för delad åtkomst (SAS). Hämta SAS-URL: en genom att gå till lagrings resursen. På fliken **Storage Explorer** går du till din behållare, högerklickar på och väljer **Hämta signatur för delad åtkomst**. Det är viktigt att hämta SAS för din behållare, inte för själva lagrings kontot. Kontrol lera att **Läs** -och **list** behörigheterna är markerade och välj sedan **skapa**. Kopiera sedan värdet i **URL** -avsnittet. Det ska ha det här formuläret: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Träna din formulär igenkännings modell i en Databricks Notebook

Du använder Azure Databricks för att lagra och köra python-koden som samverkar med formulär tolknings tjänsten.

### <a name="create-a-notebook-in-databricks"></a>Skapa en antecknings bok i Databricks

[Skapa en Azure Databricks resurs](https://ms.portal.azure.com/#create/Microsoft.Databricks) i Azure Portal. Gå till resursen när den har skapats och öppna arbets ytan.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Skapa en hemlig omfattning som backas upp av Azure Key Vault


Om du vill referera till hemligheterna i Azure Key Vault som du skapade ovan måste du skapa en hemlig omfattning i Databricks. Följ stegen här: [skapa ett hemligt Azure Key Vault hemligt omfång](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Skapa ett Databricks-kluster

Ett kluster är en samling av Databricks-beräknings resurser. Så här skapar du ett kluster:

1. I den vänstra rutan väljer du knappen **kluster** .
1. På sidan **kluster** väljer du **skapa kluster**.
1. På sidan **skapa kluster** anger du ett kluster namn och väljer sedan **7,2 (Scala 2,12, Spark 3.0.0)** i listan **Databricks runtime version** .
1. Välj **Skapa kluster**.

### <a name="write-a-settings-notebook"></a>Skriv en antecknings bok för inställningar

Nu är du redo att lägga till python-anteckningsböcker. Börja med att skapa en antecknings bok med namnet **Inställningar**. Den här antecknings boken kommer att tilldela värdena i Parameterisering-tabellen till variabler i skriptet. Azure Data Factory kommer senare att överföra värdena i som parametrar. Vi tilldelar också värden från hemligheterna i nyckel valvet till variabler. 

1. Välj knappen **arbets yta** om du vill skapa **Inställningar** för antecknings boken. Välj den nedrullningsbara listan på fliken nytt och välj **skapa** och sedan **Notebook**.
1. I popup-fönstret anger du ett namn för antecknings boken och väljer sedan **python** som standard språk. Välj ditt Databricks-kluster och välj sedan **skapa**.
1. I den första notebook-cellen hämtar vi parametrarna som skickats av Azure Data Factory:

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

1. I den andra cellen hämtar vi hemligheter från Key Vault och tilldelar dem till variabler:

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Skriv en utbildnings antecknings bok

Nu när du har slutfört **inställningarna** för Notebook, kan vi skapa en bärbar dator för att träna modellen. Som vi nämnt ovan använder vi filer som lagras i en mapp i ett Azure Data Lake Storage Gen2 konto (**training_blob_root_folder**). Mappnamnet har skickats in som en variabel. Varje uppsättning formulär typer kommer att finnas i samma mapp. När vi loopar över parameter tabellen tränar vi modellen genom att använda alla formulär typer. 

1. Skapa en antecknings bok med namnet **TrainFormRecognizer**. 
1. I den första cellen kör du **inställnings** antecknings boken:

    ```python
    %run "./Settings"
    ```

1. I nästa cell tilldelar du variabler från inställnings filen och tränar modellen dynamiskt för varje formulär typ, och använder koden i [rest-startsnabbet](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

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
        # Request headers.
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
    if model_id=="-1": # If you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key/value pairs.
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
    else :# If you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. Det sista steget i övnings processen är att hämta inlärnings resultatet i ett JSON-format:

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

## <a name="extract-form-data-by-using-a-notebook"></a>Extrahera formulär data med hjälp av en bärbar dator

### <a name="mount-the-azure-data-lake-storage"></a>Montera Azure Data Lake lagring

Nästa steg är att gå igenom de olika formulären med hjälp av den tränade modellen. Vi kommer att montera Azure Data Lake Storage-kontot i Databricks och se monteringen under inmatnings processen.

Som vi gjorde i tränings skedet använder vi Azure Data Factory för att anropa extraheringen av nyckel-/värdepar från formulären. Vi går igenom formulären i de mappar som anges i parameter tabellen.

1. Skapa antecknings boken för att montera lagrings kontot i Databricks. Anropa IT- **MountDataLake**. 
1. Du måste anropa **inställningarna** för den bärbara datorn först:

    ```python
    %run "./Settings"
    ```

1. I den andra cellen definierar du variabler för de känsliga parametrarna, som vi hämtar från våra Key Vault hemligheter:

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Försök att demontera lagrings kontot, om det tidigare har monterats:

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Montera lagrings kontot:


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
    > Vi har endast monterat utbildnings lagrings kontot. I det här fallet är utbildningsprogrammet och de filer som vi vill extrahera nyckel-/värdepar från i samma lagrings konto. Om beräknings-och utbildnings lagrings kontona är olika måste du montera båda lagrings kontona. 

### <a name="write-the-scoring-notebook"></a>Skriv bedömnings antecknings boken

Nu kan vi skapa en bedömnings antecknings bok. Vi ska göra något liknande vad vi gjorde i den utbildningsbaserade antecknings boken: vi ska använda filer som lagras i mappar i det Azure Data Lake Storage konto som vi precis har monterat. Mappnamnet skickas som en variabel. Vi går igenom alla formulär i den angivna mappen och extraherar nyckel/värde-par från dem. 

1. Skapa en bärbar dator och kalla den **ScoreFormRecognizer**. 
1. Kör **inställningarna** och **MountDataLake** -antecknings böckerna:

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Lägg till den här koden, som anropar API: et för [analys](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) :

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
        # Request headers.
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

1. I nästa cell får du resultatet från extraheringen av nyckel/värde-par. Resultatet visas i den här cellen. Vi vill ha resultatet i JSON-format så att vi kan bearbeta det ytterligare i Azure SQL Database eller Azure Cosmos DB. Vi skriver resultatet till en. JSON-fil. Namnet på utdatafilen är namnet på den resulterande filen som sammansatts med "_output.jspå". Filen kommer att lagras i samma mapp som käll filen.

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

## <a name="automate-training-and-scoring-by-using-azure-data-factory"></a>Automatisera utbildning och poäng genom att använda Azure Data Factory

Det enda återstående steget är att konfigurera Azure Data Factory tjänsten för att automatisera inlärnings-och Poäng processerna. Börja med att följa stegen under [skapa en data fabrik](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). När du har skapat Azure Data Factory resursen måste du skapa tre pipeliner: en för utbildning och två för poäng. (Vi förklarar senare.)

### <a name="training-pipeline"></a>Utbildnings pipeline

Den första aktiviteten i utbildnings pipelinen är en sökning för att läsa och returnera värdena i Parameterisering-tabellen i Azure SQL-databasen. Alla data uppsättningar för träning kommer att finnas i samma lagrings konto och behållare (men eventuellt olika mappar). Vi behåller därför standardvärdet för attributet **endast första raden** i inställningarna för söknings aktiviteten. För varje typ av formulär att träna modellen mot tränar vi modellen genom att använda alla filer i **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="Skärm bild som visar en utbildnings pipeline i Data Factory.":::

Den lagrade proceduren tar två parametrar: **model_id** och **form_batch_group_id**. Koden för att returnera modell-ID: t från Databricks Notebook är `dbutils.notebook.exit(model_id)` . Koden för att läsa koden i den lagrade procedur aktiviteten i Data Factory är `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id` .

### <a name="scoring-pipelines"></a>Poäng förlopp

För att extrahera nyckel/värde-par genomsöker vi alla mappar i Parameterisering-tabellen. För varje mapp extraherar vi nyckel-värdeparen för alla filer i den. Azure Data Factory stöder inte kapslade förgrunds slingor för närvarande. I stället skapar vi två pipeliner. Den första pipelinen kommer att göra sökningen från tabellen parameterisering och skicka mapplistan som en parameter till den andra pipelinen.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="Skärm bild som visar den första Poäng pipelinen i Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="Skärm bild som visar information om den första Poäng pipelinen i Data Factory.":::

Den andra pipelinen använder en GetMeta-aktivitet för att hämta listan över filer i mappen och skicka den som en parameter till bedömnings Databricks Notebook.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="Skärm bild som visar den andra Poäng pipelinen i Data Factory.":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="Skärm bild som visar information om den andra Poäng pipelinen i Data Factory.":::

### <a name="specify-a-degree-of-parallelism"></a>Ange en grad av parallellitet

I båda pipelinen för utbildning och Poäng kan du ange graden av parallellitet så att du kan bearbeta flera formulär samtidigt.

Ange graden av parallellitet i Azure Data Factory pipelinen:

1. Välj aktiviteten **för** aktiviteter.
1. Avmarkera den **sekventiella** rutan.
1. Ange graden av parallellitet i rutan **antal batchar** . Vi rekommenderar ett maximalt antal batchar på 15 för poängen.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="Skärm bild som visar Parallels-konfigurationen för bedömnings aktiviteten i Azure Data Factory.":::

## <a name="how-to-use-the-pipeline"></a>Använda pipelinen

Nu har du en automatiserad pipeline för att digitalisera din efter släpning av formulär och köra lite analys ovanpå den. När du lägger till nya formulär av en välbekant typ i en befintlig lagringsmapp kör du bara bedömnings pipelines igen. De uppdaterar alla utdatafiler, inklusive utdatafiler för de nya formulären. 

Om du lägger till nya former av en ny typ måste du också överföra en data uppsättning för utbildning till lämplig behållare. Sedan lägger du till en ny rad i tabellen parameterisering och anger platserna för de nya dokumenten och deras utbildnings data uppsättning. Ange värdet-1 för **model_ID** för att indikera att en ny modell behöver tränas för formulären. Kör sedan utbildnings pipelinen i Azure Data Factory. Pipelinen kommer att läsas från tabellen, träna en modell och skriva över modell-ID: t i tabellen. Sedan kan du anropa Poäng utloppet för att börja skriva utdatafilerna.

## <a name="next-steps"></a>Nästa steg

I den här självstudien ställer du in Azure Data Factory pipelines för att utlösa utbildning och körning av formulär igenkännings modeller och digitalisera en stor efter släpning av filer. Nu ska du utforska formulärets tolknings-API för att se vad mer du kan göra med det.

* [Formulär igenkännings REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
