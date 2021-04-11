---
title: Distribuera en modell för härledning med GPU
titleSuffix: Azure Machine Learning
description: Den här artikeln lär dig hur du använder Azure Machine Learning för att distribuera en GPU-aktiverad Tensorflow djup inlärnings modell som en webb tjänst. tjänst-och Poäng härlednings begär Anden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 6797c32ded5c12bbac3fafa1eabd1e6f74d28e07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519256"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Distribuera en djup inlärnings modell för en härledning med GPU


Den här artikeln lär dig hur du använder Azure Machine Learning för att distribuera en GPU-aktiverad modell som en webb tjänst. Informationen i den här artikeln baseras på distribution av en modell på Azure Kubernetes service (AKS). AKS-klustret tillhandahåller en GPU-resurs som används av modellen för härledning.

Härlednings-eller modell poängsättning är den fas där den distribuerade modellen används för att skapa förutsägelser. Att använda GPU: er i stället för processorer ger prestanda för delar med mycket kan göras parallella beräkning.

> [!IMPORTANT]
> För webb tjänst distributioner stöds endast GPU-härledning i Azure Kubernetes-tjänsten. För en härledning med en __Machine Learning-pipeline__ stöds endast GPU: er i Azure Machine Learning Compute. Mer information om hur du använder ML-pipelines finns i [Självstudier: bygga en Azure Machine Learning pipeline för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md). 

> [!TIP]
> Även om kodfragmenten i den här artikeln använder en TensorFlow-modell kan du använda informationen för alla Machine Learning-ramverk som stöder GPU: er.

> [!NOTE]
> Informationen i den här artikeln bygger på informationen i artikeln [distribuera till Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md) . Där artikeln i allmänhet täcker distribution till AKS, behandlar den här artikeln GPU-bestämd distribution.

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* En python-utvecklings miljö med Azure Machine Learning SDK installerat. Mer information finns i [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).  

* En registrerad modell som använder en GPU.

    * Information om hur du registrerar modeller finns i [Distribuera modeller](how-to-deploy-and-where.md#registermodel).

    * Information om hur du skapar och registrerar Tensorflow-modellen som används för att skapa det här dokumentet finns i [så här tränar du en Tensorflow modell](how-to-train-tensorflow.md).

* En allmän förståelse för [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

Använd följande kod för att ansluta till en befintlig arbets yta:

> [!IMPORTANT]
> Det här kodfragmentet förväntar sig att arbets ytans konfiguration sparas i den aktuella katalogen eller dess överordnade. Mer information om hur du skapar en arbets yta finns i [skapa och hantera Azure Machine Learning arbets ytor](how-to-manage-workspace.md).   Mer information om hur du sparar konfigurationen till filen finns i [skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Skapa ett Kubernetes-kluster med GPU: er

Azure Kubernetes-tjänsten erbjuder många olika GPU-alternativ. Du kan använda dem för modellens härledning. Se [listan över virtuella datorer i N-serien](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) för en fullständig analys av funktioner och kostnader.

Följande kod visar hur du skapar ett nytt AKS-kluster för din arbets yta:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure kommer att debiteras så länge AKS-klustret finns. Se till att ta bort ditt AKS-kluster när du är klar.

Mer information om hur du använder AKS med Azure Machine Learning finns i [distribuera till Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Skriv start skriptet

Inmatnings skriptet tar emot data som skickats till webb tjänsten, skickar dem till modellen och returnerar poängsättnings resultatet. Följande skript läser in Tensorflow-modellen vid start och använder sedan modellen för att räkna data.

> [!TIP]
> Startskriptet är specifikt för din modell. Skriptet måste till exempel känna till ramverket som ska användas med din modell, data format osv.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Den här filen heter `score.py` . Mer information om Entry-skript finns i [hur och var du ska distribuera](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definiera Conda-miljön

Miljö filen Conda anger beroenden för tjänsten. Den innehåller beroenden som krävs av både modellen och Entry-skriptet. Observera att du måste ange azureml-defaults med version >= 1.0.45 som ett pip-beroende, eftersom det innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst. I följande YAML definieras miljön för en Tensorflow-modell. Den anger `tensorflow-gpu` , som använder den GPU som används i den här distributionen:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

I det här exemplet sparas filen som `myenv.yml` .

## <a name="define-the-deployment-configuration"></a>Definiera distributions konfigurationen

> [!IMPORTANT]
> AKS tillåter inte att poddar delar GPU: er, du kan bara ha så många repliker av en GPU-aktiverad webb tjänst som det finns GPU: er i klustret.

Distributions konfigurationen definierar den Azure Kubernetes service-miljö som används för att köra webb tjänsten:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Mer information finns i referens dokumentationen för [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definiera konfigurationen för konfigurations störningar

Konfigurationen pekar på Start-och miljö objekt, som använder en Docker-avbildning med GPU-stöd. Observera att YAML-filen som används för miljö definitionen måste visa azureml-default med version >= 1.0.45 som ett pip-beroende, eftersom den innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).
Mer information finns i referens dokumentationen för [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig).

## <a name="deploy-the-model"></a>Distribuera modellen

Distribuera modellen till ditt AKS-kluster och vänta tills den har skapat din tjänst.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Mer information finns i referens dokumentationen för- [modellen](/python/api/azureml-core/azureml.core.model.model).

## <a name="issue-a-sample-query-to-your-service"></a>Skicka en exempel fråga till din tjänst

Skicka en test fråga till den distribuerade modellen. När du skickar en JPEG-bild till modellen, visas bilden. Följande kod exempel hämtar test data och väljer sedan en slumpmässig test avbildning som ska skickas till tjänsten.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Mer information om hur du skapar ett klient program finns i [skapa klient för att använda distribuerad webb tjänst](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Rensa resurserna

Om du har skapat AKS-klustret specifikt för det här exemplet tar du bort dina resurser när du är klar.

> [!IMPORTANT]
> Azure-räkningar baseras på hur länge AKS-klustret distribueras. Se till att rensa upp den när du är klar.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nästa steg

* [Distribuera modell på FPGA](how-to-deploy-fpga-web-service.md)
* [Distribuera modell med ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Träna Tensorflow DNN-modeller](how-to-train-tensorflow.md)