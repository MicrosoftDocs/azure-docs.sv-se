---
title: 'Självstudie: kom igång med Log Analytics frågor'
description: I den här självstudien lär du dig om hur du skriver och hanterar Azure Monitor-loggfrågor med Log Analytics i Azure Portal.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 29e24166218a6757cded9d1b002321800ab0c073
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80055510"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>Självstudie: kom igång med Log Analytics frågor

Den här självstudien visar hur du använder Log Analytics för att skriva, köra och hantera Azure Monitor-loggfrågor i Azure Portal. Du kan använda Log Analytics-frågor för att söka efter villkor, identifiera trender, analysera mönster och tillhandahålla många andra insikter från dina data. 

I den här självstudien får du lära dig hur du använder Log Analytics för att:

> [!div class="checklist"]
> * Förstå loggdataschema
> * Skriva och kör enkla frågor och ändra tidsintervallet för frågor
> * Filtrera, sortera och gruppera frågeresultat
> * Visa, ändra och dela visuella objekt av frågeresultat
> * Spara, läsa in, exportera och kopiera frågor och resultat

Mer information om loggfrågor finns i [Översikt över loggfrågor i Azure Monitor](log-query-overview.md).<br/>
En detaljerad självstudiekurs om hur du skriver loggfrågor finns [i kom igång med loggfrågor i Azure Monitor](get-started-queries.md).

## <a name="open-log-analytics"></a>Öppna Log Analytics
Om du vill använda Log Analytics måste du vara inloggad med ett Azure-konto. Om du inte har ett Azure-konto kan du [skapa ett kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

För att slutföra de flesta av stegen i den här självstudien kan du använda [den här demomiljön](https://portal.loganalytics.io/demo), som innehåller massor av exempeldata. Med demomiljön kan du inte spara frågor eller fästa resultat på en instrumentpanel.

Du kan också använda en egen miljö om du använder Azure Monitor för att samla in loggdata på minst en Azure-resurs. Öppna en Log Analytics-arbetsyta genom att välja **loggar** i Azure Monitor vänster-navigering. 

## <a name="understand-the-schema"></a>Förstå schemat
Ett *schema* är en samling tabeller grupperade under logiska kategorier. Demoschemat har flera kategorier från övervakningslösningar. Kategorin **LogManagement** innehåller till exempel Windows- och syslog-händelser, prestandadata och agentpulsslag.

Schematabellerna visas på fliken **tabeller** i arbetsytan Log Analytics. Tabellerna innehåller kolumner, var och en med en datatyp som visas med ikonen bredvid kolumnnamnet. **Händelse**-tabellen innehåller till exempel textkolumner såsom **dator** och numeriska kolumner såsom **EventCategory**.

![Schema](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>Skriv och kör grundläggande frågor

Log Analytics öppnas med en ny tom fråga i **Frågeredigeraren**.

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>Skriv en fråga
Azure Monitor-loggfrågor använder en version av Kusto-frågespråket. Frågor kan inledas med antingen ett tabellnamn eller ett [Sök](/azure/kusto/query/searchoperator) kommando. 

Följande fråga hämtar alla poster från **händelse**-tabellen:

```Kusto
Event
```

Pipe-tecknet (|) separerar kommandon, så utdata från det första kommandot är indata för nästa kommando. Du kan lägga till ett valfritt antal kommandon i en enskild fråga. Följande fråga hämtar posterna från **händelse**-tabellen och söker sedan efter ett term **fel** i alla egenskaper:

```Kusto
Event 
| search "error"
```

En enda radbrytning gör det lättare att läsa frågor. Mer än en radbrytning delar upp frågan i separata frågor.

Ett annat sätt att skriva samma fråga är:

```Kusto
search in (Event) "error"
```

I det andra exemplet söker **Sök**-kommandot endast efter poster i tabellen **händelser** för term-**felet**.

Som standard begränsar Log Analytics frågor till ett tidsintervall under de senaste 24 timmarna. Om du vill ange ett annat tidsintervall kan du lägga till ett explicit **TimeGenerated**-filter till frågan eller använda **tidsintervalls**-kontrollen.

### <a name="use-the-time-range-control"></a>Använda tidsintervallskontrollen
Om du vill använda kontrollen **tidsintervall** väljer du den i det översta fältet och väljer sedan ett värde i listrutan eller väljer **anpassad** om du vill skapa ett anpassat tidsintervall.

![Tidsväljare](media/get-started-portal/time-picker.png)

- Tidsintervallsvärden är i UTC, vilket kan skilja sig från den lokala tidszonen.
- Om frågan uttryckligen ställer in ett filter för **TimeGenerated**, visar tidsväljaren **set i Query** och är inaktiverad för att förhindra en konflikt.

### <a name="run-a-query"></a>Kör en fråga
Om du vill köra en fråga placerar du markören någonstans i frågan och väljer **Kör** i det översta fältet **eller trycker på**+**RETUR**. Frågan körs tills den hittar en tom rad.

## <a name="filter-results"></a>Filtrera resultat
Log Analytics begränsar resultatet till högst 10 000 poster. En allmän fråga som `Event` returnerar för många resultat för att vara användbar. Du kan filtrera frågeresultaten antingen genom att begränsa tabellelementen i frågan eller genom att uttryckligen lägga till ett filter i resultatet. Filtrering genom tabellelementen returnerar en ny resultatuppsättning, medan ett explicit filter används för den befintliga resultatuppsättningen.

### <a name="filter-by-restricting-table-elements"></a>Filtrera genom att begränsa tabell element
Filtrera `Event`-frågeresultat till **fel** händelser genom att begränsa tabellelement i frågan:

1. I frågeresultaten väljer du den nedrullningsbara pilen bredvid alla poster som har **fel** i kolumnen **EventLevelName** . 
   
1. Hovra över i den expanderade informationen och välj **...** bredvid **EventLevelName** och välj sedan **inkludera "fel"**. 
   
   ![Lägg till filter i fråga](media/get-started-portal/add-filter.png)
   
1. Observera att frågan **i Frågeredigeraren nu har ändrats** till:
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. Välj **Kör** för att köra den nya frågan.

### <a name="filter-by-explicitly-filtering-results"></a>Filtrera genom att uttryckligen filtrera resultat
Filtrera `Event` frågeresultaten till **fel** händelser genom att filtrera frågeresultaten:

1. I frågeresultaten väljer du **filter**-ikonen bredvid kolumn rubriken **EventLevelName**. 
   
1. I det första fältet i popup-fönstret väljer du **är lika med** och i nästa fält anger du *fel*. 
   
1. Välj **filter**.
   
   ![Filter](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>Sortera, gruppera och Välj kolumner
Om du vill sortera frågeresultaten efter en speciell kolumn, t. ex. **TimeGenerated [UTC]**, väljer du kolumnrubriken. Välj rubriken igen om du vill växla mellan stigande och fallande ordning.

![Sorteringskolumn](media/get-started-portal/sort-column.png)

Ett annat sätt att ordna resultaten är av grupper. Om du vill gruppera resultatet efter en speciell kolumn drar du kolumnrubriken till fältet ovanför resultattabellen med etiketten **dra en kolumn rubrik och släpper den här för att gruppera efter den kolumnen**. Dra andra kolumner till den övre stapeln om du vill skapa under grupper. Du kan arrangera om hierarkin och sorteringen av grupper och under grupper i fältet.

![Grupper](media/get-started-portal/groups.png)

Om du vill dölja eller visa kolumner i resultaten väljer du **kolumner** ovanför tabellen och markerar eller avmarkerar sedan de kolumner som du vill använda i listrutan.

![Välja kolumner](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>Visa och ändra diagram
Du kan också se frågeresultat i visuella format. Ange följande fråga som exempel:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Som standard visas resultaten i en tabell. Markera **diagrammet** ovanför tabellen om du vill se resultatet i en grafisk vy.

![Stapeldiagram](media/get-started-portal/bar-chart.png)

Resultaten visas i ett liggande stapeldiagram. Välj andra alternativ som **staplad kolumn** eller **cirkel** för att visa andra vyer av resultaten.

![Cirkeldiagram](media/get-started-portal/pie-chart.png)

Du kan ändra egenskaperna för vyn, till exempel x- och y-axlar, eller gruppering och dela inställningar, manuellt från kontrollfältet.

Du kan också ställa in den önskade vyn i själva frågan med operatorn [Render](/azure/kusto/query/renderoperator) .

## <a name="pin-results-to-a-dashboard"></a>Fästa resultat på en instrumentpanel
Om du vill fästa en resultattabell eller ett diagram från Log Analytics till en delad Azure-instrumentpanel väljer du **Fäst på instrument panelen** i det översta fältet. 

![Fäst vid instrumentpanelen](media/get-started-portal/pin-dashboard.png)

I rutan **Fäst på en annan instrument panel** väljer eller skapar du en delad instrumentpanel att fästa på och väljer **tillämpa**. Tabellen eller diagrammet visas på den valda Azure-instrumentpanelen.

![Diagrammet har fästs på instrumentpanelen](media/get-started-portal/pin-dashboard2.png)

En tabell eller ett diagram som du fäster på en delad instrumentpanel har följande förenklingar: 

- Data är begränsade till de senaste 14 dagarna.
- En tabell visar bara upp till fyra kolumner och de sju översta raderna.
- Diagram med många diskreta kategorier grupperar automatiskt mindre fyllda kategorier till en **annan** lagerplats.

## <a name="save-load-or-export-queries"></a>Spara, läs in eller exportera frågor
När du har skapat en fråga kan du spara eller dela frågan eller resultatet med andra. 

### <a name="save-queries"></a>Spara frågor
Så här sparar du en fråga:

1. Välj **Spara** i det översta fältet.
   
1. I dialogrutan **Spara** ger du frågan ett **namn** med hjälp av tecknen A – Z, a – z, 0-9, blanksteg, bindestreck, understreck, punkt, parentes eller pipe. 
   
1. Välj om du vill spara frågan som en **fråga** eller en **funktion**. Funktioner är frågor som andra frågor kan referera till. 
   
   Om du vill spara en fråga som en funktion anger du ett **funktionsalias**, vilket är ett kort namn för andra frågor som ska användas för att anropa den här frågan.
   
1. Ange en **kategori** för **query Explorer** som ska användas för frågan.
   
1. Välj **Spara**.
   
   ![Spara funktion](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>Läs in frågor
Om du vill läsa in en sparad fråga väljer du **query Explorer** längst upp till höger. Fönstret **query Explorer** öppnas och visar alla frågor per kategori. Expandera kategorierna eller ange ett frågenamn i sökfältet och välj sedan en fråga för att läsa in den i **Frågeredigeraren**. Du kan markera en fråga som en **favorit** genom att välja stjärnan bredvid frågans namn.

![Query Explorer](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>Exportera och dela frågor
Om du vill exportera en fråga väljer du **Exportera** i det översta fältet och väljer sedan **Exportera till CSV – alla kolumner**, **Exportera till CSV-visade kolumner**eller **Exportera till Power BI (M fråga)** i listrutan.

Om du vill dela en länk till en fråga väljer du **Kopiera länk** i det översta fältet och väljer sedan **Kopiera länk till fråga**, **Kopiera frågetext**eller **Kopiera frågeresultat** till kopiera till Urklipp. Du kan skicka frågelänken till andra som har åtkomst till samma arbetsyta.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie om du vill lära dig mer om att skriva Azure Monitor-loggfrågor.
> [!div class="nextstepaction"]
> [Skriv Azure Monitor-loggfrågor](get-started-queries.md)
