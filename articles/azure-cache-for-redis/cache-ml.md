---
title: Distribuera en maskin inlärnings modell till Azure Functions med Azure cache för Redis
description: I den här artikeln ska du distribuera en modell från Azure Machine Learning som en Function-app i Azure Functions med hjälp av en Azure-cache för Redis-instans. Azure cache för Redis är mycket presterande och skalbart – när de kombineras med en Azure Machine Learning-modell får du låg latens och högt data flöde i ditt program.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: ec8943bc73cac2020350dd4916f040f031cd842b
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499704"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-with-azure-cache-for-redis"></a>Distribuera en maskin inlärnings modell till Azure Functions med Azure cache för Redis 

I den här artikeln ska du distribuera en modell från Azure Machine Learning som en Function-app i Azure Functions med hjälp av en Azure-cache för Redis-instans.  

Azure cache för Redis är mycket presterande och skalbart – när de kombineras med en Azure Machine Learning-modell får du låg latens och högt data flöde i ditt program. Ett par scenarier där en cache är särskilt fördelaktig när inferencing data och för den faktiska modellens resultat. I båda fallen lagras metadata-data eller resultat i minnet, vilket leder till ökade prestanda. 

> [!NOTE]
> Både Azure Machine Learning och Azure Functions är allmänt tillgängliga, men möjligheten att paketera en modell från Machine Learnings tjänsten för functions är i för hands version.  
>

## <a name="prerequisites"></a>Förutsättningar
* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/).
* En Azure Machine Learning-arbetsyta. Mer information finns i artikeln [skapa en arbets yta](../machine-learning/how-to-manage-workspace.md) .
* [Azure CLI](/cli/azure/install-azure-cli).
* En utbildad Machine Learning-modell som registrerats i din arbets yta. Om du inte har någon modell använder du [själv studie kursen om bild klassificering: träna modell](../machine-learning/tutorial-train-models-with-aml.md) att träna och registrera en.

> [!IMPORTANT]
> Kodfragmenten i den här artikeln förutsätter att du har angett följande variabler:
>
> * `ws` – Din Azure Machine Learning-arbetsyta.
> * `model` – Den registrerade modellen som ska distribueras.
> * `inference_config` – Den här modellens konfigurations konfiguration.
>
> Mer information om hur du ställer in dessa variabler finns i [Distribuera modeller med Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

## <a name="create-an-azure-cache-for-redis-instance"></a>Skapa en Azure Cache for Redis-instans 
Du kommer att kunna distribuera en maskin inlärnings modell till Azure Functions med valfri cache-instans Basic, standard eller Premium. Följ dessa steg om du vill skapa en cache-instans.  

1. Gå till Azure Portal start sida eller öppna menyn på panelen och välj sedan **skapa en resurs**. 
   
1. Välj **databaser** på sidan **nytt** och välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Välj Azure-cache för Redis.":::
   
1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . Redis.cache.Windows.net*. | 
   | **Prenumeration** | List rutan och välj din prenumeration. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. | 
   | **Resursgrupp** | List rutan och välj en resurs grupp, eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. | 
   | **Plats** | List rutan och välj en plats. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **Prisnivå** | List rutan och välj en [pris nivå](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Översikt över Azure Cache for Redis](cache-overview.md). |

1. Välj fliken **nätverk** eller klicka på knappen **nätverk** längst ned på sidan.

1. På fliken **nätverk** väljer du anslutnings metod.

1. Välj **Nästa: fliken Avancerat** eller klicka på **Nästa: Avancerat** längst ned på sidan.

1. På fliken **Avancerat** för en Basic-eller standard-cachepost väljer du aktivera växling om du vill aktivera en icke-TLS-port.

1. På fliken **Avancerat** för Premium-cache-instans konfigurerar du inställningarna för icke-TLS-port, klustring och data beständighet.

1. Välj fliken **Nästa: Taggar** eller klicka på knappen **Nästa: Taggar** längst ned på sidan.

1. Alternativt går du till fliken **taggar** och anger namn och värde om du vill kategorisera resursen. 

1. Välj **Granska + skapa**. Du kommer till fliken Granska + skapa där Azure verifierar konfigurationen.

1. När meddelandet grön verifiering har skickats visas väljer du **skapa**.

Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas. 

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Innan du distribuerar måste du definiera vad som behövs för att köra modellen som en webb tjänst. I följande lista beskrivs de kärn objekt som behövs för en-distribution:

* Ett __Entry-skript__. Det här skriptet accepterar begär Anden, visar begäran med hjälp av modellen och returnerar resultatet.

    > [!IMPORTANT]
    > Start skriptet är bara för din modell. den måste förstå formatet på inkommande begär ande data, formatet på de data som förväntas av din modell och formatet på de data som returneras till klienter.
    >
    > Om begär ande data har ett format som inte kan användas av din modell kan skriptet omvandla det till ett acceptabelt format. Det kan också omvandla svaret innan det returneras till klienten.
    >
    > Som standard behandlas indatatypen som text när de paketeras för funktioner. Om du är intresse rad av att använda rå byte för indata (till exempel för BLOB-utlösare) bör du använda [AMLRequest för att acceptera rå data](../machine-learning/how-to-deploy-advanced-entry-script.md#binary-data).

Se till att den ansluter till en Redis-slutpunkt för att köra funktionen.

```python
import json
import numpy as np
import os
import redis
from sklearn.externals import joblib

def init():
    global model
    global azrediscache
    azrediscache = redis.StrictRedis(host='<host_url>', port=6380, password="<access_key>", ssl=True)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        input = azrediscache.get(data)
        result = model.predict(input)
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Mer information om Entry-skript finns i [definiera bedömnings kod.](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-entry-script)

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
>                                                            pip_packages=['azureml-defaults', 'redis'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Mer information om miljöer finns i [skapa och hantera miljöer för utbildning och distribution](../machine-learning/how-to-use-environments.md).

Mer information om konfiguration av konfiguration finns i [Distribuera modeller med Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md?tabs=python#define-an-inference-configuration).

> [!IMPORTANT]
> När du distribuerar till Functions behöver du inte skapa en __distributions konfiguration__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Installera SDK Preview-paketet för functions-stöd

Om du vill bygga paket för Azure Functions måste du installera SDK-paketet för för hands versionen.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Skapa avbildningen

Om du vill skapa Docker-avbildningen som distribueras till Azure Functions använder du [azureml. contrib. functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions) eller funktionen Package för den utlösare som du är intresse rad av. Följande kodfragment visar hur du skapar ett nytt paket med en HTTP-utlösare från modellen och konfigurationen för konfigurations härledning:

> [!NOTE]
> Kodfragmentet förutsätter att `model` innehåller en registrerad modell och att den `inference_config` innehåller konfigurationen för härlednings miljön. Mer information finns i [Distribuera modeller med Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import HTTP_TRIGGER
model_package = package(ws, [model], inference_config, functions_enabled=True, trigger=HTTP_TRIGGER)
model_package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(model_package.location)
```

När `show_output=True` visas utdata från Docker-build-processen. När processen har slutförts har avbildningen skapats i Azure Container Registry för din arbets yta. När avbildningen har skapats visas platsen i Azure Container Registry. Den plats som returnerades är i formatet `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> Paketering för functions stöder HTTP-utlösare, BLOB-utlösare och Service Bus-utlösare Mer information om utlösare finns i [Azure Functions-bindningar](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Spara plats informationen som används när avbildningen distribueras.

## <a name="deploy-image-as-a-web-app"></a>Distribuera avbildning som en webbapp

1. Använd följande kommando för att hämta inloggnings uppgifterna för den Azure Container Registry som innehåller avbildningen. Ersätt `<myacr>` med det värde som returnerades tidigare från `package.location` : 

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
    > Nu har Function-appen skapats. Men eftersom du inte har angett anslutnings strängen för HTTP-utlösaren eller autentiseringsuppgifterna till Azure Container Registry som innehåller avbildningen, är funktions programmet inte aktivt. I nästa steg anger du anslutnings strängen och autentiseringsinformationen för behållar registret. 

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

## <a name="test-azure-functions-http-trigger"></a>Test Azure Functions HTTP-utlösare 

Vi kommer nu att köra och testa vår Azure Functions HTTP-utlösare.

1. Gå till din Function-app i Azure Portal.
1. Välj **kod + test** under utvecklare. 
1. Välj fliken **inmatare** till höger. 
1. Klicka på knappen **Kör** för att testa Azure Functions http-utlösaren. 

Nu har du distribuerat en modell från Azure Machine Learning som en Function-app med hjälp av en Azure-cache för Redis-instans. Läs mer om Azure cache för Redis genom att navigera till länkarna i avsnittet nedan.

## <a name="clean-up-resources"></a>Rensa resurser

Om du tänker fortsätta till nästa självstudie kan du behålla resurserna som du har skapat i den här självstudien och använda dem igen.

Annars, om du är klar med snabb starten, kan du ta bort de Azure-resurser som du skapade i den här snabb starten för att undvika avgifter. 

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. När du tar bort en resursgrupp tas alla resurser som ingår i den bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.

### <a name="to-delete-a-resource-group"></a>Ta bort en resursgrupp

1. Logga in på [Azure-portalen](https://portal.azure.com) och välj **Resursgrupper**.

2. Skriv namnet på din resursgrupp i rutan **Filtrera efter namn...**. På din resursgrupp i resultatlistan väljer du **...** och sedan **Ta bort resursgrupp**.

Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp för att bekräfta och välj sedan **Ta bort**.

Efter en liten stund tas resursgruppen och de resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg 

* Läs mer om [Azure cache för Redis](./cache-overview.md)
* Lär dig hur du konfigurerar din Function-app [i functions-dokumentationen.](../azure-functions/functions-create-function-linux-custom-image.md)
* [API-referens](/python/api/azureml-contrib-functions/azureml.contrib.functions) 
* Skapa en [python-app som använder Azure cache för Redis](./cache-python-get-started.md)
