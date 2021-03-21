---
title: Självstudie om Kubernetes i Azure – Distribuera ett kluster
description: I den här självstudien om Azure Kubernetes Service (AKS) ska du skapa ett AKS-kluster och använda kubectl för att ansluta till Kubernetes-huvudnoden.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c39169c0531a73bd00db7de5fe393ef8c51c8c96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509429"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Självstudie: Distribuera ett Azure Kubernetes Service-kluster (AKS)

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Med AKS kan du snabbt skapa ett produktionsklart Kubernetes-kluster. I del tre av sju i den här självstudien distribuerar vi ett Kubernetes-kluster i AKS. Lär dig att:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster som kan autentisera till ett Azure Container Registry
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

I senare självstudier distribueras Azure röstnings program till klustret, skalas och uppdateras.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har utfört de här stegen och vill följa med så kan du börja med [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

Den här självstudien kräver att du kör Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Skapa ett Kubernetes-kluster

AKS-kluster kan använda Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC). Med dessa kontroller kan du definiera åtkomst till resurser baserat på roller som är tilldelade till användare. Du kan kombinera behörigheter om en användare har tilldelats flera roller, och behörigheter kan begränsas till en enda namnrymd eller tillämpas på hela klustret. Som standard aktiverar Azure CLI automatiskt Kubernetes RBAC när du skapar ett AKS-kluster.

Skapa ett AKS-kluster med [az aks create][]. I följande exempel skapas ett kluster med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. Den här resurs gruppen skapades i [föregående självstudie][aks-tutorial-prepare-acr] i regionen *östra* . I följande exempel anges ingen region så AKS-klustret skapas också i regionen *östra* . Mer information finns i [kvoter, storleks begränsningar för virtuella datorer och regions tillgänglighet i Azure Kubernetes service (AKS)][quotas-skus-regions] för mer information om resurs begränsningar och regions tillgänglighet för AKS.

Om du vill tillåta ett AKS-kluster att samverka med andra Azure-resurser skapas en kluster identitet automatiskt, eftersom du inte angav något. Här ges den här kluster identiteten behörighet [att hämta avbildningar][container-registry-integration] från den Azure Container Registry (ACR) som du skapade i föregående självstudie. Om du vill köra kommandot måste du ha rollen **ägare** eller **administratör för Azure-konto** på Azure-prenumerationen.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

För att undvika att du behöver en **ägare** eller rollen som **administratör för Azure-konto** kan du också konfigurera ett huvud namn för tjänsten manuellt för att hämta avbildningar från ACR. Mer information finns i [ACR-autentisering med tjänstens huvud namn](../container-registry/container-registry-auth-service-principal.md) eller [autentisera från Kubernetes med en pull-hemlighet](../container-registry/container-registry-auth-kubernetes.md). Alternativt kan du använda en [hanterad identitet](use-managed-identity.md) i stället för ett tjänst huvud namn för enklare hantering.

Efter några minuter slutförs distributionen och JSON-formaterad information om AKS-distributionen returneras.

> [!NOTE]
> För att klustret ska fungera på ett tillförlitligt sätt bör du köra minst två noder.

## <a name="install-the-kubernetes-cli"></a>Installera Kubernetes CLI

När du ska ansluta till Kubernetes-klustret från din lokala dator använder du [kubectl][kubectl], Kubernetes kommandoradsklient.

Om du använder Azure Cloud Shell är `kubectl` redan installerat. Du kan även installera det lokalt med hjälp av kommandot [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Ansluta till klustret med kubectl

För att konfigurera `kubectl` till att ansluta till ditt Kubernetes-kluster använder du kommandot [az aks get-credentials][]. I följande exempel hämtas autentiseringsuppgifterna för AKS-klustret med namn *myAKSCluster* i *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Om du vill kontrol lera anslutningen till klustret kör du kommandot [kubectl get Nodes][kubectl-get] för att returnera en lista över klusternoderna:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen distribuerade du ett Kubernetes-kluster i AKS och konfigurerade `kubectl` för anslutning till klustret. Du har lärt dig att:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster som kan autentisera till ett Azure Container Registry
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

Gå vidare till nästa självstudie och lär dig hur du distribuerar ett program i klustret.

> [!div class="nextstepaction"]
> [Distribuera program i Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
