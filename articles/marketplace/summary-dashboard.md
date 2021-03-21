---
title: Sammanfattnings instrument panel för partner Center Analytics på kommersiell marknads plats
description: Lär dig hur du kommer åt diagram, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet från sammanfattnings instrument panelen i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462985"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Instrumentpanelen för sammanfattning i Commercial Marketplace-analys

Den här artikeln innehåller information om sammanfattnings instrument panelen i Partner Center. Den här instrument panelen visar diagram, trender och värden för sammanställda data som sammanfattar Marketplace-aktivitet för dina erbjudanden.

För att få åtkomst till sammanfattnings instrument panelen i Partner Center väljer du **[analysera](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** Sammanfattning under **kommersiell marknads** plats  >  .

>[!NOTE]
> Detaljerade definitioner av analys terminologi finns i terminologi för [kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).

## <a name="summary-dashboard"></a>Instrumentpanel för sammanfattningar

Översikts instrument panelen innehåller en översikt över Azure Marketplace och Microsoft AppSource erbjuder företags prestanda. På instrument panelen finns en bred översikt över följande:

- Kunders order
- Kunder
- Kunders användning av erbjudandena
- Kundernas sid besök på Azure Marketplace och AppSource

## <a name="elements-of-the-summary-dashboard"></a>Element i sammanfattnings instrument panelen

I följande avsnitt beskrivs hur du använder sammanfattnings instrument panelen och hur du läser data.

### <a name="month-range"></a>Månads intervall

Du kan hitta en månads intervall markering i det övre högra hörnet på varje sida. Anpassa utdata för **sammanfattnings** sidans diagram genom att välja ett månads intervall baserat på de senaste 3, 6 eller 12 månaderna, eller genom att välja ett anpassat månads intervall med en maximal varaktighet på 12 månader. Standard månads intervallet (beräknings period) är sex månader.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Visar alternativen för månads intervall på sammanfattnings instrument panelen.":::

> [!NOTE]
> Alla mått i visualiserings-och export rapporterna följer den beräknings period som användaren har valt.

### <a name="orders-widget"></a>Widgeten order

I widgeten order på Summary * *-instrument panelen visas aktuella beställningar för alla dina Transact-baserade erbjudanden. Widgeten order visar ett antal och en trend för alla inköpta order (exklusive annullerade order) för den valda beräknings perioden. Procent andelens värde **beställningar** representerar tillväxten under den valda beräknings perioden.

[![Visar widgeten order i sammanfattnings instrument panelen.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Du kan också gå till rapporten order genom att välja länken **order instrument panel** i det nedre vänstra hörnet i widgeten.

### <a name="customers-widget"></a>Customer-widget

Widgeten **kunder** i **översikts** instrument panelen visar det totala antalet kunder som har köpt dina erbjudanden för den valda beräknings perioden. I widgeten kunder visas ett antal och en trend för det totala antalet aktiva (inklusive nya och befintliga) kunder (exklusive omsättnings bara kunder) för den valda beräknings perioden. Procent andelen för **kunderna** representerar tillväxten under den valda beräknings perioden.

[![Visar widgeten kunder på sammanfattnings instrument panelen.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

Du kan också gå till rapporten detaljerade kunder genom att välja länken **kund instrument panel** i det nedre vänstra hörnet i widgeten.

### <a name="usage-widget"></a>Användnings widget

**Använd** widgeten för **sammanfattnings** instrument panelen representerar de totala normaliserade och råa användnings tiderna för alla erbjudanden för virtuella Azure-datorer (VM). I widgeten användning visas antalet och trenden för den totala förbruknings tiden för den valda beräknings perioden.

I tabellen användnings översikt visas kund förbruknings timmar för alla erbjudanden som de har köpt.

- Normaliserad användnings tid definieras som de användnings timmar som normaliseras för antalet virtuella dator kärnor ([antal VM-kärnor] x [timmar RAW Usage]). Virtuella datorer som anges som "SHAREDCORE" använder 1/6 (eller 0,1666) som [antal VM-kärnor] multiplikatorn.
- Rå användnings timmar definieras som mängden tid som virtuella datorer har körts i timmar.

Procent andelen av den totala förbrukningen timmar representerar tillväxten i användnings timmar under den valda beräknings perioden.

[![Visar widgeten användning på sammanfattnings instrument panelen.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

Du kan också gå till användnings rapporten genom att välja länken **användnings panel** i det nedre vänstra hörnet i widgeten.

### <a name="marketplace-insights"></a>Marknads insikter

Marknads insikter visar antalet användare som har besökt dina erbjudandens sidor i Azure Marketplace och AppSource. **Antal sid besök** visar en översikt över den kommersiella Marketplace-webbanalysen som gör det möjligt för utgivare att mäta kund engagemang för sina respektive produkt informations sidor som visas på de kommersiella Marketplace-onlinebutiker: Microsoft AppSource och Azure Marketplace. Den här widgeten visar ett antal och trenden för totala sid besök under den valda beräknings perioden.

[![Visar widgeten sid för ande räknare på sammanfattnings instrument panelen.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

Du kan också gå till Marketplace Insights-rapporten genom att välja länken **Marketplace Insights-instrumentpanel** i det nedre vänstra hörnet av widgeten.

### <a name="geographical-spread"></a>Geografiskt uppslag

För den valda beräknings perioden visar termisk karta det totala antalet kunder, beställningar och normaliserade användnings timmar mot geografi dimensionen.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Visar widgeten för att sprida länder på sammanfattnings instrument panelen.":::

. Tänk på följande:

- Du kan flytta kartan för att visa den exakta platsen.
- Du kan zooma in på en bestämd plats.
- Termisk karta har ett extra rutnät för att visa information om kund antal, antal order och normaliserad användnings tid för den specifika platsen.
- Du kan söka efter och välja ett land/en region i rutnätet för att zooma till platsen i kartan. Återgå till den ursprungliga vyn genom att välja knappen **Start** på kartan.

> [!TIP]
> Du kan använda nedladdnings ikonen i det övre högra hörnet i alla widgetar för att hämta data. Du kan ge feedback om var och en av widgetarna genom att välja ikonen "tummer upp" eller "tumme ner".

## <a name="next-steps"></a>Nästa steg

- En översikt över analys rapporter som är tillgängliga i den kommersiella marknads platsen finns i [få åtkomst till analys rapporter för den kommersiella Marketplace i Partner Center](./partner-center-portal/analytics.md).
- Information om dina beställningar i ett grafiskt och nedladdnings Bart format finns i [order instrument panel i kommersiell Marketplace-analys](orders-dashboard.md).
- För virtuell dator (VM) erbjuder vi användnings-och mätnings mått i [användnings instrument panelen i den kommersiella Marketplace-analysen](usage-dashboard.md).
- Detaljerad information om dina kunder, inklusive tillväxt trender, finns [på kund instrument panel i affärs Marketplace-analys](customer-dashboard.md).
- En lista över dina nedladdnings begär Anden under de senaste 30 dagarna finns i [Hämta instrument panel i kommersiell Marketplace-analys](./partner-center-portal/downloads-dashboard.md).
- Om du vill se en samlad vy över kundfeedback för erbjudanden på Azure Marketplace och AppSource, se [klassificeringar & recensioner Analytics-instrumentpanelen i Partner Center](./partner-center-portal/ratings-reviews.md).
- Vanliga frågor och svar om affärs platser för marknads platser och en omfattande ord lista med data termer finns i [terminologi för kommersiella Marketplace-analyser och vanliga frågor](./analytics-faq.md).