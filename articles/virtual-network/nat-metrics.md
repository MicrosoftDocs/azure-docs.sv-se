---
title: Mått och aviseringar för Azure Virtual Network NAT
titleSuffix: Azure Virtual Network
description: Förstå Azure Monitor mått och aviseringar som är tillgängliga för Virtual Network NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: b22d7823ac961de53914325d7ebf2b5d53b7c5af
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055832"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT-mått

Azure Virtual Network NAT-gateway-resurser innehåller flerdimensionella mått. Du kan använda dessa mått för att se hur det fungerar och vid [fel sökning](troubleshoot-nat.md).  Aviseringar kan konfigureras för kritiska problem som SNAT-belastningar.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network NAT för utgående till Internet">
</p>

*Bild: Virtual Network NAT för utgående till Internet*

## <a name="metrics"></a>Mått

NAT-gateway-resurser ger följande flerdimensionella mått i Azure Monitor:

| Metric | Beskrivning | Rekommenderad agg regering | Dimensioner |
|---|---|---|---|
| Byte | Antal byte som bearbetats inkommande och utgående | Sum | Riktning (i; Ut), protokoll (6 TCP; 17 UDP) |
| Skickas | Paket som har bearbetats inkommande och utgående | Sum | Riktning (i; Ut), protokoll (6 TCP; 17 UDP) |
| Ignorerade paket | Paket som släppts av NAT-gatewayen | Sum | / |
| Antal SNAT-anslutningar | Tillstånds över gångar per intervall | Sum | Anslutnings tillstånd, protokoll (6 TCP; 17 UDP) |
| Totalt antal SNAT-anslutningar | Aktuella aktiva SNAT-anslutningar (~ SNAT-portar som används) | Sum | Protokoll (6 TCP; 17-tums UDP) |


## <a name="alerts"></a>Aviseringar

Aviseringar för mått kan konfigureras i Azure Monitor för vart och ett av föregående [mått](#metrics).

## <a name="limitations"></a>Begränsningar

Resource Health stöds inte.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Virtual Network NAT](nat-overview.md)
* Lär dig om [NAT gateway-resurs](nat-gateway-resource.md)
* Läs mer om [Azure Monitor](../azure-monitor/overview.md)
* Lär dig mer om att [Felsöka resurser för NAT-gateway](troubleshoot-nat.md).
* [Berätta för oss vad du ska bygga härnäst för Virtual Network NAT i UserVoice](https://aka.ms/natuservoice).


