---
title: Bygg avbildning med inbyggd molnbaserad Buildpack
description: Använd kommandot AZ ACR Pack build för att bygga en behållar avbildning från en app och skicka till Azure Container Registry, utan att använda en Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 264fc7314c78088ebfefb9ddb8edbe38fa16581a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92736628"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Bygga och skicka en avbildning från en app med hjälp av en inbyggd Cloud-Buildpack

Azure CLI-kommandot `az acr pack build` använder [`pack`](https://github.com/buildpack/pack) CLI-verktyget, från [Buildpacks](https://buildpacks.io/), för att bygga en app och skicka avbildningen till ett Azure Container Registry. Den här funktionen ger ett alternativ för att snabbt skapa en behållar avbildning från program käll koden i Node.js, Java och andra språk utan att behöva definiera en Dockerfile.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra exemplen i den här artikeln. Om du vill använda det lokalt, krävs version 2.0.70 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="use-the-build-command"></a>Använda kommandot build

Om du vill skapa och skicka en behållar avbildning med hjälp av inbyggda Cloud-Buildpacks kör du kommandot [AZ ACR Pack build][az-acr-pack-build] . [AZ ACR build][az-acr-build] -kommandot skapar och skickar en avbildning från en Dockerfile källa och relaterad kod, där `az acr pack build` du anger ett program käll träd direkt.

Ange minst följande när du kör `az acr pack build` :

* Ett Azure Container Registry där du kör kommandot
* Ett avbildnings namn och en tagg för den resulterande bilden
* En av de [kontext platser som stöds](container-registry-tasks-overview.md#context-locations) för ACR-aktiviteter, till exempel en lokal katalog, en GitHub-lagrings platsen eller en fjärran sluten tarball
* Namnet på en Buildpack Builder-avbildning som passar ditt program. Azure Container Registry Caches Builder-bilder, till exempel `cloudfoundry/cnb:0.0.34-cflinuxfs3` för snabbare versioner.  

`az acr pack build` stöder andra funktioner i ACR tasks-kommandon, inklusive [körning av variabler](container-registry-tasks-reference-yaml.md#run-variables) och körnings [loggar för uppgifter](container-registry-tasks-logs.md) som strömmas och som också sparas för senare hämtning.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exempel: Bygg Node.js-avbildning med Cloud Foundry Builder

I följande exempel skapas en behållar avbildning från en Node.js-app i mappen [Azure-samples/NodeJS-dok-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) lagrings platsen med hjälp av `cloudfoundry/cnb:0.0.34-cflinuxfs3` verktyget. Det här verktyget cachelagras av Azure Container Registry, så en `--pull` parameter krävs inte:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

I det här exemplet skapas `node-app` avbildningen med `1.0` taggen och skickas till registret för  behållar registret. I det här exemplet är mål register namnet explicit anpassningsprefix till avbildningens namn. Om inget anges anpassningsprefix namnet på inloggnings servern för registret automatiskt till avbildningens namn.

Kommandoutdata visar förloppet för att skapa och skicka avbildningen. 

När avbildningen har skapats kan du köra den med Docker, om du har den installerad. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör avbildningen:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Bläddra till `localhost:1337` i din favorit webbläsare för att se exempel webb programmet. Tryck `[Ctrl]+[C]` för att stoppa behållaren.

## <a name="example-build-java-image-with-heroku-builder"></a>Exempel: bygga Java-avbildning med Heroku Builder

I följande exempel skapas en behållar avbildning från Java-appen i [buildpack/Sample-java-app](https://github.com/buildpack/sample-java-app) lagrings platsen med hjälp av `heroku/buildpacks:18` verktyget. `--pull`Parametern anger att kommandot ska hämta den senaste Builder-avbildningen. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

I det här exemplet skapas en `java-app` avbildning som taggats med kommandots körnings-ID och push  -överför den till registret för behållar behållaren.

Kommandoutdata visar förloppet för att skapa och skicka avbildningen. 

När avbildningen har skapats kan du köra den med Docker, om du har den installerad. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör avbildningen och ersätt din avbildnings tag för *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Bläddra till `localhost:8080` i din favorit webbläsare för att se exempel webb programmet. Tryck `[Ctrl]+[C]` för att stoppa behållaren.


## <a name="next-steps"></a>Nästa steg

När du har skapat och push-överför en behållar avbildning med `az acr pack build` kan du distribuera den som valfri avbildning till önskat mål. Azures distributions alternativ omfattar att köra det i [App Service](../app-service/tutorial-custom-container.md) [-eller Azure Kubernetes-tjänsten](../aks/tutorial-kubernetes-deploy-cluster.md), bland annat.

Mer information om funktioner för ACR-funktioner finns i [Automatisera behållar avbildnings versioner och underhåll med ACR-uppgifter](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
