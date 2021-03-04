---
title: Komma igång med Azure Metrics Explorer
description: Lär dig hur du skapar ditt första mått diagram med Azure Metrics Explorer.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.openlocfilehash: e3d8d6529eb943566118cc67eab549cb32891cd8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037242"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Komma igång med Azure Metrics Explorer

## <a name="where-do-i-start"></a>Var börjar jag
Azure Monitor Metrics Explorer är en komponent i Microsoft Azure-portalen som gör det möjligt att rita diagram, visuellt korrelera trender och undersöka toppar och DIP i mått värden. Använd Mät Utforskaren för att undersöka hälso tillståndet och användningen av dina resurser. Starta i följande ordning:

1. [Välj en resurs och ett mått](#create-your-first-metric-chart) så visas ett Basic-diagram. [Välj sedan ett tidsintervall](#select-a-time-range) som är relevant för din undersökning.

1. Försök att [använda dimensions filter och delning](#apply-dimension-filters-and-splitting). Med filtren och delningen kan du analysera vilka segment i måttet som bidrar till det övergripande mått svärdet och identifiera möjliga avvikande Mät värden.

1. Använd [Avancerade inställningar](#advanced-chart-settings) för att anpassa diagrammet innan du fäster på instrument paneler. [Konfigurera aviseringar](../alerts/alerts-metric-overview.md) för att ta emot meddelanden när mått svärdet överskrider eller sjunker under ett tröskelvärde.

## <a name="create-your-first-metric-chart"></a>Skapa ditt första mått diagram

Om du vill skapa ett mått diagram går du till vyn resurs, resurs grupp, prenumeration eller Azure Monitor, öppnar fliken **mått** och följer de här stegen:

1. Klicka på knappen "Välj ett omfång" för att öppna resurs omfattnings väljaren. På så kan du välja de resurser som du vill visa mått för. Resursen bör redan vara ifylld om du öppnade Metrics Explorer från resursens meny. [Läs den här artikeln](./metrics-dynamic-scope.md)om du vill veta mer om hur du visar mått över flera resurser.
    > ![Välj en resurs](./media/metrics-getting-started/scope-picker.png)

2. För vissa resurser måste du välja ett namn område. Namn området är bara ett sätt att organisera mått så att du lätt kan hitta dem. Lagrings konton har till exempel separata namn rymder för lagring av filer, tabeller, blobbar och köers mått. Många resurs typer har bara ett namn område.

3. Välj ett mått i en lista över tillgängliga mått.

    > ![Välj ett mått](./media/metrics-getting-started/metrics-dropdown.png)

4. Alternativt kan du [ändra mått agg regeringen](../essentials/metrics-charts.md#aggregation). Du kanske exempelvis vill att diagrammet ska Visa lägsta, högsta eller genomsnittliga värden för måttet.

> [!TIP]
> Använd knappen **Lägg till mått** och upprepa de här stegen om du vill se flera mått som är ritade i samma diagram. För flera diagram i en vy väljer du knappen **Lägg till diagram** överst.

## <a name="select-a-time-range"></a>Välj ett tidsintervall

> [!WARNING]
> [De flesta mått i Azure lagras i 93 dagar](../essentials/data-platform-metrics.md#retention-of-metrics). Du kan dock fråga högst 30 dagar på data i ett enskilt diagram. Den här begränsningen gäller inte för [loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics).

Som standard visar diagrammet de senaste 24 timmarna med mät data. Använd panelen **tids väljare** för att ändra tidsintervallet, zooma in eller ut i diagrammet. 

![Ändra tids områdets panel](./media/metrics-getting-started/time.png)

> [!TIP]
> Använd **tids penseln** för att undersöka ett intressant ytdiagram i diagrammet (insamling eller dip). Placera mus pekaren i början av ytan, klicka och håll ned vänster MUSKNAPP, dra till den andra sidan av ytan och släpp sedan knappen. Diagrammet kommer att zooma in inom det tidsintervallet. 

## <a name="apply-dimension-filters-and-splitting"></a>Använd dimensions filter och delning

[Filtrering](../essentials/metrics-charts.md#filters) och [delning](../essentials/metrics-charts.md#apply-splitting) är kraftfulla diagnostiska verktyg för mått som har dimensioner. Dessa funktioner visar hur olika mått segment ("dimensions värden") påverkar det allmänna värdet för måttet och gör det möjligt att identifiera möjliga avvikande värden.

- Med **filtrering** kan du välja vilka dimensions värden som ska ingå i diagrammet. Du kanske till exempel vill visa lyckade förfrågningar när du diagramerar måttet för *Server svars tid* . Du måste använda filtret för att kunna utföra en *begär ande* dimension. 

- **Dela upp** styr om diagrammet ska visa separata rader för varje värde i en dimension eller mängd värden i en enda rad. Du kan till exempel se en rad för en genomsnittlig svars tid för alla Server instanser eller se separata rader för varje server. Du måste tillämpa delning på *Server instans* dimensionen för att se separata rader.

Se [exempel på de diagram](../essentials/metric-chart-samples.md) som har filtrering och delning tillämpad. Artikeln visar stegen som användes för att konfigurera diagram.

## <a name="advanced-chart-settings"></a>Avancerade diagram inställningar

Du kan anpassa diagram stil, rubrik och ändra avancerade diagram inställningar. När du är färdig med anpassning fäster du det på en instrument panel för att spara ditt arbete. Du kan också konfigurera mått aviseringar. Följ [produkt dokumentationen](../essentials/metrics-charts.md) om du vill lära dig mer om dessa och andra avancerade funktioner i Azure Monitor Metrics Explorer.

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om avancerade funktioner i Metrics Explorer](../essentials/metrics-charts.md)
* [Visa flera resurser i Metrics Explorer](./metrics-dynamic-scope.md)
* [Felsökning av Metrics Explorer](metrics-troubleshoot.md)
* [Visa en lista över tillgängliga mått för Azure-tjänster](./metrics-supported.md)
* [Visa exempel på konfigurerade diagram](../essentials/metric-chart-samples.md)