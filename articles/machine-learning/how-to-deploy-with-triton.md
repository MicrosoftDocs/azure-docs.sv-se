---
title: Modell med höga prestanda med Triton (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig att distribuera din modell med NVIDIA Triton-Härlednings server i Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: c5db04a673c1cdc0c0f24e128f340f4ae55fea81
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825509"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Högpresterande tjänster med Triton-Härlednings Server (för hands version) 

Lär dig hur du använder [NVIDIA Triton-härlednings Server](https://developer.nvidia.com/nvidia-triton-inference-server) för att förbättra prestanda för den webb tjänst som används för modellens härledning.

Ett sätt att distribuera en modell för härledning är som en webb tjänst. Till exempel en distribution till Azure Kubernetes-tjänsten eller Azure Container Instances. Som standard använder Azure Machine Learning en enkel tråds entråds webb ramverk för *generell användning* av webb tjänst distributioner.

Triton är ett ramverk som är *optimerat för en härledning*. Den ger bättre användning av GPU: er och mer kostnads effektiv härledning. På Server sidan batchar batcherar den inkommande begär Anden och skickar dessa batchar för att kunna bli mer konsekvent. Batching bättre använder GPU-resurser och är en viktig del av Triton-prestandan.

> [!IMPORTANT]
> Att använda Triton för distribution från Azure Machine Learning är för närvarande en för __hands version__. Förhands gransknings funktioner kanske inte omfattas av kund support. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna

> [!TIP]
> Kodfragmenten i det här dokumentet är i exempel syfte och kanske inte visar en komplett lösning. Information om hur du använder exempel kod finns i [Azure Machine Learning från slut punkt till slut punkt för Triton i](https://github.com/Azure/azureml-examples/tree/main/tutorials).

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Om du inte har en sådan kan du prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* Bekanta dig med [hur och var du distribuerar en modell](how-to-deploy-and-where.md) med Azure Machine Learning.
* [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/?view=azure-ml-py) **eller** [Azure CLI](/cli/azure/?view=azure-cli-latest) och [Machine Learning-tillägget](reference-azure-machine-learning-cli.md).
* En fungerande installation av Docker för lokal testning. Information om hur du installerar och validerar Docker finns i [orientering och konfiguration](https://docs.docker.com/get-started/) i Docker-dokumentationen.

## <a name="architectural-overview"></a>Översikt över arkitekturen

Innan du försöker använda Triton för din egen modell är det viktigt att du förstår hur den fungerar med Azure Machine Learning och hur den jämförs med en standard distribution.

**Standard distribution utan Triton**

* Flera [Gunicorn](https://gunicorn.org/) -arbetskrafter börjar samtidigt hantera inkommande begär Anden.
* Dessa arbetare hanterar för bearbetning, anropar modellen och efter bearbetning. 
* Härlednings begär Anden använder __poängsättnings-URI__. Ett exempel är `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Normalt, icke-Triton, distributions arkitektur diagram":::

### <a name="setting-the-number-of-workers"></a>Ange antalet arbetare

Ställ in miljövariabeln för att ange antalet arbetare i distributionen `WORKER_COUNT` . Med tanke på att du har ett [miljö](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) objekt `env` som kallas kan du göra följande:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

På så sätt kan Azure ML öka antalet arbets tagare som du anger.


**Distribution av konfigurations härledning med Triton**

* Flera [Gunicorn](https://gunicorn.org/) -arbetskrafter börjar samtidigt hantera inkommande begär Anden.
* Begär Anden vidarebefordras till Triton- **servern**. 
* Triton bearbetar förfrågningar i batchar för att maximera GPU-användningen.
* Klienten använder bedömnings- __URI: n__ för att göra förfrågningar. Ett exempel är `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Inferenceconfig-distribution med Triton":::

Arbets flödet för att använda Triton för din modell distribution är:

1. Kontrol lera att Triton kan hantera din modell.
1. Verifiera att du kan skicka förfrågningar till din Triton-distribuerade modell.
1. Inkludera din Triton kod i din AML-distribution.

## <a name="verify-that-triton-can-serve-your-model"></a>Kontrol lera att Triton kan hantera din modell

Börja med att följa stegen nedan för att kontrol lera att Triton-servern kan hantera din modell.

### <a name="optional-define-a-model-config-file"></a>Valfritt Definiera en modell konfigurations fil

Modell konfigurations filen anger Triton hur många indata som ska förväntas och vilka dimensioner dessa indata kommer att vara. Mer information om hur du skapar konfigurations filen finns i [modell konfiguration](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) i NVIDIA-dokumentationen.

> [!TIP]
> Vi använder `--strict-model-config=false` alternativet när du startar Triton-härlednings servern, vilket innebär att du inte behöver ange en `config.pbtxt` fil för ONNX-eller TensorFlow-modeller.
> 
> Mer information om det här alternativet finns i [genererad modell konfiguration](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) i NVIDIA-dokumentationen.

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
> Den här katalog strukturen är en Triton modell databas som krävs för att din eller dina modeller ska fungera med Triton. Mer information finns i [Triton-modell Arkiv](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) i NVIDIA-dokumentationen.

### <a name="test-with-triton-and-docker"></a>Testa med Triton och Docker

Om du vill testa din modell för att kontrol lera att den körs med Triton kan du använda Docker. Följande kommandon hämtar behållaren Triton till den lokala datorn och startar sedan Triton-servern:

1. Använd följande kommando för att hämta avbildningen för Triton-servern till den lokala datorn:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Starta Triton-servern genom att använda följande kommando. Ersätt `<path-to-models/triton>` med sökvägen till den Triton modell databas som innehåller dina modeller:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Om du använder Windows kan du uppmanas att tillåta nätverks anslutningar till den här processen första gången du kör kommandot. I så fall väljer du för att aktivera åtkomst.

    När den har startats loggas information som liknar följande text i kommando raden:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    Den första raden anger webb tjänstens adress. I det här fallet, `0.0.0.0:8000` som är samma som `localhost:8000` .

1. Använd ett verktyg som till exempel vändning för att komma åt hälso slut punkten.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    Det här kommandot returnerar information som liknar följande. Obs! `200 OK` denna status innebär att webb servern körs.

    ```bash
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

Utöver en grundläggande hälso kontroll kan du skapa en-klient för att skicka data till Triton för att få en härledning. Mer information om hur du skapar en klient finns i [klient exemplen](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) i NVIDIA-dokumentationen. Det finns också [python-exempel på Triton-GitHub](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples).

Mer information om hur du kör Triton med Docker finns i [köra Triton på ett system med en GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) och [köra Triton på ett system utan GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

### <a name="register-your-model"></a>Registrera din modell

Nu när du har kontrollerat att modellen fungerar med Triton kan du registrera den med Azure Machine Learning. Modell registreringen lagrar dina modellvariabler i Azure Machine Learning-arbetsytan och används när du distribuerar med python SDK och Azure CLI.

Följande exempel visar hur du registrerar en eller flera modeller:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="verify-you-can-call-into-your-model"></a>Verifiera att du kan anropa din modell

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

## <a name="redeploy-with-an-inference-configuration"></a>Distribuera igen med en konfigurations konfiguration

Med en konfiguration för konfigurations störningar kan du använda ett Entry-skript och Azure Machine Learning distributions processen med python SDK eller Azure CLI.

> [!IMPORTANT]
> Du måste ange den `AzureML-Triton` [granskade miljön](./resource-curated-environments.md).
>
> Python-kod exemplet klonas `AzureML-Triton` i en annan miljö som kallas `My-Triton` . Azure CLI-koden använder också den här miljön. Mer information om kloning av en miljö finns i referens för [Environment. clone ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) .

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!TIP]
> Mer information om hur du skapar en konfigurations konfiguration finns i [schemat för konfiguration av energischemat](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

När distributionen är klar visas bedömnings-URI: n. För den här lokala distributionen är det `http://localhost:6789/score` . Om du distribuerar till molnet kan du använda kommandot [AZ ml-tjänsten show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI för att hämta bedömnings-URI: n.

Information om hur du skapar en klient som skickar en uppräknings förfrågan till bedömnings-URI finns i [använda en modell som distribueras som en webb tjänst](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Rensa resurser

Använd något av följande alternativ om du planerar att fortsätta använda Azure Machine Learning arbets yta, men vill ta bort den distribuerade tjänsten:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>Nästa steg

* [Se exempel från slut punkt till slut punkt på Triton i Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Kolla [Triton client-exempel](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* Läs [dokumentationen om Triton-härlednings Server](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)