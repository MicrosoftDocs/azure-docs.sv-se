---
title: Vad är nytt i Azure Load Balancer
description: Lär dig mer om vad som är nytt med Azure Load Balancer, till exempel senaste versions information, kända problem, fel korrigeringar, inaktuella funktioner och kommande ändringar.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: ffea6cdd1c8558a07559829b025cb5338cc59ee3
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586723"
---
# <a name="whats-new-in-azure-load-balancer"></a>Vad är nytt i Azure Load Balancer?

Azure Load Balancer uppdateras regelbundet. Håll dig uppdaterad med de senaste meddelandena. Den här artikeln innehåller information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar
- Föråldrade funktioner (om tillämpligt)

Du kan också hitta de senaste Azure Load Balancer uppdateringarna och prenumerera på RSS-flödet [här](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="recent-releases"></a>Nya versioner

| Typ |Name |Beskrivning  |Tillagt den  |
| ------ |---------|---------|---------|
| Funktion | Stöd för IP-baserad hantering av Server dels pooler (för hands version) | Azure Load Balancer har stöd för att lägga till och ta bort resurser från en backend-pool via en IPv4-eller IPv6-adress. Detta möjliggör enkel hantering av behållare, virtuella datorer och skalnings uppsättningar för virtuella datorer som är associerade med Load Balancer. Det innebär också att IP-adresser kan reserveras som en del av en server del innan de associerade resurserna skapas. Lär dig mer [här](backend-pool-management.md)|Juli 2020 |
| Funktion| Azure Load Balancer insikter med Azure Monitor | Kunder har byggts in som en del av Azure Monitor för nätverk och har nu Top ologiska kartor för alla sina Load Balancer konfigurationer och hälso instrument paneler för sina standard belastnings utjämningar som förkonfigureras med mått i Azure Portal. [Kom igång och Läs mer](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | Juni 2020 |
| Validering | Tillägg av verifiering för HA-portar | En verifiering har lagts till för att se till att det inte går att konfigurera HA port regler och regler för icke-hälsoportar när flytande IP är aktiverat. Tidigare skulle den här konfigurationen gå igenom, men fungerar inte som den ska. Ingen ändring av funktionen har gjorts. Du kan lära dig mer [här](load-balancer-ha-ports-overview.md#limitations)| Juni 2020 |
| Funktion| IPv6-stöd för Azure Load Balancer (allmänt tillgänglig) | Du kan ha IPv6-adresser som klient del för dina Azure Load Balancer. Lär dig hur du [skapar ett program med dubbla stackar här](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) |April 2020|
| Funktion| TCP återställer vid tids gräns för inaktivitet (allmänt tillgänglig)| Använd TCP-återställning för att skapa ett mer förutsägbart program beteende. [Läs mer](load-balancer-tcp-reset.md)| Februari 2020 |

## <a name="known-issues"></a>Kända problem

Produkt gruppen arbetar aktivt med lösningar för följande kända problem:

|Problem |Beskrivning  |Åtgärd  |
| ---------- |---------|---------|
| Log Analytics exportera | Log Analytics kan inte exportera mått för standard belastnings utjämning eller status loggar för hälso avsökning för Basic Load Balancer  | [Använd Azure Monitor för flerdimensionella mått för din standard Load Balancer](load-balancer-standard-diagnostics.md). Även om du inte kan använda Log Analytics för övervakning, Azure Monitor tillhandahålla visualisering för en omfattande uppsättning flerdimensionella mått. Du kan använda instrument panelen för förkonfigurerade mått genom att använda under bladet insikter på din Load Balancer. Om du använder grundläggande Load Balancer [uppgradering till standard](upgrade-basic-standard.md) för övervakning av produktions nivå statistik.

  

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Load Balancer finns i [Vad är Azure Load Balancer?](load-balancer-overview.md) och [vanliga frågor](load-balancer-faqs.md).
