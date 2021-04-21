---
title: Skapa avbildning med Cloud Native Buildpack
description: Använd kommandot az acr pack build för att skapa en containeravbildning från en app och push-Azure Container Registry, utan att använda en Dockerfile.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1700c8fda8ac91e7d447d35c0989da2d5fc3aefe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780937"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Skapa och push-pusha en avbildning från en app med hjälp av cloud native buildpack

Azure CLI-kommandot använder CLI-verktyget från Buildpacks för att skapa en app och `az acr pack build` [`pack`](https://github.com/buildpack/pack) skicka dess avbildning till ett Azure-containerregister. [](https://buildpacks.io/) Med den här funktionen kan du snabbt skapa en containeravbildning från programmets källkod i Node.js, Java och andra språk utan att behöva definiera en Dockerfile.

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att köra exemplen i den här artikeln. Om du vill använda den lokalt krävs version 2.0.70 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="use-the-build-command"></a>Använd build-kommandot

Om du vill skapa och push-skicka en containeravbildning med Hjälp av Cloud Native Buildpacks kör du [kommandot az acr pack build.][az-acr-pack-build] Kommandot [az acr build][az-acr-build] skapar och push-erar en avbildning från en Dockerfile-källa och relaterad kod, men du anger ett `az acr pack build` programkällträd direkt.

Ange minst följande när du kör `az acr pack build` :

* Ett Azure-containerregister där du kör kommandot
* Ett avbildningsnamn och en tagg för den resulterande avbildningen
* En av de [kontextplatser som stöds](container-registry-tasks-overview.md#context-locations) för ACR-uppgifter, till exempel en lokal katalog, en GitHub-lagringsplats eller en fjärransluten tarball
* Namnet på en Buildpack Builder-avbildning som passar ditt program. Azure Container Registry cachelagrar byggaravbildningar, `cloudfoundry/cnb:0.0.34-cflinuxfs3` till exempel för snabbare byggen.  

`az acr pack build`har stöd för andra funktioner ACR-uppgifter kommandon, inklusive körningsvariabler och aktivitetskörningsloggar som [strömmas](container-registry-tasks-reference-yaml.md#run-variables) och även sparas för senare hämtning. [](container-registry-tasks-logs.md)

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exempel: Skapa Node.js med Cloud Foundry builder

I följande exempel skapar vi en containeravbildning från en Node.js-app i [lagringsplatsen Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) med hjälp av `cloudfoundry/cnb:0.0.34-cflinuxfs3` byggaren. Den här byggaren cachelagras Azure Container Registry, så `--pull` en parameter krävs inte:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Det här exemplet skapar `node-app` avbildningen med `1.0` taggen och push-erar den till *containerregistret myregistry.* I det här exemplet är målregistrets namn uttryckligen före avbildningens namn. Om inget anges läggs namnet på inloggningsservern för registret automatiskt till avbildningens namn.

Kommandoutdata visar förloppet för att skapa och push-pusha avbildningen. 

När avbildningen har skapats kan du köra den med Docker, om du har installerat den. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör avbildningen:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Gå till `localhost:1337` i din favoritwebbläsare för att se exempelwebbappen. Tryck `[Ctrl]+[C]` på för att stoppa containern.

## <a name="example-build-java-image-with-heroku-builder"></a>Exempel: Skapa Java-avbildning med Heroku Builder

I följande exempel skapar vi en containeravbildning från Java-appen i lagringsplatsen [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) med hjälp av `heroku/buildpacks:18` byggaren. Parametern `--pull` anger att kommandot ska hämta den senaste byggaravbildningen. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Det här exemplet skapar avbildningen som taggats med körnings-ID:t för kommandot och `java-app` push-erar den till *containerregistret myregistry.*

Kommandoutdata visar förloppet för att skapa och push-pusha avbildningen. 

När avbildningen har skapats kan du köra den med Docker, om du har installerat den. Logga först in i registret:

```azurecli
az acr login --name myregistry
```

Kör avbildningen och ersätt avbildningstaggen *med runid*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Gå till `localhost:8080` i din favoritwebbläsare för att se exempelwebbappen. Tryck `[Ctrl]+[C]` på för att stoppa containern.


## <a name="next-steps"></a>Nästa steg

När du har byggt och push-distribuerat en containeravbildning med kan du distribuera den på samma `az acr pack build` sätt som vilken avbildning du vill till ett mål. Bland distributionsalternativen för Azure kan [du köra den App Service](../app-service/tutorial-custom-container.md) eller [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md), bland annat.

Mer information om hur ACR-uppgifter finns i [Automatisera containeravbildningsbyggen och underhåll med ACR-uppgifter](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
