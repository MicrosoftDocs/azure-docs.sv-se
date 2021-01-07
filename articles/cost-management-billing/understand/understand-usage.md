---
title: Förstå din detaljerade användning och dina avgifter
description: Lär dig hur du läser och förstår filen med din detaljerade användning och dina avgifter. Visa en lista med villkor och beskrivningar som används i filen.
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: banders
ms.openlocfilehash: 07e3cfdce238d5fc4e2737a49dde6fd624de8506
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882508"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Förstå villkoren i filen för din Azure-användning och dina avgifter

Den detaljerade användnings- och avgiftsfilen innehåller daglig beräknad användning baserat på förhandlade priser, inköp (till exempel reservationer och Marketplace-avgifter) samt återbetalningar för den angivna perioden.
Avgifter omfattar inte krediter, skatter eller andra avgifter eller rabatter.
Följande tabell beskriver vilka avgifter som ingår för varje kontotyp.

Kontotyp | Azure-användning | Marketplace-användning | Inköp | Återbetalningar
--- | --- | --- | --- | ---
Enterprise-avtal (EA) | Ja | Ja | Ja | Inga
Microsoft-kundavtal (MCA) | Ja | Ja | Ja | Ja
Betala per användning (PAYG) | Ja | Ja | Inga | Inga

Mer information om Marketplace-beställningar (som även kallas externa tjänster) finns i [Förstå dina avgifter för externa Azure-tjänster](understand-azure-marketplace-charges.md).

Instruktioner om hur du laddar ned finns i [Så här hämtar du information om fakturering och daglig användning från Azure](../manage/download-azure-invoice-daily-usage-date.md).
Du kan öppna CSV-filen med användning och avgifter i Microsoft Excel eller ett annat kalkylbladsprogram.

## <a name="list-of-terms-and-descriptions"></a>Lista över termer och beskrivningar

I följande tabell beskrivs de viktiga termer som används i den senaste versionen av Azure-filen för användning och avgifter.
Listan omfattar konton för Betala per användning (PAYG), Enterprise-avtal (EA) och Microsoft-kundavtal (MCA).

Period | Kontotyp | Beskrivning
--- | --- | ---
AccountName | EA, PAYG | Visningsnamn för EA-registreringskontot eller PAYG-faktureringskontot.
AccountOwnerId<sup>1</sup> | EA, PAYG | Unik identifierare för EA-registreringskontot eller PAYG-faktureringskontot.
AdditionalInfo | Alla | Tjänstspecifika metadata. Det kan till exempel vara en avbildningstyp för en virtuell dator.
BillingAccountId<sup>1</sup> | Alla | Unik identifierare för rotfaktureringskontot.
BillingAccountName | Alla | Namnet på faktureringskontot.
BillingCurrency | Alla | Valuta som är associerad med faktureringskontot.
BillingPeriod | EA, PAYG | Avgiftens faktureringsperiod.
BillingPeriodEndDate | Alla | Slutdatumet för faktureringsperioden.
BillingPeriodStartDate | Alla | Startdatumet för faktureringsperioden.
BillingProfileId<sup>1</sup> | Alla | Unik identifierare för EA-registrering, PAYG-prenumeration, MCA-faktureringsprofil eller konsoliderat AWS-konto.
BillingProfileName | Alla | Namn på EA-registrering, PAYG-prenumeration, MCA-faktureringsprofil eller konsoliderat AWS-konto.
ChargeType | Alla | Anger huruvida avgiften representerar användning (**Usage**), ett inköp (**Purchase**) eller en återbetalning (**Refund**).
ConsumedService | Alla | Namnet på den tjänst som avgiften är associerad med.
CostCenter<sup>1</sup> | EA, MCA | Det kostnadsställe som har definierats för prenumerationen för spårning av kostnader (endast tillgängligt i öppna faktureringsperioder för MCA-konton).
Kostnad | EA, PAYG | Se CostInBillingCurrency.
CostInBillingCurrency | MCA | Kostnaden för avgiften i faktureringsvalutan före krediter eller skatter.
CostInPricingCurrency | MCA | Kostnaden för avgiften i prissättningsvalutan före krediter eller skatter.
Valuta | EA, PAYG | Se BillingCurrency.
Date<sup>1</sup> | Alla | Avgiftens användnings- eller inköpsdatum.
EffectivePrice | Alla | Blandat enhetspris för perioden. Blandade priser jämnar ut eventuella fluktuationer i enhetspriset, till exempel graderade nivåer, vilket sänker priset allt eftersom kvantiteten ökar med tiden.
ExchangeRateDate | MCA | Det datum då växelkursen upprättades.
ExchangeRatePricingToBilling | MCA | Växelkurs som används för att konvertera kostnaden i prissättningsvalutan till faktureringsvalutan.
Frequency | Alla | Anger huruvida en avgift förväntas upprepas. Avgifter kan antingen ske en gång (**OneTime**), upprepas månatligen eller årsvis (**Recurring**) eller baseras på användning (**UsageBased**).
InvoiceId | PAYG, MCA | Det unika dokument-ID som visas på PDF-filen för faktura.
InvoiceSection | MCA | Se InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Unik identifierare för avsnittet för EA-avdelning eller MCA-faktura.
InvoiceSectionName | EA, MCA | Namn på avsnittet för EA-avdelning eller MCA-faktura.
IsAzureCreditEligible | Alla | Anger huruvida avgiften är berättigad att betalas för med hjälp av Azure-krediter (värden: True, False).
Location | MCA | Platsen för det datacenter där resursen körs.
MeterCategory | Alla | Namn på mätarens klassificeringskategori. Det kan till exempel vara *Molntjänster* och *Nätverk*.
MeterId<sup>1</sup> | Alla | Unik identifierare för mätaren.
MeterName | Alla | Namnet på mätaren.
MeterRegion | Alla | Namnet på datacenterplatsen för tjänster som prissätts baserat på plats. Se Location.
MeterSubCategory | Alla | Namn på kategorin för mätarens underklassificering.
OfferId<sup>1</sup> | Alla | Namnet på det köpta erbjudandet.
PayGPrice | Alla | Resursens butikspris.
PartNumber<sup>1</sup> | EA, PAYG | Identifierare som används för att hämta specifik mätarprissättning.
PlanName | EA, PAYG | Namn på Marketplace-plan.
PreviousInvoiceId | MCA | Referens till en ursprunglig faktura om det här radobjektet är en återbetalning.
PricingCurrency | MCA | Valuta som används vid klassificering baserat på förhandlade priser.
PricingModel | Alla | Identifierare som anger hur mätarens pris bestäms. (Värden: på begäran, reservation, punkt)
Produkt | Alla | Namn på produkten.
ProductId<sup>1</sup> | MCA | Unik identifierare för produkten.
ProductOrderId | Alla | Unik identifierare för produktbeställningen.
ProductOrderName | Alla | Unikt namn för produktbeställningen.
PublisherName | Alla | Utgivare för Marketplace-tjänster.
PublisherType | Alla | Typ av utgivare (värden: **Azure**, **AWS**, **Marketplace**).
Kvantitet | Alla | Antalet köpta eller förbrukade enheter.
ReservationId | EA, MCA | Unik identifierare för den köpta reservationsinstansen.
ReservationName | EA, MCA | Namn på den köpta reservationsinstansen.
ResourceGroup | Alla | Namnet på den [resursgrupp](../../azure-resource-manager/management/overview.md) som resursen finns i. Inte alla avgifter kommer från resurser som distribuerats till resursgrupper. Avgifter som inte har någon resursgrupp visas som null/tomma, **Andra** eller **Inte tillämpligt**.
ResourceId<sup>1</sup> | Alla | Unik identifierare för [Azure Resource Manager](/rest/api/resources/resources)-resursen.
ResourceLocation | Alla | Platsen för det datacenter där resursen körs. Se Location.
ResourceName | EA, PAYG | Namn på resursen. Inte alla resurser kommer från distribuerade resurser. Avgifter som inte har någon resurstyp visas som null/tomma, **Andra** eller **Inte tillämpligt**.
ResourceType | MCA | Typ av resursinstans. Inte alla resurser kommer från distribuerade resurser. Avgifter som inte har någon resurstyp visas som null/tomma, **Andra** eller **Inte tillämpligt**.
ServiceFamily | MCA | Den tjänstfamilj som tjänsten tillhör.
ServiceInfo1 | Alla | Tjänstspecifika metadata.
ServiceInfo2 | Alla | Äldre fält med valfria servicespecifika metadata.
ServicePeriodEndDate | MCA | Slutdatumet för den klassificeringsperiod som definierade och låste prissättning för den förbrukade eller köpta tjänsten.
ServicePeriodStartDate | MCA | Startdatumet för den klassificeringsperiod som definierade och låste prissättning för den förbrukade eller köpta tjänsten.
SubscriptionId<sup>1</sup> | Alla | Unik identifierare för Azure-prenumerationen.
SubscriptionName | Alla | Namnet på Azure-prenumerationen.
Tags<sup>1</sup> | Alla | Taggar som har tilldelats till resursen. Omfattar inte taggar för resursgrupper. Kan användas för att gruppera eller distribuera kostnader för intern återbetalning. Mer information finns i [Organisera dina Azure-resurser med taggar](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Period | Alla | Visar giltighetstiden för erbjudandet. Ett exempel: I händelse av reserverade instanser visas 12 månader som giltighetstiden. För ett engångsköp eller återkommande inköp är giltighetstiden 1 månad (SaaS, Marketplace-support). Detta gäller inte för Azure-förbrukning.
UnitOfMeasure | Alla | Måttenheten för fakturering av tjänsten. Till exempel faktureras beräkningstjänster per timme.
UnitPrice | EA, PAYG | Priset per enhet för avgiften.

_<sup>**1**</sup> Fält som används för att skapa ett unikt ID för en enskild kostnadspost._

Observera att vissa fält kan skilja sig åt vad gäller skiftläge och avstånd mellan olika kontotyper.
Äldre versioner av användningsfiler för Betala per användning innehåller separata avsnitt för utdraget och den dagliga användningen.

### <a name="list-of-terms-from-older-apis"></a>Lista över giltighetsperioder från äldre API:er
Följande tabell mappar giltighetsperioder som används i äldre API:er till de nya giltighetsperioderna. Se tabellen ovan för dessa beskrivningar.

Gammal giltighetsperiod | Ny giltighetsperiod
--- | ---
ConsumedQuantity | Kvantitet
IncludedQuantity | Ej tillämpligt
InstanceId | ResourceId
Pris | EffectivePrice
Enhet | UnitOfMeasure
UsageDate | Datum
UsageEnd | Datum
UsageStart | Datum

## <a name="ensure-charges-are-correct"></a>Se till att avgifterna är korrekta

Om du vill veta mer om detaljerad användning och avgifter kan du läsa om hur du förstår din faktura för [Betala per användning](review-individual-bill.md) eller [Microsoft-kundavtal](review-customer-agreement-bill.md).

## <a name="unexpected-usage-or-charges"></a>Oväntad användning eller oväntade avgifter

Om du har användning eller avgifter som du inte känner igen finns det flera saker du kan göra för att förstå varför:

- Granska fakturan som har avgifter för resursen
- Granska dina fakturerade avgifter i Kostnadsanalys
- Hitta de personer som ansvarar för resursen och samarbeta med dem
- Analysera spårningsloggarna
- Analysera användarbehörigheter till resursens överordnade omfång
- Skapa en [Azure-supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458) för att hjälpa till att identifiera avgifterna

Mer information finns i [Analysera oväntade avgifter](analyze-unexpected-charges.md).

Observera att Azure inte loggar de flesta användaråtgärder. Microsoft loggar i stället resursanvändning för fakturering. Om du märker en tidigare användningsökning och inte hade loggning aktiverad kan Microsoft inte hitta orsaken. Aktivera loggning för den tjänst som du vill visa den ökade användningen för så att rätt tekniska team kan hjälpa dig med problemet.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och ladda ned din Microsoft Azure-faktura](download-azure-invoice.md)
- [Visa och ladda ned information om din användning och dina avgifter för Microsoft Azure](download-azure-daily-usage.md)