---
title: En referens guide till View Designer-delar i Azure Monitor | Microsoft Docs
description: Med hjälp av View Designer i Azure Monitor kan du skapa anpassade vyer som visas i Azure Portal och som innehåller en mängd olika visualiseringar på data i arbets ytan Log Analytics. Den här artikeln är en referens guide för inställningarna för de visualiserings delar som är tillgängliga i dina anpassade vyer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2018
ms.openlocfilehash: 074c5dda8ff8d5f837e8ea2a85c183c69421bedb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710880"
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-azure-monitor"></a>Referens guide för att Visa designer visualiserings delar i Azure Monitor
Med hjälp av View Designer i Azure Monitor kan du skapa en mängd olika anpassade vyer i Azure Portal som kan hjälpa dig att visualisera data i din Log Analytics-arbetsyta. Den här artikeln är en referens guide för inställningarna för de visualiserings delar som är tillgängliga i dina anpassade vyer.

Mer information om View Designer finns i:

* [Visa designer](view-designer.md): ger en översikt över Visa designer och procedurer för att skapa och redigera anpassade vyer.
* [Panel referens](view-designer-tiles.md): innehåller en referens till inställningarna för varje tillgänglig panel i dina anpassade vyer.


De tillgängliga panel typerna för Visa designer beskrivs i följande tabell:

| Vytyp | Beskrivning |
|:--- |:--- |
| [Lista med frågor](#list-of-queries-part) |Visar en lista över logg frågor. Du kan välja varje fråga för att visa resultatet. |
| [Nummer och lista](#number-and-list-part) |Rubriken visar ett enda tal som visar antalet poster från en logg fråga. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid. |
| [Två siffror och lista](#two-numbers-and-list-part) |Rubriken visar två siffror som visar antal poster från separata logg frågor. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid. |
| [Ring och lista](#donut-and-list-part) |Rubriken visar ett enstaka tal som sammanfattar en värde kolumn i en logg fråga. Ring bilden visar resultatet av de tre översta posterna. |
| [Två tids linjer och lista](#two-timelines-and-list-part) |Rubriken visar resultatet av två logg frågor med tiden som stapeldiagram, med en prat bubbla som visar ett enda tal som sammanfattar en värde kolumn i en logg fråga. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid. |
| [Information](#information-part) |Rubriken visar statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text. |
| [Linje diagram, bildtext och lista](#line-chart-callout-and-list-part) |I sidhuvudet visas ett linje diagram med flera serier från en logg fråga över tid och en prat bubbla med ett sammanfattande värde. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid. |
| [Linje diagram och lista](#line-chart-and-list-part) |I sidhuvudet visas ett linje diagram med flera serier från en logg fråga över tid. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid. |
| [Stack med linje diagram del](#stack-of-line-charts-part) |Visar tre separata linje diagram med flera serier från en logg fråga över tid. |

I nästa avsnitt beskrivs panel typerna och deras egenskaper i detalj.

> [!NOTE]
> Delar i vyer baseras på [logg frågor](../logs/log-query-overview.md) i Log Analytics arbets ytan. De stöder för närvarande inte [kors resurs frågor](../logs/cross-workspace-query.md) för att hämta data från Application Insights.

## <a name="list-of-queries-part"></a>Lista över frågor-del
I listan med frågor-delen visas en lista över logg frågor. Du kan välja varje fråga för att visa resultatet. Vyn innehåller en enda fråga som standard, och du kan välja **+ fråga** om du vill lägga till fler frågor.

![Skärm bild av en lista över frågor visualiserings del i Azure Monitor View Designer.](media/view-designer-parts/view-list-queries.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Rubrik |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Förvalda filter |En kommaavgränsad lista över egenskaper som ska ingå i det vänstra filter fönstret när du väljer en fråga. |
| Åter givnings läge |Den inledande vy som visas när frågan väljs. Du kan välja tillgängliga vyer när de har öppnat frågan. |
| **Frågor** | |
| Sökfråga |Frågan att köra. |
| Eget namn | Det beskrivande namn som visas. |

## <a name="number-and-list-part"></a>Tal-och list del
Rubriken visar ett enda tal som visar antalet poster från en logg fråga. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid.

![Skärm bild av antalet och lista visualiserings delar i Azure Monitor View Designer.](media/view-designer-parts/view-number-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Ikon |Bild filen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Rubrik** | |
| Förklaring |Den text som visas överst i sidhuvudet. |
| Söka i data |Frågan som ska köras för rubriken. Antalet poster som returneras av frågan visas. |
| Klicka och navigera | Åtgärd som vidtas när du klickar på rubriken.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. De första två egenskaperna för de första tio posterna i resultatet visas. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt som baseras på det relativa värdet för den numeriska kolumnen.<br><br>Använd `Sort` kommandot i frågan för att sortera posterna i listan. Om du vill köra frågan och returnera alla poster kan du välja **Visa alla**. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyr diagram |Välj den här länken om du vill visa ett miniatyr diagram i stället för ett vågrätt fält. Mer information finns i [vanliga inställningar](#sparklines). |
| Färg |Färgen på staplarna eller miniatyr diagrammen. |
| Namn och värde avgränsare |Avgränsaren med ett tecken som används för att parsa egenskapen text till flera värden. Mer information finns i [vanliga inställningar](#sparklines). |
| Klicka och navigera | Åtgärd som vidtas när du klickar på ett objekt i listan.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumn rubriker** |
| Namn |Texten som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [vanliga inställningar](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Två siffror och list delen
Rubriken innehåller två siffror som visar antalet poster från separata logg frågor. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid.

![Två tal & List visning](media/view-designer-parts/view-two-numbers-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i vyn. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Ikon |Bild filen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Rubrik navigering** | |
| Klicka och navigera | Åtgärd som vidtas när du klickar på rubriken.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Rubrik** | |
| Förklaring |Den text som visas överst i sidhuvudet. |
| Söka i data |Frågan som ska köras för rubriken. Antalet poster som returneras av frågan visas. |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. De första två egenskaperna för de första tio posterna i resultatet visas. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Staplar skapas automatiskt baserat på det relativa värdet för den numeriska kolumnen.<br><br>Använd `Sort` kommandot i frågan för att sortera posterna i listan. Om du vill köra frågan och returnera alla poster kan du välja **Visa alla**. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyr diagram |Välj den här länken om du vill visa ett miniatyr diagram i stället för ett vågrätt fält. Mer information finns i [vanliga inställningar](#sparklines). |
| Färg |Färgen på staplarna eller miniatyr diagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyr diagrammet. Mer information finns i [vanliga inställningar](#sparklines). |
| Namn och värde avgränsare |Avgränsaren med ett tecken som används för att parsa egenskapen text till flera värden. Mer information finns i [vanliga inställningar](#sparklines). |
| Klicka och navigera | Åtgärd som vidtas när du klickar på ett objekt i listan.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumn rubriker** |
| Namn |Texten som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [vanliga inställningar](#thresholds). |

## <a name="donut-and-list-part"></a>Ring-och list del
Rubriken visar ett enstaka tal som sammanfattar en värde kolumn i en logg fråga. Ring bilden visar resultatet av de tre översta posterna.

![Ring-och list visning](media/view-designer-parts/view-donut-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Ikon |Bild filen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Huvud** | |
| Rubrik |Den text som visas överst i sidhuvudet. |
| Underrubrik |Texten som visas under rubriken överst i sidhuvudet. |
| **Ring** | |
| Söka i data |Frågan som ska köras för ring. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. |
| Klicka och navigera | Åtgärd som vidtas när du klickar på rubriken.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Ring** |**> Center** |
| Text |Texten som visas under värdet i ring diagrammet. |
| Åtgärd |Åtgärden som ska utföras på egenskapen Value för att sammanfattas som ett enda värde.<ul><li>Sum: lägger till värden för alla poster.</li><li>Procent andel: kvoten för de poster som returneras av värdena i **resultat värden som används i Center-åtgärden** för det totala antalet poster i frågan.</li></ul> |
| Resultat värden som används i Center operation |Du kan också välja plus tecknet (+) om du vill lägga till ett eller flera värden. Resultatet av frågan är begränsat till poster med de egenskaps värden som du anger. Om inga värden läggs till inkluderas alla poster i frågan. |
| **Ytterligare alternativ** |**> färger** |
| Färg 1<br>Färg 2<br>Färg 3 |Välj färg för var och en av de värden som visas i ring diagrammet. |
| **Ytterligare alternativ** |**> avancerad färg mappning** |
| Fältvärde |Skriv namnet på ett fält för att visa det som en annan färg om det är inkluderat i ring diagrammet. |
| Färg |Välj färg för fältet unikt. |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyr diagram |Välj den här länken om du vill visa ett miniatyr diagram i stället för ett vågrätt fält. Mer information finns i [vanliga inställningar](#sparklines). |
| Färg |Färgen på staplarna eller miniatyr diagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyr diagrammet. Mer information finns i [vanliga inställningar](#sparklines). |
| Namn och värde avgränsare |Avgränsaren med ett tecken som används för att parsa egenskapen text till flera värden. Mer information finns i [vanliga inställningar](#sparklines). |
| Klicka och navigera | Åtgärd som vidtas när du klickar på ett objekt i listan.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumn rubriker** |
| Namn |Texten som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [vanliga inställningar](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Två tids linjer och list del
Rubriken visar resultatet av två logg frågor med tiden som stapeldiagram, med en prat bubbla som visar ett enda tal som sammanfattar en värde kolumn i en logg fråga. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid.

![Två tids linjer och listvy](media/view-designer-parts/view-two-timelines-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Ikon |Bild filen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Rubrik navigering** | |
| Klicka och navigera | Åtgärd som vidtas när du klickar på rubriken.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Det första diagrammets <br> andra diagram** | |
| Förklaring |Texten som visas under bild texten för den första serien. |
| Färg |Färgen som ska användas för kolumnerna i serien. |
| Söka i data |Frågan som ska köras för den första serien. Antalet poster under varje tidsintervall representeras av diagrammets kolumner. |
| Åtgärd |Åtgärden som ska utföras på egenskapen Value för att sammanfattas som ett enda värde för kommentaren.<ul><li>Sum: summan av värdena från alla poster.</li><li>Genomsnitt: medelvärdet av värden från alla poster.</li><li>Senaste exempel: värdet från det sista intervallet som ingår i diagrammet.</li><li>Första exemplet: värdet från det första intervallet som ingår i diagrammet.</li><li>Count: antalet poster som returneras av frågan.</li></ul> |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyr diagram |Välj den här länken om du vill visa ett miniatyr diagram i stället för ett vågrätt fält. Mer information finns i [vanliga inställningar](#sparklines). |
| Färg |Färgen på staplarna eller miniatyr diagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyr diagrammet. Mer information finns i [vanliga inställningar](#sparklines). |
| Klicka och navigera | Åtgärd som vidtas när du klickar på ett objekt i listan.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumn rubriker** |
| Namn |Texten som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [vanliga inställningar](#thresholds). |

## <a name="information-part"></a>Informations del
Rubriken visar statisk text och en valfri länk. I listan visas ett eller flera objekt med en statisk rubrik och text.

![Vyn information](media/view-designer-parts/view-information.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Färg |Bakgrunds färgen för sidhuvudet. |
| **Huvud** | |
| Bild |Bild filen som visas i sidhuvudet. |
| Etikett |Den text som visas i sidhuvudet. |
| **Huvud** |**> länk** |
| Etikett |Länk texten. |
| URL |Länkens URL. |
| **Informations objekt** | |
| Rubrik |Den text som visas för rubriken på varje objekt. |
| Innehåll |Den text som visas för varje objekt. |

## <a name="line-chart-callout-and-list-part"></a>Linje diagram, bildtext och list del
I rubriken visas ett linje diagram med flera serier från en logg fråga med tiden och en prat bubbla med ett sammanfattande värde. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid.

![Linje diagram, bildtext och listvy](media/view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Ikon |Bild filen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Huvud** | |
| Rubrik |Den text som visas överst i sidhuvudet. |
| Underrubrik |Texten som visas under rubriken överst i sidhuvudet. |
| **Linje diagram** | |
| Söka i data |Frågan som ska köras för linje diagrammet. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Den här frågan använder vanligt vis *mått* nyckelordet för att summera resultat. Om frågan använder *intervall* nyckelordet, använder x-axeln i diagrammet det här tidsintervallet. Om frågan inte inkluderar nyckelordet *Interval* använder x-axeln Tim intervall. |
| Klicka och navigera | Åtgärd som vidtas när du klickar på rubriken.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Linje diagram** |**> prat bubbla** |
| Rubrik för bildtext |Texten som visas ovanför bild text svärdet. |
| Serie namn |Egenskaps värde för serien som ska användas för bild text svärdet. Om ingen serie anges används alla poster från frågan. |
| Åtgärd |Åtgärden som ska utföras på egenskapen Value för att sammanfattas som ett enda värde för kommentaren.<ul><li>Genomsnitt: medelvärdet av värden från alla poster.</li><li>Count: antalet poster som returneras av frågan.</li><li>Senaste exempel: värdet från det sista intervallet som ingår i diagrammet.</li><li>Max: det högsta värdet från de intervall som ingår i diagrammet.</li><li>Min: det lägsta värdet från de intervall som ingår i diagrammet.</li><li>Sum: summan av värdena från alla poster.</li></ul> |
| **Linje diagram** |**> Y-axel** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagram etiketter som visar värde typerna och eventuellt för att konvertera värdena. *Enhets* typen anger enhetens kategori och definierar de tillgängliga värdena för den *aktuella enhets* typen. Om du väljer ett värde i *Convert to* konverteras de numeriska värdena från den *aktuella enhets* typen till typen *konvertera till* . |
| Anpassad etikett |Den text som visas för y-axeln bredvid *enhets* typens etikett. Om ingen etikett har angetts visas endast *enhets* typen. |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyr diagram |Välj den här länken om du vill visa ett miniatyr diagram i stället för ett vågrätt fält. Mer information finns i [vanliga inställningar](#sparklines). |
| Färg |Färgen på staplarna eller miniatyr diagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyr diagrammet. Mer information finns i [vanliga inställningar](#sparklines). |
| Namn och värde avgränsare |Avgränsaren med ett tecken som används för att parsa egenskapen text till flera värden. Mer information finns i [vanliga inställningar](#sparklines). |
| Klicka och navigera | Åtgärd som vidtas när du klickar på ett objekt i listan.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumn rubriker** |
| Namn |Texten som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [vanliga inställningar](#thresholds). |

## <a name="line-chart-and-list-part"></a>Linje diagram och list del
I huvudet visas ett linje diagram med flera serier från en logg fråga över tid. I listan visas de tio främsta resultaten från en fråga, med en graf som visar det relativa värdet för en numerisk kolumn eller dess förändring över tid.

![Linje diagram och listvy](media/view-designer-parts/view-line-chart-callout-list.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Ikon |Bild filen som visas bredvid resultatet i sidhuvudet. |
| Använd ikon |Välj den här länken om du vill visa ikonen. |
| **Huvud** | |
| Rubrik |Den text som visas överst i sidhuvudet. |
| Underrubrik |Texten som visas under rubriken överst i sidhuvudet. |
| **Linje diagram** | |
| Söka i data |Frågan som ska köras för linje diagrammet. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Den här frågan använder vanligt vis *mått* nyckelordet för att summera resultat. Om frågan använder *intervall* nyckelordet, använder x-axeln i diagrammet det här tidsintervallet. Om frågan inte inkluderar nyckelordet *Interval* använder x-axeln Tim intervall. |
| Klicka och navigera | Åtgärd som vidtas när du klickar på rubriken.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Linje diagram** |**> Y-axel** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagram etiketter som visar värde typerna och eventuellt för att konvertera värdena. *Enhets* typen anger enhetens kategori och definierar de tillgängliga värdena för den *aktuella enhets* typen. Om du väljer ett värde i *Convert to* konverteras de numeriska värdena från den *aktuella enhets* typen till typen *konvertera till* . |
| Anpassad etikett |Den text som visas för y-axeln bredvid *enhets* typens etikett. Om ingen etikett har angetts visas endast *enhets* typen. |
| **Lista** | |
| Söka i data |Frågan som ska köras för listan. Antalet poster som returneras av frågan visas. |
| Dölj diagram |Välj den här länken om du vill inaktivera diagrammet till höger om den numeriska kolumnen. |
| Aktivera miniatyr diagram |Välj den här länken om du vill visa ett miniatyr diagram i stället för ett vågrätt fält. Mer information finns i [vanliga inställningar](#sparklines). |
| Färg |Färgen på staplarna eller miniatyr diagrammen. |
| Åtgärd |Åtgärden som ska utföras för miniatyr diagrammet. Mer information finns i [vanliga inställningar](#sparklines). |
| Namn och värde avgränsare |Avgränsaren med ett tecken som används för att parsa egenskapen text till flera värden. Mer information finns i [vanliga inställningar](#sparklines). |
| Klicka och navigera | Åtgärd som vidtas när du klickar på ett objekt i listan.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Lista** |**> kolumn rubriker** |
| Namn |Texten som visas överst i den första kolumnen. |
| Värde |Den text som visas överst i den andra kolumnen. |
| **Lista** |**> tröskelvärden** |
| Aktivera tröskelvärden |Välj den här länken om du vill aktivera tröskelvärden. Mer information finns i [vanliga inställningar](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Stack med linje diagram del
Linje diagrammets stack visar tre separata linje diagram med flera serier från en logg fråga över tid, som du ser här:

![Stack med linje diagram](media/view-designer-parts/view-stack-line-charts.png)

| Inställning | Beskrivning |
|:--- |:--- |
| **Allmänt** | |
| Grupp rubrik |Den text som visas överst i panelen. |
| Ny grupp |Välj den här länken om du vill skapa en ny grupp i vyn med början i den aktuella vyn. |
| Ikon |Bild filen som visas bredvid resultatet i sidhuvudet. |
| **Diagram 1 <br> diagram 2 <br> diagram 3** |**> rubrik** |
| Rubrik |Den text som visas överst i diagrammet. |
| Underrubrik |Texten som visas under rubriken överst i diagrammet. |
| **Diagram 1 <br> diagram 2 <br> diagram 3** |**Linje diagram** |
| Söka i data |Frågan som ska köras för linje diagrammet. Den första egenskapen är ett text värde och den andra egenskapen är ett numeriskt värde. Den här frågan använder vanligt vis *mått* nyckelordet för att summera resultat. Om frågan använder *intervall* nyckelordet, använder x-axeln i diagrammet det här tidsintervallet. Om frågan inte inkluderar nyckelordet *Interval* använder x-axeln Tim intervall. |
| Klicka och navigera | Åtgärd som vidtas när du klickar på rubriken.  Mer information finns i [vanliga inställningar](#click-through-navigation). |
| **Diagram** |**> Y-axel** |
| Använd logaritmisk skala |Välj den här länken om du vill använda en logaritmisk skala för y-axeln. |
| Enheter |Ange enheterna för de värden som ska returneras av frågan. Den här informationen används för att visa diagram etiketter som visar värde typerna och eventuellt för att konvertera värdena. *Enhets* typen anger enhetens kategori och definierar de tillgängliga värdena för den *aktuella enhets* typen. Om du väljer ett värde i *Convert to* konverteras de numeriska värdena från den *aktuella enhets* typen till typen *konvertera till* . |
| Anpassad etikett |Den text som visas för y-axeln bredvid *enhets* typens etikett. Om ingen etikett har angetts visas endast *enhets* typen. |

## <a name="common-settings"></a>Vanliga inställningar
I följande avsnitt beskrivs de inställningar som är gemensamma för flera visualiserings delar.

### <a name="name-and-value-separator"></a><a name="name-value-separator"></a>Namn och värde avgränsare
Namn och värde avgränsare är en avgränsare för ett tecken som används för att parsa text egenskapen från en List fråga till flera värden. Om du anger en avgränsare kan du ange namn för varje fält, avgränsade med samma avgränsare i rutan **namn** .

Anta till exempel att du har en egenskap som kallas *plats* som innehåller värden som *Redmond-Building 41* och *Bellevue 12*. Du kan ange ett bindestreck (-) för namn och värde avgränsare och *skapa stad* för namnet. Den här metoden tolkar varje värde i två egenskaper som kallas *stad* och *byggnad*.

### <a name="click-through-navigation"></a><a name="click-through-navigation"></a>Klicka och navigera
Genom att klicka – navigera definieras vilken åtgärd som ska vidtas när du klickar på ett sidhuvud eller ett List objekt i en vy.  Det innebär att antingen öppna en fråga i [Log Analytics](../logs/log-query-overview.md) eller starta en annan vy.

I följande tabell beskrivs inställningarna för klick-navigering.

| Inställning           | Beskrivning |
|:--|:--|
| Loggs ökning (Auto) | Logg fråga som ska köras när du väljer ett rubrik objekt.  Det här är samma logg fråga som objektet baseras på.
| Loggsökning        | Logg fråga som ska köras när du väljer ett objekt i en lista.  Skriv frågan i rutan **navigerings fråga** .   Använd *{markerat objekt}* för att inkludera syntaxen för det objekt som användaren har valt.  Om frågan till exempel har en kolumn med namnet *dator* och navigerings frågan är *{markerat objekt}*, körs en fråga som *Computer = "Min Dator"* när du väljer en dator. Om navigerings frågan är *typ = händelse {markerat objekt}*, körs frågetyp *= Event dator = "Min Dator"* . |
| Visa              | Vy som öppnas när du väljer ett rubrik objekt eller ett objekt i en lista.  Välj namnet på en vy i din arbets yta i rutan **vynamn** . |



### <a name="sparklines"></a><a name="sparklines"></a>Miniatyr diagram
Ett miniatyr diagram är ett litet linje diagram som illustrerar värdet för en List post över tid. För visualiserings delar med en lista kan du välja om du vill visa ett vågrätt fält, vilket anger det relativa värdet för en numerisk kolumn eller ett miniatyr diagram som anger dess värde över tid.

I följande tabell beskrivs inställningarna för miniatyr diagram:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera miniatyr diagram |Välj den här länken om du vill visa ett miniatyr diagram i stället för ett vågrätt fält. |
| Åtgärd |Om miniatyr diagram är aktiverade, är det här åtgärden som ska utföras på varje egenskap i listan för att beräkna värdena för miniatyr diagrammet.<ul><li>Senaste exempel: det sista värdet för serien under tidsintervallet.</li><li>Max: det maximala värdet för serien under tidsintervallet.</li><li>Min: det lägsta värdet för serien under tidsintervallet.</li><li>Sum: summan av värdena för serien under tidsintervallet.</li><li>Sammanfattning: använder samma `measure` kommando som frågan i rubriken.</li></ul> |

### <a name="thresholds"></a><a name="thresholds"></a>Tröskelvärden
Genom att använda tröskelvärden kan du Visa en färgad ikon bredvid varje objekt i en lista. Tröskelvärden ger dig en snabb visuell indikator för objekt som överskrider ett visst värde eller faller inom ett visst intervall. Du kan till exempel Visa en grön ikon för objekt med ett giltigt värde, gult om värdet är inom ett intervall som indikerar en varning och rött om det överskrider ett felvärde.

När du aktiverar tröskelvärden för en del måste du ange ett eller flera tröskelvärden. Om värdet för ett objekt är större än ett tröskelvärde och lägre än det angivna tröskelvärdet, används färgen för det värdet. Om objektet är större än det högsta tröskelvärdet används en annan färg. 

Varje tröskel uppsättning har ett tröskelvärde med värdet **default**. Detta är den färg som anges om inga andra värden överskrids. Du kan lägga till eller ta bort tröskelvärden genom att välja knappen **Lägg till** (+) eller **ta bort** (x).

I följande tabell beskrivs inställningarna för tröskelvärden:

| Inställning | Beskrivning |
|:--- |:--- |
| Aktivera tröskelvärden |Välj den här länken om du vill visa en färg ikon till vänster om varje värde. Ikonen anger värdets hälsa relativt till angivna tröskelvärden. |
| Namn |Namnet på tröskelvärdet. |
| Tröskelvärde |Värdet för tröskelvärdet. Hälso färgen för varje List objekt anges till färgen för det högsta tröskelvärdet som överskrids av objektets värde. Om inga tröskelvärden överskrids används en standard färg. |
| Färg |Färgen som anger tröskelvärdet. |

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) som stöder frågor i visualiserings delar.