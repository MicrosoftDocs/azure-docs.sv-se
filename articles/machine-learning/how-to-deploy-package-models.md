---
title: Paketmodeller
titleSuffix: Azure Machine Learning
description: Paketera en modell. Modeller kan paketeras som antingen en Docker-avbildning, som du sedan kan hämta eller skapa en Dockerfile och använda den för att skapa avbildningen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 299eb316b534ddc9d5eee934cc15eae841276038
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93312643"
---
# <a name="how-to-package-a-registered-model-with-docker"></a>Paketera en registrerad modell med Docker

Den här artikeln visar hur du paketerar en registrerad Azure Machine Learning modell med Docker.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har tränat och registrerat en modell i din Machine Learning-arbetsyta. Om du vill lära dig mer om att träna och registrera en scikit-modell kan du [följa den här självstudien](how-to-train-scikit-learn.md).


## <a name="package-models"></a>Paketmodeller

I vissa fall kanske du vill skapa en Docker-avbildning utan att distribuera modellen (om du till exempel planerar att [distribuera till Azure App Service](how-to-deploy-app-service.md)). Eller så kanske du vill ladda ned avbildningen och köra den på en lokal Docker-installation. Du kanske till och med vill ladda ned filerna som används för att bygga avbildningen, inspektera dem, ändra dem och bygga avbildningen manuellt.

Med modell paketering kan du göra dessa saker. Den hanterar alla till gångar som krävs för att vara värd för en modell som en webb tjänst och gör att du kan ladda ned antingen en helt inbyggd Docker-avbildning eller de filer som behövs för att skapa en. Det finns två sätt att använda modell förpackningar:

**Ladda ned en packad modell:** Hämta en Docker-avbildning som innehåller modellen och andra filer som behövs för att vara värd för den som en webb tjänst.

**Generera en Dockerfile:** Ladda ned Dockerfile, modell, Entry-skript och andra till gångar som behövs för att bygga en Docker-avbildning. Du kan sedan granska filerna eller göra ändringar innan du skapar avbildningen lokalt.

Båda paketen kan användas för att hämta en lokal Docker-avbildning.

> [!TIP]
> Att skapa ett paket liknar att distribuera en modell. Du använder en registrerad modell och en konfiguration för konfigurations störningar.

> [!IMPORTANT]
> Om du vill ladda ned en helt skapad avbildning eller skapa en avbildning lokalt måste du ha [Docker](https://www.docker.com) installerat i utvecklings miljön.

### <a name="download-a-packaged-model"></a>Ladda ned en packad modell

I följande exempel skapas en avbildning som är registrerad i Azure Container Registry för din arbets yta:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

När du har skapat ett paket kan du använda `package.pull()` för att hämta avbildningen till din lokala Docker-miljö. Utdata från det här kommandot visar namnet på bilden. Exempel: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

När du har laddat ned modellen använder du `docker images` kommandot för att lista de lokala avbildningarna:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Starta en lokal behållare baserat på den här avbildningen genom att använda följande kommando för att starta en namngiven behållare från gränssnittet eller kommando raden. Ersätt `<imageid>` värdet med det bild-ID som kommandot returnerade `docker images` .

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Det här kommandot startar den senaste versionen av avbildningen med namnet `myimage` . Den mappar den lokala port 6789 till porten i behållaren där webb tjänsten lyssnar (5001). Det tilldelar också namnet `mycontainer` till behållaren, vilket gör det enklare att stoppa behållaren. När behållaren har startats kan du skicka begär anden till `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Generera en Dockerfile och beroenden

I följande exempel visas hur du hämtar Dockerfile, modellen och andra till gångar som behövs för att bygga en avbildning lokalt. `generate_dockerfile=True`Parametern anger att du vill att filerna, inte en helt skapad avbildning, ska visas.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Den här koden laddar ned de filer som krävs för att bygga avbildningen till `imagefiles` katalogen. Dockerfile som ingår i de sparade filerna hänvisar till en bas avbildning som lagras i ett Azure Container Registry. När du skapar avbildningen på den lokala Docker-installationen måste du använda adressen, användar namnet och lösen ordet för att autentisera till registret. Använd följande steg för att bygga avbildningen med hjälp av en lokal Docker-installation:

1. Använd följande kommando för att autentisera Docker med Azure Container Registry från en Shell-eller kommando rads-session. Ersätt `<address>` , `<username>` och `<password>` med värdena som hämtades av `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Använd följande kommando för att bygga avbildningen. Ersätt `<imagefiles>` med sökvägen till katalogen där `package.save()` filerna sparades.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Det här kommandot anger avbildningens namn till `myimage` .

Om du vill kontrol lera att avbildningen har skapats använder du `docker images` kommandot. Du bör se `myimage` avbildningen i listan:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Om du vill starta en ny behållare baserat på den här avbildningen använder du följande kommando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Det här kommandot startar den senaste versionen av avbildningen med namnet `myimage` . Den mappar den lokala port 6789 till porten i behållaren där webb tjänsten lyssnar (5001). Det tilldelar också namnet `mycontainer` till behållaren, vilket gör det enklare att stoppa behållaren. När behållaren har startats kan du skicka begär anden till `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Exempel klient för att testa den lokala behållaren

Följande kod är ett exempel på en python-klient som kan användas med behållaren:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Till exempel klienter i andra programmeringsspråk, se [använda modeller som distribueras som webb tjänster](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Stoppa Docker-behållaren

Om du vill stoppa behållaren använder du följande kommando från ett annat gränssnitt eller en annan kommando rad:

```bash
docker kill mycontainer
```

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)
