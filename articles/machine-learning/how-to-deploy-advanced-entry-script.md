---
title: Authoring Entry-skript för avancerade scenarier
titleSuffix: Azure Machine Learning entry script authoring
description: Lär dig hur du skriver Azure Machine Learning post skript för för-och efter bearbetning under distributionen.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: f88b6bd79e1004c108ef868f910a4e5a974cc08a
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508545"
---
# <a name="advanced-entry-script-authoring"></a>Avancerad startskriptredigering

Den här artikeln visar hur du skriver in skript för specialiserade användnings fall.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har en utbildad maskin inlärnings modell som du avser att distribuera med Azure Machine Learning. Mer information om modell distribution finns i [den här självstudien](how-to-deploy-and-where.md).

## <a name="automatically-generate-a-swagger-schema"></a>Generera ett Swagger-schema automatiskt

För att automatiskt generera ett schema för webb tjänsten, ange ett exempel på indata och/eller utdata i konstruktorn för ett av de definierade typ objekten. Typen och exemplet används för att automatiskt skapa schemat. Azure Machine Learning skapar sedan en [openapi](https://swagger.io/docs/specification/about/) -specifikation (Swagger) för webb tjänsten under distributionen. 

> [!WARNING]
> Du får inte använda känsliga eller privata data för exempel på indata eller utdata. Swagger-sidan för AML-värdbaserade inferencing visar exempel data. 

Dessa typer stöds för närvarande:

* `pandas`
* `numpy`
* `pyspark`
* Standard python-objekt

Om du vill använda schema generering inkluderar du paket versionen med öppen källkod `inference-schema` 1.1.0 eller ovanför i beroende filen. Mer information om det här paketet finns i [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) . För att kunna generera Swagger för automatiserad webb tjänst användning måste funktionen bedömnings skript Run () ha API-formen:
* En första parameter av typen "StandardPythonParameterType", namngivna **indata** och kapslade.
* En valfri andra parameter av typen "StandardPythonParameterType", med namnet **dublettparameternamnet**.
* Returnera en ord lista av typen "StandardPythonParameterType" med namnet **Results** och nested.

Definiera exempel formaten indata och utdata i `input_sample` `output_sample` variablerna och som representerar formatet för begäran och svar för webb tjänsten. Använd de här exemplen i funktionen indata och utdata dekoratörer i `run()` funktionen. Följande scikit – lära använder schema generering.



## <a name="power-bi-compatible-endpoint"></a>Power BI kompatibel slut punkt 

I följande exempel visas hur du definierar API-form enligt instruktionen ovan. Den här metoden stöds för att konsumera den distribuerade webb tjänsten från Power BI. ([Läs mer om hur du använder webb tjänsten från Power BI](/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                        'input2': pandas_sample_input, 
                                        'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) # this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])
outputs = StandardPythonParameterType({'Results':sample_output}) # 'Results' is case sensitive

@input_schema('Inputs', sample_input) 
# 'Inputs' is case sensitive

@input_schema('GlobalParameters', sample_global_parameters) 
# this is optional, 'GlobalParameters' is case sensitive

@output_schema(outputs)

def run(Inputs, GlobalParameters): 
    # the parameters here have to match those in decorator, both 'Inputs' and 
    # 'GlobalParameters' here are case sensitive
    try:
        data = Inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Binär data (d.v.s. avbildning)

Om din modell accepterar binära data, till exempel en bild, måste du ändra den `score.py` fil som används för distributionen för att acceptera obehandlade HTTP-begäranden. Om du vill acceptera rå data använder du `AMLRequest` klassen i ditt Entry-skript och lägger till `@rawhttp` decorator i `run()` funktionen.

Här är ett exempel på en `score.py` som accepterar binära data:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse
from PIL import Image
import json


def init():
    print("This is init()")
    

@rawhttp
def run(request):
    print("This is run()")
    
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        file_bytes = request.files["image"]
        image = Image.open(file_bytes).convert('RGB')
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the size of the image as the response..
        return AMLResponse(json.dumps(image.size), 200)
    else:
        return AMLResponse("bad request", 500)
```


> [!IMPORTANT]
> `AMLRequest`Klassen är i `azureml.contrib` namn området. Entiteter i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namn området bör betraktas som en för hands version som inte fullt ut stöds av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```

Med `AMLRequest` klassen kan du bara komma åt rå data som har anslagits i score.py, men det finns ingen komponent på klient sidan. Från en klient skickar du data som vanligt. Följande python-kod läser till exempel en avbildnings fil och publicerar data:

```python
import requests

uri = service.scoring_uri
image_path = 'test.jpg'
files = {'image': open(image_path, 'rb').read()}
response = requests.post(url, files=files)

print(response.json)
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Resursdelning för korsande ursprung (CORS)

Resurs delning mellan ursprung är ett sätt att tillåta att resurser på en webb sida begärs från en annan domän. CORS fungerar via HTTP-huvuden som skickas med klientbegäran och som returneras med tjänstens svar. Mer information om CORS och giltiga huvuden finns i [resurs delning mellan ursprung](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) i wikipedia.

Om du vill konfigurera modell distributionen så att den stöder CORS använder du `AMLResponse` klassen i ditt Entry-skript. Med den här klassen kan du ange huvuden för objektet Response.

I följande exempel anges `Access-Control-Allow-Origin` rubriken för svaret från Entry-skriptet:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")

@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse`Klassen är i `azureml.contrib` namn området. Entiteter i denna namnrymd ändras ofta när vi arbetar för att förbättra tjänsten. Allt i det här namn området bör betraktas som en för hands version som inte fullt ut stöds av Microsoft.
>
> Om du behöver testa detta i din lokala utvecklings miljö kan du installera komponenterna med hjälp av följande kommando:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning dirigerar bara POST-och GET-begäranden till behållarna som kör bedömnings tjänsten. Detta kan orsaka fel på grund av att webbläsare använder OPTIONs-förfrågningar till före flygning-CORS-begäranden.
> 


## <a name="load-registered-models"></a>Läsa in registrerade modeller

Det finns två sätt att hitta modeller i ditt Entry-skript:
* `AZUREML_MODEL_DIR`: En miljö variabel som innehåller sökvägen till modell platsen.
* `Model.get_model_path`: Ett API som returnerar sökvägen till modell filen med namnet på den registrerade modellen.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR är en miljö variabel som skapas under tjänst distributionen. Du kan använda den här miljövariabeln för att hitta platsen för de distribuerade modellerna.

I följande tabell beskrivs värdet för AZUREML_MODEL_DIR beroende på hur många modeller som distribueras:

| Distribution | Miljö variabel värde |
| ----- | ----- |
| Enskild modell | Sökvägen till mappen som innehåller modellen. |
| Flera modeller | Sökvägen till mappen som innehåller alla modeller. Modeller finns efter namn och version i den här mappen ( `$MODEL_NAME/$VERSION` ) |

Under modell registrering och distribution placeras modeller i AZUREML_MODEL_DIR Sök väg och deras ursprungliga fil namn bevaras.

Om du vill hämta sökvägen till en modell fil i ditt Entry-skript kombinerar du miljövariabeln med den fil Sök väg som du letar efter.

**Exempel på en modell**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exempel på flera modeller**

I det här scenariot registreras två modeller med arbets ytan:

* `my_first_model`: Innehåller en fil ( `my_first_model.pkl` ) och det finns bara en version ( `1` ).
* `my_second_model`: Innehåller en fil ( `my_second_model.pkl` ) och det finns två versioner, `1` och `2` .

När tjänsten distribuerades anges båda modellerna i distributions åtgärden:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

I Docker-avbildningen som är värd för tjänsten `AZUREML_MODEL_DIR` innehåller miljövariabeln den katalog där modellerna finns.
I den här katalogen finns varje modell i en katalog Sök väg för `MODEL_NAME/VERSION` . Där `MODEL_NAME` är namnet på den registrerade modellen och `VERSION` är versionen av modellen. De filer som utgör den registrerade modellen lagras i dessa kataloger.

I det här exemplet är Sök vägarna `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` och `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` .


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

När du registrerar en modell anger du ett modell namn som används för att hantera modellen i registret. Du kan använda det här namnet med metoden [Model.get_model_path ()](/python/api/azureml-core/azureml.core.model.model#get-model-path-model-name--version-none---workspace-none-) för att hämta sökvägen till modell filen eller filerna i det lokala fil systemet. Om du registrerar en mapp eller en samling filer, returnerar detta API sökvägen till katalogen som innehåller filerna.

När du registrerar en modell ger du den ett namn. Namnet motsvarar var modellen placeras, antingen lokalt eller under tjänst distributionen.

## <a name="framework-specific-examples"></a>Ramverk – vissa exempel

Fler exempel på skript exempel för olika användnings fall för Machine Learning finns nedan:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)
