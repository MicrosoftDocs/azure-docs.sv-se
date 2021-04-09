---
title: Självstudie om Kubernetes i Azure – Förbereda ett program
description: I den här självstudien om Azure Kubernetes Service (AKS) lär du dig hur du förbereder och skapar en app med flera containrar med Docker Compose som du sedan kan distribuera till AKS.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: 349bf90ea0b344d5232c885358814f39fba4c19f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251967"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Självstudie: Förbereda ett program för Azure Kubernetes Service (AKS)

I den här självstudien, som är del ett av sju, förbereds ett program med flera container för användning i Kubernetes. Befintliga utvecklingsverktyg som till exempel Docker Compose används för att skapa och testa ett program lokalt. Lär dig att:

> [!div class="checklist"]
> * Klona en exempelprogramkälla från GitHub
> * Skapa en containeravbildning från exempelprogramkällan
> * Testa programmet med flera containrar i en lokal Docker-miljö

När det är klart körs följande program i din lokala utvecklingsmiljö:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Skärm bild som visar behållar avbildningen Azure röstning som körs lokalt öppnad i en lokal webbläsare" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

I senare självstudier överförs behållar avbildningen till en Azure Container Registry och distribueras sedan till ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här självstudiekursen förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som containrar, containeravbildningar och `docker`-kommandon. Läs mer om grunderna för containrar i [Kom igång med Docker][docker-get-started].

För att slutföra den här självstudien behöver du en lokal Docker-utvecklingsmiljö som kör Linux-containrar. Docker tillhandahåller paket som konfigurerar Docker i ett [Mac][docker-for-mac]-, [Windows][docker-for-windows]- eller [Linux][docker-for-linux]-system.

> [!NOTE]
> Azure Cloud Shell inkluderar inte de Docker-komponenter som krävs för att slutföra stegen i dessa självstudier. Därför rekommenderar vi att du använder en fullständig Docker-utvecklingsmiljö.

## <a name="get-application-code"></a>Hämta programkod

[Exempel programmet][sample-application] som används i den här självstudien är en vanlig röstnings app som består av en front webb komponent och en backend Redis-instans. Webbkomponenten paketeras i en anpassad containeravbildning. Redis-instansen använder en oförändrad avbildning från Docker Hub.

Använd [git][] för att klona exempelprogrammet till utvecklingsmiljön:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Ändra till den klonade katalogen.

```console
cd azure-voting-app-redis
```

Inuti katalogen finns programmets källkod, en förskapad Docker Compose-fil och en Kubernetes-manifestfil. De här filerna används i hela självstudien. Katalogens innehåll och struktur är följande:

```output
azure-voting-app-redis
│   azure-vote-all-in-one-redis.yaml
│   docker-compose.yaml
│   LICENSE
│   README.md
│
├───azure-vote
│   │   app_init.supervisord.conf
│   │   Dockerfile
│   │   Dockerfile-for-app-service
│   │   sshd_config
│   │
│   └───azure-vote
│       │   config_file.cfg
│       │   main.py
│       │
│       ├───static
│       │       default.css
│       │
│       └───templates
│               index.html
│
└───jenkins-tutorial
        config-jenkins.sh
        deploy-jenkins-vm.sh
```

## <a name="create-container-images"></a>Skapa containeravbildningar

[Docker Compose][docker-compose] kan användas för att automatisera genereringen av containeravbildningar och distributionen av program med flera containrar.

Använd `docker-compose.yaml`-exempelfilen för att skapa containeravbildningen, ladda ned Redis-avbildningen och starta programmet:

```console
docker-compose up -d
```

När kommandot har körts kan du använda kommandot [docker images][docker-images] till att se de avbildningar som skapats. Tre avbildningar har hämtats eller skapats. Den *Azure-röstbaserade* avbildningen innehåller klient programmet och använder bilden *nginx-mätkolv* som bas. *Redis* -avbildningen används för att starta en Redis-instans.

```
$ docker images

REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        9 seconds ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Kör kommandot [docker ps][docker-ps] för att visa de containrar som körs:

```
$ docker ps

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS                           NAMES
d10e5244f237        mcr.microsoft.com/azuredocs/azure-vote-front:v1   "/entrypoint.sh /sta…"   3 minutes ago       Up 3 minutes        443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
21574cb38c1f        mcr.microsoft.com/oss/bitnami/redis:6.0.8         "/opt/bitnami/script…"   3 minutes ago       Up 3 minutes        0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Testa programmet lokalt

Om du vill visa programmet som körs anger du `http://localhost:8080` i en lokal webbläsare. Exempelprogrammet läses in, som du ser i följande exempel:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Skärm bild som visar behållar avbildningen Azure röstning som körs lokalt öppnad i en lokal webbläsare" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

## <a name="clean-up-resources"></a>Rensa resurser

Nu när programfunktionen har verifierats, kan containrarna som körs stoppas och tas bort. ***Ta inte bort behållar avbildningarna** _ – i nästa själv studie kurs överförs _azure-röst-framtill *-avbildningen till en Azure Container Registry-instans.

Stoppa och ta bort containerinstanser och resurser med kommandot [docker-compose down][docker-compose-down]:

```console
docker-compose down
```

När det lokala programmet har tagits bort har du en Docker-avbildning som innehåller Azures röst program, *Azure-rösta-framtill*, för användning med nästa självstudie.

## <a name="next-steps"></a>Nästa steg

I den här självstudien testades ett program och containeravbildningar skapades för programmet. Du har lärt dig att:

> [!div class="checklist"]
> * Klona en exempelprogramkälla från GitHub
> * Skapa en containeravbildning från exempelprogramkällan
> * Testa programmet med flera containrar i en lokal Docker-miljö

Gå vidare till nästa självstudie och lär dig hur du lagrar containeravbildningar i Azure Container Registry.

> [!div class="nextstepaction"]
> [Push-överföra avbildningar till Azure Container Registry][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads
[sample-application]: https://github.com/Azure-Samples/azure-voting-app-redis

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
