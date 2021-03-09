---
title: Uppdatera webb tjänster
titleSuffix: Azure Machine Learning
description: Lär dig hur du uppdaterar en webb tjänst som redan har distribuerats i Azure Machine Learning. Du kan uppdatera inställningar som modell, miljö och inmatning-skript.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: da018f212844e1587ad0e434902e49765f72e521
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102520105"
---
# <a name="update-a-deployed-web-service"></a>Uppdatera en distribuerad webb tjänst

I den här artikeln får du lära dig hur du uppdaterar en webb tjänst som har distribuerats med Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du redan har distribuerat en webb tjänst med Azure Machine Learning. [Följ dessa steg](how-to-deploy-and-where.md)om du behöver lära dig hur du distribuerar en webb tjänst.

## <a name="update-web-service"></a>Uppdatera webbtjänst

Använd-metoden för att uppdatera en webb tjänst `update` . Du kan uppdatera webb tjänsten så att den använder en ny modell, ett nytt registrerings skript eller nya beroenden som kan anges i en konfiguration för en konfiguration. Mer information finns i dokumentationen för [WebService. Update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-).

Se [AKS-tjänstens uppdaterings metod.](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Se [ACI-tjänstens uppdaterings metod.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> När du skapar en ny version av en modell måste du manuellt uppdatera varje tjänst som du vill använda.
>
> Du kan inte använda SDK för att uppdatera en webb tjänst som publicerats från Azure Machine Learning designer.

**Med SDK**

Följande kod visar hur du använder SDK för att uppdatera modell-, miljö-och registrerings skriptet för en webb tjänst:

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**Använda CLI**

Du kan också uppdatera en webb tjänst med hjälp av ML CLI. I följande exempel visas hur du registrerar en ny modell och sedan uppdaterar en webb tjänst så att den använder den nya modellen:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> I det här exemplet används ett JSON-dokument för att skicka modell informationen från registrerings kommandot till kommandot Update.
>
> Om du vill uppdatera tjänsten för att använda ett nytt start skript eller en ny miljö skapar du en [konfigurations fil för en konfigurations fil](./reference-azure-machine-learning-cli.md#inference-configuration-schema) och anger den med `ic` parametern.

Mer information finns i dokumentationen om [AZ ml-tjänst uppdatering](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-update) .

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)
