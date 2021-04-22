---
title: Så här distribuerar du maskininlärningsmodeller
titleSuffix: Azure Machine Learning
description: Lär dig hur och var du distribuerar maskininlärningsmodeller. Distribuera till Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge och FPGA.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli, contperf-fy21q2
adobe-target: true
ms.openlocfilehash: f2128949090ce0ec2aa4ed66eb476384d662953a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872649"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Distribuera maskininlärningsmodeller till Azure

Lär dig hur du distribuerar din maskininlärnings- eller djupinlärningsmodell som en webbtjänst i Azure-molnet. Du kan också distribuera till Azure IoT Edge enheter.

Arbetsflödet är ungefär likadant var du än distribuerar din modell:

1. Registrera modellen (valfritt, se nedan).
1. Förbered en inferenskonfiguration (såvida inte [distribution utan kod används).](./how-to-deploy-no-code-deployment.md)
1. Förbered ett startskript (såvida inte [distribution utan kod används).](./how-to-deploy-no-code-deployment.md)
1. Välj ett beräkningsmål.
1. Distribuera modellen till beräkningsmålet.
1. Testa den resulterande webbtjänsten.

Mer information om de begrepp som ingår i arbetsflödet för maskininlärningsdistribution finns i [Hantera, distribuera](concept-model-management-and-deployment.md)och övervaka modeller med Azure Machine Learning .

## <a name="prerequisites"></a>Förutsättningar

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)
- En modell. Om du inte har någon tränad modell kan du använda modellen och beroendefilerna som finns i den här [självstudien.](https://aka.ms/azml-deploy-cloud)
- [Azure CLI-tillägget (Command Line Interface) för Machine Learning tjänsten](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)
- En modell. Om du inte har någon tränad modell kan du använda modellen och beroendefilerna som finns i den här [självstudien.](https://aka.ms/azml-deploy-cloud)
- Den [Azure Machine Learning -development kit (SDK) för Python](/python/api/overview/azure/ml/intro).

---

## <a name="connect-to-your-workspace"></a>Anslut till arbetsytan

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Följ anvisningarna i Azure CLI-dokumentationen för att [ställa in din prenumerationskontext.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

Gör sedan följande:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

för att se de arbetsytor som du har åtkomst till.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Mer information om hur du använder SDK för att ansluta till en arbetsyta finns [i Azure Machine Learning SDK för Python.](/python/api/overview/azure/ml/intro#workspace)


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Registrera din modell (valfritt)

En registrerad modell är en logisk container för en eller flera filer som utgör din modell. Om du till exempel har en modell som lagras i flera filer kan du registrera dem som en enda modell på arbetsytan. När du har registrerat filerna kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

> [!TIP] 
> Registrering av en modell för versionsspårning rekommenderas men krävs inte. Om du hellre vill fortsätta utan att registrera en modell måste du ange en källkatalog i [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig) eller [inferenceconfig.jspå](./reference-azure-machine-learning-cli.md#inference-configuration-schema) och se till att din modell finns i källkatalogen.

> [!TIP]
> När du registrerar en modell anger du sökvägen till antingen en molnplats (från en träningskörning) eller en lokal katalog. Den här sökvägen är bara att hitta filerna för uppladdning som en del av registreringsprocessen. Den behöver inte matcha sökvägen som används i inmatningsskriptet. Mer information finns i [Hitta modellfiler i inmatningsskriptet](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> När du använder alternativet Filtrera efter på sidan Modeller i Azure Machine Learning Studio ska du i stället `Tags` för att använda kunder använda `TagName : TagValue` `TagName=TagValue` (utan utrymme)

Följande exempel visar hur du registrerar en modell.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrera en modell från en Azure ML-träningskörning

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

Parametern `--asset-path` refererar till modellens molnplats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modellregistreringen anger `--asset-path` du till sökvägen till en mapp som innehåller filerna.

### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Om du vill inkludera flera filer i modellregistreringen anger `-p` du sökvägen till en mapp som innehåller filerna.

Mer information om `az ml model register` finns i [referensdokumentationen](/cli/azure/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrera en modell från en Azure ML-träningskörning

  När du använder SDK:n för att träna en modell kan du antingen ta emot ett [Run-objekt](/python/api/azureml-core/azureml.core.run.run) eller [ett AutoMLRun-objekt,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) beroende på hur du har tränat modellen. Varje objekt kan användas för att registrera en modell som skapats av en experimentkörning.

  + Registrera en modell från ett `azureml.core.Run` objekt:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Parametern `model_path` refererar till modellens molnplats. I det här exemplet används sökvägen till en enskild fil. Om du vill inkludera flera filer i modellregistreringen anger `model_path` du sökvägen till en mapp som innehåller filerna. Mer information finns [](/python/api/azureml-core/azureml.core.run.run#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) i Run.register_model dokumentationen.

  + Registrera en modell från ett `azureml.train.automl.run.AutoMLRun` objekt:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    I det här exemplet anges inte parametrarna och , så `metric` `iteration` iterationen med det bästa primära måttet registreras. Värdet `model_id` som returneras från körningen används i stället för ett modellnamn.

    Mer information finns [](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) i AutoMLRun.register_model dokumentationen.

    Om du vill distribuera en registrerad modell från en rekommenderar vi att du gör det via knappen distribuera med `AutoMLRun` ett klick i Azure Machine Learning [Studio.](how-to-use-automated-ml-for-ml-models.md#deploy-your-model) 
### <a name="register-a-model-from-a-local-file"></a>Registrera en modell från en lokal fil

Du kan registrera en modell genom att ange modellens lokala sökväg. Du kan ange sökvägen till antingen en mapp eller en enskild fil. Du kan använda den här metoden för att registrera modeller som tränats med Azure Machine Learning och sedan hämtats. Du kan också använda den här metoden för att registrera modeller som tränats utanför Azure Machine Learning.

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

  Om du vill inkludera flera filer i modellregistreringen anger `model_path` du till sökvägen till en mapp som innehåller filerna.

Mer information finns i dokumentationen för [modellklassen](/python/api/azureml-core/azureml.core.model.model).

Mer information om hur du arbetar med modeller som tränats Azure Machine Learning finns i [Så här distribuerar du en befintlig modell.](how-to-deploy-existing-model.md)

---

## <a name="define-an-entry-script"></a>Definiera ett startskript

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definiera en inferenskonfiguration


En inferenskonfiguration beskriver hur du ställer in webbtjänsten som innehåller din modell. Den används senare när du distribuerar modellen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

En minimal inferenskonfiguration kan skrivas som:

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

Detta anger att machine learning-distributionen använder filen i katalogen för att bearbeta inkommande begäranden och att den använder Docker-avbildningen med de Python-paket som anges `score.py` `./working_dir` i `project_environment` miljön.

[I den här artikeln](./reference-azure-machine-learning-cli.md#inference-configuration-schema) finns en mer ingående diskussion om inferenskonfigurationer. 

# <a name="python"></a>[Python](#tab/python)

Följande exempel visar:

1. läsa in [en curated-miljö](resource-curated-environments.md) från din arbetsyta
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

Mer information om miljöer finns i Skapa [och hantera miljöer för träning och distribution.](how-to-use-environments.md)

Mer information om inferenskonfiguration finns i dokumentationen [för Klassen InferenceConfig.](/python/api/azureml-core/azureml.core.model.inferenceconfig)

---

> [!TIP] 
> Information om hur du använder en anpassad Docker-avbildning med en inferenskonfiguration finns i Så här distribuerar du en modell med en [anpassad Docker-avbildning.](how-to-deploy-custom-docker-image.md)

## <a name="choose-a-compute-target"></a>Välj ett beräkningsmål

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definiera en distributionskonfiguration

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Vilka alternativ som är tillgängliga för en distributionskonfiguration varierar beroende på vilket beräkningsmål du väljer.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Mer information finns i den [här referensen.](./reference-azure-machine-learning-cli.md#deployment-configuration-schema)

# <a name="python"></a>[Python](#tab/python)

Innan du distribuerar din modell måste du definiera distributionskonfigurationen. *Distributionskonfigurationen är specifik för beräkningsmålet som ska vara värd för webbtjänsten.* När du till exempel distribuerar en modell lokalt måste du ange den port där tjänsten accepterar begäranden. Distributionskonfigurationen ingår inte i inmatningsskriptet. Den används för att definiera egenskaperna för beräkningsmålet som ska vara värd för modellen och inmatningsskriptet.

Du kan också behöva skapa beräkningsresursen, om du till exempel inte redan har en AKS-instans (Azure Kubernetes Service) associerad med din arbetsyta.

Följande tabell innehåller ett exempel på hur du skapar en distributionskonfiguration för varje beräkningsmål:

| Beräkningsmål | Exempel på distributionskonfiguration |
| ----- | ----- |
| Lokal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Klasserna för lokala, Azure Container Instances och AKS-webbtjänster kan importeras från `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Distribuera din maskininlärningsmodell

Nu är du redo att distribuera din modell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>Använda en registrerad modell

Om du har registrerat din modell Azure Machine Learning arbetsytan ersätter du "mymodel:1" med namnet på din modell och dess versionsnummer.

```azurecli-interactive
az ml model deploy -n tutorial -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Använda en lokal modell

Om du föredrar att inte registrera din modell kan du skicka parametern "sourceDirectory" i din inferenceconfig.jsvidare för att ange en lokal katalog som din modell ska betjänas från.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json --name my_deploy
```

# <a name="python"></a>[Python](#tab/python)

Exemplet nedan visar en lokal distribution. Syntaxen varierar beroende på vilket beräkningsmål du valde i föregående steg.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information finns i dokumentationen för [LocalWebservice,](/python/api/azureml-core/azureml.core.webservice.local.localwebservice) [Model.deploy()](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)och [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice).

---

### <a name="understanding-service-state"></a>Förstå tjänsttillstånd

Under modelldistributionen kan tjänsttillståndet ändras medan den distribueras helt.

I följande tabell beskrivs de olika tjänst tillstånden:

| Webbtjänsttillstånd | Description | Sluttillstånd?
| ----- | ----- | ----- |
| Övergång | Tjänsten är under distribution. | No |
| Ohälsosamt | Tjänsten har distribuerats men kan för närvarande inte nås.  | No |
| Oplanerade | Tjänsten kan inte distribueras just nu på grund av brist på resurser. | No |
| Misslyckad | Det gick inte att distribuera tjänsten på grund av ett fel eller en krasch. | Yes |
| Felfri | Tjänsten är felfri och slutpunkten är tillgänglig. | Yes |

> [!TIP]
> Vid distributionen byggs Docker-avbildningar för beräkningsmål och läses in Azure Container Registry (ACR). Som standard Azure Machine Learning ett ACR som använder den *grundläggande* tjänstnivån. Om du ändrar ACR för din arbetsyta till standard- eller premiumnivån kan det minska den tid det tar att skapa och distribuera avbildningar till dina beräkningsmål. Mer information finns i [Azure Container Registry tjänstnivåer](../container-registry/container-registry-skus.md).

> [!NOTE]
> Om du distribuerar en modell till Azure Kubernetes Service (AKS) rekommenderar vi att [du aktiverar Azure Monitor](../azure-monitor/containers/container-insights-enable-existing-clusters.md) för klustret. Detta hjälper dig att förstå övergripande klusterhälsa och resursanvändning. Följande resurser kan också vara användbara:
>
> * [Sök efter Resource Health händelser som påverkar ditt AKS-kluster](../aks/aks-resource-health.md)
> * [Azure Kubernetes Service Diagnostik](../aks/concepts-diagnostics.md)
>
> Om du försöker distribuera en modell till ett felaktigt eller överbelastat kluster förväntas det uppleva problem. Kontakta AKS-supporten om du behöver hjälp med att felsöka AKS-klusterproblem.

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batch-inferens
Azure Machine Learning beräkningsmål skapas och hanteras av Azure Machine Learning. De kan användas för batchförutsägelse från Azure Machine Learning pipelines.

En genomgång av batch-inferens med Azure Machine Learning Compute finns i Så här kör [du batchförutsägelser](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge härledning
Stöd för distribution till gränsen är i förhandsversion. Mer information finns i [Distribuera Azure Machine Learning som en IoT Edge modul.](../iot-edge/tutorial-deploy-machine-learning.md)

## <a name="delete-resources"></a>Ta bort resurser

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Om du vill ta bort en distribuerad webbtjänst använder du `az ml service <name of webservice>` .

Om du vill ta bort en registrerad modell från arbetsytan använder du `az ml model delete <model id>`

Läs mer om att [ta bort en webbtjänst och](/cli/azure/ml/service#az_ml_service_delete) ta bort en [modell.](/cli/azure/ml/model#az_ml_model_delete)

# <a name="python"></a>[Python](#tab/python)

Om du vill ta bort en distribuerad webbtjänst använder du `service.delete()` .
Om du vill ta bort en registrerad modell använder du `model.delete()` .

Mer information finns i dokumentationen för [WebService.delete() och](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--) [Model.delete().](/python/api/azureml-core/azureml.core.model.model#delete--)

---

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klientprogram för att använda webbtjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Så här distribuerar du en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distribution med ett klick för automatiserade ML-körningar i Azure Machine Learning-studio](how-to-use-automated-ml-for-ml-models.md#deploy-your-model)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelseaviseringar och utlösare för modelldistributioner](how-to-use-event-grid.md)