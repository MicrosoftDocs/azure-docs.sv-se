---
title: Starta och stoppa en Azure Kubernetes Service (AKS)
description: Lär dig hur du stoppar eller startar ett Azure Kubernetes Service(AKS)-kluster.
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 2d3c946bc2f98b0c06fe33dcaaa77a5399f6d56b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782737"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster"></a>Stoppa och starta ett Azure Kubernetes Service (AKS)-kluster

Dina AKS-arbetsbelastningar kanske inte behöver köras kontinuerligt, till exempel ett utvecklingskluster som endast används under arbetstid. Detta leder till tillfällen då ditt Azure Kubernetes Service (AKS)-kluster kan vara inaktivt och inte körs mer än systemkomponenterna. Du kan minska klusterfotavtrycket genom att skala alla [ `System` ](use-system-pools.md) [ `User` nodpooler](scale-cluster.md#scale-user-node-pools-to-0)till 0 , men poolen krävs fortfarande för att köra systemkomponenterna medan klustret körs. Om du vill optimera dina kostnader ytterligare under dessa perioder kan du helt stänga av (stoppa) klustret. Den här åtgärden stoppar kontrollplanet och agentnoderna helt och hållet, så att du kan spara på alla beräkningskostnader, samtidigt som alla objekt och klustertillstånd lagras när du startar det igen. Du kan sedan fortsätta direkt där du slutade efter en helg eller bara köra klustret när du kör batchjobben.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du gå till AKS-snabbstarten med Hjälp av [Azure CLI][aks-quickstart-cli] eller [använda Azure Portal][aks-quickstart-portal].

### <a name="limitations"></a>Begränsningar

När du använder funktionen för att starta/stoppa kluster gäller följande begränsningar:

- Den här funktionen stöds endast för Virtual Machine Scale Sets kluster som stöds.
- Klustertillståndet för ett stoppat AKS-kluster bevaras i upp till 12 månader. Om klustret har stoppats i mer än 12 månader kan klustertillståndet inte återställas. Mer information finns i [Supportprinciper för AKS.](support-policies.md)
- Du kan bara starta eller ta bort ett stoppat AKS-kluster. Starta klustret först om du vill utföra åtgärder som skala eller uppgradera.

## <a name="stop-an-aks-cluster"></a>Stoppa ett AKS-kluster

Du kan använda kommandot `az aks stop` för att stoppa AKS-klustrets noder och kontrollplan som körs. I följande exempel stoppas ett kluster med *namnet myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Du kan kontrollera när klustret har stoppats med kommandot [az aks show][az-aks-show] och bekräfta `powerState` att visas som i `Stopped` utdata nedan:

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

Om visar `provisioningState` innebär det att klustret inte har `Stopping` stoppats helt ännu.

> [!IMPORTANT]
> Om du använder [poddavbrottsbudgetar](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) kan stoppåtgärden ta längre tid eftersom tömningsprocessen tar längre tid att slutföra.

## <a name="start-an-aks-cluster"></a>Starta ett AKS-kluster

Du kan använda kommandot `az aks start` för att starta ett stoppat AKS-klusters noder och kontrollplan. Klustret startas om med det tidigare kontrollplanets tillstånd och antalet agentnoder.  
I följande exempel startas ett kluster med namnet *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Du kan kontrollera när klustret har startats med kommandot [az aks show][az-aks-show] och bekräfta `powerState` att visas som i `Running` utdata nedan:

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

Om visar `provisioningState` innebär det att klustret inte har `Starting` startats helt ännu.

## <a name="next-steps"></a>Nästa steg

- Information om hur du skalar `User` pooler till 0 finns i [Skala `User` pooler till 0.](scale-cluster.md#scale-user-node-pools-to-0)
- Information om hur du sparar kostnader med instanser för punktinstanser finns i [Lägga till en skalning av skalbar skalning i AKS.](spot-node-pool.md)
- Mer information om AKS-supportprinciper finns i [AKS-supportprinciper.](support-policies.md)

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-show]: /cli/azure/aks#az_aks_show
