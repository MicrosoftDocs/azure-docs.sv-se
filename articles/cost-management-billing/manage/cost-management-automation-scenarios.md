---
title: Automationsscenarier för Azure-fakturering och kostnadshantering
description: Lär dig hur vanliga scenarier för fakturering och kostnadshantering mappas till olika API:er.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: e0a7553269e1223a5e744db967d65814877878ab
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598277"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Automationsscenarier för fakturering och kostnadshantering

Den här artikeln innehåller vanliga scenarier för fakturering och kostnadshantering i Azure. Den mappar dessa scenarier till API:er som du kan använda. I varje scenario-till-API-mappning finns en sammanfattning av API:erna och de funktioner som de innehåller.

## <a name="common-scenarios"></a>Vanliga scenarier

Du kan använda API:erna för fakturering och kostnadshantering i flera scenarier för att besvara frågor kring kostnader och användning. Här är en översikt över vanliga scenarier:

- **Fakturaavstämning**: Debiterade Microsoft mig rätt belopp?  Hur mycket är min faktura på och kan jag beräkna beloppet själv?

- **Tvärdebiteringar**: Nu när jag vet hur mycket jag debiteras, vem i min organisation ska betala?

- **Kostnadsoptimering**: Jag vet hur mycket jag har debiterats. Hur kan jag få ut mer av de pengar som jag spenderar på Azure?

- **Kostnadsspårning**: Jag vill se hur mycket jag betalar och använder Azure över tid. Hur ser trenderna ut? Hur kan jag göra detta på bättre sätt?

- **Azure-utgifter under månaden**: Hur stora är mina utgifter hittills under aktuell månad? Behöver jag göra några ändringar i mina utgifter och/eller användning av Azure? När under månaden använder jag Azure mest?

- **Aviseringar**: Hur konfigurerar jag aviseringar för resursbaserad förbrukning eller penningbaserade aviseringar?

## <a name="scenario-to-api-mapping"></a>Scenario-till-API-mappning

|         API        | Fakturaavstämning    | Tvärdebiteringar    | Kostnadsoptimering    | Kostnadsspårning    | Utgifter i mitten av månaden    | Aviseringar    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budgetar                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace-debiteringar                |             X             |         X        |           X          |         X        |          X         |     X     |
| Prisdokument                 |             X             |         X        |           X          |         X        |          X         |           |
| Reservationsrekommendationer |                           |                  |           X          |                  |                    |           |
| Information om reservation         |                           |                  |           X          |         X        |                    |           |
| Reservationssammanfattningar       |                           |                  |           X          |         X        |                    |           |
| Användningsinformation               |             X             |         X        |           X          |         X        |          X         |     X     |
| Faktureringsperioder             |             X             |         X        |           X          |         X        |                    |           |
| Fakturor                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Oklassificerad användning               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Scenario-till-API-mappningen omfattar inte Enterprise-förbruknings-API:er. Använd om möjligt de allmänna förbruknings-API:erna för nya utvecklingsscenarier.

## <a name="api-summaries"></a>API-sammanfattningar

### <a name="consumption"></a>Förbrukning
Web Direct- och Enterprise-kunder kan använda alla följande API:er, förutom där något annat anges:

-   [Budget-API](/rest/api/consumption/budgets) (*endast Enterprise-kunder*): Skapa kostnads- eller användningsbudgetar för resurser, resursgrupper eller faktureringsmätare. När du har skapat budgetar kan du konfigurera aviseringar som meddelar dig när du har överskridit definierade budgettrösklar. Du kan även konfigurera åtgärder som ska ske när du har nått budgetbeloppen.

-   [API för Marketplace-avgifter](/rest/api/consumption/marketplaces): Hämta avgifts- och användningsdata för alla Azure Marketplace-resurser (Azure-partnererbjudanden). Du kan använda dessa data för att summera kostnaderna för alla Marketplace-resurser eller för att analysera kostnaderna/användningen för specifika resurser.

-   [API för prisdokument](/rest/api/consumption/pricesheet) (*endast Enterprise-kunder*): Hämta anpassad prissättning för alla mätare. Företag kan använda dessa data i kombination med användningsdata och information om Marketplace-användning för att beräkna kostnader med hjälp av användnings- och Marketplace-data.

-   [API för reservationsrekommendationer](/rest/api/consumption/reservationrecommendations): Få rekommendationer om köp av reserverade VM-instanser. Rekommendationer hjälper dig att analysera förväntade kostnadsbesparingar och inköpsbelopp. Mer information finns i [API:er för automation av Azure-reservation](../reservations/reservation-apis.md).

-   [API för reservationsinformation](/rest/api/consumption/reservationsdetails): Se information om tidigare köpta VM-reservationer, till exempel hur mycket förbrukning som är reserverad jämfört med hur mycket som används. Du kan visa data på nivån för en enskild virtuell dator. Mer information finns i [API:er för automation av Azure-reservation](../reservations/reservation-apis.md).

-   [API för reservationssammanfattningar](/rest/api/consumption/reservationssummaries): Se aggregerad information om VM-reservationer som din organisation har köpt, till exempel hur mycket förbrukning som är reserverad jämfört med hur mycket som används i aggregatet. Mer information finns i [API:er för automation av Azure-reservation](../reservations/reservation-apis.md).

-   [API för användningsinformation](/rest/api/consumption/usagedetails): Få information om kostnader och användning för alla Azure-resurser från Microsoft. Informationen visas i form av poster med användningsinformation och skickas för närvarande en gång per mätare och dag. Du kan använda informationen för att summera kostnaderna för alla resurser eller för att analysera kostnader/användning för specifika resurser.

-   [API för RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)): Hämta mätarpriser om du är Web Direct-kund. Du kan sedan använda den returnerade informationen med din information resursanvändning till att beräkna den förväntade fakturan manuellt.

-   [API för oklassificerad användning](/previous-versions/azure/reference/mt219003(v=azure.100)): Hämta rå användningsinformation innan Azure utför mätning/avgiftsberäkning.

### <a name="billing"></a>Fakturering
-   [API för faktureringsperioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Fastställ en faktureringsperiod som ska analyseras, tillsammans med faktura-ID:n för den perioden. Du kan använda faktura-ID:n med API:et för fakturor.

-   [API för fakturor](/rest/api/billing/2019-10-01-preview/invoices): Hämta nedladdnings-URL för en faktura för en faktureringsperiod i PDF-format.

### <a name="enterprise-consumption"></a>Enterprise-förbrukning
Följande API:er gäller endast för Enterprise:

-   [API för saldosammanfattning](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Få en månatlig sammanfattning med information om saldon, nya inköp, kostnader för Azure Marketplace-tjänster, justeringar samt kostnader för överförbrukning. Du kan hämta den här informationen för den aktuella faktureringsperioden eller för en tidigare period. Företag kan använda dessa data för att jämföra dem med manuellt beräknade sammanfattningskostnader. Det här API:et returnerar inte resursspecifik information eller en sammanställd vy över kostnaderna.

-   [API för användningsinformation](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Få information om Azure-användning (av Microsoft-erbjudanden) för aktuell månad, en viss faktureringsperiod eller en anpassad datumperiod. Företag kan använda dessa data för att manuellt beräkna fakturor baserat på pris och förbrukning. Företag kan även använda avdelnings-/organisationsinformation för att tillskriva olika organisationer kostnader. Dessa data ger en resursspecifik vy av användning/kostnad.

-   [API för Marketplace Store-avgifter](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Få information om Azure-användning (av partnererbjudanden) för aktuell månad, en viss faktureringsperiod eller en anpassad datumperiod. Företag kan använda dessa data för att manuellt beräkna fakturor baserat på pris och förbrukning. Företag kan även använda avdelnings-/organisationsinformation för att tillskriva olika organisationer kostnader. Detta API ger en resursspecifik vy av användning/kostnad.

-   [API för prisdokument](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Hämta relevant pris för respektive mätare för den aktuella registreringen och faktureringsperioden. Du kan använda den här prisinformationen i kombination med användningsinformation och information om Marketplace-användning för att beräkna den förväntade fakturan manuellt.

-   [API för faktureringsperioder](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Hämta en lista över faktureringsperioder. API:et ger dig även en egenskap som pekar på API-vägen för de fyra uppsättningarna med Enterprise API-data som hör till faktureringsperioden: BalanceSummary, UsageDetails, Marketplace-avgifter, and PriceSheet.

-   [API för rekommendationer för reserverad instans](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): Titta på 7 dagar, 30 dagar eller 60 dagar med VM-användning och få rekommendationer om enskilt och delad inköp. Du kan använda detta API för att analysera förväntade kostnadsbesparingar och rekommenderade inköpsbelopp. Mer information finns i [API:er för automation av Azure-reservation](../reservations/reservation-apis.md).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Vad är skillnaden mellan API:erna för företagsrapportering och förbruknings-API:erna? När ska jag använda respektive API?
Dessa API:er har en liknande uppsättning funktioner och kan besvara samma breda uppsättning frågor vad gäller fakturering och kostnadshantering. Men de riktar sig till olika målgrupper:

- API:er för företagsrapportering är tillgängliga för kunder som har signerat ett Enterprise-avtal med Microsoft som ger dem tillgång till förhandlad Azure-förskottsbetalning (kallades tidigare ekonomiskt åtagande) och anpassad prissättning. API:erna kräver en nyckel som du kan hämta från [Enterprise-portalen](https://ea.azure.com). En beskrivning av dessa API:er finns i [översikten över rapporterings-API:er för Enterprise-kunder](enterprise-api.md).

- Förbruknings-API:er är tillgängliga för alla kunder, med några undantag. Mer information finns i [Översikt över API för Azure Consumption](consumption-api-overview.md) och [referensen för förbruknings-API i Azure](/rest/api/consumption/). Vi rekommenderar de tillhandahållna API:erna som lösning för de senaste utvecklingsscenarierna.

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Vad är skillnaden mellan API:et för användningsinformation och API:et för användning?
Dessa API:er erbjuder data som skiljer sig i grunden:

- [API:et för användningsinformation](/rest/api/consumption/usagedetails) ger information om Azure-användning och kostnader per mätarinstans. Tillhandahållna data har redan passerat genom kostnadsmätningssystemet i Azure, och kostnader samt andra möjliga ändringar har tillämpats på dem:

   - Ändringar av kontot för användning av förbetald Azure-förskottsbetalning
   - Ändringar av kontot för användningsskillnader som identifieras av Azure

- [API:er för användning](/previous-versions/azure/reference/mt219003(v=azure.100)) ger rå Azure-användningsinformation innan den går genom systemet för kostnadsberäkning i Azure. Dessa data har kanske inte någon korrelation med den användning eller det avgiftsbelopp som visas efter Azure-systemet för avgiftsberäkning.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Vad är skillnaden mellan API:et för fakturering och API:et för användningsinformation?
Dessa API:er ger olika vyer av samma data:

- [Faktura-API:et](/rest/api/billing/2019-10-01-preview/invoices) är endast till för Web Direct-kunder. Det ger en månatlig sammanställning av din faktura baserat på de aggregerade avgifterna för varje mätartyp.

- [API:et för användningsinformation](/rest/api/consumption/usagedetails) ger en detaljerad vy av användnings-/kostnadsposterna för varje dag. Det kan användas av både Enterprise-kunder och Web Direct-kunder.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Vad är skillnaden mellan API:et för prisdokument och API:et för RateCard?
Dessa API:er tillhandahåller liknande datauppsättningar men har olika målgrupper:

- [API:et för prisdokument](/rest/api/consumption/pricesheet) ger den anpassade prissättning som förhandlades för en Enterprise-kund.

- [API:et för RateCard](/previous-versions/azure/reference/mt219005(v=azure.100)) ger den offentliga prissättning som gäller för Web Direct-kunder.

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder Azure-API:er för att programmatiskt få inblick i din Azure-användning finns i [Översikt över förbruknings-API i Azure](consumption-api-overview.md) och [Översikt över fakturerings-API i Azure](usage-rate-card-overview.md).

- För att jämföra din faktura med filen med detaljerad daglig användning och kostnadshanteringsrapporterna i Azure-portalen kan du läsa [Förstå fakturan för Microsoft Azure](../understand/review-individual-bill.md).

- Om du har frågor eller behöver hjälp kan du [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).