---
title: Köra och distribuera lokalt
titleSuffix: Azure Machine Learning
description: Den här artikeln beskriver hur du använder din lokala dator som mål för träning, felsökning eller distribution av modeller som skapats i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 75836580fc2dc5a2090047865610e26d856387b0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861230"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Distribuera modeller som tränats Azure Machine Learning lokala datorer 

Den här artikeln beskriver hur du använder din lokala dator som mål för att träna eller distribuera modeller som skapats i Azure Machine Learning. Azure Machine Learning är tillräckligt flexibelt för att fungera med de flesta Ramverk för Python-maskininlärning. Maskininlärningslösningar har vanligtvis komplexa beroenden som kan vara svåra att duplicera. Den här artikeln visar hur du balanserar den totala kontrollen med enkel användning.

Scenarier för lokal distribution är:

* Snabbt iterera data, skript och modeller tidigt i ett projekt.
* Felsökning i senare steg.
* Slutlig distribution på användar hanterad maskinvara.

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)
- En modell och en miljö. Om du inte har någon tränad modell kan du använda modellen och beroendefilerna som finns i den här [självstudien.](tutorial-train-models-with-aml.md)
- Den [Azure Machine Learning SDK för Python](/python/api/overview/azure/ml/intro).
- En conda-chef, till exempel Anaconda eller Miniconda, om du vill spegla Azure Machine Learning av paketberoenden.
- Docker, om du vill använda en containerversion av den Azure Machine Learning miljön.

## <a name="prepare-your-local-machine"></a>Förbereda din lokala dator

Det mest tillförlitliga sättet att köra en modell Azure Machine Learning lokalt är med en Docker-avbildning. En Docker-avbildning ger en isolerad containerupplevelse som duplicerar, förutom maskinvaruproblem, Azure-körningsmiljön. Mer information om hur du installerar och konfigurerar Docker för utvecklingsscenarier finns i [Översikt över Docker-fjärrutveckling i Windows.](/windows/dev-environment/docker/overview)

Det går att koppla en felsökare till en process som körs i Docker. (Se [Ansluta till en container som körs](https://code.visualstudio.com/docs/remote/attach-container).) Men du kanske föredrar att felsöka och iterera Python-koden utan att involvera Docker. I det här scenariot är det viktigt att den lokala datorn använder samma bibliotek som används när du kör experimentet i Azure Machine Learning. För att hantera Python-beroenden använder Azure [conda](https://docs.conda.io/). Du kan skapa miljön igen med hjälp av andra pakethanterare, men det enklaste sättet att synkronisera är att installera och konfigurera conda på den lokala datorn. 

## <a name="prepare-your-entry-script"></a>Förbereda ditt startskript

Även om du använder Docker för att hantera modellen och beroenden måste Python-bedömningsskriptet vara lokalt. Skriptet måste ha två metoder:

- En `init()` metod som inte tar några argument och inte returnerar något 
- En `run()` metod som tar en JSON-formaterad sträng och returnerar ett JSON-serialiserbart objekt

Argumentet till metoden `run()` är i det här formuläret: 

```json
{
    "data": <model-specific-data-structure>
}
```

Det objekt som du returnerar från `run()` metoden måste implementera `toJSON() -> string` .

Följande exempel visar hur du läser in en registrerad scikit-learn-modell och poängtar den med hjälp av NumPy-data. Det här exemplet baseras på modellen och beroendena för den här [självstudien](tutorial-train-models-with-aml.md).

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Mer avancerade exempel, inklusive automatisk generering av Swagger-schema och bedömning av binära data (till exempel bilder) finns i Avancerad redigering [av startskript.](how-to-deploy-advanced-entry-script.md) 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Distribuera som en lokal webbtjänst med docker

Det enklaste sättet att replikera miljön som används av Azure Machine Learning är att distribuera en webbtjänst med hjälp av Docker. När Docker körs på den lokala datorn kommer du att:

1. Anslut till Azure Machine Learning arbetsyta där din modell är registrerad.
1. Skapa ett `Model` -objekt som representerar modellen.
1. Skapa ett `Environment` objekt som innehåller beroendena och definierar den programvarumiljö där koden ska köras.
1. Skapa ett `InferenceConfig` -objekt som associerar inmatningsskriptet med `Environment` .
1. Skapa ett `DeploymentConfiguration` -objekt för underklassen `LocalWebserviceDeploymentConfiguration` .
1. Använd `Model.deploy()` för att skapa ett `Webservice` -objekt. Den här metoden laddar ned Docker-avbildningen och associerar den med `Model` `InferenceConfig` , och `DeploymentConfiguration` .
1. Aktivera med `Webservice` hjälp av `Webservice.wait_for_deployment()` .

Följande kod visar de här stegen:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

Anropet `Model.deploy()` till kan ta några minuter. När du har distribuerat webbtjänsten är det effektivare att använda metoden i stället `update()` för att börja från början. Se [Uppdatera en distribuerad webbtjänst.](how-to-deploy-update-web-service.md)

### <a name="test-your-local-deployment"></a>Testa din lokala distribution

När du kör det tidigare distributionsskriptet matas den URI som du kan skicka postdata till för bedömning (till exempel `http://localhost:6789/score` ). I följande exempel visas ett skript som poängsätta exempeldata med hjälp av `"sklearn-mnist-local"` den lokalt distribuerade modellen. Modellen, om den har tränats korrekt, härrar `normalized_pixel_values` detta som ska tolkas som "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Ladda ned och kör din modell direkt

Att använda Docker för att distribuera din modell som en webbtjänst är det vanligaste alternativet. Men du kanske vill köra koden direkt med hjälp av lokala Python-skript. Du behöver två viktiga komponenter: 

- Själva modellen
- Beroenden som modellen förlitar sig på 

Du kan ladda ned modellen:  

- Från portalen väljer du fliken **Modeller,** väljer önskad modell och på sidan **Information** väljer du Ladda **ned.**
- Från kommandoraden använder du `az ml model download` . (Se [modellnedladdning.](/cli/azure/ml/model#az_ml_model_download))
- Med hjälp av Python `Model.download()` SDK-metoden. (Se [Modellklass.](/python/api/azureml-core/azureml.core.model.model#download-target-dir------exist-ok-false--exists-ok-none-))

En Azure-modell är ett eller flera serialiserade Python-objekt, paketerade som en Python pickle-fil (filnamnstillägget .pkl). Innehållet i pickle-filen beror på maskininlärningsbiblioteket eller tekniken som används för att träna modellen. Om du till exempel använder modellen från självstudien kan du läsa in modellen med:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Beroenden är alltid svåra att få rätt, särskilt med maskininlärning, där det ofta kan finnas en otydlig webb med specifika versionskrav. Du kan skapa en ny Azure Machine Learning på den lokala datorn antingen som en fullständig Conda-miljö eller som en Docker-avbildning med hjälp av `build_local()` metoden i `Environment` klassen : 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Om du anger `build_local()` `useDocker` argumentet till skapar funktionen `True` en Docker-avbildning i stället för en conda-miljö. Om du vill ha mer kontroll kan du använda metoden , som skriver `save_to_directory()` conda_dependencies.yml och azureml_environment.jspå definitionsfiler som du kan finjustera och använda som grund för `Environment` tillägget. 

Klassen har ett antal andra metoder för att synkronisera miljöer i din `Environment` beräkningsmaskinvara, din Azure-arbetsyta och Docker-avbildningar. Mer information finns i [Miljöklass](/python/api/azureml-core/azureml.core.environment(class)).

När du har hämtat modellen och löst dess beroenden finns det inga Azure-definierade begränsningar för hur du utför bedömning, finjusterar modellen, använder överförd inlärning och så vidare. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Ladda upp en omtränad modell till Azure Machine Learning

Om du har en lokalt tränad eller omtränad modell kan du registrera den med Azure. När den har registrerats kan du fortsätta justera den med hjälp av Azure Compute eller distribuera den med hjälp av Azure-resurser [som Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) [eller Triton Inference Server (förhandsversion).](how-to-deploy-with-triton.md)

För att kunna användas med Azure Machine Learning Python SDK måste en modell lagras som ett serialiserat Python-objekt i pickle-format (en .pkl-fil). Den måste också implementera en `predict(data)` metod som returnerar ett JSON-serialiserbart objekt. Du kan till exempel lagra en lokalt tränad scikit-learn-diabetesmodell med: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Om du vill göra modellen tillgänglig i Azure kan du använda `register()` metoden i `Model` klassen :

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Du hittar sedan din nyligen registrerade modell på fliken Azure Machine Learning **Modell:**

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Skärmbild av Azure Machine Learning modell med en uppladdad modell.":::

Mer information om hur du laddar upp och uppdaterar modeller och miljöer finns i [Registrera modell och distribuera lokalt med avancerad användning.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du hanterar miljöer finns i [Skapa & använda programvarumiljöer i Azure Machine Learning](how-to-use-environments.md).
- Mer information om hur du kommer åt data från ditt datalager finns [i Ansluta till lagringstjänster på Azure](how-to-access-data.md).
