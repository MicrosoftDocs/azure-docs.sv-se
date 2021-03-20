---
title: Nätverks överväganden – Azure Dedicated HSM | Microsoft Docs
description: Översikt över nätverks överväganden som gäller för Azure dedikerade HSM-distributioner
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 3764b261b491c660da16d7989be20742fead1fbf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91359162"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure Dedicated HSM-nätverk

Azure Dedicated HSM kräver en mycket säker nätverks miljö. Detta är sant om det är från Azure-molnet tillbaka till kundens IT-miljö (lokalt), med hjälp av distribuerade program eller för scenarier med hög tillgänglighet. Azure-nätverk ger detta och det finns fyra distinkta områden som måste åtgärdas.

- Skapa HSM-enheter i din Virtual Network (VNet) i Azure
- Ansluta lokalt till molnbaserade resurser för konfiguration och hantering av HSM-enheter
- Skapa och ansluta virtuella nätverk för program resurser mellan anslutning och HSM-enheter
- Ansluta virtuella nätverk mellan regioner för kommunikation mellan olika regioner och även för att aktivera scenarier med hög tillgänglighet

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuellt nätverk för din dedikerade HSM: er

Dedikerade HSM: er är integrerade i en Virtual Network och placeras i kundernas egna privata nätverk i Azure. Detta ger åtkomst till enheterna från virtuella datorer eller beräknings resurser i det virtuella nätverket.  
Mer information om hur du integrerar Azure-tjänster i det virtuella nätverket och de funktioner som den tillhandahåller finns i dokumentationen [för virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md) .

### <a name="virtual-networks"></a>Virtuella nätverk

Innan du konfigurerar en dedikerad HSM-enhet måste kunderna först skapa en Virtual Network i Azure eller använda en som redan finns i kund prenumerationen. Det virtuella nätverket definierar säkerhets omkretsen för den dedikerade HSM-enheten. Mer information om hur du skapar virtuella nätverk finns i dokumentationen för det [virtuella nätverket](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Undernät

Undernät segmenterar det virtuella nätverket i separata adress utrymmen som kan användas av de Azure-resurser som du placerar i dem. Dedikerade HSM: er distribueras till ett undernät i det virtuella nätverket. Varje dedikerad HSM-enhet som distribueras i kundens undernät kommer att få en privat IP-adress från det här under nätet. Under nätet där HSM-enheten distribueras måste vara uttryckligen delegerad till tjänsten: Microsoft. HardwareSecurityModules/dedicatedHSMs. Detta ger vissa behörigheter till HSM-tjänsten för distribution till under nätet. Delegering till dedikerade HSM: er tillämpar vissa princip begränsningar på under nätet. Nätverks säkerhets grupper (NSG: er) och User-Defined vägar (UDR) stöds för närvarande inte i delegerade undernät. När ett undernät delegeras till dedikerade HSM: er, kan det därför endast användas för att distribuera HSM-resurser. Det går inte att distribuera andra kund resurser till under nätet.


### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

Ett krav för den aktuella arkitekturen är konfiguration av en återställnings-gateway i under nätet för kunder där en HSM-enhet måste placeras för att möjliggöra integrering av HSM-enheten i Azure. Den här ER-gatewayen kan inte användas för att ansluta lokala platser till kundernas HSM-enheter i Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ansluta ditt lokala nätverk till Azure

När du skapar molnbaserade resurser är det ett typiskt krav för en privat anslutning tillbaka till lokala IT-resurser. I händelse av dedikerad HSM är detta huvudsakligen för HSM-klientprogramvaran att konfigurera HSM-enheter och även för aktiviteter som säkerhets kopiering och hämtning av loggar från HSM: er för analys. En viktig besluts punkt här är den typ av anslutning som finns i alternativ.  Det mest flexibla alternativet är plats-till-plats-VPN eftersom det kan finnas flera lokala resurser som kräver säker kommunikation med resurser (inklusive HSM: er) i Azure-molnet. Detta kräver att en kund organisation har en VPN-enhet för att under lätta anslutningen. En punkt-till-plats-VPN-anslutning kan användas om det bara finns en enda slut punkt lokalt, till exempel en enda administrations arbets Station.
Mer information om anslutnings alternativ finns i [VPN gateway planerings alternativ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> För närvarande är ExpressRoute inte ett alternativ för anslutning till lokala resurser. Det bör även noteras att ExpressRoute-gatewayen som används enligt beskrivningen ovan inte är för anslutningar till en lokal infrastruktur.

### <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Ett virtuellt privat nätverk från punkt till plats är den enklaste formen av säker anslutning till en enda slut punkt lokalt. Detta kan vara relevant om du endast planerar att ha en enda administrations arbets station för Azure-baserade dedikerade HSM: er.

### <a name="site-to-site-vpn"></a>Plats-till-plats-VPN

Ett plats-till-plats-virtuellt privat nätverk möjliggör säker kommunikation mellan Azure-baserade dedikerade HSM: er och din lokala IT. En anledning till att göra detta är att ha en säkerhets kopierings funktion för HSM: s lokala och behöver en anslutning mellan de två för att köra säkerhets kopieringen.

## <a name="connecting-virtual-networks"></a>Ansluta virtuella nätverk

En typisk distributions arkitektur för dedikerad HSM kommer att starta med ett enda virtuellt nätverk och motsvarande undernät där HSM-enheterna skapas och tillhandahålls. Inom samma region kan det vara bra att ha ytterligare virtuella nätverk och undernät för program komponenter som använder dedikerad HSM. För att aktivera kommunikation mellan dessa nätverk använder vi Virtual Network-peering.

### <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Om det finns flera virtuella nätverk i en region som behöver åtkomst till var and ras resurser, kan Virtual Network peering användas för att skapa säkra kommunikations kanaler mellan dem.  Peering av virtuella nätverk ger inte bara säker kommunikation utan garanterar också en låg latens och anslutningar med hög bandbredd mellan resurserna i Azure.

![nätverks-peering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ansluta i Azure-regioner

HSM-enheterna har möjlighet att via program bibliotek omdirigera trafik till en alternativ HSM. Fördirigering av trafik är användbart om enheter inte fungerar eller om åtkomst till en enhet går förlorad. Scenarier på regional nivå kan undvikas genom att distribuera HSM: er i andra regioner och aktivera kommunikation mellan virtuella nätverk över flera regioner.

### <a name="cross-region-ha-using-vpn-gateway"></a>Mellan region HA med VPN-gateway

För globalt distribuerade program eller för regionala failover-scenarier med hög tillgänglighet måste du ansluta virtuella nätverk över flera regioner. Med Azure Dedicated HSM kan hög tillgänglighet uppnås med hjälp av en VPN Gateway som tillhandahåller en säker tunnel mellan de två virtuella nätverken. Mer information om VNet-till-VNet-anslutningar med hjälp av VPN Gateway finns i artikeln med rubriken [Vad är VPN gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Global VNet-peering är inte tillgänglig i scenarier mellan olika regioner med dedikerade HSM: er just nu och VPN-gatewayen bör användas i stället. 

![Diagrammet visar två regioner anslutna med två V P N-gatewayer. Varje region innehåller peer-kopplat virtuella nätverk.](media/networking/global-vnet.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar](faq.md)
- [Support](supportability.md)
- [Hög tillgänglighet](high-availability.md)
- [Fysisk säkerhet](physical-security.md)
- [Övervakning](monitoring.md)
- [Distributions arkitektur](deployment-architecture.md)