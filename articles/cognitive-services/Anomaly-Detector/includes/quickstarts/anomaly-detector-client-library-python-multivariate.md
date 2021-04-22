---
title: Avvikelseidentifiering snabbstart för Python multivariate-klientbibliotek
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: b0eba6d0be1d6a65b911f05dabf3cdbecc9c666d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879808"
---
Kom igång med Avvikelseidentifiering flervariat klientbibliotek för Python. Följ de här stegen för att installera paketet med hjälp av de algoritmer som tillhandahålls av tjänsten. Med de nya API:erna för multivarierad avvikelseidentifiering kan utvecklare enkelt integrera avancerad AI för att identifiera avvikelser från grupper med mått, utan att behöva maskininlärning eller märkta data. Beroenden och interkorrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. Detta hjälper dig att proaktivt skydda dina komplexa system mot fel.

Använd Avvikelseidentifiering flervariat klientbibliotek för Python för att:

* Identifiera avvikelser på systemnivå från en grupp med tidsserier.
* När en enskild tidsserie inte berättar så mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predikatiskt underhåll av dyra fysiska tillgångar med tiotals till hundratals olika typer av sensorer som mäter olika aspekter av systemhälsan.

[Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector)  |  [Paket (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/3.0.0b3/)  |  [Exempelkod](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/anomalydetector/azure-ai-anomalydetector/samples/sample_multivariate_detect.py)  |  [Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.x](https://www.python.org/)
* [Pandas dataanalysbibliotek](https://pandas.pydata.org/)
* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services)
* När du har din Azure-prenumeration skapar Avvikelseidentifiering en Avvikelseidentifiering resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Avvikelseidentifiering-API:et. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.


## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

 Skapa en ny Python-fil och importera följande bibliotek.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Skapa variabler för din nyckel som en miljövariabel, sökvägen till en tidsseriedatafil och Azure-platsen för din prenumeration. Till exempel `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

När du har installerat Python kan du installera klientbiblioteket med:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med Avvikelseidentifiering klientbibliotek för Python:

* [Autentisera klienten](#authenticate-the-client)
* [Träna modellen](#train-the-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

För att instansiera en Avvikelseidentifiering klient måste du skicka Avvikelseidentifiering prenumerationsnyckel och associerad slutpunkt. Vi kommer också att upprätta en datakälla.  

Om du vill Avvikelseidentifiering flera api:er måste vi träna vår egen modell innan du använder identifiering. Data som används för träning är en batch med tidsserier. Varje tidsserie ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tidsserier ska zippas upp i en zip-fil och laddas upp till [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md#blobs) Som standard används filnamnet för att representera variabeln för tidsserien. Alternativt kan en extra meta.jspå filen inkluderas i zip-filen om du vill att namnet på variabeln ska vara ett annat än ZIP-filnamnet. När vi har [genererat blob-SAS-URL :en (signaturer för delad åtkomst)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL:en till ZIP-filen för träning.

```python
def __init__(self, subscription_key, anomaly_detector_endpoint, data_source=None):
    self.sub_key = subscription_key
    self.end_point = anomaly_detector_endpoint

    # Create an Anomaly Detector client

    # <client>
    self.ad_client = AnomalyDetectorClient(AzureKeyCredential(self.sub_key), self.end_point)
    # </client>

    if not data_source:
        # Datafeed for test only
        self.data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS"
    else:
        self.data_source = data_source
```

## <a name="train-the-model"></a>Träna modellen

Vi tränar först modellen, kontrollerar modellens status under träningen för att avgöra när träningen är klar och hämtar sedan det senaste modell-ID:t som vi behöver när vi går över till identifieringsfasen.

```python
def train(self, start_time, end_time, max_tryout=500):

    # Number of models available now
    model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models before training.".format(len(model_list)))
    
    # Use sample data to train the model
    print("Training new model...")
    data_feed = ModelInfo(start_time=start_time, end_time=end_time, source=self.data_source)
    response_header = \
    self.ad_client.train_multivariate_model(data_feed, cls=lambda *args: [args[i] for i in range(len(args))])[-1]
    trained_model_id = response_header['Location'].split("/")[-1]
    
    # Model list after training
    new_model_list = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    
    # Wait until the model is ready. It usually takes several minutes
    model_status = None
    tryout_count = 0
    while (tryout_count < max_tryout and model_status != "READY"):
        model_status = self.ad_client.get_multivariate_model(trained_model_id).additional_properties["summary"][
            "status"]
        tryout_count += 1
        time.sleep(2)
    
    assert model_status == "READY"
    
    print("Done.", "\n--------------------")
    print("{:d} available models after training.".format(len(new_model_list)))
    
    # Return the latest model id
    return trained_model_id

```

## <a name="detect-anomalies"></a>Identifiera avvikelser

Använd och `detect_anomaly` för att avgöra om det finns några avvikelser i `get_dectection_result` datakällan. Du måste skicka modell-ID:t för den modell som du precis har tränat.

```python
def detect(self, model_id, start_time, end_time, max_tryout=500):
    
    # Detect anomaly in the same data source (but a different interval)
    try:
        detection_req = DetectionRequest(source=self.data_source, start_time=start_time, end_time=end_time)
        response_header = self.ad_client.detect_anomaly(model_id, detection_req,
                                                        cls=lambda *args: [args[i] for i in range(len(args))])[-1]
        result_id = response_header['Location'].split("/")[-1]
    
        # Get results (may need a few seconds)
        r = self.ad_client.get_detection_result(result_id)
        tryout_count = 0
        while r.summary.status != "READY" and tryout_count < max_tryout:
            time.sleep(1)
            r = self.ad_client.get_detection_result(result_id)
            tryout_count += 1
    
        if r.summary.status != "READY":
            print("Request timeout after %d tryouts.".format(max_tryout))
            return None
    
    except HttpResponseError as e:
        print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
    except Exception as e:
        raise e
    
    return r
```

## <a name="export-model"></a>Exportera modell

Om du vill exportera en modellanvändning `export_model` och skicka modell-ID:t för den modell som du vill exportera:

```python
def export_model(self, model_id, model_path="model.zip"):

    # Export the model
    model_stream_generator = self.ad_client.export_model(model_id)
    with open(model_path, "wb") as f_obj:
        while True:
            try:
                f_obj.write(next(model_stream_generator))
            except StopIteration:
                break
            except Exception as e:
                raise e
```

## <a name="delete-model"></a>Ta bort modell

Så här tar du bort en `delete_multivariate_model` modellanvändning och skickar modell-ID:t för den modell som du vill ta bort:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Kör programmet

Innan du kör programmet måste vi lägga till kod för att anropa de nya funktionerna.

```python
if __name__ == '__main__':
    subscription_key = "ANOMALY_DETECTOR_KEY"
    anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"

    # Create a new sample and client
    sample = MultivariateSample(subscription_key, anomaly_detector_endpoint, data_source=None)

    # Train a new model
    model_id = sample.train(datetime(2021, 1, 1, 0, 0, 0), datetime(2021, 1, 2, 12, 0, 0))

    # Reference
    result = sample.detect(model_id, datetime(2021, 1, 2, 12, 0, 0), datetime(2021, 1, 3, 0, 0, 0))
    print("Result ID:\t", result.result_id)
    print("Result summary:\t", result.summary)
    print("Result length:\t", len(result.results))

    # Export model
    sample.export_model(model_id, "model.zip")

    # Delete model
    sample.delete_model(model_id)

```

Innan du kör kan det vara bra att kontrollera projektet mot den [fullständiga exempelkod som](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) den här snabbstarten härleds från.

Vi har också [en detaljerad Jupyter Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb) som hjälper dig att komma igång.

Kör programmet med `python` kommandot och filnamnet.


[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
