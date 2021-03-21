---
title: Vad är Azure Route Server (för hands version)?
description: Lär dig hur Azure Route Server kan förenkla routningen mellan din virtuella nätverks installation (NVA) och ditt virtuella nätverk.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: d868c064b96f58ab3febc1fd3b7f20b74d507cb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437169"
---
# <a name="what-is-azure-route-server-preview"></a>Vad är Azure Route Server (för hands version)? 

Azure Route Server fören klar dynamisk routning mellan din virtuella nätverks installation (NVA) och ditt virtuella nätverk. Det gör att du kan utbyta routningsinformation direkt via Border Gateway Protocol (BGP) routningsprotokoll mellan alla NVA som har stöd för BGP-routningsprotokollet och Azure-SDN () i Azure-Virtual Network (VNET) utan att behöva konfigurera eller underhålla vägvals tabeller manuellt. Azure Route Server är en fullständigt hanterad tjänst och har kon figurer ATS med hög tillgänglighet.

> [!IMPORTANT]
> Azure Route Server (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Hur fungerar det?

Följande diagram illustrerar hur Azure Route Server fungerar med en SDWAN-NVA och en säkerhets NVA i ett virtuellt nätverk. När du har upprättat BGP-peering får Azure Route Server en lokal väg (10.250.0.0/16) från SDWAN-enheten och en standard väg (0.0.0.0/0) från brand väggen. Dessa vägar konfigureras sedan automatiskt på de virtuella datorerna i det virtuella nätverket. Detta innebär att all trafik som är avsedd för det lokala nätverket skickas till SDWAN-enheten. Medan all Internet-baserad trafik skickas till brand väggen. I motsatt riktning skickar Azure Route-servern den virtuella nätverks adressen (10.1.0.0/16) till båda NVA. SDWAN-enheten kan sprida den ytterligare till det lokala nätverket.

![Diagram som visar Azure Route server som kon figurer ATS i ett virtuellt nätverk.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Viktiga fördelar 

Azure Route Server fören klar konfiguration, hantering och distribution av dina NVA i ditt virtuella nätverk.  

* Du behöver inte längre uppdatera routningstabellen på din NVA manuellt när dina virtuella nätverks adresser uppdateras. 

* Du behöver inte längre uppdatera [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) manuellt när din NVA meddelar nya vägar eller drar tillbaka gamla. 

* Du kan peer-koppla flera instanser av din NVA med Azure Route Server. Du kan konfigurera BGP-attributen i din NVA och, beroende på din design (t. ex. Active-Active för prestanda eller aktiv-passiv för återhämtning), ge Azure Route Server veta vilken NVA-instans som är aktiv eller som en passiv. 

* Gränssnittet mellan NVA och Azure Route Server baseras på ett vanligt standard protokoll. Så länge din NVA har stöd för BGP kan du använda peer-servern med Azure Route Server. Mer information finns i [routningsprotokoll för Route-servrar som stöds](route-server-faq.md#protocol).

* Du kan distribuera Azure Route server i något av dina nya eller befintliga virtuella nätverk. 

## <a name="faq"></a>Vanliga frågor

Vanliga frågor och svar om Azure Route Server finns i [vanliga frågor och svar om Azure Route Server](route-server-faq.md).

## <a name="next-steps"></a>Nästa steg

- [Lär dig hur du konfigurerar Azure Route Server](quickstart-configure-route-server-powershell.md)
- [Lär dig hur Azure Route Server fungerar med Azure ExpressRoute och Azure VPN](expressroute-vpn-support.md)
