---
title: Förstå hur Azure-reservationer används för Enterprise-avtal
description: Lär dig hur du läser dina användningsdata, så att du vet hur Azure-reservationen används med din Enterprise-registrering.
author: bandersmsft
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: banders
ms.openlocfilehash: 874d5cb022a38b172bb37009bd86b5e6988f3204
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545612"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Hämta data om reservationskostnader och reservationsanvändning för Enterprise-avtal (EA)

Reservationskostnader och användningsdata är tillgängliga för Enterprise-avtalskunder från Azure-portalen och REST-API:er. I den här artikeln lär du dig hur du:

- Hämtar data om reservationsköp
- Ser vilken prenumeration, resursgrupp eller resurs som använt reservationen
- Allokerar reservationsförbrukning
- Beräknar reservationsbesparingar
- Hämtar data om underutnyttjade reservationer
- Amorterar reservationskostnader

Marketplace-kostnaderna slås samman med användningsdata. Du kan visa avgifter för användning av tjänster från första part, Marketplace-användning och köp från en enskild datakälla.

## <a name="reservation-charges-in-azure-usage-data"></a>Reservationsavgifter i Azure-användningsdata

Data delas upp i två separata datauppsättningar: _Verklig kostnad_ och _Amorterad kostnad_. De två datauppsättningarna skiljer sig åt på följande sätt:

**Faktisk kostnad** – Tillhandahåller data för avstämning med din månadsfaktura. Dessa data innehåller kostnader för reservationsköp och information om reservationsprogram. Med dessa data kan du se vilken prenumeration eller resursgrupp eller resurs som reservationsrabatten tillämpades på en viss dag. EffectivePrice för den användning som reservationsrabatten tillämpas på är noll.

**Amorterad kostnad** – Den här datauppsättningen liknar datauppsättningen Faktisk kostnad förutom att – EffectivePrice för den användning som erhåller reservationsrabatt är den beräknade kostnaden för reservationen (i stället för noll). På så sätt kan du se penningvärdet för reservationsförbrukningen för en prenumeration, en resursgrupp eller en resurs. Det gör det också enklare att allokera reservationsanvändningen internt. Datauppsättningen har även oanvända reservationstimmar. Datauppsättningen saknar poster för reservationsköp.

Jämförelse av två datauppsättningar:

| Data | Datauppsättningen Faktisk kostnad | Datauppsättningen Amorterad kostnad |
| --- | --- | --- |
| Reservationsköp | Tillgängligt i den här vyn.<br><br>  Du kan hämta dessa data genom att filtrera på ChargeType = &quot;Purchase&quot;. <br><br> ReservationID eller ReservationName anger vilken reservation avgiften avser.  | Inte tillämpligt för den här vyn. <br><br> Inköpskostnader visas inte i amorterade data. |
| EffectivePrice | Värdet är noll för användning som erhåller reservationsrabatt. | Värdet är den beräknade kostnaden per timme för den reservation som reservationsrabatten tillämpas på. |
| Oanvänd reservation (visar antalet timmar som reservationen inte användes under en dag och penningvärdet för den outnyttjade tiden) | Inte tillämpligt i den här vyn. | Tillgängligt i den här vyn.<br><br> Du kan hämta dessa data genom att filtrera på ChargeType = &quot;UnusedReservation&quot;.<br><br>  ReservationID eller ReservationName indikerar vilken reservation som underutnyttjades. Detta avser hur mycket av reservationen som inte utnyttjades under dagen.  |
| UnitPrice (pris för resursen från prisdokumentet) | Tillgängligt | Tillgängligt |

Annan information som är tillgänglig i Azures användningsdata har ändrats:

- Produkt- och mätarinformation – Azure ersätter inte den ursprungligen förbrukade mätaren med ReservationId och ReservationName, vilket var fallet tidigare.
- ReservationId och ReservationName – De är egna fält i datauppsättningen. Tidigare var de bara tillgängliga under AdditionalInfo.
- ProductOrderId – ID:t för reservationsorder, tillagt som ett eget fält.
- ProductOrderName – Produktnamnet för den köpta reservationen.
- Term – 12 månader eller 36 månader.
- RINormalizationRatio – Tillgängligt under AdditionalInfo. Detta anger i vilken grad reservationen tillämpas på användningsposten. Om flexibilitet för instansstorlek är aktiverat för din reservation kan det gälla andra storlekar. Värdet visar i vilken grad reservationen tillämpades på användningsposten.

[Se definition för fält](/rest/api/consumption/usagedetails/list#definitions)

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Hämta data om användning och reservationer i Azure med hjälp av API:er

Du kan hämta data med hjälp av API:et eller ladda ned det från Azure-portalen.

Du anropar [API:et för användningsinformation](/rest/api/consumption/usagedetails/list) för att hämta nya data. Mer information om terminologi finns i [användningsvillkoren](../understand/understand-usage.md). Anroparen måste vara en företagsadministratör för Enterprise-avtalet och använda [EA-portalen](https://ea.azure.com). Företagsadministratörer som har skrivskyddad behörighet kan också hämta data.

Observera att dessa data inte är tillgängliga i [rapporterings-API:er för Enterprise-kunder – användningsinformation](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

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

Om du är EA-administratör kan du ladda ned CSV-filen som innehåller nya användningsdata från Azure-portalen. Dessa data är inte tillgängliga från EA-portalen (ea.azure.com). Du måste hämta användningsfilen från Azure-portalen (portal.azure.com) för att se nya data.

Navigera till [Kostnadshantering + fakturering](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts) på Azure-portalen.

1. Välj faktureringskontot.
2. Klicka på **Användning + avgifter**.
3. Klicka på **Hämta**.  
![Exempel som visar var du kan ladda ned CSV-filen med användningsdata på Azure-portalen](./media/understand-reserved-instance-usage-ea/portal-download-csv.png)
4. I **Ladda ned användning + kostnader** under **Användningsinformation version 2** väljer du **Alla avgifter (användning och inköp)** och klickar sedan på Ladda ned. Upprepa för **Amorterade avgifter (användning och inköp)** .

CSV-filerna som du laddar ned innehåller faktiska kostnader och amorterade kostnader.

## <a name="common-cost-and-usage-tasks"></a>Vanliga kostnader och användningsaktiviteter

Följande avsnitt beskriver vanliga sätt att visa reservationskostnader och användningsdata.

### <a name="get-reservation-purchase-costs"></a>Hämta kostnader för reservationsköp

Kostnader för reservationsköp är tillgängliga i data om faktiska kostnader. Filtrera baserat på _ChargeType = Purchase_. ProductOrderID anger vilken reservationsorder köpet avser.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Hämta antal och kostnader för outnyttjade reservationer

Hämta data om amorterade kostnader och filtrera baserat på _ChargeType_ _= UnusedReservation_. Den dagliga outnyttjade reservationskvantiteten och kostnaden visas. Du kan filtrera data för en reservation eller reservationsorder med hjälp av fälten _ReservationId_ och _ProductOrderId_. Om 100 % av en reservation användes, visar posten antalet 0.

### <a name="amortize-reservation-costs"></a>Amortera reservationskostnader

Hämta data om amorterade kostnader och filtrera fram en reservationsorder med hjälp av _ProductOrderID_ för att visa dagliga amorterade kostnader för en reservation.

### <a name="chargeback-for-a-reservation"></a>Allokera en reservation baserat på faktisk förbrukning

Du kan allokera reservationsanvändning till andra organisationer per prenumeration, resursgrupper eller taggar. Data om amorterade kostnader visar penningvärdet för en reservations förbrukning för följande datatyper:

- Resurser (till exempel en virtuell dator)
- Resursgrupp
- Taggar
- Prenumeration

### <a name="get-the-blended-rate-for-chargeback"></a>Hämta det kombinerade priset för återbetalning

Du kan beräkna det kombinerade priset genom att hämta data om de amorterade kostnaderna och aggregera totalkostnaden. För virtuella datorer kan du använda antingen MeterName- eller ServiceType-information från JSON-data för AdditionalInfo. Dividera den totala kostnaden med kvantiteten som användes för att hämta det kombinerade priset.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Granska optimal reservationsanvändning för flexibel instansstorlek

Multiplicera antalet med _RINormalizationRatio_, från AdditionalInfo. Resultatet visar hur många timmars reservationsanvändning som tillämpades på användningsposten.

### <a name="determine-reservation-savings"></a>Fastställa reservationsbesparingar

Hämta data om de amorterade kostnaderna och filtrera fram data för en reserverad instans. Sedan:

1. Visa en uppskattning av kostnaderna för användningsbaserad betalning. Multiplicera värdet för _UnitPrice_ (Enhetspris) med _Quantity_ (Kvantitet) för att beräkna kostnaderna för användningsbaserad betalning, om reservationsrabatten inte gällde för användningen.
2. Hämta reservationskostnaderna. Summera värdena för _Cost_ (Kostnad) för att hämta penningvärdet för det du betalade för den reserverade instansen. Den innehåller reservationens använda och oanvända kostnader.
3. Subtrahera reservationskostnader från uppskattade kostnader för användningsbaserad betalning för att beräkna de uppskattade besparingarna.

Om du har en underutnyttjad reservation måste du ha posten _UnusedReservation_ för _ChargeType_ i åtanke. En reservation som utnyttjas fullt ut ger högsta möjliga besparing. En _UnusedReservation_-kvantitet innebär mindre besparingar.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Köp och amortering av reservationer i kostnadsanalys

Reservationskostnader är tillgängliga i [kostnadsanalysen](https://aka.ms/costanalysis). Som standard visar kostnadsanalysen den **faktiska kostnaden**, dvs. hur kostnader visas på din faktura. Om du vill visa reservationsköp uppdelat och associerat med de resurser som använde förmånen byter du till **amorterad kostnad**:

![Exempel som visar var du väljer amorterad kostnad i kostnadsanalys](./media/understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Gruppera efter avgiftstyp om du vill visa en uppdelning av användning, inköp och återbetalningar, eller efter reservation om du vill visa en uppdelning av reservationskostnader och kostnader på begäran. Kom ihåg att de enda reservationskostnader du ser när du tittar på den faktiska kostnaden är inköp, men att kostnaderna är allokerade till de enskilda resurserna som använde förmånen när du tittar på en amorterad kostnad. Du ser också en ny **UnusedReservation**-avgiftstyp när du tittar på en amorterad kostnad.

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om Azure-reservationer i följande artiklar:

- [Vad är Azure-reservationer?](save-compute-costs-reservations.md)
- [Förskottsbetala för Virtual Machines med Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Förskottsbetala för SQL Database-beräkningsresurser med reserverad kapacitet för Azure SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Hantera Azure Reservations](manage-reserved-vm-instance.md)
- [Förstå hur reservationsrabatten tillämpas](../manage/understand-vm-reservation-charges.md)
- [Förstå reservationsanvändning för din Betala per användning-prenumeration](understand-reserved-instance-usage.md)
- [Kostnader för Windows-programvara som inte ingår i reservationer](reserved-instance-windows-software-costs.md)