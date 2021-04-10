---
title: Distribuera ml-modeller till Azure App Service (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att distribuera en utbildad ML-modell till en webbapp med hjälp av Azure App Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 3b1b416f3fec9e40261a82c88260c041918c1424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102522010"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Distribuera en maskin inlärnings modell till Azure App Service (för hands version)


Lär dig hur du distribuerar en modell från Azure Machine Learning som en webbapp i Azure App Service.

> [!IMPORTANT]
> Både Azure Machine Learning och Azure App Service är allmänt tillgängliga, men möjligheten att distribuera en modell från Machine Learnings tjänsten till App Service är i för hands version.

Med Azure Machine Learning kan du skapa Docker-avbildningar från tränade maskin inlärnings modeller. Den här avbildningen innehåller en webb tjänst som tar emot data, skickar den till modellen och returnerar svaret. Azure App Service kan användas för att distribuera avbildningen och innehåller följande funktioner:

* Avancerad [autentisering](../app-service/configure-authentication-provider-aad.md) för förbättrad säkerhet. Autentiseringsmetoder omfattar både Azure Active Directory och Multi-factor auth.
* [Skala](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) utan att behöva distribuera igen.
* [TLS-stöd](../app-service/configure-ssl-certificate-in-code.md) för säker kommunikation mellan klienter och tjänsten.

Mer information om funktioner som tillhandahålls av Azure App Service finns i [Översikt över App Service](../app-service/overview.md).

> [!IMPORTANT]
> Om du behöver kunna logga de bedömnings data som används med din distribuerade modell, eller resultatet av en bedömning, bör du istället distribuera till Azure Kubernetes-tjänsten. Mer information finns i [samla in data på dina produktions modeller](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [skapa en arbets yta](how-to-manage-workspace.md) .
* [Azure CLI](/cli/azure/install-azure-cli).
* En utbildad Machine Learning-modell som registrerats i din arbets yta. Om du inte har någon modell använder du [själv studie kursen om bild klassificering: träna modell](tutorial-train-models-with-aml.md) att träna och registrera en.

    > [!IMPORTANT]
    > Kodfragmenten i den här artikeln förutsätter att du har angett följande variabler:
    >
    > * `ws` – Din Azure Machine Learning-arbetsyta.
    > * `model` – Den registrerade modellen som ska distribueras.
    > * `inference_config` – Den här modellens konfigurations konfiguration.
    >
    > Mer information om hur du ställer in dessa variabler finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Innan du distribuerar måste du definiera vad som behövs för att köra modellen som en webb tjänst. I följande lista beskrivs de huvud objekt som behövs för en-distribution:

* Ett __Entry-skript__. Det här skriptet accepterar begär Anden, visar begäran med hjälp av modellen och returnerar resultatet.

    > [!IMPORTANT]
    > Start skriptet är bara för din modell. den måste förstå formatet på inkommande begär ande data, formatet på de data som förväntas av din modell och formatet på de data som returneras till klienter.
    >
    > Om begär ande data har ett format som inte kan användas av din modell kan skriptet omvandla det till ett acceptabelt format. Det kan också omvandla svaret innan det returneras till klienten.

    > [!IMPORTANT]
    > Azure Machine Learning SDK tillhandahåller inte något sätt för webb tjänsten att komma åt dina data lager eller data uppsättningar. Om du behöver distribuerad modell för att komma åt data som lagras utanför distributionen, t. ex. i ett Azure Storage konto, måste du utveckla en anpassad kod med hjälp av relevant SDK. Till exempel [Azure Storage SDK för python](https://github.com/Azure/azure-storage-python).
    >
    > Ett annat alternativ som kan fungera för ditt scenario är [batch-förutsägelser](./tutorial-pipeline-batch-scoring-classification.md), vilket ger åtkomst till data lager när poäng.

    Mer information om Entry-skript finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

* **Beroenden**, till exempel hjälp skript eller python/Conda-paket som krävs för att köra registrerings skriptet eller modellen

Dessa entiteter kapslas in i en konfiguration för en __härledning__. Inferenskonfigurationen refererar till startskriptet och andra beroenden.

> [!IMPORTANT]
> När du skapar en konfigurations konfiguration för användning med Azure App Service måste du använda ett [miljö](/python/api/azureml-core/azureml.core.environment(class)) objekt. Observera att om du definierar en anpassad miljö måste du lägga till azureml-defaults med version >= 1.0.45 som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst. I följande exempel visas hur du skapar ett miljö objekt och använder det med en konfigurations konfiguration:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

Mer information om konfiguration av konfiguration finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> När du distribuerar till Azure App Service behöver du inte skapa en __distributions konfiguration__.

## <a name="create-the-image"></a>Skapa avbildningen

Om du vill skapa Docker-avbildningen som distribueras till Azure App Service använder du [modell. Package](/python/api/azureml-core/azureml.core.model.model). Följande kodfragment visar hur du skapar en ny avbildning från modellen och konfigurationen för konfigurations härledning:

> [!NOTE]
> Kodfragmentet förutsätter att `model` innehåller en registrerad modell och att den `inference_config` innehåller konfigurationen för härlednings miljön. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

När `show_output=True` visas utdata från Docker-build-processen. När processen har slutförts har avbildningen skapats i Azure Container Registry för din arbets yta. När avbildningen har skapats visas platsen i Azure Container Registry. Den plats som returnerades är i formatet `<acrinstance>.azurecr.io/package@sha256:<imagename>` . Till exempel `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Spara plats informationen som används när avbildningen distribueras.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Använd följande kommando för att hämta inloggnings uppgifterna för den Azure Container Registry som innehåller avbildningen. Ersätt `<acrinstance>` med det värde som returnerades tidigare från `package.location` :

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Utdata från det här kommandot liknar följande JSON-dokument:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Spara värdet för __användar namn__ och ett av __lösen orden__.

1. Om du inte redan har en resurs grupp eller App Service-plan för att distribuera tjänsten visar följande kommandon hur du skapar båda:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    I det här exemplet används en __grundläggande__ pris nivå ( `--sku B1` ).

    > [!IMPORTANT]
    > Avbildningar som skapats av Azure Machine Learning använda Linux, så du måste använda `--is-linux` parametern.

1. Använd följande kommando för att skapa en webbapp. Ersätt `<app-name>` med det namn som du vill använda. Ersätt `<acrinstance>` och `<imagename>` med värdena från returnerade `package.location` tidigare:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    Det här kommandot returnerar information som liknar följande JSON-dokument:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Nu har webbappen skapats. Eftersom du inte har angett autentiseringsuppgifterna för Azure Container Registry som innehåller avbildningen är webbappen inte aktiv. I nästa steg anger du autentiseringsinformation för behållar registret.

1. Använd följande kommando för att tillhandahålla webbappen med de autentiseringsuppgifter som krävs för att komma åt behållar registret. Ersätt `<app-name>` med det namn som du vill använda. Ersätt `<acrinstance>` och `<imagename>` med värdena från returnerade `package.location` tidigare. Ersätt `<username>` och `<password>` med ACR-inloggnings informationen som hämtades tidigare:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Det här kommandot returnerar information som liknar följande JSON-dokument:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

Nu börjar webbappen läsa in avbildningen.

> [!IMPORTANT]
> Det kan ta flera minuter innan avbildningen har lästs in. Använd följande kommando för att övervaka förloppet:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> När avbildningen har lästs in och platsen är aktiv, visar loggen ett meddelande om tillstånd `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` .

När avbildningen har distribuerats kan du hitta värd namnet med hjälp av följande kommando:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Det här kommandot returnerar information som liknar följande hostname- `<app-name>.azurewebsites.net` . Använd det här värdet som en del av tjänstens __grundläggande URL__ .

## <a name="use-the-web-app"></a>Använda webbapp

Webb tjänsten som skickar begär anden till modellen finns på `{baseurl}/score` . Till exempel `https://<app-name>.azurewebsites.net/score`. Följande python-kod visar hur du skickar data till URL: en och visar svaret:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar din webbapp i [App Service i Linux](/azure/app-service/containers/) -dokumentationen.
* Lär dig mer om skalning i [Kom igång med automatisk skalning i Azure](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json).
* [Använd ett TLS/SSL-certifikat i din Azure App Service](../app-service/configure-ssl-certificate-in-code.md).
* [Konfigurera App Service-appen så att den använder Azure Active Directory inloggning](../app-service/configure-authentication-provider-aad.md).
* [Använda en ML-modell som distribueras som en webb tjänst](how-to-consume-web-service.md)