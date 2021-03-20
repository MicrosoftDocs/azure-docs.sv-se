---
title: 'Azure-ExpressRoute: design för hög tillgänglighet'
description: Den här sidan innehåller arkitektur rekommendationer för hög tillgänglighet när du använder Azure-ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 06/28/2019
ms.author: duau
ms.openlocfilehash: 3602c3944e8731263fbb55f024c276783950329f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92202369"
---
# <a name="designing-for-high-availability-with-expressroute"></a>Design för hög tillgänglighet med ExpressRoute

ExpressRoute har utformats för att ge hög tillgänglighet för att tillhandahålla bärvåg för privat nätverks anslutning till Microsoft-resurser. Det finns med andra ord ingen enskild felpunkt i ExpressRoute-sökvägen i Microsoft-nätverket. För att maximera tillgängligheten bör även kunden och service provider-segmentet för din ExpressRoute-krets vara konstruerat för hög tillgänglighet. I den här artikeln börjar vi med att titta på nätverks arkitektur för att skapa robust nätverks anslutning med en ExpressRoute, och sedan ska vi titta på de fin justerings funktioner som hjälper dig att förbättra din ExpressRoute-krets hög tillgänglighet.

>[!NOTE]
>Begreppen som beskrivs i den här artikeln gäller även när en ExpressRoute-krets skapas under Virtual WAN eller utanför den.
>

## <a name="architecture-considerations"></a>Arkitektur överväganden

Följande bild illustrerar det rekommenderade sättet att ansluta med hjälp av en ExpressRoute-krets för att maximera tillgängligheten för en ExpressRoute-krets.

 [![1]][1]

För hög tillgänglighet är det viktigt att upprätthålla redundansen för ExpressRoute-kretsen hela tiden från slut punkt till slut punkt. Med andra ord måste du upprätthålla redundans i ditt lokala nätverk och inte kompromissa med redundans inom tjänst leverantörens nätverk. Att upprätthålla redundans med minsta möjliga orsak till att undvika enskilda punkter av nätverks haverier. Om du har redundant strömförsörjning och kylning för nätverks enheterna kommer hög tillgänglighet att förbättras ytterligare.

### <a name="first-mile-physical-layer-design-considerations"></a>Design överväganden för första ommilering av fysiskt lager

 Om du avbryter både de primära och sekundära anslutningarna för en ExpressRoute-krets på samma kund lokal utrustning (CPE) kan du kompromissa med hög tillgänglighet i det lokala nätverket. Dessutom, om du konfigurerar både de primära och sekundära anslutningarna via samma port i en CPE (antingen genom att avsluta de två anslutningarna under olika under gränssnitt eller genom att slå samman de två anslutningarna i partner nätverket), tvingar du partner att kompromissa med hög tillgänglighet för nätverks segmentet också. Den här kompromissen illustreras i följande bild.

[![2]][2]

Å andra sidan, om du avslutar de primära och sekundära anslutningarna för en ExpressRoute-krets på olika geografiska platser, kan du kompromissa med nätverks prestandan för anslutningen. Om trafiken aktivt belastningen bal anse ras över de primära och sekundära anslutningarna som har avslut ATS på olika geografiska platser, skulle en potentiell stor skillnad i nätverks fördröjningen mellan de två Sök vägarna leda till sämre nätverks prestanda. 

För geo-redundanta design överväganden, se [design för haveri beredskap med ExpressRoute][DR].

### <a name="active-active-connections"></a>Aktiva aktiva anslutningar

Microsoft Network är konfigurerat för att använda de primära och sekundära anslutningarna till ExpressRoute-kretsar i aktivt-aktivt läge. Genom dina väg annonseringar kan du dock tvinga redundanta anslutningar för en ExpressRoute-krets att köras i aktivt läge. Annonsera mer exakta vägar och BGP AS-sökvägen är beroende av de vanliga tekniker som används för att göra en sökväg som föredras över den andra.

För att förbättra hög tillgänglighet rekommenderar vi att du använder båda anslutningarna för en ExpressRoute-krets i aktivt-aktivt läge. Om du tillåter anslutningarna att köras i aktivt läge, kommer Microsoft Network att belastningsutjämna trafiken mellan anslutningarna per flöde.

Att köra de primära och sekundära anslutningarna för en ExpressRoute-krets i aktivt läge för passivt läge är risken för att båda anslutningarna Miss lyckas på grund av ett haveri i den aktiva sökvägen. Vanliga orsaker till växling vid växling är brist på aktiv hantering av den passiva anslutningen och passiv anslutning annonserar inaktuella vägar.

Du kan också köra de primära och sekundära anslutningarna för en ExpressRoute-krets i aktivt-aktivt läge, vilket resulterar i endast hälften av flödena som Miss lyckas och som dirigeras om, efter ett ExpressRoute anslutnings problem. Det innebär att aktivt-aktivt läge bidrar avsevärt till att öka genomsnitts tiden för återställning (MTTR).

### <a name="nat-for-microsoft-peering"></a>NAT för Microsoft-peering 

Microsoft-peering är utformad för kommunikation mellan offentliga slut punkter. Lokala privata slut punkter är vanligt vis översatta för nätverks adresser (NATed) med offentlig IP i kund-eller partner nätverket innan de kommunicerar via Microsoft-peering. Förutsatt att du använder både de primära och sekundära anslutningarna i aktivt-aktivt läge, där och hur du NAT har påverkan på hur snabbt du återställer efter ett fel i en av ExpressRoute-anslutningarna. Två olika NAT-alternativ illustreras i följande figur:

[![3]][3]

I alternativ 1 tillämpas NAT när trafiken mellan de primära och sekundära anslutningarna i ExpressRoute har delats upp. För att uppfylla de tillstånds känsliga kraven i NAT används oberoende NAT-pooler mellan de primära och sekundära enheterna så att RETUR trafiken kommer till samma gräns enhet genom vilken flödet har passerat.

I alternativ 2 används en vanlig NAT-pool innan trafiken delas mellan de primära och sekundära anslutningarna i ExpressRoute. Det är viktigt att du är medveten om att den gemensamma NAT-poolen innan du delar upp trafiken inte innebär att det innebär att det inte går att införa en enskild felpunkt för att kompromissa med hög tillgänglighet.

Med alternativet 1, efter ett ExpressRoute-anslutningsfel, är möjligheten att kontakta motsvarande NAT-pool bruten. Därför måste alla brutna flöden återupprättas antingen av TCP-eller program lagret efter motsvarande tids gräns för fönster. Om någon av NAT-poolerna används för att ansluta till någon av de lokala servrarna och om motsvarande anslutning inte kunde slutföras, går det inte att nå lokala servrar från Azure förrän anslutningen har åtgärd ATS.

Med alternativet 2 kan NAT bli tillgängligt även efter ett primärt eller sekundärt anslutnings problem. Därför kan själva nätverks lagret omdirigera paketen och hjälpa till att snabbare återställa efter fel. 

> [!NOTE]
> Om du använder NAT-alternativ 1 (oberoende NAT-pooler för primära och sekundära ExpressRoute-anslutningar) och mappar en port för en IP-adress från en av NAT-poolen till en lokal server går det inte att komma åt servern via ExpressRoute-kretsen när motsvarande anslutning Miss lyckas.
> 

## <a name="fine-tuning-features-for-private-peering"></a>Fin justering av funktioner för privat peering

I det här avsnittet låter vi oss granska valfria (beroende på din Azure-distribution och hur känsligt du är att MTTR) funktioner som förbättrar hög tillgänglighet för din ExpressRoute-krets. Mer specifikt går vi igenom den zon medveten distribution av ExpressRoute virtuella nätverksgateway och BFD (dubbelriktad vidarebefordring).

### <a name="availability-zone-aware-expressroute-virtual-network-gateways"></a>Tillgänglighets zon medveten ExpressRoute virtuella nätverksgateway

En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du väljer att använda en zon-redundant Azure IaaS-distribution kan du också konfigurera zoner som är redundanta på virtuella nätverk som avslutar ExpressRoute-privata peering. Mer information finns i [om zoner-redundanta virtuella nätverksgateway i Azure-tillgänglighetszoner][zone redundant vgw]. Information om hur du konfigurerar en zon-redundant virtuell nätverksgateway finns [i skapa en zon-redundant virtuell nätverksgateway i Azure-tillgänglighetszoner][conf zone redundant vgw].

### <a name="improving-failure-detection-time"></a>Förbättra identifierings tiden för problem

ExpressRoute stöder BFD över privat peering. BFD minskar identifierings tiden för fel i Layer 2-nätverket mellan Microsoft Enterprise Edge (msee) och deras BGP-grannar på den lokala sidan från cirka tre minuter (standard) till mindre än en sekund. Tid för snabb avkänning hjälper till att återställa hastening-haverier. Mer information finns i [Konfigurera BFD över ExpressRoute][BFD].

## <a name="next-steps"></a>Nästa steg

I den här artikeln diskuterade vi hur du utformar för hög tillgänglighet för en ExpressRoute-krets anslutning. En peering-plats för ExpressRoute-kretsen fästs på en geografisk plats och kan därför påverkas av ett oåterkalleligt haveri som påverkar hela platsen. 

För design överväganden för att skapa Geo-redundant nätverks anslutning till Microsoft-stamnät som kan motstå katastrofer, som påverkar en hel region, se [design för haveri beredskap med ExpressRoute privat peering][DR].

<!--Image References-->
[1]: ./media/designing-for-high-availability-with-expressroute/exr-reco.png "rekommenderat sätt att ansluta med ExpressRoute"
[2]: ./media/designing-for-high-availability-with-expressroute/suboptimal-lastmile-connectivity.png "bästa senaste mil anslutning"
[3]: ./media/designing-for-high-availability-with-expressroute/nat-options.png "NAT-alternativ"


<!--Link References-->
[zone redundant vgw]: ../vpn-gateway/about-zone-redundant-vnet-gateways.md
[conf zone redundant vgw]: ../vpn-gateway/create-zone-redundant-vnet-gateway.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[BFD]: ./expressroute-bfd.md
[DR]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md