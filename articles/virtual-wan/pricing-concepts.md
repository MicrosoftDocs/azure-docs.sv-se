---
title: Om priser för virtuella WAN-nätverk
titleSuffix: Azure Virtual WAN
description: I den här artikeln beskrivs vanliga frågor om virtuella WAN-priser
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 13451291544f704000ab61d41ed5014fa69298e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517696"
---
# <a name="about-virtual-wan-pricing"></a>Om priser för virtuella WAN-nätverk

Azure Virtual WAN ger flera nätverks-och säkerhets tjänster tillsammans i ett enhetligt ramverk. Den baseras på en nav-och eker-arkitektur där hubbarna är Microsoft-hanterade med olika tjänster som ingår i hubben, till exempel VPN, ExpressRoute, användarens VPN (punkt-till-plats), brand vägg, routning osv.

Varje tjänst i det virtuella WAN-nätverket priss ätts. Därför är det inte möjligt att föreslå ett enskilt pris på virtuellt WAN. [Pris Kalkylatorn i Azure](https://azure.microsoft.com/pricing/calculator/) är en mekanism för att härleda kostnaden, som baseras på de tjänster som tillhandahålls i ett virtuellt WAN. I den här artikeln beskrivs vanliga frågor om virtuella WAN-priser.

>[!NOTE]
>Aktuell pris information finns i [priser för virtuella WAN-nätverk](https://azure.microsoft.com/pricing/details/virtual-wan/). Avgifter mellan hubb (hubb till hubb) visas inte på pris sidan för virtuella WAN-nätverk eftersom det är underkastat Inter-Region (Intra/interkontinental) avgifter för [Azure data överföring](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="common-pricing-questions"></a><a name="questions"></a>Vanliga pris frågor

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Vad är en skalnings enhet?

En **skalnings enhet** tillhandahåller enheten för sammanställd kapacitet för plats-till-plats (S2S), punkt-till-plats (P2s) och EXPRESSROUTE (er) i en virtuell hubb. Exempel:

* **1 S2S VPN-skalnings enhet** innebär en total kapacitet på 500 Mbit/s VPN-gateway (dubbla instanser distribueras för återhämtning) i en virtuell hubb kostnad $0.361/timme.
* **1 er skalnings enhets enhet** innebär totalt 2 Gbit/s återställnings-gateway i virtuell hubb kostnad $0.42/timme.
* **5 ÅTERställnings enheter** skulle innebära totalt 10 Gbit/s-gateway i ett virtuellt nav för VNet-pris på $0.42 * 5/timme. ER ökar $0,25/timme från den 6: a till tionde skalnings enheten.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Vad är en anslutnings enhet?

En **anslutnings enhet** gäller för alla lokala/icke-Microsoft-slutpunkter som ansluter till Azure-gatewayer. För plats-till-plats-VPN innebär detta värde grenar. För användares VPN (punkt-till-plats) innebär detta värde fjärran vändare. För ExpressRoute betyder detta värde ExpressRoute-krets-anslutningar.<br>Exempel:

* En förgrenings anslutning som ansluter till Azure VPN i en virtuell hubb kostar $0,05/timme. Därför kostar 100 förgrenings anslutningar som ansluter till en virtuell Azure-hubb $0,05 * 100/timme.

* Två ExpressRoute-krets anslutningar som ansluter till en virtuell hubb kostar $0,05 * 2/h.

* Tre fjärran vändare som ansluter till Azure Virtual Hub P2S-gatewayen kostar $0.03 * 3/h.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Hur beräknas data överförings avgifter?

* All trafik som använder Azure debiteras inte. Trafik som lämnar Azure (via VPN, ExpressRoute eller punkt-till-plats-VPN-anslutningar) omfattas av standard [avgifterna för Azure data överföring](https://azure.microsoft.com/pricing/details/bandwidth/).

* För data överförings avgifter mellan en virtuell WAN-hubb och en virtuell fjärran sluten virtuell WAN-hubb eller VNet i en annan region än käll-hubben gäller avgifterna för data överföring för trafik som lämnar en hubb. Exempel: trafik som lämnar en östra USA-hubb debiteras $0,02/GB till en västra USA-hubb. Trafik är ingen avgift för trafik som går in i hubben västra USA. All hubb till hubb trafik omfattas Inter-Region (Intra/interkontinental) avgifter för [Azure data överföring](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Vad är skillnaden mellan en standard avgift för hubb och en avgift för standard hubben?

Det virtuella WAN-nätverket finns i två varianter:

* Ett **grundläggande virtuellt WAN-nätverk** där användare kan distribuera flera hubbar och dra nytta av VPN plats-till-plats-anslutning. Ett grundläggande virtuellt WAN-nätverk har inte avancerade funktioner, till exempel helt nätbaserade nav, ExpressRoute-anslutning, VPN-anslutning via punkt-till-plats, anslutning mellan virtuella nätverk, VPN-och ExpressRoute-transit anslutning, eller Azure-brandvägg osv. Det finns ingen bas avgift eller data bearbetnings avgift för hubbar i ett grundläggande virtuellt WAN-nätverk.

* Ett **virtuellt WAN-nätverk** erbjuder avancerade funktioner, t. ex. helt nätbaserade nav, ExpressRoute-anslutning, VPN-anslutning från punkt till plats, anslutning mellan VNet-till-VNet, transitiv anslutning för VPN-och ExpressRoute-överföring, Azure-brandvägg osv. All routning för virtuell hubb tillhandahålls av en router som möjliggör flera tjänster i en virtuell hubb. Det finns en grund avgift för hubben, som priss ätts till $0,25/timme. Det finns också en avgift för data bearbetning i den virtuella hubben för anslutning mellan virtuella nätverk för VNet-till-VNet-överföring. Se följande figur.

 I **exemplet** nedan finns det två virtuella nätverk, VNet 1 och VNET 2. Vi antar att det finns 1 Gbit/s data som skickas från en virtuell dator i VNET 1 till en annan virtuell dator i VNET 2. Följande avgifter gäller:

* Bas avgift för virtuella nav $0,25/timme

* Data behandlings avgift för virtuell hubb på $0,02/GB för 1 Gbit/s

**Exempel**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Exempel":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).

* För aktuell prissättning, se [priser för virtuella WAN-nätverk](https://azure.microsoft.com/pricing/details/virtual-wan/).
