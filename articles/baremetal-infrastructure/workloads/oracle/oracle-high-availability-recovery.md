---
title: Återställa din Oracle-databas i Azure BareMetal-infrastrukturen
description: Lär dig hur du återställer din Oracle-databas i Azure BareMetal-infrastrukturen.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590278"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Återställa din Oracle-databas i Azure BareMetal-infrastrukturen

Ingen enskild teknik skyddar mot alla felscenarier, men genom att kombinera funktioner får databasadministratörer möjligheten att återställa databasen i nästan vilken situation som helst.

## <a name="causes-of-database-failure"></a>Orsaker till databasfel

Databasfel kan inträffa av många orsaker, men de faller vanligtvis under flera kategorier:

- Datamanipuleringsfel.
- Förlust av onlinerekeloggar.
- Förlust av databaskontrollfiler.
- Förlust av databasdatafiler.
- Skadade fysiska data.

## <a name="choose-your-method-of-recovery"></a>Välj din återställningsmetod

Typen av återställning beror på typen av fel. Anta att ett objekt tas bort eller att data har ändrats felaktigt. Den snabbaste lösningen är vanligtvis att göra en flashback-databasåtgärd. I andra fall kan återställning via en Azure NetApp Files ögonblicksbild ge den återställning du vill ha. Följande bilds beslutsträd representerar vanliga fel- och återställningsscenarier om alla dataskyddsalternativ som beskrivs ovan implementeras.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Diagram över beslutsträdet för databasåterställning." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Tänk på att det här exempelbeslutsträdet endast visas från en databasadministratörs objektiv. Varje distribution kan ha olika krav som kan ändra ordningen på alternativen. Till exempel kan en databasrollsväxel till en annan region via Data Guard ha en negativ inverkan på programmets prestanda. Det kan ge återställningsmetoden för ögonblicksbilder ett lägre RTO. För att säkerställa att RTO/RPO-kraven uppfylls rekommenderar vi att du utför dessa åtgärder och skapar dokumenterade procedurer för att köra dem när det behövs.

## <a name="next-steps"></a>Nästa steg

Läs mer om BareMetal-infrastruktur:

- [Vad är BareMetal-infrastruktur i Azure?](../../concepts-baremetal-infrastructure-overview.md)
- [Ansluta BareMetal-infrastrukturinstanser i Azure](../../connect-baremetal-infrastructure.md)
