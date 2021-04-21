---
title: 'Självstudie: Aktivera Ingress Controller-tillägget för ett nytt AKS-kluster med en ny Azure Application Gateway'
description: Använd den här självstudien om du vill lära dig hur du aktiverar ingress controller-tillägget för ditt nya AKS-kluster med en Application Gateway instans.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: aad57c75481230db16a63aec7fb04fc5987ae8f0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772844"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Självstudie: Aktivera Ingress Controller-tillägget för ett nytt AKS-kluster med en ny Application Gateway instans

Du kan använda Azure CLI för att aktivera [AGIC-tillägget (Application Gateway Ingress Controller)](ingress-controller-overview.md) för ett nytt [Azure Kubernetes Services-kluster (AKS).](https://azure.microsoft.com/services/kubernetes-service/)

I den här självstudien skapar du ett AKS-kluster med AGIC-tillägget aktiverat. När du skapar klustret skapas automatiskt en Azure Application Gateway som ska användas. Sedan distribuerar du ett exempelprogram som använder tillägget för att exponera programmet via Application Gateway. 

Tillägget ger ett mycket snabbare sätt att distribuera AGIC för ditt AKS-kluster än tidigare [via Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Det erbjuder också en helt hanterad upplevelse.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp. 
> * Skapa ett nytt AKS-kluster med AGIC-tillägget aktiverat.
> * Distribuera ett exempelprogram med AGIC för ingress i AKS-klustret.
> * Kontrollera att programmet kan nås via Application Gateway.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen canadacentral* (region): 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Distribuera ett AKS-kluster med tillägget aktiverat

Nu ska du distribuera ett nytt AKS-kluster med AGIC-tillägget aktiverat. Om du inte tillhandahåller en befintlig Application Gateway-instans som ska användas i den här processen kommer vi automatiskt att skapa och konfigurera en ny Application Gateway-instans för att hantera trafik till AKS-klustret.  

> [!NOTE]
> Tillägget Application Gateway Ingress Controller stöder endast  SKU:er Application Gateway v2 (Standard och  WAF) och inte SKU:er Application Gateway v1. När du distribuerar en ny Application Gateway-instans via AGIC-tillägget kan du bara distribuera en Application Gateway Standard_v2 SKU. Om du vill aktivera tillägget för en SKU Application Gateway WAF_v2 använder du någon av följande metoder:
>
> - Aktivera WAF Application Gateway via portalen. 
> - Skapa WAF_v2 Application Gateway instansen först och följ sedan anvisningarna för hur du aktiverar AGIC-tillägget med ett befintligt [AKS-kluster och en befintlig Application Gateway instans](tutorial-ingress-controller-add-on-existing.md). 

I följande exempel distribuerar du ett nytt AKS-kluster med namnet *myCluster* med hjälp [av Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) och [hanterade identiteter](../aks/use-managed-identity.md). AGIC-tillägget aktiveras i resursgruppen som du skapade, *myResourceGroup.* 

Om du distribuerar ett nytt AKS-kluster med AGIC-tillägget aktiverat utan att ange en befintlig Application Gateway-instans innebär det att en SKU-Standard_v2-Application Gateway-instans skapas automatiskt. Därför anger du även namn och adressutrymme för undernätet för den Application Gateway instansen. Namnet på den Application Gateway instansen blir *myApplicationGateway* och adressutrymmet för undernätet som vi använder är 10.2.0.0/16.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-cidr "10.2.0.0/16" --generate-ssh-keys
```

Mer information om hur du konfigurerar ytterligare `az aks create` parametrar för kommandot finns i dessa [referenser.](/cli/azure/aks#az_aks_create) 

> [!NOTE]
> AKS-klustret som du skapade visas i resursgruppen som du skapade, *myResourceGroup.* Den automatiskt skapade instansen Application Gateway dock i nodresursgruppen, där agentpoolerna finns. Nodresursgruppen efter heter *MC_resource-group-name_cluster-name_location* som standard, men kan ändras. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Distribuera ett exempelprogram med AGIC

Nu ska du distribuera ett exempelprogram till det AKS-kluster som du skapade. Programmet använder AGIC-tillägget för ingress och ansluter Application Gateway till AKS-klustret. 

Hämta först autentiseringsuppgifterna till AKS-klustret genom att köra `az aks get-credentials` kommandot : 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Nu när du har autentiseringsuppgifter kör du följande kommando för att konfigurera ett exempelprogram som använder AGIC för ingress till klustret. AGIC uppdaterar den Application Gateway som du konfigurerade tidigare med motsvarande routningsregler till det nya exempelprogrammet som du distribuerade.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Kontrollera att programmet kan nås

Nu när Application Gateway instansen har ställts in för att betjäna trafik till AKS-klustret ska vi kontrollera att programmet kan nås. Hämta först IP-adressen för ingressen: 

```azurecli-interactive
kubectl get ingress
```

Kontrollera att exempelprogrammet som du skapade körs av antingen:

- Gå till IP-adressen Application Gateway instansen som du fick när du körde föregående kommando.
- Med hjälp av `curl` . 

Application Gateway kan ta en minut att hämta uppdateringen. Om Application Gateway fortfarande är i **ett uppdateringstillstånd** på portalen ska du låta det slutföras innan du försöker nå IP-adressen. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dem tar du bort resursgruppen, Application Gateway instansen och alla relaterade resurser:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om att inaktivera AGIC-tillägget](./ingress-controller-disable-addon.md)
