---
title: Självstudie – använda Docker Compose för att distribuera grupper med flera behållare
description: Använd Docker Compose för att skapa och köra ett program med flera behållare och sedan hämta programmet i Azure Container Instances
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913848"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Självstudie: Distribuera en grupp med flera behållare med hjälp av Docker Compose 

I den här självstudien använder du [Docker Compose](https://docs.docker.com/compose/) för att definiera och köra ett program med flera behållare lokalt och sedan distribuera det som en [behållar grupp](container-instances-container-groups.md) i Azure Container instances. 

Kör behållare i Azure Container Instances på begäran när du utvecklar Cloud-inhemska appar med Docker och du vill växla sömlöst från lokal utveckling till moln distribution. Den här funktionen är aktive rad genom [integrering mellan Docker och Azure](https://docs.docker.com/engine/context/aci-integration/). Du kan använda inbyggda Docker-kommandon för att köra [en enda container instans](quickstart-docker-cli.md) eller flera container grupper i Azure.

> [!IMPORTANT]
> Det finns inte stöd för alla funktioner i Azure Container Instances. Ge feedback om Docker-Azure-integrering genom att skapa ett problem i [Docker ACI integration](https://github.com/docker/aci-integration-beta) GitHub-lagringsplatsen.

> [!TIP]
> Du kan använda [Docker-tillägget för Visual Studio Code](https://aka.ms/VSCodeDocker) för en integrerad upplevelse för att utveckla, köra och hantera behållare, bilder och kontexter.

Den här artikeln innehåller följande avsnitt:

> [!div class="checklist"]
> * Skapa ett Azure-containerregister
> * Klona programmets källkod från GitHub
> * Använd Docker Compose för att skapa en avbildning och köra ett program med flera behållare lokalt
> * Skicka program avbildningen till behållar registret
> * Skapa en Azure-kontext för Docker
> * Hämta programmet i Azure Container Instances

## <a name="prerequisites"></a>Förutsättningar

* **Azure CLI** – du måste ha Azure CLI installerat på den lokala datorn. Version 2.10.1 eller senare rekommenderas. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

* **Docker-skrivbordet** – du måste använda Docker Desktop version 2.3.0.5 eller senare, tillgängligt för [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) eller [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg). Eller installera [Docker ACI integration CLI för Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

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

I katalogen är program käll koden och en för hands skapad Docker-fil, Docker-Compose. yaml.

## <a name="modify-docker-compose-file"></a>Ändra Docker Compose-fil

Öppna Docker-Compose-yaml i en text redigerare. Filen konfigurerar-och- `azure-vote-back` `azure-vote-front` tjänsterna.

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

`azure-vote-front`Gör följande två ändringar i konfigurationen:

1. Uppdatera `image` egenskapen i `azure-vote-front` tjänsten. Prefix namnet på avbildningen med inloggnings Server namnet för ditt Azure Container Registry, \<acrName\> . azurecr.io. Om registret till exempel heter *registret*, är namnet på inloggnings servern *myregistry.azurecr.io* (alla gemener) och egenskapen image är sedan `myregistry.azurecr.io/azure-vote-front` .
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

Genom att göra dessa ersättningar `azure-vote-front` är avbildningen som du skapar i nästa steg taggad för ditt Azure Container Registry och avbildningen kan hämtas för att köras i Azure Container instances.

> [!TIP]
> Du behöver inte använda ett Azure Container Registry för det här scenariot. Du kan till exempel välja ett privat lager i Docker Hub som värd för program avbildningen. Om du väljer ett annat register uppdaterar du bild egenskapen på lämpligt sätt.

## <a name="run-multi-container-application-locally"></a>Kör program med flera behållare lokalt

Kör [Docker-Sammanställ](https://docs.docker.com/compose/reference/up/), som använder exempel `docker-compose.yaml` filen för att skapa behållar avbildningen, ladda ned Redis-avbildningen och starta programmet:

```console
docker-compose up --build -d
```

När kommandot har körts kan du använda kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/) till att se de avbildningar som skapats. Tre avbildningar har hämtats eller skapats. `azure-vote-front`Avbildningen innehåller klient programmet som använder `uwsgi-nginx-flask` avbildningen som bas. `redis`-avbildningen används för att starta en Redis-instans.

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

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Bild av röstnings app":::

När du har försökt med det lokala programmet kör du [Docker-Sammanställ](https://docs.docker.com/compose/reference/down/) för att stoppa programmet och ta bort behållarna.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Skicka avbildning till behållar registret

Om du vill distribuera programmet till Azure Container Instances måste du skicka `azure-vote-front` avbildningen till behållar registret. Kör [Docker-Skriv push](https://docs.docker.com/compose/reference/push) för att skicka avbildningen:

```console
docker-compose push
```

Det kan ta några minuter att skicka till registret.

Om du vill kontrol lera att avbildningen lagras i registret kör du kommandot [AZ ACR-lagringsplats show](/cli/azure/acr/repository#az-acr-repository-show) :

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Distribuera program till Azure Container instances

Ändra sedan till ACI-kontexten. Efterföljande Docker-kommandon körs i den här kontexten.

```console
docker context use myacicontext
```

Kör `docker compose up` för att starta programmet i Azure Container instances. `azure-vote-front`Avbildningen hämtas från behållar registret och behållar gruppen skapas i Azure Container instances.

```console
docker compose up
```

> [!NOTE]
> Docker Compose-kommandon som är tillgängliga i en ACI-kontext är `docker compose up` och `docker compose down` . Det finns inget bindestreck mellan `docker` och `compose` i dessa kommandon.

Under en kort tid distribueras behållar gruppen. Exempel på utdata:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Kör `docker ps` för att se de behållare som körs och den IP-adress som tilldelats behållar gruppen.

```console
docker ps
```

Exempel på utdata:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Om du vill se det program som körs i molnet anger du den visade IP-adressen i en lokal webbläsare. I det här exemplet anger du `52.179.23.131` . Exempelprogrammet läses in, som du ser i följande exempel:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Bild av röstnings app i ACI":::

Om du vill se loggarna för klient dels behållaren kör du kommandot [Docker-loggar](https://docs.docker.com/engine/reference/commandline/logs) . Exempel:

```console
docker logs azurevotingappredis_azure-vote-front
```

Du kan också använda Azure Portal eller andra Azure-verktyg för att se egenskaper och status för den behållar grupp som du har distribuerat.

När du har försökt att köra programmet stoppar du programmet och behållarna med `docker compose down` :

```console
docker compose down
```

Det här kommandot tar bort behållar gruppen i Azure Container Instances.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Docker Compose för att växla från att köra ett program med flera behållare lokalt för att köra i Azure Container Instances. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Azure-containerregister
> * Klona programmets källkod från GitHub
> * Använd Docker Compose för att skapa en avbildning och köra ett program med flera behållare lokalt
> * Skicka program avbildningen till behållar registret
> * Skapa en Azure-kontext för Docker
> * Hämta programmet i Azure Container Instances

Du kan också använda [Docker-tillägget för Visual Studio Code](https://aka.ms/VSCodeDocker) för en integrerad upplevelse för att utveckla, köra och hantera behållare, bilder och kontexter.

Om du vill dra nytta av fler funktioner i Azure Container Instances använder du Azure-verktyg för att ange en grupp med flera behållare. Se till exempel självstudier för att distribuera en behållar grupp med hjälp av Azure CLI med en [yaml-fil](container-instances-multi-container-yaml.md)eller distribuera med hjälp av en [Azure Resource Manager mall](container-instances-multi-container-group.md). 