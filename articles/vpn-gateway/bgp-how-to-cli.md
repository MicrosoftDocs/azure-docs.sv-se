---
title: 'Konfigurera BGP på Azure VPN Gateway: CLI'
description: Den här artikeln vägleder dig genom konfigureringen av BGP med en Azure VPN-gateway med hjälp av Azure Resource Manager och CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: a69ce0592b79be0868dd7c15ac054910eee75fc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89393606"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Så här konfigurerar du BGP på en Azure VPN-gateway med CLI

Den här artikeln hjälper dig att aktivera BGP på en plats-till-plats (S2S) VPN-anslutning och en VNet-till-VNet-anslutning (det vill säga en anslutning mellan virtuella nätverk) med hjälp av Azure Resource Manager distributions modell och Azure CLI.

## <a name="about-bgp"></a>Om BGP

BGP är standardroutningsprotokollet som ofta används på Internet för att utbyta Routning och tillgänglighets information mellan två eller flera nätverk. BGP möjliggör VPN-gatewayer och lokala VPN-enheter, som kallas BGP-peers eller grannar, till Exchange-vägar. Vägarna informerar båda gatewayerna om tillgänglighet och tillgänglighet för prefix för att gå igenom de gatewayer eller routrar som ingår. BGP kan också aktivera överförings dirigering mellan flera nätverk genom att sprida vägarna som en BGP-Gateway lär sig från en BGP-peer, till alla andra BGP-peer-datorer.

Mer information om fördelarna med BGP och att förstå de tekniska kraven och överväganden för att använda BGP finns i [Översikt över BGP med Azure VPN-gatewayer](vpn-gateway-bgp-overview.md).

Den här artikeln hjälper dig med följande uppgifter:

* [Aktivera BGP för din VPN-gateway](#enablebgp) (krävs)

  Du kan sedan utföra något av följande avsnitt eller båda:

* [Upprätta en anslutning mellan platser med BGP](#crossprembgp)
* [Upprätta en VNet-till-VNet-anslutning med BGP](#v2vbgp)

Vart och ett av dessa tre avsnitt utgör ett grundläggande Bygg block för att aktivera BGP i din nätverks anslutning. Om du har slutfört alla tre avsnitten skapar du topologin som visas i följande diagram:

![BGP-topologi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Du kan kombinera dessa avsnitt för att bygga ett mer komplext multihop-överförings nätverk som uppfyller dina behov.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Aktivera BGP för din VPN-gateway

Det här avsnittet är obligatoriskt innan du utför något av stegen i de andra två konfigurations avsnitten. Följande konfigurations steg konfigurerar BGP-parametrarna för Azure VPN-gatewayen som visas i följande diagram:

![BGP-Gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Innan du börjar

Installera den senaste versionen av CLI-kommandona (2,0 eller senare). Information om att installera CLI-kommandona finns i [Installera Azure CLI](/cli/azure/install-azure-cli) och [Kom igång med Azure CLI](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Steg 1: skapa och konfigurera TestVNet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Anslut till din prenumeration

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. skapa en resurs grupp

I följande exempel skapas en resurs grupp med namnet TestRG1 i "öster"-platsen. Om du redan har en resurs grupp i den region där du vill skapa ditt virtuella nätverk kan du använda den i stället.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. skapa TestVNet1

I följande exempel skapas ett virtuellt nätverk med namnet TestVNet1 och tre undernät: GatewaySubnet, FrontEnd och Server del. När du ersätter värden är det viktigt att du alltid namnger ditt Gateway-undernät särskilt GatewaySubnet. Om du ger det något annat namn går det inte att skapa gatewayen.

Det första kommandot skapar adress utrymmet på klient sidan och klient delens undernät. Det andra kommandot skapar ytterligare ett adress utrymme för BackEnd-undernätet. De tredje och fjärde kommandona skapar BackEnd-undernätet och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Steg 2: skapa VPN-gatewayen för TestVNet1 med BGP-parametrar

#### <a name="1-create-the-public-ip-address"></a>1. skapa den offentliga IP-adressen

Begär en offentlig IP-adress. Den offentliga IP-adressen allokeras till den VPN-gateway som du skapar för det virtuella nätverket.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Skapa VPN-gatewayen med AS-numret

Skapa den virtuella nätverksgatewayen för TestVNet1. BGP kräver en Route-Based VPN-gateway. Du behöver också ytterligare en parameter `-Asn` för att ange autonomt system nummer (ASN) för TestVNet1. Att skapa en gateway kan ta en stund (45 minuter eller mer) att slutföra. 

Om du kör det här kommandot med hjälp av `--no-wait` -parametern visas inga synpunkter eller utdata. `--no-wait`Parametern gör att gatewayen kan skapas i bakgrunden. Det innebär inte att VPN-gatewayen skapas direkt.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Hämta IP-adressen för Azure BGP-peer

När gatewayen har skapats måste du hämta IP-adressen för BGP-peer på Azure VPN-gatewayen. Den här adressen krävs för att konfigurera VPN-gatewayen som en BGP-peer för dina lokala VPN-enheter.

Kör följande kommando och markera `bgpSettings` avsnittet överst i utdata:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

När gatewayen har skapats kan du använda denna gateway för att upprätta en anslutning mellan platser eller en VNet-till-VNet-anslutning med BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Upprätta en anslutning mellan platser med BGP

Om du vill upprätta en anslutning mellan olika platser måste du skapa en lokal nätverksgateway som representerar din lokala VPN-enhet. Sedan ansluter du Azure VPN-gatewayen till den lokala Nätverksgatewayen. Även om dessa steg liknar att skapa andra anslutningar, innehåller de ytterligare egenskaper som krävs för att ange BGP-konfigurationsparametrar.

![BGP för mellan platser](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Steg 1: skapa och konfigurera den lokala Nätverksgatewayen

Den här övningen fortsätter att bygga konfigurationen som visas i diagrammet. Ersätt värdena med de som du vill använda för din konfiguration. Tänk på följande när du arbetar med lokala nätverksgateway:

* Den lokala Nätverksgatewayen kan finnas på samma plats och resurs grupp som VPN-gatewayen, eller så kan den finnas på en annan plats och i en annan resurs grupp. I det här exemplet visas gatewayerna i olika resurs grupper på olika platser.
* Det minsta prefix som du behöver deklarera för den lokala Nätverksgatewayen är värd adressen för din IP-adress för BGP-peer på din VPN-enhet. I det här fallet är det ett/32-prefix för 10.51.255.254/32.
* Som en påminnelse måste du använda olika BGP-ASN: er mellan dina lokala nätverk och det virtuella Azure-nätverket. Om de är desamma måste du ändra ditt VNet ASN om dina lokala VPN-enheter redan använder ASN för att peer-koppla med andra BGP-grannar.

Innan du fortsätter kontrollerar du att du har slutfört avsnittet [Aktivera BGP för din VPN-gateway](#enablebgp) i den här övningen och att du fortfarande är ansluten till prenumeration 1. Observera att i det här exemplet skapar du en ny resurs grupp. Lägg också märke till de två ytterligare parametrarna för den lokala Nätverksgatewayen: `Asn` och `BgpPeerAddress` .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Steg 2: Anslut VNet gateway och lokal nätverksgateway

I det här steget skapar du anslutningen från TestVNet1 till site5. Du måste ange `--enable-bgp` parametern för att aktivera BGP för den här anslutningen. 

I det här exemplet finns den virtuella Nätverksgatewayen och den lokala Nätverksgatewayen i olika resurs grupper. När gatewayerna finns i olika resurs grupper måste du ange hela resurs-ID: t för de två gatewayerna för att skapa en anslutning mellan de virtuella nätverken.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Hämta resurs-ID för VNet1GW

Använd utdata från följande kommando för att hämta resurs-ID: t för VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Leta upp raden i utdata `"id":` . Du behöver värdena inom citat tecknen för att skapa anslutningen i nästa avsnitt.

Exempel på utdata:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Kopiera värdena efter `"id":` till en text redigerare, till exempel anteckningar, så att du enkelt kan klistra in dem när du skapar anslutningen. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Hämta resurs-ID för site5

Använd följande kommando för att hämta resurs-ID: t för site5 från utdata:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. skapa TestVNet1-till-site5-anslutningen

I det här steget skapar du anslutningen från TestVNet1 till site5. Som vi nämnt tidigare är det möjligt att ha både BGP-och icke-BGP-anslutningar för samma Azure VPN-gateway. Om inte BGP är aktiverat i anslutnings egenskapen, kommer Azure inte att aktivera BGP för den här anslutningen, även om BGP-parametrar redan har kon figurer ATS på båda gatewayerna. Ersätt prenumerations-ID: n med dina egna.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

I den här övningen visar följande exempel vilka parametrar som ska anges i avsnittet BGP-konfiguration i din lokala VPN-enhet:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Anslutningen bör vara upprättad efter några minuter. BGP-peering-sessionen startar efter att IPsec-anslutningen har upprättats.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Upprätta en VNet-till-VNet-anslutning med BGP

Det här avsnittet lägger till en VNet-till-VNet-anslutning med BGP, som du ser i följande diagram: 

![BGP för VNet-till-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Följande instruktioner fortsätter från stegen i föregående avsnitt. Om du vill skapa och konfigurera TestVNet1 och VPN-gatewayen med BGP måste du fylla i avsnittet [Aktivera BGP för din VPN-gateway](#enablebgp) .

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Steg 1: skapa TestVNet2 och VPN-gatewayen

Det är viktigt att se till att IP-adressutrymmet för det nya virtuella nätverket, TestVNet2, inte överlappar något av dina VNet-intervall.

I det här exemplet tillhör de virtuella nätverken samma prenumeration. Du kan konfigurera VNet-till-VNet-anslutningar mellan olika prenumerationer. Mer information finns i [Konfigurera en VNet-till-VNET-anslutning](vpn-gateway-howto-vnet-vnet-cli.md). Se till att du lägger till `-EnableBgp $True` när du skapar anslutningarna för att aktivera BGP.

#### <a name="1-create-a-new-resource-group"></a>1. skapa en ny resurs grupp

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. skapa TestVNet2 i den nya resurs gruppen

Det första kommandot skapar adress utrymmet på klient sidan och klient delens undernät. Det andra kommandot skapar ytterligare ett adress utrymme för BackEnd-undernätet. De tredje och fjärde kommandona skapar BackEnd-undernätet och GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. skapa den offentliga IP-adressen

Begär en offentlig IP-adress. Den offentliga IP-adressen allokeras till den VPN-gateway som du skapar för det virtuella nätverket.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Skapa VPN-gatewayen med AS-numret

Skapa den virtuella Nätverksgatewayen för TestVNet2. Du måste åsidosätta standard-ASN på dina Azure VPN-gatewayer. ASN: er för anslutna virtuella nätverk måste vara olika för att aktivera BGP och transit routning.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Steg 2: Anslut TestVNet1-och TestVNet2-gatewayerna

I det här steget skapar du anslutningen från TestVNet1 till site5. Om du vill aktivera BGP för den här anslutningen måste du ange `--enable-bgp` parametern.

I följande exempel finns den virtuella Nätverksgatewayen och den lokala Nätverksgatewayen i olika resurs grupper. När gatewayerna finns i olika resurs grupper måste du ange hela resurs-ID: t för de två gatewayerna för att skapa en anslutning mellan de virtuella nätverken. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Hämta resurs-ID för VNet1GW 

Hämta resurs-ID: t för VNet1GW från utdata från följande kommando:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Hämta resurs-ID för VNet2GW

Hämta resurs-ID: t för VNet2GW från utdata från följande kommando:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. skapa anslutningarna

Skapa anslutningen från TestVNet1 till TestVNet2 och anslutningen från TestVNet2 till TestVNet1. Ersätt prenumerations-ID: n med dina egna.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Aktivera BGP för *båda* anslutningarna.
> 
> 

När du har slutfört de här stegen upprättas anslutningen om några minuter. BGP-peering-sessionen kommer att ske när VNet-till-VNet-anslutningen har slutförts.

## <a name="next-steps"></a>Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer i dina virtuella nätverk. Anvisningar finns i [skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md).
