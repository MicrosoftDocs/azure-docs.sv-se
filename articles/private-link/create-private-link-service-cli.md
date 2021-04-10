---
title: Snabb start – skapa en Azure Private Link-tjänst med Azure CLI
description: I den här snabb starten får du lära dig hur du skapar en Azure Private Link-tjänst med Azure CLI
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563140"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Snabb start: skapa en privat länk-tjänst med Azure CLI

Kom igång med att skapa en privat länk-tjänst som refererar till din tjänst.  Ge privat länk åtkomst till din tjänst eller resurs som distribueras bakom en Azure-Standard Load Balancer.  Användare av tjänsten har privat åtkomst från det virtuella nätverket.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Den här snabb starten kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group#az_group_create):

* Med namnet **CreatePrivLinkService-RG**. 
* På den **östra** platsen.

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Skapa en intern lastbalanserare

I det här avsnittet ska du skapa ett virtuellt nätverk och ett internt Azure Load Balancer.

### <a name="virtual-network"></a>Virtuellt nätverk

I det här avsnittet skapar du ett virtuellt nätverk och ett undernät som är värd för belastningsutjämnaren som har åtkomst till din privata länk tjänst.

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create):

* Med namnet **myVNet**.
* Adressprefix för **10.1.0.0/16**.
* Undernät med namnet mina **undernät**.
* Undernätsprefixet för **10.1.0.0/24**.
* I resurs gruppen **CreatePrivLinkService-RG** .
* Plats för **eastus2**.
* Inaktivera nätverks principen för tjänsten för privat länk i under nätet.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Om du vill uppdatera under nätet för att inaktivera nätverks principer för privata länk tjänster använder du [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Skapa standard Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb#az-network-lb-create):

* Med namnet **myLoadBalancer**.
* En frontend-pool med namnet ' **frontend**'.
* En backend-pool med namnet **myBackEndPool**.
* Associerat med den virtuella nätverks **myVNet**.
* Associerat med backend- **undernätets undernät.**

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

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](/cli/azure/network/lb/probe#az-network-lb-probe-create):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network lb Rule Create](/cli/azure/network/lb/rule#az-network-lb-rule-create):

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe** för hälso avsökning.
* Protokoll- **TCP**.
* Tids gräns för inaktivitet på **15 minuter**.
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

## <a name="create-a-private-link-service"></a>Skapa en privat länk-tjänst

I det här avsnittet skapar du en privat länk-tjänst som använder Azure Load Balancer som skapades i föregående steg.

Skapa en privat länk-tjänst med en standard-IP-konfiguration för belastningsutjämnare för klient del med [AZ Network Private-Link-service Create](/cli/azure/network/private-link-service#az-network-private-link-service-create):

* Med namnet **myPrivateLinkService**.
* I virtuellt nätverk **myVNet**.
* Kopplad till **myLoadBalancer** för standard belastnings utjämning och frontend **-konfiguration för klient delen.**
* På **eastus2** -platsen.
 
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

Din privata länk tjänst skapas och kan ta emot trafik. Om du vill se trafikflöden konfigurerar du ditt program bakom din standard belastningsutjämnare.


## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet ska du mappa den privata länk tjänsten till en privat slut punkt. Ett virtuellt nätverk innehåller den privata slut punkten för den privata länk tjänsten. Det här virtuella nätverket innehåller de resurser som kommer att ha åtkomst till din privata länk tjänst.

### <a name="create-private-endpoint-virtual-network"></a>Skapa privat slut punkt för virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet#az-network-vnet-create):

* Med namnet **myVNetPE**.
* Adressprefix för **11.1.0.0/16**.
* Undernät med namnet **mySubnetPE**.
* Undernätsprefixet för **11.1.0.0/24**.
* I resurs gruppen **CreatePrivLinkService-RG** .
* Plats för **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Om du vill uppdatera under nätet för att inaktivera principer för privata nätverks slut punkter använder du [AZ Network VNet Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Skapa slut punkt och anslutning

* Använd [AZ Network Private-Link-service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) för att hämta resurs-ID för den privata länk tjänsten. Kommandot placerar resurs-ID: t i en variabel för senare användning.

* Använd [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) för att skapa den privata slut punkten i det virtuella nätverk som du skapade tidigare.

* Med namnet **MyPrivateEndpoint**.
* I resurs gruppen **CreatePrivLinkService-RG** .
* **MyPEconnectiontoPLS** för anslutningens namn.
* Plats för **eastus2**.
* I Virtual Network **myVNetPE** och Subnet **mySubnetPE**.

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

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen, privat länk tjänsten, belastningsutjämnaren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten kommer du att göra följande:

* Skapade ett virtuellt nätverk och internt Azure Load Balancer.
* En privat länk tjänst har skapats

Om du vill veta mer om den privata Azure-slutpunkten fortsätter du till:
> [!div class="nextstepaction"]
> [Snabb start: skapa en privat slut punkt med Azure CLI](create-private-endpoint-cli.md)
