---
title: Använda och distribuera befintliga modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kan få ML-modeller utbildade utanför Azure till Azure-molnet med Azure Machine Learning. Distribuera sedan modellen som en webb tjänst eller IoT-modul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 8076e3f6b2198abe46ae49cd8e0fd2b02f000231
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519358"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Distribuera din befintliga modell med Azure Machine Learning


I den här artikeln får du lära dig hur du registrerar och distribuerar en maskin inlärnings modell som du har tränat utanför Azure Machine Learning. Du kan distribuera som en webb tjänst eller till en IoT Edge enhet.  När du har distribuerat kan du övervaka din modell och identifiera data avvikelser i Azure Machine Learning. 

Mer information om begreppen och termerna i den här artikeln finns i [Hantera, distribuera och övervaka Machine Learning-modeller](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

* [En Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)
  + Python-exempel förutsätter att `ws` variabeln är inställd på din Azure Machine Learning-arbetsyta. Mer information om hur du ansluter till arbets ytan finns i [dokumentationen för Azure Machine Learning SDK för python](/python/api/overview/azure/ml/#workspace).
  
  + CLI-exempel använder plats hållare för `myworkspace` och `myresourcegroup` , som du bör ersätta med namnet på din arbets yta och resurs gruppen som innehåller den.

* [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install).  

* [Azure CLI](/cli/azure/install-azure-cli) -och [Machine Learning CLI-tillägget](reference-azure-machine-learning-cli.md).

* En utbildad modell. Modellen måste vara bestående av en eller flera filer i utvecklings miljön. <br><br>För att demonstrera registrering av en modell som är utbildad använder exempel koden i den här artikeln modeller från [Paolo Ripamontis projekt för Twitter-sentiment](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrera modell (er)

Genom att registrera en modell kan du lagra, version och spåra metadata om modeller i din arbets yta. I följande python-och CLI-exempel `models` innehåller katalogen `model.h5` filerna,, `model.w2v` `encoder.pkl` och `tokenizer.pkl` . I det här exemplet överförs filerna som finns i `models` katalogen som en ny modell registrering med namnet `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Mer information finns i referensen [Model. register ()](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Du kan också ställa in Lägg till `tags` och `properties` Dictionary-objekt i den registrerade modellen. Dessa värden kan användas senare för att identifiera en speciell modell. Till exempel ramverket som används, utbildnings parametrar osv.

Mer information finns i register referens för [AZ ml-modellen](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register) .


Mer information om modell registrering i allmänhet finns i [Hantera, distribuera och övervaka Machine Learning-modeller](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definiera konfiguration av härledning

Konfigurationen av konfigurationen definierar den miljö som används för att köra den distribuerade modellen. Konfigurations konfigurationen refererar till följande entiteter, som används för att köra modellen när den distribueras:

* Ett Entry-skript med namnet `score.py` , läser in modellen när den distribuerade tjänsten startar. Det här skriptet ansvarar också för att ta emot data, skicka det till modellen och sedan returnera ett svar.
* En Azure Machine Learnings [miljö](how-to-use-environments.md). En miljö definierar de program beroenden som krävs för att köra modell-och registrerings skriptet.

I följande exempel visas hur du använder SDK för att skapa en miljö och sedan använder den med en konfigurations konfiguration:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Mer information finns i följande artiklar:

+ [Använda miljöer](how-to-use-environments.md).
+ [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) -referens.


CLI läser in konfigurationen från en YAML-fil:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Med CLI definieras Conda-miljön i `myenv.yml` filen som den här konfigurations konfigurationen refererar till. Följande YAML är innehållet i den här filen:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Mer information om konfiguration av konfiguration finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Entry-skript (score.py)

Entry-skriptet har bara två nödvändiga funktioner `init()` och `run(data)` . Dessa funktioner används för att initiera tjänsten vid start och köra modellen med hjälp av begär ande data som skickas in av en klient. Resten av skriptet hanterar inläsning och körning av modell (er).

> [!IMPORTANT]
> Det finns inget Generic Entry-skript som fungerar för alla modeller. Den är alltid unik för den modell som används. Den måste förstå hur du läser in modellen, det data format som modellen förväntar sig och hur du visar data med hjälp av modellen.

Följande python-kod är ett exempel på ett Entry-skript ( `score.py` ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Mer information om Entry-skript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiera distribution

[WebService-](/python/api/azureml-core/azureml.core.webservice) paketet innehåller de klasser som används för distribution. Klassen som du använder avgör var modellen distribueras. Om du till exempel vill distribuera som en webb tjänst på Azure Kubernetes-tjänsten använder du [AksWebService.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) för att skapa distributions konfigurationen.

Följande python-kod definierar en distributions konfiguration för en lokal distribution. Den här konfigurationen distribuerar modellen som en webb tjänst till din lokala dator.

> [!IMPORTANT]
> En lokal distribution kräver en fungerande installation av [Docker](https://www.docker.com/) på den lokala datorn:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Mer information finns i referensen [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-) .

CLI läser in distributions konfigurationen från en YAML-fil:

```YAML
{
    "computeType": "LOCAL"
}
```

Att distribuera till ett annat beräknings mål, till exempel Azure Kubernetes service i Azure-molnet, är lika enkelt som att ändra distributions konfigurationen. Mer information finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Distribuera modellen

I följande exempel läser in information om den registrerade modellen med namnet `sentiment` och distribuerar den sedan som en tjänst med namnet `sentiment` . Under distributionen används konfigurations konfiguration och distribution för att skapa och konfigurera tjänst miljön:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Mer information finns i referensen [Model. Deploy ()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) .

Använd följande kommando för att distribuera modellen från CLI. Det här kommandot distribuerar version 1 av den registrerade modellen ( `sentiment:1` ) med hjälp av konfigurationen för konfigurations härledning och distribution som lagras i `inferenceConfig.json` `deploymentConfig.json` filerna och:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Mer information finns i [distributions referens för AZ ml-modellen](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

Mer information om distribution finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Användning av begäran-svar

Efter distributionen visas bedömnings-URI: n. Denna URI kan användas av klienter för att skicka begär anden till tjänsten. Följande exempel är en enkel python-klient som skickar data till tjänsten och visar svaret:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Mer information om hur du använder den distribuerade tjänsten finns i [skapa en klient](how-to-consume-web-service.md).

## <a name="next-steps"></a>Nästa steg

* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Så här skapar du en klient för en distribuerad modell](how-to-consume-web-service.md)