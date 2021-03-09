---
title: Distribuera modeller till Azure Container Instances
titleSuffix: Azure Machine Learning
description: Lär dig hur du distribuerar dina Azure Machine Learning modeller som en webb tjänst med hjälp av Azure Container Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 5bf3c92f07cc33b35a070a3479e0063a63c9e43a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522027"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Distribuera en modell till Azure Container Instances

Lär dig hur du använder Azure Machine Learning för att distribuera en modell som en webb tjänst på Azure Container Instances (ACI). Använd Azure Container Instances om något av följande villkor är uppfyllt:

- Du måste snabbt distribuera och validera din modell. Du behöver inte skapa ACI-behållare i förväg. De skapas som en del av distributions processen.
- Du testar en modell som är under utveckling. 

Information om kvot-och regions tillgänglighet för ACI finns i [kvoter och regions tillgänglighet för Azure Container instances](../container-instances/container-instances-quotas.md) artikel.

> [!IMPORTANT]
> Vi rekommenderar starkt att felsöka lokalt innan du distribuerar till webb tjänsten. mer information finns i [Felsöka lokalt](./how-to-troubleshoot-deployment-local.md)
>
> Du kan också läsa Azure Machine Learning – [Distribuera till lokal notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Förutsättningar

- En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

- En Machine Learning-modell som registrerats i din arbets yta. Om du inte har en registrerad modell, se [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

- I __python__ -kodfragmenten i den här artikeln förutsätter vi att följande variabler har angetts:

    * `ws` – Ställ in på din arbets yta.
    * `model` – Ställ in på din registrerade modell.
    * `inference_config` -Ange som modellens konfigurations konfiguration.

    Mer information om hur du ställer in dessa variabler finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

- __CLI__ -kodfragmenten i den här artikeln förutsätter att du har skapat ett `inferenceconfig.json` dokument. Mer information om hur du skapar det här dokumentet finns i [så här distribuerar du modeller](how-to-deploy-and-where.md).

## <a name="limitations"></a>Begränsningar

* När du använder Azure Container Instances i ett virtuellt nätverk måste det virtuella nätverket finnas i samma resurs grupp som din Azure Machine Learning-arbetsyta.
* När du använder Azure Container Instances inuti det virtuella nätverket kan Azure Container Registry (ACR) för din arbets yta också inte finnas i det virtuella nätverket.

Mer information finns i [så här skyddar du inferencing med virtuella nätverk](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

## <a name="deploy-to-aci"></a>Distribuera till ACI

Om du vill distribuera en modell till Azure Container Instances skapar du en __distributions konfiguration__ som beskriver de beräknings resurser som behövs. Till exempel antal kärnor och minne. Du behöver också en __konfiguration__ med en konfiguration som beskriver den miljö som krävs för att vara värd för modellen och webb tjänsten. Mer information om hur du skapar en konfigurations konfiguration finns i [hur och var modeller ska distribueras](how-to-deploy-and-where.md).

> [!NOTE]
> * ACI passar bara för små modeller som är mindre än 1 GB. 
> * Vi rekommenderar att du använder AKS med en nod för utveckling och testning av större modeller.
> * Antalet modeller som ska distribueras är begränsat till 1 000 modeller per distribution (per behållare). 

### <a name="using-the-sdk"></a>Med SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Modell. Deploy](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Använda CLI

Använd följande kommando för att distribuera med hjälp av CLI. Ersätt `mymodel:1` med namnet och versionen för den registrerade modellen. Ersätt `myservice` med namnet för att ge den här tjänsten:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Mer information finns i [distributions referens för AZ ml-modellen](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Använda VS Code

Se [Distribuera modeller med vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Du behöver inte skapa en ACI-behållare för att testa i förväg. ACI-behållare skapas vid behov.

> [!IMPORTANT]
> Vi lägger till hashed arbetsyte-ID till alla underliggande ACI-resurser som skapas, alla ACI-namn från samma arbets yta har samma suffix. Azure Machine Learning tjänstens namn skulle fortfarande vara samma kund som tillhandahölls "service_name" och alla användare som är riktade Azure Machine Learning SDK-API: er behöver inte några ändringar. Vi ger inga garantier till namnen på underliggande resurser som skapas.

## <a name="next-steps"></a>Nästa steg

* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Distributions fel sökning](how-to-troubleshoot-deployment.md)
* [Uppdatera webb tjänsten](how-to-deploy-update-web-service.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Använda en ML-modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)