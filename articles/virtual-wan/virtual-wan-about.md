---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Lär dig mer om det virtuella WAN-nätverket med automatisk skalbar anslutning från gren till gren, tillgängliga regioner och partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 06/29/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 451e1581350bb1d38580d00ffd24c781bc30242d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507592"
---
# <a name="about-azure-virtual-wan"></a>Om Azure Virtual WAN

Azure Virtual WAN är en nätverks tjänst som ger många funktioner för nätverk, säkerhet och routning tillsammans för att tillhandahålla ett enda drift gränssnitt. Dessa funktioner är till exempel filial anslutning (via anslutnings automatisering från virtuella WAN-partner enheter som SD-WAN eller VPN CPE). VPN-anslutning för plats-till-plats, fjärran sluten användare (punkt-till-plats), privat (ExpressRoute) anslutning, anslutning inom molnet (transitiv anslutning för virtuella nätverk), VPN-ExpressRoute mellan anslutning, routning, Azure-brandvägg och kryptering för privat anslutning. Du behöver inte ha alla dessa användnings fall för att börja använda Virtual WAN. Du kan helt enkelt komma igång med bara ett användnings fall och sedan justera nätverket när det utvecklas.

Den virtuella WAN-arkitekturen är en hubb och eker-arkitektur med skalning och prestanda som är inbyggda för grenar (VPN/SD-WAN-enheter), användare (Azure VPN/OpenVPN/IKEv2-klienter), ExpressRoute-kretsar och virtuella nätverk. Den möjliggör [Global arkitektur för nätverks arkitektur](virtual-wan-global-transit-network-architecture.md), där det molnbaserade nätverkets hubb möjliggör transitiv anslutning mellan slut punkter som kan distribueras mellan olika typer av ekrar.

Azure-regioner fungerar som hubbar som du kan välja att ansluta till. Alla hubbar är anslutna i hela nät i ett standard virtuellt WAN-nätverk som gör det enkelt för användaren att använda Microsofts stamnät för alla-till-alla-anslutningar (alla ekrar). För eker-anslutningar med SD-WAN/VPN-enheter kan användarna antingen manuellt konfigurera det i Azure Virtual WAN, eller använda partner lösningen för Virtual WAN-CPE (SD-WAN/VPN) för att konfigurera anslutning till Azure. Vi har en lista över partners som stöder anslutnings automatisering (möjlighet att exportera enhets informationen till Azure, ladda ned Azure-konfigurationen och upprätta anslutning) med Azure Virtual WAN. Mer information finns i artikeln om [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Den här artikeln ger en snabb överblick över nätverks anslutningen i Azure Virtual WAN. Virtual WAN har följande fördelar:

* **Integrerade anslutnings lösningar i hubb och eker:** Automatisera plats-till-plats-konfiguration och anslutning mellan lokala platser och en Azure-hubb.
* **Automatiserad installation och konfiguration av ekerplatser:** Anslut enkelt dina virtuella nätverk och arbetsbelastningar till Azure-navet.
* **Intuitiv fel sökning:** Du kan se flödet från slut punkt till slut punkt i Azure och sedan använda den här informationen för att utföra nödvändiga åtgärder.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Basic-och standard-virtuella WAN

Det finns två typer av virtuella WAN-enheter: Basic och standard. I följande tabell visas de tillgängliga konfigurationerna för respektive typ.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Anvisningar för hur du uppgraderar ett virtuellt WAN finns i [uppgradera ett virtuellt WAN-nätverk från Basic till standard](upgrade-virtual-wan.md).

## <a name="architecture"></a><a name="architecture"></a>Arkitektur

Information om virtuella WAN-arkitektur och hur du migrerar till virtuellt WAN finns i följande artiklar:

* [Virtuell WAN-arkitektur](migrate-from-hub-spoke-topology.md)
* [Global Transit Network-arkitektur](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN från slutpunkt till slutpunkt skapar du följande resurser:

* **virtualWAN:** virtualWAN-resursen representerar ett virtuellt överlägg på ditt Azure-nätverk och består av en samling med flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella hubbar i virtuella WAN-nätverk kommunicerar inte med varandra.

* **Nav:** Ett virtuellt nav är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänst slut punkter för att möjliggöra anslutning. Från ditt lokala nätverk (vpnsite) kan du ansluta till en VPN Gateway inuti den virtuella hubben, ansluta ExpressRoute-kretsar till en virtuell hubb eller till och med ansluta mobila användare till en punkt-till-plats-gateway i den virtuella hubben. Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du använder ett virtuellt WAN-nätverk kan du till exempel inte skapa en plats-till-plats-anslutning från din lokala plats direkt till ditt VNet. I stället skapar du en plats-till-plats-anslutning till hubben. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen.

* **Anslutning mellan nav och virtuellt nätverk:** Den här resursen används för att sömlöst ansluta navet till ditt virtuella nätverk.

* **Nav-till-hubb-anslutning:** Alla nav är anslutna till varandra i ett virtuellt WAN-nätverk. Detta innebär att en gren, användare eller VNet som är ansluten till en lokal hubb kan kommunicera med en annan gren eller VNet med den kompletta nät arkitekturen i de anslutna hubbarna. Du kan också ansluta virtuella nätverk i en hubb-överföring via det virtuella navet, samt virtuella nätverk över hubben, med hjälp av det anslutna nav-till-hubb-ramverket.

* **Navroutningstabell:** Du kan skapa en virtuell navväg och tillämpa vägen på den virtuella navroutningstabellen. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

**Ytterligare virtuella WAN-resurser**

* **Plats:** Den här resursen används endast för plats-till-plats-anslutningar. Plats resursen är **vpnsite**. Den representerar din lokala VPN-enhet och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typer av anslutningar

Virtuella WAN-nätverk möjliggör följande typer av anslutningar: plats-till-plats-VPN, användar-VPN (punkt-till-plats) och ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Plats-till-plats-VPN-anslutningar

Du kan ansluta till dina resurser i Azure via en plats-till-plats IPsec/IKE-anslutning (IKEv2). Mer information finns i [skapa en plats-till-plats-anslutning med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md). 

Den här typen av anslutning kräver en VPN-enhet eller en virtuell WAN-partner enhet. Virtuella WAN-partner ger automatisering för anslutning, vilket är möjligheten att exportera enhets informationen till Azure, ladda ned Azure-konfigurationen och upprätta anslutning till Azure Virtual WAN Hub. En lista över tillgängliga partners och platser finns i artikeln [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) . Om VPN/SD-WAN-providern inte finns med i den här länken kan du förenkla användningen av steg-för-steg-instruktionen [skapa en plats-till-plats-anslutning med hjälp av Virtual WAN](virtual-wan-site-to-site-portal.md) för att konfigurera anslutningen.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Användares VPN-anslutningar (punkt-till-plats)

Du kan ansluta till dina resurser i Azure via en IPsec/IKE (IKEv2) eller OpenVPN-anslutning. Den här typen av anslutning kräver att en VPN-klient konfigureras på klient datorn. Mer information finns i [skapa en punkt-till-plats-anslutning](virtual-wan-point-to-site-portal.md).

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-anslutningar
Med ExpressRoute kan du ansluta ett lokalt nätverk till Azure över en privat anslutning. Information om hur du skapar anslutningen finns i [skapa en ExpressRoute-anslutning med hjälp av virtuellt WAN](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>NAV-till-VNet-anslutningar

Du kan ansluta ett virtuellt Azure-nätverk till en virtuell hubb. Mer information finns i [ansluta ditt VNet till en hubb](virtual-wan-site-to-site-portal.md#vnet).

### <a name="transit-connectivity"></a><a name="transit"></a>Överförings anslutning

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Överförings anslutning mellan virtuella nätverk

Det virtuella WAN-nätverket tillåter överföring av anslutningar mellan virtuella nätverk. Virtuella nätverk ansluter till en virtuell hubb via en virtuell nätverks anslutning. Överförings anslutning mellan virtuella nätverk i **standard virtuella WAN-nätverk** aktive ras på grund av förekomsten av en router i varje virtuellt nav. Den här routern instansieras när den virtuella hubben skapas.

Routern kan ha fyra Dirigerings status: etablerad, etablering, misslyckad eller ingen. **Routningsstatus** finns i Azure Portal genom att gå till sidan virtuellt nav.

* **Ingen** status anger att den virtuella hubben inte etablerade routern. Detta kan inträffa om det virtuella WAN-nätverket är av typen *Basic*eller om den virtuella hubben distribuerades innan tjänsten är tillgänglig.
* Statusen **misslyckades anger att det** inte gick att instansiera. För att instansiera eller återställa routern kan du leta upp alternativet **Återställ router** genom att gå till översikts sidan för virtuella hubbar i Azure Portal.

Varje virtuell hubb-router stöder ett sammanställt data flöde på upp till 50 Gbit/s. Anslutning mellan virtuella nätverks anslutningar förutsätter totalt 2000 VM-arbetsbelastning över alla virtuella nätverk som är anslutna till en enda virtuell hubb.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Överförings anslutning mellan VPN och ExpressRoute

Det virtuella WAN-nätverket tillåter överföring av anslutningar mellan VPN-och ExpressRoute. Detta innebär att VPN-anslutna platser eller fjärran vändare kan kommunicera med ExpressRoute-anslutna platser. Det finns också ett implicit antagande om att **flaggan för Branch-till-gren** är aktive rad. Den här flaggan finns i Azures virtuella WAN-inställningar i Azure Portal. All väg hantering tillhandahålls av den virtuella Hub-routern, som också möjliggör överförings anslutning mellan virtuella nätverk.

### <a name="custom-routing"></a><a name="routing"></a>Anpassad routning

Det virtuella WAN-nätverket ger avancerade förbättringar av routningen. Möjlighet att konfigurera anpassade väg tabeller, optimera routning av virtuella nätverk med väg Association och-spridning, logiskt gruppera väg tabeller med etiketter och förenkla flera virtuella nätverks installationer eller delade tjänster.

### <a name="global-vnet-peering"></a><a name="global"></a>Global VNet-peering

Global VNet-peering är en mekanism för att ansluta två virtuella nätverk i olika regioner. I virtuella WAN-nätverk ansluter virtuella nätverks anslutningar virtuella nätverk till virtuella nav. Användaren behöver inte konfigurera global VNet-peering explicit. Virtuella nätverk som är anslutna till virtuella hubbar i samma region ådrar sig VNet-peering-kostnader. Virtuella nätverk som är anslutna till en virtuell hubb i en annan region ådrar sig globala VNet-peering-kostnader.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>Kryptering av ExpressRoute-trafik

Azure Virtual WAN ger möjlighet att kryptera din ExpressRoute-trafik. Tekniken ger en krypterad överföring mellan lokala nätverk och virtuella Azure-nätverk via ExpressRoute, utan att gå över det offentliga Internet eller använda offentliga IP-adresser. Mer information finns i [IPSec över ExpressRoute för virtuellt WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Platser

Plats information finns i artikeln [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) .

## <a name="route-tables-in-basic-and-standard-virtual-wans"></a><a name="route"></a>Routa tabeller i Basic-och standard-virtuella WAN

Routningstabeller har nu funktioner för Association och spridning. En fördefinierad routningstabell är en routningstabell som inte har dessa funktioner. Om du har befintliga vägar i hubben Routning och vill använda de nya funktionerna bör du tänka på följande:

* **Virtuella standard WAN-kunder med befintliga vägar i Virtual Hub**: om du vill använda nya funktioner i en routningstabell väntar du tills veckan 17 för distributionen i Azure ska slutföras. Om du har befintliga vägar i avsnittet routning för hubben i Azure Portal måste du först ta bort dem och sedan försöka skapa nya routningstabeller (finns i avsnittet Route tables för hubben i Azure Portal).

* **Grundläggande virtuella WAN-kunder med befintliga vägar i virtuell hubb**: om du vill använda de nya funktionerna för en routningstabell väntar du tills veckan i augusti 17 för distributionen i Azure ska slutföras. Om du har redan befintliga vägar i avsnittet routning för hubben i Azure Portal måste du först ta bort dem och sedan **Uppgradera** ditt grundläggande virtuella WAN-nätverk till standard. Se [uppgradera ett virtuellt WAN från Basic till standard](upgrade-virtual-wan.md).

## <a name="faq"></a><a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

[Skapa en plats-till-plats-anslutning med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md)
