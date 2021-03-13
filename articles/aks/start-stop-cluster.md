---
title: Starta och stoppa en Azure Kubernetes-tjänst (AKS)
description: Lär dig hur du stoppar eller startar ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 87d51f9c1d084faf79c7ec1cf1255a6fb3c8245d
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201008"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Stoppa och starta ett Azure Kubernetes service-kluster (AKS)

Dina AKS-arbetsbelastningar kanske inte behöver köras kontinuerligt, till exempel ett utvecklings kluster som bara används under kontors tid. Detta leder till tider där ditt AKS-kluster (Azure Kubernetes service) kan vara inaktivt, vilket inte kör mer än system komponenterna. Du kan minska klustrets storlek genom [att skala alla `User` nodkonfigurationer till 0](scale-cluster.md#scale-user-node-pools-to-0), men [ `System` poolen](use-system-pools.md) krävs fortfarande för att köra system komponenterna medan klustret körs. Om du vill optimera kostnaderna ytterligare under dessa perioder kan du stänga av (stoppa) klustret. Med den här åtgärden stoppas dina kontroll Plans-och agent-noder helt, så att du kan spara på alla beräknings kostnader samtidigt som du behåller alla dina objekt och kluster tillstånd som lagras när du startar det igen. Sedan kan du hämta den högra delen av efter en helg eller låta klustret köras när du kör dina batch-jobb.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Begränsningar

När du använder funktionen för att starta/stoppa kluster gäller följande begränsningar:

- Den här funktionen stöds bara för Virtual Machine Scale Sets backade kluster.
- Kluster statusen för ett stoppat AKS-kluster bevaras i upp till 12 månader. Om klustret har stoppats i mer än 12 månader går det inte att återställa kluster statusen. Mer information finns i [AKS support policies](support-policies.md).
- Du kan bara starta eller ta bort ett stoppat AKS-kluster. Starta klustret först om du vill utföra en åtgärd som Scale eller Upgrade.

## <a name="stop-an-aks-cluster"></a>Stoppa ett AKS-kluster

Du kan använda `az aks stop` kommandot för att stoppa ett pågående AKS-klusters noder och kontroll plan. I följande exempel stoppas ett kluster med namnet *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Du kan kontrol lera när klustret har stoppats genom att använda kommandot [AZ AKS show][az-aks-show] och bekräfta att `powerState` programmen visas `Stopped` på följande utdata:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Om `provisioningState` visar `Stopping` att ditt kluster inte har stoppats helt ännu.

> [!IMPORTANT]
> Om du använder [Pod störnings budgetar](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) kan stopp åtgärden ta längre tid eftersom tömnings processen tar längre tid att slutföra.

## <a name="start-an-aks-cluster"></a>Starta ett AKS-kluster

Du kan använda `az aks start` kommandot för att starta ett stoppat AKS-klusters noder och kontroll plan. Klustret startas om med föregående kontroll Plans tillstånd och antalet agent-noder.  
I följande exempel startas ett kluster med namnet *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Du kan kontrol lera att klustret har startats med kommandot [AZ AKS show][az-aks-show] och bekräfta att de `powerState` visas `Running` på följande utdata:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Om `provisioningState` visar `Starting` att ditt kluster inte har startats helt ännu.

## <a name="next-steps"></a>Nästa steg

- Information om hur du skalar `User` pooler till 0 finns i [skala `User` pooler till 0](scale-cluster.md#scale-user-node-pools-to-0).
- Information om hur du sparar kostnader med hjälp av plats instanser finns i [lägga till en AKS för en plats](spot-node-pool.md).
- Mer information om AKS-stödprinciper finns i [AKS support policies](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-show]: /cli/azure/aks#az_aks_show
