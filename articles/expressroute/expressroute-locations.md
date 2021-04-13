---
title: 'Anslutningsleverantörer och platser: Azure ExpressRoute | Microsoft Docs'
description: Den här artikeln innehåller en detaljerad översikt över platser där tjänster erbjuds och hur du ansluter till Azure-regioner. Sorteras efter anslutningsleverantör.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/10/2021
ms.author: duau
ms.openlocfilehash: 7214e06b42cbc69a31ad66504301038c72728aa1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310758"
---
# <a name="expressroute-connectivity-partners-and-peering-locations"></a>ExpressRoute-anslutningspartner och peering-platser

> [!div class="op_single_selector"]
> * [Platser efter leverantör](expressroute-locations.md)
> * [Leverantörer efter plats](expressroute-locations-providers.md)


Tabellerna i den här artikeln innehåller information om ExpressRoute geografisk täckning och platser, ExpressRoute Connectivity-providers och ExpressRoute system integrators (SIs).

> [!Note]
> Azure-regioner och ExpressRoute-platser är två distinkta och olika koncept, så att skillnaden mellan de två är avgörande för att utforska Azure Hybrid nätverks anslutning. 
>
>

## <a name="azure-regions"></a>Azure-regioner
Azure-regioner är globala data Center där Azure Compute, nätverk och lagrings resurser finns. När du skapar en Azure-resurs måste kunden välja en resurs plats. Resurs platsen avgör vilken Azure-datacenter (eller tillgänglighets zon) som resursen skapas i.

## <a name="expressroute-locations"></a>ExpressRoute-platser
ExpressRoute-platser (kallas ibland peering-platser eller uppfyller platser) är samplacerings anläggningar där Microsoft Enterprise Edge-enheter (MSEE: N) finns. ExpressRoute platser är start punkten till Microsofts nätverk – och distribueras globalt, vilket ger kunderna möjlighet att ansluta till Microsofts nätverk runtom i världen. Dessa platser är där ExpressRoute partners och ExpressRoute Direct-kunder utfärdar kors anslutningar till Microsofts nätverk. I allmänhet behöver ExpressRoute-platsen inte matcha Azure-regionen. En kund kan till exempel skapa en ExpressRoute-krets med resurs platsen *östra USA*, på peer-platsen i *Seattle* .

Du har åtkomst till Azure-tjänster i alla regioner inom en geopolitisk region, om du är ansluten till minst en ExpressRoute-plats inom den geopolitiska regionen.

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Azure-regioner till ExpressRoute-platser inom en geopolitisk region.
Följande tabell innehåller en karta över Azures regioner till ExpressRoute-platser inom en geopolitisk region.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Australiensiska myndigheter** |Australien, centrala, Australien, centrala 2 |Canberra, Canberra2 |
| **Europa** | Frankrike, centrala, Frankrike, södra, Tyskland, norra, Tyskland, västra centrala, norra Europa, östra Norge, västra Norge, Schweiz, norra, Schweiz, västra, Storbritannien, västra, Storbritannien, södra, Västeuropa |Amsterdam, Amsterdam2, Berlin, köpen hamn, Dublin, Frankfurt, Frankfurt2, Genève, London, London2, Madrid, Marseille, Milano, München, Newport (Wales), Oslo, Paris, Stavanger, Stockholm, Zürich |
| **Nordamerika** |USA, östra; USA, västra; USA, östra 2; USA, västra 2; USA, centrala; USA, södra centrala; USA, norra centrala; USA, västra centrala; Kanada, centrala; Kanada, östra |Atlanta, Chicago, Borås, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, Montrealprotokollet, New York, Phoenix, Quebec City, Queretaro (Mexiko), Quincy, San Antonio, Seattle, Silicon dal, Silicon Valley2, Toronto, Toronto2, Vancouver, Washington DC, Washington DC2 |
| **Asien** | Asien, östra, Sydostasien | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei, |
| **Indien** | Indien, västra, Indien, centrala, Indien, södra |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japan** | Japan, västra, Japan, östra |Osaka, Tokyo, Tokyo2 |
| **Oceanien** | Australien, sydöstra, Australien, östra |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **Sydkorea** | Sydkorea, centrala; Sydkorea, södra |Busan, Söul|
| **UAE** | Förenade Arabemiraten Central, Förenade Arabemiraten Nord | Dubai, Dubai2 |
| **Sydafrika** | Sydöstra Sydafrika, norra Sydafrika |Kapstaden, Johannesburg |
| **Sydamerika** | Brasilien, södra |Bogotá, Rio de Janeiro, Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regioner och geopolitiska gränser för nationella moln
Tabellen nedan innehåller information om regioner och geopolitiska gränser för nationella moln.

| **Geopolitisk region** | **Azure-regioner** | **ExpressRoute-platser** |
| --- | --- | --- |
| **Moln för amerikanska myndigheter** |US Gov, Arizona; USA Gov, Iowa; USA Gov, Texas; USA Gov, Virginia; USA DoD, centrala; USA DoD, östra  |Atlanta, Chicago, Borås, New York, Phoenix, San-Antonio, Seattle, Silicon dal, Washington DC |
| **Kina, östra** |Kina, östra, Kina, östra 2 |Shanghai, Shanghai2 |
| **Kina, norra** |Kina, norra, Kina, norra 2 |Beijing, Beijing2 |
| **Tyskland** |Tyskland, centrala, Tyskland, östra |Berlin, Frankfurt |

Anslutningen mellan geopolitiska regioner stöds inte för standard-SKU:er i ExpressRoute. Du måste aktivera ExpressRoutes premiumtillägg som stöder global anslutning. Anslutning till nationella molnmiljöer stöds inte. Kontakta din anslutningsleverantör om detta behov uppstår.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute-anslutningsleverantörer

I följande tabell visas platser efter tjänstleverantör. Om du vill visa tillgängliga leverantörer efter plats går du till [Tjänsteleverantörer efter plats](expressroute-locations-providers.md).


### <a name="global-commercial-azure"></a>Global kommersiell Azure

| **Tjänstleverantör** | **Microsoft Azure** | **Microsoft 365**  | **Platser** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Stöds |Stöds |Melbourne, Sydney |
| **[Airtel](https://www.airtel.in/business/#/)** | Stöds | Stöds | Chennai2, Mumbai2 |
| **[AIS](https://business.ais.co.th/solution/en/azure-expressroute.html)** | Stöds | Stöds | Bangkok |
| **[Aryaka Networks](https://www.aryaka.com/)** |Stöds |Stöds |Amsterdam, Chicago, Borås, Hongkong SAR, Sao Paulo, Seattle, Silicon dal, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Stöds |Stöds |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Amsterdam, Chicago, Borås, Frankfurt, London, Silicon dal, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[I TOKYO](https://www.attokyo.com/connectivity/azure.html)** | Stöds | Stöds | Osaka, Tokyo2 |
| **[BBIX](https://www.bbix.net/en/service/ix/)** | Stöds | Stöds | Tokyo |
| **[BCX](https://www.bcx.co.za/solutions/connectivity/data-networks)** |Stöds |Stöds |Kapstaden, Johannesburg|
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Stöds |Stöds |Montreal, Toronto, Quebec City |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/cloud-connect-azure)** |Stöds |Stöds |Amsterdam, Amsterdam2, Chicago, Hong Kong SAR, Johannesburg, London, London2, Newport (Wales), Paris, Sao Paulo, Silicon dal, Singapore, Sydney, Tokyo, Washington DC |
| **[BSNL](https://www.bsnl.co.in/opencms/bsnl/BSNL/services/enterprises/cloudway.html)** |Stöds |Stöds |Chennai, Mumbai |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Stöds |Stöds |Miami |
| **CDC** | Stöds | Stöds | Canberra, Canberra2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Stöds |Stöds |Amsterdam2, Chicago, Dublin, Frankfurt, Hongkong, Las Vegas, London2, New York, Paris, San-Antonio, Silicon dal, Tokyo, Toronto, Washington DC, Washington DC2 |
| **[Chief Telecom](https://www.chief.com.tw/)** |Stöds |Stöds |Hong Kong, Taipei, |
| **Kina, mobil internationell** |Stöds |Stöds | Hong Kong, Hong Kong2, Singapore |
| **China Telecom Global** |Stöds |Stöds |Hong Kong, Hong Kong2 |
| **Kina, Unicom global** |Stöds |Stöds | Hong Kong, Singapore2 |
| **[Chunghwa Telecom](https://www.cht.com.tw/en/home/cht/about-cht/products-and-services/International/Cloud-Service)** |Stöds |Stöds |Taipei |
| **[Claro](https://www.usclaro.com/enterprise-mnc/connectivity/mpls/)** |Stöds |Stöds |Miami |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Stöds |Stöds |Chicago, Borås, Minneapolis, Montrealprotokollet, Toronto, Vancouver, Washington DC |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Stöds |Stöds |Amsterdam, Amsterdam2, Chicago, Dublin, Frankfurt, London, London2, Milano, Newport, New York, Osaka, Paris, Silicon dal, Silicon Valley2, Singapore2, Tokyo, Washington DC |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Stöds |Stöds |Chicago, Denver, Los Angeles, New York, Silicon dal, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange/find-a-cloud-service/detail/microsoft-azure)** | Stöds |Stöds |Amsterdam2, Dubai2, Frankfurt, Marseille, Mumbai, München, New York |
| **[Devoli](https://devoli.com/expressroute)** | Stöds |Stöds | Auckland, Melbourne, Sydney |
| **du datamena** |Stöds |Stöds | Dubai2 |
| **eir** |Stöds |Stöds |Dublin|
| **[Epsilon, global kommunikation](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Stöds |Stöds |Singapore, Singapore2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Amsterdam, Amsterdam2, Atlanta, Berlin, Bogotá, Canberra2, Chicago, Borås, Dubai2, Dublin, Frankfurt, Frankfurt2, Genève, Hong Kong SAR, London, London2, Los Angeles, Los Angeles2, Melbourne, Miami, Milano, New York, Osaka, Paris, Rio de Janeiro, Sao Paulo, Seattle, Söul, Silicon dal, Singapore, Stockholm, Sydney, Tokyo, Toronto, Washington DC, Zürich |
| **Etisalat Förenade Arabemiraten** |Stöds |Stöds |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Stöds |Stöds |Amsterdam, Amsterdam2, Dublin, Frankfurt, London |
| **[FarEasTone](https://www.fetnet.net/corporate/en/Enterprise.html)** |Stöds |Stöds |Taipei|
| **[Fastweb](https://www.fastweb.it/grandi-aziende/cloud/scheda-prodotto/fastcloud-interconnect/)** | Stöds |Stöds |Milano|
| **[Fibrenoire](https://fibrenoire.ca/en/services/cloudextn-2/)** |Stöds |Stöds |Montreal|
| **[GÉANT](https://www.geant.org/Networks)** |Stöds |Stöds |Amsterdam, Amsterdam2, Dublin, Frankfurt, Marseille |
| **[GlobalConnect]()** | Stöds |Stöds | Oslo, Stavanger | 
| **GTT** |Stöds |Stöds |London2 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Stöds| Stöds | Chennai, Mumbai |
| **[iAdvantage](https://www.scx.sunevision.com/)** | Stöds | Stöds | Hong Kong2 |
| **Intelsat** | Stöds | Stöds | Washington DC2 |
| **[InterCloud](https://www.intercloud.com/)** |Stöds |Stöds |Amsterdam, Chicago, Frankfurt, Hongkong, London, New York, Paris, kisel dal, Singapore, Washington DC, Zürich |
| **[Internet2](https://internet2.edu/services/cloud-connect/#service-cloud-connect)** |Stöds |Stöds |Chicago, Borås, Silicon dal, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Stöds |Stöds |Osaka, Tokyo |
| **[Internet-lösningar – moln anslutning](https://www.is.co.za/solution/cloud-connect/)** |Stöds |Stöds |Kapstaden, Johannesburg, London |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Stöds |Stöds |Amsterdam, Amsterdam2, köpen hamn, Dublin, Frankfurt, London, Madrid, Marseille, Paris, Zürich |
| **[IRIDEOS](https://irideos.it/)** |Stöds |Stöds |Milano |
| **Järn Bergs** | Stöds |Stöds |Washington DC |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Stöds |Stöds | Amsterdam, London2, Silicon dal, Toronto, Washington DC |
| **Jaguar-nätverk** |Stöds |Stöds |Marseille, Paris |
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Stöds |Stöds |London, Newport (Wales) |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Stöds |Stöds |Seoul |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Stöds |Stöds |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Stöds | Stöds | Amsterdam |
| **[KT](https://cloud.kt.com/)** | Stöds | Stöds | Seoul |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Amsterdam, Chicago, Dallas, London, Newport (Wales), Sao Paulo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Stöds |Stöds |Busan, Söul |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Stöds |Stöds |Kapstaden, Johannesburg |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds |Stöds |Amsterdam, Atlanta, Auckland, Chennai, Chicago, Borås, Denver, Dubai2, Dublin, Frankfurt, Genève, Hong Kong, Hong Kong2, Las Vegas, London, London2, Los Angeles, Melbourne, Miami, Minneapolis, Montrealprotokollet, New York, Osaka, Oslo, Paris, Perth, Quebec City, San Antonio, Seattle, Silicon dal, Singapore, Singapore2, Stavanger, Stockholm, Sydney, Sydney2, Tokyo, Tokyo2 Toronto, Vancouver, Washington DC, Washington DC2, Zürich |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** |Stöds |Stöds |London |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Stöds |Stöds |Borås, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Nästa generations data](https://vantage-dc-cardiff.co.uk/)** |Stöds |Stöds |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Stöds |Stöds |Melbourne, Perth, Sydney, Sydney2 |
| **[VER.nr](https://www.nos.pt/empresas/corporate/cloud/cloud/Pages/nos-cloud-connect.aspx)** |Stöds |Stöds |Amsterdam2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Stöds |Stöds |Amsterdam, Hongkong SAR, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://business.ntt-east.co.jp/service/crossconnect/)** |Stöds |Stöds |Tokyo |
| **[NTT globala data Center EMEA](https://hello.global.ntt/)** |Stöds |Stöds |Amsterdam2, Berlin |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Stöds |Stöds |Osaka |
| **[Ooredoo Cloud Connect](https://www.ooredoo.qa/portal/OoredooQatar/cloud-connect-expressroute)** |Stöds |Stöds |Marseille |
| **[Optus](https://www.optus.com.au/enterprise/)** |Stöds |Stöds |Melbourne, Sydney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Stöds |Stöds |Amsterdam, Amsterdam2, Dubai2, Frankfurt, Hong Kong SAR, Johannesburg, London, Paris, Sao Paulo, Silicon dal, Singapore, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Stöds | Stöds | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)** |Stöds |Stöds |Chicago, Borås, Las Vegas, Silicon dal, Washington DC |
| **[PCCW globalt begränsad](https://consoleconnect.com/clouds/#azureRegions)** |Stöds |Stöds |Chicago, Hongkong, Hong Kong2, London, Singapore2 |
| **[REANNZ](https://www.reannz.co.nz/products-and-services/cloud-connect/)** | Stöds | Stöds | Auckland |
| **[Retelit](https://www.retelit.it/EN/Home.aspx)** | Stöds | Stöds | Milano | 
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Stöds |Stöds |Seoul |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | Stöds |Stöds | London2, Washington DC |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Stöds |Stöds |Chennai, Mumbai2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Stöds |Stöds |Hong Kong2, Singapore, Singapore2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Stöds |Stöds |Osaka, Tokyo |
| **[Sohonet](https://www.sohonet.com/fastlane/)** |Stöds |Stöds |London2 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Stöds |Stöds |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | Stöds | Stöds | Genève, Zürich |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Stöds |Stöds |Amsterdam, Chennai, Hong Kong SAR, London, Mumbai, Sao Paulo, Silicon dal, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Stöds |Stöds |Amsterdam, Sao Paulo |
| **[Telehouse – KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |Stöds |Stöds |London, London2, Singapore2 |
| **Telenor** |Stöds |Stöds |Amsterdam, London, Oslo |
| **[Telia-bärvåg](https://www.teliacarrier.com/)** | Stöds | Stöds |Amsterdam, Chicago, Borås, Frankfurt, Hongkong, London, Oslo, Paris, Silicon dal, Stockholm, Washington DC |
| **[Telin](https://www.telin.net/product/data-connectivity/telin-cloud-exchange)** | Stöds | Stöds |Jakarta |
| **Telmex Uninet**| Stöds | Stöds | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Stöds |Stöds |Melbourne, Singapore, Sydney |
| **[Telus](https://www.telus.com)** |Stöds |Stöds |Montrealprotokollet, Seattle, Quebec City, Toronto, Vancouver |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Stöds |Stöds |Kapstaden, Johannesburg |
| **[TID dotCom](https://www.time.com.my/enterprise/connectivity/direct-cloud)** | Stöds | Stöds | Kuala Lumpur |
| **[Tokai-kommunikation](https://www.tokai-com.co.jp/en/)** | Stöds | Stöds | Osaka, Tokyo2 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Stöds |Stöds |Borås, Queretaro (Mexiko)|
| **T-Systems** |Stöds |Stöds |Frankfurt|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Stöds |Stöds |Sao Paulo |
| **[UIH](https://www.uih.co.th/en/network-solutions/global-network/cloud-direct-for-microsoft-azure-expressroute)** | Stöds | Stöds | Bangkok |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Stöds |Stöds |Amsterdam, Norrköping, Borås, Hongkong SAR, London, Mumbai, Silicon dal, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | Stöds | Stöds | Washington DC2 |
| **[Vocus-grupp NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Stöds | Stöds | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Stöds |Stöds |Amsterdam2, London, Singapore |
| **[Vodafone-idé](https://www.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | Stöds | Stöds | Mumbai2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Stöds |Stöds |Amsterdam, Chicago, Borås, Denver, London, Los Angeles, Montrealprotokollet, New York, Paris, Seattle, Silicon dal, Toronto, Washington DC, Washington DC2 |

 **+** kommer snart

### <a name="national-cloud-environment"></a>Nationell molnmiljö

De nationella Azure-molnen är isolerade från varandra och från globala affärs Azure. ExpressRoute för ett Azure-moln kan inte ansluta till Azure-regionerna i de andra. 

### <a name="us-government-cloud"></a>U.S. Government-moln

| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Stöds |Stöds |Chicago, Phoenix, Silicon dal, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Stöds |Stöds |New York, Phoenix, San-Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds |Atlanta, Chicago, Borås, Stockholm, Seattle, Silicon dal, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Stöds |Stöds |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds | Stöds | Chicago, Borås, San-Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Stöds |Stöds |Chicago, Dallas, New York, Silicon Valley, Washington DC |

### <a name="china"></a>Kina

| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **China Telecom** |Stöds |Stöds inte |Beijing, Beijing2, Shanghai, Shanghai2 |
| **Kina, Unicom** | Stöds | Stöds inte | Beijing2, Shanghai2 |
| **[GDS](http://www.gds-services.com/en/about_2.html)** |Stöds |Stöds inte |Beijing2, Shanghai2 |

Läs mer i [ExpressRoute i Kina](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Tyskland

| **Tjänstleverantör** | **Microsoft Azure** | **Office 365** | **Platser** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/direct-connect/azure/)** |Stöds |Stöds inte |Frankfurt |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Stöds |Stöds inte |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroute)** |Stöds |Stöds inte |Berlin |
| **Interxion** |Stöds |Stöds inte |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Stöds  | Stöds inte | Berlin |
| **T-Systems** |Stöds |Stöds inte |Berlin |

## <a name="connectivity-through-exchange-providers"></a>Anslutning via Exchange-leverantörer

Om inte din anslutningsleverantör finns med i föregående avsnitt, kan du fortfarande skapa en anslutning.

* Fråga anslutningsleverantören om de är anslutna till något av utbytena i tabellen ovan. Du kan använda följande länkar för att samla in mer information om tjänster som erbjuds av utbytesleverantörer. Flera anslutningsleverantörer är redan ansluten till Ethernet-utbyten.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure) 
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)

* Låt anslutningsleverantören utöka ditt nätverk till den valda peeringplatsen.
  * Se till att anslutningsleverantören utökar anslutningen med hög tillgänglighet så att det inte finns några enskilda problempunkter.
* Beställ en ExpressRoute-krets med utbyte så att anslutningsleverantören kan ansluta till Microsoft.
  * Följ stegen i [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md) för att konfigurera anslutningen.

## <a name="connectivity-through-satellite-operators"></a>Anslutning via satellit operatörer
Om du är fjärran sluten och inte har fiber anslutning eller om du vill utforska andra anslutnings alternativ kan du kontrol lera följande satellit operatörer. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Anslutning via ytterligare tjänst leverantörer

| **Anslutningsleverantör** | **Exchange** | **Platser** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** | Equinix |Singapore |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/Business)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokyo |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/business/cloud-exchange)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Amsterdam, Frankfurt, London, Singapore, Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokyo |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/)** | Equinix | Folkrepubliken Kinas särskilda administrativa region Hongkong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Frankfurt, Hamburg |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Amsterdam | 
| **[CMC telekom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapore | 
| **[Aptum-tekniker](https://aptum.com/services/cloud/managed-azure/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](https://www.coreazure.com/)**| Equinix | London |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Kron Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Borås, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/solutions/cloud-connect/)** | Equinix | London, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Exponentiellt E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Quebec City |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Amsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B. S. C](http://www.kalaam-telecom.com/azure/)**| Level 3 Communications |Amsterdam |
| **LGA Telecom** |Equinix |Singapore|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Folkrepubliken Kinas särskilda administrativa region Hongkong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Amsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.co.za/en/Cloud-Solutions/Pages/microsoft-express-route.aspx)** | Teraco | Kapstaden, Johannesburg |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | Amsterdam, Frankfurt |
| **[OnCore Cloud Service Inc](https://www.oncore.cloud/services/ue-for-expressroute)**| Equinix | Toronto |
| **[PUBLICERA telekom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Amsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Amsterdam, Dublin, London, Paris |
| **[QSC AG](https://www2.qbeyond.de/en/)** |Interxion | Frankfurt |  
| **[RETN](https://retn.net/services/cloud-connect/)** | Equinix | Amsterdam |
| **[Tata-teletjänster](https://www.tatateleservices.com/business-services/data-services/secure-cloud-connect)** | Tata Communications | Chennai, Mumbai |
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Spektrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Borås, Los Angeles, New York, Silicon dal | 
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | London | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Amsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Amsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Amsterdam, Frankfurt |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Amsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapore |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon dal, Silicon dal 2|
| **Zain** |Equinix |London|
| **[Zertia](https://www.zertia.es)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix, Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Anslutning via data Center leverantörer

| **Leverantör** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databasen](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX räckvidd, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX räckvidd, Megaport, PacketFabric |
| **[QTS Data Center](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Strömma Data Center]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX räckvidd, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX räckvidd, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Anslutning via nationella forsknings-och utbildnings nätverk (NREN)

| **Leverantör**|
| --- |
| **AARNET**| 
| **DeIC, via GÉANT**|
| **GARR, via GÉANT**|
| **GÉANT**|
| **HEAnet, via GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS, via GÉANT**|
| **SINET**|
| **Surfnet, via GÉANT**|

* Om din anslutningsleverantör inte finns med här kan du se om den är ansluten till någon av de ExpressRoute Exchange-partner som anges ovan.

## <a name="expressroute-system-integrators"></a>ExpressRoute-systemintegratörer
Att aktivera privata anslutningar så att de passar dina behov kan vara svårt, beroende på hur stort nätverk du har. Du kan använda någon av systemintegratörer som finns i följande tabell. De hjälper dig med integrationen till ExpressRoute.

| **Systemintegrator** | **Kontinent** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asien, Europa, Nordamerika, Sydamerika |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Asien
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Nordamerika |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Nordamerika |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Nordamerika |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australien |
| **[MOQdigital](https://www.moqdigital.com/insights)** | Australien |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Tyskland) |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | Europa |
| **[Ny signatur](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asien |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Nordamerika |
| **[Presidio](https://www.presidio.com/subpage/1107/microsoft-azure)** | Nordamerika |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Sydamerika |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australien |

## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Platskarta"
