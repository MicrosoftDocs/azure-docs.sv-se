---
title: Snabbstart – Skapa en Azure Private Link med Azure CLI
description: I den här snabbstarten lär du dig hur du skapar en Azure Private Link-tjänst med Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: c8e32a56148326104c3514b8a2fdb5d6bbd3f00a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778489"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Snabbstart: Skapa en Private Link tjänst med Azure CLI

Kom igång med att Private Link tjänst som refererar till din tjänst.  Ge Private Link åtkomst till din tjänst eller resurs som distribuerats bakom en Azure-Standard Load Balancer.  Användare av din tjänst har privat åtkomst från sina virtuella nätverk.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Den här snabbstarten kräver version 2.0.28 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create):

* Med **namnet CreatePrivLinkService-rg**. 
* På **platsen eastus.**

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

I det här avsnittet skapar du ett virtuellt nätverk och en intern Azure Load Balancer.

### <a name="virtual-network"></a>Virtuellt nätverk

I det här avsnittet skapar du ett virtuellt nätverk och undernät som är värd för lastbalanseraren som har åtkomst Private Link tjänsten.

Skapa ett virtuellt nätverk med [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create):

* Med **namnet myVNet**.
* Adressprefixet **10.1.0.0/16**.
* Undernät med namnet **mySubnet**.
* Undernätsprefixet **10.1.0.0/24**.
* I **resursgruppen CreatePrivLinkService-rg.**
* Platsen **eastus2**.
* Inaktivera nätverksprincipen för private link-tjänsten i undernätet.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Om du vill uppdatera undernätet för att inaktivera private link-tjänstnätverksprinciper använder [du az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Skapa en standardlastbalanserare

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-adresspool på serversidan som tar emot inkommande nätverkstrafik på lastbalanseraren.
  * En BACKEND-IP-pool där frontend-poolen skickar belastningsutjämnad nätverkstrafik.
  * En hälsoavsökning som fastställer hälsotillståndet för de virtuella datorinstanserna på backend-datorn.
  * En lastbalanseringsregel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa lastbalanseringsresursen

Skapa en offentlig lastbalanserare med [az network lb create](/cli/azure/network/lb#az_network_lb_create):

* Med **namnet myLoadBalancer**.
* En frontend-pool med **namnet myFrontEnd**.
* En backend-pool med **namnet myBackEndPool**.
* Associeras med det virtuella nätverket **myVNet**.
* Associeras med undernätet **mySubnet i backend.**

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälsoavsökning kontrollerar alla instanser av virtuella datorer för att säkerställa att de kan skicka nätverkstrafik. 

En virtuell dator med en misslyckad avsökningskontroll tas bort från lastbalanseraren. Den virtuella datorn läggs tillbaka i lastbalanseraren när felet är löst.

Skapa en hälsoavsökning med [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create):

* Övervakar hälsotillståndet för de virtuella datorerna.
* Med **namnet myHealthProbe.**
* Protokoll **TCP**.
* Övervakning **av port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En lastbalanseringsregel definierar:

* Frontend-IP-konfiguration för inkommande trafik.
* Backend-IP-poolen som ska ta emot trafiken.
* Käll- och målporten som krävs. 

Skapa en lastbalanseringsregel med [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create):

* Med **namnet myHTTPRule**
* Lyssnar på **port 80** i frontend-poolen **myFrontEnd**.
* Skicka belastningsutjämnad nätverkstrafik till backend-adresspoolen **myBackEndPool** med **port 80**. 
* Använda hälsoavsökningen **myHealthProbe**.
* Protokoll **TCP**.
* Tidsgräns för inaktivitet på **15 minuter**.
* Aktivera TCP-återställning.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Skapa en privat länktjänst

I det här avsnittet skapar du en privat länktjänst som använder Azure Load Balancer som skapades i föregående steg.

Skapa en privat länktjänst med en standardkonfiguration för ip-adresser för lastbalanserare på frontend [med az network private-link-service create](/cli/azure/network/private-link-service#az_network_private_link_service_create):

* Med **namnet myPrivateLinkService**.
* I det virtuella nätverket **myVNet**.
* Associerat med standardlastbalanserare **myLoadBalancer** och frontend-konfigurationen **myFrontEnd**.
* På platsen **eastus2.**
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Din privata länktjänst skapas och kan ta emot trafik. Om du vill se trafikflöden konfigurerar du programmet bakom din standardlastbalanserare.


## <a name="create-private-endpoint"></a>Skapa privat slutpunkt

I det här avsnittet mappar du tjänsten private link till en privat slutpunkt. Ett virtuellt nätverk innehåller den privata slutpunkten för private link-tjänsten. Det här virtuella nätverket innehåller de resurser som kommer åt din privata länktjänst.

### <a name="create-private-endpoint-virtual-network"></a>Skapa privat virtuellt slutpunktsnätverk

Skapa ett virtuellt nätverk med [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create):

* Med **namnet myVNetPE**.
* Adressprefixet **11.1.0.0/16**.
* Undernät med **namnet mySubnetPE**.
* Undernätsprefixet **11.1.0.0/24**.
* I **resursgruppen CreatePrivLinkService-rg.**
* Platsen **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Om du vill uppdatera undernätet för att inaktivera principer för privat slutpunktsnätverk använder [du az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Skapa slutpunkt och anslutning

* Använd [az network private-link-service show för](/cli/azure/network/private-link-service#az_network_private_link_service_show) att hämta resurs-ID för private link-tjänsten. Kommandot placerar resurs-ID:t i en variabel för senare användning.

* Använd [az network private-endpoint create för](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) att skapa den privata slutpunkten i det virtuella nätverk som du skapade tidigare.

* Med **namnet MyPrivateEndpoint**.
* I **resursgruppen CreatePrivLinkService-rg.**
* Anslutningsnamnet **myPEconnectiontoPLS**.
* Platsen **eastus2**.
* I det virtuella **nätverket myVNetPE** och undernätet **mySubnetPE**.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [kommandot az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, private link-tjänsten, lastbalanseraren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten kommer du att göra följande:

* Skapat ett virtuellt nätverk och en intern Azure Load Balancer.
* Skapat en private link-tjänst

Om du vill veta mer om den privata Azure-slutpunkten fortsätter du till:
> [!div class="nextstepaction"]
> [Snabbstart: Skapa en privat slutpunkt med Azure CLI](create-private-endpoint-cli.md)
