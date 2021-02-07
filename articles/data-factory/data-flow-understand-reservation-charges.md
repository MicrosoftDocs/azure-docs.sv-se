---
title: Förstå reservations rabatten för Azure Data Factory data flöden | Microsoft Docs
description: Lär dig hur en reservations rabatt används för att köra ADF-dataflöden. Rabatten tillämpas på dessa data flöden per timme.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 3b97f09c2b6f4c5f5352bbaa081e0bcae900ebd5
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808426"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Hur en reservations rabatt tillämpas på Azure Data Factory data flöden

När du har köpt en reserverad kapacitet för ADF-dataflöde tillämpas reservations rabatten automatiskt på data flöden med hjälp av en Azure integration runtime som matchar beräknings typen och antalet kärnor för reservationen.

## <a name="how-reservation-discount-is-applied"></a>Så tillämpas reservationsrabatten

Reservationsrabatter *går förlorade om de inte används*. Så om du inte har matchande Azure-integrerings resurser som används under en timme förlorar du en reservations kvantitet för den timmen. Det går inte att föra vidare oanvända reserverade timmar.

När du slutar använda integrerings körningen för data flöden tillämpas reservations rabatten automatiskt på en annan matchande resurs i det angivna omfånget. Om det inte finns några matchande resurser i det angivna omfånget går de reserverade timmarna *förlorade*.

## <a name="discount-applied-to-adf-data-flows"></a>Rabatt som tillämpas på ADF-dataflöden

Den reserverade kapacitets rabatten för ADF-dataflödet används för att köra integrerings körningar per timme. Den reservation som du köper matchas med den beräknings typ som används av integrerings körningarna för dina data flöden. För data flöden som inte kör hela timmen tillämpas reservationen automatiskt på andra data flöden som matchar reservationens attribut. Rabatten kan också gälla för data flöden som körs samtidigt. Om du inte har data flöden som körs för hela timmen och som matchar reservations-attributen får du inte den fulla fördelen med reservations rabatten för den timmen.

Följande exempel visar hur den reserverade kapacitets rabatten för ADF-dataflöde gäller beroende på antalet kärnor som du har köpt och när de körs.

- Scenario 1: du köper en data flödes reservation i en timme med 80 kärnor av minnesoptimerade data genom att ange 80 som kvantitet för minnesoptimerade beräknings typ. Du kör ett data flöde med en Azure integration runtime inställd på 144 kärnor av minne optimerat i en timme. Du debiteras priset för betala per användning för 64 kärnor i data flödes användningen i en timme. Du får reservations rabatten i en timme med 80 kärnor av minnesoptimerade användning.
- Scenario 2: du köper ett flödes lager för data flöde i 1 timme med 32 kärnor av generell användnings beräkning genom att ange 32 som kvantitet för beräknings typen generell användning. Du felsöker dina data flöden i 1 timme med 32 kärnor i General Compute Azure integration Runtime. Du får reservations rabatten för hela timmen.

Information om hur du förstår och visar tillämpningen av dina Azure-reservationer i rapporterna för faktureringsanvändning finns i avsnittet om [hur Azure-reservationsanvändning fungerar](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Reservations finns i följande artikel:

- [Vad är Azure-reservationer?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
