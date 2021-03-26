---
title: Så här distribuerar du Machine Learning-modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur och var du kan distribuera Machine Learning-modeller. Distribuera till Azure Container Instances, Azure Kubernetes service, Azure IoT Edge och FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
adobe-target: true
ms.openlocfilehash: 4d2aa4d43fbc8cf9040702afb1877e0271b2eab2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568298"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Distribuera Machine Learning-modeller till Azure

Lär dig hur du distribuerar din Machine Learning-eller djup inlärnings modell som en webb tjänst i Azure-molnet. Du kan också distribuera till Azure IoT Edge enheter.

Arbetsflödet är ungefär likadant var du än distribuerar din modell:

1. Registrera modellen (valfritt, se nedan).
1. Förbered en konfigurations konfiguration (om du inte använder [distribution utan kod](./how-to-deploy-no-code-deployment.md)).
1. Förbered ett Entry-skript (om du inte använder [distribution utan kod](./how-to-deploy-no-code-deployment.md)).
1. Välj ett beräknings mål.
1. Distribuera modellen till beräkningsmålet.
1. Testa den resulterande webb tjänsten.

Mer information om de begrepp som ingår i arbets flödet för Machine Learning-distribution finns i [Hantera, distribuera och övervaka modeller med Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Förutsättningar

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
- En modell. Om du inte har en tränad modell kan du använda den modell och de beroende filer som finns i [den här självstudien](https://aka.ms/azml-deploy-cloud).
- [Tillägget för Azure Command Line Interface (CLI) för tjänsten Machine Learning](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
- En modell. Om du inte har en tränad modell kan du använda den modell och de beroende filer som finns i [den här självstudien](https://aka.ms/azml-deploy-cloud).
- [Azure Machine Learning Software Development Kit (SDK) för python](/python/api/overview/azure/ml/intro).

---

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Följ anvisningarna i Azure CLI-dokumentationen för att [ställa in din prenumerations kontext](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Gör sedan följande:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

för att se de arbets ytor som du har åtkomst till.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Mer information om hur du använder SDK för att ansluta till en arbets yta finns i dokumentationen [för Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro#workspace) .


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Registrera din modell (valfritt)

En registrerad modell är en logisk behållare för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enskild modell i arbets ytan. När du har registrerat filerna kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP] 
> Att registrera en modell för versions spårning rekommenderas, men krävs inte. Om du hellre vill fortsätta utan att registrera en modell måste du ange en käll katalog i [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) eller [inferenceconfig.jspå](./reference-azure-machine-learning-cli.md#inference-configuration-schema) och se till att din modell finns i käll katalogen.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en moln plats (från en utbildnings körning) eller en lokal katalog. Den här sökvägen är bara till för att hitta filerna som ska laddas upp som en del av registrerings processen. Den behöver inte matcha den sökväg som används i Entry-skriptet. Mer information finns i [hitta modell filer i ditt Entry-skript](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> När du använder alternativet filtrera efter `Tags` på sidan modeller i Azure Machine Learning Studio, i stället för att använda `TagName : TagValue` kunder ska använda `TagName=TagValue` (utan blank steg)

Följande exempel visar hur du registrerar en modell.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrera en modell från en Azure ML-utbildning

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path`Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `--asset-path` du sökvägen till en mapp som innehåller filerna.

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Om du vill inkludera flera filer i modell registreringen anger `-p` du sökvägen till en mapp som innehåller filerna.

Mer information finns i `az ml model register` [referens dokumentationen](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrera en modell från en Azure ML-utbildning

  När du använder SDK för att träna en modell kan du ta emot antingen ett [körnings](/python/api/azureml-core/azureml.core.run.run) objekt eller ett [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) -objekt, beroende på hur du tränade modellen. Varje-objekt kan användas för att registrera en modell som skapats av en experiment körning.

  + Registrera en modell från ett `azureml.core.Run` objekt:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Parametern refererar till modellens moln plats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modell registreringen anger `model_path` du sökvägen till en mapp som innehåller filerna. Mer information finns i [Run.register_model](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) -dokumentationen.

  + Registrera en modell från ett `azureml.train.automl.run.AutoMLRun` objekt:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    I det här exemplet har `metric` `iteration` parametrarna och inte angetts, så iterationen med bästa primära mått kommer att registreras. `model_id`Värdet som returneras från körningen används i stället för ett modell namn.

    Mer information finns i [AutoMLRun.register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) -dokumentationen.

    Om du vill distribuera en registrerad modell från en `AutoMLRun` , rekommenderar vi att du gör det genom att [Klicka på knappen distribuera i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model). 
### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

Du kan registrera en modell genom att ange den lokala sökvägen till modellen. Du kan ange sökvägen till antingen en mapp eller en enskild fil. Du kan använda den här metoden för att registrera modeller som har tränats med Azure Machine Learning och sedan laddats ned. Du kan också använda den här metoden för att registrera modeller som har tränats utanför Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Använda SDK och ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Om du vill inkludera flera filer i modell registreringen anger `model_path` du sökvägen till en mapp som innehåller filerna.

Mer information finns i dokumentationen för [modell klassen](/python/api/azureml-core/azureml.core.model.model).

Mer information om hur du arbetar med modeller som har tränats utanför Azure Machine Learning finns i [distribuera en befintlig modell](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Definiera ett post skript

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definiera en konfiguration för en härledning


En konfiguration för en härledning beskriver hur du konfigurerar webb tjänsten som innehåller din modell. Den används senare när du distribuerar modellen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

En minimal konfiguration av en härlednings konfiguration kan skrivas som:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir",
    "environment": {
    "docker": {
        "arguments": [],
        "baseDockerfile": null,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "enabled": false,
        "sharedVolumes": true,
        "shmSize": null
    },
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "name": "my-deploy-env",
    "python": {
        "baseCondaEnvironment": null,
        "condaDependencies": {
            "channels": [
                "conda-forge",
                "pytorch"
            ],
            "dependencies": [
                "python=3.6.2",
                "torchvision"
                {
                    "pip": [
                        "azureml-defaults",
                        "azureml-telemetry",
                        "scikit-learn==0.22.1",
                        "inference-schema[numpy-support]"
                    ]
                }
            ],
            "name": "project_environment"
        },
        "condaDependenciesFile": null,
        "interpreterPath": "python",
        "userManagedDependencies": false
    },
    "version": "1"
}
```

Detta anger att Machine Learning-distributionen ska använda filen `score.py` i `./working_dir` katalogen för att bearbeta inkommande begär Anden och att den kommer att använda Docker-avbildningen med python-paketen som anges i `project_environment` miljön.

[I den här artikeln finns](./reference-azure-machine-learning-cli.md#inference-configuration-schema) en mer omfattande diskussion om konfigurations härledning. 

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar:

1. läsa in en [granskad miljö](resource-curated-environments.md) från din arbets yta
1. Klona miljön
1. Ange `scikit-learn` som ett beroende.
1. Använda miljön för att skapa en InferenceConfig

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Mer information om konfiguration av konfiguration finns i [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) -klassens dokumentation.

---

> [!TIP] 
> Information om hur du använder en anpassad Docker-avbildning med en konfigurations konfiguration finns i [distribuera en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Välj ett beräknings mål

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definiera en distributions konfiguration

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Vilka alternativ som är tillgängliga för en distributions konfiguration beror på vilket beräknings mål du väljer.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Mer information finns i [den här referensen](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Innan du distribuerar din modell måste du definiera distributions konfigurationen. *Distributions konfigurationen är specifika för det beräknings mål som ska vara värd för webb tjänsten.* Om du till exempel distribuerar en modell lokalt måste du ange den port där tjänsten accepterar begär Anden. Distributions konfigurationen ingår inte i ditt Entry-skript. Den används för att definiera egenskaperna för det beräknings mål som ska vara värd för modell-och registrerings skriptet.

Du kan också behöva skapa beräknings resursen, om du till exempel inte redan har en AKS-instans (Azure Kubernetes service) kopplad till din arbets yta.

Följande tabell innehåller ett exempel på hur du skapar en distributions konfiguration för varje beräknings mål:

| Beräkningsmål | Exempel på distributions konfiguration |
| ----- | ----- |
| Lokal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasserna för lokala, Azure Container Instances-och AKS-webbtjänster kan importeras från `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Distribuera din Machine Learning-modell

Nu är du redo att distribuera din modell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Använda en registrerad modell

Om du har registrerat din modell i Azure Machine Learning arbets ytan ersätter du "modell: 1" med namnet på din modell och dess versions nummer.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Använda en lokal modell

Om du föredrar att inte registrera din modell kan du skicka parametern "sourceDirectory" i din inferenceconfig.jspå för att ange en lokal katalog som din modell ska användas från.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json --name my_deploy
```

# <a name="python"></a>[Python](#tab/python)

Exemplet nedan visar en lokal distribution. Syntaxen varierar beroende på vilket beräknings mål som du valde i föregående steg.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information finns i dokumentationen för [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice), [Model. Deploy ()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)och [WebService](/python/api/azureml-core/azureml.core.webservice.webservice).

---

### <a name="understanding-service-state"></a>Förstå tjänst tillstånd

Under modell distributionen kan tjänst tillstånds ändringen visas när den distribueras fullständigt.

I följande tabell beskrivs de olika tjänst tillstånden:

| Webservice-tillstånd | Description | Slutligt tillstånd?
| ----- | ----- | ----- |
| Övergår | Tjänsten håller på att distribueras. | No |
| Ohälsosamt | Tjänsten har distribuerats men är för närvarande inte tillgänglig.  | No |
| Unschedulable | Det går inte att distribuera tjänsten för tillfället på grund av bristande resurser. | No |
| Misslyckad | Det gick inte att distribuera tjänsten på grund av ett fel eller en krasch. | Yes |
| Felfri | Tjänsten är felfri och slut punkten är tillgänglig. | Yes |

> [!TIP]
> När du distribuerar är Docker-avbildningar för beräknings mål byggda och inlästa från Azure Container Registry (ACR). Azure Machine Learning skapar som standard en ACR som använder tjänst nivån *Basic* . Att ändra ACR för arbets ytan till standard-eller Premium-nivån kan minska den tid det tar att bygga och distribuera avbildningar till dina beräknings mål. Mer information finns i [Azure Container Registry tjänst nivåer](../container-registry/container-registry-skus.md).

> [!NOTE]
> Om du distribuerar en modell till Azure Kubernetes service (AKS) rekommenderar vi att du aktiverar [Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) för klustret. Detta hjälper dig att förstå övergripande kluster hälsa och Resursanvändning. Du kan också hitta följande resurser:
>
> * [Sök efter Resource Health händelser som påverkar ditt AKS-kluster](../aks/aks-resource-health.md)
> * [Diagnostik för Azure Kubernetes service](../aks/concepts-diagnostics.md)
>
> Om du försöker distribuera en modell till ett ohälsosamt eller överbelastat kluster förväntas det uppstå problem. Kontakta AKS-supporten om du behöver hjälp med att felsöka problem med AKS-kluster.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batch-härledning
Azure Machine Learning beräknings mål skapas och hanteras av Azure Machine Learning. De kan användas för batch förutsägelse från Azure Machine Learning pipeliner.

En genom gång av batch-härledning med Azure Machine Learning Compute finns i [så här kör du batch-förutsägelser](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge-härledning
Stöd för att distribuera till Edge är i för hands version. Mer information finns i [distribuera Azure Machine Learning som en IoT Edge modul](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Ta bort resurser

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Använd om du vill ta bort en distribuerad WebService `az ml service <name of webservice>` .

Om du vill ta bort en registrerad modell från din arbets yta använder du `az ml model delete <model id>`

Läs mer om att [ta bort en WebService](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) och [ta bort en modell](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Använd om du vill ta bort en distribuerad webb tjänst `service.delete()` .
Använd om du vill ta bort en registrerad modell `model.delete()` .

Mer information finns i dokumentationen för [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) och [Model. Delete ()](/python/api/azureml-core/azureml.core.model.model#delete--).

---

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [En klicknings distribution för automatiserade ML-körningar i Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)