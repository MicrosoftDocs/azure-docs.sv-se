---
title: Avbildning av push-& Hämta behållare
description: Push-överför och hämta Docker-avbildningar till ditt privata behållar register i Azure med hjälp av Docker CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 83ef385313b035f5e5d7d993e7948725906c75a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99987762"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Skicka din första avbildning till ditt Azure Container Registry med hjälp av Docker CLI

Ett Azure Container Registry lagrar och hanterar privata behållar avbildningar och andra artefakter, på samma sätt som [Docker Hub](https://hub.docker.com/) lagrar offentliga Docker-behållar avbildningar. Du kan använda [Docker-kommando rads gränssnittet](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) för [inloggnings](https://docs.docker.com/engine/reference/commandline/login/)-, [push](https://docs.docker.com/engine/reference/commandline/push/)-, [pull](https://docs.docker.com/engine/reference/commandline/pull/)-och annan behållar avbildnings åtgärder i behållar registret.

I följande steg laddar du ned en offentlig [nginx-avbildning](https://store.docker.com/images/nginx), tagga den för ditt privata Azure Container Registry, push-överför den till registret och hämtar den sedan från registret.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-containerregister** – Skapa ett containerregister i din Azure-prenumeration. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** – du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="log-in-to-a-registry"></a>Logga in i ett register

Det finns [flera sätt att autentisera](container-registry-authentication.md) till ditt privata behållar register. Den rekommenderade metoden när du arbetar i en kommando rad är med Azure CLI-kommandot [AZ ACR login](/cli/azure/acr#az-acr-login). Om du till exempel vill logga in i ett register med namnet min *Registry* loggar du in på Azure CLI och autentiserar sedan till registret:

```azurecli
az login
az acr login --name myregistry
```

Du kan också logga in med [Docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/). Du kan till exempel ha [tilldelat ett huvud namn för tjänsten](container-registry-authentication.md#service-principal) i registret för ett automatiserings scenario. När du kör följande kommando anger du tjänstens huvud namn (username) och lösen ord när du uppmanas att göra det. Metod tips för att hantera inloggnings uppgifter finns i kommando referensen [Docker login](https://docs.docker.com/engine/reference/commandline/login/) :

```
docker login myregistry.azurecr.io
```

Båda kommandona returnerades `Login Succeeded` när de har slutförts.
> [!NOTE]
>* Du kanske vill använda Visual Studio Code med Docker-utsträckning för en snabbare och mer bekväm inloggning.

> [!TIP]
> Ange alltid det fullständigt kvalificerade register namnet (alla gemener) när du använder `docker login` och när du taggar avbildningar för att överföra till registret. I exemplen i den här artikeln är det fullständigt kvalificerade namnet *myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Hämta en offentlig nginx-avbildning

Börja med att hämta en offentlig nginx-avbildning till din lokala dator. Det här exemplet hämtar en avbildning från Microsoft Container Registry.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Kör containern lokalt

Kör följande [Docker-körnings](https://docs.docker.com/engine/reference/run/) kommando för att starta en lokal instans av nginx-behållaren interaktivt ( `-it` ) på Port 8080. `--rm`Argumentet anger att behållaren ska tas bort när du stoppar den.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Bläddra till `http://localhost:8080` om du vill visa standard webb sidan som hanteras av nginx i behållaren som körs. Du bör se en sida som liknar följande:

![Nginx på lokal dator](./media/container-registry-get-started-docker-cli/nginx.png)

Eftersom du startade behållaren interaktivt med kan `-it` du se nginx-serverns utdata på kommando raden när du har navigerat till den i webbläsaren.

Om du vill stoppa och ta bort behållaren trycker du på `Control` + `C` .

## <a name="create-an-alias-of-the-image"></a>Skapa ett alias för avbildningen

Använd [Docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) för att skapa ett alias för avbildningen med den fullständigt kvalificerade sökvägen till registret. I det här exemplet anges `samples`-namnområdet för att undvika oreda i registrets rot.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Mer information om Taggar med namn områden finns i avsnittet [databas namn områden](container-registry-best-practices.md#repository-namespaces) i [metod tips för Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Skicka avbildningen till registret

Nu när du har taggat avbildningen med den fullständigt kvalificerade sökvägen till ditt privata register, kan du skicka den till registret med [Docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Hämta avbildningen från registret

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta avbildningen från registret:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Starta Nginx-containern

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/run/) för att köra avbildningen som du har hämtat från registret:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Bläddra till `http://localhost:8080` för att Visa behållaren som körs.

Om du vill stoppa och ta bort behållaren trycker du på `Control` + `C` .

## <a name="remove-the-image-optional"></a>Ta bort avbildningen (valfritt)

Om du inte längre behöver nginx-avbildningen kan du ta bort den lokalt med kommandot [Docker RMI](https://docs.docker.com/engine/reference/commandline/rmi/) .

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Om du vill ta bort avbildningar från Azure Container Registry kan du använda Azure CLI-kommandot [AZ ACR-lagringsplats Delete](/cli/azure/acr/repository#az-acr-repository-delete). Följande kommando tar till exempel bort manifestet som refereras av `samples/nginx:latest` taggen, alla unika skikt data och alla andra taggar som refererar till manifestet.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet grunderna är det dags att börja använda ditt register! Du kan till exempel distribuera behållar avbildningar från registret för att:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Du kan också installera [Docker-tillägget för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och tillägget [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure Container register. Hämta och push-avbildningar till ett Azure Container Registry, eller kör ACR-aktiviteter, allt i Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
