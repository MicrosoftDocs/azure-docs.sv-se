---
title: Självstudie – förbereda container Registry för att distribuera avbildning
description: Självstudie om Azure Container Instances del 2 av 3 – förbereda Azure Container Registry och push-överföra en avbildning
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2eda960c53fc7ba851ffcfbe96bd8e9a48844910
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746927"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Självstudie: skapa ett Azure Container Registry och skicka en behållar avbildning

Det här är del två i en serie självstudier med tre delar. I [del ett](container-instances-tutorial-prepare-app.md) av självstudiekursen skapades en Docker-containeravbildning för ett Node.js-webbprogram. I den här självstudien push-överför du avbildningen till Azure Container Registry. Om du ännu inte har skapat containeravbildningen så gå tillbaka till [Självstudie 1 – skapa containeravbildning](container-instances-tutorial-prepare-app.md).

Azure Container Registry är ditt privata Docker-register i Azure. I den här självstudien är del två i serien:

> [!div class="checklist"]
> * Skapa en Azure Container Registry-instans med Azure CLI
> * Tagga en containeravbildning för Azure-containerregistret
> * överföra avbildningen till registret.

I nästa artikel, som är den sista i serien, distribuerar du behållaren från ditt privata register till Azure Container Instances.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>Tagga containeravbildningen

Om du vill vidarebefordra en behållaravbildning till ett privat register som Azure Container Registry, måste du först tagga avbildningen med det fullständiga namnet på registrets inloggningsserver.

Hämta först det fullständiga inloggningsservernamnet för ditt Azure-containerregister. Kör följande [az acr show][az-acr-show]-kommando och ersätt `<acrName>` med namnet på det register som du just har skapat:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Om registret t.ex. heter *mycontainerregistry082*:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

Visa nu listan över dina lokala avbildningar med kommandot [docker images][docker-images]:

```bash
docker images
```

Du bör se den *aci-tutorial-app*-avbildning som du skapade i den [föregående självstudiekursen](container-instances-tutorial-prepare-app.md) tillsammans med övriga bilder som finns på datorn:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Tagga *ACI-självstudie-app-* avbildningen med inloggnings servern för behållar registret. Ange även avbildningens versionsnummer genom att lägga till taggen `:v1` i slutet av avbildningsnamnet. Ersätt `<acrLoginServer>` med resultatet av kommandot [az acr show][az-acr-show] som du körde tidigare.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Verifiera taggningen genom att köra `docker images` igen:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Push-överför avbildningen till Azure Container Registry

Nu när du har märkt *ACI-självstudie-app-* avbildningen med det fullständiga inloggnings Server namnet för ditt privata register, kan du skicka avbildningen till registret med [Docker push][docker-push] -kommandot. Ersätt `<acrLoginServer>` med det fullständiga namnet på inloggningsservern som du erhöll i det tidigare steget.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

`push`-åtgärden kan ta från några sekunder till några minuter beroende på din internetanslutning och utdata bör likna följande:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Lista avbildningar i Azure Container Registry

Verifiera att den avbildning som du just har push-överfört verkligen finns i ditt Azure-containerregister genom att avbildningarna i registret med kommandot [az acr repository list][az-acr-repository-list]. Ersätt `<acrName>` med namnet på ditt containerregister.

```azurecli
az acr repository list --name <acrName> --output table
```

Exempel:

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

Om du vill se *taggarna* för en viss avbildning använder du kommandot [az acr repository show-tags][az-acr-repository-show-tags].

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Du bör se utdata som liknar följande:

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien förberedde du ett Azure Container Registry för användning med Azure Container Instances och push-överförde en behållaravbildning till registret. Följande steg har slutförts:

> [!div class="checklist"]
> * Skapat en Azure Container Registry-instans med Azure CLI
> * tagga en behållaravbildning för Azure Container Registry
> * ladda upp en avbildning till Azure Container Registry.

Gå vidare till nästa självstudiekurs om du vill lära dig hur man distribuerar behållaren till Azure med Azure Container Instances:

> [!div class="nextstepaction"]
> [Distribuera en behållare till Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
