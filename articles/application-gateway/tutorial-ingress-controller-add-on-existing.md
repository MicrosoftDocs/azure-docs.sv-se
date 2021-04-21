---
title: 'Självstudie: Aktivera Add-On för befintligt AKS-kluster med befintliga Azure Application Gateway'
description: Använd den här självstudien för att aktivera Add-On för ditt befintliga AKS-kluster med en befintlig Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2fe615da256099c3135f607a7b6f8095bb93b442
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772855"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Självstudie: Application Gateway för ett befintligt AKS-kluster med en befintlig Application Gateway

Du kan använda Azure CLI eller portalen för att aktivera [AGIC-tillägget (Application Gateway Ingress Controller)](ingress-controller-overview.md) för ett befintligt [Azure Kubernetes Services-kluster (AKS).](https://azure.microsoft.com/services/kubernetes-service/) I den här självstudien lär du dig hur du använder AGIC-tillägg för att exponera kubernetes-programmet i ett befintligt AKS-kluster via en befintlig Application Gateway distribuerad i separata virtuella nätverk. Du börjar med att skapa ett AKS-kluster i ett virtuellt nätverk och en Application Gateway i ett separat virtuellt nätverk för att simulera befintliga resurser. Sedan aktiverar du AGIC-tillägget, peer-peerar de två virtuella nätverken och distribuerar ett exempelprogram som exponeras via Application Gateway med AGIC-tillägget. Om du aktiverar AGIC-tillägget för en befintlig Application Gateway och ett befintligt AKS-kluster i samma virtuella nätverk kan du hoppa över peering-steget nedan. Tillägget ger ett mycket snabbare sätt att distribuera AGIC för ditt AKS-kluster än tidigare via [Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) och erbjuder även en fullständigt hanterad upplevelse.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp 
> * Skapa ett nytt AKS-kluster 
> * Skapa en ny Application Gateway 
> * Aktivera AGIC-tillägget i det befintliga AKS-klustret via Azure CLI 
> * Aktivera AGIC-tillägget i det befintliga AKS-klustret via portalen 
> * Peer-Application Gateway virtuella nätverket med det virtuella AKS-klustret
> * Distribuera ett exempelprogram med AGIC för ingress i AKS-klustret
> * Kontrollera att programmet kan nås via Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med *namnet myResourceGroup* på *platsen canadacentral* (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Distribuera ett nytt AKS-kluster

Nu ska du distribuera ett nytt AKS-kluster för att simulera att ha ett befintligt AKS-kluster som du vill aktivera AGIC-tillägget för.  

I följande exempel distribuerar du ett nytt AKS-kluster med namnet *myCluster* med [Azure CNI](../aks/concepts-network.md#azure-cni-advanced-networking) och [hanterade](../aks/use-managed-identity.md) identiteter i resursgruppen som du *skapade, myResourceGroup*.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Om du vill konfigurera ytterligare parametrar `az aks create` för kommandot går du till [referenserna här](/cli/azure/aks#az_aks_create). 

## <a name="deploy-a-new-application-gateway"></a>Distribuera en ny Application Gateway 

Nu ska du distribuera en ny Application Gateway för att simulera att ha en befintlig Application Gateway som du vill använda för att belastningsutjämna trafik till ditt *AKS-kluster, myCluster*. Namnet på Application Gateway är *myApplicationGateway,* men du måste först skapa en offentlig IP-resurs med namnet *myPublicIp* och ett nytt virtuellt nätverk med namnet *myVnet* med adressutrymmet 11.0.0.0/8 och ett undernät med adressutrymmet 11.1.0.0/16 med namnet *mySubnet* och distribuera din Application Gateway i *mySubnet med mySubnet* med *myPublicIp*. 

När du använder ett AKS-Application Gateway i separata virtuella nätverk får adressutrymmena för de två virtuella nätverken inte överlappa varandra. Standardadressutrymmet som ett AKS-kluster distribuerar i är 10.0.0.0/8, så vi anger adressprefixet för det virtuella Application Gateway-nätverket till 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway AGIC-tillägg (Ingress Controller)  stöder endast SKU:er Application Gateway v2 (Standard  och WAF) och inte SKU:er Application Gateway v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Aktivera AGIC-tillägget i befintligt AKS-kluster via Azure CLI 

Om du vill fortsätta använda Azure CLI kan du fortsätta att aktivera AGIC-tillägget i AKS-klustret som du skapade, *myCluster,* och ange AGIC-tillägget för att använda den befintliga Application Gateway som du skapade, *myApplicationGateway.*

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Aktivera AGIC-tillägget i befintligt AKS-kluster via portalen 

Om du vill använda Azure Portal aktivera AGIC-tillägg går du till [( https://aka.ms/azure/portal/aks/agic) ](https://aka.ms/azure/portal/aks/agic) och går till ditt AKS-kluster via portallänken. Därifrån går du till fliken Nätverk i ditt AKS-kluster. Du ser ett avsnitt Application Gateway ingress-kontrollant som gör att du kan aktivera/inaktivera tillägget för ingress-kontrollanten med hjälp av portalens användargränssnitt. Markera kryssrutan bredvid "Enable ingress controller" (Aktivera inkommande styrenhet) och välj den Application Gateway du *skapade, myApplicationGateway,* från den nedrullningsbara menyn. 

![Application Gateway Ingress Controller Portal](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together&quot;></a>Peering av de två virtuella nätverken

Eftersom vi har distribuerat AKS-klustret i ett eget virtuellt nätverk och Application Gateway i ett annat virtuellt nätverk måste du peer-peera de två virtuella nätverken för att trafiken ska flöda från Application Gateway till poddarna i klustret. Peering av de två virtuella nätverken kräver att Azure CLI-kommandot körs två separata gånger för att säkerställa att anslutningen är dubbelriktad. Det första kommandot skapar en peering-anslutning från det Application Gateway virtuella nätverket till det virtuella AKS-nätverket. Det andra kommandot skapar en peering-anslutning i den andra riktningen.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query &quot;nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Distribuera ett exempelprogram med AGIC 

Nu ska du distribuera ett exempelprogram till det AKS-kluster som du skapade som använder AGIC-tillägget för ingress och ansluter Application Gateway till AKS-klustret. Först får du autentiseringsuppgifter för det AKS-kluster som du distribuerade genom att köra `az aks get-credentials` kommandot . 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

När du har autentiseringsuppgifterna för klustret som du skapade kör du följande kommando för att konfigurera ett exempelprogram som använder AGIC för ingress till klustret. AGIC uppdaterar den Application Gateway du konfigurerade tidigare med motsvarande routningsregler till det nya exempelprogrammet som du distribuerade.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Kontrollera att programmet kan nås

Nu när Application Gateway har ställts in för att betjäna trafik till AKS-klustret ska vi kontrollera att programmet kan nås. Först får du IP-adressen för ingressen. 

```azurecli-interactive
kubectl get ingress
```

Kontrollera att exempelprogrammet som du skapade är igång genom att antingen besöka IP-adressen för den Application Gateway som du fick när du körde kommandot ovan eller kontrollera med `curl` . Det kan ta Application Gateway minut att hämta uppdateringen, så om Application Gateway fortfarande är i tillståndet "Uppdaterar" på portalen, så låt den slutföras innan du försöker nå IP-adressen. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om att inaktivera AGIC-tillägget](./ingress-controller-disable-addon.md)
