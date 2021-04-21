---
title: Självstudie om Kubernetes i Azure – Uppgradera ett kluster
description: I den här självstudien om Azure Kubernetes Service (AKS) lär du dig hur du uppgraderar ett befintligt AKS-kluster till den senaste tillgängliga Kubernetes-versionen.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: 68aedbe90d5f08a4b6b67d134c0460caa11c542b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786377"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Självstudie: Uppgradera Kubernetes i Azure Kubernetes Service (AKS)

Som en del av programmets och klustrets livscykel kanske du vill uppgradera till den senaste versionen av Kubernetes och använda nya funktioner. Ett kluster med Azure Kubernetes Service (AKS) kan uppgraderas med hjälp av Azure CLI.

I del sju av sju i den här självstudien uppgraderas ett Kubernetes-kluster. Lär dig att:

> [!div class="checklist"]
> * Identifiera aktuella och tillgängliga Kubernetes-versioner
> * Uppgradera Kubernetes-noderna
> * Verifiera uppgraderingen

## <a name="before-you-begin"></a>Innan du börjar

I tidigare självstudier paketerades en app i en containeravbildning. Den här avbildningen laddades upp till Azure Container Registry, och du skapade ett AKS-kluster. Programmet distribuerades sedan till AKS-klustret. Om du inte har utfört de här stegen och vill följa med börjar du med [Självstudie 1 – Skapa containeravbildningar][aks-tutorial-prepare-app].

I den här självstudien måste du köra Azure CLI version 2.0.53 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Hämta tillgängliga klusterversioner

Innan du uppgraderar ett kluster använder du kommandot [az aks get-upgrades][] för att kontrollera vilka Kubernetes-versioner som är tillgängliga för uppgradering:

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

I följande exempel är den aktuella versionen *1.18.10* och de tillgängliga versionerna visas under *uppgraderingar*.

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.18.10",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.1"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.19.3"
      }
    ]
  },
  ...
}
```

## <a name="upgrade-a-cluster"></a>Uppgradera ett kluster

I syfte att minimera störningar av program som körs avspärras och töms noderna noggrant. I den här processen utförs följande steg:

1. Kubernetes-schemaläggaren förhindrar att ytterligare poddar schemaläggs på en nod som ska uppgraderas.
1. Poddar som körs på noden schemaläggs på andra noder i klustret.
1. Det skapas en nod som kör de senaste Kubernetes-komponenterna.
1. När den nya noden är redo och ansluten till klustret börjar Kubernetes-schemaläggaren att köra poddar på den.
1. Den gamla noden tas bort och nästa nod i klustret börjar avspärrnings- och tömningsprocessen.

Använd kommandot [az aks upgrade][] för att uppgradera AKS-klustret.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> Du kan endast uppgradera en lägre version i taget. Du kan till exempel uppgradera från *1.14.x* till *1.15.x,* men det går inte att uppgradera direkt från *1.14.x* till *1.16.x.* Om du vill uppgradera *från 1.14.x* till *1.16.x* uppgraderar du först från *1.14.x* till *1.15.x* och utför sedan en till uppgradering från *1.15.x* till *1.16.x*.

Följande komprimerade exempelutdata visar resultatet av uppgraderingen till *1.19.1*. Observera att *kubernetesVersion* nu rapporterar *1.19.1*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.19.1",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Verifiera en uppgradering

Bekräfta att uppgraderingen lyckades genom att köra kommandot [az aks show][] på följande sätt:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Följande exempelutdata visar att AKS-klustret kör *KubernetesVersion 1.19.1*:

```output
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.19.1               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>Ta bort klustret

Eftersom den här självstudien är den sista delen i serien kan du ta bort AKS-klustret. Då Kubernetes-noderna körs på virtuella Azure-datorer fortsätter de att debiteras även om du inte använder klustret. Använd kommandot [az group delete][az-group-delete] för att ta bort resursgruppen, containertjänsten och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> När du tar bort klustret tas Azure Active Directory-tjänstens huvudnamn, som används av AKS-klustret, inte bort. Stegvisa instruktioner om hur du tar bort tjänstens huvudnamn finns i dokumentationen om [viktiga överväganden och borttagning av AKS-tjänsten][sp-delete]. Om du använde en hanterad identitet hanteras identiteten av plattformen och kräver inte att du etablerar eller roterar några hemligheter.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du uppgraderat Kubernetes i ett AKS-kluster. Du har lärt dig att:

> [!div class="checklist"]
> * Identifiera aktuella och tillgängliga Kubernetes-versioner
> * Uppgradera Kubernetes-noderna
> * Verifiera uppgraderingen

Mer information om AKS finns i [AKS-översikt.][aks-intro] Vägledning om hur du skapar fullständiga lösningar med AKS finns i [Vägledning för AKS-lösningar.][aks-solution-guidance]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az_aks_show
[az aks get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az aks upgrade]: /cli/azure/aks#az_aks_upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here?WT.mc_id=AKSDOCSPAGE
