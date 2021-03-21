---
title: Instrument panelen för kunder i Microsofts marknads plats analys på Partner Center, Azure Marketplace och Microsoft AppSource
description: Lär dig hur du kommer åt information om dina kunder, inklusive tillväxt trender, med hjälp av instrument panelen för kunder i den kommersiella Marketplace-analysen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c5c50787ef3e287d164e051ece26da4e83199d47
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555643"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Kund instrument panel på kommersiell Marketplace-analys

Den här artikeln innehåller information om instrument panelen för kunder i Partner Center. Den här instrument panelen visar information om dina kunder, inklusive tillväxt trender, som presenteras i ett grafiskt och nedladdnings Bart format.

För att komma åt instrument panelen kunder i Partner Center väljer du **[analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** kunder under **kommersiell marknads** plats  >  .

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i terminologi för [kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).

## <a name="customers-dashboard"></a>Instrumentpanelen Kunder

På instrument panelen kunder visas data för kunder som har köpt dina erbjudanden. Du kan visa grafiska representationer av följande objekt:

- Trender för aktiva och uppvarade kunder
- Tillväxt trend för kunder, inklusive befintliga, nya och omsättnings bara kunder
- Kunder per order och användning
- Kundernas percentil 
- Kund typ efter order och användning
- Kunder efter geografi
- Tabellen Kund information
- Kunders sid filter

> [!NOTE]
> Den maximala svars tiden mellan kund förvärv och rapportering i Partner Center är 48 timmar.

## <a name="elements-of-the-customers-dashboard"></a>Element på instrument panelen för kunder

I följande avsnitt beskrivs hur du använder instrument panelen för kunder och hur du läser data.

### <a name="month-range"></a>Månads intervall

Du kan hitta en månads intervall markering i det övre högra hörnet på varje sida. Anpassa utdata från sidan **kunders** diagram genom att välja ett månads intervall baserat på de senaste 6 eller 12 månaderna, eller genom att välja ett anpassat månads intervall med en maximal varaktighet på 12 månader. Standard månads intervallet (beräknings period) är sex månader.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Visar månads filtren på sidan kunder.":::

> [!NOTE]
> Alla mått i visualiserings-och export rapporterna följer den beräknings period som användaren har valt.

### <a name="active-and-churned-customers-trend"></a>Trender för aktiva och uppvarade kunder

I det här avsnittet hittar du dina kunders tillväxt trender för den valda beräknings perioden. Mått och tillväxt trender representeras av ett linje diagram och visar värdet för varje månad genom att hovra över linjen i diagrammet. Procent andelen av **aktiva kunder** i widgeten representerar mängden tillväxt under den valda beräknings perioden. Följande skärm bild visar till exempel en tillväxt på 0,92% för den valda beräknings perioden.

[![Visar widgeten kunder på sidan kunder.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Det finns tre _kund typer_: nya, befintliga och uppdelade.

- En ny kund har skaffat ett eller flera av dina erbjudanden för första gången inom den valda månaden.
- En befintlig kund har köpt ett eller flera av dina erbjudanden före den månad som valts.
- En upprättad kund har avbrutit alla erbjudanden som tidigare har köpts. Omsättnings bara kunder representeras på den negativa axeln i trend-widgeten.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Tillväxt trend för kunder, inklusive befintliga, nya och omsättnings bara kunder

I det här avsnittet hittar du trender och antal kunder, inklusive nya, befintliga och uppdelade, med en månads tillväxt trend.

- Linje diagrammet representerar de övergripande procent andelen av kundens tillväxt.
- Kolumnen månad representerar antalet kunder som är staplade efter nya, befintliga och uppdelade kunder.
- Antalet uppräknade kunder visas i X-axelns negativa riktning.
- Du kan välja särskilda förklarings objekt om du vill visa mer detaljerade vyer. Välj till exempel nya kunder från förklaringen för att endast visa nya kunder.
- Om du hovrar över en kolumn i diagrammet visas bara information om den månaden.

[![Visar widgeten Customer trend på sidan kunder.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Kunder per order/användning

Diagrammet **kunder per order/användning** har tre flikar: order, normaliserad användning och rå data användning. Välj fliken **beställningar** för att visa order Detaljer.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Visar fliken beställningar för widgeten kunder per order och användning på sidan kunder.":::

. Tänk på följande:

- Brädet presenterar information om kunderna rankade efter antal beställningar. När du har valt en kund visas kund informationen i avsnitten närliggande information, beställningar per SKU och SKU per platser.
- Kund profil informationen visas i det här utrymmet när utgivare är inloggade med en ägar roll. Om utgivare är inloggade med en deltagar roll kommer informationen i det här avsnittet inte att vara tillgänglig.
- I diagrammet **beställningar per SKU** ring visas en uppdelning av beställningar som har köpts för planer. De fem främsta planerna med högst order antal visas, medan resten av orderna grupperas under **rest all**.
- I ring diagrammet för **SKU: er efter platser** visas en uppdelning av platser som har beställts för planer. De fem främsta planerna med de högsta platserna visas, medan resten av orderna grupperas under **rest all**.

Du kan också välja fliken **normaliserad användning** eller **rå användning** om du vill visa användnings information.

- Brädet presenterar information om kunderna rankade efter antal användnings timmar. När du har valt en kund visas information om kunden i avsnittet om den angränsande "information", "normaliserad användning av erbjudanden" och "normaliserad användning per virtuell dator (VM) storlek (er)".
- Kund profil informationen visas i det här utrymmet när utgivare är inloggad med en ägar roll. Om utgivare är inloggade med en deltagar roll kommer informationen i det här avsnittet inte att vara tillgänglig.
- I den **normaliserade användningen efter** ring diagram visas en nedbrytning av användningen som används av erbjudandena. De fem främsta planerna med högsta användnings antal visas, medan resten av erbjudandena grupperas under **rest all**.
- Ring diagrammet **normaliserad användning efter VM-storlek (er)** visar fördelningen av användning som används av olika VM-storlekar. De fem största virtuella dator storlekarna med den högsta normaliserade användningen visas, medan resten av användningen grupperas under **rest all**.

### <a name="top-customers-percentile"></a>Vanliga kunders percentil

**Percentils diagrammet främsta kunder** har tre flikar, "order", "normaliserad användning" och "rå användning". Den _främsta kund percentilen_ visas längs x-axeln, utifrån antalet order. Y-axeln visar kundens order antal. Den sekundära y-axeln (linje diagram) visar den ackumulerade procent andelen av det totala antalet beställningar. Du kan visa information genom att hovra över punkter längs linje diagrammet.

[![Visar fliken order i widgeten bästa kund percentil på sidan kunder.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Kund typ efter order och användning

I diagrammet **order/användning per kund typ** visas antalet order, normaliserad användning och rå data timmar som delas mellan nya kunder och befintliga kunder. baserat på valet av order, normaliserad och rå användning i diagrammet.

Det här diagrammet visar följande:

- En ny kund har köpt en eller flera av dina erbjudanden eller rapporterad användning för första gången inom samma kalender månad (y-axeln).
- En befintlig kund har tidigare förvärvat ett erbjudande från dig eller rapporterat användning före den kalender månad som rapporter ATS (på y-axeln).

[![Visar fliken beställningar på widgeten order efter typ av kund på sidan kunder.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Kunder efter geografi

För den valda beräknings perioden visar termisk karta det totala antalet kunder och procent andelen nyligen tillagda kunder mot geografisk dimension. Ljuset till mörkt färg på kartan representerar det låga värdet för kund antalet. Välj en post i tabellen om du vill zooma in i ett land eller en region.

[![Visar fliken order i widgeten order by geography på sidan kunder.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

. Tänk på följande:

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en bestämd plats.
- Termisk karta har ett kompletterande rutnät för att visa information om antalet kunder, antalet order, normaliserad användnings tid på den specifika platsen.
- Du kan söka efter och välja ett land/en region i rutnätet för att zooma till platsen i kartan. Återgå till den ursprungliga vyn genom att välja knappen **Start** på kartan.

### <a name="customer-details-table"></a>Tabellen Kund information

Tabellen **kund information** visar en numrerad lista över de 1 000 främsta kunderna sorterat efter det datum då de först köpte ett av dina erbjudanden. Du kan expandera ett avsnitt genom att välja expansions ikonen i menyfliksområdet information.

. Tänk på följande:

- Kundens personliga information kommer bara att vara tillgänglig om kunden har gett sitt medgivande. Du kan bara se den här informationen om du har loggat in med en ägar roll behörighets nivå.
- Varje kolumn i rutnätet är sorterbar.
- Data kan extraheras till en. CSV eller. TSV-fil om antalet poster är mindre än 1 000.
- Om post nummer är mer än 1 000 kommer exporterade data att placeras asynkront på en nedladdnings sida under de närmaste 30 dagarna.
- Använd filter i tabellen om du bara vill visa de data som du är intresse rad av. Filtrera data efter företags namn, kund-ID, marknads plats prenumerations-ID, typ av Azure-licens, förvärvs datum, datum förlorat, kund-e-post, kund land/region/delstat/ort/post, kund språk och så vidare.
- När ett erbjudande köps av en skyddad kund kommer information i **kundens detaljerade data** att maskeras (* * * * * * * *).
- Kund dimensions information som företagets namn, kund namn och kund-e-post finns på en organisations-ID-nivå, inte på Azure Marketplace eller Microsoft AppSource transaktions nivå.

_**Tabell 1: ord lista med data termer**_

| Kolumn namn i<br>användargränssnitt | Attributnamn | Definition | Kolumn namn i programmering<br>åtkomst rapporter |
| ------------ | ------------- | ------------- | ------------- |
| Prenumerations-ID för Marketplace | Prenumerations-ID för Marketplace | Den unika identifierare som är kopplad till Azure-prenumerationen som kunden använde för att köpa ditt kommersiella Marketplace-erbjudande. För infrastruktur erbjudanden är det här kundens GUID för Azure-prenumerationen. För SaaS-erbjudanden visas detta som nollor eftersom SaaS-inköp inte kräver en Azure-prenumeration. | MarketplaceSubscriptionId |
| DateAcquired | Inköps datum | Det första datumet då kunden köpte ett erbjudande som du har publicerat. | DateAcquired |
| DateLost | Datum förlorat | Det senaste datumet då kunden avbröt den sista av alla tidigare inköpta erbjudanden. | DateLost |
| Leverantörens namn | Leverantörens namn | Namnet på leverantören som är involverad i relationen mellan Microsoft och kunden. Om kunden är ett företag genom åter försäljaren blir detta åter försäljaren. Om en moln lösnings leverantör (CSP) är involverad är detta CSP. | ProviderName |
| Providerns e-postadress | Providerns e-postadress | E-postadressen till leverantören som är involverad i relationen mellan Microsoft och kunden. Om kunden är ett företag genom åter försäljaren blir detta åter försäljaren. Om en moln lösnings leverantör (CSP) är involverad är detta CSP. | ProviderEmail |
| FirstName | Förnamn för kund | Förnamnet som tillhandahålls av kunden. Namnet får inte vara detsamma som det namn som anges i en kunds Azure-prenumeration. | FirstName |
| LastName | Kundens efter namn | Efter namnet som tillhandahålls av kunden. Namnet får inte vara detsamma som det namn som anges i en kunds Azure-prenumeration. | LastName |
| E-post | Kund-e-post | E-postadressen som tillhandahålls av slut kunden. E-postadressen kan skilja sig från e-postadressen i en kunds Azure-prenumeration. | E-post |
| Kund företags namn | Kund företags namn | Företags namnet som tillhandahålls av kunden. Namnet får inte vara detsamma som staden i en kunds Azure-prenumeration. | CustomerCompany namn |
| CustomerCity | Kund ort | Orts namnet som tillhandahålls av kunden. Staden kan skilja sig från staden i en kunds Azure-prenumeration. | CustomerCity |
| Kund post nummer | Kund post nummer | Post numret från kunden. Koden får inte vara samma som post numret i en kunds Azure-prenumeration. | CustomerPostal-kod |
| CustomerCommunicationCulture | Kundens kommunikations språk | Det språk som kunden föredrar för kommunikation. | CustomerCommunicationCulture |
| CustomerCountryRegion | Kund land/-region | Landets/regionens namn som tillhandahålls av kunden. Land/region kan vara annorlunda än landet/regionen i en kunds Azure-prenumeration. | CustomerCountryRegion |
| AzureLicenseType | Typ av Azure-licens | Den typ av licens avtal som kunder använder för att köpa Azure. Kallas även för _kanalen_. Möjliga värden är:<ul><li>Cloud Solution Provider</li><li>Stora företag</li><li>Företag via åter försäljare</li><li>Betala per användning</li></ul> | AzureLicenseType |
| PromotionalCustomers | Är kampanj kontakt deltagande | Värdet gör att du vet om kunden proaktivt har valt reklam kontakt från utgivare. För tillfället visar vi inte alternativet för kunder, så vi har angett "nej" på tavlan. När den här funktionen har distribuerats kommer vi att börja uppdatera enligt detta. | PromotionalCustomers |
| CustomerState | Kund tillstånd | Det hemland som tillhandahålls av kunden. Tillstånd kan skilja sig från det tillstånd som tillhandahölls i en kunds Azure-prenumeration. | CustomerState |
| CommerceRootCustomer | Commerces rot kund | Ett fakturerings konto-ID kan associeras med flera kund-ID: n.<br>En kombination av ett fakturerings konto-ID och ett kund-ID kan associeras med flera prenumerationer på kommersiella marknads platser.<br>Commerces rot kund anger namnet på prenumerationens kund. | CommerceRootCustomer |
| Kund-ID | Kund-ID | Den unika identifierare som tilldelats till en kund. En kund kan ha noll eller flera prenumerationer på Azure Marketplace. | CustomerId |
| ID för fakturerings konto | ID för fakturerings konto | Identifierare för det konto som faktureringen skapas på. Mappa **fakturerings konto-ID: t** till **CustomerID** för att ansluta din rapport över utbetalnings transaktion till kund-, order-och användnings rapporterna. | BillingAccountId |
|||||

### <a name="customers-page-filters"></a>Kunders sid filter

Sidan kunders filter tillämpas på sidan kunders sid nivå. Du kan välja flera filter för att återge diagrammet för de kriterier du väljer att visa och de data som du vill se i rutnätet/exporten "detaljerade order data". Filter tillämpas på de data som extraheras för månads intervallet som du valde i det övre högra hörnet på sidan kunder.

> [!TIP]
> Du kan använda nedladdnings ikonen i det övre högra hörnet i alla widgetar för att hämta data. Du kan ge feedback om var och en av widgetarna genom att klicka på ikonen "tummer upp" eller "tumme ner".

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i den kommersiella marknads platsen finns i [få åtkomst till analys rapporter för den kommersiella Marketplace i Partner Center](./partner-center-portal/analytics.md).
- För grafer, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för ditt erbjudande, se [översikts instrument panel i](./summary-dashboard.md)marknads plats analys.
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./partner-center-portal/downloads-dashboard.md).
- Om du vill se en sammanställd vy över kundfeedback för erbjudanden på Azure Marketplace och Microsoft AppSource kan du läsa mer i [klassificering & recensioner Analytics-instrumentpanelen i Partner Center](./partner-center-portal/ratings-reviews.md).
- Vanliga frågor och svar om affärs platser för marknads platser och en omfattande ord lista med data termer finns i [terminologi för kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).
