---
title: Självstudie om Kubernetes i Azure – Uppdatera ett program
description: I den här självstudien om Azure Kubernetes Service (AKS) lär du dig hur du uppdaterar en befintlig programdistribution till AKS med en ny version av programkoden.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: b969e3ec1c670c0a12129289c8ff7eb81df51ff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98250663"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Självstudie: Uppdatera ett program i Azure Kubernetes Service (AKS)

När ett program har distribuerats i Kubernetes kan du uppdatera det genom att ange en ny containeravbildning eller avbildningsversion. En uppdatering mellanlagras så att bara en del av distributionen uppdateras samtidigt. Den här mellanlagrade uppdateringen gör att programmet kan fortsätta att köras under uppdateringen. Det ger också en mekanism för återställning om ett distributionsfel inträffar.

I den här självstudien, som är del sex av sju, uppdateras Azure Vote-exempelappen. Lär dig att:

> [!div class="checklist"]
> * Uppdatera klientdelens programkod
> * Skapa en uppdaterad containeravbildning
> * Överföra containeravbildningen till Azure Container Registry
> * Distribuera den uppdaterade containeravbildningen

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades en app i en containeravbildning. Den här avbildningen laddades upp till Azure Container Registry, och du skapade ett AKS-kluster. Programmet distribuerades sedan till AKS-klustret.

En programlagringsplats klonades också som inkluderar programmets källkod och en färdig Docker Compose-fil som används i den här självstudien. Verifiera att du har skapat en klon av lagringsplatsen och har ändrat kataloger i den klonade katalogen. Om du inte har slutfört dessa steg och vill följa med börjar du med [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

Den här självstudien kräver att du kör Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="update-an-application"></a>Uppdatera ett program

Vi gör en ändring i exempelprogrammet och uppdaterar sedan den version som redan har distribuerats till ditt AKS-kluster. Se till att befinna dig i den klonade *azure-voting-app-redis*-katalogen. Exempelprogrammets källkod finns sedan inuti katalogen *azure-vote*. Öppna filen *config_file.cfg* med en textredigerare, till exempel `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Ändra värdena för *VOTE1VALUE* och *VOTE2VALUE* till olika värden, till exempel färger. I följande exempel visas de uppdaterade värdena:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Spara och stäng filen. I `vi` använder du `:wq`.

## <a name="update-the-container-image"></a>Uppdatera containeravbildningen

För att återskapa klientdelsavbildningen och testa det uppdaterade programmet använder du [docker-compose][docker-compose]. Argumentet `--build` används till att instruera Docker Compose att återskapa programavbildningen:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Testa appen lokalt

För att kontrollera att den uppdaterade containeravbildningen visar dina ändringar öppnar du en lokal webbläsare till `http://localhost:8080`.

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated.png" alt-text="Skärm bild som visar ett exempel på den uppdaterade behållar avbildningen Azure röstning som körs lokalt öppnad i en lokal webbläsare":::

De uppdaterade värdena som anges i filen *config_file.cfg* visas i appen som körs.

## <a name="tag-and-push-the-image"></a>Tagga och överföra avbildningen

För att använda den uppdaterade avbildningen på rätt sätt taggar du avbildningen *azure-vote-front* med inloggningsservernamnet för ACR-registret. Hämta inloggnings serverns namn med kommandot [AZ ACR List](/cli/azure/acr) :

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Använd [docker tag][docker-tag] till att tagga avbildningen. Ersätt `<acrLoginServer>` med namnet på ACR-inloggningsservern eller värdnamnet för det offentliga registret och uppdatera avbildningsversionen till *:v2* på följande sätt:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v2
```

Använd nu [docker push][docker-push] för att ladda upp avbildningen till registret. Ersätt `<acrLoginServer>` med namnet på din ACR-inloggningsserver.

> [!NOTE]
> Om du får problem med att skicka till ACR-registret kontrollerar du att du fortfarande är inloggad. Kör kommandot [AZ ACR login][az-acr-login] med namnet på din Azure Container Registry som du skapade i steget [skapa ett Azure Container Registry](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) . Till exempel `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Distribuera det uppdaterade programmet

Du får minimala störningar om flera instanser av programpodden körs. Kontrollera antalet klientdelsinstanser som körs med kommandot [kubectl get pods][kubectl-get]:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Om du inte har flera klientdelspoddar skalar du *azure-vote-front*-distributionen på följande sätt:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

När du ska uppdatera programmet använder du kommandot [kubectl set][kubectl-set]. Uppdatera `<acrLoginServer>` med inloggningsservern eller värdnamnet på ditt containerregister, och ange programversionen *v2*:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Du övervakar distributionen med kommandot [kubectl get pod][kubectl-get]. Eftersom det uppdaterade programmet är distribuerat avslutas dina poddar och återskapas med den nya containeravbildningen.

```console
kubectl get pods
```

Följande exempelutdata visar poddar som avslutas och nya instanser som körs medan distributionen pågår:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Testa det uppdaterade programmet

Om du vill visa det uppdaterade programmet hämtar du först den externa IP-adressen för tjänsten `azure-vote-front`:

```console
kubectl get service azure-vote-front
```

Öppna en webbläsare med IP-adressen för din tjänst:

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated-external.png" alt-text="Skärm bild som visar ett exempel på en uppdaterad bild av Azure röstnings program som körs i ett AKS-kluster som öppnas i en lokal webbläsare.":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du uppdaterat ett program och distribuerat uppdateringen till ditt AKS-kluster. Du har lärt dig att:

> [!div class="checklist"]
> * Uppdatera klientdelens programkod
> * Skapa en uppdaterad containeravbildning
> * Överföra containeravbildningen till Azure Container Registry
> * Distribuera den uppdaterade containeravbildningen

Gå vidare till nästa självstudie och lär dig hur du uppgraderar ett AKS-kluster till en ny version av Kubernetes.

> [!div class="nextstepaction"]
> [Uppgradera Kubernetes][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
