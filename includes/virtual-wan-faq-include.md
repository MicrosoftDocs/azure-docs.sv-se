---
title: inkludera fil
description: inkludera fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7411b4c000569693335cb0438fe186b290750247
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88602357"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Behöver användaren ha hubb och eker med SD-WAN/VPN-enheter för att kunna använda Azure Virtual WAN?

Det virtuella WAN-nätverket innehåller många funktioner som är inbyggda i ett enda fönster, till exempel plats-till-plats-VPN-anslutning, användare/P2S-anslutning, ExpressRoute-anslutning, Virtual Network anslutning, VPN-ExpressRoute, interaktivt, interaktivt nätverk med transitivt nätverk, centraliserad routning, Azure Firewall och Firewall Manager säkerhet, övervakning, ExpressRoute-kryptering och många andra funktioner. Du behöver inte ha alla dessa användnings fall för att börja använda Virtual WAN. Du kan komma igång med bara ett användnings fall. Den virtuella WAN-arkitekturen är en hubb och eker-arkitektur med skalning och prestanda som är inbyggda i där grenar (VPN/SD-WAN-enheter), användare (Azure VPN-klienter, openVPN eller IKEv2 klienter), ExpressRoute-kretsar, virtuella nätverk fungerar som ekrar till virtuella hubbar. Alla hubbar är anslutna i hela nät i ett standard virtuellt WAN-nätverk som gör det enkelt för användaren att använda Microsofts stamnät för alla-till-alla-anslutningar (alla ekrar). För hubb och eker med SD-WAN/VPN-enheter kan användarna antingen manuellt konfigurera det på Azures virtuella WAN-portal eller använda den virtuella WAN-partner CPE (SD-WAN/VPN) för att konfigurera anslutning till Azure. Virtuella WAN-partner ger automatisering för anslutning, vilket är möjligheten att exportera enhets informationen till Azure, ladda ned Azure-konfigurationen och upprätta anslutning till Azure Virtual WAN Hub. För VPN-anslutning från punkt-till-plats/användare stöder vi [Azure VPN-klienten](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN eller ikev2-klienten. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Kan du inaktivera helt nätaktiverade hubbar i ett virtuellt WAN-nätverk?

Det finns två varianter för virtuella WAN-nätverk: Basic och standard. I grundläggande virtuella WAN-nätverk är hubbar inte nät. I ett standard-virtuellt WAN-nätverk är hubbar nätanslutna och ansluts automatiskt när det virtuella WAN-nätverket konfigureras. Användaren behöver inte göra något speciellt. Användaren behöver inte heller inaktivera eller aktivera funktionen för att hämta nät nav. Det virtuella WAN-nätverket ger dig många routningstjänster för styrning av trafiken mellan ekrar (VNet, VPN eller ExpressRoute). Det ger dig enkelt med helt nätnav, och även flexibiliteten för att dirigera trafik efter dina behov. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Hur hanteras Tillgänglighetszoner och återhämtning i virtuella WAN-nätverk?

Virtuellt WAN är en samling nav och tjänster som görs tillgängliga i hubben. Användaren kan ha så många virtuella WAN-nätverk som behövs. I en virtuell WAN-hubb finns det flera tjänster som VPN, ExpressRoute osv. Var och en av dessa tjänster (utom Azure-brandväggen) distribueras i en Tillgänglighetszoner region, det vill säga om regionen stöder Tillgänglighetszoner. Om en region blir en tillgänglighets zon efter den första distributionen i hubben, kan användaren återskapa gatewayerna, vilket utlöser en tillgänglighets zon distribution. Alla gatewayer är etablerade i en hubb som aktiv-aktiv, vilket innebär att det finns återhämtnings kapacitet inbyggd i ett nav. Användare kan ansluta till flera hubbar om de vill ha återhämtning över flera regioner. Även om begreppet virtuellt WAN-nätverk är globalt, är den faktiska virtuella WAN-resursen Resource Manager-baserad och distribuerad i regionalt läge. Om den virtuella WAN-regionen själva skulle ha ett problem, kommer alla hubbar i det virtuella WAN-nätverket fortsätta att fungera som det är, men användaren kan inte skapa nya hubbar förrän den virtuella WAN-regionen är tillgänglig.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Vilken klient gör Azure Virtual WAN-användaren VPN (punkt-till-plats) support?

Virtuellt WAN stöder [Azure VPN-klienten](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN-klienten eller en IKEv2-klient. Azure AD-autentisering stöds med Azure VPN-klienten. minst Windows 10 client OS version 17763,0 eller senare krävs.  OpenVPN-klienter kan ha stöd för certifikatbaserad autentisering. När CERT-baserad autentisering har valts på gatewayen visas *. ovpn* -filen för att ladda ned till enheten. IKEv2 stöder både certifikat-och RADIUS-autentisering. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>För användares VPN (punkt-till-plats) – Varför delas P2S i två vägar?

Varje gateway har två instanser sker delningen så att varje gateway-instans kan allokera klient-IP: er för anslutna klienter och trafik från det virtuella nätverket dirigeras tillbaka till rätt Gateway-instans för att undvika instans hopp mellan gatewayen.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hur gör jag för att lägger du till DNS-servrar för P2S-klienter?

Det finns två alternativ för att lägga till DNS-servrar för P2S-klienter. Den första metoden är att föredra eftersom den lägger till de anpassade DNS-servrarna i gatewayen i stället för-klienten.

1. Använd följande PowerShell-skript för att lägga till anpassade DNS-servrar. Ersätt värdena för din miljö.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. Eller, om du använder Azure VPN-klienten för Windows 10, kan du ändra den hämtade profil-XML-filen och lägga ** \<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers> ** till taggarna innan du importerar den.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>För användar-VPN (punkt-till-plats) – hur många klienter stöds?

Varje användares VPN-P2S Gateway har två instanser och varje instans har stöd för upp till vissa användare när skalnings enheten ändras. Skalnings enhet 1-3 har stöd för 500 anslutningar, skalnings enhet 4-6 har stöd för 1000 anslutningar, skalnings enhet 7-12 har stöd för 5000-anslutningar och skalnings enhet 13-20 har stöd för upp till 10 000 

Till exempel låter användaren välja 1 skalen het. Varje skalnings enhet innebär att en aktiv-aktiv Gateway distribueras och var och en av instanserna (i det här fallet 2) har stöd för upp till 500 anslutningar. Eftersom du kan få 500 anslutningar * 2 per Gateway, innebär det inte att du planerar för 1000 i stället för 500 för den här skalnings enheten. Instanser kan behöva servas under vilken anslutningen för den extra 500 kan avbrytas om du överskrider det rekommenderade antalet anslutningar. Se också till att planera för stillestånds tid om du vill skala upp eller ned i skalnings enheten eller ändra plats-till-plats-konfigurationen på VPN-gatewayen.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Vad är skillnaden mellan en virtuell Azure-nätverksgateway (VPN Gateway) och en virtuell WAN-gateway i Azure?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. När du ansluter en plats till en virtuell WAN-Gateway för virtuella nätverk skiljer den sig från en vanlig virtuell nätverksgateway som använder en gateway-typ "VPN". När du ansluter en ExpressRoute-krets till en virtuell WAN-hubb används en annan resurs för ExpressRoute-gatewayen än den vanliga virtuella Nätverksgatewayen som använder Gateway-typen ExpressRoute. 

Virtuellt WAN stöder upp till 20 Gbit/s aggregerade data flöden både för VPN och ExpressRoute. Det virtuella WAN-nätverket har även stöd för anslutning med ett eko system med CPE-avdelnings enhets partner. CPE-avdelnings enheter har inbyggd automatisering som automatiskt etablerar och ansluter till Azure Virtual WAN. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner. Se [listan över föredragna partner](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Hur skiljer sig det virtuella WAN-nätverket från en virtuell Azure-nätverksgateway?

En virtuell nätverksgateway VPN är begränsad till 30 tunnlar. För anslutningar bör du använda Virtual WAN för ett storskaligt virtuellt privat nätverk. Du kan ansluta upp till 1 000 förgrenings anslutningar per region (virtuell hubb) med en agg regering på 20 Gbit/s per hubb. En anslutning är en aktiv-aktiv-tunnel från den lokala VPN-enheten till den virtuella hubben. Du kan ha en hubb per region, vilket innebär att du kan ansluta fler än 1 000 grenar över hubbar.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Vad är en skalnings enhet för virtuell WAN-Gateway

En skalnings enhet är en enhet som definieras för att välja ett sammanställt data flöde för en gateway i virtuell hubb. 1 skalnings enhet för VPN = 500 Mbit/s. 1 skalnings enhet av ExpressRoute = 2 Gbit/s. Exempel: 10 skalnings enhet för VPN skulle innebära 500 Mbit/s * 10 = 5 Gbit/s

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Vilka enhets leverantörer (virtuella WAN-partner) stöds?

Just nu stöds den helt automatiserade Virtual WAN-upplevelsen av många partner. Mer information finns i [Virtual WAN-partner](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Vilka är automatiseringsstegen för virtuella WAN-partner?

Information om automatiseringssteg för partner finns i avsnittet om [automatisering för virtuella WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Måste jag använda en önskad partnerenhet?

Nej. Du kan använda valfri VPN-kompatibel enhet som följer Azure-kraven för IKEv2/IKEv1 IPsec-stöd. Det virtuella WAN-nätverket har också CPE-partner lösningar som automatiserar anslutningen till Azure Virtual WAN, vilket gör det enklare att konfigurera IPsec VPN-anslutningar i stor skala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hur automatiserar Virtual WAN-partner anslutningsmöjligheter med Azure Virtual WAN?

Programvarudefinierade anslutningslösningar hanterar vanligtvis gren-enheter med hjälp av en kontrollant eller ett enhetsetableringscenter. Kontrollanten kan automatisera anslutningar till Azure Virtual WAN med hjälp av Azure API:er. Automation inkluderar överföring av gren information, hämtning av Azure-konfiguration, konfiguration av IPSec-tunnlar i virtuella Azure-nav och automatisk inställning av anslutning från gren enheten till Azure Virtual WAN. När du har hundratals grenar är det enkelt att ansluta med hjälp av virtuella WAN-partners för att skapa, konfigurera och hantera storskalig IPsec-anslutning. Mer information finns i den [virtuella WAN-partner automatisering](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Vad händer om en enhet jag använder inte finns med i listan över virtuella WAN-partner? Kan jag fortfarande använda den för att ansluta till Azure Virtual WAN VPN?

Ja så länge enheten stöder IPsec IKEv1 eller IKEv2. Virtuella WAN-partner automatiserar anslutningen från enheten till Azure VPN-slutpunkter. Detta innebär att automatisera steg som "gren information upload", "IPsec and Configuration" och "anslutning". Eftersom din enhet inte kommer från ett eko system med en virtuell WAN-partner måste du göra den tunga genom att manuellt ta Azure-konfigurationen och uppdatera enheten för att konfigurera IPsec-anslutning.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hur registreras nya partner som inte visas i startpartnerlistan?

Alla virtuella WAN-API: er är öppna API. Du kan gå igenom dokumentationen [virtuell WAN-partner automatisering](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) för att utvärdera teknisk genomförbarhet. En perfekt partner är en partner som har en enhet som kan etableras för IKEv1 eller IKEv2 IPSec-anslutning. När företaget har slutfört automatiserings arbetet för deras CPE-enhet baserat på de rikt linjer som anges ovan, kan du kontaktas för att azurevirtualwan@microsoft.com visas i listan här, [via partner]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Om du är en kund som vill att en viss företags lösning ska listas som en virtuell WAN-partner måste företaget kontakta det virtuella WAN-nätverket genom att skicka ett e-postmeddelande till azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hur fungerar virtuella WAN-enheter som stöder SD-WAN-enheter?

Virtuella WAN-partners automatiserar IPsec-anslutningen till Azure VPN-slutpunkter. Om den virtuella WAN-partnern är en SD-WAN-Provider, är det underförstådd att den SD-WAN-styrenheten hanterar automatisering och IPsec-anslutning till Azure VPN-slutpunkter. Om SD-WAN-enheten kräver sin egen slut punkt i stället för Azure VPN för alla patentskyddade SD-WAN-funktioner, kan du distribuera en slut punkt för SD-WAN i ett Azure VNet och samverka med Azure Virtual WAN.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hur många VPN-enheter kan ansluta till en enda hubb?

Upp till 1 000 anslutningar stöds per virtuell hubb. Varje anslutning består av fyra länkar och varje länk anslutning stöder två tunnlar som finns i en aktiv-aktiv-konfiguration. Tunnlarna avslutas i en Azure Virtual Hub VPN-gateway. Länkar representerar den fysiska ISP-länken på grenen/VPN-enheten.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Vad är en förgrenings anslutning till Azure Virtual WAN?

En anslutning från en gren eller VPN-enhet till Azure Virtual WAN är inget, men en VPN-anslutning som ansluter i stort sett VPN-platsen och Azure-VPN Gateway i en virtuell hubb.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan den lokala VPN-enheten ansluta till flera hubbar?

Ja. Trafikflöde, vid början, kommer från den lokala enheten till närmaste Microsoft-nätverksanslutning och sedan till den virtuella hubben.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Finns nya Resource Manager-resurser tillgängliga för Virtual WAN?
  
Ja, Virtual WAN har nya Resource Manager-resurser. Mer information finns i [Översikt](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan jag distribuera och använda virtuell installation av mitt favoritnätverk (i ett virtuellt NVA-nätverk) med Azure Virtual WAN?

Ja, du kan ansluta önskat NVA-VNet (virtuell nätverksinstallation) till Azure Virtual WAN.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan jag skapa en virtuell nätverks installation inuti den virtuella hubben?

Det går inte att distribuera en virtuell nätverks installation (NVA) i en virtuell hubb. Du kan dock skapa det i ett eker VNet som är anslutet till den virtuella hubben och aktivera lämplig routning för att dirigera trafik efter dina behov.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan en eker VNet ha en virtuell nätverksgateway?

Nej. VNet VNet kan inte ha en virtuell nätverksgateway om den är ansluten till den virtuella hubben.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Finns det stöd för BGP i VPN-anslutningen?

Ja, BGP stöds. När du skapar en VPN-webbplats kan du ange BGP-parametrar i den. Det innebär att alla anslutningar som har skapats i Azure för platsen aktive ras för BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?

Ja. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Är det möjligt att konstruera Azure Virtual WAN med en Resource Manager-mall?

En enkel konfiguration av ett virtuellt WAN med en hubb och en vpnsite kan skapas med en [snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Det virtuella WAN-nätverket är i första hand en REST-eller Portal driven tjänst.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kan eker-virtuella nätverk som är anslutna till en virtuell hubb kommunicera med varandra (V2V-överföring)?

Ja. Standard Virtual WAN stöder VNet-till-VNet-transitiv anslutning via det virtuella WAN-hubb som virtuella nätverk är anslutet till. I Virtual WAN-terminologi hänvisar vi till dessa sökvägar som "lokal virtuell WAN VNet-överföring" för virtuella nätverk som är ansluten till en virtuell WAN-hubb inom en region och "global virtuell WAN-överföring av virtuella WAN-nätverk" för virtuella nätverk som är ansluten via flera virtuella WAN-hubbar i två eller flera regioner. I vissa fall kan eker-virtuella nätverk också vara direkt peer-kopplad till varandra med hjälp av [Virtual Network-peering](../articles/virtual-network/virtual-network-peering-overview.md) förutom lokal eller global virtuell WAN-överföring. I detta fall prioriteras VNet-peering över den transitiva anslutningen via den virtuella WAN-hubben.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Tillåts gren-till-gren-anslutningar i Virtual WAN?

Ja, det finns stöd för gren-till-gren-anslutningar i Virtual WAN. Gren gäller konceptuellt för VPN-platsen, ExpressRoute-kretsar eller punkt-till-plats/användare VPN-användare. Aktivering av gren till gren är aktiverat som standard och kan finnas i inställningar för WAN-konfiguration. Detta gör det möjligt för VPN-grenar/användare att ansluta till andra VPN-grenar och överförings anslutningar är aktiverat mellan VPN-och ExpressRoute-användare.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Passerar gren-till-gren-trafik via det virtuella Azure-nätverket?

Ja.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Kräver Virtual WAN ExpressRoute från varje plats?

Nej. Virtual WAN kräver inte ExpressRoute från varje plats. Dina platser kan anslutas till ett providernätverk med hjälp av en ExpressRoute-krets. För platser som är anslutna via ExpressRoute till en virtuell hubb och IPsec VPN i samma hubb tillhandahåller det virtuella navet överförings anslutningar mellan VPN-och ExpressRoute-användaren.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Finns det ett nätverks flöde eller en anslutnings gräns när du använder Azure Virtual WAN?

Nätverks data flöde är per tjänst i en virtuell WAN-hubb. Även om du kan ha så många virtuella WAN-nätverk som du vill, så tillåter varje virtuellt WAN 1 hubb per region. I varje hubb är det sammanställda VPN-dataflödet upp till 20 Gbit/s, ExpressRoute-dataflödet är upp till 20 Gbit/s och det sammanlagda data flödet för VPN-och punkt-till-plats-VPN är upp till 20 Gbps. Routern i den virtuella hubben har stöd för upp till 50 Gbit/s för trafikflöden för VNet-till-VNet och antar totalt 2000 VM-arbetsbelastning över alla virtuella nätverk som är anslutna till en enda virtuell hubb.

När VPN-platser ansluter till en hubb gör de det med anslutningar. Virtuellt WAN stöder upp till 1000 anslutningar eller 2000 IPsec-tunnlar per virtuell hubb. När fjärran vändare ansluter till en virtuell hubb ansluter de till P2S VPN-gatewayen, som har stöd för upp till 10 000 användare beroende på vilken skalnings enhet (bandbredd) som valts för VPN-gatewayen för P2S i den virtuella hubben.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Vad är det totala VPN-dataflödet för en VPN-tunnel och en anslutning?

Det totala VPN-dataflödet för en hubb är upp till 20 Gbit/s baserat på den valda skalnings enheten för VPN-gatewayen. Data flödet delas av alla befintliga anslutningar. Varje tunnel i en anslutning har stöd för upp till 1 Gbit/s.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Jag ser inte inställningen 20 Gbit/s för den virtuella hubben i portalen. Hur gör jag för att konfigurera?

Navigera till VPN-gatewayen i ett nav på portalen och klicka på skalnings enheten för att ändra den till lämplig inställning.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Tillåter Virtual WAN att den lokala enheten använder flera olika Internet leverantörer parallellt, eller är alltid en enda VPN-tunnel?

Lokala enhets lösningar kan använda trafik principer för att styra trafik över flera tunnlar till Azure Virtual WAN Hub (VPN gateway i den virtuella hubben).

### <a name="what-is-global-transit-architecture"></a>Vad är global transiterings arkitektur?

Information om global överförings arkitektur finns i [Global överföring av nätverks arkitektur och virtuellt WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hur dirigeras trafiken i Azures stamnät?

Trafiken följer mönstret: Branch enhet->ISP->Microsoft Network Edge->Microsoft DC (hubb VNet)->Microsoft Network Edge->ISP->gren enhet

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Vad behöver du på varje plats i den här modellen? Bara en Internetanslutning?

Ja. En Internet anslutning och en fysisk enhet som stöder IPsec, helst från våra integrerade [virtuella WAN-partner](../articles/virtual-wan/virtual-wan-locations-partners.md). Du kan också manuellt hantera konfigurationen och anslutningen till Azure från den önskade enheten.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Hur gör jag för att aktivera standard väg (0.0.0.0/0) i en anslutning (VPN, ExpressRoute eller Virtual Network):

En virtuell hubb kan sprida en inlärd standard väg till en VPN-/ExpressRoute-anslutning för virtuella nätverk/plats-till-plats om flaggan är aktive rad på anslutningen. Den här flaggan visas när användaren redigerar en virtuell nätverks anslutning, en VPN-anslutning eller en ExpressRoute-anslutning. Som standard inaktive ras den här flaggan när en plats eller en ExpressRoute-krets är ansluten till en hubb. Den aktive ras som standard när en virtuell nätverks anslutning läggs till för att ansluta ett VNet till en virtuell hubb. Standard vägen kommer inte från den virtuella WAN-hubben. standard vägen sprids om den redan har belärts av den virtuella WAN-hubben som ett resultat av distributionen av en brand vägg i hubben, eller om en annan ansluten plats har Tvingad tunnel trafik aktive rad.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hur kan den virtuella hubben i ett virtuellt WAN-nätverk välja den bästa vägen för en väg från flera hubbar

Om en virtuell hubb lär sig samma väg från flera fjärrhubbar, är ordningen i vilken den bestämmer sig enligt följande:

1. Längsta prefix matchning.
2. Lokala vägar över interhub.
3. Statiska vägar över BGP: Detta är i kontext till besluts fattandet av en virtuell hubb-router. Om besluts fattaren däremot är VPN-gatewayen där en plats meddelar vägar via BGP eller tillhandahåller statiska adressprefix, kan statiska vägar prioriteras framför BGP-vägar.
4. ExpressRoute (ER) via VPN: ER föredras över VPN när kontexten är en lokal hubb. Överförings anslutningar mellan ExpressRoute-kretsar är bara tillgängliga via Global Reach. I scenarier där ExpressRoute-kretsen är ansluten till en hubb och det finns en annan ExpressRoute-krets som är ansluten till en annan hubb med VPN-anslutning, kan VPN vara ett alternativ för scenarier mellan olika nav.
5. SOM sökväg längd.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Tillåter Virtual WAN Hub anslutning mellan ExpressRoute-kretsar.

Överföring mellan ER-till-ER är alltid via global räckvidd. Gatewayer för virtuella hubbar distribueras i DC-eller Azure-regioner. När två ExpressRoute-kretsar ansluter via global räckvidd, behöver trafiken inte komma från gräns routrarna till den virtuella domänkontrollantens DC.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Är det en vikt i Azure Virtual WAN ExpressRoute-kretsar eller VPN-anslutningar

När flera ExpressRoute-kretsar är anslutna till en virtuell hubb, tillhandahåller flödes viktningen för anslutningen en mekanism för ExpressRoute i den virtuella hubben för att föredra en krets över den andra. Det finns ingen mekanism för att ange en vikt på en VPN-anslutning. Azure föredrar alltid en ExpressRoute-anslutning via en VPN-anslutning inom ett enda nav.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Föredrar Virtual WAN ExpressRoute via VPN för trafik som utgående Azure

Yes 

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-prefered-over-expressroute"></a>När en virtuell WAN-hubb har en ExpressRoute-krets och en VPN-plats som är ansluten till den, kan det som skulle orsaka att en anslutning till ett VPN-nätverk föredras över ExpressRoute?

När en ExpressRoute-krets är ansluten till en virtuell hubb är Microsoft Edge-routrarna den första noden för kommunikation mellan lokala platser och Azure. Dessa gräns routrar kommunicerar med de virtuella WAN-ExpressRoute gateways som i sin tur lär sig vägar från den virtuella hubben som styr alla vägar mellan alla gateways i virtuella WAN. Microsoft Edge-routrarna bearbetar virtuella nav ExpressRoute-vägar med högre prioritet över vägar från lokala platser. På grund av varje orsak om VPN-anslutningen blir det primära mediet för virtuell hubb för att lära sig vägar från (t. ex. växlings scenarier mellan ExpressRoute och VPN), om inte VPN-platsen är längre än sökvägen, kommer den virtuella hubben fortsätta att dela VPN-arbetsvägar med ExpressRoute-Gateway, vilket gör att Microsoft Edge-routrarna föredrar VPN-vägar över lokala vägar. 

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>När två hubbar (hubb 1 och 2) är anslutna och det finns en ExpressRoute-krets ansluten som en bog-bind till båda hubbarna, vilken är sökvägen för ett VNet som är anslutet till hubb 1 för att uppnå ett VNet som är anslutet i hubb 2?

Det aktuella beteendet är att föredra ExpressRoute krets Sök väg över hubb-till-hubb för anslutning mellan virtuella nätverk. Detta rekommenderas dock inte i en virtuell WAN-installation. Det virtuella WAN-teamet arbetar med en åtgärd för att aktivera inställningen för hubb-till-hubb över ExpressRoute-sökvägen. Rekommendationen gäller för flera ExpressRoute-kretsar (olika providrar) för att ansluta till en hubb och använda nav-till-hubb-anslutningen som tillhandahålls av Virtual WAN för trafik flöden mellan regioner.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Finns det stöd för IPv6 i virtuella WAN-nätverk?

IPv6 stöds inte i Virtual WAN Hub och dess gatewayer. Om du har ett VNet som har stöd för IPv6 och du vill ansluta VNet till virtuellt WAN-nätverk stöds inte det här scenariot för närvarande.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionality-"></a>Vilken är den rekommenderade API-versionen som ska användas av skript som automatiserar olika virtuella WAN-funktioner?

En lägsta version av 05-01-2020 (kan 1 2020) krävs. 

### <a name="any-virtual-wan-limits"></a>Alla virtuella WAN-gränser?
Gränser för virtuella WAN-nätverk: https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#virtual-wan-limits

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Vad är skillnaderna mellan de virtuella WAN-typerna (Basic och standard)?

Se [Basic och standard Virtual WAN](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Information om priser finns på sidan med [priser](https://azure.microsoft.com/pricing/details/virtual-wan/) .
