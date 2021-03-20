---
title: Självstudie – förbereda behållar avbildning för distribution
description: Självstudie om Azure Container Instances, del 1 av 3 – Förbereda en app i en containeravbildning för distribution till Azure Container Instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: 1eca34fb5da50b15f15322a3b534556bcd3bda09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91303122"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Självstudie: skapa en behållar avbildning för distribution till Azure Container Instances

Azure Container Instances möjliggör distribution av Docker-behållare till en Azure-infrastruktur utan att tillhandahålla några virtuella datorer eller anpassa eventuella tjänster på högre nivå. I den här självstudiekursen får du paketera ett enkelt Node.js-webbprogram i en behållaravbildning som kan köras med Azure Container Instances.

I den här artikeln, som är del ett i serien, får du göra följande:

> [!div class="checklist"]
> * Klona programmets källkod från GitHub
> * Skapa en containeravbildning från programkällan
> * Testa avbildningarna i en lokal Docker-miljö

I del två och tre av självstudiekursen får du överföra avbildningen till Azure Container Registry och sedan distribuera den till Azure Container Instances.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Hämta programkod

Exempelprogrammet i den här självstudiekursen är en enkel webbapp som skapats i [Node.js][nodejs]. Programmet har en statisk HTML-sida och ser ut ungefär som på följande skärmbild:

![Självstudieappen visas i webbläsare][aci-tutorial-app]

Använd Git om du vill klona exempelprogrammets lagringsplats:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

Du kan också [hämta ZIP-arkivet][aci-helloworld-zip] från GitHub direkt.

## <a name="build-the-container-image"></a>Bygga containeravbildningen

Den Dockerfile som finns i exempelprogrammet visar hur containern är byggd. Den börjar från en [officiell Node.js-avbildning][docker-hub-nodeimage] baserat på [Alpine Linux][alpine-linux], en liten distribution som är lämplig för användning med containrar. Den kopierar sedan programfilerna till containern, installerar beroenden med Node Package Manager (nodpaketshanteraren) och startar slutligen programmet.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Använd kommandot [docker build][docker-build] (dockerbygge) för att skapa containeravbildningen, och märk den som *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

Utdata från kommandot [docker build][docker-build] (dockerbygge) liknar följande (trunkerat för läsbarhet):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Använd kommandot [dockeravbildning][docker-images] för att se den skapade avbildningen:

```bash
docker images
```

Den nya inbyggda avbildningen ska synas i listan:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Kör containern lokalt

Innan du distribuerar behållaren till Azure Container Instances, så använd [docker run][docker-run] så att du kan köra den lokalt och bekräfta att den fungerar. Med växeln `-d` kan du köra containern i bakgrunden, medan `-p` gör att du kan mappa en godtycklig port för dina beräkningar till port 80 i containern.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

Utdata från `docker run`-kommandot visar ID:t för den container som körs om kommandot lyckas:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Navigera nu till `http://localhost:8080` i webbläsaren och bekräfta att containern körs. Du bör se en webbsida som liknar följande:

![Köra appen lokalt i webbläsaren][aci-tutorial-app-local]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en behållaravbildning som kan distribueras i Azure Container Instances, och verifierat att den körs lokalt. Hittills har du gjort följande:

> [!div class="checklist"]
> * Klonade programkällan från GitHub
> * Skapat en containeravbildning från programkällkoden
> * Testade containern lokalt

Fortsätt till nästa självstudie i serien och lär dig hur du lagrar din behållaravbildning i Azure Container Registry:

> [!div class="nextstepaction"]
> [Push-överför avbildningen till Azure Container Registry](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
