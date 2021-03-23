---
title: 'Azure-ExpressRoute: krav för routning'
description: Den här sidan innehåller detaljerade krav för att konfigurera och hantera routning för ExpressRoute-kretsar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/19/2019
ms.author: duau
ms.openlocfilehash: c65825a6d8d2d7f9059e91a1f248367fa1788e1a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799504"
---
# <a name="expressroute-routing-requirements"></a>ExpressRoute-routningskrav
För att kunna ansluta till Microsofts molntjänster med ExpressRoute måste du konfigurera och hantera routning. Vissa anslutningsleverantörer erbjuder konfigurering och hantering av routning som en hanterad tjänst. Fråga din anslutningsleverantör om de erbjuder denna tjänst. Om inte måste du uppfylla följande krav:

Referera till artikeln [Kretsar och routningsdomäner](expressroute-circuit-peerings.md) för en beskrivning av de routningssessioner som behöver ställas in för att förenkla anslutningen.

> [!NOTE]
> Microsoft stöder inte några protokoll för routerredundans (t.ex., HSRP, VRRP) för konfigurationer med hög tillgänglighet. Vi förlitar oss på ett redundant par med BGP-sessioner per peering för hög tillgänglighet.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>IP-adresser som används för peerings
Du behöver reservera några IP-adressblock för att kunna konfigurera routning mellan ditt nätverk och Microsofts Enterprise-gränsroutrar (MSEE). Det här avsnittet innehåller en lista med krav och en beskrivning av reglerna för hur dessa IP-adresser måste hämtas och användas.

### <a name="ip-addresses-used-for-azure-private-peering"></a>IP-adresser för Azures privata peering
Du kan antingen använda privata IP-adresser eller offentliga IP-adresser för att konfigurera peerings. Adressintervallet som används för att konfigurera routning får inte överlappa de adressintervall som användes för att skapa virtuella nätverk i Azure. 

* IPv6
    * Du måste reservera ett /29-undernät eller två /30-undernät för routningsgränssnitten.
    * Undernät som används för routning kan vara antingen privata IP-adresser eller offentliga IP-adresser.
    * Undernäten får inte stå i konflikt med det intervall som reserverats av kunden för användning i Microsoft-molnet.
    * Om ett /29-undernät används delas det upp i två /30-undernät. 
      * Det första /30-undernätet används för den primära länken och det andra/30-undernätet används för den sekundära länken.
      * För båda /30-undernäten gäller att du måste använda den första IP-adressen för /30-undernätet på routern. Microsoft använder den andra IP-adressen för /30-undernätet för att konfigurera en BGP-session.
      * Du måste konfigurera båda BGP-sessionerna för att vårt [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltigt.
* IPv6
    * Du måste reservera ett/125-undernät eller två/126-undernät för routningsgränssnitt.
    * Undernät som används för routning kan vara antingen privata IP-adresser eller offentliga IP-adresser.
    * Undernäten får inte stå i konflikt med det intervall som reserverats av kunden för användning i Microsoft-molnet.
    * Om ett /125-undernät används delas det upp i två /126-undernät. 
      * Det första/126-undernätet används för den primära länken och det andra/30-undernätet används för den sekundära länken.
      * För bägge /126-undernäten gäller att du måste använda den första IP-adressen för /126-undernätet på din router. Microsoft använder den andra IP-adressen för /126-undernätet för att konfigurera en BGP-session.
      * Du måste konfigurera båda BGP-sessionerna för att vårt [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltigt.

#### <a name="example-for-private-peering"></a>Exempel på privat peering
Om du väljer att använda a.b.c.d/29 för att konfigurera peeringen delas den upp i två /30-undernät. I följande exempel ser du hur ett. b. c. d/29-undernät används:

* a.b.c.d/29 delas upp till a.b.c.d/30 och a.b.c.d+4/30 samt skickas till Microsoft via etablerings-API:er.
  * Du använder a.b.c.d+1 som VRF IP för din primära PE och Microsoft kommer att använda a.b.c.d+2 som VRF IP för den primära MSEE:n.
  * Du använder a.b.c.d+5 som VRF IP för din sekundära PE och Microsoft kommer att använda a.b.c.d+6 som VRF IP för den sekundära MSEE:n.

Tänk dig ett fall där du väljer 192.168.100.128/29 för att konfigurera privat peering. 192.168.100.128/29 innehåller adresser från 192.168.100.128 till 192.168.100.135, däribland:

* 192.168.100.128/30 som tilldelas till link1, där leverantören använder 192.168.100.129 och Microsoft använder 192.168.100.130.
* 192.168.100.132/30 tilldelas till link2, där leverantören använder 192.168.100.133 och Microsoft använder 192.168.100.134.

### <a name="ip-addresses-used-for-microsoft-peering"></a>IP-adresser för Microsofts peering
Du måste använda offentliga IP-adresser som du äger när du konfigurerar BGP-sessionerna. Microsoft måste kunna verifiera ditt ägarskap till IP-adresserna via RIR (Routing Internet Registries) och IIR (Internet Routing Registries).

* IP-adresser i portalen för annonserade offentliga prefix för Microsoft Peering kommer att skapa ACL:er för Microsoft-kärnroutrar för att tillåta inkommande trafik från dessa IP-adresser. 
* Du måste använda ett unikt /29 (IPv4) eller /125 (IPv6)-undernät eller två /30 (IPv4) eller /126 (IPv6)-undernät till att konfigurera BGP-peeringen för varje peering per ExpressRoute-krets (om du har fler än en).
* Om ett /29-undernät används delas det upp i två /30-undernät.
* Det första /30-undernätet används för den primära länken och det andra/30-undernätet används för den sekundära länken.
* För båda /30-undernäten gäller att du måste använda den första IP-adressen för /30-undernätet på routern. Microsoft använder den andra IP-adressen för /30-undernätet för att konfigurera en BGP-session.
* Om ett /125-undernät används delas det upp i två /126-undernät.
* Det första /126-undernätet används för den primära länken och det andra/126-undernätet används för den sekundära länken.
* För bägge /126-undernäten gäller att du måste använda den första IP-adressen för /126-undernätet på din router. Microsoft använder den andra IP-adressen för /126-undernätet för att konfigurera en BGP-session.
* Du måste konfigurera båda BGP-sessionerna för att vårt [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltigt.

### <a name="ip-addresses-used-for-azure-public-peering"></a>IP-adresser för Azures offentliga peering

> [!NOTE]
> Offentlig Azure-peering är inte tillgänglig för nya kretsar.
> 

Du måste använda offentliga IP-adresser som du äger när du konfigurerar BGP-sessionerna. Microsoft måste kunna verifiera ditt ägarskap till IP-adresserna via RIR (Routing Internet Registries) och IIR (Internet Routing Registries). 

* Du måste använda ett unikt /29-undernät eller två /30-undernät till att konfigurera BGP-peeringen för varje peering per ExpressRoute-krets (om du har fler än en). 
* Om ett /29-undernät används delas det upp i två /30-undernät. 
  * Det första /30-undernätet används för den primära länken och det andra/30-undernätet används för den sekundära länken.
  * För båda /30-undernäten gäller att du måste använda den första IP-adressen för /30-undernätet på routern. Microsoft använder den andra IP-adressen för /30-undernätet för att konfigurera en BGP-session.
  * Du måste konfigurera båda BGP-sessionerna för att vårt [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/) ska vara giltigt.

## <a name="public-ip-address-requirement"></a>Krav för offentliga IP-adress

### <a name="private-peering"></a>Privat peering
Du kan välja att använda offentliga eller privata IPv4-adresser för privat peering. Vi erbjuder isolering av din trafik från slutpunkt till slutpunkt, vilket innebär att adressöverlappning med andra kunder inte är möjligt med privat peering. De här adresserna annonseras inte till Internet. 

### <a name="microsoft-peering"></a>Microsoft-peering
Med Microsoft peering-sökvägen kan du ansluta till Microsofts moln tjänster. Listan över tjänster omfattar Microsoft 365 tjänster, till exempel Exchange Online, SharePoint Online, Skype för företag och Microsoft Teams. Microsoft stöder dubbelriktade anslutningar för Microsoft-peering. Trafik till Microsofts molntjänster måste använda giltiga offentliga IPv4-adresser innan de kommer in i Microsoft-nätverket.

Kontrollera att din IP-adress och ditt AS-nummer är registrerade på dig i något av följande register:

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](https://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](https://www.radb.net/)
* [ALTDB](https://altdb.net/)

Om dina prefixer och AS-nummer inte har tilldelats till dig i registren ovan måste du öppna ett supportärende för manuell validering av dina prefixer och ASN:er. Supporten behöver den dokumentation, som ett auktoriseringsdokument, som bevisar att du har behörighet att använda resurserna.

Ett privat AS-nummer tillåts med Microsoft-peering men kommer också att kräva manuell verifiering. Dessutom tar vi bort privata AS-nummer i AS PATH för de mottagna prefixen. Som ett resultat av detta kan du inte lägga till privata AS-nummer i AS PATH som [påverkar routningen för Microsoft-peering](expressroute-optimize-routing.md). SOM nummer 64496-64511 reserverat av IANA för dokumentations syfte tillåts inte heller i sökvägen.

> [!IMPORTANT]
> Annonsera inte samma offentliga IP-väg till det offentliga Internet och över ExpressRoute. För att minska risken för felaktig konfiguration som orsakar asymmetrisk routning, rekommenderar vi starkt att [NAT-IP-adresserna](expressroute-nat.md) som annonseras till Microsoft via ExpressRoute kommer från ett intervall som inte annonseras till Internet alls. Om detta inte är möjligt är det viktigt att se till att du annonserar ett mer särskilt intervall över ExpressRoute än det som finns på Internet-anslutningen. Förutom den offentliga vägen för NAT kan du även annonsera över ExpressRoute de offentliga IP-adresser som används av servrarna i ditt lokala nätverk som kommunicerar med Microsoft 365 slut punkter inom Microsoft. 
> 
> 

### <a name="public-peering-deprecated---not-available-for-new-circuits"></a>Offentlig peering (inaktuellt-inte tillgängligt för nya kretsar)
Med Azures offentliga peeringsökväg kan du ansluta till alla tjänster som finns i Azure med deras offentliga IP-adresser. Det inkluderar tjänster som finns i listan [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md) och tjänster med ISV:er i Microsoft Azure. Anslutningen till Microsoft Azure-tjänster vid offentlig peering initieras alltid från ditt nätverk till Microsoft-nätverket. Du måste använda offentliga IP-adresser för trafik till Microsoft-nätverk.

> [!IMPORTANT]
> Alla Azure PaaS-tjänster är tillgängliga via Microsoft-peering.
>   

Ett privat AS-nummer tillåts med offentlig peering.

## <a name="dynamic-route-exchange"></a>Dynamiskt routningsutbyte
Routningsutbytet kommer att ske via EBGP-protokollet. EBGP-sessioner upprättas mellan MSEE:erna och dina routrar. Autentisering av BGP-sessioner är inte något krav. Om det behövs kan en MD5-hash konfigureras. Se [Konfigurera routning](how-to-routefilter-portal.md) och [Kretsetablering av arbetsflöden och kretsstatus](expressroute-workflows.md) för information om att konfigurera BGP-sessioner.

## <a name="autonomous-system-numbers"></a>Autonoma systemnummer
Microsoft använder AS 12076 för offentliga Azure, privata Azure och Microsofts peering. Vi har reserverat ASN:er från 65515 till 65520 för intern användning. Både 16- och 32-bitars AS-nummer stöds.

Det finns inga krav på symmetri vid dataöverföring. Sökvägar vid vidarebefordran och retur kan passera olika routerpar. Identiska vägar måste annonseras från båda sidor över flera krets par som tillhör dig. Vägmåtten behöver inte vara identiska.

## <a name="route-aggregation-and-prefix-limits"></a>Vägsammanställning och begränsningar för prefix
Vi har stöd för upp till 4000 IPv4-prefix och 100 IPv6-prefix som annonseras till oss via Azures privata peering. Detta kan ökas upp till 10 000 IPv4-prefix om ExpressRoute Premium-tillägget är aktiverat. Vi kan acceptera upp till 200 prefix per BGP-session för Azures offentliga och Microsofts peering. 

BGP-sessionen kommer att tas bort om antalet prefix överskrider gränsen. Vi kommer endast att acceptera standardvägar på den privata peeringlänken. Leverantören måste filtrera ut standardvägen och privata IP-adresser (RFC 1918) från Azures offentliga och Microsofts peeringsökvägar. 

## <a name="transit-routing-and-cross-region-routing"></a>Överföringsroutning och routning mellan regioner
ExpressRoute kan inte konfigureras som överföringsroutrar. Du måste förlita dig på din anslutningsleverantör vid överföring av routningstjänster.

## <a name="advertising-default-routes"></a>Annonsering av standardvägar
Standardvägar tillåts bara i Azures privata peeringsessioner. I dessa fall kommer vi dirigera all trafik från associerade virtuella nätverk till ditt nätverk. Annonsering av standardvägar till privat peering resulterar i att Internetvägen från Azure blockeras. Du måste använda på ditt företags gräns till att dirigera trafik från och till Internet för tjänster som finns i Azure. 

 Om du vill aktivera anslutningar till andra Azure-tjänster och infrastrukturtjänster, måste något av följande objekt finnas på plats:

* Offentlig Azure-peering är aktiverat för att dirigera trafik till offentliga slut punkter.
* Du använder användardefinierad routning för att tillåta anslutning till Internet för varje undernät som kräver Internetanslutning.

> [!NOTE]
> Annonsering av standardvägar bryter Windows- och andra VM-licensaktiveringar. Följ anvisningarna [här](/archive/blogs/mast/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling) för kringgå detta.
> 
> 

## <a name="support-for-bgp-communities"></a><a name="bgp"></a>Stöd för BGP-communities
Det här avsnittet innehåller en översikt över hur BGP-communities kommer att användas med ExpressRoute. Microsoft kommer att annonsera vägar i offentliga och Microsofts peeringsökvägar med vägar som är taggade med lämpliga community-värden. Anledningen till att man gör detta samt information om community-värden beskrivs nedan. Microsoft använder dock inte några community-värden som är taggade på vägar som annonserats till Microsoft.

Om du ansluter till Microsoft via ExpressRoute på valfri peeringplats i en geopolitisk region, får du åtkomst till alla Microsoft-molntjänster i alla regioner inom den geopolitiska gränsen. 

Om du exempelvis har anslutit till Microsoft i Amsterdam via ExpressRoute, kommer du ha åtkomst till alla Microsoft-molntjänster som finns i Europa, norra och Europa, västra. 

Se sidan [ExpressRoute-partners och peeringplatser](expressroute-locations.md) för en detaljerad lista med geopolitiska regioner, associerade Azure-regioner och motsvarande ExpressRoute-peeringplatser.

Du kan köpa mer än en ExpressRoute-krets per geopolitisk region. Att ha flera anslutningar ger dig betydande fördelar med hög tillgänglighet tack vare den geografiska redundansen. I de fall där du har flera ExpressRoute-kretsar får du samma uppsättning prefix som annonseras från Microsoft på Microsoft peering och offentliga peering-sökvägar. Det innebär att du har flera sökvägar från ditt nätverk till Microsoft. Detta kan eventuellt medföra att icke-optimala beslut om routning tas i nätverket. Därmed kan du få icke-optimala anslutningsupplevelser till andra tjänster. Du kan använda community-värden för att fatta rätt beslut om routning och erbjuda [optimal routning till användare](expressroute-optimize-routing.md).

| **Microsoft Azure-region** | **Regional BGP-community** | **Storage BGP-community** | **SQL BGP-community** | **Cosmos DB BGP-community** | **Säkerhetskopiera BGP-community** |
| --- | --- | --- | --- | --- | --- |
| **Nordamerika** | |
| East US | 12076:51004 | 12076:52004 | 12076:53004 | 12076:54004 | 12076:55004 |
| USA, östra 2 | 12076:51005 | 12076:52005 | 12076:53005 | 12076:54005 | 12076:55005 |
| USA, västra | 12076:51006 | 12076:52006 | 12076:53006 | 12076:54006 | 12076:55006 |
| USA, västra 2 | 12076:51026 | 12076:52026 | 12076:53026 | 12076:54026 | 12076:55026 |
| USA, västra centrala | 12076:51027 | 12076:52027 | 12076:53027 | 12076:54027 | 12076:55027 |
| USA, norra centrala | 12076:51007 | 12076:52007 | 12076:53007 | 12076:54007 | 12076:55007 |
| USA, södra centrala | 12076:51008 | 12076:52008 | 12076:53008 | 12076:54008 | 12076:55008 |
| Central US | 12076:51009 | 12076:52009 | 12076:53009 | 12076:54009 | 12076:55009 |
| Kanada, centrala | 12076:51020 | 12076:52020 | 12076:53020 | 12076:54020 | 12076:55020 |
| Kanada, östra | 12076:51021 | 12076:52021 | 12076:53021 | 12076:54021 | 12076:55021 |
| **Sydamerika** | |
| Brasilien, södra | 12076:51014 | 12076:52014 | 12076:53014 | 12076:54014 | 12076:55014 |
| **Europa** | |
| Europa, norra | 12076:51003 | 12076:52003 | 12076:53003 | 12076:54003 | 12076:55003 |
| Europa, västra | 12076:51002 | 12076:52002 | 12076:53002 | 12076:54002 | 12076:55002 |
| Storbritannien, södra | 12076:51024 | 12076:52024 | 12076:53024 | 12076:54024 | 12076:55024 |
| Storbritannien, västra | 12076:51025 | 12076:52025 | 12076:53025 | 12076:54025 | 12076:55025 |
| Frankrike, centrala | 12076:51030 | 12076:52030 | 12076:53030 | 12076:54030 | 12076:55030 |
| Frankrike, södra | 12076:51031 | 12076:52031 | 12076:53031 | 12076:54031 | 12076:55031 |
| Schweiz, norra | 12076:51038 | 12076:52038 | 12076:53038 | 12076:54038 | 12076:55038 |
| Schweiz, västra | 12076:51039 | 12076:52039 | 12076:53039 | 12076:54039 | 12076:55039 | 
| Tyskland, norra | 12076:51040 | 12076:52040 | 12076:53040 | 12076:54040 | 12076:55040 | 
| Tyskland, västra centrala | 12076:51041 | 12076:52041 | 12076:53041 | 12076:54041 | 12076:55041 | 
| Östra Norge | 12076:51042 | 12076:52042 | 12076:53042 | 12076:54042 | 12076:55042 | 
| Norge, väst | 12076:51043 | 12076:52043 | 12076:53043 | 12076:54043 | 12076:55043 | 
| **Asien och stillahavsområdet** | |
| Asien, östra | 12076:51010 | 12076:52010 | 12076:53010 | 12076:54010 | 12076:55010 |
| Sydostasien | 12076:51011 | 12076:52011 | 12076:53011 | 12076:54011 | 12076:55011 |
| **Japan** | |
| Japan, östra | 12076:51012 | 12076:52012 | 12076:53012 | 12076:54012 | 12076:55012 |
| Japan, västra | 12076:51013 | 12076:52013 | 12076:53013 | 12076:54013 | 12076:55013 |
| **Australien** | |
| Australien, östra | 12076:51015 | 12076:52015 | 12076:53015 | 12076:54015 | 12076:55015 |
| Australien, sydöstra | 12076:51016 | 12076:52016 | 12076:53016 | 12076:54016 | 12076:55016 |
| **Australiensiska myndigheter** | |
| Australien, centrala | 12076:51032 | 12076:52032 | 12076:53032 | 12076:54032 | 12076:55032 |
| Australien, centrala 2 | 12076:51033 | 12076:52033 | 12076:53033 | 12076:54033 | 12076:55033 |
| **Indien** | |
| Södra Indien | 12076:51019 | 12076:52019 | 12076:53019 | 12076:54019 | 12076:55019 |
| Indien, västra | 12076:51018 | 12076:52018 | 12076:53018 | 12076:54018 | 12076:55018 |
| Indien, centrala | 12076:51017 | 12076:52017 | 12076:53017 | 12076:54017 | 12076:55017 |
| **Korea** | |
| Sydkorea, södra | 12076:51028 | 12076:52028 | 12076:53028 | 12076:54028 | 12076:55028 |
| Sydkorea, centrala | 12076:51029 | 12076:52029 | 12076:53029 | 12076:54029 | 12076:55029 |
| **Sydafrika**| |
| Sydafrika, norra | 12076:51034 | 12076:52034 | 12076:53034 | 12076:54034 | 12076:55034 |
| Sydafrika, västra | 12076:51035 | 12076:52035 | 12076:53035 | 12076:54035 | 12076:55035 |
| **UAE**| |
| Förenade Arabemiraten, norra | 12076:51036 | 12076:52036 | 12076:53036 | 12076:54036 | 12076:55036 |
| Förenade Arabemiraten Central | 12076:51037 | 12076:52037 | 12076:53037 | 12076:54037 | 12076:55037 |


Alla vägar som annonseras från Microsoft taggas med lämpligt community-värde. 

> [!IMPORTANT]
> Globala prefix taggas med lämpligt community-värde.
> 
> 

### <a name="service-to-bgp-community-value"></a>Tjänst till BGP community-värde
Förutom ovanstående taggar Microsoft också prefix baserat på vilken tjänst de tillhör. Detta gäller endast för Microsoft-peering. Tabellen nedan innehåller en mappning av tjänsten till community-värden för BGP. Du kan köra cmdleten "Get-AzBgpServiceCommunity" om du vill se en fullständig lista över de senaste värdena.

| **Tjänst** | **BGP-community värde** |
| --- | --- |
| Exchange Online\*\* | 12076:5010 |
| SharePoint Online\*\* | 12076:5020 |
| Skype för företag – Online\*\*/\*\*\* | 12076:5030 |
| CRM Online\*\*\*\* |12076:5040 |
| Globala Azure-tjänster\* | 12076:5050 |
| Azure Active Directory |12076:5060 |
| Azure Resource Manager |12076:5070 |
| Andra Office 365 Online Services * * | 12076:5100 |

\* Azures globala tjänster innehåller bara Azure-DevOps just nu.

\*\* Auktorisering krävs från Microsoft, se [Konfigurera väg filter för Microsoft-peering](how-to-routefilter-portal.md)

\*\*\* Den här gruppen publicerar även de vägar som krävs för Microsoft Teams Services.

\*\*\*\* CRM Online stöder Dynamics v 8.2 och nedan. För högre versioner väljer du den regionala communityn för dina Dynamics-distributioner.

> [!NOTE]
> Microsoft använder inte några community-värden för BGP som du har angett för vägar som annonseras till Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds"></a>Stöd för BGP-community i nationella moln

| **Regioner för nationella Azure-moln**| **BGP-community värde** |
| --- | --- |
| **AMERIKANSKA myndigheter** |  |
| US Gov, Arizona | 12076:51106 |
| USA Gov, Iowa | 12076:51109 |
| US Gov, Virginia | 12076:51105 |
| US Gov, Texas | 12076:51108 |
| USA DoD, centrala | 12076:51209 |
| USA DoD, östra | 12076:51205 |


| **Tjänst i nationella moln** | **BGP-community värde** |
| --- | --- |
| **AMERIKANSKA myndigheter** |  |
| Exchange Online |12076:5110 |
| sharepoint online |12076:5120 |
| Skype för företag – Online |12076:5130 |
| Azure Active Directory |12076:5160 |
| Andra Office 365 Online-tjänster |12076:5200 |

## <a name="next-steps"></a>Nästa steg
* Konfigurera ExpressRoute-anslutningen.
  
  * [Skapa och ändra en krets](expressroute-howto-circuit-arm.md)
  * [Skapa och ändra peering-konfiguration](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
