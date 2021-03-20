---
title: Val av Azure-sökväg i flera ISP-länkar
titleSuffix: Azure Virtual WAN
description: Lär dig om val av Azure-sökvägar och virtuellt WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: f24696c0db3155a59106e1361b01454b9ac16a20
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91267762"
---
# <a name="azure-path-selection-across-multiple-isp-links"></a>Val av Azure-sökväg i flera ISP-länkar

Azure Virtual WAN ger en användare möjlighet att inkludera länk information på en VPN-webbplats, vilket möjliggör scenarier där VPN/SD-WAN-enhets lösningen kan programmera Branch-/regionsspecifika principer för att styra trafik mellan olika länkar i Azure. Detta kallas för **Val av Azure-sökväg**.

## <a name="architecture"></a>Arkitektur

För att förstå hur valet av Azure-sökvägar fungerar, ska vi använda exemplet på en virtuell WAN-webbplats och en plats-till-plats-anslutning.

En VPN-plats representerar den lokala SD-WAN/VPN-enheten med information som offentlig IP, enhets modell och namn osv. Den faktiska lokala VPN-platsen kan ha flera ISP-länkar som också kan inkluderas i virtuella WAN-webbplatsens information. På så sätt kan du Visa länk informationen i Azure.

En plats-till-plats-IPsec-anslutning som kommer till ett virtuellt WAN-nätverk avslutas med VPN gateway-instanserna i en virtuell hubb. En plats-till-plats-anslutning representerar anslutningen mellan VPN-platsen och Azure VPN-gatewayen. Det består av en eller flera länk anslutningar. Varje länk anslutning består av två tunnlar där varje tunnel slutar på en unik instans av Azure Virtual WAN VPN gateway. Upp till fyra länk anslutningar kan konfigureras i plats-till-plats-anslutningen, vilket gör det möjligt att ha upp till åtta tunnlar inom en plats-till-plats-anslutning. Azure har stöd för upp till 2000 tunnlar i en enda virtuell WAN-Gateway.

:::image type="content" source="./media/path-selection-multiple-links/multi-link-site.png" alt-text="Diagram med flera länkar":::

Den här bilden visar flera länkar på en plats som ansluter till Azure Virtual WAN. I det här diagrammet:

* Det finns två ISP-länkar på den lokala grenen (VPN/SD-WAN-enhet). Varje ISP-länk motsvarar en länk anslutning.

* Det förutsätter att den lokala VPN/SD-WAN-enheten för kund-Manager stöder IKEv1 eller IKEv2 IPsec.

* Varje Azure plats-till-plats-virtuell WAN-anslutning består av länk anslutningar i sig själva. En anslutning har stöd för upp till fyra länk anslutningar. Azure debiterar en anslutnings enhets avgift för den virtuella WAN-anslutningen. Det kostar inget att länka anslutningarna.

* Varje länk anslutning består i sin tur av två IPsec-tunnlar som kan avslutas på två olika instanser av den virtuella WAN-gatewayen. Gatewayerna konfigureras som aktiva-aktiva gatewayer för återhämtning. Varje länk anslutning måste ha en unik IP-adress och en IP-adress för BGP-peering. I diagrammet kan tunnel 0 avslutas på instans 0 och tunnel 1 kan sluta på instans 1.

* Avdelnings enheter som tillhandahåller val av Sök vägar kan aktivera lämplig princip i gren hanterings lösningen för att styra trafik över flera länkar till Azure. ISP 1-länken kan till exempel användas för trafik med högre prioritet och länken ISP 2 kan användas som säkerhets kopia.

* Det är viktigt att Observera att VPN för virtuell hubb använder ECMP (samma kostnad som routning av flera vägar) över alla avslutande tunnlar.

## <a name="next-steps"></a>Nästa steg

Se [vanliga frågor och svar om Azure](virtual-wan-faq.md).