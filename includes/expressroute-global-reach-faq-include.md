---
title: ta med fil
description: ta med fil
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187079"
---
### <a name="what-is-expressroute-global-reach"></a>Vad är ExpressRoute Global Reach?

ExpressRoute Global Reach är en Azure-tjänst som ansluter dina lokala nätverk via ExpressRoute-tjänsten via Microsofts globala nätverk. Om du till exempel har ett privat data Center i Kalifornien anslutet till ExpressRoute i Silicon dal och ett annat privat data Center i Texas som är anslutet till ExpressRoute i Borås, med ExpressRoute Global Reach, kan du ansluta dina privata data Center tillsammans via de två ExpressRoute-anslutningarna och trafiken i kors data centret går igenom Microsofts stamnät till nätverket.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Hur gör jag för att aktivera eller inaktivera ExpressRoute Global Reach?

Du aktiverar ExpressRoute Global Reach genom att ansluta dina ExpressRoute-kretsar tillsammans. Du inaktiverar funktionen genom att koppla bort kretsarna. Se [konfigurationen](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Behöver jag ExpressRoute Premium för ExpressRoute Global Reach?

Om dina ExpressRoute-kretsar finns i samma naturpolitisk region behöver du inte ExpressRoute Premium för att ansluta dem till varandra. Om två ExpressRoute-kretsar finns i olika politiska regioner behöver du ExpressRoute Premium för båda kretsarna för att möjliggöra anslutning mellan dem. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Hur debiteras jag för ExpressRoute Global Reach?

ExpressRoute möjliggör anslutning från ditt lokala nätverk till Microsofts moln tjänster. ExpressRoute Global Reach möjliggör anslutning mellan dina egna lokala nätverk via dina befintliga ExpressRoute-kretsar, och utnyttjar Microsofts globala nätverk. ExpressRoute Global Reach faktureras separat från den befintliga ExpressRoute-tjänsten. Det finns en tilläggs avgift för att aktivera den här funktionen på varje ExpressRoute-krets. Trafik mellan dina lokala nätverk som är aktiverade av ExpressRoute Global Reach faktureras för en utgående taxa vid källan och för en ingångs takt vid målet. Priserna baseras på den zon där kretsarna finns.

### <a name="where-is-expressroute-global-reach-supported"></a>Var stöds ExpressRoute Global Reach?

ExpressRoute Global Reach stöds i [utvalda länder/regioner eller platser](../articles/expressroute/expressroute-global-reach.md). ExpressRoute-kretsarna måste skapas på peering-platserna i dessa länder/regioner eller platser.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Jag har fler än två lokala nätverk, var och en ansluten till en ExpressRoute-krets. Kan jag aktivera ExpressRoute Global Reach för att ansluta alla mina lokala nätverk tillsammans?

Ja, du kan så länge kretsarna finns i de länder/regioner som stöds. Du måste ansluta två ExpressRoute-kretsar i taget. Om du vill skapa ett helt nätverk med ett helt nätverk måste du räkna upp alla krets par och upprepa konfigurationen. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Kan jag aktivera ExpressRoute Global Reach mellan två ExpressRoute-kretsar på samma peering-plats?

Nej. De två kretsarna måste vara från olika peering-platser. Om en tunnelbane i ett land/region som stöds har mer än en ExpressRoute-peering-plats kan du ansluta samman de ExpressRoute-kretsar som skapats på olika peering-platser i denna tunnelbane linje. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Om ExpressRoute Global Reach är aktiverat mellan krets X och krets Y, och mellan krets Y och krets Z, kommer mina lokala nätverk som är anslutna till krets X och krets Z att prata med varandra via Microsofts nätverk?

Nej. Om du vill aktivera anslutningar mellan två av dina lokala nätverk måste du ansluta motsvarande ExpressRoute-kretsar uttryckligen. I exemplet ovan måste du ansluta krets X och krets Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Vad förväntar sig nätverks genom strömningen mellan mina lokala nätverk när jag har aktiverat ExpressRoute Global Reach?

Nätverks data flödet mellan dina lokala nätverk, som aktive ras av ExpressRoute Global Reach, är ett tak av den mindre av de två ExpressRoute-kretsarna. Lokal-till-Azure-trafik och lokal-till-lokal-trafik delar samma krets och omfattas av samma bandbredds begränsning. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Vilka ExpressRoute-Global Reach, vilka är gränserna för antalet vägar som jag kan annonsera och hur många vägar jag får?

Antalet vägar som du kan annonsera till Microsoft på Azures privata peering är kvar på 4000 på en standard-krets eller 10000 på en Premium-krets. Antalet vägar som du får från Microsoft på privat Azure-peering är summan av vägarna för dina virtuella Azure-nätverk och vägarna från dina andra lokala nätverk som är anslutna via ExpressRoute Global Reach. Se till att du anger en lämplig maxgräns för den lokala routern. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Vad är service avtalet för ExpressRoute Global Reach?

ExpressRoute Global Reach ger samma [tillgänglighets-SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) som den vanliga ExpressRoute-tjänsten.
