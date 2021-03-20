---
title: Azure nätverks arkitektur
description: Den här artikeln innehåller en allmän beskrivning av det Microsoft Azure infrastruktur nätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89567883"
---
# <a name="azure-network-architecture"></a>Azure nätverks arkitektur
Azures nätverks arkitektur ger anslutning från Internet till Azure-datacentren. Alla arbets belastningar som distribueras (IaaS, PaaS och SaaS) på Azure utnyttjar Azure datacenter-nätverket.

## <a name="network-topology"></a>Nätverkstopologi
Nätverks arkitekturen i ett Azure-datacenter består av följande komponenter:

- Edge-nätverk
- WAN (Wide Area Network)
- Regionala gateways-nätverk
- Data Center nätverk

![Diagram över Azure-nätverk](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Nätverkskomponenter
En kort beskrivning av nätverks komponenterna.

- Edge-nätverk

   - Avgränsnings punkt mellan Microsoft-nätverk och andra nätverk (till exempel Internet, företags nätverk)
   - Tillhandahåller Internet-och [ExpressRoute](../../expressroute/expressroute-introduction.md) -peering i Azure

- WAN (Wide Area Network)

   - Microsoft Intelligent stamnät nätverk som täcker hela världen
   - Tillhandahåller anslutning mellan [Azure-regioner](https://azure.microsoft.com/global-infrastructure/geographies/)

- Regional Gateway

   - Agg regerings punkt för alla data Center i en Azure-region
   - Ger enorma anslutningar mellan data Center inom en Azure-region (till exempel flera hundra terabits per Data Center)

- Data Center nätverk

   - Tillhandahåller anslutning mellan servrar i data centret med låg överprenumererad bandbredd

Ovanstående nätverks komponenter har utformats för att ge maximal tillgänglighet för att stödja ständigt tillgänglig moln verksamhet. Redundansen är utformad och inbyggd i nätverket från den fysiska aspekten till kontroll protokollet.

## <a name="datacenter-network-resiliency"></a>Nätverks återhämtning för data Center
Nu ska vi illustrera den återhämtnings design princip som använder data Center nätverk.

Data Center nätverket är en modifierad version av ett [Clos-nätverk](https://en.wikipedia.org/wiki/Clos_network), vilket ger hög bi-section bandbredd för moln skalnings trafik. Nätverket konstrueras med ett stort antal olika enheter för att minska påverkan som orsakas av enskilda maskin varu problem. Dessa enheter befinner sig strategiskt på olika fysiska platser med separat energi-och kylnings domän för att minska påverkan av en miljö händelse.  På kontroll planet körs alla nätverks enheter som OSI-modell skikt 3, vilket eliminerar det historiska problemet med trafik slingan. Alla sökvägar mellan olika nivåer är aktiva för att ge hög redundans och bandbredd med hjälp av Equal-Cost multi-path (ECMP)-routning.

Följande diagram visar att data Center nätverket är konstruerat på olika nivåer av nätverks enheter. Staplarna i diagrammet motsvarar grupper av nätverks enheter som tillhandahåller redundans och anslutning med hög bandbredd.

![Data Center nätverk](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokal och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure information system](infrastructure-components.md)
- [Azures produktions nätverk](production-network.md)
- [Azure SQL Database säkerhetsfunktioner](infrastructure-sql.md)
- [Åtgärder och hantering av Azure-produktion](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Integritet för Azure-infrastruktur](infrastructure-integrity.md)
- [Data skydd för Azure-kunder](protection-customer-data.md)
