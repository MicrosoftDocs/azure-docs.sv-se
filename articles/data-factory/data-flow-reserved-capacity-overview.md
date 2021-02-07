---
title: Spara beräknings kostnader med reserverad kapacitet
description: Lär dig hur du köper Azure Data Factory reserverad Data Flow-kapacitet för att spara pengar på dina beräknings kostnader.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: 86ebbb46e8f3b5bb34dfe5789a17a2d63526f65c
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808434"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Spara kostnader för resurser med reserverad kapacitet – Azure Data Factory data flöden

Spara pengar med Azure Data Factory data flödes kostnader genom att gå till en reservation för beräknings resurser jämfört med priser enligt principen betala per användning. Med reserverad kapacitet gör du ett åtagande för användning av ADF-dataflödet under en period av en eller tre år för att få en betydande rabatt på beräknings kostnaderna. Om du vill köpa reserverad kapacitet måste du ange Azure-region, Compute-typ, antal kärnor och villkor.

Du behöver inte tilldela reservationen till en viss fabrik eller integrerings körning. Befintliga fabriker eller nyligen distribuerade fabriker erhåller automatiskt förmånen. Genom att köpa en reservation ska du allokera till användning för beräknings kostnaderna för data flödet under en period på en eller tre år. Så snart du köper en reservation debiteras inte längre de beräknings kostnader som matchar reservations-attributen enligt priserna för betala per användning. 

Du kan köpa [reserverad kapacitet](https://portal.azure.com) genom att välja reservationer [upp eller månads vis betalningar](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md). Så här köper du reserverad kapacitet:

- Du måste ha ägar rollen för minst ett företag eller en enskild prenumeration med priser enligt principen betala per användning.
- För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com). Eller, om inställningen är inaktive rad, måste du vara en EA-administratör i prenumerationen. Reserverad kapacitet.

Mer information om hur företags kunder och kunder som betalar per användning debiteras för reservations köp finns i [förstå Azure reservation-användning för företagets registrering](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) och [förstå Azure reservation-användning för din prenumeration enligt principen betala per](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage)användning.

> [!NOTE]
> Köp reserverad kapacitet tar inte förallokerar eller reserverar vissa infrastruktur resurser (virtuella datorer eller kluster) för användning.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Fastställ korrekt Azure IR storlek som krävs före köpet

Reservationens storlek bör baseras på den totala mängd data som används av befintliga eller närmast distribuerade data flöden med samma beräknings nivå.

Anta till exempel att du kör en pipeline varje timme med ett minne som är optimerat med 32 kärnor. Vi vill också att du planerar att distribuera i nästa månad ytterligare en pipeline som använder generell användning 64 kärnor. Vi antar också att du vet att du behöver dessa resurser i minst 1 år. I det här fallet anger du antalet kärnor som behövs för varje beräknings typ i 1 timme. I Azure-portalen söker du efter reservationer. Välj Data Factory > data flöden och ange sedan 32 för minnesoptimerade och 64 för generell användning.

## <a name="buy-reserved-capacity"></a>Köp reserverad kapacitet

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla tjänster** > **Reservationer**.
3. Välj **Lägg till** och sedan i fönstret **köp reservationer** väljer du **ADF-dataflöden** för att köpa en ny reservation för ADF-dataflöden.
4. Fyll i de obligatoriska fälten och attributen som du väljer kvalificera för att hämta rabatten för reserverad kapacitet. Det faktiska antalet data flöden som erhåller rabatten beror på vald omfattning och kvantitet.
5. Granska kostnaden för kapacitets reservationen i avsnittet **kostnader** .
6. Välj **Köp**.
7. Välj **Visa den här reservationen** för att se inköpets status.

## <a name="cancel-exchange-or-refund-reservations"></a>Avbryta, byta ut eller återbetala reservationer

Du kan avbryta, byta ut och återbetala reservationer med vissa begränsningar. Läs mer i [Byten och återbetalning för Azure-reservationer via självbetjäning](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Förstå reservationsrabatter i Azure](data-flow-understand-reservation-charges.md)
