---
title: Azure Kubernetes service (AKS) med SLA för drift tid
description: Lär dig mer om SLA-erbjudandet (tillval) för API-servern för Azure Kubernetes service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 69a4955f28bbd42cd7bf5651bd057412e15303de
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952923"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA för Azure Kubernetes service (AKS) drift tid

SLA för drift tid är en valfri funktion för att aktivera ett finansiellt, högre service avtal för ett kluster. SLA för drift tid garanterar 99,95% tillgänglighet för Kubernetes API Server-slutpunkten för kluster som använder [Tillgänglighetszoner][availability-zones] och 99,9% av tillgänglighet för kluster som inte använder Tillgänglighetszoner. AKS använder huvudnoders repliker över uppdaterings-och fel domäner för att säkerställa att SLA-kraven är uppfyllda.

Kunder som behöver ett service avtal för att uppfylla kraven på efterlevnad eller som kräver utökning av ett service avtal för sina slutanvändare bör aktivera den här funktionen. Kunder med kritiska arbets belastningar som kommer att ha nytta av ett service avtal med högre drift tid kan också dra nytta av. Med hjälp av SLA-funktionen för drift tid med Tillgänglighetszoner möjliggör en högre tillgänglighet för drift tiden för Kubernetes API-servern.  

Kunder kan ändå skapa obegränsat antal kostnads fria kluster med ett service nivå mål på 99,5% och välja önskat service nivå mål eller drift tid för service nivå avtal vid behov.

> [!Important]
> För kluster med utgående låsning, se [begränsa utgående trafik](limit-egress-traffic.md) för att öppna lämpliga portar.

## <a name="region-availability"></a>Regional tillgänglighet

* SLA för drift tid är tillgängligt i offentliga regioner och Azure Government regioner där [AKS stöds](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service).
* SLA för drift tid är tillgängligt för [privata AKS-kluster][private-clusters] i alla offentliga regioner där AKS stöds.

## <a name="sla-terms-and-conditions"></a>Villkor för SLA

SLA för drift tid är en betald funktion och aktive rad per kluster. SLA-priser för drift tid bestäms av antalet diskreta kluster och inte av storleken på de enskilda klustren. Du kan visa [SLA pris information för drift tid](https://azure.microsoft.com/pricing/details/kubernetes-service/) för mer information.

## <a name="before-you-begin"></a>Innan du börjar

* Installera [Azure CLI](/cli/azure/install-azure-cli) version 2.8.0 eller senare

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Skapa ett nytt kluster med SLA för drift tid

Om du vill skapa ett nytt kluster med SLA för drift tid använder du Azure CLI.

I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Använd [`az aks create`][az-aks-create] kommandot för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Den här åtgärden tar flera minuter att slutföra:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret. Följande JSON-kodfragment visar den betalda nivån för SKU: n, vilket anger att ditt kluster är aktiverat med SLA för drift tid:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Ändra ett befintligt kluster för att använda SLA för drift tid

Du kan också uppdatera befintliga kluster för att använda SLA för drift tid.

Om du har skapat ett AKS-kluster med föregående steg tar du bort resurs gruppen:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Skapa en ny resurs grupp:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Skapa ett nytt kluster och Använd inte SLA för drift tid:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Använd [`az aks update`][az-aks-update] kommandot för att uppdatera det befintliga klustret:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Följande JSON-kodfragment visar den betalda nivån för SKU: n, vilket anger att ditt kluster är aktiverat med SLA för drift tid:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>Service avtal för att välja ut ur drift tid

Du kan uppdatera klustret om du vill ändra till den kostnads fria nivån och välja service avtal i drift tid.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>Rensa

Ta bort alla resurser som du har skapat för att undvika avgifter. Ta bort klustret genom att använda [`az group delete`][az-group-delete] kommandot för att ta bort resurs gruppen AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Nästa steg

Använd [Tillgänglighetszoner][availability-zones] för att öka hög tillgänglighet med AKS-klustrets arbets belastningar.

Konfigurera klustret för att [begränsa utgående trafik](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az-group-delete
[private-clusters]: private-clusters.md
