---
title: Självstudie – Använda Docker Compose för att distribuera grupp med flera containrar
description: Använd Docker Compose för att skapa och köra ett program med flera containrar och sedan öppna programmet i Azure Container Instances
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: ef08b9f9e0f596f1d94c0e6edfd46f735fe78053
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786928"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Självstudie: Distribuera en grupp med flera containrar med Docker Compose 

I den här självstudien använder du [Docker Compose](https://docs.docker.com/compose/) för att definiera och köra ett program med flera containrar lokalt och sedan distribuera det som en [containergrupp](container-instances-container-groups.md) i Azure Container Instances. 

Kör containrar Azure Container Instances på begäran när du utvecklar molnbaserade appar med Docker och vill växla sömlöst från lokal utveckling till molndistribution. Den här funktionen aktiveras genom [integrering mellan Docker och Azure](https://docs.docker.com/engine/context/aci-integration/). Du kan använda interna Docker-kommandon för att köra [antingen en enskild containerinstans](quickstart-docker-cli.md) eller en grupp med flera containrar i Azure.

> [!IMPORTANT]
> Alla funktioner i Azure Container Instances stöds inte. Ge feedback om Docker-Azure genom att skapa ett problem på [GitHub-lagringsplatsen för Docker ACI-integrering.](https://github.com/docker/aci-integration-beta)

> [!TIP]
> Du kan använda [Docker-tillägget för Visual Studio Code](https://aka.ms/VSCodeDocker) för en integrerad upplevelse för att utveckla, köra och hantera containrar, avbildningar och kontexter.

Den här artikeln innehåller följande avsnitt:

> [!div class="checklist"]
> * Skapa ett Azure-containerregister
> * Klona programmets källkod från GitHub
> * Använda Docker Compose för att skapa en avbildning och köra ett program med flera containrar lokalt
> * Push-skicka programavbildningen till containerregistret
> * Skapa en Azure-kontext för Docker
> * Öppna programmet i Azure Container Instances

## <a name="prerequisites"></a>Förutsättningar

* **Azure CLI** – Du måste ha Azure CLI installerat på den lokala datorn. Version 2.10.1 eller senare rekommenderas. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

* **Docker Desktop** – Du måste använda Docker Desktop version 2.3.0.5 eller senare, som är tillgänglig för [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) eller [macOS.](https://desktop.docker.com/mac/edge/Docker.dmg) Eller installera [Docker ACI Integration CLI för Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Hämta programkod

Exempelprogrammet som används i den här självstudien är en enkel röstningsapp. Programmet består av en webbkomponent på klientsidan och en Redis-instans på serversidan. Webbkomponenten paketeras i en anpassad containeravbildning. Redis-instansen använder en oförändrad avbildning från Docker Hub.

Använd [git](https://git-scm.com/downloads) för att klona exempelprogrammet till utvecklingsmiljön:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Ändra till den klonade katalogen.

```console
cd azure-voting-app-redis
```

I katalogen finns programmets källkod och en förskapad Docker Compose-fil, docker-compose.yaml.

## <a name="modify-docker-compose-file"></a>Ändra Docker Compose-fil

Öppna docker-compose.yaml i en textredigerare. Filen konfigurerar `azure-vote-back` tjänsterna `azure-vote-front` och .

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

Gör `azure-vote-front` följande två ändringar i konfigurationen:

1. Uppdatera `image` egenskapen i `azure-vote-front` tjänsten. Prefixet avbildningsnamnet med inloggningsservernamnet för ditt \<acrName\> Azure-containerregister, .azurecr.io. Om registret till exempel heter *myregistry* är inloggningsserverns *namn myregistry.azurecr.io* (endast gemener) och avbildningsegenskapen är då `myregistry.azurecr.io/azure-vote-front` .
1. Ändra `ports` mappningen till `80:80` . Spara filen.

Den uppdaterade filen bör se ut ungefär så här:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Genom att göra dessa ersättningar taggas avbildningen som du skapar i nästa steg för ditt Azure-containerregister och avbildningen kan hämtas för att köras `azure-vote-front` i Azure Container Instances.

> [!TIP]
> Du behöver inte använda ett Azure-containerregister för det här scenariot. Du kan till exempel välja en privat lagringsplats i Docker Hub som värd för programavbildningen. Om du väljer ett annat register uppdaterar du avbildningsegenskapen på lämpligt sätt.

## <a name="run-multi-container-application-locally"></a>Köra program med flera containrar lokalt

Kör [docker-compose up](https://docs.docker.com/compose/reference/up/), som använder exempelfilen för att skapa containeravbildningen, ladda ned `docker-compose.yaml` Redis-avbildningen och starta programmet:

```console
docker-compose up --build -d
```

När kommandot har körts kan du använda kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/) till att se de avbildningar som skapats. Tre avbildningar har hämtats eller skapats. `azure-vote-front`Avbildningen innehåller frontend-programmet, som använder `uwsgi-nginx-flask` avbildningen som bas. `redis`-avbildningen används för att starta en Redis-instans.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Kör kommandot [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) för att visa de containrar som körs:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Om du vill visa programmet som körs anger du `http://localhost:80` i en lokal webbläsare. Exempelprogrammet läses in, som du ser i följande exempel:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Bild av röstningsappen":::

När du har kört det lokala programmet kör [du docker-compose för](https://docs.docker.com/compose/reference/down/) att stoppa programmet och ta bort containrarna.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Push-avbildning till containerregister

Om du vill distribuera programmet Azure Container Instances måste du skicka `azure-vote-front` avbildningen till containerregistret. Kör [docker-compose push för](https://docs.docker.com/compose/reference/push) att push-skicka avbildningen:

```console
docker-compose push
```

Det kan ta några minuter att push-skicka till registret.

Kontrollera att avbildningen lagras i registret genom att köra [kommandot az acr repository show:](/cli/azure/acr/repository#az_acr_repository_show)

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Distribuera program till Azure Container Instances

Ändra sedan till ACI-kontexten. Efterföljande Docker-kommandon körs i den här kontexten.

```console
docker context use myacicontext
```

Kör `docker compose up` för att starta programmet i Azure Container Instances. `azure-vote-front`Avbildningen hämtas från containerregistret och containergruppen skapas i Azure Container Instances.

```console
docker compose up
```

> [!NOTE]
> Docker Compose-kommandon som för närvarande är tillgängliga i en ACI-kontext är `docker compose up` och `docker compose down` . Det finns inget bindestreck mellan `docker` och `compose` i dessa kommandon.

På kort tid distribueras containergruppen. Exempel på utdata:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Kör `docker ps` för att se de containrar som körs och IP-adressen som tilldelats till containergruppen.

```console
docker ps
```

Exempel på utdata:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Om du vill se programmet som körs i molnet anger du IP-adressen som visas i en lokal webbläsare. I det här exemplet anger du `52.179.23.131` . Exempelprogrammet läses in, som du ser i följande exempel:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Bild av röstningsappen i ACI":::

Om du vill se loggarna för frontend-containern kör du [kommandot docker logs.](https://docs.docker.com/engine/reference/commandline/logs) Exempel:

```console
docker logs azurevotingappredis_azure-vote-front
```

Du kan också använda Azure Portal eller andra Azure-verktyg för att se egenskaperna och statusen för den containergrupp som du distribuerade.

När du har provat klart programmet stoppar du programmet och containrarna med `docker compose down` :

```console
docker compose down
```

Det här kommandot tar bort containergruppen i Azure Container Instances.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Docker Compose för att växla från att köra ett program med flera containrar lokalt till att köras Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure-containerregister
> * Klona programmets källkod från GitHub
> * Använda Docker Compose för att skapa en avbildning och köra ett program med flera containrar lokalt
> * Push-skicka programavbildningen till containerregistret
> * Skapa en Azure-kontext för Docker
> * Öppna programmet i Azure Container Instances

Du kan också använda [Docker-tillägget för Visual Studio Code](https://aka.ms/VSCodeDocker) för en integrerad upplevelse för att utveckla, köra och hantera containrar, avbildningar och kontexter.

Om du vill dra nytta av fler funktioner i Azure Container Instances kan du använda Azure-verktyg för att ange en grupp med flera containrar. Se till exempel självstudierna om hur du distribuerar en containergrupp med hjälp av Azure CLI med en [YAML-fil](container-instances-multi-container-yaml.md)eller distribuerar med hjälp av [en Azure Resource Manager mall](container-instances-multi-container-group.md). 