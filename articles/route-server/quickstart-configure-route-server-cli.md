---
title: 'Snabb start: skapa och konfigurera Route server med Azure CLI'
description: I den här snabb starten får du lära dig hur du skapar och konfigurerar en Route-server med Azure CLI.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: e9c583db7493afc04b2c66553801f62d364b0a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419616"
---
# <a name="quickstart-create-and-configure-route-server-using-azure-cli"></a>Snabb start: skapa och konfigurera Route server med Azure CLI 

Den här artikeln hjälper dig att konfigurera Azure Route server till peer med virtuella nätverks installationer (NVA) i det virtuella nätverket med Azure CLI. Azure Route server kommer att lära sig vägar från NVA och program för de virtuella datorerna i det virtuella nätverket. Den kommer också att annonsera de virtuella nätverks vägarna till NVA. Mer information finns i [Azure Route Server](overview.md).

> [!IMPORTANT]
> Azure Route Server (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

##  <a name="prerequisites"></a>Förutsättningar 

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
* Kontrol lera att du har den senaste versionen av Azure CLI, eller så kan du använda Azure Cloud Shell i portalen. 
* Granska [tjänst gränserna för Azure Route Server](route-server-faq.md#limitations). 

##  <a name="create-a-route-server"></a>Skapa en Route-Server 

###  <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration. 

Logga in på ditt Azure-konto för att påbörja konfigurationen. Om du använder Cloud Shell "testa" är du inloggad automatiskt. Använd följande exempel för att ansluta:

```azurecli-interactive
az login
```

Kontrollera prenumerationerna för kontot.

```azurecli-interactive
az account list
```

Välj den prenumeration som du vill skapa en ExpressRoute-krets för.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

### <a name="create-a-resource-group-and-virtual-network"></a>Skapa en resurs grupp och ett virtuellt nätverk 

Innan du kan skapa en Azure Route-server behöver du ett virtuellt nätverk som värd för distributionen. Använd kommandot Följ för att skapa en resurs grupp och ett virtuellt nätverk. Om du redan har ett virtuellt nätverk kan du gå vidare till nästa avsnitt.

```azurecli-interactive
az group create -n "RouteServerRG" -l "westus" 
az network vnet create -g "RouteServerRG" -n "myVirtualNetwork" --address-prefix "10.0.0.0/16" 
``` 

### <a name="add-a-subnet"></a>Lägga till ett undernät 

1. Lägg till ett undernät med namnet *RouteServerSubnet* för att distribuera Azure Route-servern till. Det här under nätet är endast ett dedikerat undernät för Azure Route Server. RouteServerSubnet måste vara/27 eller ett kortare prefix (till exempel/26,/25), eller så visas ett fel meddelande när du lägger till Azure Route-servern.

    ```azurecli-interactive 
    az network vnet subnet create -g "RouteServerRG" --vnet-name "myVirtualNetwork" --name "RouteServerSubnet" --address-prefix "10.0.0.0/24"  
    ``` 

1. Hämta RouteServerSubnet-ID: t. Om du vill visa resurs-ID för alla undernät i det virtuella nätverket använder du följande kommando: 

    ```azurecli-interactive 
    $subnet_id = $(az network vnet subnet show -n "RouteServerSubnet" --vnet-name "myVirtualNetwork" -g "RouteServerRG" --query id -o tsv) 
    ``` 

RouteServerSubnet-ID: t ser ut ungefär så här: 

`/subscriptions/<subscriptionID>/resourceGroups/RouteServerRG/providers/Microsoft.Network/virtualNetworks/myVirtualNetwork/subnets/RouteServerSubnet`

## <a name="create-the-route-server"></a>Skapa en Route-Server 

Skapa en Route-server med det här kommandot: 

```azurecli-interactive
az network routeserver create -n "myRouteServer" -g "RouteServerRG" --hosted-subnet $subnet_id  
``` 

Platsen måste matcha platsen för det virtuella nätverket. HostedSubnet är RouteServerSubnet-ID: t som du fick i föregående avsnitt. 

## <a name="create-peering-with-an-nva"></a>Skapa peering med en NVA 

Använd följande kommando för att upprätta peering från väg servern till NVA: 

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA1_name" 

``` 

"nva_ip" är den virtuella nätverks-IP som tilldelats till NVA. "nva_asn" är det autonoma system numret (ASN) som kon figurer ATS i NVA. ASN kan vara andra 16-bitarsnummer än de som finns i intervallet 65515-65520. Detta ASN: er är reserverat för Microsoft. 

Använd följande kommando för att konfigurera peering med olika NVA eller en annan instans av samma NVA för redundans:

```azurecli-interactive 

az network routeserver peering create --routeserver-name "myRouteServer" -g "RouteServerRG" --peer-ip "nva_ip" --peer-asn "nva_asn" -n "NVA2_name" 
``` 

## <a name="complete-the-configuration-on-the-nva"></a>Slutför konfigurationen av NVA 

För att slutföra konfigurationen på NVA och aktivera BGP-sessioner behöver du IP och ASN för Azure Route Server. Du kan hämta den här informationen med hjälp av det här kommandot: 

```azurecli-interactive 
az network routeserver show -g "RouteServerRG" -n "myRouteServer" 
``` 

Utdata har följande information. 

```azurecli-interactive 
RouteServerAsn  : 65515 

RouteServerIps  : {10.5.10.4, 10.5.10.5}  "virtualRouterAsn": 65515, 

  "virtualRouterIps": [ 

    "10.0.0.4", 

    "10.0.0.5" 

  ], 

``` 

## <a name="configure-route-exchange"></a>Konfigurera Route Exchange 

Om du har en ExpressRoute-gateway och en Azure VPN-gateway i samma VNet och du vill att de ska utbyta vägar, kan du aktivera routning av Exchange på Azure Route-servern.

> [!IMPORTANT]
> För bygg-distributioner ser du till att skapa Azure VPN-gatewayen innan du skapar en Azure Route-Server. annars fungerar inte distributionen av Azure VPN Gateway.
> 

1. Använd följande kommando om du vill aktivera Route Exchange mellan Azure Route Server och gateway (-erna):

```azurecli-interactive 
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic true 

``` 

2. Använd följande kommando om du vill inaktivera Route Exchange mellan Azure Route Server och gateway (erna):

```azurecli-interactive
az network routeserver update -g "RouteServerRG" -n "myRouteServer" --allow-b2b-traffic false 
``` 

## <a name="troubleshooting"></a>Felsökning 

Du kan visa de vägar som annonseras och tas emot av Azure Route server med det här kommandot:

```azurecli-interactive 
az network routeserver peering list-advertised-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
az network routeserver peering list-learned-routes -g RouteServerRG --vrouter-name myRouteServer -n NVA1_name 
``` 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver Azure Route-servern använder du dessa kommandon för att ta bort BGP-peering och tar sedan bort väg servern. 

1. Ta bort BGP-peering mellan Azure Route Server och en NVA med det här kommandot:

```azurecli-interactive
az network routeserver peering delete --routeserver-name "myRouteServer" -g "RouteServerRG" -n "NVA2_name" 
``` 

2. Ta bort Azure Route server med det här kommandot: 

```azurecli-interactive 
az network routeserver delete -n "myRouteServer" -g "RouteServerRG" 
``` 

## <a name="next-steps"></a>Nästa steg

När du har skapat Azure Route Server kan du fortsätta med att lära dig hur Azure Route Server samverkar med ExpressRoute och VPN-gatewayer: 

> [!div class="nextstepaction"]
> [Azure-ExpressRoute och Azure VPN-support](expressroute-vpn-support.md)
 
