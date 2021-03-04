---
title: Förstå Microsoft-kundavtalsfaktura i Azure
description: Lär dig att läsa och förstå din Microsoft-kundavtalsfaktura i Azure
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 996c974473c53833dd26abae2fb61585999f33b4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050451"
---
# <a name="terms-in-your-microsoft-customer-agreement-invoice"></a>Termer på din Microsoft-kundavtalsfaktura

Den här artikeln gäller för ett Azure-faktureringskonto för ett Microsoft-kundavtal. [Kontrollera om du har åtkomst till ett Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Din faktura innehåller en sammanfattning av dina avgifter och instruktioner för betalning. Du kan ladda ned den i PDF-format (.pdf) från [Azure-portalen](https://portal.azure.com/) eller få den skickad via e-post. Mer information finns i [Visa och ladda ned din Microsoft Azure-faktura](download-azure-invoice.md).

Titta på videon om [kund avtals faktura i Microsoft](https://www.youtube.com/watch?v=e2LGZZ7GubA) om du vill veta mer om din faktura och hur du analyserar avgifterna.

>[!VIDEO https://www.youtube.com/embed/e2LGZZ7GubA]

## <a name="billing-period"></a>Faktureringsperiod

Du faktureras per månad. Du kan ta reda på vilken dag i månaden du får fakturor genom att kontrollera *fakturadatumet* under egenskaperna för faktureringsprofil i [Azure-portalen](https://portal.azure.com/). Kostnader som inträffar mellan slutet av faktureringsperioden och fakturadatumet inkluderas i nästa månads faktura, eftersom de tillhör nästa faktureringsperiod. Faktureringsperiodens startdatum och slutdatum för varje anges ovanför **Faktureringssammanfattning** i PDF-fakturan.

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
