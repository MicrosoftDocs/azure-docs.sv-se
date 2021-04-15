---
title: 'Platser och anslutningsleverantörer: Azure ExpressRoute | Microsoft Docs'
description: Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter plats.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 033ffb6e274432c5398779ba3e3226c626c10ce8
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496715"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partners och peeringplatser

> [!div class="op_single_selector"]
> * [Platser efter provider](expressroute-locations.md)
> * [Leverantörer efter plats](expressroute-locations-providers.md)


Tabellerna i den här artikeln innehåller information om ExpressRoutes geografiska täckning och platser, ExpressRoute-anslutningsleverantörer och ExpressRoute-systemintegrerare (SIs).

> [!Note]
> Azure-regioner och ExpressRoute-platser är två distinkta och olika begrepp. Det är viktigt att förstå skillnaden mellan de två för att utforska Azure-hybridnätverksanslutningar. 
>
>

## <a name="azure-regions"></a>Azure-regioner
Azure-regioner är globala datacenter där Azures beräknings-, nätverks- och lagringsresurser finns. När du skapar en Azure-resurs måste kunden välja en resursplats. Resursplatsen avgör vilket Azure-datacenter (eller tillgänglighetszon) som resursen skapas i.

## <a name="expressroute-locations"></a>ExpressRoute-platser
ExpressRoute-platser (kallas ibland peeringplatser eller meet-me-locations) är samplatsplatser där Microsoft Enterprise Edge-enheter (MSEE) finns. ExpressRoute-platser är startpunkten för Microsofts nätverk – och distribueras globalt, vilket ger kunderna möjlighet att ansluta till Microsofts nätverk över hela världen. På dessa platser kan ExpressRoute-partner ExpressRoute Direct kunder utfärda korsanslutningar till Microsofts nätverk. I allmänhet behöver ExpressRoute-platsen inte matcha Azure-regionen. En kund kan till exempel skapa en ExpressRoute-krets med resursplatsen *USA, östra* på platsen för Seattle-peering. 

Du har åtkomst till Azure-tjänster i alla regioner inom en geopolitisk region, om du är ansluten till minst en ExpressRoute-plats inom den geopolitiska regionen. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-regioner till ExpressRoute-platser inom en geopolitisk region
Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Australiensiska myndigheter** | Australien, centrala, Australien, centrala 2 |Canberra, Canberra2 |
| **Europa** | Frankrike, centrala, Frankrike, södra, Tyskland, norra, Tyskland, västra centrala, Europa, norra, Östra Tyskland, västra Schweiz, norra Schweiz, västra, Storbritannien, västra, Storbritannien, södra, Europa, västra |Amsterdam, Amsterdam2, Berlin, Dublin, Dublin, Dublin, Dublin, Dublin2, Genève, London, London2, Antare, Dublin, Anten, Mürch, Newport(Wales), Paris, Stavanger, London, Mün |
| **Nordamerika** | USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA, västra centrala; Kanada, centrala; Kanada, östra |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minnealack, Montreal, New York, Phoenix, Quebec City, Queretaro(Mexico), Qucy, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Toronto, Toronto2, Toronto2, Toronto, Washington DC, Washington DC2 |
| **Asien** | Asien, östra, Sydostasien | Sydkorea, Hongkong, Hongkong 2, Jakarta, Gate Gate, Singapore, Singapore2, Taipei |
| **Indien** | Indien, västra, Indien, centrala, Indien, södra |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | Japan, västra, Japan, östra |Osaka, Tokyo, Tokyo2 |
| **Oceanien** | Australien, sydöstra, Australien, östra |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Sydkorea** | Sydkorea, centrala; Sydkorea, södra |Busan, Söul|
| **UAE** | Förenade Arabemiraten, centrala; Förenade Arabemiraten, norra | Hart, Eller2 |
| **Sydafrika** | Sydafrika, västra, Sydafrika, norra |Kapstaden, Johannesburg |
| **Sydamerika** | Brasilien, södra |Sasa, Rio deSkapa, Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Azure-regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Moln för amerikanska myndigheter** |US Gov, Arizona; USA Gov, Iowa; USA Gov, Texas; USA Gov, Virginia; USA DoD, centrala; USA DoD, östra  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Kina, östra** |Kina, östra, Kina, östra 2 |Shanghai, Shanghai2 |
| **Kina, norra** |Kina, norra, Kina, norra 2 |Beijing, Beijing2 |
| **Tyskland** |Tyskland, centrala, Tyskland, östra |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-anslutningsleverantörer

Följande tabell innehåller anslutningsplatser och tjänstleverantörerna för varje plats. Om du vill visa leverantörer och de platser där de kan tillhandahålla service går du till [Platser efter tjänsteleverantör](expressroute-locations.md).

* **Lokala Azure-regioner** är de som [ExpressRoute Local på](expressroute-faqs.md) varje peeringplats kan komma åt. **n/a** anger att ExpressRoute Local inte är tillgängligt på den peeringplatsen.

* **Zon** refererar till [priser](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Global kommersiell Azure
| **Plats** | **Adress** | **Zon** | **Lokala Azure-regioner** | **ER Direct** | **Tjänsteleverantörer** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa, västra | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa, västra | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, GÉANT, Interxion, NOS, NTT Global DataCenters EMEA, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | saknas | 10G, 100G | Equinix, Megaport |
| **Auckland** | [Vocus Group NZNzy](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | saknas | 10G | Devoli, Kordia, Megaport, REANNZ, Spark NZ, Vocus Group NZ |
| **Bangkok** | [Ais](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | saknas | 10G | AIS, UIH |
| **Berlin** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Tyskland, norra | 10G | Colt, Equinix, NTT Global DataCenters EMEA|
| **Bogota** | [Equinix BG1](https://www.equinix.com/locations/americas-colocation/colombia-colocation/bogota-data-centers/bg1/) | 4 | saknas | 10 G | Equinix |
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Sydkorea, södra | saknas | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Australien, centrala | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/about-us/) | 1 | Australien, centrala 2| 10G, 100G | CDC, Equinix |
| **Kapstaden** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Sydafrika, västra | 10 G | BCX, Internet Solutions – Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Indien, södra | 10 G | BSNL, Global CloudXchange (GCX), SIFY, Tata Communications, VodafoneIdea |
| **Chennai2** | Airtel | 2 | Indien, södra | 10 G | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | USA, norra centrala | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Köpenhamn** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | saknas | 10 G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | saknas | 10G, 100G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, PacketFabric, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | USA, västra centrala | saknas | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Förenade Arabemiraten, norra | saknas | Etisalat FÖRENADE ARABEMIRATEN |
| **Så här ser det ut 2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Förenade Arabemiraten, norra | saknas | DE-CIX, du datamena, Equinix, Megaport, Orange, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa, norra | 10G, 100G | CenturyLink Cloud Connect, Colt, eir, Equinix, GEANT, euNetworks, Interxion, Megaport |
| **Frankfurt** | [Interxion STUDENT11](https://www.interxion.com/Locations/frankfurt/) | 1 | Tyskland, västra centrala | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, GEANT, InterCloud, Interxion, Megaport, Orange, Telia Carrier, T-Systems |
| **Därför vill jag inte att du ska göra** | [Equinix FR7](https://www.equinix.com/locations/europe-colocation/germany-colocation/frankfurt-data-centers/fr7/) | 1 | Tyskland, västra centrala | 10G, 100G | Equinix |
| **Genève** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Schweiz, västra | 10G, 100G | Equinix, Megaport,Bytecom |
| **Hongkong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Asien, östra | 10 G | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, China Unicom, Colt, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hongkong 2** | [iAdvantage MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Asien, östra | 10 G | China Mobile International, China Telecom Global, iAdvantage, Megaport, PCCW Global Limited, SingTel |
| **Jakarta** | Telin, Telkom Indonesia | 4 | saknas | 10 G | Telin |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Sydafrika, norra | 10G | BCX, British Telecom, Internet Solutions – Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | saknas | saknas | TIME dotCom |
| **Las Vegas** | [Växla LV](https://www.switch.com/las-vegas) | 1 | saknas | 10G, 100G | CenturyLink Cloud Connect, Megaport, PacketFabric |
| **London** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Storbritannien, södra | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Storbritannien, södra | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, GTT, IX Reach, Equinix, Megaport, SES, Sohonet, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | saknas | 10G, 100G | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Los Angeles2** | [Equinix LA1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | saknas | 10G, 100G | Equinix |
| **Madrid** | [Interxion MAD1](https://www.interxion.com/es/donde-estamos/europa/madrid) | 1 | Europa, västra | 10G, 100G | Interxion |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Frankrike, södra | saknas | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australien, sydöstra | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | saknas | 10G, 100G | Claro, C3ntro, Equinix, Megaport, Neutrona Networks |
| **Milano** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | saknas | 10 G | Colt, Equinix, Fastweb, IRIDEOS, Retelit |
| **Minneapolis** | [Cologix MIN1](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | saknas | 10G, 100G | Cologix, Megaport |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | saknas | 10G, 100G | Bell Canada, Cologix, Fibrenoire, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Indien, västra | 10 G | BSNL, DE-CIX, Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Indien, västra | 10G | Airtel, Sify, Vodafone Idea |
| **München** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | saknas | 10G | DE-CIX |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | saknas | 10G, 100G | CenturyLink Cloud Connect, Colt, Coresite, DE-CIX, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | [Nästa generations data](https://www.nextgenerationdata.co.uk) | 1 | Storbritannien, västra | saknas | British Telecom, Colt, Jisc, Level 3 Communications, Next Generation Data |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Japan, västra | 10G, 100G | AT TOKYO, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank, Toelekommunikation |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Mellanöstern | 10G, 100G | GlobalConnect, Megaport, Telenor, Telia Carrier |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Frankrike, centrala | 10G, 100G | British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Jaguar Network, Megaport, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | saknas | 10 G | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | saknas | 10G, 100G | |
| **Quebec City** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Kanada, östra | 10G, 100G | Bell Canada, Megaport, Telus |
| **Queretaro (Mexiko)** | [KIO Networks QR01](https://www.kionetworks.com/es-mx/) | 4 | saknas | 10 G | Transtelco|
| **Quincy** | [Sabey Datacenter – Skapa A](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | USA, västra 2 | 10G, 100G | |
| **Rio de Janeiro** | [Equinix-RJ2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/rio-de-janeiro-data-centers/rj2/) | 3 | Brasilien, sydöstra | 10 G | Equinix |
| **San Antonio** | [–One SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | USA, södra centrala | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brasilien, södra | 10G, 100G | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA, västra 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Söul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Sydkorea, centrala | 10G, 100G | KINX, KT, LG CNS, Equinix, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA, västra | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA, västra | 10G, 100G | Colt, Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Sydostasien | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Sydostasien | 10G, 100G | China Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel, Telehouse – KDDI |
| **Stavanger** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Tyskland, västra | 10G, 100G |GlobalConnect, Megaport |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | saknas | 10 G | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australien, östra | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australien, östra | 10G, 100G | Megaport, NextDC |
| **Taipei** | Chief Telecom | 2 | saknas | 10 G | Chief Telecom, Chunghwa Telecom, FarEasFöretaget |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japan, östra | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokyo2** | [AT TOKYO](https://www.attokyo.com/) | 2 | Japan, östra | 10G, 100G | AT TOKYO, Megaport, Toyo Communications |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Kanada, centrala | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Toronto2** | [Reit-reit](https://www.alliedreit.com/property/905-king-st-w/) | 1 | Kanada, centrala | 10G, 100G | |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | saknas | 10G | Cologix, Megaport, Telus |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | USA, östra, USA, östra 2 | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, Iron Mountain, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-center-locations/northern-virginia-washington-dc) | 1 | USA, östra, USA, östra 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **Zurich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Schweiz, norra | 10G, 100G | Colt, Equinix, Intercloud, Interxion, Megaport,Cloudcom |

 **+** anger kommer snart

### <a name="national-cloud-environments"></a>Nationella molnmiljöer

Nationella Azure-moln är isolerade från varandra och från globala kommersiella Azure. ExpressRoute för ett Azure-moln kan inte ansluta till Azure-regionerna i de andra.

### <a name="us-government-cloud"></a>U.S. Government-moln
| **Plats** | **Adress** | **Lokala Azure-regioner**| **ER Direct** | **Tjänsteleverantörer** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | saknas | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | saknas | 10G, 100G | AT&T NetBond, British Telecom, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | saknas | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | saknas | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [Så här gör du:](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov, Arizona | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [Sasone SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov, Texas | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | saknas | 10G, 100G | AT&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | saknas | 10G, 100G | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | USA DoD, östra, US Gov, Virginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Kina
| **Plats** | **Tjänsteleverantörer** |
| --- | --- |
| **Peking** |China Telecom |
| **Beijing2** | China Telecom, China Unicom, DDRS |
| **Shanghai** |China Telecom |
| **Shanghai2** | China Telecom, China Unicom, CHINAS |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Tyskland
| **Plats** | **Tjänsteleverantörer** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Frankfurt** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Anslutning via Exchange-leverantörer
Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

## <a name="connectivity-through-satellite-operators"></a>Anslutning via satellitoperatörer
Om du är fjärransluten och inte har fiberanslutning eller om du vill utforska andra anslutningsalternativ kan du kontrollera följande satellitoperatörer. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Anslutning via ytterligare tjänstleverantörer
| **Plats** | **Exchange** | **Anslutningsleverantörer** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Level 3 Communications | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| IoAA (Påk)
| **Kapstaden** | Teraco | MTN |
| **Chennai** | Tata Communications | Tata Teleservices |
| **Chicago** | Equinix| IaaA, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business,RiktRikting, Data Foundry, Spectrum Enterprise, Transtelco |
| **Frankfurt** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **Folkrepubliken Kinas särskilda administrativa region Hongkong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **London** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Hare, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **Mumbai** | Tata Communications | Tata Teleservices |
| **New York** |Equinix, Megaport | Altice Business, Underklistret, Spectrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Oncore Cloud Services Inc., Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business,IstanIstan, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Kontinent** | **Systemintegratörer** |
| --- | --- |
| **Asien** |Avanade Inc., OneAs1a |
| **Australien** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Nordamerika** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Sydamerika** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i Vanliga frågor [och svar om ExpressRoute.](expressroute-faqs.md)
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"
