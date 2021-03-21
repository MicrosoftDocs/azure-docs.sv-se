---
title: En referens guide till vyer i designer i Azure Monitor | Microsoft Docs
description: Med hjälp av View Designer i Azure Monitor kan du skapa anpassade vyer som visas i Azure Portal och som innehåller en mängd olika visualiseringar på data i arbets ytan Log Analytics. Den här artikeln är en referens guide för inställningarna för de paneler som är tillgängliga i dina anpassade vyer.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: d1d0da70dc1e47d0a1ddb90abbed2eaea83919cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040183"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Referens guide för att Visa designer-paneler i Azure Monitor
Med hjälp av View Designer i Azure Monitor kan du skapa en mängd olika anpassade vyer i Azure Portal som kan hjälpa dig att visualisera data i din Log Analytics-arbetsyta. Den här artikeln är en referens guide för inställningarna för de paneler som är tillgängliga i dina anpassade vyer.

Mer information om View Designer finns i:

* [Visa designer](view-designer.md): ger en översikt över Visa designer och procedurer för att skapa och redigera anpassade vyer.
* [Referens för visualiserings del](view-designer-parts.md): innehåller en referens guide för inställningarna för de visualiserings delar som är tillgängliga i dina anpassade vyer.


De tillgängliga panelerna i designern beskrivs i följande tabell:  

| Panel | Beskrivning |
|:--- |:--- |
| [Nummer](#number-tile) |Antalet poster från en fråga. |
| [Två tal](#two-numbers-tile) |Antalet poster från två olika frågor. |
| [Ring](#donut-tile) | Ett diagram baserat på en fråga med ett sammanfattnings värde i mitten. |
| Linje diagram och bildtext | Ett linje diagram som är baserat på en fråga och en prat bubbla med ett sammanfattnings värde. |
| [Linje diagram](#line-chart-tile) |Ett linje diagram som baseras på en fråga. |
| [Två tids linjer](#two-timelines-tile) | Ett stapeldiagram med två serier, var och en baserat på en separat fråga. |

I nästa avsnitt beskrivs panel typerna och deras egenskaper i detalj.

> [!NOTE]
> Paneler i vyer baseras på [logg frågor](../logs/log-query-overview.md) i Log Analytics arbets ytan. De stöder för närvarande inte [kors resurs frågor](../logs/cross-workspace-query.md) för att hämta data från Application Insights.

## <a name="number-tile"></a>Siffer panel
Panelen **tal** visar både antalet poster från en logg fråga och en etikett.

![Siffer panel](media/view-designer-tiles/tile-number.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Name |Den text som visas överst i panelen. |
| Beskrivning |Texten som visas under panel namnet. |
| **Panel** | |
| Förklaring |Texten som visas under värdet. |
| Fråga |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Avancerat** |**> data flödes verifiering** |
| Enabled |Välj den här länken om data flödes verifiering ska aktive ras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligt vis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Fråga |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för huvud frågans värde. |
| Meddelande |Det meddelande som visas om verifierings frågan för data flödet inte returnerar några data. Om du inte anger något meddelande visas ett status meddelande om att *utföra utvärderingen* . |


## <a name="two-numbers-tile"></a>Panel med två siffror
Den här panelen visar antalet poster från två olika logg frågor och en etikett för var och en.

![Panel med två siffror](media/view-designer-tiles/tile-two-numbers.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Name |Den text som visas överst i panelen. |
| Beskrivning |Texten som visas under panel namnet. |
| **Första panelen** | |
| Förklaring |Texten som visas under värdet. |
| Fråga |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Andra panelen** | |
| Förklaring |Texten som visas under värdet. |
| Fråga |Den fråga som körs. Antalet poster som returneras av frågan visas. |
| **Avancerat** |**> data flödes verifiering** |
| Enabled |Välj den här länken om data flödes verifiering ska aktive ras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligt vis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Fråga |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för huvud frågans värde. |
| Meddelande |Det meddelande som visas om verifierings frågan för data flödet inte returnerar några data. Om du inte anger något meddelande visas ett status meddelande om att *utföra utvärderingen* . |


## <a name="donut-tile"></a>Ring panel
**Ring** panelen visar ett enda tal som sammanfattar en värde kolumn i en logg fråga. Ring bilden visar resultatet av de tre översta posterna.

![Ring panel](media/view-designer-tiles/tile-donut.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Name |Den text som visas överst i panelen. |
| Beskrivning |Texten som visas under panel namnet. |
| **Ring** | |
| Fråga |Den fråga som körs för ringen. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Den här frågan använder vanligt vis *mått* nyckelordet för att summera resultat. |
| **Ring** |**> Center** |
| Text |Texten som visas under värdet i ring diagrammet. |
| Åtgärd |Åtgärden som utförs på egenskap svärdet för att sammanfattas som ett enda värde.<ul><li>Sum: Lägg till värdena för alla poster med egenskap svärdet.</li><li>Procent andel: procent andelen av summerade värden från poster med egenskap svärdet jämfört med de summerade värdena för alla poster.</li></ul> |
| Resultat värden som används i Center operation |Du kan också välja plus tecknet (+) om du vill lägga till ett eller flera värden. Resultatet av frågan är begränsat till poster med de egenskaps värden som du anger. Om inga värden läggs till inkluderas alla poster i frågan. |
| **Ring** |**> ytterligare alternativ** |
| Färger |Färgen som visas för var och en av de tre översta egenskaperna. Använd *Avancerad färg mappning* om du vill ange alternativa färger för speciella egenskaps värden. |
| Avancerad färg mappning |Visar en färg som representerar vissa egenskaps värden. Om värdet du anger är i de tre översta visas den alternativa färgen i stället för standard färgen. Om egenskapen inte finns i de tre översta, visas inte färgen. |
| **Avancerat** |**> data flödes verifiering** |
| Enabled |Välj den här länken om data flödes verifiering ska aktive ras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligt vis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Fråga |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för huvud frågans värde. |
| Meddelande |Det meddelande som visas om verifierings frågan för data flödet inte returnerar några data. Om du inte anger något meddelande visas ett status meddelande om att *utföra utvärderingen* . |


## <a name="line-chart-tile"></a>Linje diagram panel
Den här panelen är ett linje diagram som visar flera serier från en logg fråga över tid. 

![Skärm bild av en linje diagram panel i Azure Monitor View Designer.](media/view-designer-tiles/tile-line-chart.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Name |Den text som visas överst i panelen. |
| Beskrivning |Texten som visas under panel namnet. |
| **Linje diagram** | |
| Fråga |Frågan som körs för linje diagrammet. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Den här frågan använder vanligt vis *mått* nyckelordet för att summera resultat. Om frågan använder *intervall* nyckelordet använder x-axeln det här tidsintervallet. Om frågan inte använder *intervall* nyckelordet använder x-axeln Tim intervall. |
| **Linje diagram** |**> Y-axel** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för värdena som returneras av frågan. Den här informationen används för att visa etiketter i diagrammet som visar värde typerna och eventuellt för att konvertera värdena. **Enhets typen** anger enhets kategorin och definierar de aktuella värdena för **enhets typen** som är tillgängliga. Om du väljer ett värde i **Convert så** konverteras de numeriska värdena från den **aktuella enhets** typen till typen **konvertera till** . |
| Anpassad etikett |Den text som visas för y-axeln bredvid *enhets* typens etikett. Om ingen etikett har angetts visas endast *enhets* typen. |
| **Avancerat** |**> data flödes verifiering** |
| Enabled |Välj den här länken om data flödes verifiering ska aktive ras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligt vis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Fråga |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för huvud frågans värde. |
| Meddelande |Det meddelande som visas om verifierings frågan för data flödet inte returnerar några data. Om du inte anger något meddelande visas ett status meddelande om att *utföra utvärderingen* . |


## <a name="line-chart-and-callout-tile"></a>Linje diagram och bild text panel
Den här panelen innehåller både ett linje diagram som visar flera serier från en logg fråga över tid och en prat bubbla med ett sammanfattande värde. 

![Skärm bild av ett linje diagram och en bild text panel i Azure Monitor View Designer. med prat bubblan förstärker du linje diagrammet genom att visa ett sammanfattande värde.](media/view-designer-tiles/tile-line-chart-callout.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Name |Den text som visas överst i panelen. |
| Beskrivning |Texten som visas under panel namnet. |
| **Linje diagram** | |
| Fråga |Frågan som körs för linje diagrammet. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Den här frågan använder vanligt vis *mått* nyckelordet för att summera resultat. Om frågan använder *intervall* nyckelordet använder x-axeln det här tidsintervallet. Om frågan inte använder *intervall* nyckelordet använder x-axeln Tim intervall. |
| **Linje diagram** |**> prat bubbla** |
| Rubrik för bildtext | Texten som visas ovanför bild text svärdet. |
| Serie namn |Egenskap svärdet för serien som ska användas som bild text värde. Om ingen serie anges används alla poster från frågan. |
| Åtgärd |Den åtgärd som utförs på egenskap svärdet som ska sammanfattas som ett enda värde för kommentaren.<ul><li>Genomsnitt: medelvärdet av värden från alla poster.</li><li>Count: antalet poster som returneras av frågan.</li><li>Senaste exempel: värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: det högsta värdet för de intervall som ingår i diagrammet.</li><li>Min: det minsta värdet för de intervall som ingår i diagrammet.</li><li>Sum: summan av värdena från alla poster.</li></ul> |
| **Linje diagram** |**> Y-axel** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagram etiketter som visar värde typerna och eventuellt för att konvertera värdena. *Enhets* typen anger enhetens kategori och definierar de tillgängliga värdena för den *aktuella enhets* typen. Om du väljer ett värde i *Convert to* konverteras de numeriska värdena från den *aktuella enhets* typen till typen *konvertera till* . |
| Anpassad etikett |Den text som visas för y-axeln bredvid *enhets* typens etikett. Om ingen etikett har angetts visas endast *enhets* typen. |
| **Avancerat** |**> data flödes verifiering** |
| Enabled |Välj den här länken om data flödes verifiering ska aktive ras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligt vis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Fråga |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för huvud frågans värde. |
| Meddelande |Det meddelande som visas om verifierings frågan för data flödet inte returnerar några data. Om du inte anger något meddelande visas ett status meddelande om att *utföra utvärderingen* . |


## <a name="two-timelines-tile"></a>Panelen två tids linjer
I panelen **två tids linjer** visas resultaten av två logg frågor med tiden som stapeldiagram. En prat bubbla visas för varje serie. 

![Panelen två tids linjer](media/view-designer-tiles/tile-two-timelines.png)

| Inställning | Beskrivning |
|:--- |:--- |
| Name |Den text som visas överst i panelen. |
| Beskrivning |Texten som visas under panel namnet. |
| Första diagrammet | |
| Förklaring |Texten som visas under bild texten för den första serien. |
| Färg |Färgen som används för kolumnerna i den första serien. |
| Diagram fråga |Frågan som körs för den första serien. Antalet poster under varje tidsintervall representeras av diagrammets kolumner. |
| Åtgärd |Den åtgärd som utförs på egenskap svärdet som ska sammanfattas som ett enda värde för kommentaren.<ul><li>Genomsnitt: medelvärdet av värden från alla poster.</li><li>Count: antalet poster som returneras av frågan.</li><li>Senaste exempel: värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: det högsta värdet för de intervall som ingår i diagrammet.</li></ul> |
| **Andra diagrammet** | |
| Förklaring |Texten som visas under bild texten för den andra serien. |
| Färg |Färgen som används för kolumnerna i den andra serien. |
| Diagram fråga |Frågan som körs för den andra serien. Antalet poster under varje tidsintervall representeras av diagrammets kolumner. |
| Åtgärd |Den åtgärd som utförs på egenskap svärdet som ska sammanfattas som ett enda värde för kommentaren.<ul><li>Genomsnitt: medelvärdet av värden från alla poster.</li><li>Count: antalet poster som returneras av frågan.</li><li>Senaste exempel: värdet för det sista intervallet som ingår i diagrammet.</li><li>Max: det högsta värdet för de intervall som ingår i diagrammet. |
| **Avancerat** |**> data flödes verifiering** |
| Enabled |Välj den här länken om data flödes verifiering ska aktive ras för panelen. Den här metoden ger ett alternativt meddelande om data inte är tillgängliga. Du använder vanligt vis metoden för att tillhandahålla ett meddelande under den tillfälliga perioden när vyn installeras och data blir tillgängliga. |
| Fråga |Frågan som körs för att avgöra om data är tillgängliga för vyn. Om frågan inte returnerar några resultat visas ett meddelande i stället för huvud frågans värde. |
| Meddelande |Det meddelande som visas om verifierings frågan för data flödet inte returnerar några data. Om du inte anger något meddelande visas ett status meddelande om att *utföra utvärderingen* . |


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) som stöder frågor i brickor.
* Lägg till [visualiserings delar](view-designer-parts.md) i din anpassade vy.