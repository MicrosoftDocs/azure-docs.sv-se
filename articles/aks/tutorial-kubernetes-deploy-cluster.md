---
title: Självstudie om Kubernetes i Azure – Distribuera ett kluster
description: I den här självstudien om Azure Kubernetes Service (AKS) ska du skapa ett AKS-kluster och använda kubectl för att ansluta till Kubernetes-huvudnoden.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d7e931a55ec0a9d46a8b92d4353bd2de8edd8818
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777841"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Självstudie: Distribuera ett Azure Kubernetes Service-kluster (AKS)

Kubernetes tillhandahåller en distribuerad plattform för containerbaserade program. Med AKS kan du snabbt skapa ett produktionsklart Kubernetes-kluster. I del tre av sju i den här självstudien distribuerar vi ett Kubernetes-kluster i AKS. Lär dig att:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster som kan autentisera till ett Azure-containerregister
> * Installera Kubernetes CLI (kubectl)
> * Konfigurera kubectl för anslutning till ditt AKS-kluster

I senare självstudier distribueras Azure Vote-programmet till klustret, skalas och uppdateras.

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier skapades en behållaravbildning som sedan överfördes till en Azure Container Registry-instans. Om du inte har utfört de här stegen och vill följa med så kan du börja med [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

Den här självstudien kräver att du kör Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Skapa ett Kubernetes-kluster

AKS-kluster kan använda kubernetes rollbaserad åtkomstkontroll (Kubernetes RBAC). Med dessa kontroller kan du definiera åtkomst till resurser baserat på roller som är tilldelade till användare. Du kan kombinera behörigheter om en användare har tilldelats flera roller, och behörigheter kan begränsas till en enda namnrymd eller tillämpas på hela klustret. Som standard aktiverar Azure CLI automatiskt Kubernetes RBAC när du skapar ett AKS-kluster.

Skapa ett AKS-kluster med [az aks create][]. I följande exempel skapas ett kluster med namnet *myAKSCluster* i resursgruppen med namnet *myResourceGroup*. Den här resursgruppen skapades i föregående [självstudie][aks-tutorial-prepare-acr] i *regionen eastus.* I följande exempel anges ingen region, så AKS-klustret skapas också i *regionen eastus.* Mer information finns i [Kvoter, storleksbegränsningar][quotas-skus-regions] för virtuella datorer och regionstillgänglighet i Azure Kubernetes Service (AKS) för mer information om resursgränser och regionstillgänglighet för AKS.

Om du vill tillåta att ett AKS-kluster interagerar med andra Azure-resurser skapas automatiskt en klusteridentitet eftersom du inte angav någon. Här beviljas den här [klusteridentiteten][container-registry-integration] behörighet att hämta avbildningar från den Azure Container Registry -instans (ACR) som du skapade i föregående självstudie. Om du vill köra kommandot måste du  ha rollen Ägare eller **Azure-kontoadministratör i** Azure-prenumerationen.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

För att undvika att behöva ha **rollen Ägare** eller **Azure-kontoadministratör** kan du också manuellt konfigurera ett huvudnamn för tjänsten för att hämta avbildningar från ACR. Mer information finns i [ACR-autentisering med tjänstens huvudnamn](../container-registry/container-registry-auth-service-principal.md) eller [Autentisera från Kubernetes med en pull-hemlighet.](../container-registry/container-registry-auth-kubernetes.md) Du kan också använda en hanterad [identitet i stället](use-managed-identity.md) för tjänstens huvudnamn för enklare hantering.

Efter några minuter slutförs distributionen och JSON-formaterad information om AKS-distributionen returneras.

> [!NOTE]
> För att säkerställa att klustret fungerar på ett tillförlitligt sätt bör du köra minst 2 (två) noder.

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

Kontrollera anslutningen till klustret genom att köra kommandot [kubectl get nodes för][kubectl-get] att returnera en lista över klusternoderna:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen distribuerade du ett Kubernetes-kluster i AKS och konfigurerade `kubectl` för anslutning till klustret. Du har lärt dig att:

> [!div class="checklist"]
> * Distribuera ett Kubernetes AKS-kluster som kan autentisera till ett Azure-containerregister
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
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az acr show]: /cli/azure/acr#az_acr_show
[az role assignment create]: /cli/azure/role/assignment#az_role_assignment_create
[az aks create]: /cli/azure/aks#az_aks_create
[az aks install-cli]: /cli/azure/aks#az_aks_install_cli
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
