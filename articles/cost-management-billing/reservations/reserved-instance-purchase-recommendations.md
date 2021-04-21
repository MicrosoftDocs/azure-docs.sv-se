---
title: Reservationsrekommendationer för Azure
description: Läs om reservationsrekommendationer för Azure.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: d70580a34e832d6465571adbc8f0524abeba609a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767933"
---
# <a name="reservation-recommendations"></a>Reservationsrekommendationer

Inköpsrekommendationer för reserverade Azure-instanser (RI) tillhandahålls av [API:et för reservationsrekommendationer](/rest/api/consumption/reservationrecommendations) för Azure Consumption, [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) och när du köper reservationer i Microsoft Azure-portalen.

Följande steg definierar hur rekommendationer beräknas:

1. Rekommendationsmotorn utvärderar hur mycket dina resurser används per timme inom det angivna omfånget som kan utgöras av de senaste 7, 30 eller 60 dagarna.
2. Motorn simulerar dina kostnader med och utan reservationer enligt dessa förbrukningsdata.
3. Kostnaderna simuleras enligt olika kvantiteter och den kvantitet som maximerar dina besparingar rekommenderas.
4. Om dina resurser stängs av regelbundet kommer simuleringen inte att hitta några besparingsmöjligheter och inte ge dig någon inköpsrekommendation.
5. Rekommendationsberäkningarna innehåller eventuella särskilda rabatter som du kan ha på dina priser för användning på begäran.

## <a name="recommendations-in-the-azure-portal"></a>Rekommendationer på Azure-portalen

Rekommendationer för reservationsköp visas också i Azure-portalen när du köper reservationer. Rekommendationer visas med **rekommenderat antal**. Du får maximala besparingar om du köper den kvantitet som Azure rekommenderar. Du kan köpa vilken kvantitet du vill, men om du köper en annan kvantitet är dina besparingar inte optimala.

Låt oss titta på några exempel.

På bilden i följande exempel rekommenderar Azure en inköpskvantitet på 6 för den reservationen som valts.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" alt-text="Exempel som visar rekommendation för reservationsköp" lightbox="./media/reserved-instance-purchase-recommendations/recommended-quantity.png" :::

Mer information om rekommendationen visas när du väljer **Visa information.** Följande bild visar mer information om rekommendationen. Den rekommenderade kvantiteten beräknas för högsta möjliga användning och baseras på din historiska användning. Din rekommendation kanske inte gäller 100 procent användning om du har inkonsekvent användning. I det här exemplet ser du att användningen varierar över tid. Kostnaden för reservationen, möjliga besparingar och användningsprocenten visas.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details.png" alt-text="Exempel som visar information om en rekommendation för reservationsköp " :::

Diagrammet och de uppskattade värdena ändras när du ökar den rekommenderade kvantiteten. Genom att öka reservationskvantiteten minskar dina besparingar eftersom du får minskad reservationsanvändning. Du kommer med andra ord att betala för reservationer som inte utnyttjar fullt ut.

Om du sänker reservationskvantiteten minskas även dina besparingar. Även om du får större utnyttjande kommer det förmodligen att finnas perioder då dina reservationer inte täcker din användning. För den användning som överstiger reservationskvantiteten används dyrare resurser som betalas per användning. Följande exempel illustrerar detta. Vi har minskat reservationskvantiteten manuellt till 4. Reservationsanvändningen ökar, men de totala besparingarna minskar eftersom du betalar för användningsbaserade kostnader.

:::image type="content" source="./media/reserved-instance-purchase-recommendations/recommended-quantity-details-changed.png" alt-text="Exempel som visar ändrad rekommendation för reservationsköp" :::

Om du vill maximera besparingarna med reservationer försöker du köpa reservationer så nära rekommendationen som möjligt.

## <a name="recommendations-in-azure-advisor"></a>Rekommendationer i Azure Advisor

Rekommendationer för reservationsköp finns i Azure Advisor. Tänk på följande punkter:

- Advisor tillhandahåller endast rekommendationer som omfattas av en prenumeration. Om du vill se rekommendationer för hela faktureringsomfånget (faktureringskonto eller faktureringsprofil) gör du följande:
  -  I listan Azure Portal du till **Reservations**  >  Add (Reservations **add)** och väljer sedan den typ som du vill se rekommendationerna för.
- Rekommendationerna i Advisor visar din senaste 30-dagars användningstrend.
- Rekommendationer för kvantitet och besparingar gäller för en treårig reservation, där det är tillgängligt. Om en treårig reservation inte säljs för tjänsten beräknas rekommendationen med ett års reservationspris.
- Rekommendationsberäkningarna innehåller eventuella särskilda rabatter som du kan ha på dina priser för användning på begäran.
- Om du köper en reservation med delat omfång kan det ta upp till fem dagar innan Advisors rekommendationer för reservationsköp försvinner.

## <a name="other-expected-api-behavior"></a>Annat förväntat API-beteende

- Om du försöker få rekommendationer som bygger på data från de senaste sju dagarna kanske detta misslyckas om de virtuella datorerna har stängts ned i mer än en dag.

## <a name="next-steps"></a>Nästa steg
- Hämta [reservationsrekommendationer med hjälp av REST-API:er.](/rest/api/consumption/reservationrecommendations/list)
- Lär dig [hur rabatten för Azure-reservation tillämpas på virtuella datorer](../manage/understand-vm-reservation-charges.md).
