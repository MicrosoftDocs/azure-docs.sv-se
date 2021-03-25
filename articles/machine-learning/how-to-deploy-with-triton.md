---
title: Modell med höga prestanda med Triton (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig att distribuera din modell med NVIDIA Triton-Härlednings server i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 0bb17ded6822c477fe2107c66711af5e2dc384d3
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107847"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Högpresterande tjänster med Triton-Härlednings Server (för hands version) 

Lär dig hur du använder [NVIDIA Triton-härlednings Server](https://aka.ms/nvidia-triton-docs) för att förbättra prestanda för den webb tjänst som används för modellens härledning.

Ett sätt att distribuera en modell för härledning är som en webb tjänst. Till exempel en distribution till Azure Kubernetes-tjänsten eller Azure Container Instances. Som standard använder Azure Machine Learning en enkel tråds entråds webb ramverk för *generell användning* av webb tjänst distributioner.

Triton är ett ramverk som är *optimerat för en härledning*. Den ger bättre användning av GPU: er och mer kostnads effektiv härledning. På Server sidan batchar batcherar den inkommande begär Anden och skickar dessa batchar för att kunna bli mer konsekvent. Batching bättre använder GPU-resurser och är en viktig del av Triton-prestandan.

> [!IMPORTANT]
> Att använda Triton för distribution från Azure Machine Learning är för närvarande en för __hands version__. Förhands gransknings funktioner kanske inte omfattas av kund support. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna

> [!TIP]
> Kodfragmenten i det här dokumentet är i exempel syfte och kanske inte visar en komplett lösning. Information om hur du använder exempel kod finns i [Azure Machine Learning från slut punkt till slut punkt för Triton i](https://aka.ms/triton-aml-sample).

> [!NOTE]
> [NVIDIA Triton-härlednings Server](https://aka.ms/nvidia-triton-docs) är en tredjeparts program vara från tredje part som är integrerad i Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* Bekanta dig med [hur och var du distribuerar en modell](how-to-deploy-and-where.md) med Azure Machine Learning.
* [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/) **eller** [Azure CLI](/cli/azure/) och [Machine Learning-tillägget](reference-azure-machine-learning-cli.md).
* En fungerande installation av Docker för lokal testning. Information om hur du installerar och validerar Docker finns i [orientering och konfiguration](https://docs.docker.com/get-started/) i Docker-dokumentationen.

## <a name="architectural-overview"></a>Översikt över arkitekturen

Innan du försöker använda Triton för din egen modell är det viktigt att du förstår hur den fungerar med Azure Machine Learning och hur den jämförs med en standard distribution.

**Standard distribution utan Triton**

* Flera [Gunicorn](https://gunicorn.org/) -arbetskrafter börjar samtidigt hantera inkommande begär Anden.
* Dessa arbetare hanterar för bearbetning, anropar modellen och efter bearbetning. 
* Klienter använder __Azure ml-bedömnings-URI__. Till exempel `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normalt, icke-Triton, distributions arkitektur diagram":::

**Distribuera med Triton direkt**

* Begär Anden går direkt till Triton-servern.
* Triton bearbetar förfrågningar i batchar för att maximera GPU-användningen.
* Klienten använder Triton- __URI: n__ för att göra förfrågningar. Till exempel `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferenceconfig-distribution med enbart Triton och inga python-mellanprogram":::

**Distribution av konfigurations härledning med Triton**

* Flera [Gunicorn](https://gunicorn.org/) -arbetskrafter börjar samtidigt hantera inkommande begär Anden.
* Begär Anden vidarebefordras till Triton- **servern**. 
* Triton bearbetar förfrågningar i batchar för att maximera GPU-användningen.
* Klienten använder __Azure ml-bedömnings-URI: n__ för att göra förfrågningar. Till exempel `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Distribution med Triton och python mellanprogram":::

Arbets flödet för att använda Triton för din modell distribution är:

1. Hantera din modell med Triton direkt.
1. Verifiera att du kan skicka förfrågningar till din Triton-distribuerade modell.
1. Valfritt Skapa ett lager med python-mellanprogram för för-och efter bearbetning på Server Sidan

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Distribuera Triton utan python för-och efter bearbetning

Börja med att följa stegen nedan för att kontrol lera att Triton-servern kan hantera din modell.

### <a name="optional-define-a-model-config-file"></a>Valfritt Definiera en modell konfigurations fil

Modell konfigurations filen anger Triton hur många indata som ska förväntas och vilka dimensioner dessa indata kommer att vara. Mer information om hur du skapar konfigurations filen finns i [modell konfiguration](https://aka.ms/nvidia-triton-docs) i NVIDIA-dokumentationen.

> [!TIP]
> Vi använder `--strict-model-config=false` alternativet när du startar Triton-härlednings servern, vilket innebär att du inte behöver ange en `config.pbtxt` fil för ONNX-eller TensorFlow-modeller.
> 
> Mer information om det här alternativet finns i [genererad modell konfiguration](https://aka.ms/nvidia-triton-docs) i NVIDIA-dokumentationen.

### <a name="use-the-correct-directory-structure"></a>Använd rätt katalog struktur

När du registrerar en modell med Azure Machine Learning kan du registrera antingen enskilda filer eller en katalog struktur. För att kunna använda Triton måste modell registreringen vara för en katalog struktur som innehåller en katalog med namnet `triton` . Den allmänna strukturen för den här katalogen är:

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
> Den här katalog strukturen är en Triton modell databas som krävs för att din eller dina modeller ska fungera med Triton. Mer information finns i [Triton-modell Arkiv](https://aka.ms/nvidia-triton-docs) i NVIDIA-dokumentationen.

### <a name="register-your-triton-model"></a>Registrera din Triton-modell

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Mer information finns i `az ml model register` [referens dokumentationen](/cli/azure/ext/azure-cli-ml/ml/model).

När modellen registreras i Azure Machine Learning måste värdet för `--model-path  -p` parametern vara namnet på den överordnade mappen för Triton.  
I exemplet ovan  `--model-path` är models.

Värdet för `--name  -n` parametern my_triton_model i exemplet är modell namnet som är känt för Azure Machine Learning-arbetsyta. 

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
Mer information finns i dokumentationen för [modell klassen](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Distribuera din modell

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Om du har ett GPU-aktiverat Azure Kubernetes service-kluster med namnet "AKS-GPU" som skapats via Azure Machine Learning kan du använda följande kommando för att distribuera din modell.

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

I [den här dokumentationen finns mer information om hur du distribuerar modeller](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Anropa din distribuerade modell

Börja med att hämta din bedömnings-URI och Bearer-token.

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

Kontrol lera sedan att tjänsten körs genom att göra följande: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Det här kommandot returnerar information som liknar följande. Obs! `200 OK` denna status innebär att webb servern körs.

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

När du har utfört en hälso kontroll kan du skapa en-klient för att skicka data till Triton för att få en härledning. Mer information om hur du skapar en klient finns i [klient exemplen](https://aka.ms/nvidia-client-examples) i NVIDIA-dokumentationen. Det finns också [python-exempel på Triton-GitHub](https://aka.ms/nvidia-triton-docs).

Om du nu inte vill lägga till python för-och efter bearbetning i den distribuerade webb tjänsten kan du göra det. Läs vidare om du vill lägga till den här logiken för efter bearbetning.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Valfritt Distribuera igen med ett Python-skript för för-och efter bearbetning

När du har verifierat att Triton kan hantera din modell kan du lägga till för-och efter bearbetnings kod genom att definiera ett _Entry-skript_. Den här filen heter `score.py` . Mer information om Entry-skript finns i [definiera ett post skript](how-to-deploy-and-where.md#define-an-entry-script).

De två huvudsakliga stegen är att initiera en Triton HTTP-klient i din `init()` metod och för att anropa klienten i din `run()` funktion.

### <a name="initialize-the-triton-client"></a>Initiera Triton-klienten

Inkludera kod som i följande exempel i `score.py` filen. Triton i Azure Machine Learning förväntas adresseras på localhost, Port 8000. I det här fallet finns localhost i Docker-avbildningen för distributionen, inte en port på den lokala datorn:

> [!TIP]
> `tritonhttpclient`Pip-paketet ingår i den granskade `AzureML-Triton` miljön, så du behöver inte ange det som ett pip-beroende.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Ändra bedömnings skriptet för att anropa Triton

Följande exempel visar hur du dynamiskt begär metadata för modellen:

> [!TIP]
> Du kan dynamiskt begära metadata för modeller som har lästs in med Triton med hjälp av `.get_model_metadata` metoden för Triton-klienten. I [exempel antecknings boken](https://aka.ms/triton-aml-sample) finns ett exempel på hur det används.

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

### <a name="redeploy-with-an-inference-configuration"></a>Distribuera igen med en konfigurations konfiguration

Med en konfiguration för konfigurations störningar kan du använda ett Entry-skript och Azure Machine Learning distributions processen med python SDK eller Azure CLI.

> [!IMPORTANT]
> Du måste ange den `AzureML-Triton` [granskade miljön](./resource-curated-environments.md).
>
> Python-kod exemplet klonas `AzureML-Triton` i en annan miljö som kallas `My-Triton` . Azure CLI-koden använder också den här miljön. Mer information om kloning av en miljö finns i referens för [Environment. clone ()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) .

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

> [!TIP]
> Mer information om hur du skapar en konfigurations konfiguration finns i [schemat för konfiguration av energischemat](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

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

När distributionen är klar visas bedömnings-URI: n. För den här lokala distributionen är det `http://localhost:6789/score` . Om du distribuerar till molnet kan du använda kommandot [AZ ml-tjänsten show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI för att hämta bedömnings-URI: n.

Information om hur du skapar en klient som skickar en uppräknings förfrågan till bedömnings-URI finns i [använda en modell som distribueras som en webb tjänst](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Ange antalet arbetare

Ställ in miljövariabeln för att ange antalet arbetare i distributionen `WORKER_COUNT` . Med tanke på att du har ett [miljö](/python/api/azureml-core/azureml.core.environment.environment) objekt `env` som kallas kan du göra följande:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

På så sätt kan Azure ML öka antalet arbets tagare som du anger.


## <a name="clean-up-resources"></a>Rensa resurser

Använd något av följande alternativ om du planerar att fortsätta använda Azure Machine Learning arbets yta, men vill ta bort den distribuerade tjänsten:


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

* [Felsök en misslyckad distribution](how-to-troubleshoot-deployment.md), lär dig att felsöka och lösa eller kringgå vanliga fel som kan uppstå när du distribuerar en modell.

* Om distributions loggar visar att **TritonServer inte kunde starta**, se [NVIDIA: s dokumentation om öppen källkod.](https://github.com/triton-inference-server/server)

## <a name="next-steps"></a>Nästa steg

* [Se exempel från slut punkt till slut punkt på Triton i Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Kolla [Triton client-exempel](https://aka.ms/nvidia-client-examples)
* Läs [dokumentationen om Triton-härlednings Server](https://aka.ms/nvidia-triton-docs)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)