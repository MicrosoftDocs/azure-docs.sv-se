---
title: Vanliga frågor och svar om Azure VPN Gateway
description: Vanliga frågor och svar om VPN Gateway. Vanliga frågor och svar om Microsoft Azure Virtual Network-anslutningar på flera platser, konfiguration av hybridanslutningar och VPN Gateway.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: yushwang
ms.openlocfilehash: 3d29e99f3b539fdbea2a19df7ffc25d4e41a5376
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731266"
---
# <a name="vpn-gateway-faq"></a>Vanliga frågor och svar om VPN Gateway

## <a name="connecting-to-virtual-networks"></a><a name="connecting"></a>Ansluta till virtuella nätverk

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>Kan jag ansluta till virtuella nätverk i olika Azure-regioner?

Ja. Faktum är att det inte finns någon regionbegränsning. Ett virtuellt nätverk kan ansluta till ett annat virtuellt nätverk i samma region eller i en annan Azure-region.

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>Kan jag ansluta till virtuella nätverk i olika prenumerationer?

Ja.

### <a name="can-i-specify-private-dns-servers-in-my-vnet-when-configuring-vpn-gateway"></a>Kan jag ange privata DNS-servrar i mitt VNet när jag konfigurerar VPN Gateway?

Om du har angett en DNS-server eller-servrar när du skapade ditt VNet kommer VPN Gateway att använda de DNS-servrar som du har angett. Om du anger en DNS-server måste du kontrol lera att DNS-servern kan matcha de domän namn som krävs för Azure.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>Kan jag ansluta till flera platser från en enda virtuellt nätverk?

Du kan ansluta till flera platser med hjälp av Windows PowerShell och Azure REST-API:er. Se [Multisite- och VNet-till-VNet-anslutning](#V2VMulti), avsnittet Vanliga frågor och svar.

### <a name="is-there-an-additional-cost-for-setting-up-a-vpn-gateway-as-active-active"></a>Finns det ytterligare kostnader för att konfigurera en VPN-gateway som aktiv-aktiv?

Nej.

### <a name="what-are-my-cross-premises-connection-options"></a>Vilka alternativ finns det för min anslutning till flera platser?

Följande anslutningar mellan flera platser stöds:

* Plats-till-plats – VPN-anslutning via IPsec (IKE v1 och IKE v2). Den här typen av anslutning kräver en VPN-enhet eller RRAS. Mer information finns i [Plats-till-plats](./tutorial-site-to-site-portal.md).
* Punkt-till-plats – VPN-anslutning över SSTP (Secure Socket Tunneling Protocol) eller IKE v2. Den här anslutningen kräver inte någon VPN-enhet. Mer information finns i [Punkt-till-plats](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* VNet-till-VNet – Den här typen av anslutning är samma som en plats-till-plats-konfiguration. VNet-till-VNet är en VPN-anslutning via IPsec (IKE v1 och IKE v2). Den kräver inte någon VPN-enhet. Mer information finns i [VNet-till-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Flera platser – Det här är en variant av en plats-till-plats-konfiguration som gör att du kan ansluta flera lokala platser till ett virtuellt nätverk. Mer information finns i [Flera platser](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute – ExpressRoute är en privat anslutning till Azure från ditt WAN, inte en VPN-anslutning via det offentliga Internet. Mer information finns i [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md) och [Vanliga frågor och svar om ExpressRoute](../expressroute/expressroute-faqs.md).

Mer information om VPN-gatewayanslutningar finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Vad är skillnaden mellan en plats-till-plats-anslutning och en punkt-till-plats-anslutning?

**Plats-till-plats**-konfigurationer (IPsec/IKE VPN-tunnel) sker mellan en lokal plats och Azure. Detta innebär att du kan ansluta mellan datorer i dina lokaler till valfri virtuell dator eller rollinstans i det virtuella nätverket, beroende på hur du väljer att konfigurera routning och behörigheter. Det är ett bra alternativ för en ständigt tillgänglig anslutning mellan platser och passar bra för Hybrid konfigurationer. Den här typen av anslutning bygger på en IPsec VPN-installation (maskinvara eller programinstallation), som måste distribueras i utkanten av nätverket. Om du vill skapa den här typen av anslutning måste du ha en extern IPv4-adress.

Med **punkt-till-plats**-konfigurationer (VPN över SSTP) kan du ansluta från en enda dator varifrån som helst till något som finns i ditt virtuella nätverk. Den använder Windows som ingår i VPN-klienten. Som en del av punkt-till-plats-konfigurationen kan du installera ett certifikat och ett VPN-klientkonfigurationspaket, som innehåller inställningar för att datorn ska kunna ansluta till en virtuell dator eller rollinstans i det virtuella nätverket. Detta är användbart om du vill ansluta till ett virtuellt nätverk som inte finns lokalt. Det är också ett bra alternativ om du inte har tillgång till VPN-maskinvara eller en extern IPv4-adress. Båda krävs för en plats-till-plats-anslutning.

Du kan konfigurera ditt virtuella nätverk till att använda både plats-till-plats och punkt-till-plats samtidigt, förutsatt att du skapar din plats-till-plats-anslutning med en routningsbaserad VPN-typ för din gateway. Routningsbaserade VPN-typer kallas för dynamiska gateways i den klassiska distributionsmodellen.

## <a name="virtual-network-gateways"></a><a name="gateways"></a>Virtuella nätverksgatewayer

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Är en VPN-gateway en virtuell nätverksgateway?

En VPN-gateway är en typ av virtuell nätverksgateway. En VPN-gateway skickar krypterad trafik mellan det virtuella nätverket och lokal plats via en offentlig anslutning. Du kan också använda en VPN-gateway för att skicka trafik mellan virtuella nätverk. När du skapar en VPN-gateway använder du värdet ”Vpn” för -GatewayType. Mer information finns i [Om konfigurationsinställningar för VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Vad är en principbaserad gateway (statisk routning)?

Principbaserade gateways implementerar principbaserade VPN:er. Principbaserade VPN:er krypterar och dirigerar paket via IPsec-tunnlar, baserat på kombinationerna av adressprefix mellan ditt lokala nätverk och Azure VNet. Principen (eller trafikväljaren) definieras vanligtvis som en åtkomstlista i VPN-konfigurationen.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Vad är en routningsbaserad gateway (dynamisk routning)?

Routningsbaserade gateways implementerar routningsbaserade VPN:er. Routningsbaserade VPN:er använder ”vägar” i IP-vidarebefordringen eller i routningstabellen för att dirigera paket till sina respektive tunnelgränssnitt. Tunnelgränssnitten krypterar eller dekrypterar sedan paketen in och ut från tunnlarna. Principen eller trafik väljare för vägbaserade VPN: er konfigureras som alla-till-alla (eller jokertecken).

### <a name="can-i-update-my-policy-based-vpn-gateway-to-route-based"></a>Kan jag uppdatera min principbaserade VPN-gateway till routing-based?

Nej. En gateway-typ kan inte ändras från principbaserad till Route-baserad eller från väg-till-principbaserad. Om du vill ändra en gateway-typ måste gatewayen tas bort och återskapas. Den här processen tar cirka 60 minuter. När du skapar den nya gatewayen kan du inte behålla den ursprungliga gatewayens IP-adress.

1. Ta bort alla anslutningar som är associerade med gatewayen.

1. Ta bort gatewayen med hjälp av någon av följande artiklar:

   * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
   * [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
   * [Azure PowerShell-klassisk](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
1. Skapa en ny Gateway med den gateway-typ som du vill använda och slutför sedan VPN-installationen. Anvisningar finns i [själv studie kursen för Site-to-Site](./tutorial-site-to-site-portal.md#VNetGateway).

### <a name="do-i-need-a-gatewaysubnet"></a>Behöver jag ett gatewayundernät?

Ja. Gatewayundernätet innehåller de IP-adresser som gatewaytjänsterna för virtuella nätverk använder. Om du vill konfigurera en virtuell nätverksgateway måste du först skapa ett gatewayundernät för det virtuella nätverket. Alla gatewayundernät måste ha namnet ”GatewaySubnet” för att fungera korrekt. Ge inte något annat namn till gateway-undernätet. Och distribuera inte virtuella datorer eller något annat till gateway-undernätet.

När du skapar gatewayundernätet anger du det antal IP-adresser som undernätet innehåller. IP-adresserna i gatewayundernätet allokeras till gatewaytjänsten. Vissa konfigurationer kräver att fler IP-adresser allokeras till gatewaytjänsterna än andra. Du måste kontrollera att gatewayundernätet innehåller tillräckligt med IP-adresser för att hantera framtida tillväxt och eventuella ytterligare nya anslutningskonfigurationer. Även om du kan skapa ett gatewayundernät som är så litet som /29, rekommenderar vi att du skapar ett gatewayundernät på /27 eller större (/27, /26, /25 osv.). Granska kraven för den konfiguration som du vill skapa och kontrollera att gatewayundernätet som du har kommer att uppfylla dessa krav.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>Kan jag distribuera virtuella datorer eller rollinstanser till mitt gateway-undernät?

Nej.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>Kan jag få min IP-adress för VPN-gatewayen innan jag skapar den?

Zoner-redundanta och zonindelade-gatewayer (Gateway-SKU: er som har _AZ_ i namnet) båda förlitar sig på en offentlig Azure-resurs med _standard-SKU_ . Azure standard SKU offentliga IP-resurser måste använda en statisk allokeringsmetod. Därför kommer du att ha den offentliga IP-adressen för din VPN-gateway så snart du skapar den offentliga standard-IP-resursen som du vill använda för den.

För icke-zoner-redundanta och icke-zonindelade gatewayer (Gateway-SKU: er som _inte_ har _AZ_ i namnet) kan du inte hämta IP-adressen för VPN-gatewayen innan den skapas. IP-adressen ändras endast om du tar bort och återskapar din VPN-gateway.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>Kan jag begära en statisk offentlig IP-adress för min VPN-gateway?

Som anges ovan förlitar sig zoner-redundanta och zonindelade-gatewayer (Gateway-SKU: er som har _AZ_ i namnet) både för en offentlig Azure-resurs med _standard-SKU_ . Azure standard SKU offentliga IP-resurser måste använda en statisk allokeringsmetod.

För icke-zoner-redundanta och icke-zonindelade gatewayer (Gateway-SKU: er som _inte_ har _AZ_ i namnet), stöds endast dynamisk IP-adresstilldelning. Detta betyder dock inte att IP-adressen ändras efter att den har tilldelats till din VPN-gateway. Den enda gången som VPN-gatewayens IP-adress ändras är när gatewayen tas bort och sedan skapas på nytt. VPN-gatewayens offentliga IP-adress ändras inte när du ändrar storlek på, återställer eller Slutför annat internt underhåll och uppgraderingar av din VPN-gateway.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Hur blir min VPN-tunnel autentiserad?

Azure VPN använder PSK-autentisering (I förväg delad nyckel). Vi kan generera en i förväg delad nyckel (PSK) när vi skapar VPN-tunneln. Du kan ändra det automatiskt genererade PSK till ditt eget med PowerShell-cmdleten Set i förväg delad nyckel eller REST API.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>Kan jag använda API:n Set Pre-Shared Key till att konfigurera min principbaserade gateway-VPN (statisk routning)?

Ja, både API:n och PowerShell-cmdleten Set Pre-Shared Key kan användas för att konfigurera Azures principbaserade (statiska) VPN:er och routningsbaserade (dynamisk) routning-VPN:er.

### <a name="can-i-use-other-authentication-options"></a>Kan jag använda andra autentiseringsalternativ?

Vi är begränsade till att använda PSK (I förväg delad nyckel) vid autentisering.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>Hur anger jag vilken trafik som ska passera VPN-gatewayen?

#### <a name="resource-manager-deployment-model"></a>Distributionsmodell med Resource Manager

* PowerShell: Använd ”AddressPrefix” för att ange trafik för den lokala nätverksgatewayen.
* Azure Portal: Gå till den lokala nätverksgatewayen > Konfiguration > Adressutrymme.

#### <a name="classic-deployment-model"></a>Klassisk distributionsmodell

* Azure Portal: Gå till det klassiska virtuella nätverket > VPN-anslutningar > Plats-till-plats-VPN-anslutningar > Namn på lokal plats > Lokal plats > Adressutrymme för klient.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Kan jag använda NAT-T på mina VPN-anslutningar?

Ja, NAT Traversal (NAT-T) stöds. Azure VPN Gateway kan inte utföra NAT-liknande funktioner på de inre paketen till/från IPsec-tunnlarna.  I den här konfigurationen kontrollerar du att den lokala enheten initierar IPSec-tunneln.

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>Kan jag installera min egen VPN-server i Azure och använda den för att ansluta till mitt lokala nätverk?

Ja, du kan distribuera egna VPN-gatewayer eller servrar i Azure, antingen från Azure Marketplace eller genom att skapa egna VPN-routrar. Du måste konfigurera användardefinierade vägar i det virtuella nätverket så att trafiken dirigeras korrekt mellan de lokala nätverken och de virtuella undernätverken.

### <a name="why-are-certain-ports-opened-on-my-virtual-network-gateway"></a><a name="gatewayports"></a>Varför öppnas vissa portar på min virtuella nätverksgateway?

De krävs för Azures infrastrukturkommunikation. De är skyddade (låsta) med Azure-certifikat. Utan rätt certifikat kommer externa entiteter, inklusive kunderna till dessa gateways, inte kunna orsaka någon effekt på dessa slutpunkter.

En virtuell nätverksgateway är i grunden en multihomed-enhet med ett nätverkskort som är igång i kundens privata nätverk och ett nätverkskort som är riktade mot det offentliga nätverket. Azure-infrastrukturens entiteter kan inte använda kundens privata nätverk av kompatibilitetsskäl, så de måste använda offentliga slutpunkter för infrastrukturkommunikationen. De offentliga slutpunkterna genomsöks regelbundet av Azures säkerhetsgranskning.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Mer information om gateway-typer, krav och dataflöde

Mer information finns i [Om konfigurationsinställningar för VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="site-to-site-connections-and-vpn-devices"></a><a name="s2s"></a>Plats-till-plats-anslutningar och VPN-enheter

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Vad bör jag tänka på när jag väljer en VPN-enhet?

Vi har verifierat en uppsättning VPN-standardenheter för plats-till-plats tillsammans med våra enhetsleverantörer. En lista med kända kompatibla VPN-enheter, deras konfigurationsanvisningar eller exempel, samt specifikationer för enheten finns i artikeln [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Alla enheter i enhetsfamiljerna som är kompatibla bör fungera tillsammans med Virtual Network. Om du behöver hjälp med att konfigurera din VPN-enhet kan du se enhetens konfigurationsexempel eller den länk som leder till lämplig enhetsfamilj.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Var hittar jag konfigurationsinställningar för VPN-enheter?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Hur redigerar jag konfigurationsexempel för VPN-enheter?

Mer information om att redigera enhetens konfigurationsexempel finns i [Redigera exempel](vpn-gateway-about-vpn-devices.md#editing).

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Var hittar jag IPsec- och IKE-parametrar?

Mer information om IPsec-/IKE-parametrar finns i [Parametrar](vpn-gateway-about-vpn-devices.md#ipsec).

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Varför stängs min principbaserade VPN-tunnel när trafiken är inaktiv?

Detta är ett förväntat beteende för principbaserade VPN-gatewayer (även kallat statisk routning). När trafiken i tunneln varit inaktiv i mer än 5 minuter kommer tunneln att stängas. När trafik börjar flöda i båda riktningarna, upprättas tunneln på nytt omedelbart.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>Kan jag använda programvaru-VPN:er för att ansluta till Azure?

Vi har stöd för Routning och fjärråtkomst (RRAS) i Windows Server 2012 för plats-till-plats-konfiguration mellan flera platser.

Andra VPN-programlösningar bör fungera med vår gateway så länge de uppfyller branschens standardimplementeringar för IPsec. Kontakta leverantören av programvaran för konfigurations- och supportinstruktioner.

## <a name="how-do-i-change-the-authentication-type-for-my-point-to-site-connections"></a>Hur gör jag för att ändra autentiseringstypen för mina punkt-till-plats-anslutningar?

Du kan ändra autentiseringsmetoden för punkt-till-plats-anslutningar genom att gå till avsnittet **punkt-till-plats-konfiguration** under VPN gateway och markera önskad alternativ knapp. De aktuella alternativen är **Azure-certifikat, RADIUS-autentisering och Azure Active Directory**. Observera att aktuella klienter **kanske inte kan ansluta** efter ändringen tills den nya profilen har laddats ned och kon figurer ATS på klienten.

## <a name="point-to-site-using-native-azure-certificate-authentication"></a><a name="P2S"></a>Punkt-till-plats använder ursprunglig Azure-certifikatautentisering

Det här avsnittet gäller distributionsmodellen i Resource Manager.

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="point-to-site-using-radius-authentication"></a><a name="P2SRADIUS"></a>Punkt-till-plats använder RADIUS-autentisering

Det här avsnittet gäller distributionsmodellen i Resource Manager.

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="vnet-to-vnet-and-multi-site-connections"></a><a name="V2VMulti"></a>Anslutning mellan virtuella nätverk och flera platser

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Hur gör jag för att aktivera routning mellan min plats-till-plats-VPN-anslutning och min ExpressRoute?

Om du vill aktivera routning mellan din gren som är ansluten till ExpressRoute och din gren är ansluten till en plats-till-plats-VPN-anslutning måste du konfigurera [Azure Route Server](../route-server/expressroute-vpn-support.md).

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>Kan jag använda Azures VPN-gateway till att överföra trafik mellan mina lokala platser eller till ett annat virtuellt nätverk?

**Distributionsmodell med Resource Manager**<br>
Ja. Mer information finns i avsnittet om [BGP](#bgp).

**Klassisk distributionsmodell**<br>
Överföringstrafik via Azures VPN-gateway är möjlig med den klassiska distributionsmodellen, men den förlitar sig på statiska definierade adressutrymmen i nätverkskonfigurationsfilen. BGP stöds inte ännu med virtuella Azure-nätverk och VPN-gatewayer som använder den klassiska distributionsmodellen. Utan BGP är manuellt definierade överföringsadressutrymmen mycket felbenägna och rekommenderas inte.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Genererar Azure samma i förväg delade IPsec/IKE-nyckel för alla mina VPN-anslutningar för samma virtuella nätverk?

Nej, Azure genererar som standard olika nycklar för olika VPN-anslutningar. Du kan dock använda REST-API:n eller PowerShell-cmdleten Set VPN Gateway Key för att ange det nyckelvärde som du föredrar. Nyckeln måste vara skrivbara ASCII-tecken.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Får jag mer bandbredd med fler plats-till-plats-VPN:er än med ett enda virtuellt nätverk?

Nej, alla VPN-tunnlar, inklusive punkt-till-plats-VPN:er, delar samma Azure VPN-gateway och tillgänglig bandbredd.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>Kan jag konfigurera flera tunnlar mellan mitt virtuella nätverk och min lokala plats med hjälp av multisite-VPN?

Ja, men du måste konfigurera BGP för båda tunnlarna till samma plats.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>Kan jag använda punkt-till-plats-VPN med mitt virtuella nätverk med flera VPN-tunnlar?

Ja, VPN:er för punkt-till-plats (P2S) kan användas med VPN-gateways som ansluter till flera lokala platser och andra virtuella nätverk.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>Kan jag ansluta ett virtuellt nätverk med IPsec-VPN:er till min ExpressRoute-krets?

Ja, det stöds. Mer information finns i [Konfigurera ExpressRoute och VPN-anslutningar för plats till plats som kan samexistera](../expressroute/expressroute-howto-coexist-classic.md)

## <a name="ipsecike-policy"></a><a name="ipsecike"></a>IPsec/IKE-princip

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]

## <a name="bgp-and-routing"></a><a name="bgp"></a>BGP och routning

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

### <a name="can-i-configure-forced-tunneling"></a>Kan jag konfigurera Tvingad tunnel trafik?

Ja. Se [Konfigurera tvingad tunneltrafik](vpn-gateway-about-forced-tunneling.md).

## <a name="cross-premises-connectivity-and-vms"></a><a name="vms"></a>Anslutning mellan platser och VM:ar

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Om min virtuella dator finns i ett virtuellt nätverk och jag har en anslutning mellan flera platser, hur ansluter jag då till den virtuella datorn?

Du har några alternativ att välja mellan. Om du har aktiverat RDP för din virtuella dator kan du ansluta till den genom att använda den privata IP-adressen. I detta fall anger du den privata IP-adressen och den port som du vill ansluta till (vanligtvis 3389). Du måste konfigurera porten på den virtuella datorn för trafiken.

Du kan också ansluta till den virtuella datorn med en privat IP-adress från en annan virtuell dator som finns i samma virtuella nätverk. Du kan inte använda RDP till den virtuella datorn med hjälp av den privata IP-adressen om du ansluter från en plats utanför ditt virtuella nätverk. Om du till exempel har konfigurerat ett virtuellt nätverk från punkt-till-plats och du inte har upprättat någon anslutning från datorn så kan du inte ansluta till den virtuella datorn med en privat IP-adress.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Om min virtuella dator finns i ett virtuellt nätverk med flera platsanslutningar, går då all trafik från min virtuella dator via den anslutningen?

Nej. Bara den trafik som har ett mål-IP som finns i det virtuella nätverkets lokala nätverks-IP-adressintervall som du har angett passerar den virtuella nätverksgatewayen. Trafik med ett mål-IP som finns i det virtuella nätverket stannar kvar i det virtuella nätverket. Annan trafik skickas via lastbalanseraren till offentliga nätverk. Om tvingad tunneltrafik används skickas den via Azures VPN-gateway.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Hur felsöker jag en RDP-anslutning till en virtuell dator

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]

## <a name="virtual-network-faq"></a><a name="faq"></a>Virtual Network vanliga frågor och svar

Du kan se mer information om virtuella nätverk i [Vanliga frågor och svar om Virtual Network](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om VPN Gateway finns i [Om VPN Gateway](vpn-gateway-about-vpngateways.md).
* Mer information om konfigurationsinställningar för VPN Gateway finns i [Om konfigurationsinställningar för VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md).

**"OpenVPN" är ett varumärke som tillhör OpenVPN Inc.**
