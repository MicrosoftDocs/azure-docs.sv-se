---
title: Återbetala kostnader för Azure-reservationer
description: Lär dig hur du visar kostnader för Azure-reservationer för återbetalning.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/10/2021
ms.author: banders
ms.openlocfilehash: 4fb15a7e677d566454d5d487c1cf69767d7f3a30
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368752"
---
# <a name="charge-back-azure-reservation-costs"></a>Återbetala kostnader för Azure-reservationer

Faktureringsläsare för Enterprise-avtal och Microsoft-kundavtal kan visa amorterade kostnadsdata för reservationer. De kan använda dessa kostnadsdata för att återbetala det monetära värdet för en prenumeration, en resursgrupp, en resurs eller en tagg till sina partner. I amorterade data är det effektiva priset den beräknade kostnaden per timme för reservationer. Kostnaden är den totala kostnaden för resursens reservationsanvändning den dagen.

Användare med en enskild prenumeration kan hämta amorterade kostnadsdata från sin användningsfil. När en resurs får en reservationsrabatt innehåller avsnittet *AdditionalInfo* i användningsfilen reservationsinformationen. Du kan läsa mer i [Ladda ned användningsinformation från Azure-portalen](../understand/download-azure-daily-usage.md#download-usage-from-the-azure-portal-csv).

## <a name="see-reservation-usage-data-for-show-back-and-charge-back"></a>Se reservations användnings data för Visa tillbaka och tillbaka till åter debitering

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till **Cost Management + fakturering** 
3. Välj **kostnads analys** från vänster navigering 
4. Under **Faktisk kostnad** väljer du måttet **Amorterad kostnad**.
5. Om du vill se vilka resurser som använts av en reservation använder du ett filter för **reservation** och väljer sedan reservationer.
6. Ange **Kornighet** till **Varje månad** eller **Varje dag**.
7. Ange diagramtypen till **Tabell**.
8. Ange alternativet **Gruppera efter** till **Resurs**.

[![Exempel som visar kostnader för reservationsresurser som du kan använda för återbetalning](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Här är en video som visar hur du ser kostnader för reservationsanvändning i Azure Portal.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="get-the-data-for-show-back-and-charge-back"></a>Hämta data för att Visa tillbaka och debitera igen
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Navigera till **Cost Management + fakturering** 
3. Välj **Exportera** från vänster navigering 
4. Klicka på knappen **Lägg till**
5. Välj amorterings kostnad som knappen mått och konfigurera exporten

EffectivePrice för användning som erhåller reservations rabatt är den beräknade kostnaden för reservationen (i stället för att vara noll). På så sätt kan du se penningvärdet för reservationsförbrukningen för en prenumeration, en resursgrupp eller en resurs. Det gör det också enklare att allokera reservationsanvändningen internt. Datauppsättningen har även oanvända reservationstimmar. 

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Hämta data om användning och reservationer i Azure med hjälp av API:er

Du kan hämta data med hjälp av API:et eller ladda ned det från Azure-portalen.

Du anropar [API:et för användningsinformation](/rest/api/consumption/usagedetails/list) för att hämta nya data. Mer information om terminologi finns i [användningsvillkoren](../understand/understand-usage.md).

Här är ett exempel på ett anrop till API:et för användningsinformation:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-05-01&amp;$filter={filter}
```

Mer information om {enrollmentId} och {billingPeriodId} finns i artikeln om [API:et för användningsinformation – lista](/rest/api/consumption/usagedetails/list).

Informationen i följande tabell om mått och filter kan hjälpa dig att lösa vanliga reservationsproblem.

| **Typ av API-data** | API-anropsåtgärd |
| --- | --- |
| **Alla avgifter (användning och inköp)** | Ersätt {metric} med ActualCost |
| **Användning som erhållit reservationsrabatt** | Ersätt {metric} med ActualCost<br><br>Ersätt {filter} med: properties/reservationId%20ne%20 |
| **Användning som inte erhöll reservationsrabatt** | Ersätt {metric} med ActualCost<br><br>Ersätt {filter} med: properties/reservationId%20eq%20 |
| **Amorterade avgifter (användning och inköp)** | Ersätt {metric} med AmortizedCost |
| **Oanvänd reservationsrapport** | Ersätt {metric} med AmortizedCost<br><br>Ersätt {filter} med: properties/ChargeType%20eq%20'UnusedReservation' |
| **Reservationsköp** | Ersätt {metric} med ActualCost<br><br>Ersätt {filter} med: properties/ChargeType%20eq%20'Purchase'  |
| **Återbetalningar** | Ersätt {metric} med ActualCost<br><br>Ersätt {filter} med: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>Ladda ned CSV-filen med nya användningsdata

Om du är en EA-administratör kan du ladda ned den CSV-fil som innehåller nya användnings data från Azure Portal. Dessa data är inte tillgängliga från EA-portalen (ea.azure.com). Du måste hämta användningsfilen från Azure-portalen (portal.azure.com) för att se nya data.

Navigera till [Kostnadshantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts) på Azure-portalen.

1. Välj faktureringskontot.
2. Klicka på **Användning + avgifter**.
3. Klicka på **Hämta**.  
![Exempel som visar var du kan ladda ned CSV-filen med användningsdata på Azure-portalen](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. I **användnings information** väljer du **periodiserade användnings data**.

CSV-filerna som du laddar ned innehåller faktiska kostnader och amorterade kostnader.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nästa steg
- Mer information om Azure Reservations användnings data finns i följande artiklar:
  - [Enterprise-avtal och Microsofts kund avtal reservations kostnader och användning](understand-reserved-instance-usage-ea.md)
 
