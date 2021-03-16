---
title: Vanliga frågor och svar om Azure ExpressRoute | Microsoft Docs
description: Vanliga frågor och svar om ExpressRoute innehåller information om Azure-tjänster, kostnader, data och anslutningar som stöds, SLA, providers och platser, bandbredd och ytterligare teknisk information.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: duau
ms.openlocfilehash: 417a6a55f0114ec35554e598b456f4fb67eb1c1b
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103574466"
---
# <a name="expressroute-faq"></a>Vanliga frågor och svar för ExpressRoute

## <a name="what-is-expressroute"></a>Vad är ExpressRoute?

ExpressRoute är en Azure-tjänst som gör det möjligt att skapa privata anslutningar mellan Microsoft-datacenter och infrastruktur som finns lokalt eller i en samplacerings anläggning. ExpressRoute-anslutningar går inte via det offentliga Internet och ger högre säkerhet, tillförlitlighet och hastighet med lägre fördröjning än vanliga anslutningar via Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Vilka är fördelarna med att använda ExpressRoute och privata nätverks anslutningar?

ExpressRoute-anslutningar går inte via offentligt Internet. De ger högre säkerhet, tillförlitlighet och hastigheter, med lägre och konsekvent fördröjning än vanliga anslutningar via Internet. I vissa fall kan användning av ExpressRoute-anslutningar för att överföra data mellan lokala enheter och Azure ge avsevärda kostnads besparingar.

### <a name="where-is-the-service-available"></a>Var finns tjänsten?

Se den här sidan för tjänst lokalisering och tillgänglighet: [ExpressRoute partners och platser](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Hur kan jag använda ExpressRoute för att ansluta till Microsoft om jag inte har några samarbeten med någon av ExpressRoute-frakt partnerna?

Du kan välja en regional operatör och land-Ethernet-anslutning till en av de Exchange Provider-platser som stöds. Du kan sedan peer-koppla med Microsoft på leverantörs platsen. Kontrol lera det sista avsnittet av [ExpressRoute-partners och platser](expressroute-locations.md) för att se om din tjänst leverantör finns på någon av Exchange-platserna. Du kan sedan beställa en ExpressRoute-krets via tjänst leverantören för att ansluta till Azure.

### <a name="how-much-does-expressroute-cost"></a>Hur mycket kostar ExpressRoute?

Se [pris](https://azure.microsoft.com/pricing/details/expressroute/) information för pris information.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>Använder jag den här bandbredden för ingångs-och utgående trafik separat om jag betalar för en ExpressRoute-krets för en specifik bandbredd?

Ja, ExpressRoute-kretsens bandbredd är duplex. Om du till exempel köper en 200 Mbit/s ExpressRoute-krets, ska du köpa 200 Mbit/s för ingress trafik och 200 Mbit/s för utgående trafik.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-private-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Om jag betalar för en ExpressRoute-krets med en specifik bandbredd, måste den privata anslutningen jag köper från min nätverks leverantör ha samma hastighet?

Nej. Du kan köpa en privat anslutning till valfri hastighet från din tjänst leverantör. Anslutningen till Azure är dock begränsad till den ExpressRoute krets bandbredd som du köper.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>Har jag möjlighet att använda mer än min upphandlade bandbredd om jag betalar för en ExpressRoute-krets för en specifik bandbredd?

Ja, du kan använda upp till två gånger bandbredds gränsen som du har upprättat genom att använda den tillgängliga bandbredden på den sekundära anslutningen av ExpressRoute-kretsen. Den inbyggda redundansen av kretsen konfigureras med hjälp av primära och sekundära anslutningar, var och en av de upphandlade bandbredderna till två Microsoft Enterprise Edge-routrar (msee). Bandbredden som är tillgänglig via den sekundära anslutningen kan användas för ytterligare trafik om det behövs. Eftersom den sekundära anslutningen är avsedd för redundans, är den inte garanterad och bör inte användas för ytterligare trafik under en varaktig tids period. Om du vill veta mer om hur du använder både anslutningar för att överföra trafik, se [Använd som sökväg väntar](./expressroute-optimize-routing.md#solution-use-as-path-prepending).

Om du planerar att endast använda din primära anslutning för att överföra trafik, åtgärdas bandbredden för anslutningen och ett försök görs att överprenumerera på den, vilket leder till ökade paket. Om trafiken flödar genom en ExpressRoute-Gateway, är bandbredden för gateway-SKU: n fast och inte burstbar. Bandbredden för varje gateway-SKU finns i [om ExpressRoute virtuella](expressroute-about-virtual-network-gateways.md#aggthroughput)nätverksgateway.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kan jag använda samma privata nätverks anslutning med virtuella nätverk och andra Azure-tjänster samtidigt?

Ja. En ExpressRoute-krets, som har kon figurer ATS, gör att du kan komma åt tjänster i ett virtuellt nätverk och andra Azure-tjänster samtidigt. Du ansluter till virtuella nätverk via den privata peering-sökvägen och till andra tjänster via Microsoft peering-sökvägen.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Hur annonseras virtuella nätverk på ExpressRoute privata peering?

ExpressRoute-gatewayen annonserar *adress utrymmet* för det virtuella Azure-nätverket. du kan inte inkludera/exkludera på under näts nivån. Det är alltid det virtuella nätverkets adress utrymme som annonseras. Om VNet-peering används och peer-allokerat VNet har aktiverat "Använd fjärran sluten Gateway", annonseras även adress utrymmet för peer-allokerat VNet.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Hur många prefix kan annonseras från ett VNet till lokalt på ExpressRoute privata peering?

Det finns högst 1000 prefix som annonseras på en enskild ExpressRoute-anslutning eller via VNet-peering med hjälp av Gateway-överföring. Om du till exempel har 999 adress utrymmen på ett enskilt VNet som är anslutet till en ExpressRoute-krets, annonseras alla 999 av dessa prefix till lokalt. Alternativt, om du har ett VNet aktiverat för att tillåta Gateway-överföring med 1 adress utrymme och 500 ekrar som är aktiverade med alternativet "Tillåt fjärran sluten Gateway", kommer det virtuella nätverk som distribueras med gatewayen att annonsera 501-prefix till lokalt.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Vad händer om jag överskrider prefixlängden för en ExpressRoute-anslutning?

Anslutningen mellan ExpressRoute-kretsen och gatewayen (och peer-virtuella nätverk som använder Gateway-överföring, om tillämpligt) går nedåt. Den återupprättas när prefixlängden inte längre överskrids.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Kan jag filtrera vägar som kommer från mitt lokala nätverk?

Det enda sättet att filtrera/inkludera vägar finns på den lokala Edge-routern. Användardefinierade vägar kan läggas till i VNet för att påverka viss routning, men detta kommer att vara statiskt och inte en del av BGP-annonsen.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Erbjuder ExpressRoute ett Serviceavtal (SLA)?

Mer information finns på sidan om [service avtal för ExpressRoute](https://azure.microsoft.com/support/legal/sla/) .

## <a name="supported-services"></a>Tjänster som stöds

ExpressRoute stöder [tre routningsdomäner](expressroute-circuit-peerings.md) för olika typer av tjänster: privat peering, Microsoft-peering och offentlig peering (inaktuell).

### <a name="private-peering"></a>Privat peering

**Tillåtna**

* Virtuella nätverk, inklusive alla virtuella datorer och moln tjänster

### <a name="microsoft-peering"></a>Microsoft-peering

Om din ExpressRoute-krets är aktive rad för Microsoft-peering för Azure kan du komma åt de [offentliga IP-adressintervall](../virtual-network/public-ip-addresses.md#public-ip-addresses) som används i Azure över kretsen. Azure Microsoft-peering ger till gång till tjänster som finns i Azure (med geo-begränsningar beroende på din krets-SKU). Om du vill verifiera tillgänglighet för en speciell tjänst kan du kontrol lera dokumentationen för tjänsten för att se om det finns ett reserverat intervall publicerat för tjänsten. Leta sedan upp IP-intervallen för mål tjänsten och jämför med intervallen som anges i [Azure IP-intervallen och service märken – offentlig moln-XML-fil](https://www.microsoft.com/download/details.aspx?id=56519). Du kan också öppna ett support ärende för tjänsten i fråga för att klargöra.

**Tillåtna**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI – tillgängligt via en regional Azure-community, finns [här](/power-bi/service-admin-where-is-my-tenant-located) för hur du kan ta reda på den region där din Power BI-klient finns.
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Azures globala tjänster community)
* Offentliga Azure-IP-adresser för IaaS (Virtual Machines, Virtual Network gatewayer, belastnings utjämning osv.)  
* De flesta andra Azure-tjänster stöds också. Kontrol lera direkt med den tjänst som du vill använda för att verifiera stödet.

**Stöds inte:**

* CDN
* Azure Front Door
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* Multi-Factor Authentication-Server (bakåtkompatibelt)
* Traffic Manager
* Logic Apps

### <a name="public-peering"></a>Offentlig peering

Offentlig peering har inaktiverats på nya ExpressRoute-kretsar. Azure-tjänster är nu tillgängliga på Microsoft-peering. Om du har skapat en krets som har skapats innan offentlig peering är inaktuell, kan du välja att använda Microsoft peering eller offentlig peering, beroende på vilka tjänster du vill ha.

Mer information och konfigurations steg för offentlig peering finns i [ExpressRoute offentlig peering](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Varför ser jag status "annonserade offentliga prefix" som "verifiering krävs" när jag konfigurerar Microsoft-peering?

Microsoft kontrollerar om de angivna annonserade offentliga prefixen och peer-ASN (eller kund-ASN) är tilldelade till dig i Internet-routningstabellen. Om du hämtar de offentliga prefixen från en annan entitet och om tilldelningen inte är registrerad i routningstabellen, slutförs inte den automatiska verifieringen och kräver manuell verifiering. Om den automatiska valideringen Miss lyckas visas meddelandet "validering krävs".

Om du ser meddelandet "validering krävs" samlar du in dokumenten som visar att de offentliga prefixen har tilldelats till din organisation av den entitet som anges som ägare till prefixen i routningstabellen och skickar dessa dokument för manuell verifiering genom att öppna ett support ärende som visas nedan.

![Skärm bild som visar en ny supportbegäran (support ärende) för "bevis på ägarskap för offentliga prefix".](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Stöds Dynamics 365 på ExpressRoute?

Dynamics 365-och Common Data Service-miljöer (CD) är värdbaserade på Azure och därför drar kunderna nytta av det underliggande ExpressRoute-stödet för Azure-resurser. Du kan ansluta till dess tjänst slut punkter om ditt router-filter innehåller Azure-regionerna som dina Dynamics 365/CD-miljöer finns i.

> [!NOTE]
> [ExpressRoute Premium](#expressroute-premium) krävs **inte** för Dynamics 365-anslutning via Azure ExpressRoute om ExpressRoute-kretsen har distribuerats inom samma [naturpolitisk region](./expressroute-locations-providers.md#expressroute-locations).

## <a name="data-and-connections"></a>Data och anslutningar

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Finns det några begränsningar för mängden data som jag kan överföra med ExpressRoute?

Vi har inte angett någon gräns för mängden data som ska överföras. Se [pris information](https://azure.microsoft.com/pricing/details/expressroute/) för information om bandbredds taxa.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Vilka anslutnings hastigheter stöds av ExpressRoute?

Bandbredds erbjudanden som stöds:

50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps

### <a name="which-service-providers-are-available"></a>Vilka tjänst leverantörer är tillgängliga?

Se [ExpressRoute-partner och platser](expressroute-locations.md) för att få en lista över tjänst leverantörer och platser.

## <a name="technical-details"></a>Teknisk information

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Vilka är de tekniska kraven för att ansluta min lokala plats till Azure?

Mer information finns på [sidan för ExpressRoute](expressroute-prerequisites.md) -krav.

### <a name="are-connections-to-expressroute-redundant"></a>Är anslutningar till ExpressRoute redundanta?

Ja. Varje ExpressRoute-krets har ett redundant par med kors anslutningar konfigurerade för att ge hög tillgänglighet.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Kommer jag att förlora anslutning om en av mina ExpressRoute-länkar Miss känner?

Du kommer inte att förlora anslutningen om någon av kors anslutningarna Miss lyckas. Det finns en redundant anslutning som stöder belastningen på nätverket och ger hög tillgänglighet för din ExpressRoute-krets. Du kan också skapa en krets på en annan peering-plats för att uppnå återhämtning på krets nivå.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Hur gör jag för att implementera redundans på privat peering?

Flera ExpressRoute-kretsar från olika peering-platser eller upp till fyra anslutningar från samma peering-plats kan anslutas till samma virtuella nätverk för att tillhandahålla hög tillgänglighet om en enda krets blir otillgänglig. Du kan sedan [tilldela högre vikter](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection) till en av de lokala anslutningarna för att föredra en speciell krets. Vi rekommenderar starkt att kunderna installerar minst två ExpressRoute-kretsar för att undvika enskilda felpunkter. 

Se [här](./designing-for-high-availability-with-expressroute.md) för att utforma för hög tillgänglighet [och för att utforma](./designing-for-disaster-recovery-with-expressroute-privatepeering.md) för haveri beredskap.  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Hur implementerar jag redundans på Microsoft-peering?

Vi rekommenderar starkt att du använder Microsoft-peering för att få åtkomst till Azures offentliga tjänster som Azure Storage eller Azure SQL, samt kunder som använder Microsoft-peering för att Microsoft 365 att de implementerar flera kretsar på olika peering-platser för att undvika enskilda fel punkter. Kunder kan antingen annonsera samma prefix på båda kretsarna och använda [som sökväg förväntat](./expressroute-optimize-routing.md#solution-use-as-path-prepending) eller annonsera olika prefix för att fastställa sökväg lokalt.

Se [här](./designing-for-high-availability-with-expressroute.md) för att utforma för hög tillgänglighet.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Hur gör jag för att garantera hög tillgänglighet på ett virtuellt nätverk som är anslutet till ExpressRoute?

Du kan få hög tillgänglighet genom att ansluta upp till fyra ExpressRoute-kretsar på samma peering-plats till ditt virtuella nätverk, eller genom att ansluta ExpressRoute-kretsar på olika peering-platser (till exempel Singapore, Singapore2) till ditt virtuella nätverk. Om en ExpressRoute-krets kraschar går anslutningen över till en annan ExpressRoute-krets. Trafik som lämnar ditt virtuella nätverk dirigeras som standard baserat på likvärdig kostnad routning med flera sökvägar (ECMP). Du kan använda anslutnings vikt för att föredra en krets till en annan. Mer information finns i [optimera ExpressRoute-routning](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Hur gör jag för att se till att min trafik som är avsedd för Azures offentliga tjänster som Azure Storage och Azure SQL på Microsoft peering eller offentlig peering föredras på ExpressRoute-sökvägen?

Du måste implementera attributet *lokal inställning* på din router (er) för att säkerställa att sökvägen från lokal till Azure alltid prioriteras på dina ExpressRoute-kretsar.

Se ytterligare information [här](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings) om val av BGP-sökvägar och vanliga konfigurationer av routrar. 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Måste jag beställa två fysiska anslutningar mellan mitt lokala nätverk och Microsoft om jag inte är samordnad i ett moln utbyte och min tjänst leverantör har en punkt-till-punkt-anslutning?

Om din tjänst leverantör kan upprätta två virtuella Ethernet-kretsar över den fysiska anslutningen behöver du bara en fysisk anslutning. Den fysiska anslutningen (t. ex. en optisk fiber) avslutas på en Layer 1-enhet (se bilden). De två virtuella Ethernet-kretsarna är taggade med olika VLAN-ID: n, ett för den primära kretsen och en för den sekundära. Dessa VLAN-ID: n finns i det yttre 802.1 Q Ethernet-huvudet. Det inre 802.1 Q Ethernet-huvudet (visas inte) mappas till en speciell [ExpressRoute-routningsdomän](expressroute-circuit-peerings.md).

![Diagram som markerar de primära och sekundära virtuella kretsarna för Layer 1 (L1) som utgör den fysiska anslutningen mellan växlarna på en kunds webbplats och en ExpressRoute plats.](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kan jag utöka ett av mina VLAN till Azure med ExpressRoute?

Nej. Vi stöder inte Layer 2 anslutnings tillägg till Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kan jag ha fler än en ExpressRoute-krets i min prenumeration?

Ja. Du kan ha mer än en ExpressRoute-krets i din prenumeration. Standardvärdet är inställt på 10. Du kan kontakta Microsoft Support för att öka gränsen, om det behövs.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kan jag ha ExpressRoute-kretsar från olika tjänst leverantörer?

Ja. Du kan ha ExpressRoute-kretsar med många tjänst leverantörer. Varje ExpressRoute-krets är bara kopplad till en tjänst leverantör. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Jag ser två ExpressRoute-peering-platser i samma tunnelbane linje, till exempel Singapore och Singapore2. Vilken peering-plats ska jag välja för att skapa min ExpressRoute-krets?
Om din tjänst leverantör erbjuder ExpressRoute på båda platserna kan du arbeta med din Provider och välja antingen plats för att ställa in ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kan jag ha flera ExpressRoute-kretsar i samma tunnelbane linje? Kan jag länka dem till samma virtuella nätverk?

Ja. Du kan ha flera ExpressRoute-kretsar med samma eller olika tjänst leverantörer. Om tunnelbane linjen har flera ExpressRoute-peering-platser och kretsarna skapas på olika peering-platser, kan du länka dem till samma virtuella nätverk. Om kretsarna skapas på samma peering-plats kan du länka upp till fyra kretsar till samma virtuella nätverk.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Hur gör jag för att ansluta mina virtuella nätverk till en ExpressRoute-krets

De grundläggande stegen är:

* Upprätta en ExpressRoute-krets och låt tjänst leverantören aktivera den.
* Du eller providern måste konfigurera BGP-peering (s).
* Länka det virtuella nätverket till ExpressRoute-kretsen.

Mer information finns i [ExpressRoute-arbetsflöden för krets etablering och krets tillstånd](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Finns det anslutnings gränser för min ExpressRoute-krets?

Ja. Artikeln [ExpressRoute partners och platser](expressroute-locations.md) ger en översikt över anslutnings gränserna för en ExpressRoute-krets. Anslutningen för en ExpressRoute-krets är begränsad till en enda politisk region. Anslutningen kan utökas för att korsa politiska regioner genom att aktivera funktionen ExpressRoute Premium.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kan jag länka till fler än ett virtuellt nätverk till en ExpressRoute-krets?

Ja. Du kan ha upp till 10 virtuella nätverks anslutningar på en standard ExpressRoute-krets och upp till 100 på en [Premium ExpressRoute-krets](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Jag har flera Azure-prenumerationer som innehåller virtuella nätverk. Kan jag ansluta virtuella nätverk som finns i separata prenumerationer till en enda ExpressRoute-krets?

Ja. Du kan länka upp till 10 virtuella nätverk i samma prenumeration som kretsen eller olika prenumerationer med en enda ExpressRoute-krets. Den här gränsen kan ökas genom att aktivera ExpressRoute Premium-funktionen. Observera att anslutnings-och bandbredds avgifter för den dedikerade kretsen kommer att användas för ExpressRoute-kretsens ägare. alla virtuella nätverk delar samma bandbredd.

Mer information finns i [dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Jag har flera Azure-prenumerationer som är kopplade till olika Azure Active Directory klienter eller Enterprise-avtal registreringar. Kan jag ansluta virtuella nätverk som finns i separata klienter och registreringar till en enda ExpressRoute-krets som inte tillhör samma klient organisation eller registrering?

Ja. ExpressRoute-auktoriseringar kan omfatta prenumerations-, klient-och registrerings gränser utan ytterligare konfiguration som krävs. Observera att anslutnings-och bandbredds avgifter för den dedikerade kretsen kommer att användas för ExpressRoute-kretsens ägare. alla virtuella nätverk delar samma bandbredd.

Mer information finns i [dela en ExpressRoute-krets över flera prenumerationer](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Är virtuella nätverk anslutna till samma krets isolerade från varandra?

Nej. Från ett Dirigerings perspektiv är alla virtuella nätverk som är länkade till samma ExpressRoute-krets en del av samma routningsdomän och är inte isolerade från varandra. Om du behöver routnings isolering måste du skapa en separat ExpressRoute-krets.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kan jag ha ett virtuellt nätverk som är anslutet till fler än en ExpressRoute-krets?

Ja. Du kan länka ett enda virtuellt nätverk med upp till fyra ExpressRoute-kretsar i antingen samma eller olika peering-platser. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag komma åt Internet från mina virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Om du inte har annonserat standard vägar (0.0.0.0/0) eller prefix för Internet väg via BGP-sessionen kan du ansluta till Internet från ett virtuellt nätverk som är länkat till en ExpressRoute-krets.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kan jag blockera Internet anslutning till virtuella nätverk som är anslutna till ExpressRoute-kretsar?

Ja. Du kan annonsera standard vägar (0.0.0.0/0) för att blockera all Internet anslutning till virtuella datorer som distribueras i ett virtuellt nätverk och dirigera all trafik via ExpressRoute-kretsen.

> [!NOTE]
> Om den annonserade vägen på 0.0.0.0/0 återkallas från vägarna som annonseras (till exempel på grund av ett avbrott eller en felaktig konfiguration), kommer Azure att tillhandahålla en [system väg](../virtual-network/virtual-networks-udr-overview.md#system-routes) till resurser på den anslutna Virtual Network för att tillhandahålla anslutning till Internet.  För att säkerställa att utgående trafik till Internet blockeras, rekommenderar vi att du placerar en nätverks säkerhets grupp på alla undernät med en utgående regel för Neka för Internet trafik.

Om du annonserar standard vägar tvingar vi trafik till tjänster som erbjuds via Microsoft-peering (t. ex. Azure Storage och SQL DB) tillbaka till dina lokaler. Du måste konfigurera dina routrar för att returnera trafik till Azure via Microsoft peering-sökvägen eller via Internet. Om du har aktiverat en tjänst slut punkt för tjänsten tvingas trafiken till tjänsten inte till dina lokaler. Trafiken förblir i Azures stamnät nätverk. Mer information om tjänst slut punkter finns i [tjänst slut punkter för virtuella nätverk](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Kan virtuella nätverk som är länkade till samma ExpressRoute-krets prata med varandra?

Ja. Virtuella datorer som distribueras i virtuella nätverk som är anslutna till samma ExpressRoute-krets kan kommunicera med varandra. Vi rekommenderar att du konfigurerar [peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) för att under lätta kommunikationen.

### <a name="can-i-set-up-a-site-to-site-vpn-connection-to-my-virtual-network-in-conjunction-with-expressroute"></a>Kan jag konfigurera en VPN-anslutning från plats till plats till mitt virtuella nätverk tillsammans med ExpressRoute?

Ja. ExpressRoute kan samverka med plats-till-plats-VPN. Se [Konfigurera ExpressRoute-och plats-till-plats-sambefintliga anslutningar](expressroute-howto-coexist-resource-manager.md).

### <a name="how-do-i-enable-routing-between-my-site-to-site-vpn-connection-and-my-expressroute"></a>Hur gör jag för att aktivera routning mellan min plats-till-plats-VPN-anslutning och min ExpressRoute?

Om du vill aktivera routning mellan din gren som är ansluten till Expressoute och din gren är ansluten till en plats-till-plats-VPN-anslutning måste du konfigurera [Azure Route Server](../route-server/expressroute-vpn-support.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Varför finns det en offentlig IP-adress som är associerad med ExpressRoute-gatewayen på ett virtuellt nätverk?

Den offentliga IP-adressen används endast för intern hantering och utgör ingen säkerhets exponering för det virtuella nätverket.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Finns det några begränsningar för hur många vägar jag kan annonsera?

Ja. Vi accepterar upp till 4000 väg prefix för privat peering och 200 för Microsoft-peering. Du kan öka detta till 10 000 vägar för privat peering om du aktiverar ExpressRoute Premium-funktionen.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Finns det begränsningar för IP-intervall som jag kan annonsera över BGP-sessionen?

Vi accepterar inte privata prefix (RFC1918) för Microsoft-peering BGP-sessionen. Vi accepterar alla prefixlängd (upp till/32) på både Microsoft och den privata peeringen.

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Vad händer om jag överskrider BGP-gränserna?

BGP-sessioner kommer att tas bort. De kommer att återställas när prefixet räknas under gränsen.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Vad är ExpressRoute BGP Hold Time? Kan den justeras?

Spärr tiden är 180. Keep-Alive-meddelanden skickas var 60: e sekund. Detta är fasta inställningar på Microsoft-sidan som inte kan ändras. Det är möjligt att du konfigurerar olika timers och att parametrarna för BGP-sessionen kommer att förhandlas enligt detta.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kan jag ändra bandbredden för en ExpressRoute-krets?

Ja, du kan försöka öka bandbredden för ExpressRoute-kretsen i Azure Portal eller genom att använda PowerShell. Om det finns tillgänglig kapacitet på den fysiska porten som din krets skapades på, lyckades ändringen. 

Om ändringen Miss lyckas innebär det att det inte finns tillräckligt med kapacitet kvar på den aktuella porten och du måste skapa en ny ExpressRoute-krets med större bandbredd, eller att det inte finns någon ytterligare kapacitet på den platsen, i så fall kan du inte öka bandbredden. 

Du måste också följa upp med din anslutnings leverantör för att säkerställa att de uppdaterar begränsningarna i sina nätverk för att stödja bandbredds ökningen. Du kan dock inte minska bandbredden för din ExpressRoute-krets. Du måste skapa en ny ExpressRoute-krets med lägre bandbredd och ta bort den gamla kretsen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Hur gör jag för att ändra bandbredden för en ExpressRoute-krets?

Du kan uppdatera bandbredden för ExpressRoute-kretsen med hjälp av REST API eller PowerShell-cmdleten.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Vad är ExpressRoute Premium?

ExpressRoute Premium är en samling av följande funktioner:

* Den ökade gränsen för vägvals tabellen från 4000 vägar till 10 000 vägar för privat peering.
* Ökat antal virtuella nätverk-och ExpressRoute-Global Reach-anslutningar som kan aktive ras på en ExpressRoute-krets (Standardvärdet är 10). Mer information finns i [ExpressRoute Limits](#limits) -tabellen.
* Anslutning till Microsoft 365
* Global anslutning över Microsoft Core-nätverket. Nu kan du länka ett VNet i en politisk region med en ExpressRoute-krets i en annan region.<br>
    **Exempel:**

    *  Du kan länka ett VNet som skapats i Europa, västra till en ExpressRoute-krets som skapats i Silicon dal. 
    *  På Microsofts peering annonseras prefix från andra politiska regioner, så att du kan ansluta till, till exempel SQL Azure i Europa, väst från en krets i kisel dal.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Hur många virtuella nätverk-och ExpressRoute Global Reach-anslutningar kan jag aktivera på en ExpressRoute-krets om jag har aktiverat ExpressRoute Premium?

Följande tabeller visar ExpressRoute-gränser och antalet virtuella nätverk-och ExpressRoute-Global Reach anslutningar per ExpressRoute-krets:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Hur gör jag för att aktivera ExpressRoute Premium?

ExpressRoute Premium-funktioner kan aktive ras när funktionen är aktive rad och kan stängas av genom att uppdatera kretsens tillstånd. Du kan aktivera ExpressRoute Premium när kretsen skapas, eller så kan du anropa REST API/PowerShell-cmdleten.

### <a name="how-do-i-disable-expressroute-premium"></a>Hur gör jag för att inaktivera ExpressRoute Premium?

Du kan inaktivera ExpressRoute Premium genom att anropa REST API-eller PowerShell-cmdleten. Du måste se till att du har skalat din anslutning måste uppfylla standard gränserna innan du inaktiverar ExpressRoute Premium. Om din användning skalas bortom standard gränserna, Miss lyckas begäran om att inaktivera ExpressRoute Premium.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kan jag välja vilka funktioner jag vill ha från Premium-funktionen?

Nej. Du kan inte välja funktionerna. Vi aktiverar alla funktioner när du aktiverar ExpressRoute Premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Hur mycket kostar ExpressRoute Premium?

Se [pris information](https://azure.microsoft.com/pricing/details/expressroute/) för kostnad.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Betalar jag för ExpressRoute Premium förutom standard avgifterna för ExpressRoute?

Ja. ExpressRoute Premium-avgifterna gäller för de ExpressRoute-krets avgifter och avgifter som krävs av anslutnings leverantören.

## <a name="expressroute-local"></a>ExpressRoute-lokal
### <a name="what-is-expressroute-local"></a>Vad är ExpressRoute Local?
ExpressRoute Local är en SKU av ExpressRoute-kretsen, förutom standard-SKU: n och Premium-SKU: n. En viktig funktion i lokal är att en lokal krets på en ExpressRoute-peering-plats ger dig åtkomst endast till en eller två Azure-regioner i eller nära samma tunnelbane linje. En standard-krets ger däremot till gång till alla Azure-regioner i ett politiskt område och en Premium-krets till alla Azure-regioner globalt. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Vilka är fördelarna med ExpressRoute Local?
Du måste betala utgående data överföring för din standard-eller Premium ExpressRoute-krets, men du betalar inte utgående data överföring separat för din ExpressRoute-lokala krets. Priset för ExpressRoute Local inkluderar med andra ord avgifter för data överföring. ExpressRoute Local är en mer ekonomisk lösning om du har en enorm mängd data som ska överföras och du kan ta med dina data över en privat anslutning till en ExpressRoute-peering-plats nära önskade Azure-regioner. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Vilka funktioner är tillgängliga och vilka som inte finns på ExpressRoute lokala?
Jämfört med en vanlig ExpressRoute-krets har en lokal krets samma uppsättning funktioner, förutom:
* Omfattning av åtkomst till Azure-regioner enligt beskrivningen ovan
* ExpressRoute-Global Reach finns inte på den lokala datorn

ExpressRoute Local har också samma begränsningar för resurser (t. ex. antalet virtuella nätverk per krets) som standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Var finns ExpressRoute lokalt och vilka Azure-regioner är varje peering-plats mappad till?
ExpressRoute Local är tillgängligt på peering-platserna där en eller två Azure-regioner stängs av. Den är inte tillgänglig på en peering-plats där det inte finns någon Azure-region i detta tillstånd eller region eller land/region. Se exakta mappningar på [sidan platser](expressroute-locations-providers.md).  

## <a name="expressroute-for-microsoft-365"></a>ExpressRoute för Microsoft 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Hur gör jag för att skapa en ExpressRoute-krets för att ansluta till Microsoft 365 Services?

1. Granska [sidan ExpressRoute-krav](expressroute-prerequisites.md) för att se till att du uppfyller kraven.
2. För att säkerställa att dina anslutnings behov är uppfyllda granskar du listan över tjänst leverantörer och platser i artikeln [ExpressRoute partners och platser](expressroute-locations.md) .
3. Planera dina kapacitets krav genom att granska [nätverks planering och prestanda justering för Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance).
4. Följ stegen som listas i arbets flödena för att konfigurera anslutnings [ExpressRoute arbets flöden för krets etablering och krets tillstånd](expressroute-workflows.md).

> [!IMPORTANT]
> Kontrol lera att du har aktiverat ExpressRoute Premium-tillägget när du konfigurerar anslutningen till Microsoft 365 Services.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>Kan mina befintliga ExpressRoute-kretsar stödja anslutning till Microsoft 365 Services?

Ja. Din befintliga ExpressRoute-krets kan konfigureras för att stödja anslutning till Microsoft 365 tjänster. Kontrol lera att du har tillräcklig kapacitet för att ansluta till Microsoft 365 tjänster och att du har aktiverat Premium-tillägget. [Nätverks planering och prestanda justering för Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance) hjälper dig att planera dina anslutnings behov. Se även [skapa och ändra en ExpressRoute-krets](expressroute-howto-circuit-classic.md).

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>Vilka Microsoft 365s tjänster kan nås via en ExpressRoute-anslutning?

Se sidan [Microsoft 365 URL: er och IP-adressintervall](/microsoft-365/enterprise/urls-and-ip-address-ranges) för en uppdaterad lista över tjänster som stöds över ExpressRoute.

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Hur mycket kostar ExpressRoute för Microsoft 365 Services?

Microsoft 365 Services kräver Premium-tillägg för att vara aktiverat. Se [pris informations sidan](https://azure.microsoft.com/pricing/details/expressroute/) för kostnader.

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>Vilka regioner är ExpressRoute för Microsoft 365 som stöds i?

Mer information finns i [ExpressRoute-partners och-platser](expressroute-locations.md) .

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kan jag få åtkomst till Microsoft 365 via Internet, även om ExpressRoute har kon figurer ATS för min organisation?

Ja. Microsoft 365 tjänstens slut punkter går att komma åt via Internet, även om ExpressRoute har kon figurer ATS för nätverket. Kontakta din organisations nätverks team om nätverket på din plats är konfigurerat för att ansluta till Microsoft 365 Services via ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Hur kan jag planera för hög tillgänglighet för Microsoft 365 nätverks trafik på Azure-ExpressRoute?
Se rekommendationer för [hög tillgänglighet och redundans med Azure ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kan jag få åtkomst till Office 365 amerikanska GCC-tjänster (amerikanska myndigheter) via en ExpressRoute-krets för Azure-myndigheter?

Ja. Office 365 GCC-tjänstens slut punkter går att komma åt via Azure amerikanska myndigheters ExpressRoute. Du måste dock först öppna ett support ärende på Azure Portal för att tillhandahålla de prefix du planerar att annonsera till Microsoft. Din anslutning till Office 365 GCC-tjänster upprättas efter att support ärendet har lösts. 

## <a name="route-filters-for-microsoft-peering"></a>Flödes filter för Microsoft-peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Jag aktiverar Microsoft-peering för första gången, vilka vägar ser jag?

Du kommer inte att se några vägar. Du måste koppla ett väg filter till din krets för att kunna starta prefix annonseringar. Instruktioner finns i [Konfigurera väg filter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Jag aktiverade Microsoft-peering och nu försöker jag välja Exchange Online, men det ger mig ett fel som jag inte har behörighet att göra.

När du använder väg filter kan alla kunder aktivera Microsoft-peering. Men för att förbruka Microsoft 365 tjänster måste du ändå få behörighet genom att Microsoft 365.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Jag aktiverade Microsoft-peering före den 1 augusti 2017, hur kan jag dra nytta av väg filter?

Din befintliga krets fortsätter att annonsera om prefixen för Microsoft 365. Om du vill lägga till offentliga Azure-prefix över samma Microsoft-peering kan du skapa ett flödes filter, välja de tjänster som du behöver annonseras (inklusive de Microsoft 365-tjänster du behöver) och koppla filtret till din Microsoft-peering. Instruktioner finns i [Konfigurera väg filter för Microsoft-peering](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Jag har Microsoft-peering på en plats, nu försöker jag aktivera den på en annan plats och jag ser inga prefix.

* Microsoft-peering av ExpressRoute-kretsar som har kon figurer ATS före den 1 augusti 2017 kommer att ha alla tjänste prefix som annonseras via Microsoft-peering, även om det inte finns några väg filter definierade.

* Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inga prefix som annonseras förrän ett flödes filter är kopplat till kretsen. Inga prefix visas som standard.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>Sekretess

### <a name="does-the-expressroute-service-store-customer-data"></a>Lagrar ExpressRoute-tjänsten kund information?

Nej.
