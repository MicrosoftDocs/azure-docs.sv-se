---
title: Vanliga frågor och svar om Azure Route Server
description: Få svar på vanliga frågor om Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 04/16/2021
ms.author: duau
ms.openlocfilehash: 0bbe16fb63a4546b4b4745df16074f6a4b0cb26b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599544"
---
# <a name="azure-route-server-preview-faq"></a>Vanliga frågor och svar om Azure Route Server (förhandsversion)

> [!IMPORTANT]
> Azure Route Server (förhandsversion) är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Vad är Azure Route Server?

Azure Route Server är en fullständigt hanterad tjänst som gör att du enkelt kan hantera routning mellan din virtuella nätverksinstallation (NVA) och ditt virtuella nätverk.

### <a name="is-azure-route-server-just-a-vm"></a>Är Azure Route Server bara en virtuell dator?

Nej. Azure Route Server är en tjänst utformad med hög tillgänglighet. Om den distribueras i en Azure-region [som stöder Tillgänglighetszoner](../availability-zones/az-overview.md)kommer den att ha zonnivåredundans.

### <a name="how-many-route-servers-can-i-create-in-a-virtual-network"></a>Hur många vägservrar kan jag skapa i ett virtuellt nätverk?

Du kan bara skapa en vägserver i ett VNet. Den måste distribueras i ett anvisat undernät med namnet *RouteServerSubnet*.

### <a name="does-azure-route-server-support-vnet-peering"></a>Stöder Azure Route Server VNet-peering?

Ja. Om du peer-peerar ett VNet som är värd för Azure Route Server till ett annat VNet och aktiverar Använd fjärrgateway på det senare virtuella nätverket, lär sig Azure Route Server adressutrymmen för det virtuella nätverket och skickar dem till alla peer-styrda NVA:er. Den kommer också att programmera vägarna från NVA:erna till routningstabellen för de virtuella datorerna i det peer-ade virtuella nätverket. 


### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Vilka routningsprotokoll stöder Azure Route Server?

Azure Route Server stöder endast Border Gateway Protocol (BGP). Din NVA måste ha stöd för extern BGP med flera hopp eftersom du måste distribuera Azure Route Server i ett dedikerat undernät i ditt virtuella nätverk. Den [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) som du väljer måste vara annorlunda än den som Azure Route Server använder när du konfigurerar BGP på din NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Dirigerar Azure Route Server datatrafik mellan min NVA och mina virtuella datorer?

Nej. Azure Route Server utbyter endast BGP-vägar med din NVA. Datatrafiken går direkt från den virtuella nätverksdatorn till den virtuella måldatorn och direkt från den virtuella datorn till den virtuella nätverksdatorn.

### <a name="does-azure-route-server-store-customer-data"></a>Lagrar Azure Route Server kunddata?
Nej. Azure Route Server utbyter endast BGP-vägar med din NVA och sprider dem sedan till ditt virtuella nätverk.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-how-does-it-handle-them"></a>Hur hanterar Azure Route Server samma väg från fler än en NVA?

Om vägen har samma AS-sökvägslängd programmerar Azure Route Server flera kopior av vägen, var och en med ett annat nästa hopp, till de virtuella datorerna i det virtuella nätverket. När de virtuella datorerna skickar trafik till målet för den här vägen, kommer DE virtuella värdarna att Equal-Cost ECMP-routning (Multi-Path). Men om en NVA skickar vägen med en kortare AS-sökvägslängd än andra NVA:er kommer Azure Route Server endast att programmera den väg som har nästa hopp inställt på denna NVA till de virtuella datorerna i det virtuella nätverket.

### <a name="does-azure-route-server-preserve-the-bgp-communities-of-the-route-it-receives"></a>Bevarar Azure Route Server BGP-communities för den väg som den tar emot?

Ja, Azure Route Server sprider vägen med BGP-communities som den är.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Vilka autonoma systemnummer (ASN) kan jag använda?

Du kan använda dina egna offentliga ASN:er eller privata ASN:er i din virtuella nätverksinstallation. Du kan inte använda intervallen som reserverats av Azure eller IANA.
Följande ASN:er är reserverade för Azure eller IANA:

* ASN-nätverk som reserverats av Azure:
    * Offentliga ASN:er: 8074, 8075, 12076
    * Privata ASN:er: 65515, 65517, 65518, 65519, 65520
* ASN-nätverk [som reserverats av IANA:](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kan jag använda 32-bitars (4 byte) ASN:er?

Nej, Azure Route Server stöder endast 16-bitars ASN:er (2 byte).

## <a name="route-server-limits"></a><a name = "limitations"></a>Vägserverbegränsningar

Azure Route Server har följande begränsningar (per distribution).

| Resurs | Gräns |
|----------|-------|
| Antal BGP-peers som stöds | 8 |
| Antal vägar som varje BGP-peer kan annonsera till Azure Route Server | 200 |
| Antal vägar som Azure Route Server kan annonsera till ExpressRoute eller VPN-gateway | 200 |

Om din NVA annonserar fler vägar än gränsen tas BGP-sessionen bort. Om detta inträffar för gatewayen och Azure Route Server förlorar du anslutningen från ditt lokala nätverk till Azure. Mer information finns i Diagnostisera [problem med routning av virtuella Azure-datorer.](../virtual-network/diagnose-network-routing-problem.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur [du konfigurerar Azure Route Server](quickstart-configure-route-server-powershell.md).
