---
title: Om Azure Route Server (för hands version) stöder för ExpressRoute och Azure VPN
description: Lär dig mer om hur Azure Route Server samverkar med ExpressRoute och Azure VPN-gatewayer.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: 6e588c7c0381c6825bcf75cbbe28a1dd6b865940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101680531"
---
# <a name="about-azure-route-server-preview-support-for-expressroute-and-azure-vpn"></a>Om stöd för Azure Route Server (för hands version) för ExpressRoute och Azure VPN

Azure Route Server stöder inte bara tredjeparts virtuella nätverks installationer (NVA) som körs på Azure, men som även integreras sömlöst med ExpressRoute och Azure VPN-gatewayer. Du behöver inte konfigurera eller hantera BGP-peering mellan gatewayen och Azure Route Server. Du kan aktivera Route Exchange mellan gatewayen och Azure Route server med en enkel [konfigurations ändring](quickstart-configure-route-server-powershell.md#route-exchange).

> [!IMPORTANT]
> Azure Route Server (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Hur fungerar det?

När du distribuerar en Azure Route-Server tillsammans med en ExpressRoute-gateway och en NVA i ett virtuellt nätverk som standard, sprids inte de vägar som tas emot från NVA-och ExpressRoute-gatewayen mellan varandra i Azure Route Server. När du aktiverar Route Exchange kan ExpressRoute och NVA lära sig var and ras vägar.

I följande diagram:

* SDWAN-enheten tas emot från Azure Route Server vägen från "on-lokal 2", som är ansluten till ExpressRoute, tillsammans med den virtuella nätverks vägen.

* ExpressRoute-gatewayen tar emot vägen från "on-lokal 1", som är ansluten till SDWAN-enheten, tillsammans med den virtuella nätverks vägen från Azure Route Server.

    ![Diagram som visar ExpressRoute som kon figurer ATS med Route Server.](./media/expressroute-vpn-support/expressroute-with-route-server.png)

Du kan också ersätta SDWAN-enheten med Azure VPN-gatewayen. Eftersom Azure VPN-gatewayen och ExpressRoute är fullständigt hanterade behöver du bara aktivera Route Exchange för de två lokala nätverken för att kommunicera med varandra.

> [!IMPORTANT] 
> Azure VPN-gatewayen måste konfigureras i [**aktivt-aktivt**](../vpn-gateway/vpn-gateway-activeactive-rm-powershell.md) läge.
>

![Diagram som visar ExpressRoute och VPN-gateway som kon figurer ATS med Route Server.](./media/expressroute-vpn-support/expressroute-and-vpn-with-route-server.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Route Server](route-server-faq.md).
- Lär dig hur du [konfigurerar Azure Route Server](quickstart-configure-route-server-powershell.md).
- Lär dig mer om [Azure ExpressRoute och Azure VPN-samexistens](../expressroute/expressroute-howto-coexist-resource-manager.md).
