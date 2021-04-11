---
title: Instrument panel för partner Center-beställning i kommersiell Marketplace-analys | Microsoft AppSource och Azure Marketplace
description: Lär dig hur du får åtkomst till analys rapporter om dina kommersiella Marketplace-erbjudanden i ett grafiskt och nedladdnings Bart format.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ed12e470f3f3d8c1035c1e4e2e0fa7a3b33e2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561389"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>Analys av orderinstrumentpanelen på kommersiell marknadsplats

Den här artikeln innehåller information om instrument panelen för order i Partner Center. Den här instrument panelen visar information om dina beställningar, inklusive tillväxt trender, som presenteras i ett grafiskt och nedladdnings Bart format.

För att komma åt instrument panelen för order i Partner Center väljer du **[analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** beställningar under **kommersiell marknads** plats  >  .

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i terminologi för [kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).

## <a name="orders-dashboard"></a>Orderinstrumentpanel

På instrument panelen för order visas de aktuella orderna för alla SaaS-erbjudanden (Software as a Service). Du kan visa grafiska representationer av följande objekt:

- Order trend
- Beställningar per plats och plats trend
- Beställningar per erbjudande och SKU: er
- Beställningar efter geografi
- Detaljerad order tabell
- Sid filter för order

> [!NOTE]
> Den maximala svars tiden mellan kund förvärv och rapportering i Partner Center är 48 timmar.

## <a name="elements-of-the-orders-dashboard"></a>Element på instrument panelen för order

I följande avsnitt beskrivs hur du använder instrument panelen för order och hur du läser data.

### <a name="month-range"></a>Månads intervall

Du kan hitta en månads intervall markering i det övre högra hörnet på varje sida. Anpassa utdata från sidan **Orders** -diagram genom att välja ett månads intervall baserat på de senaste 6 eller 12 månaderna, eller genom att välja ett anpassat månads intervall med en maximal varaktighet på 12 månader. Standard månads intervallet (beräknings period) är sex månader.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Visar månads filter på instrument panelen för order.":::

> [!NOTE]
> Alla mått i visualiserings-och export rapporterna följer den beräknings period som användaren har valt.

### <a name="orders-trend"></a>Order trend

I det här avsnittet hittar du diagrammet **order** som visar trenden för dina aktiva och annullerade order för den valda beräknings perioden. Mått och tillväxt trender representeras av ett linje diagram och visar värdet för varje månad genom att hovra över linjen i diagrammet. Procent andelen under order måtten i widgeten representerar mängden tillväxt eller neka under den valda beräknings perioden.

Det finns två räknare för order: _aktiv_ och _avbruten_.

- **Aktivt** är lika med antalet beställningar som för närvarande används av kunder under det valda datum intervallet.
- **Avbruten** är lika med antalet order som tidigare har köpts och sedan avbrutits under det valda datum intervallet.

[![Visar widgeten order i order instrument panelen som visar trenden för aktiva och annullerade order.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Beställningar per plats och plats trend

**Order by per plats och platsbaserade** linje diagram representerar måttet och trenden för SaaS per plats och per plats SaaS beställningar som köpts av kunder (det här diagrammet innehåller annullerade order).

[![Visar widgeten order på instrument panelen order som visar order per plats och plats trend.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

SaaS-erbjudanden kan använda en av två pris modeller för varje plan: antingen fast Rate (plats-baserad) eller per användare (plats-baserad).

- **Fast** pris: ge åtkomst till ditt erbjudande med ett enda månatligt eller årligt schablonmässigt pris. Detta kallas ibland för platsbaserade priser.
- **Per användare**: ge åtkomst till ditt erbjudande med ett pris baserat på antalet användare som har åtkomst till erbjudandet eller har platser. Med den här användningen-baserade modellen kan du ange det lägsta och högsta antalet användare som stöds av planen. Du kan skapa flera planer för att konfigurera olika pris punkter baserat på antalet användare. Dessa fält är valfria. Om alternativet inte är markerat kommer antalet användare att tolkas som att de inte har en gräns (minst 1 och högst lika många som din tjänst kan stödja). De här fälten kan redige ras som en del av en uppdatering av planen.
- **Avgiftsbelagd fakturering**: ovanpå fasta priser. Med den här pris sättnings modellen kan du välja att definiera Mät planer som använder API: et för avläsning av program vara för att debitera kunder för användning som inte omfattas av den fasta taxan.

Mer information om plats, plats och avgiftsbelagd fakturering finns i [så här planerar du ett SaaS-erbjudande för den kommersiella Marketplace](plan-saas-offer.md).

### <a name="orders-by-offers-and-skus"></a>Beställningar per erbjudande och SKU: er

Diagrammet beställningar per erbjudanden och SKU visar mått och trender för alla erbjudanden:

- De främsta erbjudandena visas i diagrammet och resten av erbjudandena grupperas som **rest alla**.
- Du kan välja vissa erbjudanden i förklaringen om du bara vill visa det erbjudandet och tillhör ande SKU: er i diagrammet.
- Om du hovrar över en sektor i diagrammet visas antalet order och procent andel av erbjudandet jämfört med det totala antalet beställningar i alla erbjudanden.
- I **trenderna order by-erbjudanden** visas trender i månaden. Kolumnen månad representerar antalet order efter namnet på erbjudandet. Linje diagrammet visar trenden för tillväxt i procent som ritats på en z-axel.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="Visar diagrammet order by-erbjudanden på instrument panelen för order.":::

Du kan välja ett erbjudande och maximalt tre SKU: er för det erbjudandet för att Visa månads månads trend för erbjudandet, SKU: er och platser.

### <a name="orders-by-geography"></a>Beställningar efter geografi

För den valda beräknings perioden visar termisk karta det totala antalet beställningar och tillväxt procenten av nyligen tillagda order mot ett geografiskt område.  Ljuset till mörkt färg på kartan representerar det låga värdet för kund antalet. Välj en post i tabellen om du vill zooma in i ett särskilt land eller en annan region.

[![Illustrerar det geografiska spridnings diagrammet på instrument panelen för order.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

. Tänk på följande:

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en bestämd plats.
- Termisk karta har ett extra rutnät för att visa information om kund antal, antal order och normaliserad användnings tid för den specifika platsen.
- Du kan söka efter och välja ett land/en region i rutnätet för att zooma till platsen i kartan. Återgå till den ursprungliga vyn genom att välja knappen **Start** på kartan.

### <a name="orders-details-table"></a>Tabellen Order Detaljer

Tabellen Order information visar en numrerad lista över de 1 000 främsta order som sorteras efter förvärvs datum.

- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en. CSV eller. TSV-fil om antalet poster är mindre än 1 000.
- Om poster nummer över 1 000 placeras exporterade data asynkront på sidan nedladdningar under de närmaste 30 dagarna.
- Använd filter i tabellen **Orderspecifikationer** om du bara vill visa de data du är intresse rad av. Filtrera efter land/region, typ av Azure-licens, licens typ för kommersiell Marketplace, erbjudande typ, order status, kostnads fria historik, prenumerations-ID för handels-Marketplace, kund-ID och företags namn.
- När en order har köpts av en skyddad kund, maskeras information i **detaljerade data om ordern** (* * * * * * * * * * * *).

***Tabell 1: ord lista med data termer***

| Kolumn namn i<br>användargränssnitt | Attributnamn | Definition | Kolumn namn i programmering<br>åtkomst rapporter |
| ------------ | ------------- | ------------- | ------------- |
| Prenumerations-ID för Marketplace | Prenumerations-ID för Marketplace | Den unika identifierare som är kopplad till Azure-prenumerationen som kunden använde för att köpa ditt kommersiella Marketplace-erbjudande. För infrastruktur erbjudanden är det här kundens GUID för Azure-prenumerationen. För SaaS-erbjudanden visas detta som nollor eftersom SaaS-inköp inte kräver en Azure-prenumeration. | Prenumerations-ID för Marketplace |
| MonthStartDate | Månadens start datum | Månadens start datum representerar inköps månad. Formatet är åååå-mm-dd. | MonthStartDate |
| Erbjudandetyp | Erbjudandetyp | Typ av erbjudande för kommersiella marknads platser. | OfferType |
| Typ av Azure-licens | Typ av Azure-licens | Den typ av licens avtal som kunder använder för att köpa Azure. Kallas även för kanal. Möjliga värden är:<ul><li>Cloud Solution Provider</li><li>Stora företag</li><li>Företag via åter försäljare</li><li>Betala per användning</li></ul> | AzureLicenseType |
| Marketplace-licens typ | Marketplace-licens typ | Fakturerings metoden för det kommersiella Marketplace-erbjudandet. De olika värdena är:<ul><li>Debiteras via Azure</li><li>Ta med din egen licens</li><li>Kostnadsfri</li><li>Microsoft as åter försäljare</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Planen som är associerad med erbjudandet | SKU |
| Kund land | Kund land/-region | Landets/regionens namn som tillhandahålls av kunden. Land/region kan vara annorlunda än landet/regionen i en kunds Azure-prenumeration. | CustomerCountry |
| Är för hands version av SKU | Är för hands version av SKU | Värdet meddelar dig om du har taggat SKU som "Preview". Värdet är "Ja" om SKU: n har taggats enligt detta och bara Azure-prenumerationer som har auktoriserats av dig kan distribuera och använda den här avbildningen. Värdet är "nej" om SKU: n inte har identifierats som "för hands version". | IsPreviewSKU |
| Order-ID | Order-ID | Den unika identifieraren för kund ordern för din kommersiella Marketplace-tjänst. Användnings erbjudanden för virtuella datorer är inte kopplade till någon order. | OrderId |
| Orderkvantitet | Orderkvantitet | Antal till gångar som är kopplade till order-ID för aktiva beställningar | OrderQuantity |
| Moln instans namn | Moln instans namn | Den Microsoft Cloud där en VM-distribution ägdes. | CloudInstanceName |
| Är ny kund | Är ny kund | Värdet identifierar om en ny kund har köpt en eller flera av dina erbjudanden för första gången. Värdet blir "Ja" om det är inom samma kalender månad för "inköps datum". Värdet blir "nej" om kunden har köpt något av dina erbjudanden före den kalender månad som rapporter ATS. | IsNewCustomer |
| Orderstatus | Orderstatus | Status för en kommersiell Marketplace-order vid den tidpunkt då data senast uppdaterades. | OrderStatus |
| Annullera datum för beställning | Annullera datum för beställning | Det datum då den kommersiella Marketplace-ordern annullerades. | OrderCancelDate |
| Kund företags namn | Kund företags namn | Företags namnet som tillhandahålls av kunden. Namnet får inte vara detsamma som staden i en kunds Azure-prenumeration. | CustomerCompanyName |
| Inköps datum för beställning | Inköps datum för beställning | Det datum då den kommersiella Marketplace-ordern skapades. Formatet är åååå-mm-dd. | OrderPurchaseDate |
| Erbjudandets namn | Erbjudandets namn | Namnet på den kommersiella Marketplace-erbjudandet. | OfferName |
| Slutdatum för utvärdering | Slutdatum för utvärdering | Det datum då utvärderings perioden för den här ordern upphör eller har avslut ATS. | TrialEndDate |
| Kund-ID | Kund-ID | Den unika identifierare som tilldelats till en kund. En kund kan ha noll eller flera prenumerationer på Azure Marketplace. | CustomerId |
| ID för fakturerings konto | ID för fakturerings konto | Identifierare för det konto som faktureringen skapas på. Mappa **fakturerings konto-ID: t** till **CustomerID** för att ansluta din rapport över utbetalnings transaktion till kund-, order-och användnings rapporterna. | BillingAccountId |
| AssetCount | Antal till gångar | Antalet till gångar som är kopplade till order-ID: t. | Inaktuell |
|||||

### <a name="orders-page-filters"></a>Sid filter för order

Sidan **order** sid filter används på sid nivån order. Du kan välja ett eller flera filter för att återge diagrammet för de kriterier du väljer att visa och de data som du vill se i "detaljerade order data"-rutnät/-export. Filter tillämpas på de data som extraheras för månads intervallet som du har valt i det övre högra hörnet på sidan order.

> [!TIP]
> Du kan använda nedladdnings ikonen i det övre högra hörnet i alla widgetar för att hämta data. Du kan ge feedback om var och en av widgetarna genom att klicka på ikonen "tummer upp" eller "tumme ner".

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i den kommersiella marknads platsen finns i [få åtkomst till analys rapporter för den kommersiella Marketplace i Partner Center](./partner-center-portal/analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./partner-center-portal/downloads-dashboard.md).
- Om du vill se en samlad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificeringar & recensioner Analytics-instrumentpanelen i Partner Center](./partner-center-portal/ratings-reviews.md).
- Vanliga frågor och svar om affärs platser för marknads platser och en omfattande ord lista med data termer finns i [terminologi för kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).