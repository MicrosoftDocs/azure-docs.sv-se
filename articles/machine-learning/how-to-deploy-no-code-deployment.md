---
title: Ingen kod distribution (för hands version)
titleSuffix: Azure Machine Learning
description: Med ingen kod distribution kan du distribuera en modell som en webb tjänst utan att behöva skapa ett Entry-skript manuellt.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: deploy
ms.reviewer: larryfr
ms.openlocfilehash: a17126695aa5138d1df7fd17cfaa2f5f75ad1004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93324912"
---
# <a name="preview-no-code-model-deployment"></a>Förhandsgranskningsvyn Distribution utan kod modell

Ingen kod modell distribution är för närvarande en för hands version och stöder följande ramverk för Machine Learning:

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel-format
TensorFlow-modeller måste registreras i **SavedModel-format** för att fungera med modell distribution utan kod.

Se [den här länken](https://www.tensorflow.org/guide/saved_model) om du vill ha information om hur du skapar en SavedModel.

Vi har stöd för alla TensorFlow-versioner som anges under "Taggar" vid [TensorFlow-servning av DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags).

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX-modeller

ONNX-modell registrering och-distribution stöds för ett ONNX-diagram för härledning. Det finns för närvarande inte stöd för preprocess-och postprocess-stegen.

Här är ett exempel på hur du registrerar och distribuerar en MNIST ONNX-modell:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

Om du vill visa en modell läser du [använda en Azure Machine Learning modell som distribueras som en webb tjänst](./how-to-consume-web-service.md). Många ONNX-projekt använder protobuf-filer för att komprimera inlärnings-och verifierings data i stor form, vilket kan göra det svårt att veta vilket data format tjänsten förväntar sig. Som modell utvecklare bör du dokumentera för dina utvecklare:

* Indataformat (JSON eller Binary)
* Inmatnings data form och-typ (till exempel en matris med flytt ALS form [100100, 3])
* Domän information (till exempel för en bild, färg rymden, komponent ordningen och om värdena är normaliserade)

Om du använder Pytorch, [exporterar modeller från Pytorch till ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) information om konvertering och begränsningar. 

## <a name="scikit-learn-models"></a>Scikit – lär dig modeller

Ingen distribution av kod modeller stöds för alla inbyggda scikit – lär dig modell typer.

Här är ett exempel på hur du registrerar och distribuerar en sklearn-modell utan extra kod:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Modeller som stöder predict_proba kommer att använda den metoden som standard. Om du vill åsidosätta detta om du vill använda predict kan du ändra INLÄGGs texten enligt nedan:

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> Dessa beroenden ingår i den förbyggda scikit-lärens härlednings behållare:

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
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