---
title: Uppdatera webbtjänster
titleSuffix: Azure Machine Learning
description: Lär dig hur du uppdaterar en webbtjänst som redan har distribuerats i Azure Machine Learning. Du kan uppdatera inställningar som modell, miljö och inmatningsskript.
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.custom: deploy
ms.openlocfilehash: 098dc4be33c82faff1b85a5b7224ecaf1be64944
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875475"
---
# <a name="update-a-deployed-web-service"></a>Uppdatera en distribuerad webbtjänst

I den här artikeln får du lära dig hur du uppdaterar en webbtjänst som har distribuerats med Azure Machine Learning.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du redan har distribuerat en webbtjänst med Azure Machine Learning. Följ dessa steg om du behöver lära dig hur du [distribuerar en webbtjänst.](how-to-deploy-and-where.md)

## <a name="update-web-service"></a>Uppdatera webbtjänst

Om du vill uppdatera en webbtjänst använder du `update` metoden . Du kan uppdatera webbtjänsten så att den använder en ny modell, ett nytt inmatningsskript eller nya beroenden som kan anges i en inferenskonfiguration. Mer information finns i dokumentationen för [Webservice.update](/python/api/azureml-core/azureml.core.webservice.webservice.webservice#update--args-).

Se [AKS-tjänstens uppdateringsmetod.](/python/api/azureml-core/azureml.core.webservice.akswebservice#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)

Se [ACI-tjänstens uppdateringsmetod.](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)

> [!IMPORTANT]
> När du skapar en ny version av en modell måste du manuellt uppdatera varje tjänst som du vill använda den.
>
> Du kan inte använda SDK för att uppdatera en webbtjänst som publicerats från Azure Machine Learning designer.

**Med SDK**

Följande kod visar hur du använder SDK för att uppdatera modellen, miljön och startskriptet för en webbtjänst:

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

Du kan också uppdatera en webbtjänst med hjälp av ML CLI. I följande exempel visas hur du registrerar en ny modell och sedan uppdaterar en webbtjänst för att använda den nya modellen:

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> I det här exemplet används ett JSON-dokument för att skicka modellinformationen från registreringskommandot till uppdateringskommandot.
>
> Om du vill uppdatera tjänsten så att den använder ett nytt startskript eller en ny miljö skapar du [en konfigurationsfil](./reference-azure-machine-learning-cli.md#inference-configuration-schema) för slutsatsledning och anger den med `ic` parametern .

Mer information finns i dokumentationen [för az ml service update.](/cli/azure/ml/service#az_ml_service_update)

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klientprogram för att använda webbtjänster](how-to-consume-web-service.md)
* [Så här distribuerar du en modell med hjälp av en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelseaviseringar och utlösare för modelldistributioner](how-to-use-event-grid.md)
