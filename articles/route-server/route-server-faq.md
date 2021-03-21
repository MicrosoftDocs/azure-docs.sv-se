---
title: Vanliga frågor och svar om Azure Route Server
description: Få svar på vanliga frågor om Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102485427"
---
# <a name="azure-route-server-preview-faq"></a>Vanliga frågor och svar om Azure Route Server (för hands version)

> [!IMPORTANT]
> Azure Route Server (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>Vad är Azure Route Server?

Azure Route Server är en helt hanterad tjänst som gör att du enkelt kan hantera routning mellan din virtuella nätverks installation (NVA) och ditt virtuella nätverk.

### <a name="is-azure-route-server-just-a-vm"></a>Är Azure Route server bara en virtuell dator?

Nej. Azure Route Server är en tjänst som har utformats med hög tillgänglighet. Om den har distribuerats i en Azure-region som har stöd för [Tillgänglighetszoner](../availability-zones/az-overview.md), kommer den att ha redundans på zon nivå.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Vilka routningsprotokoll stöder Azure Route Server?

Azure Route Server stöder endast Border Gateway Protocol (BGP). Din NVA måste ha stöd för multi-hop extern BGP eftersom du måste Distribuera Azure Route server i ett dedikerat undernät i det virtuella nätverket. Det [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) du väljer måste skilja sig från den enda Azure Route server som används när du konfigurerar BGP på din NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>Dirigerar Azure Route data trafik mellan mina NVA och mina virtuella datorer?

Nej. Azure Route Server utbyter bara BGP-vägar med din NVA. Data trafiken går direkt från NVA till den valda virtuella datorn och direkt från den virtuella datorn till NVA.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Om Azure Route server tar emot samma väg från fler än en NVA, kommer den att program mera alla kopior av vägen (men var och en med olika nästa hopp) till de virtuella datorerna i det virtuella nätverket?

Ja, endast om vägen har samma sökväg som Sök vägs längden. När de virtuella datorerna skickar trafik till målet för den här vägen, kommer de virtuella dator värdarna att göra Equal-Cost ECMP-routning (multi-path). Men om en NVA skickar vägen med en kortare sökväg än andra NVA. Azure Route server kommer bara att program mera den väg som har nästa hopp angivet till den här NVA för de virtuella datorerna i det virtuella nätverket.

### <a name="does-azure-route-server-support-vnet-peering"></a>Stöder Azure Route Server VNet-peering?

Ja. Om du peer-koppla ett virtuellt nätverk som är värd för Azure Route-servern till ett annat VNet och aktiverar Använd fjärr-gateway på det virtuella nätverket. Azure Route server kommer att lära sig adress utrymmen för det virtuella nätverket och skicka dem till alla peer-NVA.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Vilka autonoma system nummer (ASN: er) kan jag använda?

Du kan använda din egen offentliga ASN: er eller privata ASN: er i din virtuella nätverks installation. Du kan inte använda de intervall som reserver ATS av Azure eller IANA.
Följande ASN: er är reserverade för Azure eller IANA:

* ASN: er reserverade av Azure:
    * Offentliga ASN:er: 8074, 8075, 12076
    * Privata ASN:er: 65515, 65517, 65518, 65519, 65520
* ASN: er [reserverade av IANA](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml):
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kan jag använda 32-bitars (4 byte) ASN: er?

Nej, Azure Route Server har endast stöd för 16-bitars (2 byte) ASN: er.

## <a name="route-server-limits"></a><a name = "limitations"></a>Väg Server gränser

Azure Route Server har följande gränser (per distribution).

| Resurs | Gräns |
|----------|-------|
| Antal BGP-peer-datorer som stöds | 8 |
| Antal vägar varje BGP-peer kan annonsera till Azure Route Server | 200 |
| Antal vägar som Azure Route Server kan annonsera till ExpressRoute eller VPN-gateway | 200 |

Om din NVA meddelar fler vägar än gränsen kommer BGP-sessionen att tas bort. Om detta sker till gatewayen och Azure Route server förlorar du anslutningen från ditt lokala nätverk till Azure. Mer information finns i [diagnostisera ett problem med Routning av virtuella Azure-datorer](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar Azure Route Server](quickstart-configure-route-server-powershell.md).
