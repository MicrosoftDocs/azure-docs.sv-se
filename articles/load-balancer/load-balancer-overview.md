---
title: Vad är Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Översikt över funktioner, arkitektur och implementering av Azure Load Balancer-funktioner. Lär dig hur Load Balancer fungerar och hur du använder det i molnet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/25/2021
ms.author: allensu
ms.openlocfilehash: 51ceb72d53f78264edcadd2255e20c8fbdac2cae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181218"
---
# <a name="what-is-azure-load-balancer"></a>Vad är Azure Load Balancer?

*Belastnings utjämning* syftar på att fördela belastningen jämnt (inkommande nätverks trafik) över en grupp server dels resurser eller servrar. 

Azure Load Balancer arbetar på nivå 4 i OSI-modellen (Open Systems Interconnection). Det är den enda kontakt punkten för-klienter. Load Balancer distribuerar inkommande flöden som anländer till belastningsutjämnarens klient del till instanser av backend-poolen. Dessa flöden sker enligt konfigurerade regler för belastnings utjämning och hälso avsökningar. Instanserna för backend-poolen kan vara Azure-Virtual Machines eller instanser i en skalnings uppsättning för virtuella datorer.

En **[offentlig belastningsutjämnare](./components.md#frontend-ip-configurations)** kan tillhandahålla utgående anslutningar för virtuella datorer (VM) i det virtuella nätverket. Dessa anslutningar utförs genom att översätta sina privata IP-adresser till offentliga IP-adresser. Offentliga belastnings utjämning används för att belastningsutjämna Internet trafik till dina virtuella datorer.

En **[intern (eller privat) belastningsutjämnare](./components.md#frontend-ip-configurations)** används där privata IP-adresser bara behövs på klient delen. Interna belastnings utjämning används för att belastningsutjämna trafik i ett virtuellt nätverk. En klient del för belastningsutjämnare kan nås från ett lokalt nätverk i ett hybrid scenario.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Bild: balansera program på flera nivåer med både offentliga och interna Load Balancer*

Mer information om de enskilda komponenterna för belastnings utjämning finns i [Azure Load Balancer-komponenter](./components.md).

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. 
> * Om du vill göra DNS-baserad global Routning och **inte** har kraven för Transport Layer Security (TLS) protokoll terminering ("SSL-avlastning"), per http/https-begäran eller bearbetning av program lager, granska [Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
> * Om du vill belastningsutjämna mellan servrarna i en region i program lagret granskar du [Application Gateway](../application-gateway/overview.md).
> * Om du behöver optimera en global routning av din webb trafik och optimera prestanda och tillförlitlighet på toppnivå med hjälp av snabb global redundans, se [front dörren](../frontdoor/front-door-overview.md).
> 
> Dina scenarier från slut punkt till slut punkt kan dra nytta av att kombinera de här lösningarna efter behov.
> En alternativ jämförelse för Azure-belastnings utjämning finns i [Översikt över belastnings Utjämnings alternativ i Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).


## <a name="why-use-azure-load-balancer"></a>Varför ska jag använda Azure Load Balancer?
Med Azure Load Balancer kan du skala dina program och skapa tjänster med hög tillgänglighet. Belastnings utjämning stöder både inkommande och utgående scenarier. Load Balancer ger låg latens och högt data flöde, och skalar upp till miljon tals flöden för alla TCP-och UDP-program.

Viktiga scenarier som du kan utföra med Azure Standard Load Balancer är:

- Belastningsutjämna **[intern](./quickstart-load-balancer-standard-internal-portal.md)** och **[extern](./quickstart-load-balancer-standard-public-portal.md)** trafik till virtuella Azure-datorer.

- Öka tillgängligheten genom att distribuera resurser **[inom](./tutorial-load-balancer-standard-public-zonal-portal.md)** och **[mellan](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** zoner.

- Konfigurera **[utgående anslutning](./load-balancer-outbound-connections.md)** för virtuella Azure-datorer.

- Använd **[hälso avsökningar](./load-balancer-custom-probe-overview.md)** för att övervaka belastningsutjämnade resurser.

- Använd **[port vidarebefordring](./tutorial-load-balancer-port-forwarding-portal.md)** för att få åtkomst till virtuella datorer i ett virtuellt nätverk via offentlig IP-adress och port.

- Aktivera stöd för **[belastnings utjämning](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** av **[IPv6](../virtual-network/ipv6-overview.md)**.

- Standard Load Balancer tillhandahåller flerdimensionella mått via [Azure Monitor](../azure-monitor/overview.md).  Dessa mått kan filtreras, grupperas och delas upp för en specifik dimension.  De tillhandahåller aktuella och historiska insikter om prestanda och hälsa för din tjänst. [Insikter för Azure Load Balancer](./load-balancer-insights.md) erbjuder en förkonfigurerad instrument panel med användbara visualiseringar för dessa mått.  Resource Health stöds också. Granska **[standarddiagnostiken för belastningsutjämnare](load-balancer-standard-diagnostics.md)** för mer information.

- Belastnings Utjämnings tjänster på **[flera portar, flera IP-adresser eller både](./load-balancer-multivip-overview.md)** och.

- Flytta **[interna](./move-across-regions-internal-load-balancer-portal.md)** och **[externa](./move-across-regions-external-load-balancer-portal.md)** belastnings Utjämnings resurser i Azure-regioner.

- Belastningsutjämna TCP-och UDP-flöde på alla portar samtidigt med **[ha-portar](./load-balancer-ha-ports-overview.md)**.

### <a name="secure-by-default"></a><a name="securebydefault"></a>Säker som standard

* Standard Load Balancer bygger på den nolla förtroende nätverks säkerhets modellen.

* Standard Load Balancer är säker som standard och ingår i ditt virtuella nätverk. Det virtuella nätverket är ett privat och isolerat nätverk.  

* Standard belastnings utjämning och offentliga standard-IP-adresser stängs till inkommande anslutningar om de inte öppnas av nätverks säkerhets grupper. NSG: er används för att uttryckligen tillåta tillåten trafik.  Om du inte har en NSG på ett undernät eller ett nätverkskort för din virtuella dator resurs, tillåts inte trafik att komma åt den här resursen. Mer information om NSG: er och hur du tillämpar dem i ditt scenario finns i [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).

* Den grundläggande belastningsutjämnaren är öppen för Internet som standard. 

* Load Balancer lagrar inte kund information.

## <a name="pricing-and-sla"></a>Priser och service nivå avtal

Information om priser för standard belastningsutjämnare finns i [priser för belastnings utjämning](https://azure.microsoft.com/pricing/details/load-balancer/).
Basic Load Balancer erbjuds utan kostnad.
Se [SLA för Load Balancer](https://aka.ms/lbsla). Basic Load Balancer har inget service avtal.

## <a name="whats-new"></a>Nyheter

Prenumerera på RSS-flödet och Visa de senaste Azure Load Balancer funktions uppdateringarna på sidan [Azure updates](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer) .

## <a name="next-steps"></a>Nästa steg

Se [skapa en offentlig standard belastningsutjämnare](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en belastningsutjämnare.

Mer information om Azure Load Balancer begränsningar och komponenter finns i [Azure Load Balancer komponenter](./components.md) och [Azure Load Balancer begrepp](./concepts.md)