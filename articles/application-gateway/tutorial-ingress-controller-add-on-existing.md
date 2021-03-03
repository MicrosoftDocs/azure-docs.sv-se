---
title: 'Självstudie: Aktivera ingångs styrenhet Add-On för befintligt AKS-kluster med befintlig Azure Application Gateway'
description: Använd den här självstudien för att aktivera ingångs styrenheten Add-On för ditt befintliga AKS-kluster med en befintlig Application Gateway
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 1daf5fef1383272f728ff3dac7557e55398f7d50
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720230"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Självstudie: Aktivera Application Gateway ingress Controller-tillägg för ett befintligt AKS-kluster med en befintlig Application Gateway

Du kan använda Azure CLI eller portal för att aktivera [AGIC-tillägget (Application Gateway ingress Controller)](ingress-controller-overview.md) för ett befintligt [AKS-kluster (Azure Kubernetes Services](https://azure.microsoft.com/services/kubernetes-service/) ). I den här självstudien får du lära dig hur du använder AGIC-tillägg för att exponera ditt Kubernetes-program i ett befintligt AKS-kluster via en befintlig Application Gateway som distribueras i separata virtuella nätverk. Du börjar med att skapa ett AKS-kluster i ett virtuellt nätverk och en Application Gateway i ett separat virtuellt nätverk för att simulera befintliga resurser. Sedan aktiverar du AGIC-tillägget, peer-koppla de två virtuella nätverken och distribuerar ett exempel program som visas via Application Gateway med hjälp av AGIC-tillägget. Om du aktiverar AGIC-tillägget för en befintlig Application Gateway och ett befintligt AKS-kluster i samma virtuella nätverk kan du hoppa över peering-steget nedan. Tillägget ger ett mycket snabbare sätt att distribuera AGIC för ditt AKS-kluster än [tidigare via Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) och erbjuder även en fullständigt hanterad upplevelse.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en resursgrupp 
> * Skapa ett nytt AKS-kluster 
> * Skapa en ny Application Gateway 
> * Aktivera AGIC-tillägget i det befintliga AKS-klustret via Azure CLI 
> * Aktivera AGIC-tillägget i det befintliga AKS-klustret via portalen 
> * Peer The Application Gateway Virtual Network med AKS-klustrets virtuella nätverk
> * Distribuera ett exempel program med AGIC för ingress i AKS-klustret
> * Kontrol lera att programmet kan kontaktas via Application Gateway

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* i *indiensödra* -platsen (region). 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Distribuera ett nytt AKS-kluster

Nu ska du distribuera ett nytt AKS-kluster för att simulera att ha ett befintligt AKS-kluster som du vill aktivera AGIC-tillägget för.  

I följande exempel ska du distribuera ett nytt AKS-kluster *med namnet IT-kluster* med [Azure cni](../aks/concepts-network.md#azure-cni-advanced-networking) och [hanterade identiteter](../aks/use-managed-identity.md) i resurs gruppen som du skapade, *myResourceGroup*.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Om du vill konfigurera ytterligare parametrar för `az aks create` kommandot kan du gå till referenser [här](/cli/azure/aks#az-aks-create). 

## <a name="deploy-a-new-application-gateway"></a>Distribuera en ny Application Gateway 

Nu ska du distribuera en ny Application Gateway, för att simulera att ha en befintlig Application Gateway som du vill använda för att belastningsutjämna trafik till ditt AKS-kluster, för *klustret*. Namnet på Application Gateway kommer att vara *myApplicationGateway*, men du måste först skapa en offentlig IP-resurs med namnet *myPublicIp* och ett nytt virtuellt nätverk som kallas *myVnet* med adress utrymme 11.0.0.0/8 och ett undernät med adress utrymme 11.1.0.0/16 som kallas för *undernät* och distribuera Application Gateway i *under nätet* med *myPublicIp*. 

När du använder ett AKS-kluster och Application Gateway i separata virtuella nätverk, får inte adress utrymmena för de två virtuella nätverken överlappa varandra. Standard adress utrymmet som ett AKS-kluster distribuerar i är 10.0.0.0/8, så vi ställer in det Application Gateway virtuella nätverkets adressprefix till 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> AGIC-tillägget (Application Gateway ingress Controller) stöder **bara** Application Gateway v2 SKU: er (standard och WAF) och **inte** Application Gateway v1 SKU: er. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Aktivera AGIC-tillägget i det befintliga AKS-klustret via Azure CLI 

Om du vill fortsätta att använda Azure CLI kan du fortsätta att aktivera AGIC-tillägget i AKS-klustret som du skapade, till exempel ett *kluster* och ange AGIC-tillägget för att använda den befintliga Application Gateway du skapade, *myApplicationGateway*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Aktivera AGIC-tillägget i det befintliga AKS-klustret via portalen 

Om du vill använda Azure Portal för att aktivera AGIC-tillägg går du till [( https://aka.ms/azure/portal/aks/agic) ](https://aka.ms/azure/portal/aks/agic) och navigerar till ditt AKS-kluster via portal länken. Därifrån går du till fliken nätverk i ditt AKS-kluster. Du ser avsnittet Application Gateway ingångs kontroll, som gör att du kan aktivera/inaktivera tillägg för ingångs styrenheten med hjälp av Portal gränssnittet. Markera kryss rutan bredvid "Aktivera ingångs styrenhet" och välj den Application Gateway du skapade, *myApplicationGateway* från List menyn. 

![Application Gateway ingress-kontrollanten](./media/tutorial-ingress-controller-add-on-existing/portal_ingress_controller_addon.png)

## <a name="peer-the-two-virtual-networks-together"></a>Peer de två virtuella nätverken tillsammans

Eftersom vi har distribuerat AKS-klustret i ett eget virtuellt nätverk och Application Gateway i ett annat virtuellt nätverk, måste du koppla ihop de två virtuella nätverken för att trafik ska flöda från Application Gateway till poddar i klustret. Peering de två virtuella nätverken kräver att du kör Azure CLI-kommandot två separata gånger för att säkerställa att anslutningen är dubbelriktad. Det första kommandot skapar en peering-anslutning från Application Gateway virtuella nätverket till det virtuella AKS-nätverket. det andra kommandot skapar en peering-anslutning i den andra riktningen.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Distribuera ett exempel program med AGIC 

Nu ska du distribuera ett exempel program till det AKS-kluster som du skapade som kommer att använda AGIC-tillägget för ingress och ansluta Application Gateway till AKS-klustret. Först får du autentiseringsuppgifter till det AKS-kluster som du distribuerade genom att köra `az aks get-credentials` kommandot. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

När du har autentiseringsuppgifterna till klustret som du har skapat kör du följande kommando för att konfigurera ett exempel program som använder AGIC för ingångs händelser till klustret. AGIC kommer att uppdatera Application Gateway som du ställer in tidigare med motsvarande routningsregler till det nya exempel program som du har distribuerat.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Kontrol lera att programmet kan kommas åt

Nu när Application Gateway har kon figurer ATS för att betjäna trafik till AKS-klustret ska vi kontrol lera att det går att komma åt programmet. Du får först ta emot IP-adressen för ingångarna. 

```azurecli-interactive
kubectl get ingress
```

Kontrol lera att det exempel program som du har skapat är igång genom att antingen gå till IP-adressen för den Application Gateway som du fick från att köra kommandot ovan eller genom att markera med `curl` . Det kan ta Application Gateway en minut att hämta uppdateringen, så om Application Gateway fortfarande är i ett "uppdaterings läge" på portalen, kan den avslutas innan den försöker nå IP-adressen. 

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om hur du inaktiverar AGIC-tillägget](./ingress-controller-disable-addon.md)
