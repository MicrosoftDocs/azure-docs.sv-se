---
title: Nätverks-och anslutnings barhet för Azure VMware-lösningar
description: Azure VMware-lösning nätverks-och anslutnings beskrivning.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462598"
---
<!-- Used in introduction.md and concepts-networking.md -->

Azure VMware-lösningen erbjuder en privat moln miljö som är tillgänglig från lokala och Azure-baserade resurser. Tjänster som Azure ExpressRoute, VPN-anslutningar eller Azure Virtual WAN levererar anslutningen. Dessa tjänster kräver vissa nätverks adress intervall och brand Väggs portar för att aktivera tjänsterna.

När du distribuerar ett privat moln, skapas privata nätverk för hantering, etablering och vMotion. Använd de här privata nätverken för att få åtkomst till vCenter och NSX-T Manager och vMotion eller distribution av virtuella datorer.  

ExpressRoute Global Reach används för att ansluta privata moln till lokala miljöer. Anslutningen kräver ett virtuellt nätverk med en ExpressRoute-krets till lokalt i din prenumeration.

Virtuella datorer som distribueras i det privata molnet är tillgängliga för Internet via de offentliga IP-funktionerna i Azure Virtual WAN.  Som standard är Internet åtkomst inaktive rad för nya privata moln. Mer information finns i [så här använder du de offentliga IP-funktionerna i Azure VMware-lösningen](../public-ip-usage.md).

