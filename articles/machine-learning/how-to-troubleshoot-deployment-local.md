---
title: Felsöka distribution av lokal modell
titleSuffix: Azure Machine Learning
description: Testa en lokal modell distribution som ett första steg i fel sökning av modell distributions fel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 69ac47296cb4624de6cdf05ddb3e72973751f631
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519630"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Fel sökning med en lokal modell distribution

Testa en lokal modell distribution som ett första steg i fel sökning av distribution till Azure Container Instances (ACI) eller Azure Kubernetes service (AKS).  Med hjälp av en lokal webb tjänst blir det enklare att hitta och åtgärda vanliga Azure Machine Learning distributions fel i Docker-webbtjänsten.

## <a name="prerequisites"></a>Förutsättningar

* En **Azure-prenumeration**. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).
* Alternativ A (**rekommenderas**) – Felsök lokalt på Azure Machine Learning beräknings instans
   * En Azure Machine Learning-arbetsyta med [beräknings instans](how-to-deploy-local-container-notebook-vm.md) som körs
* Alternativ B – Felsök lokalt i din beräkning
   * [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
   * [Azure CLI](/cli/azure/install-azure-cli).
   * [CLI-tillägget för Azure Machine Learning](reference-azure-machine-learning-cli.md).
   * Ha en fungerande Docker-installation på det lokala systemet. 
   * Verifiera din Docker-installation genom att använda kommandot `docker run hello-world` från en terminal eller kommando tolk. Information om hur du installerar Docker eller felsöker Docker-fel finns i [Docker-dokumentationen](https://docs.docker.com/).

## <a name="debug-locally"></a>Felsök lokalt

Du hittar ett exempel på en [lokal distributions antecknings bok](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) i  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) -lagrings platsen för att utforska ett körbara-exempel.

> [!WARNING]
> Distributioner av lokala webb tjänster stöds inte i produktions scenarier.

Om du vill distribuera lokalt ändrar du koden så att den används `LocalWebservice.deploy_configuration()` för att skapa en distributions konfiguration. Använd sedan `Model.deploy()` för att distribuera tjänsten. I följande exempel distribueras en modell (som finns i modell variabeln) som en lokal webb tjänst:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Om du definierar din egen Conda-specifikation YAML visar du azureml-defaults version >= 1.0.45 som ett pip-beroende. Det här paketet krävs för att vara värd för modellen som en webb tjänst.

Nu kan du arbeta med tjänsten som vanligt. Följande kod visar hur du skickar data till tjänsten:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Mer information om hur du anpassar din python-miljö finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md). 

### <a name="update-the-service"></a>Uppdatera tjänsten

Under lokal testning kan du behöva uppdatera `score.py` filen för att lägga till loggning eller försöka lösa eventuella problem som du har identifierat. Om du vill läsa in ändringarna i `score.py` filen igen använder du `reload()` . Följande kod läser till exempel in skriptet för tjänsten och skickar sedan data till den. Data får poäng med den uppdaterade `score.py` filen:

> [!IMPORTANT]
> `reload`Metoden är endast tillgänglig för lokala distributioner. Information om hur du uppdaterar en distribution till ett annat beräknings mål finns i [så här uppdaterar du din WebService](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Skriptet läses in på nytt från den plats som anges av det `InferenceConfig` objekt som används av tjänsten.

Om du vill ändra modellen, Conda-beroenden eller distributions konfigurationen använder du [Update ()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-). I följande exempel uppdateras modellen som används av tjänsten:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Ta bort tjänsten

Om du vill ta bort tjänsten använder du [Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Granska Docker-loggen

Du kan skriva ut detaljerade logg meddelanden för Docker-motorn från serviceobjektet. Du kan visa loggen för ACI, AKS och lokala distributioner. Följande exempel visar hur du skriver ut loggarna.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Om du ser att linjen `Booting worker with pid: <pid>` inträffar flera gånger i loggarna innebär det att det inte finns tillräckligt med minne för att starta arbetaren.
Du kan åtgärda felet genom att öka värdet för `memory_gb` i `deployment_config`

## <a name="next-steps"></a>Nästa steg

Lär dig mer om distribution:

* [Så här felsöker du fjärrdistributioner](how-to-troubleshoot-deployment.md)
* [Distribuera och var](how-to-deploy-and-where.md)
* [Självstudie: träna & distribuera modeller](tutorial-train-models-with-aml.md)
* [Köra och felsöka experiment lokalt](./how-to-debug-visual-studio-code.md)