---
title: Azure Monitor arbets bok honung kamma-visualiseringar
description: Lär dig mer om att Azure Monitor arbets bok honung kamma-visualiseringar.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: lagayhar
ms.openlocfilehash: c91912f711b912c54c1673f2f92e998b4d9ea9db
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736941"
---
# <a name="honey-comb-visualizations"></a>Honung kamma-visualiseringar

Med honungsbi kammar kan du använda vyer med hög densitet i mått eller kategorier som kan grupperas som kluster. De är användbara för att visuellt identifiera hotspots och ytterligare detalj nivå.

Bilden nedan visar processor användningen för virtuella datorer över två prenumerationer. Varje cell representerar en virtuell dator och färgen/etiketten representerar den genomsnittliga processor användningen (röda är aktiva datorer). De virtuella datorerna klustras av prenumerationen.

[![Skärm bild som visar processor användningen för virtuella datorer över två prenumerationer](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Lägga till en honung kamma

1. Ändra arbets boken till redigerings läge genom att klicka på objektet redigera verktygsfält.
2. Använd **Lägg till**  längst ned och *Lägg till fråga* för att lägga till en logg frågas kontroll i arbets boken.
3. Välj "loggar" som *data källa*, "Log Analytics" som *resurs typ* och för *resurs* punkt till en arbets yta som har prestanda logg för virtuella datorer.
4. Använd Frågeredigeraren för att ange KQL för din analys.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Kör fråga.
6. Ange *visualiseringen* till "Graph".
7. Välj **diagram inställningar**.
    1. I *Nedlayout fälten* längst ned anger du:
        1. Diagram typ: **Hive-kluster**.
        2. Node-ID: `Id` .
        3. Gruppera efter: `None` .
        4. Node-storlek: 100.
        5. Marginal mellan sexhörningar: `5` .
        6. Typ av färgning: **termisk karta**.
        7. Fält färgs fält: `CouterValue` .
        8. Färgpalett: `Red to Green` .
        9. Minsta värde: `100` .
        10. Högsta värde: `2000` .
    2. I *format inställningar för Node* överst anger du:
        1. Högsta innehåll:
            1. Använd kolumn: `Computer` .
            2. Kolumn åter givning " `Text` .
        9. Center innehåll:
            1. Använd kolumn: `CounterValue` .
            2. Kolumn åter givning: `Big Number` .
            3. Färgpalett: `None` .
            4. Markera rutan *anpassad talformatering* .
            5. Enheter: `Megabytes` .
            6. Högst bråk tals siffror: `1` .
8. Välj knappen **Spara och Stäng** längst ned i fönstret.

[![Skärm bild av frågans kontroll, diagram inställningar och honung kamma med ovanstående fråga och inställningar](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Inställningar för honung kamma-layout

| Inställning | Förklaring |
|:------------- |:-------------|
| `Node Id` | Markerar en kolumn som tillhandahåller unika ID för noder. Värdet för kolumnen kan vara en sträng eller ett tal. |
| `Group By Field` | Välj den kolumn som du vill använda för att klustra noderna. |
| `Node Size` | Anger storleken på de sexhörningiga cellerna. Använd med `Margin between hexagons` egenskapen för att anpassa utseendet på honung kamma-diagrammet. |
| `Margin between hexagons` | Anger avståndet mellan de sexhörningiga cellerna. Använd med `Node size` egenskapen för att anpassa utseendet på honung kamma-diagrammet. |
| `Coloring type` | Väljer det schema som ska användas för att färga noden. |
| `Node Color Field` | Markerar en kolumn som tillhandahåller det mått som Node-områdena kommer att baseras på. |

## <a name="node-coloring-types"></a>Färg typer för noder

| Typ av färgning | Förklaring |
|:------------- |:-------------|
| `None` | Alla noder har samma färg. |
| `Categorical` | Noder tilldelas färger baserat på värdet eller kategorin från en kolumn i resultat uppsättningen. I exemplet ovan baseras färgningen på kolumn _typen_ för resultat uppsättningen. Paletter som stöds är `Default` , `Pastel` och `Cool tone` .  |
| `Heatmap` | I den här typen är cellerna färgade baserat på en mått kolumn och en färgpalett. Detta är ett enkelt sätt att markera mått uppslag i celler. |
| `Thresholds` | I den här typen anges cell färger av tröskel regler (till exempel _CPU > 90% => Red, 60% > CPU > 90% => gul, CPU < 60% => grönt_) |
| `Field Based` | I den här typen tillhandahåller en kolumn vissa RGB-värden som används för noden. Ger störst flexibilitet men kräver vanligt vis mer arbete för att aktivera.  |
      
## <a name="node-format-settings"></a>Inställningar för Node-format

Honung kamma-författare kan ange vilket innehåll som ska gå till olika delar av en nod: Top, Left, mitten, Right och nederkant. Författare är kostnads fria att använda någon av arbets böckerna för åter givning som stöder (text, Big Number, Spark-linjer, ikon osv.).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du skapar en [sammansatt bar-återgivning i arbets böcker](workbooks-composite-bar.md).
- Lär dig hur du [importerar Azure Monitor loggdata till Power BI](./powerbi.md).