---
title: 'ML Studio (klassisk): hantera webb tjänster med API Management – Azure'
description: En guide som visar hur du hanterar AzureML-webbtjänster med API Management. Hantera dina REST API-slutpunkter genom att definiera användar åtkomst, användnings begränsning och övervakning av instrument paneler.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 293643ab5354c51142baaf281b1845cfc9fa6f23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518036"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Hantera Azure Machine Learning Studio (klassiska) webb tjänster med API Management

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


## <a name="overview"></a>Översikt
Den här guiden visar hur du snabbt kommer igång med API Management för att hantera dina Azure Machine Learning Studio (klassiska) webb tjänster.

## <a name="what-is-azure-api-management"></a>Vad är Azure API Management?
Azure API Management är en Azure-tjänst som låter dig hantera dina REST API-slutpunkter genom att definiera användar åtkomst, användnings begränsning och övervakning av instrument paneler. Mer information finns på [webbplatsen för Azure API Management](https://azure.microsoft.com/services/api-management/) . Information om hur du kommer igång med Azure API Management finns i [import-och publicerings guiden](../../api-management/import-and-publish.md). Den här guiden, som den här guiden bygger på, omfattar fler ämnen, inklusive aviserings konfiguration, nivå priser, svars hantering, användarautentisering, skapa produkter, prenumerationer för utvecklare och användnings instrument paneler.

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att kunna slutföra den här guiden:

* Ett Azure-konto.
* Ett AzureML-konto.
* Arbets ytan, tjänsten och api_key för ett AzureML experiment som distribueras som en webb tjänst. Mer information om hur du skapar ett AzureML-experiment finns i [snabb start för Studio](create-experiment.md). Information om hur du distribuerar ett Studio-experiment (klassiskt) som en webb tjänst finns i avsnittet om hur du distribuerar ett AzureML experiment som en webb tjänst i avsnittet om [distribution av Studio-distribution](deploy-a-machine-learning-web-service.md) . Alternativt har bilaga A instruktioner för hur du skapar och testar ett enkelt AzureML experiment och distribuerar det som en webb tjänst.

## <a name="create-an-api-management-instance"></a>Skapa en API Management-instans

Du kan hantera Azure Machine Learning-webbtjänsten med en API Management instans.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **+ Skapa en resurs**.
3. I sökrutan skriver du "API Management" och väljer sedan "API Management"-resursen.
4. Klicka på **Skapa**.
5. **Name** -värdet kommer att användas för att skapa en unik URL (det här exemplet använder "demoazureml").
6. Välj en **prenumeration**, en **resurs grupp** och en **plats** för tjänst instansen.
7. Ange ett värde för **organisations namn** (det här exemplet använder "demoazureml").
8. Ange **administratörens e-postadress** – det här e-postmeddelandet kommer att användas för meddelanden från API Management systemet.
9. Klicka på **Skapa**.

Det kan ta upp till 30 minuter innan en ny tjänst skapas.

![Skärm bild som visar dialog rutan för hanterings tjänsten i I Management-tjänsten med de alternativ som krävs för att skapa en tjänst.](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Skapa API: et
När tjänst instansen har skapats är nästa steg att skapa API: et. Ett API består av en uppsättning åtgärder som kan anropas från ett klientprogram. API-åtgärder körs via en proxy till befintliga webbtjänster. Den här guiden skapar API: er som proxy till de befintliga AzureML-RESURSPOSTerna och BES-webbtjänsterna.

Så här skapar du API: et:

1. Öppna tjänst instansen som du skapade i Azure Portal.
2. Välj **API: er** i det vänstra navigerings fönstret.

   ![API-hantering-menyn](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Klicka på **Lägg till API**.
2. Ange ett **webb-API-namn** (det här exemplet använder "azureml demo-API").
3. För **webb tjänstens URL** anger du " `https://ussouthcentral.services.azureml.net` ".
4. Ange ett * * URL-suffix för webb-API ". Detta blir den sista delen av URL: en som kunderna ska använda för att skicka begär anden till tjänst instansen (det här exemplet använder "azureml-demo").
5. För **webb-API URL-schema** väljer du **https**.
6. För **produkter** väljer du **starter**.
7. Klicka på **Spara**.


## <a name="add-the-operations"></a>Lägg till åtgärder

Åtgärder läggs till och konfigureras till ett API i utgivar portalen. Öppna utgivar portalen genom att klicka **på Publisher-Portal** i Azure Portal för din API Management-tjänst, Välj API: **er**, **åtgärder** och klicka sedan på **Lägg till åtgärd**.

![Lägg till åtgärd](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Fönstret **ny åtgärd** visas och fliken **signatur** är markerad som standard.

## <a name="add-rrs-operation&quot;></a>Lägg till resurs åtgärd
Skapa först en åtgärd för AzureML RR-tjänsten:

1. För **http-verbet** väljer du **post**.
2. Skriv &quot;&quot; för **URL**-mallen `/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}` .
3. Ange ett **visnings namn** (det här exemplet använder &quot;resurs poster körs").

   ![Skärm bild som visar sidan signatur där du kan ange ett visnings namn.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Klicka på **svar**  >  **Lägg till** vänster och välj **200 OK**.
5. Klicka på **Spara** för att spara åtgärden.

   ![Skärm bild som visar sidan för åtgärden R R S-körning med knappen Spara.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Lägg till BES-åtgärder

> [!NOTE]
> Skärm dum par ingår inte här för BES-åtgärderna eftersom de liknar dem för att lägga till en resurs åtgärd.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Skicka (men inte starta) ett batch-körnings jobb

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet** väljer du **post**.
3. Skriv "" för **URL**-mallen `/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}` .
4. Ange ett **visnings namn** (det här exemplet använder "bes Submit").
5. Klicka på **svar**  >  **Lägg till** vänster och välj **200 OK**.
6. Klicka på **Spara**.

### <a name="start-a-batch-execution-job"></a>Starta ett batch-jobb för körning

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet** väljer du **post**.
3. Skriv "" för **http-verbet** `/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}` .
4. Ange ett **visnings namn** (det här exemplet använder "bes start").
6. Klicka på **svar**  >  **Lägg till** vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Hämta status eller resultat för ett batchjobb för batch-körning

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet** väljer du **Hämta**.
3. Skriv "" för **URL**-mallen `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` .
4. Ange ett **visnings namn** (det här exemplet använder "bes status").
6. Klicka på **svar**  >  **Lägg till** vänster och välj **200 OK**.
7. Klicka på **Spara**.

### <a name="delete-a-batch-execution-job"></a>Ta bort ett batch-jobb för körning

1. Klicka på **Lägg till åtgärd** för att lägga till en bes-åtgärd i API: et.
2. För **http-verbet** väljer du **ta bort**.
3. Skriv "" för **URL**-mallen `/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}` .
4. Ange ett **visnings namn** (det här exemplet använder "bes Delete").
5. Klicka på **svar**  >  **Lägg till** vänster och välj **200 OK**.
6. Klicka på **Spara**.

## <a name="call-an-operation-from-the-developer-portal"></a>Anropa en åtgärd från Developer-portalen

Åtgärder kan anropas direkt från Developer-portalen, vilket ger ett bekvämt sätt att visa och testa åtgärderna i ett API. I det här steget ska du anropa metoden **RR-körning** som har lagts till i **AZUREML demo-API**. 

1. Klicka på **Developer Portal**.

   ![Skärm bild som visar länken till Developer-portalen.](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Klicka på **API: er** på den översta menyn och klicka sedan på **AZUREML demo-API** för att se vilka åtgärder som är tillgängliga.

   ![Skärm bild som visar en P I-länk till Azure M L-demon.](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Välj **resurs resurs körning** för åtgärden. Klicka på **prova**.

   ![Skärm bild som visar en dialog ruta för Azure M L-demon i dialog rutan med R R S utför valda och knappen prova.](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. För **Parametrar för begäran** skriver du din **arbets yta** och  **tjänst**, skriver "2,0 för **API version**" och "true" för **information**. Du kan hitta **arbets ytan** och **tjänsten** i azureml-webbtjänstens instrument panel (se **testa webb tjänsten** i bilaga A).

   För **begärandehuvuden** klickar du på **Lägg till sidhuvud** och skriver "Content-Type" och "Application/JSON". Klicka på **Lägg till sidhuvud** igen och skriv "Authorization" och "Bearer *\<your service API-KEY\>* ". Du hittar din API-nyckel i AzureML-webbtjänstens instrument panel (se **testa webb tjänsten** i bilaga A).

   I **begär ande text** skriver du `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}` .

   ![Skärm bild som visar Azure M L-demon A P I begär parametrar, begärandehuvuden, begär ande brödtext och auktorisering.](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Klicka på **Skicka**.

   ![Skärm bild som visar knappen Skicka.](./media/manage-web-service-endpoints-using-api-management/send.png)

När en åtgärd har anropats visar Developer-portalen den **begärda URL:** en från backend-tjänsten, **svars status**, **svars rubriker** och allt **svars innehåll**.

![Skärm bild som visar Developer-portalen som visar svars status, svars fördröjning, svars rubriker och svars innehåll.](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Bilaga A – skapa och testa en enkel AzureML-webbtjänst
### <a name="creating-the-experiment"></a>Skapa experimentet
Nedan visas stegen för att skapa ett enkelt AzureML-experiment och distribuera det som en webb tjänst. Webb tjänsten tar sig in i en kolumn med godtycklig text och returnerar en uppsättning funktioner som visas som heltal. Exempel:

| Text | Hash-text |
| --- | --- |
| Det här är en lämplig dag |1 1 2 2 0 2 0 1 |

Börja med att använda en valfri webbläsare, navigera till: [https://studio.azureml.net/](https://studio.azureml.net/) och ange autentiseringsuppgifter för inloggning. Skapa sedan ett nytt tomt experiment.

![Skärm bild som visar en ny sida med EXPERIMENT markerat och en texts ökning.](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Byt namn på den till **SimpleFeatureHashingExperiment**. Expandera **sparade data uppsättningar** och dra **bok granskningar från Amazon** till experimentet.

![Skärm bild som visar exempel på vänster sida och ett SimpleFeatureHashingExperiment-fönster till höger med instruktionen att dra objekt hit.](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Expandera **data omvandling** och **manipulering** och dra **Välj kolumner i data uppsättningen** till experimentet. Anslut **bok granskningar från Amazon** för att **välja kolumner i data uppsättningen**.

![Anslut datauppsättnings modulen för bok granskningar till en modul för projekt kolumner](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Klicka på **Välj kolumner i data uppsättning** och klicka sedan på **Starta kolumn väljaren** och välj **Col2**. Klicka på bock markeringen för att tillämpa ändringarna.

![Välj kolumner med kolumn namn](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Expandera **textanalys** och dra **funktions-hashar** till experimentet. Anslut **Välj kolumner i data uppsättning** till **funktionens hashing**.

![Skärm bild som visar ett hash-objekt för funktionen som läggs till i arbets ytan.](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Typ **3** för **hashing-bitsize**. Då skapas 8 (23) kolumner.

![Skärm bild som visar egenskaper med funktionen hash markerad och du kan ange hashing-bitsize.](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Nu kanske du vill klicka på **Kör** för att testa experimentet.

![Skärm bild som visar knappen Kör.](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Skapa en webbtjänst
Nu ska du skapa en webb tjänst. Expandera **webb tjänsten** och dra **inmatade inmatade** till experimentet. Anslut **Indatamängden** till **funktionens hashing**. Dra även **utdata** till experimentet. Anslut **utdata** till **funktionens hashing**.

![Skärm bilden visar arbets ytan efter de angivna ändringarna.](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Klicka på **publicera webb tjänst**.

![Skärm bild som visar knappen publicera webb tjänst.](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Klicka på **Ja** för att publicera experimentet.

![Skärm bild som visar ett bekräftelse meddelande och alternativet att publicera eller inte.](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testa webbtjänsten
En AzureML-webbtjänst består av RSS-(Request/Response service) och BES-slutpunkter (batch execution service). RSS är för synkron körning. BES är för asynkron jobb körning. Om du vill testa din webb tjänst med exempel på python-källan nedan kan du behöva ladda ned och installera Azure SDK för python (se: [så här installerar du python](/azure/developer/python/azure-sdk-install)).

Du kommer också att behöva **arbets ytan**, **tjänsten** och **api_key** av ditt experiment för exempel källan nedan. Du kan hitta arbets ytan och tjänsten genom att klicka på **begär ande/svar** eller **batch-körning** för ditt experiment på webb tjänstens instrument panel.

![Skärm bild som visar fönstret begäran där du kan hitta värdena för arbets ytan och tjänsten.](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Du kan hitta **api_key** genom att klicka på experimentet på instrument panelen för webb tjänster.

![Skärm bild som visar experimentet på instrument panelen för webb tjänster där du kan hitta en P I-nyckel.](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Test resurs resurs slut punkt
##### <a name="test-button"></a>Knappen Testa
Ett enkelt sätt att testa resurs slut punkten är att klicka på **testa** på instrument panelen för webb tjänsten.

![Skärm bild som visar experimentet på instrument panelen för webb tjänster som har test knappen.](./media/manage-web-service-endpoints-using-api-management/test.png)

Skriv **det här är en lämplig dag** för **col2**. Klicka på bock markeringen.

![Skärm bild som visar dialog rutan Ange data att förutse där du kan ange text, till exempel en lämplig dag.](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Du ser något som liknar

![Skärm bild som visar resultatet av experimentet, som läser det här är en lämplig dag och flera siffror inom citat tecken.](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Exempelkod
Ett annat sätt att testa dina resurs poster är från klient koden. Om du klickar på **begär/svara** på instrument panelen och rullar till slutet visas exempel kod för C#, python och R. Du kommer också att se syntaxen för begäran om resurs poster, inklusive URI, rubriker och brödtext för begäran.

I den här hand boken visas ett arbets python-exempel. Du måste ändra den med **arbets ytan**, **tjänsten** och **api_key** av experimentet.

```python
import urllib2
import json
workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
data = {
"Inputs": {
    "input1": {
        "ColumnNames": ["Col2"],
        "Values": [ [ "This is a good day" ] ]
    },
},
"GlobalParameters": { }
}
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
body = str.encode(json.dumps(data))
try:
    req = urllib2.Request(url, body, headers)
    response = urllib2.urlopen(req)
    result = response.read()
    print "result:" + result
        except urllib2.HTTPError, error:
    print("The request failed with status code: " + str(error.code))
    print(error.info())
    print(json.loads(error.read()))
```

#### <a name="test-bes-endpoint"></a>Testa BES-slutpunkt
Klicka på **batch-körning** på instrument panelen och rulla längst ned. Exempel kod visas för C#, python och R. Du kan också se syntaxen för BES-begäranden för att skicka ett jobb, starta ett jobb, Hämta status eller resultat för ett jobb och ta bort ett jobb.

I den här hand boken visas ett arbets python-exempel. Du måste ändra den med **arbets ytan**, **tjänsten** och **api_key** av experimentet. Dessutom måste du ändra **lagrings kontots namn**, **lagrings konto nyckel** och **namn på lagrings behållare**. Slutligen måste du ändra platsen för **indatafilen** och platsen för **utdatafilen**.

```python
import urllib2
import json
import time
from azure.storage import *
workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
service = "<REPLACE WITH YOUR SERVICE ID>"
api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
input_blob_name = "mydatablob.csv"
output_blob_name = "myresultsblob.csv"
def printHttpError(httpError):
print("The request failed with status code: " + str(httpError.code))
print(httpError.info())
print(json.loads(httpError.read()))
return
def saveBlobToFile(blobUrl, resultsLabel):
print("Reading the result from " + blobUrl)
try:
    response = urllib2.urlopen(blobUrl)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
with open(output_file, "wb+") as f:
    f.write(response.read())
print(resultsLabel + " have been written to the file " + output_file)
return
def processResults(result):
first = True
results = result["Results"]
for outputName in results:
    result_blob_location = results[outputName]
    sas_token = result_blob_location["SasBlobToken"]
    base_url = result_blob_location["BaseLocation"]
    relative_url = result_blob_location["RelativeLocation"]
    print("The results for " + outputName + " are available at the following Azure Storage location:")
    print("BaseLocation: " + base_url)
    print("RelativeLocation: " + relative_url)
    print("SasBlobToken: " + sas_token)
    if (first):
        first = False
        url3 = base_url + relative_url + sas_token
        saveBlobToFile(url3, "The results for " + outputName)
return

def invokeBatchExecutionService():
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
print("Uploading the input to blob storage...")
data_to_upload = open(input_file, "r").read()
blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
print "Uploaded the input to blob storage"
input_blob_path = "/" + storage_container_name + "/" + input_blob_name
connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
payload =  {
    "Input": {
        "ConnectionString": connection_string,
        "RelativeLocation": input_blob_path
    },
    "Outputs": {
        "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
    },
    "GlobalParameters": {
    }
}
    body = str.encode(json.dumps(payload))
headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
print("Submitting the job...")
# submit the job
req = urllib2.Request(url + "?api-version=2.0", body, headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
result = response.read()
job_id = result[1:-1] # remove the enclosing double-quotes
print("Job ID: " + job_id)
# start the job
print("Starting the job...")
req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
url2 = url + "/" + job_id + "?api-version=2.0"

while True:
    print("Checking the job status...")
    # If you are using Python 3+, replace urllib2 with urllib.request in the following code
    req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = json.loads(response.read())
    status = result["StatusCode"]
    print "status:" + status
    if (status == 0 or status == "NotStarted"):
        print("Job " + job_id + " not yet started...")
    elif (status == 1 or status == "Running"):
        print("Job " + job_id + " running...")
    elif (status == 2 or status == "Failed"):
        print("Job " + job_id + " failed!")
        print("Error details: " + result["Details"])
        break
    elif (status == 3 or status == "Cancelled"):
        print("Job " + job_id + " cancelled!")
        break
    elif (status == 4 or status == "Finished"):
        print("Job " + job_id + " finished!")
        processResults(result)
        break
    time.sleep(1) # wait one second
return
invokeBatchExecutionService()
```