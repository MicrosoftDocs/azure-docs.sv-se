---
title: Högpresterande modell med Triton (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig att distribuera din modell med NVIDIA Triton Inference Server i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: 9b7b8fe9c05d0de64dcd0cf7c6c324e0d03cb1ac
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874161"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Högpresterande tjänst med Triton Inference Server (förhandsversion) 

Lär dig hur du [använder NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) för att förbättra prestanda för webbtjänsten som används för modell inferens.

Ett sätt att distribuera en modell för slutsatsledning är som en webbtjänst. Till exempel en distribution till Azure Kubernetes Service eller Azure Container Instances. Som standard Azure Machine Learning ett enkeltrådat *webbramverk för* webbtjänstdistributioner.

Triton är ett ramverk som är *optimerat för slutsatsledning.* Det ger bättre användning av GPU:er och mer kostnadseffektiv slutsatsledning. På serversidan batchar den inkommande begäranden och skickar dessa batchar för slutsatsledning. Batchbearbetning utnyttjar GPU-resurser bättre och är en viktig del av Tritons prestanda.

> [!IMPORTANT]
> Triton för distribution från Azure Machine Learning för närvarande i __förhandsversion.__ Förhandsgranskningsfunktioner kanske inte omfattas av kundsupporten. Mer information finns i Kompletterande [villkor för användning av Microsoft Azure förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Kodfragmenten i det här dokumentet är avsedda som exempel och kanske inte visar en fullständig lösning. Exempelkod för arbete finns i exempel [från slutet till slut av Triton i Azure Machine Learning](https://aka.ms/triton-aml-sample).

> [!NOTE]
> [NVIDIA Triton Inference Server](https://aka.ms/nvidia-triton-docs) är en programvara från tredje part med öppen källkod som är integrerad i Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har någon kan du prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* Kunskaper om hur [och var du distribuerar en modell](how-to-deploy-and-where.md) med Azure Machine Learning.
* Den [Azure Machine Learning SDK för Python eller](/python/api/overview/azure/ml/)  Azure [CLI och](/cli/azure/) [maskininlärningstillägget](reference-azure-machine-learning-cli.md).
* En fungerande installation av Docker för lokal testning. Information om hur du installerar och validerar Docker finns i [Orientering och installation](https://docs.docker.com/get-started/) i Docker-dokumentationen.

## <a name="architectural-overview"></a>Översikt över arkitekturen

Innan du försöker använda Triton för din egen modell är det viktigt att förstå hur det fungerar med Azure Machine Learning och hur det kan jämföras med en standarddistribution.

**Standarddistribution utan Triton**

* Flera [Gunicorn-arbetare](https://gunicorn.org/) startas för att hantera inkommande begäranden samtidigt.
* De här arbetarna hanterar förbearbetning, anropar modellen och efterbearbetning. 
* Klienter använder __Azure ML-bedömnings-URI:t__. Till exempel `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normalt diagram över distributionsarkitekturer som inte är tritona":::

**Distribuera direkt med Triton**

* Begäranden går direkt till Triton-servern.
* Triton bearbetar begäranden i batchar för att maximera GPU-användningen.
* Klienten använder __Triton-URI:en__ för att göra begäranden. Till exempel `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferenceconfig-distribution endast med Triton och inget Python-mellanprogram":::

**Distribution av slutsatsledningskonfiguration med Triton**

* Flera [Gunicorn-arbetare](https://gunicorn.org/) startas för att hantera inkommande begäranden samtidigt.
* Begäranden vidarebefordras till **Triton-servern**. 
* Triton bearbetar begäranden i batchar för att maximera GPU-användningen.
* Klienten använder Azure __ML-bedömnings-URI för__ att göra begäranden. Till exempel `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Distribution med Triton och Python-mellanprogram":::

Arbetsflödet för att använda Triton för din modelldistribution är:

1. Betjäna din modell direkt med Triton.
1. Kontrollera att du kan skicka begäranden till din Triton-distribuerade modell.
1. (Valfritt) Skapa ett lager med Python-mellanprogram för för- och efterbearbetning på serversidan

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Distribuera Triton utan Python för- och efterbearbetning

Börja med att följa stegen nedan för att kontrollera att Triton Inference Server kan betjäna din modell.

### <a name="optional-define-a-model-config-file"></a>(Valfritt) Definiera en modellkonfigurationsfil

Modellkonfigurationsfilen talar om för Triton hur många indata som ska förväntas och vilka dimensioner dessa indata kommer att vara. Mer information om hur du skapar konfigurationsfilen finns i [Modellkonfiguration](https://aka.ms/nvidia-triton-docs) i NVIDIA-dokumentationen.

> [!TIP]
> Vi använder alternativet när du startar Triton Inference Server, vilket innebär att du inte behöver ange en fil för `--strict-model-config=false` `config.pbtxt` ONNX- eller TensorFlow-modeller.
> 
> Mer information om det här alternativet finns i [Genererad modellkonfiguration](https://aka.ms/nvidia-triton-docs) i NVIDIA-dokumentationen.

### <a name="use-the-correct-directory-structure"></a>Använda rätt katalogstruktur

När du registrerar en modell Azure Machine Learning kan du registrera antingen enskilda filer eller en katalogstruktur. Om du vill använda Triton måste modellregistreringen vara för en katalogstruktur som innehåller en katalog med namnet `triton` . Den allmänna strukturen för den här katalogen är:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
            - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Den här katalogstrukturen är en Triton-modelldatabas och krävs för att dina modeller ska fungera med Triton. Mer information finns i [Triton Model Repositories](https://aka.ms/nvidia-triton-docs) i NVIDIA-dokumentationen.

### <a name="register-your-triton-model"></a>Registrera din Triton-modell

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Mer information om `az ml model register` finns i [referensdokumentationen](/cli/azure/ml/model).

När du registrerar modellen Azure Machine Learning måste värdet för parametern vara namnet på den `--model-path  -p` överordnade mappen i Triton.  
I exemplet ovan är  `--model-path` "modeller".

Värdet för parametern â€my_triton_modelâ™ i exemplet är det modellnamn som är känt `--name  -n` för Azure Machine Learning-arbetsyta. 

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Mer information finns i dokumentationen för [modellklassen](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Distribuera din modell

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Om du har ett GPU-aktiverat Azure Kubernetes Service kluster med namnet "aks-gpu" som skapats via Azure Machine Learning kan du använda följande kommando för att distribuera din modell.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

Mer [information om hur du distribuerar modeller finns i den här dokumentationen.](how-to-deploy-and-where.md)

### <a name="call-into-your-deployed-model"></a>Anropa din distribuerade modell

Hämta först din bedömnings-URI och bearer-token.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Kontrollera sedan att tjänsten körs genom att göra följande: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Det här kommandot returnerar information som liknar följande. Observera `200 OK` . Den här statusen innebär att webbservern körs.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

När du har utfört en hälsokontroll kan du skapa en klient som skickar data till Triton för slutsatsledning. Mer information om hur du skapar en klient finns i [klientexempel i](https://aka.ms/nvidia-client-examples) NVIDIA-dokumentationen. Det finns även [Python-exempel på Triton GitHub](https://aka.ms/nvidia-triton-docs).

Om du inte vill lägga till Python för- och efterbearbetning till den distribuerade webbtjänsten kan du vara klar. Om du vill lägga till den här för- och efterbearbetningslogiken läser du vidare.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>(Valfritt) Distribuera om med ett Python-startskript för för- och efterbearbetning

När du har verifierat att Triton kan hantera din modell kan du lägga till kod för för- och efterbearbetning genom att definiera ett _inmatningsskript_. Den här filen heter `score.py` . Mer information om inmatningsskript finns i [Definiera ett inmatningsskript.](how-to-deploy-and-where.md#define-an-entry-script)

De två huvudstegen är att initiera en Triton HTTP-klient i metoden och anropa `init()` klienten i din `run()` funktion.

### <a name="initialize-the-triton-client"></a>Initiera Triton-klienten

Inkludera kod som i följande exempel i `score.py` filen. Triton i Azure Machine Learning förväntar sig att åtgärdas på localhost, port 8000. I det här fallet finns localhost i Docker-avbildningen för den här distributionen, inte en port på den lokala datorn:

> [!TIP]
> `tritonhttpclient`Pip-paketet ingår i den curated `AzureML-Triton` -miljön, så du behöver inte ange det som ett pip-beroende.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Ändra ditt bedömningsskript för att anropa Triton

Följande exempel visar hur du dynamiskt begär metadata för modellen:

> [!TIP]
> Du kan dynamiskt begära metadata för modeller som har lästs in med Triton med hjälp av `.get_model_metadata` metoden i Triton-klienten. Ett exempel på hur notebook-exempelanteckningsboken används finns i exempelanteckningsboken. [](https://aka.ms/triton-aml-sample)

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Distribuera om med en inferenskonfiguration

Med en inferenskonfiguration kan du använda ett inmatningsskript samt Azure Machine Learning distributionsprocessen med hjälp av Python SDK eller Azure CLI.

> [!IMPORTANT]
> Du måste ange den `AzureML-Triton` [curated environment (den curated environment).](./resource-curated-environments.md)
>
> Python-kodexempel klonar till `AzureML-Triton` en annan miljö som heter `My-Triton` . Azure CLI-koden använder också den här miljön. Mer information om kloning av en miljö finns i [referensen Environment.Clone().](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> Mer information om hur du skapar en inferenskonfiguration finns i [konfigurationsschemat för slutsatsledning.](./reference-azure-machine-learning-cli.md#inference-configuration-schema)

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

När distributionen är klar visas bedömnings-URI:en. För den här lokala distributionen blir det `http://localhost:6789/score` . Om du distribuerar till molnet kan du använda [CLI-kommandot az ml service show](/cli/azure/ml/service#az_ml_service_show) för att hämta bedömnings-URI:en.

Information om hur du skapar en klient som skickar inferensbegäranden till bedömnings-URI finns i använda en modell som [distribueras som en webbtjänst](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Ange antalet arbetare

Ange antalet arbetare i distributionen genom att ange miljövariabeln `WORKER_COUNT` . Med tanke på att [du har ett](/python/api/azureml-core/azureml.core.environment.environment) `env` miljöobjekt med namnet kan du göra följande:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Detta innebär att Azure ML ska skapa det antal arbetare som du anger.


## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att använda Azure Machine Learning-arbetsytan, men vill ta bort den distribuerade tjänsten, använder du något av följande alternativ:


# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---
## <a name="troubleshoot"></a>Felsöka

* [Felsök en misslyckad](how-to-troubleshoot-deployment.md)distribution, lär dig hur du felsöker och löser eller löser vanliga fel som kan uppstå när du distribuerar en modell.

* Om distributionsloggar visar **att TritonServer inte** kunde starta kan du läsa [dokumentationen ™ Nvidiaâ™ öppen källkod.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Nästa steg

* [Se exempel från slutet av Triton i Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Kolla in [Triton-klientexempel](https://aka.ms/nvidia-client-examples)
* Läs dokumentationen [för Triton Inference Server](https://aka.ms/nvidia-triton-docs)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
