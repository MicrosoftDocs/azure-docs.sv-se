---
title: Virtuella WAN-och SD-WAN-anslutningsproblem
titleSuffix: Azure Virtual WAN
description: Lär dig mer om att ansluta ett privat SD-WAN med Azure Virtual WAN
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: ea9ddd05fe6fc745a3eefc29ab4f1d6aababc936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94564709"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>SD – WAN-anslutning med Azure Virtual WAN

Azure Virtual WAN är en nätverks tjänst som sammanställer många moln anslutningar och säkerhets tjänster med ett enda drift gränssnitt. De här tjänsterna omfattar gren (via plats-till-plats-VPN), fjärran vändare (punkt-till-plats-VPN), privat (ExpressRoute) anslutning, transitivt molnbaserad anslutning mellan moln för virtuella nätverk, VPN och ExpressRoute, routning, Azure-brandvägg och kryptering för privat anslutning.

Även om det virtuella Azure-nätverket är en programdefinierad WAN-anslutning (SD-WAN) är det också utformat för att möjliggöra sömlös samman koppling med de lokala säkerhets teknikerna och tjänsterna. Många sådana tjänster erbjuds av vårt [virtuella WAN](virtual-wan-locations-partners.md) -eko system och Azure Network Managed Services [-partner (MSP)](../networking/networking-partners-msp.md). Företag som omvandlar sina privata WAN-nätverk till SD-WAN har alternativ för att ansluta sina privata SD-WAN-nätverk med Azure Virtual WAN. Företag kan välja bland följande alternativ:

* Direkt Interconnect-modell
* Direkt Interconnect-modell med NVA-in-VWAN-Hub
* Indirekt Interconnect-modell
* Hanterad hybrid WAN-modell med hjälp av sin favorit [-Provider för](../networking/networking-partners-msp.md) hanterade tjänster

I alla dessa fall är det mellan koppling av virtuella WAN-nätverk med SD-WAN detsamma som anslutnings sidan, men kan variera beroende på samordnings-och drifts sidan.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Direkt Interconnect-modell

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="Direkt Interconnect-modell":::

I den här arkitektur modellen är SD-WAN-grenen kund lokal utrustning (CPE) direkt ansluten till virtuella WAN-hubbar via IPsec-anslutningar. Grenen CPE kan också anslutas till andra grenar via det privata SD-WAN-nätverket eller utnyttja virtuella WAN-anslutningar för gren anslutning. Grenar som behöver åtkomst till sina arbets belastningar i Azure kan direkt och säkert komma åt Azure via de IPsec-tunnlar som avslutas i de virtuella WAN-hubbarna.

SD-WAN CPE-partner kan aktivera automatisering för att automatisera de vanliga omständliga och fel känsliga IPsec-anslutningarna från deras respektive CPE-enheter. Automation gör att SD-WAN-styrenheten kan kommunicera med Azure via det virtuella WAN-API: et för att konfigurera virtuella WAN-platser, samt push-konfigurationer för IPsec-tunnel till gren CPEs. Se [rikt linjer för Automation](virtual-wan-configure-automation-providers.md) om du vill ha en beskrivning av automatisering av virtuella WAN-anslutningar från olika SD-WAN-partner.

SD-WAN-CPE fortsätter att vara den plats där trafik optimering och val av sökväg implementeras och tillämpas. 

I den här modellen kanske en del leverantörs trafik optimering baserat på trafik egenskaper i real tid inte stöds eftersom anslutningen till det virtuella WAN-nätverket är över IPsec och IPsec-VPN-gatewayen avslutas på den virtuella WAN-gatewayen. Till exempel är dynamisk Sök vägs val i grenen CPE möjligt på grund av att gren enheten utbyter olika information om nätverks paket med en annan SD-WAN-nod, vilket innebär att den bästa länken kan användas för olika prioriterade trafik dynamiskt på grenen. Den här funktionen kan vara användbar i områden där senaste mil optimering (gren till närmaste Microsoft-POP) krävs.

Med Virtual WAN kan användarna få val av Azure-sökvägar, vilket är principbaserad Sök vägs val över flera ISP-länkar från grenen CPE till virtuella WAN-gatewayer. Med det virtuella WAN-nätverket kan du konfigurera flera länkar (sökvägar) från samma SD-WAN-gren CPE. varje länk representerar en dubbel tunnel anslutning från en unik offentlig IP-adress för SD-WAN-CPE till två olika instanser av Azure Virtual WAN-gatewayen. SD-WAN-leverantörer kan implementera den mest optimala sökvägen till Azure, baserat på de trafik principer som anges av deras princip motor på CPE-länkarna. På Azure-slutpunkten behandlas alla anslutningar på samma sätt.

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>Direkt Interconnect-modell med NVA-in-VWAN-Hub

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="Direkt Interconnect-modell med NVA-in-VWAN-Hub":::

Den här arkitektur modellen stöder distribution av en [virtuell nätverks installation (NVA) från tredje part direkt till den virtuella hubben](./about-nva-hub.md). Detta gör det möjligt för kunder som vill ansluta sina förgreningar till samma varumärkes NVA i den virtuella hubben så att de kan dra nytta av patentskyddade funktioner från slut punkt till slut punkt för SD-WAN när de ansluter till Azure-arbetsbelastningar. 

Flera virtuella WAN-partner har arbetat för att ge en upplevelse som konfigurerar NVA automatiskt som en del av distributions processen. När NVA har etablerats i den virtuella hubben måste all ytterligare konfiguration som krävs för NVA göras via NVA partners portal eller hanterings program. Direkt åtkomst till NVA är inte tillgänglig. NVA som är tillgängliga för distribution direkt till Azures virtuella WAN-hubb är utformad för att användas i den virtuella hubben. För partner som stöder NVA i VWAN-hubben och deras distributions guider, se artikeln om [virtuella WAN-partner](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) .

SD-WAN-CPE fortsätter att vara den plats där trafik optimering och val av sökväg implementeras och tillämpas.
I den här modellen stöds leverantörsspecifik trafik optimering utifrån real tids trafik egenskaper eftersom anslutningen till det virtuella WAN-nätverket är via NVA SD-WAN i hubben.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Indirekt Interconnect-modell

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="Indirekt Interconnect-modell":::

I den här arkitektur modellen är SD-WAN-grenen CPEs indirekt anslutna till virtuella WAN-hubbar. När bilden visas distribueras ett SD-WAN Virtual CPE i ett Enterprise VNet. Den här virtuella CPE: n är i sin tur ansluten till den virtuella WAN-hubben med IPsec. Virtual CPE fungerar som en SD-WAN-gateway i Azure. Grenar som behöver åtkomst till sina arbets belastningar i Azure kan komma åt dem via gatewayen för v-CPE.

Eftersom anslutningen till Azure är via NVA (v-CPE Gateway) kan all trafik till och från Azure-arbetsbelastningen virtuella nätverk till andra SD-WAN-grenar gå via NVA. I den här modellen ansvarar användaren för att hantera och arbeta med SD-WAN-NVA, inklusive hög tillgänglighet, skalbarhet och routning.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Hanterad hybrid WAN-modell

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="Hanterad hybrid WAN-modell":::

I den här arkitektur modellen kan företag utnyttja en hanterad SD-WAN-tjänst som erbjuds av en provider för hanterad tjänst leverantör (MSP). Den här modellen liknar de direkta eller indirekta modeller som beskrivs ovan. Men i den här modellen levereras SD-WAN-designen, dirigeringen och åtgärderna till SD-WAN-providern.

[Azure Network MSP-partner](../networking/networking-partners-msp.md) kan använda [Azure-Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) för att implementera SD-WAN-tjänsten och Virtual WAN-tjänsten i företags kundens Azure-prenumeration, samt för att använda hybrid-till-slutpunkt-hybriden för kundens räkning. Dessa MSP kanske också kan implementera Azure-ExpressRoute i det virtuella WAN-nätverket och använda det som en hanterad tjänst från slut punkt till slut punkt.

## <a name="additional-information"></a>Ytterligare information

* [Inkludera vanliga frågor och svar](virtual-wan-faq.md)
* [Lösa fjärr anslutningar](work-remotely-support.md)