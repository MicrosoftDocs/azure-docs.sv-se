---
title: Felsök problem med Azure Route Server
description: Lär dig hur du felsöker problem med Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: how-to
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 83f1e83653c5674988cadcb5b54d3c675ae0b8b8
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489448"
---
# <a name="troubleshooting-azure-route-server-issues"></a>Fel sökning av problem med Azure Route Server

> [!IMPORTANT]
> Azure Route Server (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connectivity-issues"></a>Anslutningsproblem

### <a name="why-does-my-nva-lose-internet-connectivity-after-it-advertises-the-default-route-00000-to-azure-route-server"></a>Varför förlorar min NVA Internet anslutning när den har annonserat standard vägen (0.0.0.0/0) till Azure Route Server?
När din NVA meddelar standard vägen, programerar Azure Route Server den för alla virtuella datorer i det virtuella nätverket, inklusive själva NVA. Den här standard vägen ställer in NVA som nästa hopp för all Internet-baserad trafik. Om din NVA behöver Internet anslutning måste du konfigurera en [användardefinierad väg](../virtual-network/virtual-networks-udr-overview.md) för att åsidosätta den här standard vägen från NVA och bifoga UDR till under nätet där NVA är värdbaserad (se exemplet nedan). Annars fortsätter NVA-värddatorn att skicka den Internet-kopplade trafiken, inklusive den som skickas av NVA till NVA.

| Väg | Nästa hopp |
|-------|----------|
| 0.0.0.0/0 | Internet |


### <a name="why-can-i-ping-from-my-nva-to-the-bgp-peer-ip-on-azure-route-server-but-after-i-set-up-the-bgp-peering-between-them-i-cant-ping-the-same-ip-anymore-why-does-the-bgp-peering-go-down"></a>Varför kan jag pinga från min NVA till BGP-peer-IP på Azure Route Server, men när jag har konfigurerat BGP-peering mellan dem kan jag inte pinga samma IP-adress längre? Varför går BGP-peering ned?

I vissa NVA måste du lägga till en statisk väg för Azure Route Server-undernätet. Om till exempel Azure Route Server är i 10.0.255.0/27 och din NVA är i 10.0.1.0/24, måste du lägga till följande väg i routningstabellen i tabellen NVA:

| Väg | Nästa hopp |
|-------|----------|
| 10.0.255.0/27 | 10.0.1.1 |

10.0.1.1 är standard-gatewayens IP-adress i under nätet där din NVA (eller mer exakt, ett av nätverkskorten) finns.

### <a name="why-do-i-lose-connectivity-to-my-on-premises-network-over-expressroute-andor-azure-vpn-when-im-deploying-azure-route-server-to-a-virtual-network-that-already-has-expressroute-gateway-andor-azure-vpn-gateway"></a>Varför förlorar jag anslutningen till mitt lokala nätverk via ExpressRoute och/eller Azure VPN när jag distribuerar Azure Route server till ett virtuellt nätverk som redan har ExpressRoute gateway och/eller Azure VPN-gateway?
När du distribuerar Azure Route server till ett virtuellt nätverk måste vi uppdatera kontroll planet mellan gatewayerna och det virtuella nätverket. Under den här uppdateringen är det en tids period när de virtuella datorerna i det virtuella nätverket kommer att förlora anslutningen till det lokala nätverket. Vi rekommenderar starkt att du schemalägger ett underhåll för att distribuera Azure Route server i produktions miljön.  

## <a name="control-plane-issues"></a>Problem med kontroll planet

### <a name="why-is-the-bgp-peering-between-my-nva-and-the-azure-route-server-going-up-and-down-flapping"></a>Varför kommer BGP-peering mellan min NVA och Azure Route-servern att bli igång ("växlar")?

Orsaken till växlar kan bero på BGP timer-inställningen. Som standard är Keep-Alive på Azure Route Server inställd på 60 sekunder och timer-Down-timern är 180 sekunder.

### <a name="why-does-my-nva-not-receive-routes-from-azure-route-server-even-though-the-bgp-peering-is-up"></a>Varför tar min NVA inte emot vägar från Azure Route Server trots att BGP-peering är igång?

Det ASN som Azure Route Server använder är 65515. Se till att konfigurera ett annat ASN för din NVA så att en "eBGP"-session kan upprättas mellan din NVA och Azure Route server så att väg spridningen kan ske automatiskt. Se till att aktivera "Multi-hop" i BGP-konfigurationen eftersom din NVA och Azure Route Server finns i olika undernät i det virtuella nätverket.

### <a name="the-bgp-peering-between-my-nva-and-azure-route-server-is-up-i-can-see-routes-exchanged-correctly-between-them-why-arent-the-nva-routes-in-the-effective-routing-table-of-my-vm"></a>BGP-peering mellan mitt NVA och Azure Route Server är igång. Jag kan se vägar som utbyts korrekt mellan dem. Varför är inte NVA-vägarna i den effektiva routningstabellen för min virtuella dator? 

* Om den virtuella datorn finns i samma VNet som din NVA-och Azure Route-Server:

     Azure Route Server exponerar två BGP peer IP-adresser, som finns på två virtuella datorer som delar ansvaret för att skicka vägarna till alla andra virtuella datorer som körs i det virtuella nätverket. Var och en av dina NVA måste konfigurera två identiska BGP-sessioner (t. ex. använda samma som-antal, samma som sökväg och annonsera samma uppsättning vägar) till de två virtuella datorerna så att dina virtuella datorer i det virtuella nätverket kan få konsekvent routningsinformation från Azure Route Server. Se diagrammet nedan.

    ![Diagram som visar en virtuell nätverks installation med Route Server.](./media/faq/network-virtual-appliances.png)

    Om du har två eller fler instanser av NVA *kan* du annonsera olika som sökvägar för samma väg från olika NVA-instanser om du vill ange en NVA-instans som aktiv och den andra passiva.

* Om den virtuella datorn finns i ett annat virtuellt nätverk än det som är värd för din NVA och Azure Route Server. Kontrol lera om VNet-peering är aktiverat mellan de två virtuella nätverk *och* om Använd fjärrroute-Server är aktiverat på den virtuella datorns VNet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar en Azure Route-Server](quickstart-configure-route-server-powershell.md)
