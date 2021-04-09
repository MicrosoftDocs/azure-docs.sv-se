---
title: Offentligt IPv6-adressprefix i Azure Virtual Network
titlesuffix: Azure Virtual Network
description: Lär dig mer om offentliga IPv6-adressprefix i Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 3e3d3c01ba1190871da39ce27de6a95952d09be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538214"
---
# <a name="reserved-public-ipv6-address-prefix"></a>Reserverat offentligt IPv6-adressprefix

I Azure är dubbla stack (IPv4 + IPv6) virtuella nätverk (VNet) och virtuella datorer (VM) säkra som standard eftersom de inte har någon Internet anslutning. Du kan enkelt lägga till en IPv6-Internet anslutning till dina Azure-belastningsutjämnare och virtuella datorer med offentliga IPv6-adresser som du får från Azure.

Alla offentliga IP-adresser som du reserverar är kopplade till en valfri Azure-region och med din Azure-prenumeration. Du kan flytta en reserverad (statisk) offentlig IPv6-IP-adress mellan alla Azure-belastningsutjämnare eller virtuella datorer i din prenumeration. Du kan koppla bort IPv6-offentliga IP-adresser helt och hållet för din användning när du är klar.

> [!WARNING]
> Var försiktig med att inte ta bort dina offentliga IP-adresser av misstag. Om du tar bort en offentlig IP-adress tas den bort från din prenumeration och du kan inte återställa den (inte ens i hjälpen för Azure-supporten).

Förutom att reservera enskilda IPv6-adresser kan du reservera sammanhängande intervall av Azure IPv6-adresser (kallas även IP-prefix) för din användning.  Precis som enskilda IP-adresser är reserverade prefix kopplade till en valfri Azure-region och med din Azure-prenumeration. Att reservera ett förutsägbart, sammanhängande intervall med adresser har många användnings områden. Du kan till exempel förenkla IP- *filtreringen* av dina Azure-värdbaserade program av ditt företag och dina kunder som statiska IP-intervall kan programmeras i lokala brand väggar.  Du kan skapa enskilda offentliga IP-adresser från ditt IP-prefix vid behov och när du tar bort de enskilda offentliga IP-adresser som de *returneras* till det reserverade intervallet så att du kan återanvända dem senare. Alla IP-adresser i IP-prefixet är reserverade för exklusiv användning tills du tar bort ditt prefix.



## <a name="ipv6-prefix-sizes"></a>Mått för IPv6-prefix
Följande storlekar för offentliga IP-prefix är tillgängliga:

-  Minsta storlek för IPv6-prefix:/127 = 2 adresser
-  Maximal storlek för IPv6-prefix:/124 = 16 adresser

Prefixlängden anges som en CIDR-mask storlek (Classless Inter-Domain routing). En mask för/128 representerar till exempel en individuell IPv6-adress som IPv6-adresser består av 128 bitar.

## <a name="pricing"></a>Priser
 
För kostnader som är kopplade till användning av offentliga Azure-IP-adresser, både enskilda IP-adresser och IP-intervall, se [priser för offentliga IP](https://azure.microsoft.com/pricing/details/ip-addresses/)-adresser

## <a name="limitations"></a>Begränsningar
IPv6 stöds endast på offentliga offentliga IP-adresser med "dynamisk" tilldelning, vilket innebär att IPv6-adressen ändras om du tar bort och distribuerar om ditt program (VM eller belastningsutjämnare) i Azure. Standardvärdet för offentliga IPv6-IP-adresser är endast statisk (reserverad) allokering, och interna standardbelastningsutjämnare kan också stödja dynamisk allokering från det undernät som de är tilldelade till.  

Som bästa praxis rekommenderar vi att du använder standard offentliga IP-adresser och standardbelastningsutjämnare för dina IPv6-program.

## <a name="next-steps"></a>Nästa steg
- Reservera ett offentligt [IPv6](ipv6-reserve-public-ip-address-prefix.md)-adressprefix.
- Läs mer om [IPv6-adresser](ipv6-overview.md).
- Lär dig mer om [hur du skapar och använder offentliga IP-adresser](virtual-network-public-ip-address.md) (både IPv4 och IPv6) i Azure.
