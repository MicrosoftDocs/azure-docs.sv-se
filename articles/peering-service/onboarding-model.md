---
title: Onboarding-modell för Azure peering-tjänsten
description: Lär dig mer om hur du integrerar Azure peering-tjänsten
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84872080"
---
# <a name="onboarding-peering-service-model"></a>Integrering av peering-tjänstens modell

Onboarding-processen för peering-tjänsten består av två modeller enligt listan nedan:

 - Onboarding peering service-anslutning

 - Registrera peering-tjänstens anslutnings telemetri

Åtgärds planer för ovanstående modeller beskrivs nedan:

| **Steg** | **Åtgärd**| **Det här får du**|
|-----------|---------|---------|---------|
|1|Kunden att etablera anslutningen från en anslutnings partner (ingen interaktion med Microsoft). |En Internet leverantör som är väl ansluten till Microsoft och uppfyller de tekniska kraven för att utföra och tillförlitlig anslutning till Microsoft.  |
|2 (valfritt)|Kunden registrerar platser i Azure Portal. En plats definieras av: ISP/IXP-namn, fysisk plats för kundens webbplats (tillstånds nivå), IP-prefix som tilldelas platsen av tjänst leverantören eller företaget.  |Telemetri: övervakning av Internet väg, trafik prioritering från Microsoft till användarens närmaste högra POP-plats. |



## <a name="onboarding-peering-service-connection"></a>Onboarding peering service-anslutning

Om du vill ha en anslutning till peering-tjänsten gör du följande:

Arbeta med Internet leverantör (ISP) eller Internet Exchange (IX)-partner för att få peering-tjänsten att ansluta ditt nätverk till Microsoft-nätverket.

Se till att [anslutnings leverantörer](location-partners.md) är partner med Microsoft för peering-tjänsten. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Registrera peering-tjänstens anslutnings telemetri

Kunder kan välja sin telemetri, till exempel BGP Route Analytics, för att övervaka nätverks fördröjning och prestanda vid åtkomst till Microsoft-nätverket. Detta kan uppnås genom att du registrerar anslutningen i Azure Portal.

För att kunna integrera peering-tjänstens anslutnings telemetri måste kunden registrera peering-tjänstens anslutning till Azure Portal. Läs mer i avsnittet [Registrera peering-tjänsten – Azure Portal](azure-portal.md) .

Efter det kan du mäta telemetri genom att referera [hit](measure-connection-telemetry.md).

## <a name="next-steps"></a>Nästa steg

Information om steg för steg hur du registrerar peering service-anslutning finns i [Registrera peering-tjänsten – Azure Portal](azure-portal.md).

Om du vill veta mer om mått för telemetri av anslutningar, se [mått på telemetri för anslutning](measure-connection-telemetry.md).
