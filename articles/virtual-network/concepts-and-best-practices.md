---
title: Azure Virtual Network – koncept och bästa praxis
description: Lär dig mer om Azure Virtual Network-koncept och bästa praxis.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: 0a9945a58aa6ec49ad58f3a0a0d03ea75e30f6d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223626"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Azure Virtual Network-koncept och bästa praxis

I den här artikeln beskrivs viktiga begrepp och metod tips för Azure Virtual Network (VNet).

## <a name="vnet-concepts"></a>VNet-koncept

- **Adress utrymme:** När du skapar ett VNet måste du ange ett anpassat privat IP-adressutrymme med hjälp av offentliga och privata (RFC 1918)-adresser. Azure tilldelar resurser i ett virtuellt nätverk en privat IP-adress från det adressutrymme som du tilldelar. Om du till exempel distribuerar en virtuell dator i ett VNet med adress utrymme, 10.0.0.0/16, tilldelas den virtuella datorn en privat IP-adress som 10.0.0.4.
- **Undernät:** Med undernät kan du segmentera det virtuella nätverket i en eller flera under nätverk och allokera en del av det virtuella nätverkets adress utrymme till varje undernät. Du kan sedan distribuera Azure-resurser i ett särskilt undernät. Precis som i ett traditionellt nätverk kan du använda undernät för att segmentera ditt VNet-adressutrymme till segment som är lämpliga för organisationens interna nätverk. Detta förbättrar även effektiviteten för adress tilldelning. Du kan skydda resurser i undernät med hjälp av nätverks säkerhets grupper. Mer information finns i [nätverks säkerhets grupper](./network-security-groups-overview.md).
- **Regioner**: VNet är begränsat till en enda region/plats. flera virtuella nätverk från olika regioner kan dock anslutas tillsammans med Virtual Network-peering.
- **Prenumeration:** VNet är begränsat till en prenumeration. Du kan implementera flera virtuella nätverk i varje Azure-[prenumeration](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) och Azure-[region](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region).

## <a name="best-practices"></a>Bästa praxis

När du skapar nätverket i Azure är det viktigt att tänka på följande allmänna design principer:

- Se till att adress utrymmen inte överlappar varandra. Kontrol lera att ditt VNet-adressutrymme (CIDR-block) inte överlappar din organisations andra nätverks intervall.
- Dina undernät ska inte avse hela det virtuella adress utrymmet för det virtuella nätverket. Planera i förväg och reservera vissa adress utrymmen för framtiden.
- Vi rekommenderar att du har färre stora virtuella nätverk i stället för flera små virtuella nätverk. Detta förhindrar hanterings kostnader.
- Skydda ditt VNet genom att tilldela nätverks säkerhets grupper (NSG: er) till under näten under dem.

## <a name="next-steps"></a>Nästa steg

 Kom igång med ett virtuellt nätverk genom att skapa ett, distribuera några virtuella datorer till det och kommunicera mellan de virtuella datorerna. Mer information finns i snabbstarten [Skapa ett virtuellt nätverk](quick-create-portal.md).