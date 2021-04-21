---
title: Översikt över Azure Virtual WAN | Microsoft Docs
description: Lär dig Virtual WAN automatiserad skalbar gren-till-gren-anslutning, tillgängliga regioner och partner.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ab54192480a1c36a27c175254d6d4d275b67c8b7
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835334"
---
# <a name="what-is-azure-virtual-wan"></a>Vad är Azure Virtual WAN?

Azure Virtual WAN är en nätverkstjänst som sammanför många funktioner för nätverk, säkerhet och routning för att tillhandahålla ett enda driftsgränssnitt. Dessa funktioner omfattar grenanslutning (via anslutningsautomatisering från Virtual WAN Partner-enheter som SD-WAN eller VPN CPE), PLATS-till-plats VPN-anslutning, VPN-anslutning för fjärranvändare (punkt-till-plats), privat anslutning (ExpressRoute), anslutning mellan moln (transitiv anslutning för virtuella nätverk), VPN ExpressRoute-anslutning, routning, Azure Firewall och kryptering för privat anslutning. Du behöver inte ha alla dessa användningsfall för att börja använda Virtual WAN. Du kan helt enkelt komma igång med bara ett användningsfall och sedan justera nätverket allt eftersom det utvecklas.

Arkitekturen Virtual WAN är en nav- och ekerarkitektur med inbyggd skalning och prestanda för grenar (VPN/SD-WAN-enheter), användare (Azure VPN/OpenVPN/IKEv2-klienter), ExpressRoute-kretsar och virtuella nätverk. Det möjliggör global överföringsnätverksarkitektur, där det molnbaserade nätverket "hub" möjliggör transitiv anslutning mellan slutpunkter som kan distribueras över olika typer av "ekrar". [](virtual-wan-global-transit-network-architecture.md)

Azure-regioner fungerar som hubbar som du kan välja att ansluta till. Alla hubbar är anslutna i ett helt nät i ett Standard Virtual WAN vilket gör det enkelt för användaren att använda Microsofts stamnät för alla-till-alla-anslutningar (alla ekrar). För ekeranslutning med SD-WAN-/VPN-enheter kan användarna antingen konfigurera den manuellt i Azure Virtual WAN eller använda SD-WAN/VPN-partnerlösningen (Virtual WAN CPE) för att konfigurera anslutning till Azure. Vi har en lista över partner som stöder anslutningsautomatisering (möjlighet att exportera enhetsinformation till Azure, ladda ned Azure-konfigurationen och upprätta anslutning) med Azure Virtual WAN. Mer information finns i artikeln [Virtual WAN partner och](virtual-wan-locations-partners.md) platser.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Den här artikeln ger en snabb översikt över nätverksanslutningen i Azure Virtual WAN. Virtual WAN har följande fördelar:

* **Integrerade anslutningslösningar i nav och ekrar:** Automatisera plats-till-plats-konfiguration och anslutning mellan lokala platser och en Azure-hubb.
* **Automatiserad installation och konfiguration av ekerplatser:** Anslut enkelt dina virtuella nätverk och arbetsbelastningar till Azure-navet.
* **Intuitiv felsökning:** Du kan se flödet från start till slut i Azure och sedan använda den här informationen för att vidta nödvändiga åtgärder.

## <a name="basic-and-standard-virtual-wans"></a><a name="basicstandard"></a>Grundläggande och standard virtuella WAN

Det finns två typer av virtuella WAN: Basic och Standard. I följande tabell visas tillgängliga konfigurationer för varje typ.

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

Anvisningar för hur du uppgraderar ett virtuellt WAN finns [i Uppgradera ett virtuellt WAN från Basic till Standard.](upgrade-virtual-wan.md)

## <a name="architecture"></a><a name="architecture"></a>Arkitektur

Information om Virtual WAN arkitektur och hur du migrerar till Virtual WAN finns i följande artiklar:

* [Virtual WAN arkitektur](migrate-from-hub-spoke-topology.md)
* [Global Transit Network-arkitektur](virtual-wan-global-transit-network-architecture.md)

## <a name="virtual-wan-resources"></a><a name="resources"></a>Virtual WAN-resurser

Om du vill konfigurera ett virtuellt WAN från slutpunkt till slutpunkt skapar du följande resurser:

* **virtualWAN:** virtualWAN-resursen representerar ett virtuellt överlägg på ditt Azure-nätverk och består av en samling med flera resurser. Den innehåller länkar till alla virtuella nav som ska ingå i det virtuella WAN-nätverket. Virtual WAN-resurser är isolerade från varandra och kan inte innehålla ett gemensamt nav. Virtuella hubbar över Virtual WAN kommunicerar inte med varandra.

* **Nav:** Ett virtuellt nav är ett Microsoft-hanterat virtuellt nätverk. Hubben innehåller olika tjänstslutpunkter för att möjliggöra anslutning. Från ditt lokala nätverk (vpnsite) kan du ansluta till en VPN Gateway i den virtuella hubben, ansluta ExpressRoute-kretsar till en virtuell hubb eller till och med ansluta mobila användare till en punkt-till-plats-gateway i den virtuella hubben. Navet är kärnan i ditt nätverk i en region. Det kan bara finnas ett nav per Azure-region.

  En nav-gateway är inte detsamma som en virtuell nätverksgateway som du använder för ExpressRoute och VPN Gateway. När du till exempel Virtual WAN en plats-till-plats-anslutning från din lokala plats direkt till ditt VNet. I stället skapar du en plats-till-plats-anslutning till hubben. Trafiken passerar alltid nav-gatewayen. Det innebär att dina virtuella nätverk inte behöver egna gateways för virtuella nätverk. Med Virtual WAN kan dina virtuella nätverk enkelt dra nytta av skalning via det virtuella navet och den virtuella nav-gatewayen.

* **Anslutning mellan nav och virtuellt nätverk:** Den här resursen används för att sömlöst ansluta navet till ditt virtuella nätverk.

* **Hubb-till-hubb-anslutning:** Alla hubbar är anslutna till varandra i ett virtuellt WAN. Detta innebär att en gren, användare eller ett VNet som är anslutet till en lokal hubb kan kommunicera med en annan gren eller ett VNet med hjälp av arkitekturen med fullständigt nät i de anslutna hubbarna. Du kan också ansluta virtuella nätverk inom en hubb som överförs via den virtuella hubben, samt virtuella nätverk över hubben, med hjälp av det hubb-till-hubb-anslutna ramverket.

* **Navroutningstabell:** Du kan skapa en virtuell navväg och tillämpa vägen på den virtuella navroutningstabellen. Du kan använda flera vägar i routningstabellen för den virtuella hubben.

**Ytterligare Virtual WAN resurser**

* **Webbplats:** Den här resursen används endast för plats-till-plats-anslutningar. Platsresursen är **vpnsite**. Den representerar din lokala VPN-enhet och dess inställningar. Om du arbetar med en Virtual WAN-partner har du en inbyggd lösning som hjälper dig att automatiskt exportera den här informationen till Azure.

## <a name="types-of-connectivity"></a><a name="connectivity"></a>Typer av anslutningar

Virtual WAN tillåter följande typer av anslutningar: plats-till-plats-VPN, användar-VPN (punkt-till-plats) och ExpressRoute.

### <a name="site-to-site-vpn-connections"></a><a name="s2s"></a>Plats-till-plats-VPN-anslutningar

Du kan ansluta till dina resurser i Azure via en plats-till-plats-IPsec/IKE-anslutning (IKEv2). Mer information finns i Skapa [en plats-till-plats-anslutning med hjälp av Virtual WAN](virtual-wan-site-to-site-portal.md). 

Den här typen av anslutning kräver en VPN-enhet eller en Virtual WAN partnerenhet. Virtual WAN tillhandahåller automatisering för anslutning, vilket är möjligheten att exportera enhetsinformation till Azure, ladda ned Azure-konfigurationen och upprätta en anslutning till Azure Virtual WAN hub. En lista över tillgängliga partner och platser finns i artikeln [om Virtual WAN partner och](virtual-wan-locations-partners.md) platser. Om VPN/SD-WAN-enhetsprovidern inte finns med på den angivna länken kan du bara använda den stegvisa instruktionen Skapa en [plats-till-plats-anslutning](virtual-wan-site-to-site-portal.md) med Virtual WAN för att konfigurera anslutningen.

### <a name="user-vpn-point-to-site-connections"></a><a name="uservpn"></a>Vpn-anslutningar för användare (punkt-till-plats)

Du kan ansluta till dina resurser i Azure via en IPsec/IKE-anslutning (IKEv2) eller OpenVPN. Den här typen av anslutning kräver att en VPN-klient konfigureras på klientdatorn. Mer information finns i [Skapa en punkt-till-plats-anslutning.](virtual-wan-point-to-site-portal.md)

### <a name="expressroute-connections"></a><a name="er"></a>ExpressRoute-anslutningar
Med ExpressRoute kan du ansluta ett lokalt nätverk till Azure via en privat anslutning. Information om hur du skapar anslutningen finns [i Skapa en ExpressRoute-anslutning med hjälp av Virtual WAN](virtual-wan-expressroute-portal.md).

### <a name="hub-to-vnet-connections"></a><a name="hub"></a>Hubb-till-VNet-anslutningar

Du kan ansluta ett virtuellt Azure-nätverk till en virtuell hubb. Mer information finns i Ansluta [ditt VNet till en hubb.](virtual-wan-site-to-site-portal.md#vnet)

### <a name="transit-connectivity"></a><a name="transit"></a>Överföringsanslutning

#### <a name="transit-connectivity-between-vnets"></a><a name="transit-vnet"></a>Överföringsanslutning mellan virtuella nätverk

Virtual WAN tillåter överföringsanslutning mellan virtuella nätverk. Virtuella nätverk ansluter till en virtuell hubb via en virtuell nätverksanslutning. Överföringsanslutningen mellan de virtuella **nätverken i Standard Virtual WAN** aktiveras på grund av förekomsten av en router i varje virtuell hubb. Den här routern instansieras när den virtuella hubben först skapas.

Routern kan ha fyra routningsstatusar: Etablerade, Etablering, Misslyckades eller Ingen. **Routningsstatusen** finns i Azure Portal genom att gå till sidan Virtuell hubb.

* **Statusen** Ingen anger att den virtuella hubben inte etablerade routern. Detta kan inträffa om Virtual WAN är av *typen Basic*, eller om den virtuella hubben har distribuerats innan tjänsten görs tillgänglig.
* Statusen **Misslyckad** anger att instansen misslyckades. För att instansiera eller återställa routern kan du hitta alternativet Återställ **router** genom att gå till översiktssidan för den virtuella hubben i Azure Portal.

Varje virtuell hubbrouter stöder ett aggregerat dataflöde på upp till 50 Gbit/s. Anslutningen mellan de virtuella nätverksanslutningarna förutsätter en total arbetsbelastning på 2 000 virtuella datorer i alla virtuella nätverk som är anslutna till en enda virtuell hubb.

#### <a name="transit-connectivity-between-vpn-and-expressroute"></a><a name="transit-er"></a>Överföringsanslutning mellan VPN och ExpressRoute

Virtual WAN tillåter överföringsanslutning mellan VPN och ExpressRoute. Detta innebär att VPN-anslutna platser eller fjärranvändare kan kommunicera med ExpressRoute-anslutna platser. Det finns också ett implicit antagande att **flaggan för gren-till-gren** är aktiverad och BGP stöds i VPN- och ExpressRoute-anslutningar. Den här flaggan finns i Azure Virtual WAN i Azure Portal. All väghantering tillhandahålls av den virtuella hubbrouter, vilket även möjliggör överföringsanslutning mellan virtuella nätverk.

### <a name="custom-routing"></a><a name="routing"></a>Anpassad routning

Virtual WAN avancerade routningsförbättringar. Möjlighet att konfigurera anpassade routningstabeller, optimera routning av virtuella nätverk med routningsassociation och spridning, logiskt gruppera routningstabeller med etiketter och förenkla många nätverks-NVA-dirigeringsscenarier eller routningsscenarier för delade tjänster.

### <a name="global-vnet-peering"></a><a name="global"></a>Global VNet-peering

Global VNet-peering ger en mekanism för att ansluta två virtuella nätverk i olika regioner. I Virtual WAN ansluter anslutningar för virtuella nätverk virtuella nätverk till virtuella hubbar. Användaren behöver inte konfigurera global VNet-peering explicit. VNet som är anslutna till en virtuell hubb i samma region medför VNet-peeringavgifter. Virtuella nätverk som är anslutna till en virtuell hubb i en annan region debiteras för global VNet-peering.

### <a name="expressroute-traffic-encryption"></a><a name="encryption"></a>ExpressRoute-trafikkryptering

Azure Virtual WAN ger möjlighet att kryptera ExpressRoute-trafiken. Tekniken ger en krypterad överföring mellan de lokala nätverken och virtuella Azure-nätverk via ExpressRoute, utan att gå via det offentliga Internet eller använda offentliga IP-adresser. Mer information finns i [IPsec över ExpressRoute för Virtual WAN](vpn-over-expressroute.md).

## <a name="locations"></a><a name="locations"></a>Platser

Platsinformation finns i artikeln [om Virtual WAN partner och](virtual-wan-locations-partners.md) platser.

## <a name="route-tables-for-basic-and-standard-virtual-wans"></a><a name="route"></a>Vägtabeller för virtuella WAN med Basic och Standard

Vägtabeller har nu funktioner för association och spridning. En befintlig vägtabell är en vägtabell som inte har dessa funktioner. Om du har befintliga vägar i hubbroutning och vill använda de nya funktionerna bör du tänka på följande:

* **Standard Virtual WAN-kunder** med befintliga vägar i den virtuella hubben: Om du har befintliga vägar i avsnittet Routning för hubben i Azure Portal måste du först ta bort dem och sedan försöka skapa nya routningstabeller (finns i avsnittet Routningstabeller för hubben i Azure Portal). Vi rekommenderar starkt att du gör borttagningssteget för alla hubbar i en Virtual WAN.

* **Basic Virtual WAN-kunder** med befintliga vägar i den virtuella hubben: Om du har befintliga vägar i avsnittet Routning för hubben  i Azure Portal måste du först ta bort dem och sedan uppgradera din Basic-Virtual WAN till Standard Virtual WAN. Se [Uppgradera ett virtuellt WAN från Basic till Standard.](upgrade-virtual-wan.md) Vi rekommenderar starkt att du gör borttagningssteget för alla hubbar i en Virtual WAN.

## <a name="faq"></a><a name="faq"></a>Vanliga frågor

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="whats-new"></a><a name="new"></a>Vad är det senaste?

Prenumerera på RSS-flödet och visa de senaste Virtual WAN funktionsuppdateringarna på [sidan Azure-uppdateringar.](https://azure.microsoft.com/updates/?category=networking&query=VIRTUAL%20WAN)

## <a name="next-steps"></a>Nästa steg

[Skapa en plats-till-plats-anslutning med Virtual WAN](virtual-wan-site-to-site-portal.md)
