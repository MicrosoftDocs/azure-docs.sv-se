---
title: Azure Load Balancer flytande IP-konfiguration
description: Översikt över Azure Load Balancer flytande IP
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94699414"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer flytande IP-konfiguration

Load Balancer har flera funktioner för både UDP-och TCP-program.

## <a name="floating-ip"></a>Flytande IP

Vissa program scenarier föredrar eller kräver samma port som används av flera program instanser på en enskild virtuell dator i backend-poolen. Vanliga exempel på port åter användning är klustring för hög tillgänglighet, virtuella nätverks installationer och exponerar flera TLS-slutpunkter utan omkryptering. Om du vill återanvända backend-porten över flera regler måste du aktivera flytande IP i regel definitionen.

**Flytande IP** är Azures terminologi för en del av vad som kallas för direkt Server retur (DSR). DSR består av två delar:

- Flödes sto pol Ogin
- Ett schema för IP-adress mappning

På en plattforms nivå körs Azure Load Balancer alltid i en hög DSR-flödes topologi oavsett om flytande IP är aktiverat eller inte. Det innebär att den utgående delen av ett flöde alltid skrivs om korrekt för att flöda direkt tillbaka till ursprunget.
Utan flytande IP exponerar Azure ett traditionellt mappnings schema för IP-adresser för belastnings utjämning för enkel användning (VM-instansernas IP). Att aktivera flytande IP ändrar IP-adress mappningen till belastningsutjämnaren för klient delen för att möjliggöra ytterligare flexibilitet. Läs mer [här](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Hos

- Flytande IP stöds för närvarande inte på sekundära IP-konfigurationer för belastnings Utjämnings scenarier

## <a name="next-steps"></a>Nästa steg

- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att komma igång med att använda en Load Balancer.
- Läs mer om [Azure Load Balancer utgående anslutningar](load-balancer-outbound-connections.md).
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
- Läs mer om [hälso avsökningar](load-balancer-custom-probe-overview.md).
- Lär dig mer om [standard Load Balancer Diagnostics](load-balancer-standard-diagnostics.md).
- Läs mer om [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md).