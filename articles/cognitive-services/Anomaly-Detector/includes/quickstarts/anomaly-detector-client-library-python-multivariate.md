---
title: Snabb start för multivarierad-klient bibliotek för avvikelse detektor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 9b848f6c86f2ff2e95fa5cc191b088b7175f2311
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316036"
---
Kom igång med multivarierad klient bibliotek för avvikelse detektor för python. Följ de här stegen för att installera paket starten med hjälp av algoritmerna som tillhandahålls av tjänsten. De nya API: erna för avvikelse identifiering i multivarierad gör det möjligt för utvecklare att enkelt integrera avancerad AI för att identifiera avvikelser från grupper av mått, utan att det behövs några kunskaper om Machine Learning eller märkta data. Beroenden och Inter-korrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. Detta hjälper dig att proaktivt skydda dina komplexa system från haverier.

Använd klient biblioteket för avvikelse detektor multivarierad för python för att:

* Identifiera avvikelser på system nivå från en grupp tids serier.
* När en enskild tid serie inte meddelar dig mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predicative-underhåll av dyra fysiska till gångar med flera till hundratals olika typer av sensorer som mäter olika aspekter av system hälsan.

## <a name="prerequisites"></a>Förutsättningar

* [Python 3.x](https://www.python.org/)
* [Pandas data analys bibliotek](https://pandas.pydata.org/)
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.


## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

 Skapa en ny python-fil och importera följande bibliotek.

```python
import os
import time
from datetime import datetime

from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectionRequest, ModelInfo
from azure.core.credentials import AzureKeyCredential
from azure.core.exceptions import HttpResponseError
```

Skapa variabler för din nyckel som en miljö variabel, sökvägen till en tids serie data fil och Azure-platsen för din prenumeration. Till exempel `westus2`.

```python
subscription_key = "ANOMALY_DETECTOR_KEY"
anomaly_detector_endpoint = "ANOMALY_DETECTOR_ENDPOINT"
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera klient biblioteket med:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du gör följande med klient biblioteket för avvikelse detektor för python:

* [Autentisera klienten](#authenticate-the-client)
* [Träna modellen](#train-the-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

Om du vill instansiera en ny klient för avvikelse detektor måste du skicka prenumerations nyckeln för avvikelse detektor och associerad slut punkt. Vi upprättar också en data källa.  

För att kunna använda multivarierad-API: erna för avvikelse detektor måste vi träna vår egen modell innan identifieringen används. Data som används för utbildning är en batch över tids serier, varje gång serien ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tids serier ska vara zippade till en zip-fil och överföras till [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md#blobs). Som standard används fil namnet för att representera variabeln för tids serien. Alternativt kan en extra meta.jsi filen inkluderas i zip-filen om du vill att namnet på variabeln ska skilja sig från. zip-filens namn. När vi genererar [URL: en BLOB SAS (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL: en till zip-filen för utbildning.

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

Vi börjar med att träna modellen, kontrollerar modellens status och träningen för att fastställa när utbildningen är klar och hämtar sedan det senaste modell-ID som vi behöver när vi går vidare till identifierings fasen.

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

Använd `detect_anomaly` och `get_dectection_result` för att avgöra om det finns några avvikelser i data källan. Du måste skicka modell-ID: t för den modell som du precis har tränat.

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

Om du vill exportera en modell användning `export_model` och skicka modell-ID: t för den modell som du vill exportera:

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

Ta bort en modell användning `delete_multivariate_model` och skicka modell-ID: t för den modell som du vill ta bort:

```python
def delete_model(self, model_id):

    # Delete the mdoel
    self.ad_client.delete_multivariate_model(model_id)
    model_list_after_delete = list(self.ad_client.list_multivariate_model(skip=0, top=10000))
    print("{:d} available models after deletion.".format(len(model_list_after_delete)))
```

## <a name="run-the-application"></a>Kör programmet

Innan du kör programmet måste vi lägga till kod för att anropa våra nyligen skapade funktioner.

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

Kör programmet med `python` kommandot och ditt fil namn.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
