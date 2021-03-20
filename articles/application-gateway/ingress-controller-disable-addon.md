---
title: Inaktivera och återaktivera Application Gateway ingress-tillägg för Azure Kubernetes service-kluster
description: Den här artikeln innehåller information om hur du inaktiverar och återaktiverar AGIC-tillägget för ditt AKS-kluster
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807943"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>Inaktivera och återaktivera AGIC-tillägg för ditt AKS-kluster
Application Gateway ingress (AGIC) som distribuerats som ett AKS-tillägg kan du aktivera och inaktivera tillägget med en rad i Azure CLI. Livs cykeln för Application Gateway varierar när du inaktiverar AGIC-tillägget, beroende på om Application Gateway har skapats av AGIC-tillägget eller om det distribuerades separat från AGIC-tillägget. Du kan köra samma kommando för att återaktivera AGIC-tillägget om du råkar inaktivera det, eller för att aktivera AGIC-tillägget med ett befintligt AKS-kluster och Application Gateway.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>Inaktiverar AGIC-tillägg med tillhör ande Application Gateway 
Om AGIC-tillägget automatiskt har distribuerat Application Gateway åt dig när du först ställer in allt, så tar du bort den Application Gateway baserat på ett par villkor om du inaktiverar AGIC-tillägget. Det finns två villkor som AGIC-tillägget letar efter för att avgöra om det ska ta bort associerade Application Gateway när du inaktiverar det:
- Är Application Gateway att AGIC-tillägget är associerat med distribuerat i resurs gruppen MC_ *-nod? 
- Är Application Gateway att AGIC-tillägget är associerat med har taggen "skapad av: ingress-appgw"? Taggen används av AGIC för att avgöra om Application Gateway har distribuerats av tillägget eller inte. 

Om båda villkoren är uppfyllda, tar AGIC-tillägget bort Application Gateway som skapades när tillägget har inaktiverats. Det tar dock inte bort den offentliga IP-adressen eller under nätet där Application Gateway distribuerades med/i. Om det första villkoret inte är uppfyllt spelar det ingen roll om Application Gateway har taggen "skapad av: ingress-appgw" – om du inaktiverar tillägget tar du inte bort Application Gateway. Om inte det andra villkoret är uppfyllt, d.v.s. Application Gateway saknar den taggen och sedan inaktivera tillägget tar inte bort Application Gateway i resurs gruppen för MC_ *-noden. 

> [!TIP] 
> Om du inte vill att Application Gateway ska tas bort när du inaktiverar tillägget, men det uppfyller båda villkoren, tar du bort taggen "skapad av: ingress-appgw" för att förhindra att tillägget tar bort Application Gateway. 

Om du vill inaktivera AGIC-tillägget kör du följande kommando: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>Aktivera AGIC-tillägg på befintliga Application Gateway-och AKS-kluster
Om du senare inaktiverar AGIC-tillägget och behöver aktivera tillägget igen, eller om du vill aktivera tillägget med ett befintligt Application Gateway-och AKS-kluster, kör du följande kommando:

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du aktiverar AGIC-tillägget med hjälp av ett befintligt Application Gateway-och AKS-kluster finns i [AGIC-tillägg brownfield-distribution](tutorial-ingress-controller-add-on-existing.md).