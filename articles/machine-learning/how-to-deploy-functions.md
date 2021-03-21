---
title: Distribuera ml-modeller till Azure Functions appar (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder Azure Machine Learning för att paketera och distribuera en modell som en webb tjänst i en Azure Functions app.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: conceptual
ms.custom: how-to, racking-python, devx-track-azurecli
ms.openlocfilehash: abb38ebbdacdf4f153148fbe121e54ede88f066a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519273"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Distribuera en maskin inlärnings modell till Azure Functions (för hands version)


Lär dig hur du distribuerar en modell från Azure Machine Learning som en Function-app i Azure Functions.

> [!IMPORTANT]
> Både Azure Machine Learning och Azure Functions är allmänt tillgängliga, men möjligheten att paketera en modell från Machine Learnings tjänsten för functions är i för hands version.

Med Azure Machine Learning kan du skapa Docker-avbildningar från tränade maskin inlärnings modeller. Azure Machine Learning har nu förhands gransknings funktioner för att bygga dessa maskin inlärnings modeller i Function-appar som kan [distribueras till Azure Functions](../azure-functions/functions-deployment-technologies.md#docker-container).

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

Innan du distribuerar måste du definiera vad som behövs för att köra modellen som en webb tjänst. I följande lista beskrivs de kärn objekt som behövs för en-distribution:

* Ett __Entry-skript__. Det här skriptet accepterar begär Anden, visar begäran med hjälp av modellen och returnerar resultatet.

    > [!IMPORTANT]
    > Start skriptet är bara för din modell. den måste förstå formatet på inkommande begär ande data, formatet på de data som förväntas av din modell och formatet på de data som returneras till klienter.
    >
    > Om begär ande data har ett format som inte kan användas av din modell kan skriptet omvandla det till ett acceptabelt format. Det kan också omvandla svaret innan det returneras till klienten.
    >
    > Som standard behandlas indatatypen som text när de paketeras för funktioner. Om du är intresse rad av att använda rå byte för indata (till exempel för BLOB-utlösare) bör du använda [AMLRequest för att acceptera rå data](./how-to-deploy-advanced-entry-script.md#binary-data).

Mer information om Entry-skript finns i [definiera bedömnings kod](./how-to-deploy-and-where.md#define-an-entry-script)

* **Beroenden**, till exempel hjälp skript eller python/Conda-paket som krävs för att köra registrerings skriptet eller modellen

Dessa entiteter kapslas in i en konfiguration för en __härledning__. Inferenskonfigurationen refererar till startskriptet och andra beroenden.

> [!IMPORTANT]
> När du skapar en konfigurations konfiguration för användning med Azure Functions måste du använda ett [miljö](/python/api/azureml-core/azureml.core.environment%28class%29) objekt. Observera att om du definierar en anpassad miljö måste du lägga till azureml-defaults med version >= 1.0.45 som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst. I följande exempel visas hur du skapar ett miljö objekt och använder det med en konfigurations konfiguration:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
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
> När du distribuerar till Functions behöver du inte skapa en __distributions konfiguration__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installera SDK Preview-paketet för functions-stöd

Om du vill bygga paket för Azure Functions måste du installera SDK-paketet för för hands versionen.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Skapa avbildningen

Om du vill skapa Docker-avbildningen som distribueras till Azure Functions använder du [azureml. contrib. functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions) eller funktionen Package för den utlösare som du är intresse rad av. Följande kodfragment visar hur du skapar ett nytt paket med en BLOB-utlösare från modellen och konfigurationen för konfigurations härledning:

> [!NOTE]
> Kodfragmentet förutsätter att `model` innehåller en registrerad modell och att den `inference_config` innehåller konfigurationen för härlednings miljön. Mer information finns i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

När `show_output=True` visas utdata från Docker-build-processen. När processen har slutförts har avbildningen skapats i Azure Container Registry för din arbets yta. När avbildningen har skapats visas platsen i Azure Container Registry. Den plats som returnerades är i formatet `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Paketering för functions stöder HTTP-utlösare, BLOB-utlösare och Service Bus-utlösare Mer information om utlösare finns i [Azure Functions-bindningar](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Spara plats informationen som används när avbildningen distribueras.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Använd följande kommando för att hämta inloggnings uppgifterna för den Azure Container Registry som innehåller avbildningen. Ersätt `<myacr>` med det värde som returnerades tidigare från `blob.location` : 

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

    I det här exemplet används en pris nivå för _Linux Basic_ ( `--sku B1` ).

    > [!IMPORTANT]
    > Avbildningar som skapats av Azure Machine Learning använda Linux, så du måste använda `--is-linux` parametern.

1. Skapa lagrings kontot som ska användas för webb jobbets lagring och hämta dess anslutnings sträng. Ersätt `<webjobStorage>` med det namn som du vill använda.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Använd följande kommando för att skapa en Function-app. Ersätt `<app-name>` med det namn som du vill använda. Ersätt `<acrinstance>` och `<imagename>` med värdena från returnerade `package.location` tidigare. Ersätt `<webjobStorage>` med namnet på lagrings kontot från föregående steg:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Nu har Function-appen skapats. Men eftersom du inte har angett anslutnings strängen för BLOB-utlösaren eller autentiseringsuppgifterna till Azure Container Registry som innehåller avbildningen, är funktions programmet inte aktivt. I nästa steg anger du anslutnings strängen och autentiseringsinformationen för behållar registret. 

1. Skapa lagrings kontot som ska användas för BLOB trigger Storage och hämta dess anslutnings sträng. Ersätt `<triggerStorage>` med det namn som du vill använda.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Registrera den här anslutnings strängen för att tillhandahålla Function-appen. Vi kommer att använda den senare när vi ber om `<triggerConnectionString>`

1. Skapa behållarna för indata och utdata i lagrings kontot. Ersätt `<triggerConnectionString>` med den anslutnings sträng som returnerades tidigare:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Om du vill associera anslutnings strängen för utlösaren med Function-appen använder du följande kommando. Ersätt `<app-name>` med namnet på Function-appen. Ersätt `<triggerConnectionString>` med den anslutnings sträng som returnerades tidigare:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Du måste hämta taggen som är associerad med den skapade behållaren med hjälp av följande kommando. Ersätt `<username>` med det användar namn som returnerades tidigare från behållar registret:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Spara värdet som returneras, används som `imagetag` i nästa steg.

1. Använd följande kommando för att tillhandahålla funktionen appen med de autentiseringsuppgifter som krävs för att komma åt behållar registret. Ersätt `<app-name>` med namnet på Function-appen. Ersätt `<acrinstance>` och `<imagetag>` med värdena från AZ CLI-anropet i föregående steg. Ersätt `<username>` och `<password>` med ACR-inloggnings informationen som hämtades tidigare:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

I det här läget börjar Function-appen läsa in avbildningen.

> [!IMPORTANT]
> Det kan ta flera minuter innan avbildningen har lästs in. Du kan övervaka förloppet med hjälp av Azure Portal.

## <a name="test-the-deployment"></a>Testa distributionen

När avbildningen har lästs in och appen är tillgänglig använder du följande steg för att utlösa appen:

1. Skapa en textfil som innehåller de data som score.py-filen förväntar sig. Följande exempel fungerar med en score.py som förväntar sig en matris med 10 tal:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Formatet på data beror på vad din score.py och modell förväntar sig.

2. Använd följande kommando för att överföra den här filen till behållaren indata i den Utlös ande lagrings blob som skapades tidigare. Ersätt `<file>` med namnet på den fil som innehåller data. Ersätt `<triggerConnectionString>` med den anslutnings sträng som returnerades tidigare. I det här exemplet `input` är namnet på den indatamängd som skapades tidigare. Om du har använt ett annat namn ersätter du det här värdet:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Utdata från det här kommandot liknar följande JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Om du vill visa utdata som skapas av funktionen använder du följande kommando för att lista de utdatafiler som genereras. Ersätt `<triggerConnectionString>` med den anslutnings sträng som returnerades tidigare. I det här exemplet `output` är namnet på den utmatnings behållare som skapades tidigare. Om du har använt ett annat namn ersätter du det här värdet:

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    Utdata från det här kommandot liknar `sample_input_out.json` .

4. Om du vill hämta filen och kontrol lera innehållet använder du följande kommando. Ersätt `<file>` med fil namnet som returneras av föregående kommando. Ersätt `<triggerConnectionString>` med den anslutnings sträng som returnerades tidigare: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    När kommandot har slutförts öppnar du filen. Den innehåller de data som returneras av modellen.

Mer information om hur du använder BLOB-utlösare finns i artikeln [skapa en funktion som utlöses av Azure Blob Storage](../azure-functions/functions-create-storage-blob-triggered-function.md) .

## <a name="next-steps"></a>Nästa steg

* Lär dig att konfigurera Functions-appen i [Functions](../azure-functions/functions-create-function-linux-custom-image.md) -dokumentationen.
* Läs mer om Blob Storage-utlösare av [Azure Blob Storage-bindningar](../azure-functions/functions-bindings-storage-blob.md).
* [Distribuera din modell till Azure App Service](how-to-deploy-app-service.md).
* [Använda en ML-modell som distribueras som en webb tjänst](how-to-consume-web-service.md)
* [API-referens](/python/api/azureml-contrib-functions/azureml.contrib.functions)
