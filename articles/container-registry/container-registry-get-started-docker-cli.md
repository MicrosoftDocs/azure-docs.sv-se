---
title: Push-& hämta containeravbildning
description: Skicka och hämta Docker-avbildningar till ditt privata containerregister i Azure med hjälp av Docker CLI
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 48f5f1707881ac8461e12212be631d3b80c16ca7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783835"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Skicka din första avbildning till Azure-containerregistret med docker-CLI

Ett Azure-containerregister lagrar och hanterar privata containeravbildningar och andra artefakter, på samma [sätt som Docker Hub](https://hub.docker.com/) lagrar offentliga Docker-containeravbildningar. Du kan använda [Docker-kommandoradsgränssnittet](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) för [inloggning,](https://docs.docker.com/engine/reference/commandline/login/) [push-push,](https://docs.docker.com/engine/reference/commandline/push/) [pull](https://docs.docker.com/engine/reference/commandline/pull/)och andra containeravbildningsåtgärder i containerregistret.

I följande steg laddar du ned en offentlig [Nginx-avbildning,](https://store.docker.com/images/nginx)taggar den för ditt privata Azure-containerregister, push-överför den till registret och hämtar den sedan från registret.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-containerregister** – Skapa ett containerregister i din Azure-prenumeration. Du kan till exempel använda [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** – Du måste också ha Docker installerat lokalt. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.

## <a name="log-in-to-a-registry"></a>Logga in i ett register

Det finns [flera sätt att autentisera](container-registry-authentication.md) till ditt privata containerregister. Den rekommenderade metoden när du arbetar på en kommandorad är med Azure CLI-kommandot [az acr login](/cli/azure/acr#az_acr_login). Om du till exempel vill logga in på ett register med *namnet myregistry* loggar du in på Azure CLI och autentiserar sedan till registret:

```azurecli
az login
az acr login --name myregistry
```

Du kan också logga in med [Docker-inloggning.](https://docs.docker.com/engine/reference/commandline/login/) Du kan till exempel ha tilldelat [ett huvudnamn för tjänsten](container-registry-authentication.md#service-principal) till registret för ett automatiseringsscenario. När du kör följande kommando anger du appID (användarnamn) och lösenord för tjänstens huvudnamn interaktivt när du uppmanas till det. Metodtips för att hantera inloggningsuppgifter finns i [kommandoreferensen för docker-inloggning:](https://docs.docker.com/engine/reference/commandline/login/)

```
docker login myregistry.azurecr.io
```

Båda kommandona returneras `Login Succeeded` när de har slutförts.
> [!NOTE]
>* Du kanske vill använda Visual Studio Code med Docker-tillägg för en snabbare och enklare inloggning.

> [!TIP]
> Ange alltid det fullständigt kvalificerade registernamnet (endast gemener) när du använder och när du taggar avbildningar `docker login` för push-push till registret. I exemplen i den här artikeln är det fullständigt kvalificerade *namnet myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Hämta en offentlig Nginx-avbildning

Hämta först en offentlig Nginx-avbildning till din lokala dator. Det här exemplet hämtar en avbildning från Microsoft Container Registry.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Kör containern lokalt

Kör följande [docker run-kommando](https://docs.docker.com/engine/reference/run/) för att starta en lokal instans av Nginx-containern interaktivt ( `-it` ) på port 8080. Argumentet `--rm` anger att containern ska tas bort när du stoppar den.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Bläddra till `http://localhost:8080` för att visa standardwebbsidan som betjänas av Nginx i containern som körs. Du bör se en sida som liknar följande:

![Nginx på lokal dator](./media/container-registry-get-started-docker-cli/nginx.png)

Eftersom du startade containern interaktivt med kan du se Nginx-serverns utdata på kommandoraden när du har navigerat `-it` till den i webbläsaren.

Tryck på för att stoppa och ta bort `Control` + `C` containern.

## <a name="create-an-alias-of-the-image"></a>Skapa ett alias för avbildningen

Använd [docker-taggen](https://docs.docker.com/engine/reference/commandline/tag/) för att skapa ett alias för avbildningen med den fullständigt kvalificerade sökvägen till registret. I det här exemplet anges `samples`-namnområdet för att undvika oreda i registrets rot.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

Mer information om taggning med namnområden finns i avsnittet Namnrymder för [lagringsplats](container-registry-best-practices.md#repository-namespaces) [i Metodtips för Azure Container Registry](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Skicka avbildningen till registret

Nu när du har taggat avbildningen med den fullständigt kvalificerade sökvägen till ditt privata register kan du skicka den till registret med [docker push:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Hämta avbildningen från registret

Använd [kommandot docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta avbildningen från registret:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Starta Nginx-containern

Använd [kommandot docker run](https://docs.docker.com/engine/reference/run/) för att köra avbildningen som du har tagit från registret:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Bläddra till `http://localhost:8080` för att visa containern som körs.

Tryck på för att stoppa och ta bort `Control` + `C` containern.

## <a name="remove-the-image-optional"></a>Ta bort avbildningen (valfritt)

Om du inte längre behöver Nginx-avbildningen kan du ta bort den lokalt med [kommandot docker rmi.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Om du vill ta bort avbildningar från Azure-containerregistret kan du använda Azure CLI-kommandot [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete). Följande kommando tar till exempel bort det manifest som taggen refererar till, alla unika lagerdata och alla andra taggar `samples/nginx:latest` som refererar till manifestet.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Nästa steg

Nu när du känner till grunderna är du redo att börja använda registret! Du kan till exempel distribuera containeravbildningar från ditt register till:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Du kan också installera [Docker-tillägget för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och [Azure-kontotillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) för att arbeta med dina Azure-containerregister. Hämta och push-pusha avbildningar till ett Azure-containerregister eller kör ACR-uppgifter i Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
