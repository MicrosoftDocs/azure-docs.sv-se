---
title: Skapa behållar avbildningar på Service Fabric i Azure
description: I den här självstudiekursen lär du dig hur du skapar containeravbildningar för ett Service Fabric-program med flera containrar.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 31b5f870465bc1dff9d6ff7827a4efed084bcf62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92739058"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Självstudie: Skapa containeravbildningar i ett Service Fabric-kluster i Linux

Den här självstudien ingår i en serie som visar hur du använder containrar i ett Linux Service Fabric-kluster. I den här självstudien förbereds ett program med flera container för användning med Service Fabric. I efterföljande självstudier används de här avbildningarna som en del i ett Service Fabric-program. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * klona programmets källkod från GitHub
> * Skapa en containeravbildning från programkällkoden
> * distribuera en ACR-instans (Azure Container Registry)
> * Tagga en containeravbildning för ACR
> * ladda upp avbildningen till ACR.

I den här självstudieserien får du lära du dig att:

> [!div class="checklist"]
> * Skapa containeravbildningar för Service Fabric
> * [Skapa och köra ett Service Fabric-program med containrar](service-fabric-tutorial-package-containers.md)
> * [Hantera redundans och skalning i Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Förutsättningar

* Linux-utvecklingsmiljö konfigurerad för Service Fabric. Följ instruktionerna [här](service-fabric-get-started-linux.md) för att konfigurera din Linux-miljö.
* I den här självstudien krävs att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).
* Dessutom måste du ha en Azure-prenumeration tillgänglig. Mer information om en kostnadsfri utvärderingsversion finns [här](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Hämta programkod

Exempelprogrammet som används i den här självstudien är en röstningsapp. Programmet består av en webbkomponent på klientsidan och en Redis-instans på serversidan. Komponenterna är paketerade i containeravbildningar.

Använd git och ladda ned en kopia av programmet till utvecklingsmiljön.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

Lösningen innehåller två mappar och filen docker-compose.yml. Mappen azure-vote innehåller Python-tjänsten för klientdelen och den dockerfil som används till att skapa avbildningen. Katalogen Voting innehåller Service Fabric-programpaketet som distribueras till klustret. Dessa kataloger innehåller de resurser som behövs i den här självstudien.

## <a name="create-container-images"></a>Skapa containeravbildningar

Öppna katalogen **azure-vote** och kör följande kommando för att skapa avbildningen för webbkomponenten på klientsidan. I kommandot används dockerfilen i katalogen till att skapa avbildningen.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Om du får ett meddelande om nekad åtkomst följer du anvisningarna i [den här](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) dokumentationen som beskriver hur du arbetar med Docker utan sudo.

Det här kommandot kan ta lite tid eftersom alla nödvändiga beroenden måste hämtas från Docker Hub. När kommandot har körts kan du använda kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/) till att se de avbildningar som skapats.

```bash
docker images
```

Observera att två avbildningar har laddats ned eller skapats. Avbildningen *azure-vote-front* innehåller själva programmet. Det har härletts från en *Python*-avbildning från Docker Hub.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Distribuera Azure Container Registry

Kör först kommandot **AZ login** för att logga in på ditt Azure-konto.

```azurecli
az login
```

Använd sedan kommandot **az account** till att välja din prenumeration och skapa Azure-containerregistret. Du måste ange ID:t för din Azure-prenumeration i stället för <subscription_id>.

```azurecli
az account set --subscription <subscription_id>
```

När du distribuerar ett Azure Container Registry behöver du först en resursgrupp. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot **az group create**. I det här exemplet skapas en resursgrupp med namnet *myResourceGroup* i regionen *westus*.

```azurecli
az group create --name <myResourceGroup> --location westus
```

Skapa ett Azure Container Registry med kommandot **AZ ACR Create** . Ersätt \<acrName> med namnet på det behållar register som du vill skapa under din prenumeration. Det här namnet måste vara alfanumeriskt och unikt.

```azurecli
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

I resten av den här självstudien använder vi acrName som platshållare för det containerregisternamn du väljer. Skriv ned det här värdet.

## <a name="sign-in-to-your-container-registry"></a>Logga in på ditt behållar register

Logga in på din ACR-instans innan du skickar avbildningar till den. Använd kommandot **az acr login** till att slutföra åtgärden. Ange det unika namn du angav för containerregistret när det skapades.

```azurecli
az acr login --name <acrName>
```

Du får ett meddelande om att inloggningen lyckades när inloggningen är klar.

## <a name="tag-container-images"></a>Tagga containeravbildningar

Varje containeravbildning måste taggas med namnet på inloggningsservern för registret. Den här taggen används till routning när du push-överför containeravbildningar till ett avbildningsregister.

Om du vill se en lista med aktuella avbildningar använder du kommandot [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Utdata:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Du hämtar namnet på inloggningsservern genom att köra följande kommando:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Utdata är en tabell med följande resultat. Resultatet kommer att användas när du ska tagga avbildningen **azure-vote-front** innan du push-överför den till containerregistret i nästa steg.

```output
Result
------------------
<acrName>.azurecr.io
```

Tagga nu avbildningen *azure-vote-front* med namnet på inloggningsservern för containerregistret. Lägg även till `:v1` i slutet av avbildningens namn. Den här taggen anger versionsnumret för avbildningen.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

När taggningen är färdig verifierar du åtgärden genom att köra ”docker images”.

Utdata:

```output
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Push-överför avbildningar till registret

Push-överför avbildningen *azure-vote-front* till registret. 

Använd följande exempel och ersätt namnet på ACR-inloggningsservern med inloggningsnamnet från din miljö.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Docker push-kommandona tar ett par minuter att slutföra.

## <a name="list-images-in-registry"></a>Lista med avbildningar i registret

Du kan returnera en lista med avbildningar som push-överförts till Azure-containerregistret med kommandot [az acr repository list](/cli/azure/acr/repository). Uppdatera kommandot med namnet på ACR-instansen.

```azurecli
az acr repository list --name <acrName> --output table
```

Utdata:

```output
Result
----------------
azure-vote-front
```

När självstudien är färdig har behållaravbildningen lagrats i en privat Azure Container Registry-instans. Den här avbildningen distribueras från ACR till ett Service Fabric-kluster i efterföljande självstudier.

## <a name="next-steps"></a>Nästa steg

I den här självstudien hämtades ett program från GitHub. Sedan skapades och push-överfördes containeravbildningar till ett register. Följande steg har slutförts:

> [!div class="checklist"]
> * klona programmets källkod från GitHub
> * Skapa en containeravbildning från programkällkoden
> * distribuera en ACR-instans (Azure Container Registry)
> * Tagga en containeravbildning för ACR
> * ladda upp avbildningen till ACR.

Gå vidare till nästa självstudie om du vill veta mer om paketering av containrar till ett Service Fabric-program med hjälp av Yeoman.

> [!div class="nextstepaction"]
> [Paketera och distribuera containrar som ett Service Fabric-program](service-fabric-tutorial-package-containers.md)
