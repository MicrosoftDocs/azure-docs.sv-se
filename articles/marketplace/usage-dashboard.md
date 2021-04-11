---
title: Användnings instrument panel i kommersiell Marketplace-analys | Azure Marketplace
description: Lär dig hur du får åtkomst till alla användnings mått och avgiftsbelagda fakturerings mått för erbjudanden som publicerats på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 765f4f358d593cd5fcc021fbf4ec2276c422c4d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555133"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Instrumentpanelen för användning i Commersial Marketplace-analys

Den här artikeln innehåller information om användnings instrument panelen i Partner Center. Den här instrument panelen visar all virtuell dator (VM) med normaliserad användning, rå data användning och mätnings mått i tre separata flikar: virtuell dator normaliserad användning, VM RAW-användning och mätning av fakturerings användning.

För att få åtkomst till instrument panelen för användning i Partner Center väljer du **[analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** användning under **kommersiell marknads** plats  >  .

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i terminologi för [kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).

## <a name="usage-dashboard"></a>Instrumentpanelen Användning

På instrument panelen för **användning** i **analys** menyn visas de aktuella orderna för alla SaaS-erbjudanden (Software as a Service). Du kan visa grafiska representationer av följande objekt:

- Användnings trend
- Normaliserad användning med erbjudanden
- Normaliserad användning av andra dimensioner: VM-storlek, försäljnings kanaler och erbjudande typer
- Användning efter geografi
- Detaljerad användnings tabell
- Sid filter för order

> [!NOTE]
> Maximal latens mellan generering och rapportering av användnings händelser i Partner Center är 48 timmar.

## <a name="elements-of-the-usage-dashboard"></a>Element i användnings instrument panelen

I följande avsnitt beskrivs hur du använder instrument panelen för användning och hur du läser data.

### <a name="month-range"></a>Månads intervall

Du kan hitta en månads intervall markering i det övre högra hörnet på varje sida. Anpassa utdata från diagrammet med **användnings** sidan genom att välja ett månads intervall baserat på de senaste 6 eller 12 månaderna, eller genom att välja ett anpassat månads intervall med en maximal varaktighet på 12 månader. Standard månads intervallet (beräknings period) är sex månader.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Visar månads filtren på användnings instrument panelen.":::

### <a name="usage-trend"></a>Användnings trend

I det här avsnittet får du total användnings tid och trend för alla dina erbjudanden som används av dina kunder under den valda beräknings perioden. Mått och tillväxt trender representeras av ett linje diagram. Visa värdet för varje månad genom att hovra över linjen i diagrammet. Procentvärdet under användnings måtten i widgeten representerar mängden tillväxt eller neka under den valda beräknings perioden.

Det finns två representationer av användnings timmar: normaliserad VM-användning och VM RAW-användning.

- Normaliserad användnings tid definieras som de användnings timmar som normaliseras för antalet virtuella dator kärnor ([antal VM-kärnor] x [timmar RAW Usage]). Virtuella datorer som anges som "SHAREDCORE" använder 1/6 (eller 0,1666) som [antal VM-kärnor] multiplikatorn.
- Rå användnings timmar definieras som mängden tid som virtuella datorer har körts i timmar.

[![Visar den normaliserade användningen och rå data i användnings instrument panelen.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Normaliserad användning med erbjudanden

Det här avsnittet innehåller den totala förbruknings tiden och trenden för dina användnings erbjudanden i Azure Marketplace. Diagrammet normaliserad användning per erbjudande beskrivs nedan.

- Det staplade **användnings diagrammet med** ett stående stapeldiagram visar en uppdelning av normaliserade användnings timmar för de fem främsta erbjudandena enligt den valda beräknings perioden. De fem främsta erbjudandena visas i ett diagram, medan resten grupperas i resten av kategorin **alla** .
- Det liggande stapeldiagrammet visar en tillväxt trend i månaden för det valda datum intervallet. Månads kolumnerna representerar användnings timmar från erbjudandena med högst användnings timmar för respektive månad. Linje diagrammet visar trenden för tillväxt procent ritad på den sekundära Y-axeln.
- Du kan välja vissa erbjudanden i förklaringen om du bara vill visa de erbjudanden som finns i diagrammet.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Visar hur den normaliserade användningen erbjuder data på instrument panelen för användning.":::

Du kan välja ett erbjudande och maximalt tre SKU: er för det erbjudandet för att visa den månads Visa användnings trenden för erbjudandet och de valda SKU: erna.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Visar de normaliserade användnings erbjudandena och SKU-data på användnings instrument panelen.":::

### <a name="orders-by-offers-and-skus"></a>Beställningar per erbjudande och SKU: er

Diagrammet **beställningar per erbjudanden och SKU** visar mått och trender för alla erbjudanden. . Tänk på följande:

- De främsta erbjudandena visas i diagrammet och resten av erbjudandena grupperas som **rest alla**.
- Du kan välja vissa erbjudanden i förklaringen om du bara vill visa de erbjudanden som finns i diagrammet.
- Om du hovrar över en sektor i diagrammet visas antalet order och procent andel av erbjudandet jämfört med det totala antalet beställningar i alla erbjudanden.
- I **trenderna order by-erbjudanden** visas trender i månaden. Kolumnen månad representerar antalet order efter namnet på erbjudandet. Linje diagrammet visar trenden för tillväxt i procent som ritats på z-axeln.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Visar diagram över beställningar efter erbjudanden och SKU på användnings instrument panelen.":::

Du kan välja ett erbjudande och maximalt tre SKU: er för det erbjudandet för att Visa månads månads trend för erbjudandet, SKU: er och platser.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Visar diagram över beställningar efter erbjudanden och SKU på användnings instrument panelen. Trenderna för erbjudande, SKU och platser visas.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Normaliserad användning av andra dimensioner: VM-storlek, försäljnings kanaler och erbjudande typ

Det finns tre flikar för dimensionerna: VM-storlek, försäljnings kanaler och erbjudande typ. Du kan se användnings måtten och den månads Visa trender för var och en av dessa dimensioner.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Visar det normaliserade användnings diagrammet för andra dimensioner på användnings instrument panelen.":::

### <a name="usage-by-geography"></a>Användning efter geografi

För den valda beräknings perioden visar termisk karta den totala användningen mot geografi dimensionen. Ljuset till mörkt färg på kartan representerar det låga värdet för kund antalet. Välj en post i tabellen om du vill zooma in i ett land/en region.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Visar det normaliserade användnings diagrammets land på instrument panelen för användning.":::

. Tänk på följande:

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en bestämd plats.
- Termisk karta har ett extra rutnät för att visa information om kund antal, antal order och normaliserad användnings tid på den specifika platsen.
- Du kan söka efter och välja ett land/en region i rutnätet för att zooma till platsen i kartan. Återgå till den ursprungliga vyn genom att välja knappen **Start** på kartan.

### <a name="usage-details-table"></a>Tabellen användnings information

Tabellen **användnings information** visar en numrerad lista över de översta och de 1 000 användnings posterna sorterade efter användning. . Tänk på följande:

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en. TSV eller. CSV-fil om antalet poster är mindre än 1 000.
- Om antalet poster är över 1 000 placeras export data asynkront på en nedladdnings sida som kommer att vara tillgänglig under de närmaste 30 dagarna.
- Använd filter för **detaljerad användnings information** för att visa endast de data som du är intresse rad av. Filtrera data efter land/region, försäljnings kanal, marknads plats licens typ, användnings typ, erbjudande namn, erbjudande typ, kostnads fri utvärderings prenumerations-ID, kund-ID och företags namn.

_**Tabell 1: ord lista med data termer**_

| Kolumn namn i<br>användargränssnitt | Attributnamn | Definition | Kolumn namn i programmering<br>åtkomst rapporter |
| ------------ | ------------- | ------------- | ------------- |
| Prenumerations-ID för Marketplace | Prenumerations-ID för Marketplace | Den unika identifierare som är kopplad till Azure-prenumerationen som kunden använde för att köpa ditt kommersiella Marketplace-erbjudande. ID var tidigare GUID för Azure-prenumerationen. | MarketplaceSubscriptionId |
| MonthStartDate | Månadens start datum | Månadens start datum representerar månad för inköp. | MonthStartDate |
| Erbjudandetyp | Erbjudandetyp | Typ av erbjudande för kommersiella marknads platser. | OfferType |
| Typ av Azure-licens | Typ av Azure-licens | Den typ av licens avtal som kunder använder för att köpa Azure. Kallas även för kanalen. Möjliga värden är:<ui><li>Cloud Solution Provider</li><li>Stora företag</li><li>Företag via åter försäljare</li><li>Betala per användning</li></ul> | AzureLicenseType |
| Marketplace-licens typ | Marketplace-licens typ | Fakturerings metoden för det kommersiella Marketplace-erbjudandet. Möjliga värden är:<ul><li>Debiteras via Azure</li><li>Ta med din egen licens</li><li>Kostnadsfri</li><li>Microsoft as åter försäljare</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Planen som är associerad med erbjudandet. | SKU |
| Kund land | Kund land/-region | Landets/regionens namn som tillhandahålls av kunden. Land/region kan vara annorlunda än landet/regionen i en kunds Azure-prenumeration. | CustomerCountry |
| Är för hands version av SKU | Är för hands version av SKU | Värdet visar om du har taggat SKU som "Preview". Värdet är "Ja" om SKU: n har taggats enligt detta och bara Azure-prenumerationer som har auktoriserats av dig kan distribuera och använda den här avbildningen. Värdet är "nej" om SKU: n inte har identifierats som "för hands version". | IsPreviewSKU |
| SKU-fakturerings typ | SKU-fakturerings typ | Fakturerings typen som är associerad med varje SKU i erbjudandet. Möjliga värden är:<ul><li>Kostnadsfri</li><li>Betald</li></ul> | SKUBillingType |
| IsInternal | Inaktuell | Inaktuell | Inaktuell |
| Storlek på virtuell dator | Storlek på virtuell dator | För VM-baserade erbjudande typer anger den här entiteten storleken på den virtuella dator som är associerad med SKU: n för erbjudandet. | VMSize |
| Moln instans namn | Moln instans namn | Den Microsoft Cloud där en VM-distribution ägdes. | CloudInstanceName |
| ServicePlanName | Inaktuell | Föråldrad (samma definition som SKU) | ServicePlanName |
| Erbjudandets namn | Erbjudandets namn | Namnet på den kommersiella Marketplace-erbjudandet. | OfferName |
| DeploymentMethod | Inaktuell | Inaktuell (samma definition som erbjudande typen) | DeploymentMethod |
| Kund företags namn | Kund företags namn | Företags namnet som tillhandahålls av kunden. Namnet får inte vara detsamma som namnet i en kunds Azure-prenumeration. | CustomerCompanyName |
| Datum för användning | Datum för användning | Datum för generering av användnings händelse för användnings till gångar. | UsageDate |
| IsMultisolution | Är multilösning | Indikerar om erbjudandet är en typ av erbjudande för multilösning. | IsMultisolution |
| Är ny kund | Inaktuell | Inaktuell | IsNewCustomer |
| Kärn storlek | Kärn storlek | Antal kärnor som associeras med det VM-baserade erbjudandet. | CoreSize |
| Användningstyp | Användningstyp | Indikerar om användnings händelsen som är associerad med erbjudandet är något av följande:<ul><li>Normaliserad användning</li><li>Rå användning</li><li>Mätnings användning</li></ul> | UsageType |
| Slutdatum för utvärdering | Slutdatum för utvärdering | Det datum då utvärderings perioden för den här ordern upphör eller har avslut ATS. | TrialEndDate |
| Kund valuta (CC) | Kund valuta | Valutan som används av kunden för den kommersiella Marketplace-transaktionen. | CustomerCurrencyCC |
| Pris (CC) | Pris | Enhets priset för SKU: n som visas i kund valutan. | PriceCC |
| Utbetalnings valuta (PC) | Utbetalnings valuta | Publisher betalas för användnings händelser som är kopplade till till gången i den valuta som har kon figurer ATS av utgivaren. | PayoutCurrencyPC |
| Uppskattat pris (PC) | Beräknat pris | Enhets priset för SKU: n i den valuta som har kon figurer ATS av utgivaren. | EstimatedPricePC |
| Användnings referens | Användnings referens | Ett sammanfogat GUID som används för att ansluta användnings rapporten (i affärs marknads plats analys) med rapporten utbetalnings transaktion. Användnings referensen är kopplad till Ordernr och LineItemId i rapporten utbetalnings transaktion. | UsageReference |
| Användnings enhet | Användnings enhet | Förbruknings enhet som är associerad med SKU: n. | UsageUnit |
| Kund-ID | Kund-ID | Den unika identifierare som tilldelats till en kund. En kund kan ha noll eller flera prenumerationer på Azure Marketplace. | CustomerId |
| ID för fakturerings konto | ID för fakturerings konto | Identifierare för det konto som faktureringen skapas på. Mappa **fakturerings konto-ID: t** till **CustomerID** för att ansluta din rapport över utbetalnings transaktion till kund-, order-och användnings rapporterna. | BillingAccountId |
| Användnings antal | Användnings antal | Det totala antalet användnings enheter som används av till gången som distribueras av kunden.<br>Detta baseras på objekt av typen användning. Om användnings typen till exempel är normaliserad används, är användnings antalet för normaliserad användning. | UsageQuantity |
| NormalizedUsage | Normaliserad användning | Total normaliserad användnings enhet som används av till gången som distribueras av kunden.<br>Normaliserad användnings tid definieras som de användnings timmar som normaliseras för antalet virtuella dator kärnor ([antal VM-kärnor] x [timmar RAW Usage]). Virtuella datorer som anges som "SHAREDCORE" använder 1/6 (eller 0,1666) som [antal VM-kärnor] multiplikatorn. | NormalizedUsage |
| MeteredUsage | Mätnings användning | Det totala antalet användnings enheter som används av de mätare som är konfigurerade med erbjudandet som distribueras av kunden. | MeteredUsage |
| RawUsage | Rå användning | Det totala antalet obearbetade användnings enheter som förbrukas av den till gång som har distribuerats av kunden.<br>Rå användnings timmar definieras som mängden tid som virtuella datorer har körts i användnings enheter. | RawUsage |
| Uppskattad utökad avgift (CC) | Uppskattad utökad avgift i kund valuta | Visar de kostnader som är associerade med användningen. Kolumnen är produkten av pris (CC) och förbruknings antal. | EstimatedExtendedChargeCC |
| Uppskattad utökad avgift (PC) | Uppskattad utökad avgift i utbetalnings valuta | Visar de kostnader som är associerade med användningen. Kolumnen är produkten av uppskattat pris (PC) och förbruknings antal. | EstimatedExtended ChargePC |
|||||

### <a name="usage-page-filters"></a>Användnings sid filter

**Användnings** sidans filter tillämpas på sid nivån order. Du kan välja ett eller flera filter för att återge diagrammet för de kriterier du väljer att visa och de data som du vill se i data fältet för användnings order data, Grid/export. Filter tillämpas på de data som extraheras för månads intervallet som du valde i det övre högra hörnet på användnings sidan.

Widgeten och export rapporten för VM RAW-användning liknar den normaliserade virtuella datorn med följande distinkta:

- Normaliserad användnings tid definieras som de användnings timmar som normaliseras för antalet virtuella dator kärnor ([antal VM-kärnor] x [timmar RAW Usage]). Virtuella datorer som anges som "SHAREDCORE" använder 1/6 (eller 0,1666) som [antal VM-kärnor] multiplikatorn.
- Rå användnings timmar definieras som mängden tid som virtuella datorer har körts i användnings enheter.

### <a name="metered-billing-usage"></a>Avgiftsbelagd fakturerings användning

På fliken **avgiftsbelagd användning** visas användnings information för de erbjudande typer där användningen mäts av dimensionen per mätning. Skriv överanvändning av SaaS visas för närvarande. På fliken visas grafiska representationer av överförbruknings trender för SaaS-avläsning av fakturerings användning:

- **Överförbruknings trend per mätnings dimension**: visar den månatliga överförbruknings trenden för den valda mätar dimensionen för ett erbjudande. X-axeln representerar månaden och Y-axeln representerar överförbruknings antalet. Mått enheten för den anpassade mätaren visas också på Y-axeln.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Visar det normaliserade användnings diagrammet för virtuell dator på instrument panelen för användning.":::

- Överförbruknings **trend per SKU**: representerar trenden för användnings antalet för den valda mätnings dimensionen enligt SKU/abonnemang. De fem främsta planerna med den högsta mängden användning för det valda erbjudandet visas.

- Överförbruknings **trend av kunder**: kund ledaren representerar en staplad lista över kunder med högst användnings timmar och visas på en _ledare_ som rangordnas efter den högsta användningen av den anpassade mätaren. Välj en kund på affärsplanen för att Visa överförbruknings trend för en vald mätnings dimension.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Visar diagram över mätning av användning per kund på instrument panelen för användning.":::

Om du har flera erbjudanden som använder anpassade mätare, visar rapporten förbrukad fakturerings användning information om alla dina erbjudanden, enligt de anpassade mätar dimensionerna.

> [!TIP]
> Du kan använda nedladdnings ikonen i det övre högra hörnet i alla widgetar för att hämta data. Du kan ge feedback om var och en av widgetarna genom att klicka på ikonen "tummer upp" eller "tumme ner".

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i den kommersiella marknads platsen finns i [få åtkomst till analys rapporter för den kommersiella Marketplace i Partner Center](./partner-center-portal/analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md)
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](usage-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./partner-center-portal/downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Azure Marketplace och Microsoft AppSource kan du läsa mer i [klassificering & recensioner Analytics-instrumentpanelen i Partner Center](./partner-center-portal/ratings-reviews.md).
- Vanliga frågor och svar om affärs platser för marknads platser och en omfattande ord lista med data termer finns i [terminologi för kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).
