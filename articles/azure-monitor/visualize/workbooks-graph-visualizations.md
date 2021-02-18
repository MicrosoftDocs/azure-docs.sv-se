---
title: Diagram visualiseringar för Azure Monitor arbets bok
description: Lär dig mer om alla diagram visualiseringar för Azure Monitor arbets böcker.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: a538eaf06013dcce6529c91816b419159a2600a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100625649"
---
# <a name="graph-visualizations"></a>Diagram visualiseringar

Arbets böcker stöder visualisering av godtyckliga grafer som baseras på data från loggar för att Visa relationerna mellan övervakning av entiteter.

Diagrammet nedan visar data som flödar in/ut ur en dator via olika portar till/från externa datorer. Det är färgat efter typ (dator vs. port vs. extern IP) och kant storlekarna motsvarar mängden data som flödar in mellan. Underliggande data kommer från KQL-frågor riktade mot virtuella dator anslutningar.

[![Skärm bild av vyn Sammanfattning av panel](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Lägga till ett diagram
1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. Ange visualiseringen som **graf**
6. Välj knappen **Graph-inställningar** för att öppna fönstret inställningar
7. I _Nedlayout fälten_ längst ned anger du:
    * Nod-ID: `Id`
    * Käll-ID: `SourceId`
    * Mål-ID: `TargetId`
    * Kant etikett: `None`
    * Kant storlek: `Calls`
    * Node-storlek: `None`
    * Typ av färgning: `Categorical`
    * Fält färgs fält: `Kind`
    * Färgpalett: `Pastel`
8. I _format inställningar för Node_ överst anger du:
    * _Topp innehåll_– Använd kolumn: `Name` , kolumn åter givning: `Text`
    * _Centrera innehåll_– Använd kolumn: `Calls` , kolumn åter givning: `Big Number` , färgpalett: `None`
    * _Nedre innehålls_ användning – Använd kolumn: `Kind` , kolumn åter givning: `Text`
9. Välj knappen _Spara och Stäng_ längst ned i fönstret.

[![Skärm bild som visar sammanfattnings vyn för panelen med ovanstående fråga och inställningar.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Diagram inställningar

| Inställning         | Förklaring                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Markerar en kolumn som innehåller det unika ID: t för noderna i grafen. Värdet för kolumnen kan vara en sträng eller ett tal. |
| `Source Id`     | Markerar en kolumn som innehåller ID: n för källentiteter för kanter i grafen. Värdena måste mappas till ett värde i kolumnen _Node ID_ . |
| `Target Id`     | Markerar en kolumn som innehåller ID: n för målattribut för kanter i grafen. Värdena måste mappas till ett värde i kolumnen _Node ID_ . |
| `Edge Label`    | Markerar en kolumn som innehåller kant etiketter i grafen.                                                            |
| `Edge Size`     | Markerar en kolumn som tillhandahåller måttet som kant bredderna ska baseras på.                                |
| `Node Size`     | Markerar en kolumn som tillhandahåller det mått som Node-områdena kommer att baseras på.                                 |
| `Coloring Type` | Används för att välja nodens färg schema.                                                                            |

## <a name="node-coloring-types"></a>Färg typer för noder

| Typ av färgning | Förklaring |
|:------------- |:------------|
| `None`        | Alla noder har samma färg. |
| `Categorical` | Noder tilldelas färger baserat på värdet eller kategorin från en kolumn i resultat uppsättningen. I exemplet ovan baseras färgningen på kolumn _typen_ för resultat uppsättningen. Paletter som stöds är `Default` , `Pastel` och `Cool tone` .  |
| `Field Based` | I den här typen tillhandahåller en kolumn vissa RGB-värden som används för noden. Ger störst flexibilitet men kräver vanligt vis mer arbete för att aktivera.  |

## <a name="node-format-settings"></a>Inställningar för Node-format

Grafer författare kan ange vilket innehåll som ska gå till olika delar av en nod: överkant, vänster, mitten, höger och nederkant. Diagram kan använda valfri åter givnings arbets bok som stöder (text, Big Number, Spark-linjer, ikon osv.).

## <a name="field-based-node-coloring"></a>Färg på fält baserad nod

1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys.

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Ange visualiseringen som *graf*
6. Välj knappen **Graph-inställningar**  för att öppna fönstret inställningar.
7. I *Nedlayout fälten* längst ned anger du:
    * Nod-ID:`Id`
    * Käll-ID: `SourceId`
    * Mål-ID: `TargetId`
    * Kant etikett: `None`
    * Kant storlek: `Calls`
    * Node-storlek: `Node`
    * Typ av färgning: `Field Based`
    * Fält färgs fält: `Color`
8. I *format inställningar för Node* överst anger du följande.
    * I *högsta innehåll* ställer du in:
        * Använd kolumn: `Name` .
        * Kolumn åter givning: `Text` .
    * I *Center-innehåll* ställer du in:
        * Använd kolumn: `Calls`
        * Kolumn åter givning: `Big Number`
        * Färgpalett: `None`
    * I *innehåll längst ned* anger du:
        * Använd kolumn: `Kind`
        * Kolumn åter givning: `Text` .
9. Välj **knappen Spara och Stäng** längst ned i fönstret.

[![Skärm bild som visar skapandet av en diagram visualisering med fält Base Node-färger.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Diagram stöder också sammansatt bar åter givning. Mer information finns på den [sammansatta streckkods dokumentationen](workbooks-composite-bar.md).
* Lär dig mer om de [data källor](workbooks-data-sources.md) som du kan använda i arbets böcker.
