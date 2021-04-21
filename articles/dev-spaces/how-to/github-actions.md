---
title: GitHub Actions & Azure Kubernetes Service (förhandsversion)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Granska och testa ändringar från en pull-begäran direkt i Azure Kubernetes Service med GitHub Actions och Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, GitHub Actions, Helm, service mesh, service mesh routing, kubectl, k8s
manager: gwallace
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 84d4f94cdb756b0bc11026baaa3acf065604c421
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777553"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Actions & Azure Kubernetes Service (förhandsversion)

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces tillhandahåller ett arbetsflöde med GitHub Actions som gör att du kan testa ändringar från en pull-begäran direkt i AKS innan pull-begäran slås samman med lagringsplatsens huvudgren. Att ha ett program som körs för att granska ändringar i en pull-begäran kan öka både utvecklarens och teamets förtroende. Det här programmet som körs kan också hjälpa teammedlemmar som produktansvariga och designers att bli en del av granskningsprocessen under tidiga utvecklingsstadier.

I den här guiden får du lära dig hur du:

* Konfigurera Azure Dev Spaces på ett hanterat Kubernetes-kluster i Azure.
* Distribuera ett stort program med flera mikrotjänster till en utvecklarplats.
* Konfigurera CI/CD med GitHub Actions.
* Testa en enskild mikrotjänst i en isolerad utvecklarrymd i kontexten för det fullständiga programmet.

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto.](https://azure.microsoft.com/free)
* [Azure CLI installerat][azure-cli-installed].
* [Helm 3 installerat.][helm-installed]
* Ett GitHub-konto med [GitHub Actions aktiverat][github-actions-beta-signup].
* Azure [Dev Spaces-exempelprogrammet för cykeldelning som](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) körs i ett AKS-kluster.

## <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

Skapa en Azure Container Registry (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Namnet på din ACR måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Alla bokstäver som du använder måste skrivas med gemener.

Spara *loginServer-värdet* från utdata eftersom det används i ett senare steg.

## <a name="create-a-service-principal-for-authentication"></a>Skapa ett huvudnamn för tjänsten för autentisering

Använd [az ad sp create-for-rbac för][az-ad-sp-create-for-rbac] att skapa ett huvudnamn för tjänsten. Exempel:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Spara JSON-utdata eftersom de används i ett senare steg.

Använd [az aks show för][az-aks-show] att visa *ID:t* för ditt AKS-kluster:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Använd [az acr show][az-acr-show] för att visa *ID* för ACR:

```azurecli
az acr show --name <acrName> --query id
```

Använd [az role assignment create för][az-role-assignment-create] att ge *deltagare* åtkomst till ditt AKS-kluster och *AcrPush-åtkomst* till din ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Du måste vara ägare till både ditt AKS-kluster och ACR för att ge tjänstens huvudnamn åtkomst till dessa resurser.

## <a name="configure-your-github-action"></a>Konfigurera din GitHub-åtgärd

> [!IMPORTANT]
> Du måste ha GitHub Actions aktiverat för lagringsplatsen. Om du GitHub Actions för din lagringsplats navigerar du till din lagringsplats på GitHub, klickar på fliken Åtgärder och väljer att aktivera åtgärder för den här lagringsplatsen.

Gå till den fördelade lagringsplatsen och klicka på *Inställningar.* Klicka på *Hemligheter* i det vänstra sidofältet. Klicka *på Lägg till en ny hemlighet* för att lägga till varje ny hemlighet nedan:

1. *AZURE_CREDENTIALS:* hela utdata från skapandet av tjänstens huvudnamn.
1. *RESOURCE_GROUP:* resursgruppen för ditt AKS-kluster, som i det här exemplet är *MyResourceGroup*.
1. *CLUSTER_NAME*: namnet på ditt AKS-kluster, som i det här exemplet är *MyAKS*.
1. *CONTAINER_REGISTRY:* *loginServer* för ACR.
1. *VÄRD:* värden för dev-utrymmet, som har formen *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, som i det här exemplet *är dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET:* namnet på den hemlighet som du vill använda, till exempel *demohemlighet*.
1. *MASTER_SPACE*: namnet på din överordnade Dev Space, som i det här exemplet är *dev*.
1. *REGISTRY_USERNAME:* *clientId från* JSON-utdata från skapandet av tjänstens huvudnamn.
1. *REGISTRY_PASSWORD:* *clientSecret från* JSON-utdata från skapandet av tjänstens huvudnamn.

> [!NOTE]
> Alla dessa hemligheter används av GitHub-åtgärden och konfigureras i [.github/workflows/bikes.yml][github-action-yaml].

Om du vill uppdatera huvudutrymmet när din pr har sammanfogats kan du lägga till GATEWAY_HOST-hemligheten, som har formen *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, som i det här exemplet *är dev.gateway.fedcab0987.eus.azds.io*.  När du sammanslår ändringarna till main-grenen i din förgrening körs en annan åtgärd för att återskapa och köra hela programmet i huvudutvecklingsutrymmet. I det här exemplet är huvudutrymmet *dev*. Den här åtgärden konfigureras i [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Om du vill att ändringarna i din pr ska köras i  ett grandchild-utrymme uppdaterar du dessutom MASTER_SPACE *host-hemligheterna.* Om ditt program till exempel körs i *dev* med ett underområde *dev/azureuser1*, så att PR körs i ett underområde för *dev/azureuser1:*

* Uppdatera *MASTER_SPACE* till det underordnade utrymme som du vill använda som överordnat utrymme, i det här *exemplet azureuser1*.
* Uppdatera *HOST* till *<GRANDPARENT_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, i det här *dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Skapa en ny gren för kodändringar

Gå till `BikeSharingApp/` och skapa en ny gren som heter *bike-images.*

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Redigera [Bikes/server.jsatt ][bikes-server-js] ta bort raderna 232 och 233:

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

Spara filen och använd sedan `git add` och `git commit` för att mellanse ändringarna.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Push-skicka ändringarna

Använd `git push` för att skicka din nya gren till din förgrenade lagringsplats:

```cmd
git push origin bike-images
```

När pushen är klar går du till din förgrenade lagringsplats  på GitHub för att skapa en *pull-begäran* med huvudgrenen i din förgrenade lagringsplats som basgrenen jämfört med cykelavbildningsgrenen.

När pull-begäran har öppnats går du till *fliken* Åtgärder. Kontrollera att en ny åtgärd har startats och bygger *tjänsten Bikes.*

## <a name="view-the-child-space-with-your-changes"></a>Visa det underordnade utrymmet med dina ändringar

När åtgärden har slutförts visas en kommentar med en URL till ditt nya underordnade utrymme baserat på ändringarna i pull-begäran.

> [!div class="mx-imgBorder"]
> ![GitHub-åtgärds-URL](../media/github-actions/github-action-url.png)

Navigera till *bikesharingweb-tjänsten* genom att öppna URL:en från kommentaren. Välj *Aurriggs Briggs (kund)* som användare och välj sedan en cykel att hyra. Kontrollera att du inte längre ser platshållarbilden för cykeln.

Om du sammanfogar  ändringarna i main-grenen i din förgrening körs en annan åtgärd för att återskapa och köra hela programmet i det överordnade utvecklingsutrymmet. I det här exemplet är det överordnade utrymmet *dev*. Den här åtgärden konfigureras i [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Rensa dina Azure-resurser

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Läs mer om hur Azure Dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](../how-dev-spaces-works.md)

[azure-cli-installed]: /cli/azure/install-azure-cli
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
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
