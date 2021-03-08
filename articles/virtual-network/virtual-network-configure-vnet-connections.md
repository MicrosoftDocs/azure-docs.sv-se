---
title: Konfigurera och verifiera virtuella nätverk eller VPN-anslutningar
description: Steg för steg-anvisningar för att konfigurera och validera olika Azure VPN-och virtuella nätverks distributioner
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 9a0d06a8f8fa8f68f063404f2b483b817eb0563f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452109"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Konfigurera och verifiera virtuella nätverk eller VPN-anslutningar

Den här genom gången innehåller steg-för-steg-anvisningar om hur du konfigurerar och validerar olika Azure VPN-och virtuella nätverks distributioner. Scenarier innefattar transit routning, nätverk-till-nätverks-anslutningar, Border Gateway Protocol (BGP), multisite-anslutningar och punkt-till-plats-anslutningar.

Med Azure VPN-gatewayer kan du ordna nästan alla typer av anslutna virtuella nätverk i Azure. Du kan till exempel ansluta virtuella nätverk:

- Mellan regioner.
- Mellan virtuella nätverks typer (Azure Resource Manager jämfört med klassisk).
- I Azure eller i en lokal hybrid miljö.
- I olika prenumerationer. 

## <a name="network-to-network-vpn-connection"></a>Anslutning till nätverks-till-nätverk

Att ansluta ett virtuellt nätverk till ett annat virtuellt nätverk (nätverk till nätverk) via VPN liknar att ansluta ett virtuellt nätverk till en lokal plats. Båda typerna av anslutning använder en VPN-gateway för att tillhandahålla en säker tunnel via IPsec och IKE. De virtuella nätverken kan finnas i samma eller olika regioner och i samma eller olika prenumerationer.

![Anslutning från nätverk till nätverk med IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Om dina virtuella nätverk finns i samma region kan det vara bättre att ansluta dem med hjälp av det virtuella nätverkets peering. Peering för virtuella nätverk använder inte en VPN-gateway. Det ökar data flödet och minskar svars tiden. Om du vill konfigurera en peer anslutning för virtuellt nätverk väljer du **Konfigurera och validera VNet-peering**.

Om dina virtuella nätverk har skapats via distributions modellen för Azure Resource Manager väljer du **Konfigurera och validera en Resource Manager VNet till en Resource Manager VNET-anslutning** för att konfigurera en VPN-anslutning.

Om något av de virtuella nätverken har skapats via den klassiska Azure-distributions modellen och det andra har skapats via Resource Manager väljer du **Konfigurera och validera ett klassiskt VNet till en Resource Manager VNET-anslutning** för att konfigurera en VPN-anslutning.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Konfigurera peering för virtuellt nätverk för två virtuella nätverk i samma region

Kontrol lera att du uppfyller följande krav innan du börjar implementera och konfigurera peering i Azures virtuella nätverk:

* Peerkopplade virtuella nätverk måste finnas i samma Azure-region.
* Peer-kopplat virtuella nätverk måste ha IP-adressutrymme som inte överlappar varandra.
* Peer-kopplingarna i virtuella nätverk upprättas mellan två virtuella nätverk. Det finns ingen härledd transitiv relation mellan peer-kopplingar. Om VNetA till exempel är peer-kopplat med VNetB och VNetB är peer-kopplat med VNetC, är VNetA *inte* peer-koppla med VNetC.

När du uppfyller kraven kan du följa [själv studie kursen: ansluta virtuella nätverk med peering för virtuella nätverk med hjälp av Azure Portal](./tutorial-connect-virtual-networks-portal.md) för att skapa och konfigurera peering.

Använd följande metod för att kontrol lera peering-konfigurationen:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](virtual-network-manage-peering.md#permissions)som krävs.
2. I rutan som innehåller text **Sök resurserna** överst i portalen skriver du **virtuella nätverk**. När **virtuella nätverk** visas i Sök resultaten väljer du det.
3. I bladet **virtuella nätverk** som visas väljer du det virtuella nätverk som du vill skapa en peering för.
4. I fönstret som visas för det virtuella nätverket väljer du **peering** i avsnittet **Inställningar** .
5. Välj en peering och Visa konfigurations resultaten.

![Val för att kontrol lera peering-konfigurationen för virtuellt nätverk](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
För Azure PowerShell kör du kommandot [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) för att hämta det virtuella nätverkets peering. Här är ett exempel:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Ansluta ett virtuellt Resource Manager-nätverk till ett annat virtuellt Resource Manager-nätverk

Du kan konfigurera en anslutning från ett virtuellt Resource Manager-nätverk till ett annat virtuellt Resource Manager-nätverk direkt. Eller så kan du konfigurera anslutningen med hjälp av IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Konfigurera en VPN-anslutning mellan virtuella Resource Manager-nätverk

Om du vill konfigurera en anslutning mellan virtuella Resource Manager-nätverk utan IPsec, se [Konfigurera en anslutning från nätverk till nätverk med hjälp av Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

Om du vill konfigurera en anslutning med IPsec mellan två virtuella Resource Manager-nätverk följer du steg 1 till 5 i [skapa en plats-till-plats-anslutning i Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md) för varje virtuellt nätverk.

> [!Note]
> De här stegen fungerar bara för virtuella nätverk i samma prenumeration. Om dina virtuella nätverk finns i olika prenumerationer måste du använda PowerShell för att upprätta anslutningen. Mer information finns i [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)-artikeln.

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Verifiera VPN-anslutningen mellan virtuella Resource Manager-nätverk

![Klassisk virtuell nätverks anslutning till ett Azure Resource Manager virtuellt nätverk](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Följ dessa instruktioner för att kontrol lera att VPN-anslutningen har kon figurer ATS korrekt.

> [!Note] 
> Antalet efter virtuella nätverks komponenter i de här stegen motsvarar talen i föregående diagram.

1. Se till att det inte finns några överlappande adress utrymmen i de anslutna virtuella nätverken.
2. Kontrol lera att adress intervallet för det Azure Resource Manager virtuella nätverket (1) är korrekt definierat i **anslutnings objekt** instansen (4).
3. Kontrol lera att adress intervallet för det Azure Resource Manager virtuella nätverket (6) är korrekt definierat i **anslutnings objekt** instansen (3).
4. Kontrol lera att de i förväg delade nycklarna matchar anslutnings objekten.
5. Verifiera att Azure Resource Manager virtuell nätverksgateway VIP (2) definieras korrekt i **anslutnings objekt** instansen (4).
6. Kontrol lera att den Azure Resource Manager virtuella Nätverksgatewayen VIP (5) definieras korrekt i **anslutnings objekt** instansen (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Ansluta ett klassiskt virtuellt nätverk till ett virtuellt Resource Manager-nätverk

Du kan skapa en anslutning mellan virtuella nätverk som finns i olika prenumerationer och i olika regioner. Du kan också ansluta virtuella nätverk som redan har anslutningar till lokala nätverk, förutsatt att du har konfigurerat Gateway-typen som Route-baserad.

Om du vill konfigurera en anslutning mellan ett klassiskt virtuellt nätverk och ett virtuellt Resource Manager-nätverk, se [Anslut virtuella nätverk från olika distributions modeller med hjälp av Azure Portal](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

![Diagram som visar en klassisk virtuell nätverks anslutning till ett Azure Resource Manager virtuellt nätverk.](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Följ dessa instruktioner för att kontrol lera konfigurationen när du ansluter ett klassiskt virtuellt nätverk till ett Azure Resource Manager virtuellt nätverk.

> [!Note] 
> Antalet efter virtuella nätverks komponenter i de här stegen motsvarar talen i föregående diagram. 

1. Se till att det inte finns några överlappande adress utrymmen i de anslutna virtuella nätverken.
2. Kontrol lera att adress intervallet för det Azure Resource Manager virtuella nätverket (6) är korrekt definierat i den klassiska lokala nätverks definitionen (3).
3. Kontrol lera att adress intervallet för det klassiska virtuella nätverket (1) är korrekt definierat i Azure Resource Manager **anslutnings objekts** instans (4).
4. Kontrol lera att den klassiska virtuella Nätverksgatewayen VIP (2) är korrekt definierad i Azure Resource Manager **anslutning objekt** instans (4).
5. Kontrol lera att den Azure Resource Manager virtuella Nätverksgatewayen (5) är korrekt definierad i den klassiska **lokala nätverks definitions** instansen (3).
6. Kontrol lera att de i förväg delade nycklarna matchar både anslutna virtuella nätverk:
   - Klassiskt virtuellt nätverk: **definition för lokalt nätverk** (3)
   - Azure Resource Manager virtuellt nätverk: **anslutnings objekt** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Skapa en VPN-anslutning från punkt till plats

Med en punkt-till-plats-konfiguration (*P2s* i följande diagram) kan du skapa en säker anslutning från en enskild klient dator till ett virtuellt nätverk. Punkt-till-plats-anslutningar är användbara när du vill ansluta till ditt virtuella nätverk från en annan plats, t. ex. hemifrån eller från en konferens. De är också användbara när du bara har ett fåtal klienter som behöver ansluta till ett virtuellt nätverk. 

Punkt-till-plats-VPN-anslutningen initieras från klient datorn via den inbyggda Windows VPN-klienten. Anslutande klienter använder certifikat för autentisering.

![Punkt-till-plats-anslutning](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Punkt-till-plats-anslutningar kräver inte någon VPN-enhet. De skapar VPN-anslutningen via SSTP (Secure Socket Tunneling Protocol). Du kan ansluta en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av olika distributions verktyg och distributions modeller:

* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av Azure Portal (klassisk)](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)
* [Konfigurera en punkt-till-plats-anslutning till ett virtuellt nätverk med hjälp av PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="validate-your-point-to-site-connection"></a>Verifiera din punkt-till-plats-anslutning

Artikeln [fel sökning: problem med Azure punkt-till-plats-anslutningen](../vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) går igenom vanliga problem med punkt-till-plats-anslutningar.

## <a name="create-a-multisite-vpn-connection"></a>Skapa en multisite VPN-anslutning

Du kan lägga till en plats-till-plats (*S2S* i följande diagram) anslutning till ett virtuellt nätverk som redan har en plats-till-plats-anslutning, punkt-till-plats-anslutning eller nätverk-till-nätverks-anslutning. Den här typen av anslutning kallas ofta för en *multisitedistribution* . 

![Multisite-anslutning](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Azure fungerar för nuvarande med två distributionsmodeller: Resource Manager och klassisk. De två modellerna är inte helt kompatibla med varandra. Information om hur du konfigurerar en multisite-anslutning med olika modeller finns i följande artiklar:

* [Lägga till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig VPN gateway-anslutning](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
* [Lägga till en plats-till-plats-anslutning till ett virtuellt nätverk med en befintlig VPN gateway-anslutning (klassisk)](../vpn-gateway/vpn-gateway-multi-site.md)

> [!Note]
> Stegen i dessa artiklar gäller inte för Azure-ExpressRoute och sambefintliga konfigurationer för plats-till-plats-anslutning. Mer information finns i [ExpressRoute och samtidiga samtidiga anslutningar från plats till plats](../expressroute/expressroute-howto-coexist-resource-manager.md).

## <a name="configure-transit-routing"></a>Konfigurera överförings dirigering

Transit routning är ett särskilt routningsgränssnitt där du kan ansluta flera nätverk i en seriekopplade-topologi. Den här dirigeringen aktiverar resurser i virtuella nätverk i båda ändar av kedjan för att kommunicera med varandra via virtuella nätverk i mellan. Utan transit routning kan nätverk eller enheter som peer-kopplas via en hubb inte komma åt varandra.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Konfigurera transit routning i en punkt-till-plats-anslutning

Föreställ dig ett scenario där du vill konfigurera en plats-till-plats-VPN-anslutning mellan VNetA och VNetB. Du vill också konfigurera en punkt-till-plats-VPN för klienten för att ansluta till gatewayen för VNetA. Sedan vill du aktivera transit routning för punkt-till-plats-klienter för att ansluta till VNetB, som passerar genom VNetA. 

Det här scenariot stöds när BGP är aktiverat på plats-till-plats-VPN mellan VNetA och VNetB. Mer information finns i [om punkt-till-plats-VPN-routning](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Konfigurera överförings dirigering i en ExpressRoute-anslutning

Med Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning från en anslutningsleverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster som Microsoft Azure, Microsoft 365 och Dynamics 365. Mer information finns i [Översikt över ExpressRoute](../expressroute/expressroute-introduction.md).

![ExpressRoute privat peering-anslutning till virtuella Azure-nätverk](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Vi rekommenderar att om VNetA och VNetB finns i samma naturpolitisk region [länkar du båda virtuella nätverken till ExpressRoute-kretsen](../expressroute/expressroute-howto-linkvnet-arm.md) i stället för att konfigurera transit routning. Om dina virtuella nätverk finns i olika politiska regioner kan du också länka dem till din krets direkt om du har [ExpressRoute Premium](../expressroute/expressroute-faqs.md#expressroute-premium). 

Om du har ExpressRoute och plats-till-plats-samexistens stöds inte transit routning. Mer information finns i [Konfigurera ExpressRoute och plats-till-plats med hjälp av PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md).

Om du har aktiverat ExpressRoute för att ansluta dina lokala nätverk till ett virtuellt Azure-nätverk kan du aktivera peering mellan de virtuella nätverk där du vill ha överförings dirigering. Om du vill tillåta att dina lokala nätverk ansluter till det virtuella fjärrnätverket måste du konfigurera [peering för virtuella nätverk](./virtual-network-peering-overview.md#gateways-and-on-premises-connectivity). 

> [!Note]
> Peering för virtuella nätverk är endast tillgängligt för virtuella nätverk i samma region.

Följ dessa anvisningar för att kontrol lera om du har konfigurerat överförings dirigering för peering av virtuella nätverk:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](virtual-network-manage-peering.md#permissions)som krävs.
2. [Skapa en peering mellan VNetA och VNetB](./tutorial-connect-virtual-networks-portal.md) som visas i det tidigare diagrammet. 
3. I fönstret som visas för det virtuella nätverket väljer du **peering** i avsnittet **Inställningar** .
4. Välj den peering som du vill visa. Välj sedan **konfiguration** för att kontrol lera att du har aktiverat **Tillåt Gateway-överföring** på VNetA-nätverket som är anslutet till ExpressRoute-kretsen och **Använd fjärrgateway** på VNetB-nätverket som inte är anslutet till ExpressRoute-kretsen.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Konfigurera överförings dirigering i ett virtuellt nätverk peering-anslutning

När virtuella nätverk har peer-kopplats kan du också konfigurera gatewayen i det peer-kopplade virtuella nätverket som en överföringspunkt till det lokala nätverket. Information om hur du konfigurerar en överförings väg i virtuell nätverks-peering finns i [nätverk-till-nätverk-anslutningar](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!Note]
> Gateway-överföring stöds inte i peering-relationen mellan virtuella nätverk som skapats via olika distributions modeller. Både virtuella nätverk i peering-relationen måste ha skapats genom att Resource Manager för gateway-överföring fungerar.

Följ dessa anvisningar för att kontrol lera om du har konfigurerat en överförings väg för peering av virtuella nätverk:

1. Logga in på [Azure Portal](https://portal.azure.com/) med ett konto som har de [roller och behörigheter](virtual-network-manage-peering.md#permissions)som krävs.
2. I rutan som innehåller text **Sök resurserna** överst i portalen skriver du **virtuella nätverk**. När **virtuella nätverk** visas i Sök resultaten väljer du det.
3. I bladet **virtuella nätverk** som visas väljer du det virtuella nätverk som du vill kontrol lera peering-inställningen för.
4. I fönstret som visas för det virtuella nätverk som du har valt väljer du **peering** i avsnittet **Inställningar** .
5. Välj den peering som du vill visa. Kontrol lera att du har aktiverat **Tillåt Gateway-överföring** och **Använd fjärrgatewayer** under **konfigurationen**.

![Val för att kontrol lera att du har konfigurerat en överförings väg för peering av virtuella nätverk](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Konfigurera överförings dirigering i en anslutning från nätverk till nätverk

Om du vill konfigurera transit routning mellan virtuella nätverk måste du aktivera BGP på alla mellanliggande nätverk-till-nätverks-anslutningar med hjälp av distributions modellen för Resource Manager och PowerShell. Instruktioner finns i [så här konfigurerar du BGP på Azure VPN-gatewayer med hjälp av PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Överförings trafik via Azure VPN-gatewayer är möjlig via den klassiska distributions modellen, men den förlitar sig på statiska definierade adress utrymmen i nätverks konfigurations filen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer via den klassiska distributions modellen. Utan BGP är manuell definiering av överförings adress utrymmen fel känslig och vi rekommenderar det inte.

> [!Note]
> Du konfigurerar klassiska nätverks anslutningar med hjälp av den klassiska Azure-portalen eller genom att använda en nätverks konfigurations fil på den klassiska portalen. Du kan inte skapa eller ändra ett klassiskt virtuellt nätverk via Azure Resource Manager distributions modell eller Azure Portal. Mer information om transit routning för klassiska virtuella nätverk finns i [Microsoft Developer-bloggen](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Konfigurera överförings dirigering i en plats-till-plats-anslutning

Om du vill konfigurera överförings dirigering mellan ditt lokala nätverk och ett virtuellt nätverk med en plats-till-plats-anslutning måste du aktivera BGP på alla mellanliggande plats-till-plats-anslutningar med hjälp av distributions modellen för Resource Manager och PowerShell. Instruktioner finns i [så här konfigurerar du BGP på Azure VPN-gatewayer med hjälp av PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) .

Överförings trafik via Azure VPN-gatewayer är möjlig via den klassiska distributions modellen, men den förlitar sig på statiska definierade adress utrymmen i nätverks konfigurations filen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer via den klassiska distributions modellen. Utan BGP är manuell definiering av överförings adress utrymmen fel känslig och vi rekommenderar det inte.

> [!Note]
> Du konfigurerar klassiska plats-till-plats-anslutningar med hjälp av den klassiska Azure-portalen eller genom att använda en nätverks konfigurations fil på den klassiska portalen. Du kan inte skapa eller ändra ett klassiskt virtuellt nätverk via Azure Resource Manager distributions modell eller Azure Portal. Mer information om transit routning för klassiska virtuella nätverk finns i [Microsoft Developer-bloggen](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Konfigurera BGP för en VPN-gateway

BGP är det standard Dirigerings protokoll som används på Internet för att utbyta routnings-och tillgänglighets information mellan två eller flera nätverk. När BGP används i Azure Virtual Networks-kontexter, aktiverar det Azure VPN-gatewayer och dina lokala VPN-enheter, som kallas BGP-peers eller grannar. De utbyter "vägar" som kommer att informera båda gatewayerna om tillgänglighet och tillgänglighet för dessa prefix för att gå igenom de gatewayer eller routrar som ingår. 

BGP kan också aktivera överförings dirigering mellan flera nätverk genom att sprida vägar som en BGP-Gateway lär sig från en BGP-peer till alla andra BGP-peer-datorer. Mer information finns i [Översikt över BGP med Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurera BGP för en VPN-anslutning

Information om hur du konfigurerar en VPN-anslutning som använder BGP finns i [så här konfigurerar du BGP på Azure VPN-gatewayer med hjälp av PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md).

Aktivera BGP på den virtuella Nätverksgatewayen genom att skapa ett autonomt system (AS)-nummer för det. Basic gateways stöder inte BGP. Om du vill kontrol lera SKU för gatewayen går du till avsnittet **Översikt** på bladet **VPN gateway** i Azure Portal. Om SKU: n är **Basic** måste du ändra SKU: n (se ändra [storlek på gatewayen](/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway)) till **VpnGw1**. 

Att kontrol lera SKU: n kommer att orsaka 20 till 30 minuters stillestånds tid. Så snart gatewayen har rätt SKU kan du lägga till AS-numret med hjälp av PowerShell-kommandot för [set-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway) . När du har konfigurerat AS-numret kommer en BGP peer-IP för gatewayen att tillhandahållas automatiskt.

Du måste ange `LocalNetworkGateway` ett as-nummer och en BGP-peer-adress manuellt. Du kan ställa in `ASN` `-BgpPeeringAddress` värdena och genom att använda antingen [New-AzureRmLocalNetworkGateway](/powershell/module/azurerm.network/new-azurermlocalnetworkgateway) eller [set-AzureRmLocalNetworkGateway PowerShell-](/powershell/module/azurerm.network/set-azurermlocalnetworkgateway) kommandot. Vissa tal är reserverade för Azure och du kan inte använda dem som beskrivs i [om BGP med Azure VPN gateway](../vpn-gateway/vpn-gateway-bgp-overview.md#faq).

BGP måste vara aktiverat för anslutningsobjektet. Du kan ange `-EnableBGP` värdet till `$True` genom [New-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection) eller [set-AzureRmVirtualNetworkGatewayConnection](/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection).

### <a name="validate-the-bgp-configuration"></a>Verifiera BGP-konfigurationen

Du kan kontrol lera om BGP har kon figurer ATS korrekt genom att köra- `get-AzureRmVirtualNetworkGateway` och `get-AzureRmLocalNetworkGateway` cmdletarna. Sedan kommer du att märka BGP-relaterade utdata i `BgpSettingsText` delen. Exempel:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Skapa en aktiv/aktiv VPN-anslutning med hög tillgänglighet

De viktigaste skillnaderna mellan de aktiva/aktiva och aktiva/standby-gatewayerna är:

* Du måste skapa två Gateway-IP-konfigurationer med två offentliga IP-adresser.
* Du måste ange flaggan **EnableActiveActiveFeature** .
* Gateway-SKU: n måste vara **VpnGw1**, **VpnGw2** eller **VpnGw3**.

För att uppnå hög tillgänglighet för anslutningar mellan olika platser och nätverks anslutning, bör du distribuera flera VPN-gatewayer och upprätta flera parallella anslutningar mellan dina nätverk och Azure. En översikt över anslutnings alternativ och topologi finns i [hög tillgängliga anslutningar mellan olika platser och nätverk till nätverk](../vpn-gateway/vpn-gateway-highlyavailable.md).

Om du vill skapa aktiva/aktiva anslutningar mellan platser och nätverk, följer du anvisningarna i [Konfigurera aktiva/aktiva S2S VPN-anslutningar med Azure VPN-gatewayer](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) för att konfigurera en Azure VPN-gateway i aktivt/aktivt läge.

> [!Note]  
> * När du lägger till adresser i den lokala Nätverksgatewayen för BGP-aktiverade aktiva/aktiva läge, *lägger du bara till/32-adresserna för BGP-peer-datorer*. Om du lägger till fler adresser betraktas de som statiska vägar och prioriteras framför BGP-vägar.
> * Du måste använda olika BGP AS-nummer för dina lokala nätverk som ansluter till Azure. (Om de är samma måste du ändra ditt virtuella nätverk som nummer om din lokala VPN-enhet redan använder ASN för att peer-koppla med andra BGP-grannar.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Ändra en Azure VPN gateway-typ efter distributionen

Du kan inte ändra en Azure Virtual Network Gateway-typ från principbaserad till Route-baserad eller på det andra sättet direkt. Du måste först ta bort gatewayen. Därefter bevaras inte IP-adressen och den i förväg delade nyckeln. Sedan kan du skapa en ny Gateway av önskad typ. 

Följ dessa steg om du vill ta bort och skapa en gateway:

1. Ta bort alla anslutningar som är associerade med den ursprungliga gatewayen.
2. Ta bort gatewayen med hjälp av Azure Portal, PowerShell eller klassisk PowerShell: 
   * [Ta bort en virtuell nätverksgateway med hjälp av Azure Portal](../vpn-gateway/vpn-gateway-delete-vnet-gateway-portal.md)
   * [Ta bort en virtuell nätverksgateway med hjälp av PowerShell](../vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Ta bort en virtuell nätverksgateway med hjälp av PowerShell (klassisk)](../vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell.md)
3. Följ stegen i [skapa VPN-gatewayen](../vpn-gateway/tutorial-site-to-site-portal.md#VNetGateway) för att skapa den nya gatewayen av önskad typ och slutför VPN-installationen.

> [!Note]
> Processen tar cirka 60 minuter.

## <a name="next-steps"></a>Nästa steg

* [Felsökning av anslutningsproblem mellan virtuella Azure-datorer](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
