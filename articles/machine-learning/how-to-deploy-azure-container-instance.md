---
title: Så här distribuerar du modeller till Azure Container Instances
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar Azure Machine Learning modeller som en webbtjänst med hjälp av Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 845a146d9e3f920f3313a80f1bb8c845cb781f37
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875547"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Distribuera en modell till Azure Container Instances

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webbtjänst på Azure Container Instances (ACI). Använd Azure Container Instances om något av följande villkor är sant:

- Du måste snabbt distribuera och verifiera din modell. Du behöver inte skapa ACI-containrar i förväg. De skapas som en del av distributionsprocessen.
- Du testar en modell som är under utveckling. 

Information om kvot och regionstillgänglighet för ACI finns i [artikeln Kvoter och regionstillgänglighet för Azure Container Instances.](../container-instances/container-instances-quotas.md)

> [!IMPORTANT]
> Vi rekommenderar starkt att du felsöker lokalt innan du distribuerar till webbtjänsten. Mer information finns [i Felsöka lokalt](./how-to-troubleshoot-deployment-local.md)
>
> Du kan också läsa Azure Machine Learning – [Distribuera till lokal notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)

- En maskininlärningsmodell registrerad på din arbetsyta. Om du inte har en registrerad modell kan du se Hur [och var du distribuerar modeller.](how-to-deploy-and-where.md)

- [Azure CLI-tillägget för Machine Learning,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)eller Azure Machine Learning Visual Studio [Code-tillägget](tutorial-setup-vscode-extension.md).

- Python-kodfragmenten i den här artikeln förutsätter att följande variabler har angetts: 

    * `ws` – Ställ in på din arbetsyta.
    * `model` – Ställ in på din registrerade modell.
    * `inference_config` – Ställ in på inferenskonfigurationen för modellen.

    Mer information om hur du anger dessa variabler finns i [Så här och var du distribuerar modeller.](how-to-deploy-and-where.md)

- CLI-kodfragmenten i den här artikeln förutsätter att du har skapat ett  `inferenceconfig.json` dokument. Mer information om hur du skapar det här dokumentet finns [i Hur och var du distribuerar modeller.](how-to-deploy-and-where.md)

## <a name="limitations"></a>Begränsningar

* När du Azure Container Instances i ett virtuellt nätverk måste det virtuella nätverket finnas i samma resursgrupp som din Azure Machine Learning arbetsyta.
* När du Azure Container Instances i det virtuella nätverket kan inte Azure Container Registry (ACR) för din arbetsyta också finnas i det virtuella nätverket.

Mer information finns i [Skydda inferens med virtuella nätverk.](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci)

## <a name="deploy-to-aci"></a>Distribuera till ACI

Om du vill distribuera en modell Azure Container Instances skapar du __en distributionskonfiguration__ som beskriver de beräkningsresurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __härledningskonfiguration__ som beskriver den miljö som behövs som värd för modellen och webbtjänsten. Mer information om hur du skapar inferenskonfigurationen finns i [Så här och var du distribuerar modeller.](how-to-deploy-and-where.md)

> [!NOTE]
> * ACI är endast lämpligt för små modeller som är mindre än 1 GB. 
> * Vi rekommenderar att du använder AKS med en nod för att dev-testa större modeller.
> * Antalet modeller som ska distribueras är begränsat till 1 000 modeller per distribution (per container). 

### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill distribuera med hjälp av CLI använder du följande kommando. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Mer information finns i referensen [för az ml model deploy.](/cli/azure/ml/model#az_ml_model_deploy) 

## <a name="using-vs-code"></a>Använda VS Code

Se [distribuera dina modeller med VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Du behöver inte skapa en ACI-container för att testa i förväg. ACI-containrar skapas efter behov.

> [!IMPORTANT]
> Vi lägger till hash-id:t för arbetsytan till alla underliggande ACI-resurser som skapas. Alla ACI-namn från samma arbetsyta har samma suffix. Namnet Azure Machine Learning Service fortfarande vara samma kund som "service_name" och alla användare som Azure Machine Learning SDK-API:er behöver inte ändra. Vi ger inga garantier för namnen på de underliggande resurser som skapas.

## <a name="next-steps"></a>Nästa steg

* [Så här distribuerar du en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Felsökning av distribution](how-to-troubleshoot-deployment.md)
* [Uppdatera webbtjänsten](how-to-deploy-update-web-service.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribuerats som en webbtjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)