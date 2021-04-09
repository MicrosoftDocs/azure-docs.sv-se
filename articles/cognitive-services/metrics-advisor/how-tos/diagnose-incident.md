---
title: Diagnostisera incidenter med hjälp av Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Lär dig hur du diagnostiserar en incident med hjälp av Metrics Advisor och få detaljerade vyer av avvikelser i dina data.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: ecbfb2d9acf6c62f95c264a14e306442db25e483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "101703430"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>Anvisningar: diagnosticera en incident med hjälp av Metrics Advisor

Metrics Advisor innehåller flera funktioner för diagnostik och ger en djupgående vy över identifierade incidenter och ger rotor Saks analys. När en grupp med avvikelser identifierade på ett mått grupperar Metrics Advisor avvikelser i en hierarki och analyserar ovanpå den.

> [!NOTE]
> För närvarande har Metrics Advisor stöd för incident diagnostik för mått med minst en dimension och mått med den  *numeriska* typen. Ditt mått måste ha ett aggregerat dimensions värde som SUM för varje dimension, som används för att bygga diagnostikprogrammet. Mått Advisor ger [**automatiska inställningar**](onboard-your-data.md#automatic-roll-up-settings) för att skapa sammanställda värden. 

Klicka på **incident Hub** i det vänstra navigerings fönstret för att se alla incidenter under ett givet mått. Längst upp på sidan kan du välja olika mått för att se deras identifierings-konfigurationer, identifierings resultat och ändra tidsintervallet.

> [!TIP]
> Du kan också gå till **incident Hub** genom att:
> * Klicka på en data punkt i visualiseringen för måttet och använda länkarna längst ned i fönstret **Lägg till feedback** som visas.
> * Klicka på någon av avvikelserna på fliken **incidenter** för måttet. 

**Översikts** avsnittet innehåller identifierings resultat, inklusive antalet avvikelser och aviseringar inom det valda tidsintervallet.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="Incident hubb" lightbox="../media/diagnostics/incident-hub-overview.png":::

Identifierade incidenter inom det valda måttet och tidsintervallet visas i listan **över incidenter**. Det finns alternativ för att filtrera och beställa incidenter. Till exempel efter allvarlighets grad. Klicka på en av incidenterna för att gå till sidan **incident** för ytterligare diagnostik.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="Incident lista" lightbox="../media/diagnostics/incident-list.png":::

I avsnittet **Diagnostic** kan du utföra djupgående analyser av en incident och verktyg för att identifiera rotor-orsaker.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="Diagnostisera en incident" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>Rotor sakens råd

När en grupp med avvikelser upptäcks i ett mått och orsakar en incident försöker Metric Advisor analysera den bakomliggande orsaken till incidenten. **Rotor sakens råd** innehåller automatiska förslag för troliga orsaker till en incident. Den här funktionen är endast tillgänglig om det finns ett sammanställt värde i dimensionen. Om måttet inte har någon dimension blir rotor saken själva. Rotors Aker visas i höger panel och det kan finnas flera orsaker. Om det inte finns några data i tabellen innebär det att din dimension inte uppfyller kraven för att utföra analysen.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="Rotor sakens råd":::


När måttet för rotor saken anges med vissa dimensioner kan du klicka på **gå till mått** om du vill visa mer information om måttet.

## <a name="incident-tree"></a>Incident träd

Tillsammans med automatiserad analys av potentiella rotor orsaker stöder Metrics Advisor manuell rotor Saks analys med hjälp av **incident trädet**. Det finns två typer av incident träd på sidan incident: **snabb diagnostiserings** trädet och det **interaktiva trädet**.

Trädet för snabb diagnoser är till för att diagnostisera en aktuell incident och rotnoden är begränsad till den aktuella noden för incident-rotnoden. Du kan expandera och komprimera trädnoden genom att klicka på den och dess serie visas tillsammans med den aktuella incident serien i diagrammet ovanför trädet.

Med det interaktiva trädet kan du diagnostisera aktuella incidenter samt äldre incidenter, och de som är relaterade. När du använder det interaktiva trädet högerklickar du på en nod för att öppna en åtgärds meny där du kan välja en dimension för att öka detalj nivån i rotnoden och en dimension för att öka detalj nivån för varje nod. Genom att klicka på knappen Avbryt i dimensions listan längst upp kan du ta bort detalj nivån upp eller ned från den här dimensionen. Vänsterklicka på en nod för att välja den och visa dess serie tillsammans med den aktuella incident serien i diagrammet.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="Incident träd" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>Avvikelse detalj nivå nedåt

När du visar incident information kan du behöva få mer detaljerad information, till exempel för olika dimensioner och tidsstämplar. Om dina data har en eller flera dimensioner kan du använda funktionen öka detalj nivån för att få en mer detaljerad vy. 

Om du vill använda funktionen detalj granskning klickar du på fliken **mått detalj** visning i **incident Hub**. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Mått borrning ":::

**Dimensions** inställningen är en lista över dimensioner för en incident. du kan välja andra tillgängliga dimensions värden för var och en. När dimensions värden har ändrats. Med inställningen **tidsstämpel** kan du Visa den aktuella incidenten vid olika tidpunkter.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Välj alternativ för detalj granskning och välj en dimension

Det finns två typer av detalj nivå alternativ: **öka detalj nivån** och **vågrät jämförelse**.

> [!Note]
> 1. Om du vill öka detalj nivån kan du utforska data från olika dimensions värden, förutom de närvarande valda dimensionerna. 
> 2. För horisontell jämförelse kan du utforska data från olika dimensions värden, förutom alla-upp-dimensionerna.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Öka detalj nivån för dimension":::

### <a name="value-comparison-for-different-dimension-values"></a>Värde jämförelse för olika dimensions värden

Den andra delen av fliken detaljgranska är en tabell med jämförelser för olika dimensions värden. Den innehåller värdet, bas linje värde, differens värde, delta värde och om det är en avvikelse.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Jämförelse av detalj nivå" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Värde och förväntade värde jämförelser för olika dimensions värden

Det tredje avsnittet av fliken detaljgranska är ett histogram med värdena och de förväntade värdena för olika dimensions värden. Histogrammet sorteras efter skillnaden mellan värde och förväntat värde. Du kan hitta det oväntade värdet med den största effekten enkelt. I bilden ovan kan vi till exempel ta reda på att, förutom allt upp-värdet, **US7** bidrar mest till avvikelsen.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Detaljgranska tabell" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Rå värde visualisering
Den sista delen av fliken detalj granskning är ett linje diagram med RAW-värden. Med det här diagrammet behöver du inte gå till mått sidan för att visa information.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Öka detalj nivån för linje diagram" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Visa liknande avvikelser med klustring för tids serier

När du visar en incident kan du använda fliken **liknande tids serier – klustring** för att se de olika serier som är kopplade till den. Serier i en grupp sammanfattas tillsammans. Vi kan se att det finns minst två serie grupper i bilden ovan. Den här funktionen är endast tillgänglig om följande krav uppfylls:

1. Mått måste ha en eller flera dimensioner eller dimensions värden.
2. Serien inom ett mått måste ha en liknande trend.

Tillgängliga dimensioner visas överst på fliken och du kan välja att ange serien.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Serie grupp":::

## <a name="compare-time-series"></a>Jämför tids serier

Ibland när en avvikelse identifieras i en viss tids serie är det bra att jämföra den med flera andra serier i samma visualisering. Klicka på fliken **Jämför verktyg** och klicka sedan på knappen blå **+ Lägg till** . 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Lägg till serie som ska jämföras" lightbox="../media/diagnostics/add-series.png":::

Välj en serie från datafeeden. Du kan välja samma granularitet eller en annan. Välj mål dimensionerna och Läs in serie trenden och klicka sedan på **OK** för att jämföra den med en tidigare serie. Serien placeras tillsammans i en visualisering. Du kan fortsätta att lägga till fler serier för jämförelse och få ytterligare insikter. Klicka på den nedrullningsbara menyn högst upp på fliken **Jämför verktyg** för att jämföra tids serie data över en tids period.  

> [!Warning]
> Om du vill göra en jämförelse kan tids serie data analyser kräva Skift i data punkter så att data punkterna är mer detaljerade måste de ha stöd för det. Om dina data till exempel är varje vecka och du använder jämförelse **dagen** , får du inga resultat. I det här exemplet ska du använda jämförelse **månads** månad i stället.

När du har valt en tids Skift ande jämförelse kan du välja om du vill jämföra data värden, delta värden eller procent andelen.

> [!Note]
> * **Datavärdet** är det obearbetade data värdet.
> * **Delta värde** är skillnaden mellan oformaterat värde och jämfört värde.
> * **Procent andels värde** är skillnaden mellan oformaterat värde och jämfört värde dividerat med ett jämfört värde.

## <a name="related-incidents-across-metrics"></a>Relaterade incidenter över mått

Ibland kan du behöva kontrol lera incidenter för olika mått samtidigt eller relaterade incidenter i andra mått. Du hittar en lista över relaterade incidenter i avsnittet **kors statistik analys** . 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="relaterade incidenter över mått":::

Innan du kan se relaterade incidenter för aktuella mått måste du lägga till en relation mellan måtten. Lägg till en relation genom att klicka på **mått diagram inställningar** . Endast mått med samma dimensions namn kan vara relaterade. Använd följande parametrar.

- Aktuellt & Mät värde för datafeed: datafeeden och mät värdet för den aktuella incidenten
- Riktning: riktningen för relationen mellan två mått. (gäller inte relaterade incident listor nu)
- En annan datafeed & mått: datafeeden och måttet för att ansluta med nuvarande mått


## <a name="next-steps"></a>Nästa steg 

- [Justera avvikelseidentifiering med hjälp av feedback](anomaly-feedback.md)
- [Konfigurera mått och finjustera konfiguration för identifiering](configure-metrics.md)
