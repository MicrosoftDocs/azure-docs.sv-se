---
title: Azure Kubernetes Service (AKS) med serviceavtal för drifttid
description: Lär dig mer om det valfria serviceavtalet för drifttid Azure Kubernetes Service API-servern (AKS).
services: container-service
ms.topic: conceptual
ms.date: 01/08/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 846446b4c19c066afe789bf636d68ad37b20709e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779569"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>SLA för Azure Kubernetes Service (AKS) drifttid

Serviceavtal för drifttid är en valfri funktion för att möjliggöra ett ekonomiskt säkerhetskopierat, högre serviceavtal för ett kluster. Serviceavtalet för drifttid garanterar 99,95 % tillgänglighet för Kubernetes API-serverslutpunkten för kluster som använder [Tillgänglighetszoner][availability-zones] och 99,9 % tillgänglighet för kluster som inte använder Tillgänglighetszoner. AKS använder huvudnodrepliker över uppdaterings- och feldomäner för att säkerställa att SLA-kraven uppfylls.

Kunder som behöver ett serviceavtal för att uppfylla efterlevnadskrav eller som behöver utöka ett serviceavtal till sina slutanvändare bör aktivera den här funktionen. Kunder med kritiska arbetsbelastningar som kan dra nytta av ett serviceavtal med högre drifttid kan också dra nytta av detta. Genom att använda SLA-funktionen för drifttid med Tillgänglighetszoner ger en högre tillgänglighet för drifttiden för Kubernetes API-servern.  

Kunder kan fortfarande skapa obegränsade kostnadsfria kluster med ett servicenivåmål på 99,5 % och välja önskat SLO eller SLA-drifttid efter behov.

> [!Important]
> Information om kluster med utgående låsning finns i [Begränsa utgående trafik för](limit-egress-traffic.md) att öppna lämpliga portar.

## <a name="region-availability"></a>Regional tillgänglighet

* Serviceavtalet för drifttid är tillgängligt i offentliga regioner Azure Government regioner där [AKS stöds.](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)
* Serviceavtal för drifttid är tillgängligt [för privata AKS-kluster][private-clusters] i alla offentliga regioner där AKS stöds.

## <a name="sla-terms-and-conditions"></a>Allmänna villkor för serviceavtal

Serviceavtalet för drifttid är en betald funktion som aktiveras per kluster. Prissättningen för serviceavtal för drifttid bestäms av antalet diskreta kluster och inte av storleken på de enskilda klustren. Du kan visa [prisinformation för serviceavtal för drifttid](https://azure.microsoft.com/pricing/details/kubernetes-service/) för mer information.

## <a name="before-you-begin"></a>Innan du börjar

* Installera [Azure CLI](/cli/azure/install-azure-cli) version 2.8.0 eller senare

## <a name="creating-a-new-cluster-with-uptime-sla"></a>Skapa ett nytt kluster med serviceavtal för drifttid

Om du vill skapa ett nytt kluster med serviceavtalet för drifttid använder du Azure CLI.

I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen eastus:*

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```
Använd kommandot [`az aks create`][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster* och en enda nod. Den här åtgärden tar flera minuter att slutföra:

```azurecli-interactive
# Create an AKS cluster with uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1
```
Efter några minuter slutförs kommandot och returnerar JSON-formaterad information om klustret. Följande JSON-kodfragment visar den betalda nivån för SKU:n, vilket anger att klustret har aktiverats med serviceavtal för drifttid:

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
```

## <a name="modify-an-existing-cluster-to-use-uptime-sla"></a>Ändra ett befintligt kluster för att använda serviceavtal för drifttid

Om du vill kan du uppdatera dina befintliga kluster för att använda serviceavtalet för drifttid.

Om du har skapat ett AKS-kluster med föregående steg tar du bort resursgruppen:

```azurecli-interactive
# Delete the existing cluster by deleting the resource group 
az group delete --name myResourceGroup --yes --no-wait
```

Skapa en ny resursgrupp:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus
```

Skapa ett nytt kluster och använd inte serviceavtal för drifttid:

```azurecli-interactive
# Create a new cluster without uptime SLA
az aks create --resource-group myResourceGroup --name myAKSCluster--node-count 1
```

Använd kommandot [`az aks update`][az-aks-update] för att uppdatera det befintliga klustret:

```azurecli-interactive
# Update an existing cluster to use Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --uptime-sla
 ```

 Följande JSON-kodfragment visar den betalda nivån för SKU:n, vilket anger att klustret har aktiverats med serviceavtal för drifttid:

 ```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  ```

## <a name="opt-out-of-uptime-sla"></a>Avanmäla sig från serviceavtal för drifttid

Du kan uppdatera klustret för att ändra till den kostnadsfria nivån och välja bort serviceavtalet för drifttid.

```azurecli-interactive
# Update an existing cluster to opt out of Uptime SLA
 az aks update --resource-group myResourceGroup --name myAKSCluster --no-uptime-sla
 ```

## <a name="clean-up"></a>Rensa

Rensa alla resurser som du har skapat för att undvika kostnader. Om du vill ta bort klustret använder du [`az group delete`][az-group-delete] kommandot för att ta bort AKS-resursgruppen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Nästa steg

Använd [Tillgänglighetszoner för][availability-zones] att öka hög tillgänglighet med dina AKS-klusterarbetsbelastningar.

Konfigurera klustret för att [begränsa utgående trafik](limit-egress-traffic.md).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?#az_aks_create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-group-delete]: /cli/azure/group#az_group_delete
[private-clusters]: private-clusters.md
