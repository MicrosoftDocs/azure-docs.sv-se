---
title: Azures över-nätverks anslutning
description: På den här sidan beskrivs ett program scenario för anslutning mellan nätverk och lösning baserad på funktioner i Azure-nätverk.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 04/03/2019
ms.author: duau
ms.openlocfilehash: 018afa1b2a31ebd44925a3fc79cbdc729b2d4695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202505"
---
# <a name="cross-network-connectivity"></a>Anslutning mellan nätverk

Fabrikam Inc. har en stor fysisk närvaro och Azure-distribution i USA, östra. Fabrikam har Server dels anslutningar mellan lokala och Azure-distributioner via ExpressRoute. På samma sätt har contoso Ltd. en närvaro och Azure-distribution i USA, västra. Contoso har Server dels anslutningar mellan lokala och Azure-distributioner via ExpressRoute.  

Fabrikam Inc. köper contoso Ltd. Efter sammanslagningen vill Fabrikam ansluta nätverken till varandra. Följande bild illustrerar scenariot:

![Program scenariot](./media/cross-network-connectivity/premergerscenario.png)

De streckade pilarna i mitten av figuren ovan visar önskade nätverks anslutningar. Mer specifikt finns det tre typer av korslänkade anslutningar: 1) Fabrikam och contoso virtuella nätverk Cross Connect, 2) korsande regionala lokala och virtuella nätverk kors anslutningar (det vill säga ansluter Fabrikam lokalt nätverk till contoso VNet och ansluta contoso lokalt nätverk till Fabrikam VNet) och 3) Fabrikam och contoso on-premises Network-Interconnect. 

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Contoso Ltd, före fusionen.

![Contoso ExpressRoute Route-tabellen före fusionen](./media/cross-network-connectivity/contosoexr-rt-premerger.png)

I följande tabell visas effektiva vägar för en virtuell dator i Contoso-prenumerationen, före fusionen. Per tabell är den virtuella datorn i VNet medveten om VNet-adressutrymmet och det lokala nätverket Contoso, förutom standardvärdena.

![Contoso VM-vägar före fusion](./media/cross-network-connectivity/contosovm-routes-premerger.png)

I följande tabell visas routningstabellen för den privata peering-ExpressRoute av Fabrikam Inc. före fusionen.

![Fabrikam ExpressRoute route-tabell före fusion](./media/cross-network-connectivity/fabrikamexr-rt-premerger.png)

I följande tabell visas effektiva vägar för en virtuell dator i den Fabrikam-prenumerationen innan fusionen. Per tabell är den virtuella datorn i VNet medveten om det virtuella nätverkets adress utrymme och det lokala nätverket Fabrikam, förutom standardvärdena.

![Fabrikam VM-vägar före fusion](./media/cross-network-connectivity/fabrikamvm-routes-premerger.png)

I den här artikeln går vi igenom steg för steg och diskuterar hur du uppnår önskade kors anslutningar med följande funktioner i Azure-nätverket:

* [Peering för virtuella nätverk][Virtual network peering] 
* [ExpressRoute anslutning för virtuellt nätverk][connection]
* [Global Reach][Global Reach] 

## <a name="cross-connecting-vnets"></a>Korsa anslutning virtuella nätverk

Det virtuella nätverkets peering (VNet-peering) ger den optimala och bästa nätverks prestanda vid anslutning av två virtuella nätverk. VNet-peering stöder peering av två virtuella nätverk i samma Azure-region (kallas vanligt vis VNet-peering) och i två olika Azure-regioner (vanligt vis kallade global VNet-peering). 

Vi konfigurerar global VNet-peering mellan virtuella nätverk i Contoso och Fabrikam Azure-prenumerationer. Information om hur du skapar peering för virtuella nätverk mellan två virtuella nätverk finns i artikeln [skapa en peering för virtuella nätverk][Configure VNet peering] .

Följande bild visar nätverks arkitekturen när du har konfigurerat global VNet-peering.

![Arkitekturen efter VNet-peering](./media/cross-network-connectivity/vnet-peering.png )

I följande tabell visas de vägar som är kända för den virtuella Contoso-prenumerationen. Var uppmärksam på den sista posten i tabellen. Den här posten är resultatet av att koppla samman de virtuella nätverken.

![Contoso VM-vägar efter VNet-peering](./media/cross-network-connectivity/contosovm-routes-peering.png)

I följande tabell visas de vägar som är kända för den virtuella fabriks prenumerationen. Var uppmärksam på den sista posten i tabellen. Den här posten är resultatet av att koppla samman de virtuella nätverken.

![Fabrikam VM-vägar efter VNet-peering](./media/cross-network-connectivity/fabrikamvm-routes-peering.png)

VNet-peering länkar direkt två virtuella nätverk (se det finns inget nästa hopp för *VNetGlobalPeering* -posten i ovanstående två tabeller)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Korsa anslutning av virtuella nätverk till lokala nätverk

Vi kan ansluta en ExpressRoute-krets till flera virtuella nätverk. Se [prenumerations-och tjänst begränsningar][Subscription limits] för maximalt antal virtuella nätverk som kan anslutas till en ExpressRoute-krets. 

Nu ska vi ansluta Fabrikam ExpressRoute-kretsen till contoso-prenumerationens VNet och på samma sätt contoso ExpressRoute-krets till Fabrikam-prenumerationens VNet för att möjliggöra anslutning mellan virtuella nätverk och lokala nätverk. För att ansluta ett virtuellt nätverk till en ExpressRoute-krets i en annan prenumeration måste vi skapa och använda en auktorisering.  Se artikeln: [ansluta ett virtuellt nätverk till en ExpressRoute-krets][Connect-ER-VNet].

Följande bild visar nätverks arkitekturen när du har konfigurerat ExpressRoute-Cross-anslutningen till de virtuella nätverken.

![Arkitekturen efter ExpressRoute-kors anslutning](./media/cross-network-connectivity/exr-x-connect.png)

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Contoso Ltd., efter att ha kopplat virtuella nätverk över till lokala nätverk via ExpressRoute. Se att routningstabellen har vägar som hör till båda de virtuella nätverken.

![Contoso ExpressRoute route-tabell efter kors anslutning av ExR och virtuella nätverk](./media/cross-network-connectivity/contosoexr-rt-xconnect.png)

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Fabrikam Inc. efter att ha kopplat samman virtuella nätverk till de lokala nätverken via ExpressRoute. Se att routningstabellen har vägar som hör till båda de virtuella nätverken.

![Fabrikam ExpressRoute Route Table efter att ha kopplat ExR och virtuella nätverk](./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png)

I följande tabell visas de vägar som är kända för den virtuella Contoso-prenumerationen. Var noga med de *virtuella nätverksgateway* för tabellen. Den virtuella datorn ser vägar för både lokala nätverk.

![Contoso VM-vägar efter mellan koppling av ExR och virtuella nätverk](./media/cross-network-connectivity/contosovm-routes-xconnect.png)

I följande tabell visas de vägar som är kända för den virtuella fabriks prenumerationen. Var noga med de *virtuella nätverksgateway* för tabellen. Den virtuella datorn ser vägar för både lokala nätverk.

![Fabrikam VM-vägar efter mellan koppling av ExR och virtuella nätverk](./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png)

>[!NOTE]
>I antingen Fabrikam-och/eller Contoso-prenumerationer kan du också ha eker-virtuella nätverk till respektive hubb-VNet (en nav och eker-design illustreras inte i arkitektur diagrammen i den här artikeln). Kors anslutningarna mellan Hub VNet-gatewayerna till ExpressRoute kommer också att tillåta kommunikation mellan öst-och västra hubbar och ekrar.
>

## <a name="cross-connecting-on-premises-networks"></a>Korsa anslutning av lokala nätverk

ExpressRoute Global Reach ger anslutning mellan lokala nätverk som är anslutna till olika ExpressRoute-kretsar. Vi konfigurerar Global Reach mellan contoso-och Fabrikam ExpressRoute-kretsar. Eftersom ExpressRoute-kretsarna finns i olika prenumerationer måste vi skapa och använda en auktorisering. Mer information om steg för steg finns i [Konfigurera ExpressRoute Global Reach][Configure Global Reach] artikel.

Följande bild visar nätverks arkitekturen när du har konfigurerat Global Reach.

![Arkitekturen efter att du har konfigurerat Global Reach](./media/cross-network-connectivity/globalreach.png)

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Contoso Ltd. efter att du har konfigurerat Global Reach. Se att routningstabellen har vägar som hör till både det lokala nätverket. 

![Contoso ExpressRoute Route-tabellen efter Global Reach](./media/cross-network-connectivity/contosoexr-rt-gr.png)

I följande tabell visas routningstabellen för den privata peering-ExpressRoute för Fabrikam Inc. När du har konfigurerat Global Reach. Se att routningstabellen har vägar som hör till både det lokala nätverket.

![Fabrikam ExpressRoute Route Table efter Global Reach]( ./media/cross-network-connectivity/fabrikamexr-rt-gr.png )

## <a name="next-steps"></a>Nästa steg

Se [vanliga frågor och svar om virtuella nätverk][VNet-FAQ]för ytterligare frågor om VNet och VNET-peering. Se [vanliga][ER-FAQ] frågor och svar om ExpressRoute för ytterligare frågor om ExpressRoute och virtuell nätverks anslutning.

Global Reach distribueras på ett land/en region efter land/region. Se [ExpressRoute Global Reach][Global Reach]för att se om Global Reach är tillgänglig i de länder/regioner som du vill ha.

<!--Link References-->
[Virtual network peering]: ../virtual-network/virtual-network-peering-overview.md
[connection]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[Global Reach]: ./expressroute-global-reach.md
[Configure VNet peering]: ../virtual-network/create-peering-different-subscriptions.md
[Configure Global Reach]: ./expressroute-howto-set-global-reach.md
[Subscription limits]: ../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits
[Connect-ER-VNet]: ./expressroute-howto-linkvnet-portal-resource-manager.md
[ER-FAQ]: ./expressroute-faqs.md
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq