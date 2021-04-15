---
title: Förstå Microsoft-kundavtalsfaktura i Azure
description: Lär dig att läsa och förstå din Microsoft-kundavtalsfaktura i Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: banders
ms.openlocfilehash: ff53131f3078b33b7e7d853c1fca891b0b86d792
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484612"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termer på din Microsoft-kundavtalsfaktura

Den här artikeln gäller för ett Azure-faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Din faktura innehåller en sammanfattning av dina avgifter och instruktioner för betalning. Du kan ladda ned den i PDF-format (.pdf) från [Azure-portalen](https://portal.azure.com/) eller få den skickad via e-post. Mer information finns i [Visa och ladda ned din Microsoft Azure-faktura](download-azure-invoice.md).

Titta på [videon Understand your Microsoft-kundavtal invoice (Förstå](https://www.youtube.com/watch?v=e2LGZZ7GubA) din faktura) för att lära dig mer om din faktura och hur du analyserar avgifterna på den.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Faktureringsperiod

Du faktureras per månad. Du kan ta reda på vilken dag i månaden du får fakturor genom att kontrollera *fakturadatumet* under egenskaperna för faktureringsprofil i [Azure-portalen](https://portal.azure.com/). Kostnader som inträffar mellan slutet av faktureringsperioden och fakturadatumet inkluderas i nästa månads faktura, eftersom de tillhör nästa faktureringsperiod. Faktureringsperiodens startdatum och slutdatum för varje anges ovanför **Faktureringssammanfattning** i PDF-fakturan.

Om du migrerar från ett EA till en Microsoft-kundavtal fortsätter du att få fakturor för ditt EA fram till migreringsdatumet. Den nya fakturan för Microsoft-kundavtal genereras den femte dagen i månaden efter att du har migrerat. Den första fakturan visar en partiell avgift från migreringsdatumet. Senare fakturor genereras varje månad och visar alla avgifter för varje månad.

### <a name="changes-for-pay-as-you-go-subscriptions"></a>Ändringar för prenumerationer där du betalar enligt din prenumeration

När en prenumeration överförs, överförs eller avbryts innehåller den senaste genererade fakturan avgifter för den tidigare faktureringsperioden och den nya ofullständiga faktureringsperioden.

Exempel:

Anta att faktureringsperioden för din betala per du-prenumeration är från dag 8 till dag 7 i varje månad. Prenumerationen överfördes till en Microsoft-kundavtal den 16 november. Den senaste betala per du-fakturan har avgifter för den 8 oktober 2020 till och med 7 november 2020. Den har också avgifter för den nya partiella faktureringsperioden för Microsoft-kundavtal från 8 november 2020 till och med 16 november 2020. Här är en exempelbild.

:::image type="content" source="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" alt-text="Exempelbild av en faktura som visar den senaste faktureringsperioden." lightbox="./media/mca-understand-your-invoice/last-invoice-billing-cycle.png" :::

## <a name="invoice-terms-and-descriptions"></a>Termer och beskrivningar för faktura

I följande avsnitt anges viktiga termer som står på fakturan samt beskrivningar för respektive term.

### <a name="invoice-summary"></a>Sammanfattning av faktura

**Fakturasammanfattningen** finns längst upp på den första sidan och visar information om din faktureringsprofil samt hur du betalar.

![Avsnittet Fakturasammanfattning](./media/mca-understand-your-invoice/invoicesummary.png)

| Period | Beskrivning |
| --- | --- |
| Såldes till |Adress för din juridiska person, som finns i egenskaperna för faktureringskonto|
| Fakturera till |Faktureringsadressen för den faktureringsprofil som tar emot fakturan, som finns i egenskaperna för faktureringsprofil|
| Faktureringsprofil |Namnet på den faktureringsprofil som tar emot fakturan |
| IO- antal |Ett valfritt inköpsordernummer som du tilldelar i spårningssyfte |
| Fakturanummer |Ett unikt fakturanummer som Microsoft genererar i spårningssyfte |
| Fakturadatum |Det datum då fakturan genereras, vanligtvis fem till tolv dagar efter faktureringsperiodens slut. Du kan kontrollera fakturadatumet i egenskaperna för faktureringsprofil.|
| Betalningsvillkor |Hur du betalar för din Microsoft-faktura. *30 dagar netto* innebär att du betalar inom 30 dagar från fakturadatumet. |

### <a name="billing-summary"></a>Faktureringssammanfattning

I **faktureringssammanfattningen** visas kostnaderna för faktureringsprofilen sedan den föregående faktureringsperioden, eventuella krediter som tillämpades, skatt samt det totala restbeloppet.

![Avsnittet Faktureringssammanfattning](./media/mca-understand-your-invoice/billingsummary.png)

| Period | Beskrivning |
| --- | --- |
| Avgifter|Totalt antal Microsoft-avgifter för den här faktureringsprofilen sedan den senaste faktureringsperioden |
| Krediter |Krediter som du fått från returer |
| Tillämpade Azure-krediter | Azure-krediter som automatiskt tillämpas på Azure-avgifter varje faktureringsperiod |
| Delsumma |Restbelopp före skatt |
| Skatt |Typ av och belopp för skatt som du betalar, beroende på land/region för din faktureringsprofil. Om du inte behöver betala skatt står ingen skatt på din faktura. |
| Uppskattade totalbesparingar |Den uppskattade totalsumma som du har sparat från effektiva rabatter. Om effektiva rabattsatser är tillämpliga visas de nedanför inköpsradobjekten i Information efter fakturaavsnitt. |

### <a name="invoice-sections"></a>Fakturaavsnitt

För varje fakturaavsnitt under din faktureringsprofil ser du avgifterna, hur många Azure-krediter som används, skatt samt totalt restbelopp.

`Total = Charges - Azure Credit + Tax`

### <a name="details-by-invoice-section"></a>Information efter fakturaavsnitt

Informationen visar kostnaden för varje fakturaavsnitt indelat efter produktorder. Inom varje produktorder delas kostnaden in efter typ av tjänst. Det finns dagliga avgifter för dina produkter och tjänster i Azure-portalen samt i CSV-filen för Azure-användning och -avgifter. Mer information finns i [Förstå avgifterna på din faktura för ett Microsoft-kundavtal](review-customer-agreement-bill.md).

Det totala restbeloppet för varje tjänstfamilj beräknas subtraktion av *Azure-krediter* från *Krediter/avgifter* och addition av *Skatt*:


![Information efter fakturaavsnitt](./media/mca-understand-your-invoice/invoicesectiondetails.png)

| Period |Beskrivning |
| --- | --- |
| Enhetspris | Det effektiva enhetspriset för tjänsten (i prisvalutan) som används för att klassificera användningen. Detta är unikt för en produkt, en tjänstfamilj, en mätare och ett erbjudande. |
| Kvantitet | Köpt eller förbrukad kvantitet under faktureringsperioden |
| Avgifter/krediter | Nettobeloppet för avgifterna efter krediter/återbetalningar har tillämpats |
| Azure-kredit | Den mängd Azure-krediter som tillämpats för avgifterna/krediterna|
| Skattesats | Skattesats(er) beroende på land/region |
| Skattebelopp | Den mängd skatt som tillämpas på inköp baserat på skattesats |
| Totalt | Det totala restbeloppet för köpet |

### <a name="how-to-pay"></a>Så betalar du

Längst ned på fakturan finns instruktioner för hur du betalar faktura. Du kan betala med check eller banköverföring eller online. Om du betalar online kan du använda ett kreditkort eller en Azure-kredit, om så är tillämpligt.

### <a name="publisher-information"></a>Utgivarinformation

Om du har tjänster från tredje part på din faktura visas namn och adress för varje utgivare längst ned på fakturan.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomsten till ett Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Förstå avgifterna på faktureringsprofilens faktura](review-customer-agreement-bill.md)
- [Så här hämtar du information om fakturering och daglig användning från Azure](../manage/download-azure-invoice-daily-usage-date.md)
- [Visa organisationens Azure-priser](../manage/ea-pricing.md)
- [Visa skattedokument för din faktureringsprofil](mca-download-tax-document.md)
