---
title: Trafikdirigering i virtuella Azure-nätverk
titlesuffix: Azure Virtual Network
description: Lär dig hur Azure dirigerar trafik i virtuella nätverk och hur du kan anpassa Azures routning.
services: virtual-network
documentationcenter: na
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2021
ms.author: aldomel
ms.openlocfilehash: 232b83fef2da312828f4f9f332ab2505e3a68100
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503651"
---
# <a name="virtual-network-traffic-routing"></a>Trafikdirigering i virtuella nätverk

Lär dig mer om hur Azure dirigerar trafik mellan Azure, lokala och Internet-resurser. Azure skapar automatiskt en routningstabell för varje undernät inom ett virtuellt Azure-nätverk och lägger till systemstandardvägar i tabellen. Läs mer om virtuella nätverk och undernät i [Översikt över virtuella nätverk](virtual-networks-overview.md). Du kan åsidosätta en del av Azures systemvägar med [anpassade vägar](#custom-routes) och lägga till fler anpassade vägar i routningstabeller. Azure dirigerar trafik från ett undernät baserat på vägar i routningstabellen för ett undernät.

## <a name="system-routes"></a>Systemvägar

Azure skapar automatiskt systemvägar och tilldelar vägarna till varje undernät i ett virtuellt nätverk. Du kan varken skapa eller ta bort systemvägar, men du kan åsidosätta vissa systemvägar med [anpassade vägar](#custom-routes). Azure skapar standardsystemvägar för varje undernät och lägger till ytterligare [valfria standardvägar](#optional-default-routes) till specifika undernät eller varje undernät när du använder specifika funktioner i Azure.

### <a name="default"></a>Standardvärde

Varje väg innehåller ett adressprefix och en nästa hopp-typ. När trafik lämnar ett undernät och skickas till en IP-adress inom en vägs adressprefix använder Azure vägen som innehåller prefixet. Läs mer om [hur vägar väljs i Azure](#how-azure-selects-a-route) när flera vägar innehåller samma prefix eller överlappande prefix. När ett virtuellt nätverk skapas skapar Azure automatiskt följande standardsystemvägar för varje undernät inom det virtuella nätverket:

|Källa |Adressprefix                                        |Nästa hopptyp  |
|-------|---------                                               |---------      |
|Standard|Unikt för det virtuella nätverket                           |Virtuellt nätverk|
|Standard|0.0.0.0/0                                               |Internet       |
|Standard|10.0.0.0/8                                              |Inget           |
|Standard|192.168.0.0/16                                          |Inget           |
|Standard|100.64.0.0/10                                           |Inget           |

Nästa hopptyper som anges i föregående tabell representerar hur Azure dirigerar trafik till det angivna adressprefixet. Här följer förklaringar för nästa hopptyper:

* **Virtuellt nätverk**: Dirigerar trafik mellan adressintervall inom det virtuella nätverkets [adressutrymme](manage-virtual-network.md#add-or-remove-an-address-range). Azure skapar en väg med ett adressprefix som motsvarar varje adressintervall som definierats inom adressutrymmet för ett virtuellt nätverk. Om det virtuella nätverkets adressutrymme har flera definierade adressintervall skapar Azure en enskild väg för varje adressintervall. Azure dirigerar automatiskt trafik mellan undernät med de vägar som skapas för varje adressintervall. Du behöver inte definiera gatewayer för Azure för att dirigera trafik mellan undernät. Även om ett virtuellt nätverk innehåller undernät, och varje undernät har ett definierat adressintervall, skapar *inte* Azure standardvägar för undernäts adressintervall. Det beror på att varje undernäts adressintervall är inom ett adressintervall för ett virtuellt nätverks adressutrymme.<br>
* **Internet**: dirigerar trafik som anges av adressprefixet till Internet. Systemstandardvägen anger adressprefixet 0.0.0.0/0. Om du inte åsidosätter Azures standardvägar dirigerar Azure trafik för alla adresser som inte har angetts av ett adressintervall inom ett virtuellt nätverk till Internet, med ett undantag. Om måladressen är någon av Azures tjänster dirigerar Azure trafiken direkt till tjänsten via Azures stamnätverk i stället för att dirigera trafiken till Internet. Trafik mellan Azure-tjänster sker inte via Internet, oavsett vilken Azure-region det virtuella nätverket finns i eller vilken Azure-region en instans av Azure-tjänsten har distribuerats i. Du kan åsidosätta Azures standardsystemväg för adressprefixet 0.0.0.0/0 med en [anpassad väg](#custom-routes).<br>
* **None** (Ingen): Trafik som dirigeras till den nästa hopptypen **None** (Ingen) tas bort istället för att dirigeras utanför undernätet. Azure skapar automatiskt standardvägar för följande adressprefix:<br>

    * **10.0.0.0/8 och 192.168.0.0/16:** Reserverat för privat användning i RFC 1918.<br>
    * **100.64.0.0/10**: Reserverad i RFC 6598.

    Om du tilldelar några av de föregående adressintervallerna inom adressutrymmet för ett virtuellt nätverk ändrar Azure automatiskt nästa hopptyp för vägen från **None** (Ingen) till **Virtuellt nätverk**. Om du tilldelar ett adressintervall till ett virtuellt nätverks adressområde som inkluderar, men inte är detsamma som, något av de fyra reserverade adressprefixen, tar Azure bort prefixets väg och lägger till vägen för adressprefixet du la till, med **Virtuellt nätverk** som nästa hopptyp.

### <a name="optional-default-routes"></a>Valfria standardvägar

Azure lägger till ytterligare systemstandardvägar för olika Azure-funktioner, men endast om du aktiverar funktionerna. Beroende på funktion lägger Azure till valfria standardvägar till antingen specifika undernät i det virtuella nätverket eller till alla undernät i ett virtuellt nätverk. De ytterligare systemvägar och nästa hopptyper som Azure kan lägga till när du aktiverar olika funktioner är:

|Källa                 |Adressprefix                       |Nästa hopptyp|Undernät för virtuellt nätverk som vägen har lagts till i|
|-----                  |----                                   |---------                    |--------|
|Standardvärde                |Unikt för det virtuella nätverket, till exempel 10.1.0.0/16|VNet-peering                 |Alla|
|Virtuell nätverksgateway|Prefix annonseras lokalt via BGP eller konfigureras i den lokala nätverksgatewayen     |Virtuell nätverksgateway      |Alla|
|Standardvärde                |Flera                               |VirtualNetworkServiceEndpoint|Endast undernätet en tjänstslutpunkt har aktiverats för.|

* **Virtuell nätverkspeering (VNet-peering)**: När du skapar en virtuell nätverkspeering mellan två virtuella nätverk läggs en väg till för varje adressintervall i adressutrymmet för varje virtuellt nätverk som en peering har skapats för. Läs mer om [virtuell nätverkspeering](virtual-network-peering-overview.md).<br>
* **Virtuell nätverksgateway**: En eller flera vägar med *virtuell nätverksgateway* angiven som nästa hopptyp läggs till när en virtuell nätverksgateway läggs till för ett virtuellt nätverk. Källan är också *virtuell nätverksgateway* eftersom gatewayen lägger till vägar till undernätet. Om din lokala nätverksgateway utbyter[BGP-vägar](#border-gateway-protocol)(Border Gateway Protocol) med en virtuell nätverksgateway i Azure läggs en väg till för varje väg som sprids från den lokala nätverksgatewayen. Vi rekommenderar att du sammanfattar lokala vägar till största möjliga adressområden, så att så få antal vägar som möjligt sprids till en virtuell nätverksgateway i Azure. Det finns begränsningar för hur många vägar du kan sprida till en virtuell nätverksgateway i Azure. Läs mer i informationen om [begränsningar för Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).<br>
* **VirtualNetworkServiceEndpoint**: De offentliga IP-adresserna för vissa tjänster läggs till i routningstabellen av Azure när du aktiverar en tjänstslutpunkt för tjänsten. Tjänstslutpunkter aktiveras för enskilda undernät i ett virtuellt nätverk, så att vägen endast läggs till i routningstabellen för ett undernät som en tjänstslutpunkt är aktiverad för. Azure-tjänsters offentliga IP-adresser ändras regelbundet. Azure hanterar adresserna i routningstabellen automatiskt när adresserna ändras. Läs mer om [tjänstslutpunkter för virtuellt nätverk](virtual-network-service-endpoints-overview.md) och för vilka tjänster du kan skapa tjänstslutpunkter.<br>

    > [!NOTE]
    > De nästa hopptyperna **VNet-peering** och **VirtualNetworkServiceEndpoint** läggs endast till i undernäts routningstabeller i virtuella nätverk som skapats via distributionsmodellen Azure Resource Manager. Nästa hopptyper läggs inte till i routningstabeller som är associerade till undernät i virtuella nätverk som har skapats via den klassiska distributionsmodellen. Läs mer om [Azure-distributionsmodeller.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="custom-routes"></a>Anpassade vägar

Du skapar anpassade vägar genom att antingen skapa [användardefinierade](#user-defined) vägar, eller genom att utbyta vägar eller utbyta [border gateway protocol-vägar](#border-gateway-protocol) (BGP) mellan din lokala nätverksgateway och en virtuell nätverksgateway i Azure. 

### <a name="user-defined"></a>Användardefinierade

Du kan skapa anpassade eller användardefinierade (statiska) vägar i Azure för att åsidosätta Azures standardsystemvägar eller lägga till ytterligare vägar i ett undernäts vägtabell. I Azure skapar du en routningstabell och associerar sedan routningstabellen till noll eller flera undernät för virtuella nätverk. Varje undernät kan ha noll eller en associerad routningstabell. Läs om det maximala antalet vägar du kan lägga till i en routningstabell och hur många användardefinierade routningstabeller du kan skapa per Azure-prenumeration i [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Om du skapar en routningstabell och associerar den till ett undernät kombineras dess vägar med, eller åsidosätter, de standardvägar som Azure lägger till i ett undernät som standard.

Du kan ange följande nästa hopptyper när du skapar en användardefinierad väg:

* **Virtuell installation**: En virtuell installation är en virtuell dator som vanligtvis kör ett nätverksprogram som en brandvägg. Mer information om olika förkonfigurerade virtuella nätverksinstallationer du kan distribuera i ett virtuellt nätverk finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). När du skapar en väg med hopptypen **virtuell installation** anger du även en nästa hopp-IP-adress. IP-adressen kan vara:

    * Ett nätverksgränssnitts [privata IP-adress](./private-ip-addresses.md) som är kopplad till en virtuell dator. Ett nätverksgränssnitt som är kopplat till en virtuell dator som vidarebefordrar nätverkstrafik till en annan adress än sin egen måste ha Azure-alternativet *Enable IP forwarding* (Aktivera IP-vidarebefordring) aktiverat. Inställningen inaktiverar Azures kontroll av källa och mål för ett nätverksgränssnitt. Läs mer om att [aktivera IP-vidarekoppling för ett nätverksgränssnitt](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Även om *Enable IP forwarding* (Aktivera IP-vidarebefordring) är en Azure-inställning kanske du också måste aktivera IP-vidarebefordring i den virtuella datorns operativsystem för att vidarebefordra trafik mellan privata IP-adresser som är tilldelade till Azure-nätverksgränssnitt. Om apparaten måste dirigera trafik till en offentlig IP-adress måste den antingen vidarebefordra trafiken eller nätadressöversätta den privata IP-adressen för källans privata IP-adress till sin egen privata IP-adress, som Azure sedan nätadressöversätter till en offentlig IP-adress innan trafiken skickas till Internet. Information om att fastställa nödvändiga inställningar på den virtuella datorn finns i dokumentationen för operativsystemet eller nätverksprogrammet. Läs mer om utgående anslutningar i Azure i [Förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>

        > [!NOTE]
        > Distribuera en virtuell installation till ett annat undernät än det resurserna som dirigeras genom den virtuella installationen har distribuerats i. Om du distribuerar den virtuella installationen till samma undernät och sedan applicerar en routningstabell till undernätet som dirigerar trafik via den virtuella installationen kan det leda till vägloopar, så att trafiken aldrig lämnar undernätet.

    * Den privata IP-adressen till en [intern lastbalanserare](../load-balancer/quickstart-load-balancer-standard-internal-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure. En lastbalanserare används ofta som en del av en [strategi med hög tillgänglighet för virtuella nätverksinstallationer](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).

    Du kan definiera en väg med 0.0.0.0/0 som adressprefix och en nästa hopptyp som virtuell installation. Det gör att enheten kan inspektera trafiken och avgöra om den ska vidarebefordras eller tas bort. Om du vill skapa en användardefinierad väg som innehåller adressprefixet 0.0.0.0/0 läser du först [adressprefixet 0.0.0.0/0](#default-route).

* **Virtuell nätverksgateway**: Ange när du vill att trafik till specifika adressprefix dirigeras till en virtuell nätverksgateway. Den virtuella nätverksgatewayen måste skapas med typen **VPN**. Du kan inte ange en virtuell nätverksgateway som har skapats som typen **ExpressRoute** i en användardefinierad väg. Med ExpressRoute måste du använda BGP för anpassade vägar. Du kan definiera en väg som dirigerar trafik som är avsedd för adressprefixet 0.0.0.0/0 till en [vägbaserad](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#vpntype) virtuell nätverksgateway. Lokalt kan du ha en enhet som inspekterar trafiken och avgör om den ska vidarebefordras eller tas bort. Om du vill skapa en användardefinierad väg för adressprefixet 0.0.0.0/0 läser du först [adressprefixet 0.0.0.0/0](#default-route). I stället för att konfigurera en användardefinierad väg för adressprefixet 0.0.0.0/0 kan du annonsera en väg med prefixet 0.0.0.0/0 prefix via BGP, om du har [aktiverat BGP för en virtuell VPN-nätverksgateway](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).<br>
* **None** (Ingen): Ange när du vill ta bort trafik till ett adressprefix, istället för att vidarebefordra trafiken till en destination. Om du inte har konfigurerat en funktion fullständigt kan Azure ange *None* (Ingen) för vissa av de valfria systemvägarna. Om du till exempel ser att det står *None* (Ingen) som **IP-adress till nästa hopp** och **Nästa hopptyp** är *Virtuell nätverksgateway* eller *Virtuell installation* kan det bero på att enheten inte körs eller inte är fullständigt konfigurerad. Azure skapar [systemstandardvägar](#default) för reserverade adressprefix med **None** (Ingen) som nästa hopptyp.<br>
* **Virtuellt nätverk**: Ange när du vill åsidosätta standardroutning inom ett virtuellt nätverk. I [Exempel på dirigering](#routing-example) finns ett exempel på varför du kan skapa en väg med hopptypen **Virtuellt nätverk**.<br>
* **Internet**: Ange när du uttryckligen vill dirigera trafik till ett adressprefix till Internet eller om du vill att trafik till Azure-tjänster med offentliga IP-adresser behålls i Azure-stamnätverket.

Du kan inte ange **VNet-peering** eller **VirtualNetworkServiceEndpoint** son nästa hopptyp i användardefinierade vägar. Vägar med nästa hopptyperna **VNet-peering** eller **VirtualNetworkServiceEndpoint** skapas endast av Azure när du konfigurerar en virtuell nätverkspeering eller en tjänstslutpunkt.

### <a name="service-tags-for-user-defined-routes-preview"></a>Tjänsttaggar för användardefinierade vägar (förhandsversion)

Nu kan du ange en [tjänsttagg som](service-tags-overview.md) adressprefix för en användardefinierad väg i stället för ett explicit IP-intervall. En tjänsttagg representerar en grupp med IP-adressprefix från en viss Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras, vilket minimerar komplexiteten i frekventa uppdateringar av användardefinierade vägar och minskar antalet vägar som du behöver skapa. Du kan för närvarande skapa 25 eller färre vägar med tjänsttaggar i varje vägtabell. </br>

> [!IMPORTANT]
> Tjänsttaggar för användardefinierade vägar finns för närvarande i förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

#### <a name="exact-match"></a>Exakt matchning
När det finns en exakt prefixmatchning mellan en väg med ett explicit IP-prefix och en väg med en tjänsttagg, ges prioritet till vägen med det explicita prefixet. När flera vägar med tjänsttaggar har matchande IP-prefix utvärderas vägarna i följande ordning: 

   1. Regionala taggar (t.ex. Storage.EastUS, AppService.AustraliaCentral)
   2. Toppnivåtaggar (t.ex. Storage, AppService)
   3. Regionala taggar för AzureCloud (t.ex. AzureCloud.canadacentral, AzureCloud.eastasia)
   4. AzureCloud-taggen </br></br>

Om du vill använda den här funktionen anger du ett tjänsttaggnamn för adressprefixparametern i vägtabellkommandon. I Powershell kan du till exempel skapa en ny väg för att dirigera trafik som skickas till ett IP-Azure Storage-prefix till en virtuell installation med hjälp av: </br></br>

```azurepowershell-interactive
New-AzRouteConfig -Name "StorageRoute" -AddressPrefix "Storage" -NextHopType "VirtualAppliance" -NextHopIpAddress "10.0.100.4"
```

Samma kommando för CLI blir: </br>

```azurecli-interactive
az network route-table route create -g MyResourceGroup --route-table-name MyRouteTable -n StorageRoute --address-prefix Storage --next-hop-type VirtualAppliance --next-hop-ip-address 10.0.100.4
```
</br>

#### <a name="known-issues-april-2021"></a>Kända problem (april 2021)

När det finns BGP-vägar eller om en tjänstslutpunkt har konfigurerats i undernätet kan det hända att vägarna inte utvärderas med rätt prioritet. En korrigering för dessa scenarier pågår för närvarande </br>


> [!NOTE] 
> I den offentliga förhandsversionen finns det flera begränsningar. Funktionen stöds inte för närvarande i Azure Portal och är endast tillgänglig via Powershell och CLI. Det finns inget stöd för användning med containrar. 

## <a name="next-hop-types-across-azure-tools"></a>Nästa hopptyper i Azure-verktyg

Namnet som visas och refereras för nästa hopptyper är olika för Azure-portalen och kommandoradsverktyg och Azure Resource Manager och klassiska distributionsmodeller. I följande tabell visas de namn som används för att referera till varje nästa hopptyp med olika verktyg och [distributionsmodeller](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json):

|Nästa hopptyp                   |Azure CLI och PowerShell (Resource Manager) |Azures klassiska CLI och PowerShell (klassisk)|
|-------------                   |---------                                       |-----|
|Virtuell nätverksgateway         |VirtualNetworkGateway                           |VPNGateway|
|Virtuellt nätverk                 |VNetLocal                                       |VNETLocal (inte tillgängligt i klassiska CLI i asm-läge)|
|Internet                        |Internet                                        |Internet (inte tillgängligt i klassiska CLI i asm-läge)|
|Virtuell installation               |VirtualAppliance                                |VirtualAppliance|
|Inga                            |Inga                                            |Null (inte tillgängligt i klassiska CLI i asm-läge)|
|Virtuell nätverkspeering         |VNet-peering                                    |Inte tillämpligt|
|Tjänstslutpunkt för virtuellt nätverk|VirtualNetworkServiceEndpoint                   |Inte tillämpligt|



### <a name="border-gateway-protocol"></a>BGP (Border Gateway Protocol)

En lokal nätverksgateway kan utbyta vägar med en virtuell nätverksgateway i Azure med BGP (Border Gateway Protocol). Användningen av BGP med en virtuell nätverksgateway i Azure beror på den typ du valde när du skapade gatewayen. Om den typ du valt var:

* **ExpressRoute:** Du måste använda BGP för att annonsera lokala vägar till Microsoft Edge router. Du kan inte skapa användardefinierade vägar för att tvinga trafik till den virtuella ExpressRoute-nätverksgatewayen om du distribuerar en virtuell nätverksgateway som distribueras som typen: ExpressRoute. Du kan använda användardefinierade vägar för att tvinga trafik från Express Route till exempelvis en virtuell nätverksinstallation.<br>
* **VPN**: Du kan eventuellt använda BGP. Mer information finns i [BGP with site-to-site VPN connections](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (BGP med plats-till-plats-VPN-anslutningar).

När du skickar vägar till Azure med hjälp av BGP läggs en separat väg till i routningstabellen för alla undernät i ett virtuellt nätverk för varje annonserat prefix. Vägen läggs till med *Virtuell nätverksgateway* angiven som källa och nästa hopptyp. 

ER och VPN Gateway kan inaktiveras i ett undernät med hjälp av en egenskap i en vägtabell. När du utbyter vägar med Azure med hjälp av BGP läggs inte vägar till i vägtabellen för alla undernät med vägspridning för virtuell nätverksgateway inaktiverad. Anslutningar via VPN uppnås med hjälp av [anpassade vägar](#custom-routes) där nästa hopp har typen *Virtual network gateway* (gateway för virtuellt nätverk). **Vägspridning ska inte inaktiveras på GatewaySubnet. Gatewayen fungerar inte med den här inställningen inaktiverad.** Mer information finns i [Så här inaktiverar du spridning av vägspridning för virtuell nätverksgateway.](manage-route-table.md#create-a-route-table)

## <a name="how-azure-selects-a-route"></a>Hur Azure väljer en väg

När utgående trafik skickas från ett undernät väljer Azure en väg baserat på mål-IP-adressen med matchningsalgoritmen med det längsta prefixet. Exempel: En routningstabell har två vägar: En väg anger adressprefixet 10.0.0.0/24 och den andra vägen anger adressprefixet 10.0.0.0/16. Azure dirigerar trafik som är avsedd för 10.0.0.5 till nästa hopptyp som är angiven i vägen med adressprefixet 10.0.0.0/24. Det beror på att prefixet 10.0.0.0/24 är längre än 10.0.0.0/16, trots att 10.0.0.5 är inom båda adressprefixen. Azure dirigerar trafik som är avsedd för 10.0.1.5 till nästa hopptyp som är angiven i vägen med adressprefixet 10.0.0.0/16. Det beror på att 10.0.1.5 inte ingår i adressprefixet 10.0.0.0/24, och därför är vägen med adressprefixet 10.0.0.0/16 det längsta prefix som matchar.

Om flera vägar innehåller samma adressprefix väljer Azure vägtyp utifrån följande prioritet:

1. Användardefinierad väg
1. BGP-väg
1. Systemväg

> [!NOTE]
> Systemvägar för trafik som är relaterad till virtuella nätverk, peerkopplingar mellan virtuella nätverk eller tjänstslutpunkter för virtuellt nätverk föredras, även om BGP-vägar är mer specifika.

Till exempel innehåller en routningstabell följande vägar:


|Källa   |Adressprefix  |Nästa hopptyp           |
|---------|---------         |-------                 |
|Standard  | 0.0.0.0/0        |Internet                |
|Användare     | 0.0.0.0/0        |Virtuell nätverksgateway |

När trafik är avsedd för en IP-adress utanför adressprefixen till andra vägar i routningstabellen väljer Azure vägen med källan **Användare** eftersom användardefinierade vägar har högre prioritet än systemstandardvägar.

I [Exempel på dirigering](#routing-example) finns en omfattande routningstabell med förklaringar av vägarna i tabellen.

## <a name="00000-address-prefix"></a><a name="default-route"></a>0.0.0.0/0 adressprefix

En väg med adressprefixet 0.0.0.0/0 instruerar Azure hur trafik ska dirigeras som är avsedd för en IP-adress som inte ligger inom någon annan vägs adressprefix i ett undernäts routningstabell. När ett undernät skapas, skapar Azure en [standardväg](#default) till adressprefixet 0.0.0.0/0 med **Internet** som nästa hopptyp. Om du inte åsidosätter den här vägen dirigerar Azure all trafik som är avsedd för IP-adresser som inte ingår i adressprefixet för någon annan väg till Internet. Undantaget är att trafik till Azure-tjänsternas offentliga IP-adresser stannar kvar i Azure-stamnätet och dirigeras inte till Internet. Om du åsidosätter den här vägen med en [anpassad](#custom-routes) väg skickas trafik till adresser utanför adressprefixen för någon annan väg i routningstabellen till ett nätverks virtuella installation eller virtuella nätverksgateway. Vilket det blir beror på det du anger i en anpassad väg.

När du åsidosätter adressprefixet 0.0.0.0/0 sker följande ändringar med Azures standardroutning (utöver utgående trafik från undernätet som flödar genom den virtuella nätverksgatewayen eller den virtuella installationen): 

* Azure skickar all trafik till nästa hopptyp som anges i vägen, inklusive trafik till offentliga IP-adresser för Azure-tjänster. När nästa hopptyp för vägen med adressprefixet 0.0.0.0/0 är **Internet** lämnar aldrig trafik som är avsedd för Azure-tjänsternas offentliga IP-adresser Azures stamnätverk, oavsett vilken Azure-region det virtuella nätverket eller Azure-tjänsten finns i. När du skapar en användardefinierad väg eller BGP-väg med en **virtuell nätverksgateway** eller en **virtuell installation** som nästa hopptyp skickas dock all trafik, inklusive trafik som skickats till offentliga IP-adresser till Azure-tjänster du inte har aktiverat [tjänstslutpunkter](virtual-network-service-endpoints-overview.md) för, till nästa hopptyp som är angiven i vägen. Om du har aktiverat en tjänstslutpunkt för en tjänst kan inte trafik till den tjänsten dirigeras till nästa hopptyp i en väg med adressprefixet 0.0.0.0/0. Det beror på att tjänstens adressprefix anges i vägen som skapas av Azure när du aktiverar tjänstslutpunkten, och adressprefixen för tjänsten är längre än 0.0.0.0/0.
* Du inte längre komma åt resurser på undernätet direkt från Internet. Du kan indirekt få tillgång till resurser i undernätet från Internet, om inkommande trafik passerar genom enheten som anges av nästa hopptyp för en väg med adressprefixet 0.0.0.0/0 innan trafiken når resursen i det virtuella nätverket. Om vägen innehåller följande värden för nästa hopptyp:<br>

    * **Virtuell installation**: Enheten måste:<br>

        * Vara tillgänglig från Internet<br>
        * Ha en offentlig IP-adress tilldelad till sig<br>
        * Inte ha en regel för nätverkssäkerhetsgrupper kopplad till sig som förhindrar kommunikation till enheten<br>
        * Inte neka kommunikationen<br>
        * Kunna nätadressöversätta och vidarebefordra eller skicka trafik via proxy till målresursen i undernätet och leda trafiken tillbaka till Internet.

    * **Virtuell nätverksgateway**: Om gatewayen är en virtuell ExpressRoute-nätverksgateway kan en Internet-ansluten enhet lokalt nätadressöversätta och vidarebefordra eller skicka trafik via proxy till målresursen i undernätet via ExpressRoutes [privata peering](../expressroute/expressroute-circuit-peerings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#privatepeering). 

Om ditt virtuella nätverk är anslutet till en Azure VPN-gateway, associerar du inte en routingtabell till det [gatewayundernät](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) som innehåller en väg med målet 0.0.0.0/0. Om du gör det så förhindrar du gatewayen från att fungera korrekt. Mer information finns i frågan som rör *varför vissa portar är öppna på en VPN-gateway* i [vanliga frågor och svar om VPN Gateway](../vpn-gateway/vpn-gateway-vpn-faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gatewayports).

Se [DMZ mellan Azure och ditt lokala datacenter för](/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid?toc=%2fazure%2fvirtual-network%2ftoc.json) implementeringsinformation när du använder virtuella nätverksgatewayer mellan Internet och Azure.

## <a name="routing-example"></a>Exempel på dirigering

För att illustrera begreppen i den här artikeln beskrivs i avsnitten som följer:

* Ett scenario, med krav<br>
* De nödvändiga anpassade vägarna för att uppfylla kraven<br>
* Routningstabellen som finns för ett undernät som innehåller standardvägar och anpassade vägar som behövs för att uppfylla kraven

> [!NOTE]
> Det här exemplet är inte avsett att bli en rekommenderad eller bästa praxis-implementation. Det visas endast för att illustrera begreppen i den här artikeln.

### <a name="requirements"></a>Krav

1. Implementera två virtuella nätverk i samma Azure-region och aktivera resurser för att kommunicera mellan virtuella nätverk.
1. Aktivera ett lokalt nätverk för att kommunicera säkert med de båda virtuella nätverken via en VPN-tunnel över Internet. *Det går också att använda en ExpressRoute-anslutning, men i det här exemplet används en VPN-anslutning.*
1. För ett undernät i ett virtuellt nätverk:

    * Tvinga all utgående trafik från undernätet förutom till Azure Storage och inom undernätet, så att den flödar genom ett nätverks virtuella installation, för granskning och loggning.<br>
    * Granska inte trafik mellan privata IP-adresser i undernätet, och tillåt att trafik flödar direkt mellan alla resurser.<br>
    * Ta bort all utgående trafik som var avsedd för det virtuella nätverket.<br>
    * Gör det möjligt för utgående trafik till Azure Storage att flöda direkt till Storage, utan att tvinga den genom ett nätverks virtuella installation.

1. Tillåt all trafik mellan alla andra undernät och virtuella nätverk.

### <a name="implementation"></a>Implementering

Följande bild visar en implementering via Azure Resource Manager-distributionsmodellen som uppfyller föregående krav:

![Diagram över nätverk](./media/virtual-networks-udr-overview/routing-example.png)

Pilarna visar trafikflödet. 

### <a name="route-tables"></a>Routningstabeller

#### <a name="subnet1"></a>Subnet1

Routningstabellen för *Subnet1* på bilden innehåller följande vägar:

|ID  |Källa |Tillstånd  |Adressprefix    |Nästa hopptyp          |Nästa hopp-IP-adress|Namn på användardefinierad väg| 
|----|-------|-------|------              |-------                |--------           |--------      |
|1   |Standardvärde|Ogiltig|10.0.0.0/16         |Virtuellt nätverk        |                   |              |
|2   |Användare   |Aktiv |10.0.0.0/16         |Virtuell installation      |10.0.100.4         |Inom-VNet1  |
|3   |Användare   |Aktiv |10.0.0.0/24         |Virtuellt nätverk        |                   |Inom-Subnet1|
|4   |Standardvärde|Ogiltig|10.1.0.0/16         |VNet-peering           |                   |              |
|5   |Standardvärde|Ogiltig|10.2.0.0/16         |VNet-peering           |                   |              |
|6   |Användare   |Aktiv |10.1.0.0/16         |Inget                   |                   |ToVNet2-1-Drop|
|7   |Användare   |Aktiv |10.2.0.0/16         |Inget                   |                   |ToVNet2-2-Drop|
|8   |Standardvärde|Ogiltig|10.10.0.0/16        |Virtuell nätverksgateway|[X.X.X.X]          |              |
|9   |Användare   |Aktiv |10.10.0.0/16        |Virtuell installation      |10.0.100.4         |Till lokalt    |
|10  |Standard|Aktiv |[X.X.X.X]           |VirtualNetworkServiceEndpoint    |         |              |
|11  |Standardvärde|Ogiltig|0.0.0.0/0           |Internet               |                   |              |
|12  |Användare   |Aktiv |0.0.0.0/0           |Virtuell installation      |10.0.100.4         |Standard-NVA   |

En förklaring av varje väg-ID följer:

1. Azure la automatiskt till den här vägen för alla undernät i *Virtual-network-1*, eftersom 10.0.0.0/16 är det enda adressintervall som definierats i adressutrymmet för det virtuella nätverket. Om den användardefinierade vägen i väg ID2 inte hade skapats skulle trafik som skickades till en adress mellan 10.0.0.1 och 10.0.255.254 dirigeras inom det virtuella nätverket, eftersom prefixet är längre än 0.0.0.0/0, och inte inom de andra vägarnas adressprefix. Azure ändrade automatiskt tillståndet från *Aktiv* till *Ogiltig*, när ID2, en användardefinierad väg, lades till, eftersom den har samma prefix som standardvägen och användardefinierade vägar åsidosätter standardvägar. Vägens tillstånd är fortfarande *Aktiv* för *Subnet2*, eftersom routningstabellen som den användardefinierade vägen ID2 finns i inte är kopplad till *Subnet2*.
2. Den här vägen lades till i Azure när en användardefinierad väg för adressprefixet 10.0.0.0/16 associerades till undernätet *Subnet1* i det virtuella nätverket *Virtual-network-1*. Den användardefinierade vägen anger 10.0.100.4 som IP-adress för den virtuella installationen, eftersom adressen är den privata IP-adress som tilldelats den virtuella installationens virtuella dator. Vägtabellen som den här vägen finns i var inte associerad till *Subnet2*, så den visas inte i routningstabellen för *Subnet2*. Den här vägen åsidosätter standardvägen för prefixet 10.0.0.0/16 (ID1), som automatiskt dirigerade trafik adresserad till 10.0.0.1 och 10.0.255.254 i det virtuella nätverket via det virtuella nätverkets nästa hopptyp. Den här vägen finns för att uppfylla [krav](#requirements) 3 för att tvinga all utgående trafik via en virtuell installation.
3. Den här vägen lades till i Azure när en användardefinierad väg för adressprefixet 10.0.0.0/24 associerades till undernätet *Subnet1*. Trafik till adresser mellan 10.0.0.1 och 10.0.0.254 stannar kvar i undernätet, istället för att dirigeras till den virtuella installation som anges i den föregående regeln (ID2), eftersom det har ett längre prefix än ID2-vägen. Den här vägen var inte associerad till *Subnet2*, så vägen visas inte i routningstabellen för *Subnet2*. Den här vägen åsidosätter effektivt ID2-vägen för trafik i *Subnet1*. Den här vägen finns för att uppfylla [krav](#requirements) 3.
4. Azure la automatiskt till den här vägarna i IDs 4 och 5 för alla undernät i *Virtual-network-1* när det virtuella nätverket var peerkopplat med *Virtual-network-2.* *Virtual-network-2* har två adressintervall i sitt adressutrymme: 10.1.0.0/16 och 10.2.0.0/16, så Azure la till en väg för varje intervall. Om de användardefinierade vägarna i väg iDs 6 och 7 inte hade skapats skulle trafik som skickades till en adress mellan 10.1.0.1-10.1.255.254 och 10.2.0.1-10.2.255.254 dirigeras inom det peer-kopplade virtuella nätverket, eftersom prefixet är längre än 0.0.0.0/0, och inte inom de andra vägarnas adressprefix. Azure ändrade automatiskt tillståndet från *Aktiv* till *Ogiltig* när vägarna i IDs 6 och 7 lades till, eftersom de har samma prefix som vägarna i IDs 4 och 5, och användardefinierade vägar åsidosätter standardvägar. Tillståndet för vägarna i IDs 4 och  5 är fortfarande Aktiv för *Subnet2*, eftersom den vägtabell som de användardefinierade vägarna i IDs 6 och 7 finns i inte är associerad med *Subnet2*. En virtuell nätverkspeering har skapats för att uppfylla [krav](#requirements) 1.
5. Samma förklaring som ID4.
6. Azure la till den här vägen och vägen i ID7, när användardefinierade vägar för adressprefixen 10.1.0.0/16 och 10.2.0.0/16 kopplades till undernätet *Subnet1*. Trafik till adresser mellan 10.1.0.1-10.1.255.254 och 10.2.0.1-10.2.255.254 tas bort av Azure, istället för att den dirigeras till det peer-kopplade virtuella nätverket, eftersom användardefinierade vägar åsidosätter standardvägar. De här vägarna var inte associerade till *Subnet2*, så de visas inte i routningstabellen för *Subnet2*. Vägarna åsidosätter vägarna ID4 och ID5 för trafik som lämnar *Subnet1*. Vägarna ID6 och ID7 finns för att uppfylla [krav](#requirements) 3 för att ta bort trafik som är avsedd för det andra virtuella nätverket.
7. Samma förklaring som ID6.
8. Azure la automatiskt till den här vägarna för alla undernät i *Virtual-network-1* när ett virtuellt nätverket av VPN-typ skapades inom det virtuella nätverket. Azure la till den offentliga IP-adressen till den virtuella nätverksgatewayen till routningstabellen. Trafik som skickas till alla adresser mellan 10.10.0.1 och 10.10.255.254 dirigeras till den virtuella nätverksgatewayen. Prefixet är längre än 0.0.0.0/0 och är inte inom adressprefixet för någon av de andra vägarna. En virtuell nätverksgateway har skapats för att uppfylla [krav](#requirements) 2.
9. Den här vägen lades till i Azure när en användardefinierad väg för adressprefixet 10.10.0.0/16 lades till i routningstabellen som är kopplad till *Subnet1*. Den här vägen åsidosätter ID8. Vägen skickar all trafik till lokala nätverk till en NVA för granskning, istället för att dirigera trafiken lokalt direkt. Den här vägen har skapats för att uppfylla [krav](#requirements) 3.
10. Azure la automatiskt till den här vägen i undernätet när en tjänstslutpunkt till en Azure-tjänst aktiverades för undernätet. Azure dirigerar trafik från undernätet till en offentlig IP-adress för tjänsten via nätverket i Azure-infrastrukturen. Prefixet är längre än 0.0.0.0/0 och är inte inom adressprefixet för någon av de andra vägarna. En tjänstslutpunkt skapades för att uppfylla [krav](#requirements) 3, för att göra det möjligt för trafik avsedd för Azure Storage att flöda direkt till Azure Storage.
11. Azure la automatiskt till den här vägen i routningstabellen för alla undernät inom *Virtual-network-1* och *Virtual-network-2.* Adressprefixet 0.0.0.0/0 är kortast. All trafik som skickas till adresser inom ett längre adressprefix dirigeras baserat på andra vägar. Som standard dirigerar Azure all trafik som är avsedd för adresser utöver de adresser som angavs i någon av de andra vägarna till Internet. Azure ändrade automatiskt tillståndet från *Aktiv* till *Ogiltig* för undernätet *Subnet1* när en användardefinierad väg för adressprefixet 0.0.0.0/0 (ID12) kopplades till undernätet. Tillståndet för den här vägen är fortfarande *Aktiv* för alla andra undernät i de båda virtuella nätverken, eftersom vägen inte är kopplad till några andra undernät i några andra virtuella nätverk.
12. Den här vägen lades till i Azure när en användardefinierad väg för adressprefixet 0.0.0.0/0 associerades till undernätet *Subnet1*. Den användardefinierade vägen anger 10.0.100.4 som IP-adress för den virtuella installationen. Den här vägen var inte associerad till *Subnet2*, så vägen visas inte i routningstabellen för *Subnet2*. All trafik för adresser som inte ingår i adressprefixen för någon av de andra vägarna skickas till den virtuella installationen. Tillägget av den här vägen ändrade tillståndet för standardvägen för adressprefixet 0.0.0.0/0 (ID11) från *Aktiv* till *Ogiltig* för *Subnet1*, eftersom en användardefinierad väg åsidosätter en standardväg. Den här vägen finns för att uppfylla det tredje [kravet](#requirements).

#### <a name="subnet2"></a>Subnet2

Routningstabellen för *Subnet2* på bilden innehåller följande vägar:

|Källa  |Tillstånd  |Adressprefix    |Nästa hopptyp             |Nästa hopp-IP-adress|
|------- |-------|------              |-------                   |--------           
|Standard |Aktiv |10.0.0.0/16         |Virtuellt nätverk           |                   |
|Standard |Aktiv |10.1.0.0/16         |VNet-peering              |                   |
|Standard |Aktiv |10.2.0.0/16         |VNet-peering              |                   |
|Standard |Aktiv |10.10.0.0/16        |Virtuell nätverksgateway   |[X.X.X.X]          |
|Standard |Aktiv |0.0.0.0/0           |Internet                  |                   |
|Standard |Aktiv |10.0.0.0/8          |Inget                      |                   |
|Standard |Aktiv |100.64.0.0/10       |Inget                      |                   |
|Standard |Aktiv |192.168.0.0/16      |Inget                      |                   |

Routningstabellen för *Subnet2* innehåller alla Azure-skapade standardvägar och den valfria VNet-peeringen och de valfria vägarna för virtuell nätverksgateway. Azure la till de valfria vägarna till alla undernät i det virtuella nätverket när gatewayen och peeringen lades till i det virtuella nätverket. Azure tog bort vägarna för adressprefixen 10.0.0.0/8, 192.168.0.0/16 och 100.64.0.0/10 från *subnet1-vägtabellen* när den användardefinierade vägen för adressprefixet 0.0.0.0/0 lades till i *Subnet1*.  

## <a name="next-steps"></a>Nästa steg

* [Skapa en användardefinierad routningstabellen med vägar och en virtuell nätverksenhet](tutorial-create-route-table-portal.md)<br>
* [Konfigurera BGP för Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
* [Använda BGP med ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#route-aggregation-and-prefix-limits)<br>
* [Visa alla vägar för ett undernät](diagnose-network-routing-problem.md). En användardefinierad routningstabell visar bara de användardefinierade vägarna och inte standardvägarna och BGP-vägarna för ett undernät. Om du visar alla vägar ser du standardvägarna, GBP- och de användardefinierade vägarna för undernätet som ett nätverksgränssnitt finns i.<br>
* [Bestäm nästa hopptyp](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mellan en virtuell dator och en mål-IP-adress. Nästa hopp-funktionen Azure Network Watcher gör att du kan bestämma om trafik som lämnar ett undernät dirigeras dit du önskar.
