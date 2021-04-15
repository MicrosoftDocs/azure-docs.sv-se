---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2c1cf6e1d47f9bb78349e0846f624e1d6a484669
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386819"
---
### <a name="is-azure-virtual-wan-in-ga"></a>Är Azure Virtual WAN i GA?

Ja, Azure Virtual WAN är allmänt tillgänglig (GA). Men Virtual WAN av flera funktioner och scenarier. Det finns funktioner eller scenarier i Virtual WAN där Microsoft använder taggen Förhandsversion. I dessa fall är den specifika funktionen, eller själva scenariot, i förhandsversion. Om du inte använder en specifik förhandsgranskningsfunktion gäller vanlig ga-support. Mer information om förhandsversionssupport finns [i Kompletterande villkor för användning av Microsoft Azure förhandsversioner.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Behöver användaren ha nav och ekrar med SD-WAN/VPN-enheter för att kunna använda Azure Virtual WAN?

Virtual WAN innehåller många funktioner som är inbyggda i ett fönster, till exempel PLATS-/plats-till-plats-VPN-anslutning, Användar-/P2S-anslutning, ExpressRoute-anslutning, Virtual Network-anslutning, VPN ExpressRoute-anslutning, transitiv anslutning mellan virtuella nätverk, centraliserad routning, Azure Firewall- och Firewall Manager-säkerhet, övervakning, ExpressRoute-kryptering och många andra funktioner. Du behöver inte ha alla dessa användningsfall för att börja använda Virtual WAN. Du kan komma igång med bara ett användningsfall.

Arkitekturen Virtual WAN är en nav- och ekerarkitektur med inbyggd skalning och prestanda där grenar (VPN/SD-WAN-enheter), användare (Azure VPN-klienter, openVPN- eller IKEv2-klienter), ExpressRoute-kretsar, virtuella nätverk fungerar som ekrar till virtuella hubbar. Alla hubbar är anslutna i fullständigt nät i ett Standard Virtual WAN vilket gör det enkelt för användaren att använda Microsofts stamnät för alla-till-alla-anslutningar (alla ekrar). För hubb- och ekerenheter med SD-WAN/VPN-enheter kan användarna antingen konfigurera det manuellt i Azure Virtual WAN-portalen eller använda Virtual WAN Partner CPE (SD-WAN/VPN) för att konfigurera anslutningen till Azure.

Virtual WAN-partner tillhandahåller automatisering för anslutning, vilket är möjligheten att exportera enhetsinformation till Azure, ladda ned Azure-konfigurationen och upprätta en anslutning till Azure Virtual WAN hubben. För punkt-till-plats-/användar-VPN-anslutning stöder vi [Azure VPN-klienten,](https://go.microsoft.com/fwlink/?linkid=2117554)OpenVPN- eller IKEv2-klienten.

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>Kan du inaktivera helt nätade hubbar i en Virtual WAN?

Virtual WAN finns i två varianter: Basic och Standard. I Basic Virtual WAN är hubbar inte nätade. I en standard-Virtual WAN ansluts hubbar automatiskt när det virtuella WAN-nätverket konfigureras. Användaren behöver inte göra något specifikt. Användaren behöver inte heller inaktivera eller aktivera funktionen för att hämta mesh-hubbar. Virtual WAN många routningsalternativ för att styra trafiken mellan alla ekrar (VNet, VPN eller ExpressRoute). Det ger enkel helt nätade hubbar och flexibiliteten att dirigera trafik efter dina behov.

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Hur hanteras Tillgänglighetszoner och återhämtning i Virtual WAN?

Virtual WAN är en samling hubbar och tjänster som görs tillgängliga i hubben. Användaren kan ha så många Virtual WAN per behov. I en Virtual WAN finns det flera tjänster som VPN, ExpressRoute osv. Var och en av dessa tjänster (utom Azure Firewall) distribueras i en Tillgänglighetszoner region, det vill säga om regionen stöder Tillgänglighetszoner. Om en region blir en tillgänglighetszon efter den första distributionen i hubben kan användaren återskapa gatewayerna, vilket utlöser en distribution av tillgänglighetszonen. Alla gatewayer etableras i en hubb som aktiv-aktiv, vilket innebär att det finns inbyggd återhämtning i en hubb. Användare kan ansluta till flera hubbar om de vill ha återhämtning i flera regioner.

Även om begreppet Virtual WAN är globalt är själva Virtual WAN resurs Resource Manager och distribueras regionalt. Om själva den virtuella WAN-regionen skulle ha ett problem fortsätter alla hubbar i det virtuella WAN-wanet att fungera som det är, men användaren kommer inte att kunna skapa nya hubbar förrän den virtuella WAN-regionen är tillgänglig.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Vilken klient stöder Azure Virtual WAN VPN för användare (punkt-till-plats)?

Virtual WAN har stöd [för Azure VPN-klienten,](https://go.microsoft.com/fwlink/?linkid=2117554)OpenVPN-klienten eller en IKEv2-klient. Azure AD-autentisering stöds med Azure VPN Client. Minst Windows 10 version 17763.0 eller senare av klientoperativsystemet krävs.  OpenVPN-klienter har stöd för certifikatbaserad autentisering. När certifikatbaserad autentisering har valts på gatewayen visas filen.ovpn* som ska laddas ned till enheten. IKEv2 stöder både certifikat- och RADIUS-autentisering. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>För användar-VPN (punkt-till-plats)– Varför är P2S-klientpoolen uppdelad i två vägar?

Varje gateway har två instanser. Uppdelningen sker så att varje gatewayinstans oberoende kan allokera klient-IP-adresser för anslutna klienter och trafik från det virtuella nätverket dirigeras tillbaka till rätt gatewayinstans för att undvika hopp mellan gatewayinstanser.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Hur gör jag för att lägga till DNS-servrar för P2S-klienter?

Det finns två alternativ för att lägga till DNS-servrar för P2S-klienterna. Den första metoden är att föredra eftersom den lägger till anpassade DNS-servrar till gatewayen i stället för klienten.

1. Använd följande PowerShell-skript för att lägga till de anpassade DNS-servrarna. Ersätt värdena för din miljö.

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
2. Om du använder Azure VPN-klienten för Windows 10 kan du ändra XML-filen **\<dnsservers> \<dnsserver> \</dnsserver> \</dnsservers>** för den nedladdade profilen och lägga till taggarna innan du importerar den.

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

Varje VPN P2S-gateway för användare har två instanser. Varje instans stöder upp till ett visst antal anslutningar när skalningsenheten ändras. Skalningsenhet 1–3 stöder 500 anslutningar, skalningsenhet 4–6 stöder 1 000 anslutningar, skalningsenhet 7–12 stöder 5 000 anslutningar och skalningsenhet 13–18 stöder upp till 10 000 anslutningar.

Anta till exempel att användaren väljer 1 skalningsenhet. Varje skalningsenhet skulle innebära en aktiv-aktiv gateway distribuerad och var och en av instanserna (i det här fallet 2) skulle stödja upp till 500 anslutningar. Eftersom du kan få 500 anslutningar * 2 per gateway betyder det inte att du planerar för 1 000 i stället för 500 för den här skalningsenheten. Instanser kan behöva serdas när anslutningen för de extra 500 kan avbrytas om du överskrider det rekommenderade antalet anslutningar. Se också till att planera för stilleståndstid om du bestämmer dig för att skala upp eller ned på skalningsenheten eller ändra punkt-till-plats-konfigurationen på VPN-gatewayen.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Vad är skillnaden mellan en virtuell Azure-nätverksgateway (VPN Gateway) och en Azure Virtual WAN VPN-gateway?

Virtual WAN tillhandahåller storskalig plats-till-plats-anslutning och byggs för dataflöde, skalbarhet och användarvänlighet. När du ansluter en plats till en Virtual WAN VPN-gateway skiljer sig den från en vanlig virtuell nätverksgateway som använder gatewaytypen "VPN". När du ansluter en ExpressRoute-krets till en Virtual WAN-hubb använder den på samma sätt en annan resurs för ExpressRoute-gatewayen än den vanliga virtuella nätverksgatewayen som använder gatewaytypen ExpressRoute. 

Virtual WAN har stöd för upp till 20 Gbit/s aggregerat dataflöde både för VPN och ExpressRoute. Virtual WAN har även automatisering för anslutning till ett ekosystem med CPE-grenenhetspartner. CPE-grenenheter har inbyggd automatisering som automatiskt företer och ansluter till Azure Virtual WAN. Enheterna finns tillgängliga från ett växande ekosystem av SD-WAN- och VPN-partner. Se [listan över föredragna partner](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>Hur skiljer Virtual WAN en virtuell nätverksgateway i Azure?

Vpn för en virtuell nätverksgateway är begränsat till 30 tunnlar. För anslutningar bör du använda Virtual WAN för ett storskaligt virtuellt privat nätverk. Du kan ansluta upp till 1 000 grenanslutningar per region (virtuell hubb) med sammanlagt 20 Gbit/s per hubb. En anslutning är en aktiv-aktiv-tunnel från den lokala VPN-enheten till den virtuella hubben. Du kan ha en hubb per region, vilket innebär att du kan ansluta fler än 1 000 grenar mellan hubbar.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Vad är en Virtual WAN gatewayskalningsenhet?

En skalningsenhet är en enhet som definieras för att välja ett aggregerat dataflöde för en gateway i virtuell hubb. 1 skalningsenhet för VPN = 500 Mbit/s. 1 skalningsenhet för ExpressRoute = 2 Gbit/s. Exempel: 10 skalningsenhet för VPN skulle innebära 500 Mbit/s * 10 = 5 Gbit/s

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Vilka enhetsleverantörer (Virtual WAN partner) stöds?

Just nu stöds den helt automatiserade Virtual WAN-upplevelsen av många partner. Mer information finns i [Virtual WAN-partner](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Vilka är automatiseringsstegen för virtuella WAN-partner?

Information om automatiseringssteg för partner finns i avsnittet om [automatisering för virtuella WAN-partner](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Måste jag använda en önskad partnerenhet?

Nej. Du kan använda valfri VPN-kompatibel enhet som följer Azure-kraven för IKEv2/IKEv1 IPsec-stöd. Virtual WAN har också CPE-partnerlösningar som automatiserar anslutningen till Azure Virtual WAN gör det enklare att konfigurera IPsec VPN-anslutningar i stor skala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Hur automatiserar Virtual WAN-partner anslutningsmöjligheter med Azure Virtual WAN?

Programvarudefinierade anslutningslösningar hanterar vanligtvis gren-enheter med hjälp av en kontrollant eller ett enhetsetableringscenter. Kontrollanten kan automatisera anslutningar till Azure Virtual WAN med hjälp av Azure API:er. Automatiseringen omfattar överföring av greninformation, nedladdning av Azure-konfigurationen, konfiguration av IPsec-tunnlar till Virtuella Azure-hubbar och automatisk konfiguration av anslutningar från grenenheten till Azure Virtual WAN. När du har hundratals grenar är det enkelt att ansluta med Virtual WAN CPE-partner eftersom onboarding-upplevelsen gör att du inte behöver konfigurera, konfigurera och hantera storskaligA IPsec-anslutningar. Mer information finns i [Virtual WAN automatisering av partner.](../articles/virtual-wan/virtual-wan-configure-automation-providers.md)

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Vad händer om en enhet som jag använder inte finns Virtual WAN en partnerlista? Kan jag fortfarande använda den för att ansluta till Azure Virtual WAN VPN?

Ja så länge enheten stöder IPsec IKEv1 eller IKEv2. Virtual WAN-partner automatiserar anslutningen från enheten till Azure VPN-slutpunkter. Detta innebär att du automatiserar steg som "överföring av greninformation", "IPsec och konfiguration" och "anslutning". Eftersom enheten inte är från ett Virtual WAN-partnerekosystem måste du göra grovjobbet med att manuellt ta Azure-konfigurationen och uppdatera enheten för att konfigurera IPsec-anslutning.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Hur registreras nya partner som inte visas i startpartnerlistan?

Alla VIRTUAL WAN-API:er är öppna API:er. Du kan gå igenom dokumentationen för Virtual WAN [för att utvärdera](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) teknisk genomförbarhet. En perfekt partner är en partner som har en enhet som kan etableras för IKEv1 eller IKEv2 IPSec-anslutning. När företaget har slutfört automatiseringsarbetet för sin CPE-enhet baserat på automationsriktlinjerna ovan kan du kontakta för att visas här azurevirtualwan@microsoft.com [Anslutning via partner]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Om du är en kund som vill att en viss företagslösning ska listas som en Virtual WAN-partner, be företaget kontakta Virtual WAN genom att skicka ett e-postmeddelande till azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>Hur stöds Virtual WAN SD-WAN-enheter?

Virtual WAN automatiserar IPsec-anslutningen till Azure VPN-slutpunkter. Om SD-WAN Virtual WAN partnern är en SD-WAN-provider är det underförstått att SD-WAN-kontrollanten hanterar automatisering och IPsec-anslutning till Azure VPN-slutpunkter. Om SD-WAN-enheten kräver en egen startpunkt i stället för Azure VPN för alla egna SD-WAN-funktioner kan du distribuera SD-WAN-startpunkten i ett azure-VNet och samexistera med Azure Virtual WAN.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Hur många VPN-enheter kan ansluta till en enda hubb?

Upp till 1 000 anslutningar stöds per virtuell hubb. Varje anslutning består av fyra länkar och varje länkanslutning stöder två tunnlar som är i en aktiv-aktiv-konfiguration. Tunnlarna avslutas i en virtuell Azure Hub VPN-gateway. Länkar representerar den fysiska Internetleverantörens länk på grenen/VPN-enheten.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Vad är en grenanslutning till Azure Virtual WAN?

En anslutning från en gren eller VPN-enhet till Azure Virtual WAN är en VPN-anslutning som ansluter virtuellt vpn-platsen och Azure-VPN Gateway i en virtuell hubb.

### <a name="what-happens-if-the-on-premises-vpn-device-only-has-1-tunnel-to-an-azure-virtual-wan-vpn-gateway"></a>Vad händer om den lokala VPN-enheten bara har en tunnel till en Azure Virtual WAN VPN-gateway?

En Azure Virtual WAN-anslutning består av två tunnlar. En Virtual WAN VPN-gateway distribueras i en virtuell hubb i aktivt-aktivt läge, vilket innebär att det finns separata tunnlar från lokala enheter som avslutas på separata instanser. Det här är rekommendationen för alla användare. Men om användaren väljer att bara ha en tunnel till en av Virtual WAN VPN-gatewayinstanserna, om gatewayinstansen av någon anledning (underhåll, korrigeringar osv.) tas offline, flyttas tunneln till den sekundära aktiva instansen och användaren kan uppleva en återanslutning. BGP-sessioner flyttas inte mellan instanser.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Kan den lokala VPN-enheten ansluta till flera hubbar?

Ja. När trafikflödet kommer från den lokala enheten till den närmaste Microsoft-nätverkskanten och sedan till den virtuella hubben.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Finns nya Resource Manager-resurser tillgängliga för Virtual WAN?
  
Ja, Virtual WAN har nya Resource Manager resurser. Mer information finns i [Översikt](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Kan jag distribuera och använda virtuell installation av mitt favoritnätverk (i ett virtuellt NVA-nätverk) med Azure Virtual WAN?

Ja, du kan ansluta önskat NVA-VNet (virtuell nätverksinstallation) till Azure Virtual WAN.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Kan jag skapa en virtuell nätverksinstallation i den virtuella hubben?

En virtuell nätverksinstallation (NVA) kan inte distribueras i en virtuell hubb. Du kan dock skapa det i ett virtuellt ekernätverk som är anslutet till den virtuella hubben och aktivera lämplig routning för att dirigera trafik efter dina behov.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Kan ett virtuellt ekernätverk ha en virtuell nätverksgateway?

Nej. Det virtuella ekernätverket kan inte ha en virtuell nätverksgateway om det är anslutet till den virtuella hubben.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>Finns det stöd för BGP i VPN-anslutning?

Ja, BGP stöds. När du skapar en VPN-plats kan du ange BGP-parametrarna i den. Detta innebär att alla anslutningar som skapats i Azure för den platsen kommer att aktiveras för BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Finns det licens- eller prisinformation om Virtual WAN?

Ja. Se sidan med [prissättning](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>Är det möjligt att konstruera Azure Virtual WAN med en Resource Manager-mall?

En enkel konfiguration av en Virtual WAN med en hubb och en vpnsite kan skapas med hjälp av en [snabbstartsmall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). Virtual WAN är främst en REST- eller portaldriven tjänst.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Kan virtuella ekernätverk som är anslutna till en virtuell hubb kommunicera med varandra (V2V-överföring)?

Ja. Standard Virtual WAN stöder transitiv anslutning mellan virtuella nätverk via den Virtual WAN som de virtuella nätverken är anslutna till. I Virtual WAN terminologi refererar vi till dessa sökvägar som "lokal Virtual WAN VNet-överföring" för virtuella nätverk som är anslutna till en Virtual Wan-hubb inom en enda region och "global Virtual WAN VNet-överföring" för virtuella nätverk som är anslutna via flera Virtual WAN-hubbar i två eller flera regioner.

I vissa scenarier kan ekernätverk också peer-kopplades direkt med varandra med hjälp av [peering](../articles/virtual-network/virtual-network-peering-overview.md) för virtuella nätverk utöver lokal eller global Virtual WAN VNet-överföring. I det här fallet har VNet-peering företräde framför den transitiva anslutningen via den Virtual WAN hubben.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>Tillåts gren-till-gren-anslutningar i Virtual WAN?

Ja, det finns stöd för gren-till-gren-anslutningar i Virtual WAN. Gren gäller konceptuellt för VPN-plats, ExpressRoute-kretsar eller PUNKT-till-plats-/användar-VPN-användare. Aktivering av gren-till-gren är aktiverat som standard och kan finnas i **WAN-konfigurationsinställningar.** Detta gör att VPN-grenar/användare kan ansluta till andra VPN-grenar och överföringsanslutningen är också aktiverad mellan VPN- och ExpressRoute-användare.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Passerar trafik från gren till gren genom Azure Virtual WAN?

Ja. Trafik från gren till gren passerar genom Azure Virtual WAN.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>Kräver Virtual WAN ExpressRoute från varje plats?

Nej. Virtual WAN kräver inte ExpressRoute från varje plats. Dina platser kan anslutas till ett providernätverk med hjälp av en ExpressRoute-krets. För platser som är anslutna med ExpressRoute till en virtuell hubb och IPsec VPN i samma hubb tillhandahåller den virtuella hubben överföringsanslutning mellan VPN- och ExpressRoute-användaren.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Finns det ett dataflöde eller en anslutningsgräns för nätverket när du använder Azure Virtual WAN?

Nätverkets dataflöde är per tjänst i en virtuell WAN-hubb. Du kan ha så många virtuella WAN som du vill, men varje Virtual WAN 1 hubb per region. I varje hubb är DET aggregerade VPN-dataflödet upp till 20 Gbit/s, expressroute-aggregerat dataflöde är upp till 20 Gbit/s och användar-VPN/punkt-till-plats VPN-aggregerat dataflöde är upp till 20 Gbit/s. Routern i den virtuella hubben stöder upp till 50 Gbit/s för VNet-till-VNet-trafikflöden och förutsätter totalt 2 000 VM-arbetsbelastningar för alla virtuella nätverk som är anslutna till en enda virtuell hubb.

När VPN-platser ansluter till en hubb gör de det med anslutningar. Virtual WAN stöder upp till 1 000 anslutningar eller 2 000 IPsec-tunnlar per virtuell hubb. När fjärranslutna användare ansluter till en virtuell hubb ansluter de till P2S VPN-gatewayen, som stöder upp till 10 000 användare beroende på vilken skalningsenhet (bandbredd) som valts för P2S VPN-gatewayen i den virtuella hubben.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Vad är det totala VPN-dataflödet för en VPN-tunnel och en anslutning?

Det totala VPN-dataflödet för en hubb är upp till 20 Gbit/s baserat på den valda skalningsenheten för VPN-gatewayen. Dataflödet delas av alla befintliga anslutningar. Varje tunnel i en anslutning har stöd för upp till 1 Gbit/s.

### <a name="can-i-use-nat-t-on-my-vpn-connections"></a>Kan jag använda NAT-T på mina VPN-anslutningar?

Ja, NAT-traverserar (NAT-T) stöds. DEN Virtual WAN VPN-gatewayen utför INTE NAT-liknande funktioner på de inre paketen till/från IPsec-tunnlarna. I den här konfigurationen ser du till att den lokala enheten initierar IPsec-tunneln.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Jag ser inte inställningen 20 Gbit/s för den virtuella hubben i portalen. Hur gör jag för att konfigurera det?

Navigera till VPN-gatewayen i en hubb på portalen och klicka sedan på skalningsenheten för att ändra den till lämplig inställning.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>Tillåter Virtual WAN lokala enheten att använda flera Internetleverantörer parallellt, eller är det alltid en enda VPN-tunnel?

Lokala enhetslösningar kan tillämpa trafikprinciper för att styra trafiken över flera tunnlar till Azure Virtual WAN (VPN-gateway i den virtuella hubben).

### <a name="what-is-global-transit-architecture"></a>Vad är global transit-arkitektur?

Information om global transit-arkitektur finns i [Global transit network architecture and Virtual WAN](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Hur dirigeras trafiken i Azures stamnät?

Trafiken följer mönstret: grenenhet ->ISP->Microsoft network edge->Microsoft DC (hub VNet)->Microsoft network edge->ISP->branch device

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Vad behöver du på varje plats i den här modellen? Bara en Internetanslutning?

Ja. En Internetanslutning och fysisk enhet som stöder IPsec, helst från våra integrerade [Virtual WAN partner](../articles/virtual-wan/virtual-wan-locations-partners.md). Du kan också manuellt hantera konfigurationen och anslutningen till Azure från önskad enhet.

### <a name="how-do-i-enable-default-route-00000-for-a-connection-vpn-expressroute-or-virtual-network"></a>Hur gör jag för att aktivera standardväg (0.0.0.0/0) för en anslutning (VPN, ExpressRoute eller Virtual Network)?

En virtuell hubb kan sprida en inlärd standardväg till ett virtuellt nätverk/plats-till-plats-VPN/ExpressRoute-anslutning om flaggan är Aktiverad för anslutningen. Den här flaggan visas när användaren redigerar en virtuell nätverksanslutning, en VPN-anslutning eller en ExpressRoute-anslutning. Som standard är den här flaggan inaktiverad när en plats eller en ExpressRoute-krets är ansluten till en hubb. Den är aktiverad som standard när en virtuell nätverksanslutning läggs till för att ansluta ett virtuellt nätverk till en virtuell hubb.

Standardvägen kommer inte från den Virtual WAN hubben. Standardvägen sprids om den redan har lärts in av Virtual WAN-hubben på grund av att en brandvägg har distribuerats i hubben, eller om en annan ansluten plats har tvingad tunneling aktiverat. En standardväg sprids inte mellan hubbar (mellan hubbar).

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>Hur väljer den virtuella hubben i ett virtuellt WAN den bästa vägen för en väg från flera hubbar?

Om en virtuell hubb lär sig samma väg från flera fjärrhubar är ordningen som den bestämmer i följande ordning:

1. Matchning med längst prefix.
1. Lokala vägar via interhub (virtuell hubb tilldelar 65520-65520 för interhub AS).
1. Statiska vägar över BGP: Detta är i kontexten till det beslut som fattas av den virtuella hubbrouter. Men om beslutsfattare är VPN-gatewayen där en plats annonserar vägar via BGP eller tillhandahåller statiska adressprefix, kan statiska vägar föredras framför BGP-vägar.
1. ExpressRoute (ER) över VPN: ER föredras framför VPN när kontexten är en lokal hubb. Överföringsanslutning mellan ExpressRoute-kretsar är endast tillgänglig via Global Reach. I scenarier där ExpressRoute-kretsen är ansluten till en hubb och det finns en annan ExpressRoute-krets som är ansluten till en annan hubb med VPN-anslutning kan VPN därför föredras för scenarier mellan hubbar.
1. AS-sökvägslängd.

### <a name="does-the-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>Tillåter Virtual WAN-hubben anslutning mellan ExpressRoute-kretsar?

Överföringen mellan ER-till-ER sker alltid via global räckvidd. Virtuella hubbgatewayer distribueras i DC- eller Azure-regioner. När två ExpressRoute-kretsar ansluter via global räckvidd behöver trafiken inte komma hela vägen från gränsroutrar till den virtuella hubbdomänkontrollanten.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Finns det ett begrepp för vikt i Azure Virtual WAN ExpressRoute-kretsar eller VPN-anslutningar?

När flera ExpressRoute-kretsar är anslutna till en virtuell hubb ger routningsvikten för anslutningen en mekanism för ExpressRoute i den virtuella hubben att föredra den ena kretsen framför den andra. Det finns ingen mekanism för att ange en vikt för en VPN-anslutning. Azure föredrar alltid en ExpressRoute-anslutning framför en VPN-anslutning inom en enda hubb.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>Föredrar Virtual WAN ExpressRoute framför VPN för utgående trafik i Azure

Ja. Virtual WAN föredrar ExpressRoute framför VPN för utgående trafik i Azure.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Vad skulle göra Virtual WAN VPN-anslutningsväg att föredra framför ExpressRoute när en Virtual WAN en ExpressRoute-krets och en VPN-plats är ansluten till den?

När en ExpressRoute-krets är ansluten till en virtuell hubb är Microsoft Edge-routrar den första noden för kommunikation mellan den lokala platsen och Azure. Dessa gränsroutrar kommunicerar med Virtual WAN ExpressRoute-gatewayer som i sin tur lär sig vägar från den virtuella hubbrouter som styr alla vägar mellan gatewayer i Virtual WAN. Microsoft-gränsroutrar bearbetar ExpressRoute-vägar för virtuella hubbar med högre prioritet över vägar som lärs in från en lokal plats.

Om VPN-anslutningen av någon anledning blir det primära mediet för den virtuella hubben att lära sig vägar från (t.ex. redundansscenarier mellan ExpressRoute och VPN), såvida inte VPN-platsen har en längre AS Path-längd, fortsätter den virtuella hubben att dela VPN-inlärda vägar med ExpressRoute-gatewayen. Detta gör att Microsoft-gränsroutrar föredrar VPN-vägar framför lokala vägar.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Vad är sökvägen för ett VNet som är anslutet till hubb 1 för att nå ett VNet som är anslutet i hubb 2 när två hubbar (hubb 1 och 2) är anslutna och det finns en ExpressRoute-krets som är ansluten till båda hubbarna?

Det aktuella beteendet är att föredra ExpressRoute-kretssökvägen framför hubb-till-hubb för VNet-till-VNet-anslutning. Detta rekommenderas dock inte i en virtual WAN-installation. Teamet Virtual WAN arbetar med en korrigering för att aktivera inställningen för hubb-till-hubb över ExpressRoute-sökvägen. Rekommendationen är att flera ExpressRoute-kretsar (olika leverantörer) ansluter till en hubb och använder hubb-till-hubb-anslutningen som tillhandahålls av Virtual WAN för trafikflöden mellan regioner.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>Kan hubbar skapas i olika resursgrupper i Virtual WAN?

Ja. Det här alternativet är för närvarande endast tillgängligt via PowerShell. Den Virtual WAN portalen kräver att hubbarna finns i samma resursgrupp som den Virtual WAN resursen själv.

### <a name="what-is-the-recommended-hub-address-space-during-hub-creation"></a>Vilket är det rekommenderade hubbadressutrymmet när hubben skapas?

Den rekommenderade Virtual WAN hubbadressutrymmet är /23. Virtual WAN tilldelar undernät till olika gatewayer (ExpressRoute, plats-till-plats-VPN, punkt-till-plats-VPN, Azure Firewall, virtuell hubbrouter). För scenarier där NVA:er distribueras i en virtuell hubb, tar en /28 vanligtvis ut för NVA-instanserna. Om användaren däremot etablerar flera NVA:er kan ett /27-undernät tilldelas . Därför bör du ha en framtida arkitektur i åtanke, medan Virtual WAN-hubbar distribueras med en minsta storlek på /24, men det rekommenderade hubbadressutrymmet vid skapandetiden för användaren att mata in är /23.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>Finns det stöd för IPv6 i Virtual WAN?

IPv6 stöds inte i Virtual WAN och dess gatewayer. Om du har ett VNet som har stöd för IPv4 och IPv6 och du vill ansluta det virtuella nätverket till Virtual WAN stöds inte det här scenariot för närvarande.

I scenariot med punkt-till-plats-användar-VPN med Internet-utbrytning via Azure Firewall måste du förmodligen stänga av IPv6-anslutningen på klientenhet för att tvinga trafik till Virtual WAN hubben. Det beror på att moderna enheter som standard använder IPv6-adresser.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Vilken API-version rekommenderas för skript som automatiserar olika Virtual WAN funktioner?

En lägsta version på 05-01-2020 (1 maj 2020) krävs.

### <a name="are-there-any-virtual-wan-limits"></a>Finns det några Virtual WAN gränser?

Se avsnittet [Virtual WAN gränser](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) på sidan Prenumerations- och tjänstbegränsningar.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Vilka är skillnaderna mellan de Virtual WAN typerna (Basic och Standard)?

Se [Grundläggande och Standard virtuella WAN.](../articles/virtual-wan/virtual-wan-about.md#basicstandard) Information om priser finns på [sidan](https://azure.microsoft.com/pricing/details/virtual-wan/) Prissättning.

### <a name="does-virtual-wan-store-customer-data"></a>Lagrar Virtual WAN kunddata?

Nej. Virtual WAN lagrar inga kunddata.

### <a name="are-there-any-managed-service-providers-that-can-manage-virtual-wan-for-users-as-a-service"></a>Finns det några leverantörer av hanterade tjänster som kan hantera Virtual WAN för användare som en tjänst?

Ja. En lista över MSP-lösningar (Managed Service Provider) som är aktiverade via Azure Marketplace finns i [Azure Marketplace av Azure-nätverkstjänster MSP-partner](../articles/networking/networking-partners-msp.md#msp).

### <a name="how-does-virtual-wan-hub-routing-differ-from-azure-route-server-in-a-vnet"></a>Hur skiljer sig Virtual WAN Hub-routning från Azure Route Server i ett VNet?

Azure Route Server tillhandahåller en BGP Border Gateway Protocol-peeringtjänst (Border Gateway Protocol) som kan användas av NVA:er (Network Virtual Appliance) för att lära sig vägar från vägservern i ett virtuellt HUB-hubbnätverk. Virtual WAN-routning innehåller flera funktioner, inklusive VNet-till-VNet-överföringsroutning, anpassad routningsassociation och spridning samt en helt nätad hubbtjänst med zero touch tillsammans med anslutningstjänster för ExpressRoute, Plats-VPN, P2S VPN för fjärranvändare/storskalig p2S och säkra hubbfunktioner (Azure Firewall). När du upprättar en BGP-peering mellan din NVA och Azure Route Server kan du annonsera IP-adresser från din NVA till ditt virtuella nätverk. För alla avancerade routningsfunktioner, till exempel överföringsroutning, anpassad routning osv. kan du använda Virtual WAN routning.
