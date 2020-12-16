---
title: GitHub-åtgärder & Azure Kubernetes service (för hands version)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Granska och testa ändringar från en pull-begäran direkt i Azure Kubernetes-tjänsten med hjälp av GitHub-åtgärder och Azure dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, GitHub Actions, Helm, service nät, service mask-routning, kubectl, K8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 447c41055ededfc55e44bebd92de89b3d23de3c7
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591573"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub-åtgärder & Azure Kubernetes service (för hands version)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure dev Spaces innehåller ett arbets flöde med GitHub-åtgärder som gör att du kan testa ändringar från en pull-begäran direkt i AKS innan pull-begäran slås samman i huvud grenen för din databas. Att ha ett program som körs för att granska ändringar av en pull-begäran kan öka säkerheten både för utvecklare och grupp medlemmar. Detta program som körs kan också hjälpa grupp medlemmar, till exempel produkt chefer och designers, att bli en del av gransknings processen under de första utvecklings faserna.

I den här guiden får du lära dig hur du:

* Konfigurera Azure dev Spaces i ett hanterat Kubernetes-kluster i Azure.
* Distribuera ett stort program med flera mikrotjänster till ett dev-utrymme.
* Konfigurera CI/CD med GitHub-åtgärder.
* Testa en enskild mikrotjänst i ett isolerat dev-utrymme inom ramen för det fullständiga programmet.

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free).
* [Azure CLI installerat][azure-cli-installed].
* [Helm 3 är installerat][helm-installed].
* Ett GitHub-konto med [aktiverade GitHub-åtgärder][github-actions-beta-signup].
* [Exempel programmet för cykel delning i Azure dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) som körs på ett AKS-kluster.

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

Skapa en Azure Container Registry (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Namnet ACR måste vara unikt i Azure och innehålla 5-50 alfanumeriska tecken. Alla bokstäver som du använder måste vara gemena.

Spara *namnet* -värdet från utdata eftersom det används i ett senare steg.

## <a name="create-a-service-principal-for-authentication"></a>Skapa ett huvud namn för tjänsten för autentisering

Använd [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] för att skapa ett huvud namn för tjänsten. Exempel:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Spara JSON-utdata eftersom det används i ett senare steg.

Använd [AZ AKS show][az-aks-show] för att visa *ID: t* för ditt AKS-kluster:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Använd [AZ ACR show][az-acr-show] för att visa *ID: t* för ACR:

```azurecli
az acr show --name <acrName> --query id
```

Använd [AZ roll tilldelning skapa][az-role-assignment-create] för att ge *deltagar* åtkomst till ditt AKS-kluster och *AcrPush* åtkomst till din ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Du måste vara ägare till både ditt AKS-kluster och ACR för att ge tjänstens huvud namn åtkomst till dessa resurser.

## <a name="configure-your-github-action"></a>Konfigurera din GitHub-åtgärd

> [!IMPORTANT]
> Du måste ha aktiverat GitHub-åtgärder för din lagrings plats. Om du vill aktivera GitHub-åtgärder för din lagrings plats går du till din lagrings plats på GitHub, klickar på fliken åtgärder och väljer att aktivera åtgärder för den här lagrings platsen.

Navigera till din förgrenade lagrings plats och klicka på *Inställningar*. Klicka på *hemligheter* i den vänstra panelen. Klicka på *Lägg till en ny hemlighet* för att lägga till varje ny hemlighet nedan:

1. *AZURE_CREDENTIALS*: hela utdata från tjänstens huvud namn skapas.
1. *RESOURCE_GROUP*: resurs gruppen för ditt AKS-kluster, som i det här exemplet är *MyResourceGroup*.
1. *CLUSTER_NAME*: namnet på ditt AKS-kluster, som i det här exemplet är *MyAKS*.
1. *CONTAINER_REGISTRY*: *namnet* för ACR.
1. *Värd*: värden för ditt dev Space, som tar formuläret *<MASTER_SPACE>. <APP_NAME>. <* HOST_SUFFIX>, som i det här exemplet är *dev.bikesharingweb.fedcab0987.EUs.azds.io*.
1. *IMAGE_PULL_SECRET*: namnet på hemligheten som du vill använda, till exempel *demo-Secret*.
1. *MASTER_SPACE*: namnet på det överordnade dev-utrymmet, som i det här exemplet är *dev*.
1. *REGISTRY_USERNAME*: *clientId* från JSON-utdata från det att tjänstens huvud namn skapas.
1. *REGISTRY_PASSWORD*: *clientSecret* från JSON-utdata från det att tjänstens huvud namn skapas.

> [!NOTE]
> Alla dessa hemligheter används av GitHub-åtgärden och konfigureras i [. GitHub/arbets flöden/Bikes. yml][github-action-yaml].

Om du vill uppdatera huvud utrymmet efter att din PR har slagits samman lägger du till *GATEWAY_HOST* hemlighet, som tar formuläret *<MASTER_SPACE>. GATEWAY. <HOST_SUFFIX>*, som i det här exemplet är *dev.Gateway.fedcab0987.EUs.azds.io*. När du sammanfogar dina ändringar i huvud grenen i din förgrening, kommer en annan åtgärd att köras för att återskapa och köra hela programmet i huvud utvecklings utrymmet. I det här exemplet är huvud utrymmet *dev*. Den här åtgärden har kon figurer ATS i [. GitHub/Workflows/bikesharing. yml][github-action-bikesharing-yaml].

Dessutom, om du vill att ändringarna i din PR ska köras i ett grandchild-utrymme, uppdaterar du *MASTER_SPACE* och *värd* hemligheter. Om ditt program till exempel körs i *dev* med en *azureuser1 dev/*, så att PR-körningen kan köras i ett underordnat utrymme med *dev/azureuser1*:

* Uppdatera *MASTER_SPACE* till det underordnade utrymmet som du vill ha som överordnat utrymme, i det här exemplet *azureuser1*.
* Uppdatera *värd* till *<GRANDPARENT_SPACE>. <APP_NAME>.*<HOST_SUFFIX>, i det här exemplet *dev.bikesharingweb.fedcab0987.EUs.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Skapa en ny gren för kod ändringar

Navigera till `BikeSharingApp/` och skapa en ny gren med namnet *Bikes-bilder*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Redigera [cyklar/server.js][bikes-server-js] för att ta bort raderna 232 och 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Avsnittet bör nu se ut så här:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Spara filen `git add` och Använd och `git commit` för att mellanlagra ändringarna.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Skicka dina ändringar

Använd `git push` för att skicka din nya gren till din förgrenade lagrings plats:

```cmd
git push origin bike-images
```

När push-åtgärden är klar navigerar du till din förgrenade lagrings plats på GitHub för att skapa en pull-begäran med *huvud* grenen i din förgrenade lagrings plats som bas gren jämfört med grenen *cykel-images* .

När din pull-begäran har öppnats går du till fliken *åtgärder* . Verifiera att en ny åtgärd har börjat och bygger tjänsten *Bikes* .

## <a name="view-the-child-space-with-your-changes"></a>Visa det underordnade utrymmet med dina ändringar

När åtgärden har slutförts visas en kommentar med en URL till ditt nya underordnade utrymme baserat på ändringarna i pull-begäran.

> [!div class="mx-imgBorder"]
> ![GitHub åtgärds-URL](../media/github-actions/github-action-url.png)

Navigera till *bikesharingweb* -tjänsten genom att öppna URL: en från kommentaren. Välj *Aurelia Briggs (kund)* som användare och välj sedan en cykel att hyra. Kontrol lera att du inte längre ser platshållarbilden för cykeln.

Om du sammanfogar dina ändringar i *huvud* grenen i din förgrening, kommer en annan åtgärd att köras för att återskapa och köra hela programmet i det överordnade dev-utrymmet. I det här exemplet är det överordnade utrymmet *dev*. Den här åtgärden har kon figurer ATS i [. GitHub/Workflows/bikesharing. yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
