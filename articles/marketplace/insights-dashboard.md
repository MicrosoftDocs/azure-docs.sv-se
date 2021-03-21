---
title: Marketplace Insights-instrumentpanelen i Commersial Marketplace-analys
description: Få åtkomst till en sammanfattning av Marketplace Web Analytics i Partner Center, som gör att du kan mäta kund engagemang i Microsoft AppSource och Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: a8a53a54d30df9a2f7079d705f754e554328c3f7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553943"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Marketplace Insights-instrumentpanelen i Commersial Marketplace-analys

Den här artikeln innehåller information om instrument panelen för marknads insikter i Partner Center. På den här instrument panelen visas en sammanfattning av den kommersiella Marketplace Web Analytics som gör det möjligt för utgivare att mäta kund engagemang för sina respektive produkt informations sidor som anges i de kommersiella onlinebutiker online butiker: Microsoft AppSource och Azure Marketplace.

Om du vill komma åt **Marketplace Insights** -instrumentpanelen i Partner Center väljer du **[analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **marknads insikter** under kommersiell marknads plats.

Detaljerade definitioner av analys terminologi finns i terminologi för [kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).

## <a name="marketplace-insights-dashboard"></a>Instrumentpanelen Marknadsplatsinsikter

Marketplace insikter-instrument panelen innehåller en översikt över Azure Marketplace-och AppSource-erbjudanden "affärs prestanda. Den här instrument panelen innehåller en omfattande översikt över följande:

- Trend för sid besök
- Anrop till åtgärds trend
- Sid besök och anrop till åtgärder mot erbjudanden, referens domäner och kampanj-ID: n
- Marknads insikter efter geografi
- Informations tabell för Marketplace

Marketplace insikter-instrument panelen tillhandahåller klick Ströms-data som inte bör korreleras med leads som genereras i mål slut punkten för målet.

> [!NOTE]
> Den maximala svars tiden mellan användare som besöker erbjudanden på Azure Marketplace eller AppSource och rapportering i Partner Center är 48 timmar.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Element på instrument panelen för Marketplace-insikter

Marketplace-instrumentpanelen visar webbtelemetri-information för Azure Marketplace och AppSource på två separata flikar. I följande avsnitt beskrivs hur du använder instrument panelen för Marketplace-insikter och hur du läser data.

### <a name="month-range"></a>Månads intervall

Du kan hitta en månads intervall markering i det övre högra hörnet på varje sida. Anpassa utdata från sidan med **marknads insikter** genom att välja ett månads intervall baserat på de senaste 6 eller 12 månaderna, eller genom att välja ett anpassat månads intervall med en maximal varaktighet på 12 månader. Standard månads intervallet (beräknings period) är sex månader.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Visar månads filtren på instrument panelen för marknads insikter.":::

> [!NOTE]
> Alla mått i visualiserings-och export rapporterna följer den beräknings period som användaren har valt.

### <a name="page-visits-trends"></a>Trender för sid besök

I diagrammet Marketplace Insights- **besökare** visas ett antal _sid besök_ och _unika besökare_ för den valda beräknings perioden.

**Sid besök**: det här antalet representerar antalet olika användarsessioner på sidan erbjudande lista (produkt informations sida) för en vald beräknings period. Rött och grönt procentuella indikatorer representerar tillväxten i procent av sid besök. Trend diagrammet visar antalet sid besök per månad.

**Unika besökare**: det här antalet representerar de distinkta besökarnas antalet under den valda beräknings perioden för erbjudandet i Azure Marketplace och AppSource. En besökare som har besökt en eller flera produkt informations sidor räknas som en unik besökare.

[![Visar ett diagram över besökare på instrument panelen för Marketplace-insikter.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Anrop till åtgärds trend

Det här talet representerar knappen antal **anrop till åtgärds** klickningar som har slutförts på sidan erbjudande lista (produkt informations sida). _Anrop till åtgärden_ räknas när användarna väljer knapparna **Hämta nu**, **kostnads fri utvärdering**, **kontakta mig** eller **testa enhet** .

[![Illustrerar anropet till åtgärds diagrammet på instrument panelen för Marketplace-insikter.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Sid besök och anrop till åtgärder mot erbjudanden, referens domäner och kampanj-ID: n

**Referens domäner**: om du väljer en angiven hänvisnings domän visas den månatliga trenden för sid besök och anrop till åtgärden i diagrammet till höger.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Visar hänvisnings domän diagrammet på instrument panelen för Marketplace-insikter.":::

**Erbjudanden**: Välj ett särskilt erbjudande för att se den månatliga trenden för sid besök och anrop till åtgärd i diagrammet till höger.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Visar diagrammet för erbjuden alias på instrument panelen för Marketplace-insikter.":::

**Kampanj-ID**: genom att välja ett speciellt kampanj-ID bör du kunna förstå kampanjens framgång. För varje kampanj bör du kunna se den månatliga trenden för sid besök och anrop till åtgärden i diagrammet till höger.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Visar kampanj diagrammet på instrument panelen för Marketplace-insikter.":::

### <a name="marketplace-insights-by-geography"></a>Marknads insikter efter geografi

För den valda beräknings perioden visar termisk karta antalet sid besök, unika besökare och anrop till åtgärder (centrum). Ljuset till mörkt färg på kartan representerar det låga till höga värdet för de unika besökarna. Välj en post i tabellen om du vill zooma in i ett land/en region.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Illustrerar det geografiska spridnings diagrammet på instrument panelen för Marketplace-insikter.":::

. Tänk på följande:

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en bestämd plats.
- Termisk karta har ett extra rutnät för att visa information om kund antal, antal order och normaliserad användnings tid på den specifika platsen.
- Du kan söka efter och välja ett land/en region i rutnätet för att zooma till platsen i kartan. Återgå till den ursprungliga vyn genom att välja knappen **Start** på kartan.

### <a name="marketplace-insights-details-table"></a>Informations tabell för Marketplace

Den här tabellen innehåller en listvy över sid besöken och anrop till åtgärden för de valda erbjudandena sidor sorterade efter datum.

- Data kan extraheras till en. TSV eller. CSV-fil om antalet poster är mindre än 1 000.
- Om antalet poster är över 1 000 placeras exporterade data asynkront på sidan nedladdningar under de närmaste 30 dagarna.
- Filtrera data efter namn och kampanj namn för att visa de data du är intresse rad av.

> [!TIP]
> Du kan använda nedladdnings ikonen i det övre högra hörnet i alla widgetar för att hämta data. Du kan ge feedback om var och en av widgetarna genom att klicka på ikonen "tummer upp" eller "tumme ner".

| Kolumn namn i<br>användargränssnitt | Attributnamn | Definition | Kolumn namn i programmering<br>åtkomst rapporter |
| ------------ | ------------- | ------------- | ------------- |
| Datum | Datum för besök | Datumet då sidan besöks och/eller centrum klickar på händelse generering på erbjudandets sida i Azure Marketplace och/eller AppSource. | Datum |
| Erbjudandets namn | Erbjudandets namn | Namnet på den kommersiella Marketplace-erbjudandet. | OfferName |
| Referens domän | Referens domän | Namnet på den referens domän från vilken besöks sidan gjordes. Om det inte finns några referens domäner som har registrerats för sidan besök är motsvarande post "referens domän saknas". |  ReferralDomain |
| Landsnamn | Landsnamn | Namnet på det land från vilket sidan besök har inträffat. | CountryName |
| Sid besök | Sid besök | Antalet sid besök som är kopplade till erbjudande namnet för ett visst datum. | PageVisits |
| Hämta nu | Hämta nu | Antalet klick till CENTRUMet "Hämta nu" på erbjudandets sida för ett visst datum. | GetItNow |
| Kontakta mig | Kontakta mig | Antalet klick till CENTRUMet "kontakta mig" på erbjudandets sida för ett visst datum. | ContactMe |
| Test Drive | Test Drive | Antalet klick till CENTRUMet "Test Drive" på erbjudandets sida för ett visst datum. | TestDrive |
| Kostnadsfri utvärdering | Kostnadsfri utvärdering | Antalet klick till CENTRUMet "kostnads fri utvärdering" på erbjudandets sida för ett visst datum. | Fri utvärdering |
|||||

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i den kommersiella marknads platsen finns i [få åtkomst till analys rapporter för den kommersiella Marketplace i Partner Center](./partner-center-portal/analytics.md).
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](./orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](./usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxt trender, finns [på kund instrument panel i affärs Marketplace-analys](./customer-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./partner-center-portal/downloads-dashboard.md).
- Om du vill se en samlad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificeringar & recensioner Analytics-instrumentpanelen i Partner Center](./partner-center-portal/ratings-reviews.md).
- Vanliga frågor och svar om affärs platser för marknads platser och en omfattande ord lista med data termer finns i [terminologi för kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).