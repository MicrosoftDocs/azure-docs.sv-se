---
title: Använda och distribuera befintliga modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du tar ML-modeller som tränats utanför Azure till Azure-molnet med Azure Machine Learning. Distribuera sedan modellen som en webbtjänst eller IoT-modul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 46dc9e48ba68189253885ae823457a62c3c4426e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877812"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Distribuera din befintliga modell med Azure Machine Learning


I den här artikeln får du lära dig hur du registrerar och distribuerar en maskininlärningsmodell som du har tränat Azure Machine Learning. Du kan distribuera som en webbtjänst eller till en IoT Edge enhet.  När den har distribuerats kan du övervaka din modell och identifiera dataavdrift i Azure Machine Learning. 

Mer information om begreppen och termerna i den här artikeln finns i [Hantera, distribuera och övervaka maskininlärningsmodeller.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Förutsättningar

* [En Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)
  + Python-exempel förutsätter `ws` att variabeln är inställd på Azure Machine Learning arbetsyta. Mer information om hur du ansluter till arbetsytan finns i dokumentationen [Azure Machine Learning SDK för Python.](/python/api/overview/azure/ml/#workspace)
  
  + CLI-exempel använder platshållare för och , som du bör ersätta med namnet på `myworkspace` `myresourcegroup` din arbetsyta och den resursgrupp som innehåller den.

* Den [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/install).  

* [Azure CLI och](/cli/azure/install-azure-cli) Machine Learning [CLI-tillägget](reference-azure-machine-learning-cli.md).

* En tränad modell. Modellen måste bevaras i en eller flera filer i utvecklingsmiljön. <br><br>För att demonstrera registrering av en modell som tränats använder exempelkoden i den här artikeln modellerna från [Ripamonti:s Twitter-projekt för attitydanalys.](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)

## <a name="register-the-models"></a>Registrera modellen/modell(erna)

Genom att registrera en modell kan du lagra, versionsregistrera och spåra metadata om modeller på din arbetsyta. I följande Python- och CLI-exempel `models` innehåller katalogen filerna , , och `model.h5` `model.w2v` `encoder.pkl` `tokenizer.pkl` . I det här exemplet laddas filerna i katalogen upp `models` som en ny modellregistrering med namnet `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Mer information finns i [referensen Model.register().](/python/api/azureml-core/azureml.core.model%28class%29#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Du kan också ange lägg `tags` till och `properties` ordlisteobjekt i den registrerade modellen. Dessa värden kan användas senare för att identifiera en specifik modell. Till exempel det ramverk som används, träningsparametrar osv.

Mer information finns i referensen [för az ml model register.](/cli/azure/ml/model#az_ml_model_register)


Mer information om modellregistrering i allmänhet finns i [Hantera, distribuera och övervaka maskininlärningsmodeller.](concept-model-management-and-deployment.md)

## <a name="define-inference-configuration"></a>Definiera slutsatsledningskonfiguration

Inferenskonfigurationen definierar den miljö som används för att köra den distribuerade modellen. Inferenskonfigurationen refererar till följande entiteter som används för att köra modellen när den distribueras:

* Ett startskript med namnet `score.py` läser in modellen när den distribuerade tjänsten startar. Det här skriptet ansvarar också för att ta emot data, skicka dem till modellen och sedan returnera ett svar.
* En Azure Machine Learning [miljö](how-to-use-environments.md). En miljö definierar de programvaruberoenden som behövs för att köra modellen och inmatningsskriptet.

I följande exempel visas hur du använder SDK för att skapa en miljö och sedan använda den med en inferenskonfiguration:

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

+ [Så här använder du miljöer](how-to-use-environments.md).
+ [Referens för InferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)


CLI läser in härledningskonfigurationen från en YAML-fil:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Med CLI definieras conda-miljön i filen `myenv.yml` som refereras av inferenskonfigurationen. Följande YAML är innehållet i den här filen:

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

Mer information om slutsatsledningskonfiguration finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Startskript (score.py)

Startskriptet har bara två nödvändiga funktioner, `init()` och `run(data)` . Dessa funktioner används för att initiera tjänsten vid start och köra modellen med hjälp av databegäran som skickas av en klient. Resten av skriptet hanterar inläsning och körning av modeller.

> [!IMPORTANT]
> Det finns inget allmänt inmatningsskript som fungerar för alla modeller. Den är alltid specifik för den modell som används. Den måste förstå hur du läser in modellen, det dataformat som modellen förväntar sig och hur du poängar data med hjälp av modellen.

Följande Python-kod är ett exempel på ett startskript ( `score.py` ):

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

Mer information om startskript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definiera distribution

Webbtjänstpaketet [innehåller](/python/api/azureml-core/azureml.core.webservice) de klasser som används för distribution. Den klass som du använder avgör var modellen distribueras. Om du till exempel vill distribuera som en webbtjänst på Azure Kubernetes Service använder [du AksWebService.deploy_configuration() för](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) att skapa distributionskonfigurationen.

Följande Python-kod definierar en distributionskonfiguration för en lokal distribution. Den här konfigurationen distribuerar modellen som en webbtjänst till din lokala dator.

> [!IMPORTANT]
> En lokal distribution kräver en fungerande installation av [Docker](https://www.docker.com/) på den lokala datorn:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Mer information finns i [referensen för LocalWebservice.deploy_configuration().](/python/api/azureml-core/azureml.core.webservice.localwebservice#deploy-configuration-port-none-)

CLI läser in distributionskonfigurationen från en YAML-fil:

```YAML
{
    "computeType": "LOCAL"
}
```

Att distribuera till ett annat beräkningsmål, till exempel Azure Kubernetes Service i Azure-molnet, är lika enkelt som att ändra distributionskonfigurationen. Mer information finns i Hur [och var du distribuerar modeller.](how-to-deploy-and-where.md)

## <a name="deploy-the-model"></a>Distribuera modellen

I följande exempel läses information in på den registrerade `sentiment` modellen med namnet och distribueras sedan som en tjänst med namnet `sentiment` . Under distributionen används härledningskonfigurationen och distributionskonfigurationen för att skapa och konfigurera tjänstmiljön:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Mer information finns i [referensen model.deploy().](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Om du vill distribuera modellen från CLI använder du följande kommando. Det här kommandot distribuerar version 1 av den registrerade modellen ( ) med hjälp av `sentiment:1` inferens- och distributionskonfigurationen som lagras i `inferenceConfig.json` `deploymentConfig.json` filerna och :

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Mer information finns i referensen [för az ml model deploy.](/cli/azure/ml/model#az_ml_model_deploy)

Mer information om distribution finns i Hur [och var du distribuerar modeller.](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Förbrukning av begäran-svar

Efter distributionen visas bedömnings-URI:en. Den här URI:en kan användas av klienter för att skicka begäranden till tjänsten. Följande exempel är en enkel Python-klient som skickar data till tjänsten och visar svaret:

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

Mer information om hur du använder den distribuerade tjänsten finns i [Skapa en klient.](how-to-consume-web-service.md)

## <a name="next-steps"></a>Nästa steg

* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Så här skapar du en klient för en distribuerad modell](how-to-consume-web-service.md)