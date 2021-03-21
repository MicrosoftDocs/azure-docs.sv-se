---
title: Översikt över IPv6 för Azure Virtual Network
titlesuffix: Azure Virtual Network
description: IPv6-Beskrivning av IPv6-slutpunkter och data Sök vägar i ett virtuellt Azure-nätverk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: bb7769298940095900da25e61e13612b92bad00c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419107"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Vad är IPv6 för Azure Virtual Network?

Med IPv6 för Azure Virtual Network (VNet) kan du vara värd för program i Azure med IPv6-och IPv4-anslutning både i ett virtuellt nätverk och till och från Internet. På grund av belastningen på offentliga IPv4-adresser bygger nya nätverk för mobilitet och Sakernas Internet (IoT) ofta på IPv6. Även lång upprättade ISP och mobila nätverk omvandlas till IPv6. Endast IPv4-tjänster kan få en riktig nackdel på både befintliga och nya marknader. Med IPv4/IPv6-anslutning med dubbla stackar kan Azure-värdbaserade tjänster förflytta sig i den här tekniken med globalt tillgängliga, dubbla tjänster som enkelt kan ansluta till både befintliga IPv4 och dessa nya IPv6-enheter och-nätverk.

Azures ursprungliga IPv6-anslutning gör det enkelt att tillhandahålla en Internet anslutning med dubbla stackar (IPv4/IPv6) för program som finns i Azure. Det möjliggör enkel distribution av virtuella datorer med belastningsutjämnad IPv6-anslutning för både inkommande och utgående initierade anslutningar. Den här funktionen är fortfarande tillgänglig och mer information finns [här](../load-balancer/load-balancer-ipv6-overview.md).
IPv6 för Azure Virtual Network är mycket mer komplett – så att fullständiga IPv6-lösnings arkitekturer kan distribueras i Azure.


Följande diagram visar en enkel distribution med dubbla stackar (IPv4/IPv6) i Azure:

![Diagram över IPv6-nätverks distribution](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Fördelar

IPv6 för Azure VNET-förmåner:

- Hjälper till att utöka räckvidden för dina Azure-värdbaserade program till de växande mobila och Sakernas Internet marknaderna.
- Dubbla stackade IPv4/IPv6-datorer ger maximal flexibilitet för tjänst distribution. En enskild tjänst instans kan ansluta till både IPv4-och IPv6-kompatibla Internet klienter.
- Bygger på lång upprättade, stabila Azure VM-till-Internet IPv6-anslutning.
- Skydda som standard eftersom IPv6-anslutning till Internet endast upprättas när du uttryckligen begär det i distributionen.

## <a name="capabilities"></a>Funktioner

IPv6 för Azure VNet innehåller följande funktioner:

- Azure-kunder kan definiera sina egna adress utrymmen för det virtuella IPv6-nätverket för att möta behoven hos sina program, kunder eller sömlöst integrera dem i det lokala IP-utrymmet.
- Virtuella nätverk med dubbla stackar (IPv4 och IPv6) med dubbla stack-undernät gör det möjligt för program att ansluta till både IPv4-och IPv6-resurser i sitt virtuella nätverk eller Internet.
    > [!IMPORTANT]
    > Undernät för IPv6 måste vara exakt/64 i storlek.  Detta säkerställer framtida kompatibilitet om du väljer att aktivera routning av under nätet till ett lokalt nätverk eftersom vissa routrar bara kan acceptera/64 IPv6-vägar.  
- Skydda dina resurser med IPv6-regler för nätverks säkerhets grupper.
    - Och Azure Platforms DDoS-skydd (distributed denial of Service) har utökats till Internet-riktade offentliga IP-adresser
- Anpassa routningen av IPv6-trafik i ditt virtuella nätverk med User-Defined vägar – särskilt när du använder virtuella nätverks installationer för att utöka ditt program.
- Linux och Virtuella Windows-datorer kan alla använda IPv6 för Azure VNET
- [Standard IPv6 offentlig Load Balancer](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) stöd för att skapa elastiska, skalbara program, bland annat:
    - Valfri IPv6-hälso avsökning för att avgöra vilka instanser av backend-poolen som är hälso tillstånd och kan därmed ta emot nya flöden.
    - Valfria utgående regler som ger fullständig deklarativ kontroll över utgående anslutning för skalning och justering av den här funktionen för dina speciella behov.
    - Valfria flera klient konfigurations inställningar som gör att en enkel belastningsutjämnare kan använda flera offentliga IPv6-IP-adresser, samma klient dels protokoll och port kan återanvändas över klient dels adresser.
    - Valfria IPv6-portar kan återanvändas på Server dels instanser med den *flytande IP-* funktionen i belastnings Utjämnings regler 
    - Obs: belastnings utjämning utför ingen protokoll översättning (ingen NAT64). 
    - Obs! IPv6 kan endast läsas in till det primära nätverks gränssnittet (NIC) på virtuella Azure-datorer. 
- [Standard IPv6 internt Load Balancer](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) stöd för att skapa elastiska program på flera nivåer i Azure datavirtuella nätverks.   
- Grundläggande IPv6 offentlig Load Balancer stöd för kompatibilitet med äldre distributioner
- [Reserverade IPv6 offentliga IP-adresser och adress intervall](ipv6-public-ip-address-prefix.md) ger stabila, förutsägbara IPv6-adresser som fören klar filtreringen av dina Azure-värdbaserade program för ditt företag och dina kunder.
- Offentlig IP på instansnivå tillhandahåller IPv6-Internet anslutning direkt till enskilda virtuella datorer.
- [Lägg till IPv6 i befintliga endast IPv4-distributioner](ipv6-add-to-existing-vnet-powershell.md)– med den här funktionen kan du enkelt lägga till IPv6-anslutning till befintliga distributioner med endast IPv4 utan att behöva skapa distributioner på annat sätt.  IPv4-nätverks trafiken påverkas inte under den här processen, så beroende på ditt program och ditt operativ system kan du eventuellt lägga till IPv6 till och med Live-tjänster.    
- Låt Internet klienter sömlöst få åtkomst till ditt program med dubbla stackar med hjälp av det protokoll du väljer med Azure DNS stöd för IPv6-poster (AAAA). 
- Skapa dubbla stack-program som automatiskt skalar till din belastning med skalnings uppsättningar för virtuella datorer med IPv6.
- [Virtual Network (VNet) peering](virtual-network-peering-overview.md) – både inom-regionala och global peering – gör att du kan smidigt ansluta dubbla stack-virtuella nätverk – både IPv4-och IPv6-slutpunkterna på virtuella datorer i de peer-anslutna nätverken kan kommunicera med varandra. Du kan även distribuera dubbla stackar med endast IPv4-virtuella nätverk eftersom du översätter dina distributioner till dubbla stackar. 
- IPv6-felsökning och-diagnostik är tillgängligt med belastnings Utjämnings mått/aviseringar och Network Watcher funktioner som paket fångst, NSG Flow-loggar, anslutnings fel och anslutnings övervakning.   

## <a name="scope"></a>Omfång
IPv6 för Azure VNET är en grundläggande funktions uppsättning som gör det möjligt för kunder att vara värdar för dubbla stack-program (IPv4 + IPv6) i Azure.  Vi planerar att lägga till IPv6-stöd till fler funktioner i Azure-nätverk över tid och till och med att erbjuda dubbla stack-versioner av Azure PaaS Services, men under tiden kan alla Azure PaaS-tjänster nås via IPv4-slut punkter i dubbla stack-Virtual Machines.   

## <a name="limitations"></a>Begränsningar
Aktuell IPv6 för Azure Virtual Network-versionen har följande begränsningar:
- IPv6 för Azure Virtual Network är tillgängligt i alla globala Azure-regioner och amerikanska myndigheter med alla distributions metoder.  
- ExpressRoute-gatewayer kan endast användas för IPv4-trafik i ett virtuellt nätverk med IPv6 aktiverat.  Stöd för IPv6-trafik finns i vår översikt.   
- VPN-gatewayer kan inte användas i ett virtuellt nätverk med IPv6 aktiverat, antingen direkt eller peer-kopplas med "UseRemoteGateway".
- Azure-plattformen (AKS osv.) stöder inte IPv6-kommunikation för behållare. 
- Endast IPv6-Virtual Machines eller Virtual Machines skalnings uppsättningar stöds inte, varje nätverkskort måste innehålla minst en IPv4-IP-konfiguration. 
- När du lägger till IPv6 i befintliga IPv4-distributioner går det inte att lägga till IPv6-intervall i ett VNET med befintliga länkar för resurs navigering.  
- Vidarebefordring av DNS för IPv6 stöds för Azures offentliga DNS idag men omvänd DNS stöds inte ännu.   

## <a name="pricing"></a>Priser

IPv6 Azure-resurser och bandbredd debiteras enligt samma taxa som IPv4. Det finns inga ytterligare eller andra avgifter för IPv6. Du kan hitta information om priser för [offentliga IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/), [nätverks bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/)eller [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med hjälp av Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Lär dig hur du [distribuerar ett IPv6-program med dubbla stackar med Resource Manager-mallar (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
