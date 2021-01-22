---
title: Optimera Azure Databricks-kostnader med ett förhandsköp
description: Lär dig hur du kan förskottsbetala Azure Databricks-avgifter med reserverad kapacitet för att spara pengar.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 390a8b421a7b34391bde689e4b968fa98cdbaf76
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599172"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Optimera Azure Databricks-kostnader med ett förhandsköp

Du kan minska kostnader för Azure Databricks-enheter (DBU) när du förhandsköper förhandsköpta Azure Databricks-enheter (DBCU) för ett eller tre år. Du kan använda förhandsköpta DBCU:er när som helst under inköpsperioden. Till skillnad från virtuella datorer upphör inte de förhandsköpta enheterna på timbasis, och du kan använda dem när som helst under inköpsperioden.

All Azure Databricks-användning dras automatiskt av från de förhandsköpta DBU:erna. Du behöver inte distribuera om eller tilldela en förhandsköpt plan till dina Azure Databricks-arbetsytor för DBU-användningen för att få förhandsköpsrabatterna.

Förhandsköpsrabatten gäller endast för DBU-användningen. Andra avgifter för till exempel beräkning, lagring och nätverk debiteras separat.

## <a name="determine-the-right-size-to-buy"></a>Välja rätt storlek att köpa

Förhandsköp av Databricks gäller för alla arbetsbelastningar och nivåer i Databricks. Förhandsköp kan liknas vid en pool av förbetalda DBCU:er. Användningen dras av från poolen, oberoende av arbetsbelastning eller nivå. Användningen dras av enligt följande förhållande:

| **Arbetsbelastning** | **DBU-tillämpningsgrad – Standard-nivå** | **DBU-tillämpningsgrad – Premium-nivå** |
| --- | --- | --- |
| Dataanalys | 0,4 | 0,55 |
| Datateknik | 0,15 | 0,30 |
| Datateknik (lättversion) | 0,07 | 0,22 |

Exempelvis dras 0,4 enheter av från de förhandsköpta DBCU:erna när en viss kvantitet av Dataanalys på Standard-nivå använts.

Innan du köper bör du beräkna den totala DBU-kvantitet som förbrukas för olika arbetsbelastningar och nivåer. Använd föregående förhållanden för att normalisera till DBCU och kör sedan en prognos av total användning för nästa år eller de kommande tre åren.

## <a name="purchase-databricks-commit-units"></a>Köpa förhandsköpta Databricks-enheter

Du kan köpa Databricks-planer i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). För att kunna köpa reserverad kapacitet måste du ha rollen som ägare för minst en Enterprise-prenumeration.

- Du måste ha ägarrollen för minst ett Enterprise-avtal (erbjudandenummer: MS-AZR-0017P eller MS-AZR-0148P) eller Microsoft-kundavtal eller en enskild prenumeration med priser enligt principen betala per användning (erbjudandenummer: MS-AZR-0003P eller MS-AZR-0023P).
- För EA-prenumerationer måste alternativet Lägg till reserverade instanser vara aktiverat på EA-portalen. Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.
- För Enterprise-prenumerationer måste **Lägg till reserverade instanser** vara aktiverat i [EA-portalen](https://ea.azure.com/). Om den inställningen är inaktiverad måste du vara EA-administratör för prenumerationen.

**Så här köper du:**

1. Gå till [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Välj en prenumeration. Använd **prenumerationslistan** och välj den prenumeration som används till att betala för den reserverade kapaciteten. Betalningsmetoden för prenumerationen debiteras förhandskostnaderna för den reserverade kapaciteten. Avgifterna dras från saldot för Azure-förskottsbetalningen (kallades tidigare ekonomiskt åtagande) om det finns ett sådant eller debiteras som överförbrukning.
1. Välj ett omfång. Använd **omfångslistan** till att välja ett prenumerationsomfång:
    - **Omfång för enskild resursgrupp** – reservationsrabatten tillämpas endast på matchande resurser i den valda resursgruppen.
    - **Omfång för enskild prenumeration** – reservationsrabatten tillämpas på matchande resurser i den valda prenumerationen.
    - **Delat omfång** – reservationsrabatten tillämpas på matchande resurser i berättigade prenumerationer i faktureringskontexten. För Enterprise-avtalskunder är det registreringen som är faktureringskontext.
1. Välj hur många förhandsköpta Azure Databricks-enheter som du vill köpa och slutför köpet.


![Exempel som visar Azure Databricks-inköp i Azure-portalen](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Ändra omfång och ägarskap

Du kan göra följande typer av ändringar i en reservation efter köpet:

- Uppdatera reservationsomfång
- Azure RBAC (rollbaserad åtkomstkontroll)

Du kan inte dela eller sammanslå förhandsköpet av den förhandsköpta Databricks-enheten. Mer information om hantering av reservationer finns i [Hantera reservationer efter inköp](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Annulleringar och utbyten

Annulleringar och utbyten stöds inte för Databricks-förhandsinköpsplaner. Alla inköp är slutliga.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg

- Du kan läsa mer om Azure-reservationer i följande artiklar:
  - [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
  - [Förstå hur en rabatt för förhandsköp av förhandsköpta Azure Databricks-DBCU tillämpas](reservation-discount-databricks.md)
  - [Förstå reservationsanvändning för din Enterprise-registrering](understand-reserved-instance-usage-ea.md)
