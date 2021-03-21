---
title: Så här migrerar du från Azure Application Gateway ingress-styrenhet Helm till AGIC-tillägg
description: Den här artikeln innehåller anvisningar om hur du migrerar från AGIC som distribueras via Helm till AGIC distribueras som ett AKS-tillägg
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050842"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migrera från AGIC Helm till AGIC-tillägg 

Om du redan har AGIC distribuerat via Helm men vill migrera till AGIC som har distribuerats som ett AKS-tillägg, kommer följande steg att vägleda dig genom migreringsprocessen. 

## <a name="prerequisites"></a>Förutsättningar 
Innan du påbörjar migreringsprocessen finns det några saker att kontrol lera. 
  - Använder du några funktioner med AGIC Helm som [för närvarande inte stöds med AGIC-tillägget](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)?
  - Använder du fler än en AGIC Helm-distribution per AKS-kluster? 
  - Använder du flera AGIC Helm-distributioner för att nå en Application Gateway? 

Om du svarade ja på någon av frågorna ovan stöder AGIC-tillägget inte ditt användnings fall än så det är bäst att fortsätta använda AGIC Helm under tiden. Annars fortsätter du med migreringsprocessen nedan under kontors tid. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Hitta Application Gateway resurs-ID som AGIC Helm för närvarande riktar sig till 
Navigera till Application Gateway att distributionen av AGIC Helm är riktad. Kopiera och spara resurs-ID: t för Application Gateway. Du kommer att behöva resurs-ID i ett senare steg. Resurs-ID: t finns i portalen på fliken Egenskaper i Application Gateway eller via Azure CLI. I följande exempel sparas Application Gateway resurs-ID till *appgwId* för en gateway med namnet *myApplicationGateway* i resurs gruppen *myResourceGroup*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>Ta bort AGIC Helm från ditt AKS-kluster
Ta bort din AGIC Helm-distribution från klustret via Azure CLI. Du måste först ta bort AGIC Helm-distributionen innan du kan aktivera AGIC AKS-tillägget. Observera att alla ändringar som sker i AKS-klustret mellan tidpunkten för att ta bort din AGIC Helm-distribution och den tid som du aktiverar AGIC-tillägget inte visas på din Application Gateway, och därför bör migreringen göras utanför kontors tid för att minimera påverkan. Application Gateway fortsätter att ha den senaste konfigurationen som tillämpats av AGIC så att befintliga regler för routning inte påverkas. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>Aktivera AGIC-tillägg med din befintliga Application Gateway 
Nu kan du aktivera AGIC-tillägget i ditt AKS-kluster för att rikta in ditt befintliga Application Gateway via Azure CLI eller Portal. Kör följande Azure CLI-kommando för att aktivera AGIC-tillägget i ditt AKS-kluster. Exemplet aktiverar tillägget i ett kluster som kallas för ett *kluster*, i en resurs grupp med namnet *myResourceGroup*, med hjälp av Application Gateway resurs-ID *appgwId* som vi sparade ovan i föregående steg. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Du kan också navigera till ditt AKS-kluster i portalen med hjälp av den här [länken](https://portal.azure.com/?feature.aksagic=true) och aktivera AGIC-tillägget på fliken nätverk i klustret. Välj din befintliga Application Gateway i list rutan när du väljer vilken Application Gateway tillägget ska vara målet för. 

![Application Gateway ingress-kontrollanten](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Nästa steg
- [**Fel sökning av Application Gateway inkommande styrenhet**](ingress-controller-troubleshoot.md): fel söknings guide för AGIC 
- Application Gateway ingångs [**styrenhets anteckningar**](ingress-controller-annotations.md): lista över anteckningar på AGIC 