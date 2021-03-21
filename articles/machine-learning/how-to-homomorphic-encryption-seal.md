---
title: Distribuera en krypterad inferencing-tjänst (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Microsoft SEAL för att distribuera en krypterad förutsägelse tjänst för bild klassificering
author: luisquintanilla
ms.author: luquinta
ms.date: 07/09/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, responsible-ml
ms.openlocfilehash: a2c8b765f109408b8cb08f78a4b3ad2283f35eb9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223592"
---
# <a name="how-to-deploy-an-encrypted-inferencing-web-service-preview"></a>Så här distribuerar du en krypterad inferencing-webb tjänst (för hands version)

Lär dig hur du distribuerar en bild klassificerings modell som en krypterad inferencing-webbtjänst i [Azure Container instances](../container-instances/index.yml) (ACI). Webb tjänsten är en Docker-behållar avbildning som innehåller modellen och bedömnings logiken.

I den här hand boken använder du Azure Machine Learning-tjänsten för att:

> [!div class="checklist"]
> * Konfigurera dina miljöer
> * Distribuera krypterad inferencing-webbtjänst
> * Förbered test data
> * Gör krypterade förutsägelser
> * Rensa resurser

ACI är en bra lösning för att testa och förstå arbets flödet för modell distribution. För skalbara produktionsdistributioner kan du använda Azure Kubernetes Service. Mer information finns i [Hur och var man distribuerar](./how-to-deploy-and-where.md).

Krypterings metoden som används i det här exemplet är [homomorphic-kryptering](https://github.com/Microsoft/SEAL#homomorphic-encryption). Homomorphic-kryptering gör det möjligt att utföra beräkningar på krypterade data utan att behöva åtkomst till en hemlig nyckel (dekryptering). Resultatet av beräkningarna krypteras och kan bara visas av ägaren av den hemliga nyckeln. 

## <a name="prerequisites"></a>Förutsättningar

Den här guiden förutsätter att du har en modell för bild klassificering som registrerats i Azure Machine Learning. Om inte, registrera modellen med en [förtränad modell](https://github.com/Azure/MachineLearningNotebooks/raw/master/tutorials/image-classification-mnist-data/sklearn_mnist_model.pkl) eller skapa en egen genom att slutföra [en bild klassificerings modell med Azure Machine Learning själv studie kursen](tutorial-train-models-with-aml.md).

## <a name="configure-local-environment"></a>Konfigurera lokal miljö

I en Jupyter Notebook

1. Importera python-paketen som krävs för det här exemplet.

    ```python
    %matplotlib inline
    import numpy as np
    import matplotlib.pyplot as plt

    import azureml.core

    # display the core SDK version number
    print("Azure ML SDK Version: ", azureml.core.VERSION)
    ```

2. Installera homomorphic Encryption Library för säker inferencing.

    > [!NOTE]
    > `encrypted-inference`Paketet är för närvarande en för hands version.

    [`encrypted-inference`](https://pypi.org/project/encrypted-inference) är ett bibliotek som innehåller bindningar för krypterade inferencing som baseras på [Microsoft Seal](https://github.com/Microsoft/SEAL).

    ```python
    !pip install encrypted-inference==0.9
    ```

## <a name="configure-the-inferencing-environment"></a>Konfigurera inferencing-miljön

Skapa en miljö för inferencing och Lägg till `encrypted-inference` paket som ett Conda-beroende.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataprep[pandas,fuse]>=1.1.14', 'azureml-defaults', 'azure-storage-blob', 'encrypted-inference==0.9'], conda_packages = ['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace = ws)
```

## <a name="deploy-encrypted-inferencing-web-service"></a>Distribuera krypterad inferencing-webbtjänst

Distribuera modellen som en webb tjänst som finns i ACI.

Skapa rätt miljö för ACI genom att ange följande:

* Ett bedömningsskript som visar hur man ska använda modellen
* En konfigurationsfil för att skapa ACI
* En utbildad modell

### <a name="create-scoring-script"></a>Skapa ett bedömningsskript

Skapa bedömnings skriptet som `score.py` används av webb tjänsten för inferencing.

Du måste inkludera två obligatoriska funktioner i bedömningsskriptet:

* Funktionen `init()`, som vanligtvis läser in modellen i ett globalt objekt. Den här funktionen körs endast en gång när Docker-containern startas.
* Funktionen `run(input_data)` använder modellen till att förutsäga ett värde som baseras på indatan. Indatan och utdatan i körningen använder vanligtvis JSON för serialisering och deserialisering, men andra format stöds också. Funktionen hämtar homomorphic-kryptering baserade offentliga nycklar som överförs av tjänst anroparen.

```python
%%writefile score.py
import json
import os
import pickle
import joblib
from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, PublicAccess
from encrypted.inference.eiserver import EIServer

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

    global server
    server = EIServer(model.coef_, model.intercept_, verbose=True)

def run(raw_data):

    json_properties = json.loads(raw_data)

    key_id = json_properties['key_id']
    conn_str = json_properties['conn_str']
    container = json_properties['container']
    data = json_properties['data']

    # download the public keys from blob storage
    blob_service_client = BlobServiceClient.from_connection_string(conn_str=conn_str)
    blob_client = blob_service_client.get_blob_client(container=container, blob=key_id)
    public_keys = blob_client.download_blob().readall()

    result = {}
    # make prediction
    result = server.predict(data, public_keys)

    # you can return any data type as long as it is JSON-serializable
    return result
```

### <a name="create-configuration-file"></a>Skapa konfigurationsfilen

Skapa en konfigurationsfil för konfigurationen samt ange hur många processorer och gigabyte RAM-minne som behövs till din ACI-container. Även om det beror på din modell, räcker standardvärdet 1 kärna och 1 GB RAM-minne oftast för de flesta modeller. Om du märker att du behöver fler senare måste du återskapa avbildningen och distribuera om tjänsten.

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Encrypted Predict MNIST with sklearn + SEAL')
```

### <a name="deploy-to-azure-container-instances"></a>Distribuera till Azure Container Instances

Beräknad tids åtgång: **cirka 2-5 minuter**

Konfigurera avbildningen och distribuera. Följande kod går igenom de här stegen:

1. Skapa ett miljö objekt som innehåller beroenden som behövs av modellen med hjälp av miljö filen ( `myenv.yml` )
1. Skapa en konfigurations konfiguration som krävs för att distribuera modellen som en webb tjänst med:
   * Bedömningsfilen (`score.py`)
   * Miljö objekt som skapades i föregående steg
1. Distribuera modellen till ACI-behållaren.
1. Hämta webbtjänstens HTTP-slutpunkt.

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

myenv = Environment.get(workspace=ws, name="tutorial-env")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-encrypted-mnist-svc',
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

Hämta bedömningswebbtjänstens HTTP-slutpunkt, som accepterar REST-klientanrop. Den här slutpunkten kan delas med alla som vill testa webbtjänsten eller integrera den i ett program.

```python
print(service.scoring_uri)
```

## <a name="prepare-test-data"></a>Förbered test data

1. Hämta test data. I det här fallet sparas den i en katalog med namnet *data*.

    ```python
    import os
    from azureml.core import Dataset
    from azureml.opendatasets import MNIST
    
    data_folder = os.path.join(os.getcwd(), 'data')
    os.makedirs(data_folder, exist_ok=True)
    
    mnist_file_dataset = MNIST.get_file_dataset()
    mnist_file_dataset.download(data_folder, overwrite=True)
    ```

1. Läs in test data från *data* katalogen.

    ```python
    from utils import load_data
    import os
    import glob
    
    data_folder = os.path.join(os.getcwd(), 'data')
    # note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
    X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
    y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
    ```

## <a name="make-encrypted-predictions"></a>Gör krypterade förutsägelser

Använd test data uppsättningen med modellen för att hämta förutsägelser.

Så här gör du krypterade förutsägelser:

1. Skapa en ny `EILinearRegressionClient` , homomorphic-Encryption-baserad klient och offentliga nycklar.

    ```python
    from encrypted.inference.eiclient import EILinearRegressionClient

    # Create a new Encrypted inference client and a new secret key.
    edp = EILinearRegressionClient(verbose=True)

    public_keys_blob, public_keys_data = edp.get_public_keys()
    ```

1. Ladda upp homomorphic-kryptering genererade offentliga nycklar till standard-BLOB-arkivet för arbets ytan. Detta gör att du kan dela nycklarna med en härlednings Server.

    ```python
    import azureml.core
    from azureml.core import Workspace, Datastore
    import os

    ws = Workspace.from_config()

    datastore = ws.get_default_datastore()
    container_name=datastore.container_name

    # Create a local file and write the keys to it
    public_keys = open(public_keys_blob, "wb")
    public_keys.write(public_keys_data)
    public_keys.close()

    # Upload the file to blob store
    datastore.upload_files([public_keys_blob])

    # Delete the local file
    os.remove(public_keys_blob)
    ```

1. Kryptera test data

    ```python
    #choose any one sample from the test data 
    sample_index = 1

    #encrypt the data
    raw_data = edp.encrypt(X_test[sample_index])

    ```

1. Använd SDK: s `run` API för att anropa tjänsten och ange test data uppsättningen till modellen för att hämta förutsägelser. Vi kommer att behöva skicka anslutnings strängen till blob-lagringen där de offentliga nycklarna laddades upp.

    ```python
    import json
    from azureml.core import Webservice

    service = Webservice(ws, 'sklearn-encrypted-mnist-svc')

    #pass the connection string for blob storage to give the server access to the uploaded public keys 
    conn_str_template = 'DefaultEndpointsProtocol={};AccountName={};AccountKey={};EndpointSuffix=core.windows.net'
    conn_str = conn_str_template.format(datastore.protocol, datastore.account_name, datastore.account_key)

    #build the json 
    data = json.dumps({"data": raw_data, "key_id" : public_keys_blob, "conn_str" : conn_str, "container" : container_name })
    data = bytes(data, encoding='ASCII')

    print ('Making an encrypted inference web service call ')
    eresult = service.run(input_data=data)

    print ('Received encrypted inference results')
    ```

1. Använd-klienten för att dekryptera resultaten.

    ```python
    import numpy as np

    results = edp.decrypt(eresult)

    print ('Decrypted the results ', results)

    #Apply argmax to identify the prediction result
    prediction = np.argmax(results)

    print ( ' Prediction : ', prediction)
    print ( ' Actual Label : ', y_test[sample_index])
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort webb tjänsten som skapats i det här exemplet:

```python
service.delete()
```

Om du inte längre planerar att använda de Azure-resurser som du har skapat tar du bort dem. Detta gör att du inte kan debiteras för resurser som fortfarande används. I den här guiden finns mer information om hur du [rensar resurser](how-to-manage-workspace.md#clean-up-resources) .