---
title: Översikt över Log Analytics i Azure Monitor
description: Beskriver Log Analytics som är ett verktyg i Azure Portal som används för att redigera och köra logg frågor för att analysera data i Azure Monitor loggar.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: ef8be3d1fd815362971d5ae2beb27edf47766c94
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723171"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Översikt över Log Analytics i Azure Monitor
Log Analytics är ett verktyg i Azure Portal som används för att redigera och köra logg frågor med data i Azure Monitor loggar. Du kan skriva en enkel fråga som returnerar en uppsättning poster och sedan använda funktioner i Log Analytics för att sortera, filtrera och analysera dem. Du kan också skriva en mer avancerad fråga för att utföra statistisk analys och visualisera resultatet i ett diagram för att identifiera en viss trend. Oavsett om du arbetar med resultatet av dina frågor interaktivt eller använder dem med andra Azure Monitor funktioner som logg frågas aviseringar eller arbets böcker, Log Analytics är det verktyg som du tänker använda för att skriva och testa dem. 


> [!TIP]
> Den här artikeln innehåller en beskrivning av Log Analytics och var och en av dess funktioner. Om du vill gå direkt till en själv studie kurs kan du läsa [Log Analytics själv studie kursen](./log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Startar Log Analytics
Starta Log Analytics från **loggar** i **Azure Monitor** -menyn i Azure Portal. Du ser även det här alternativet på menyn för de flesta Azure-resurser. Oavsett var du startar den, är det samma Log Analytics-verktyg. Den meny som du använder för att starta Log Analytics avgör vilka data som kommer att vara tillgängliga. Om du startar den från **Azure Monitor** -menyn eller **Log Analytics arbets ytans** meny, har du åtkomst till alla poster i en arbets yta. Om du väljer **loggar** från en annan typ av resurs, är dina data begränsade till att logga data för den resursen. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](./scope.md) .

[![Starta Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

När du startar Log Analytics är det första du ser en dialog ruta med [exempel frågor](../logs/example-queries.md). Dessa kategoriseras efter lösning och du kan bläddra eller söka efter frågor som uppfyller dina specifika krav. Du kanske kan hitta en som har exakt det du behöver, eller läsa in en i redigeraren och ändra den efter behov. Att bläddra bland exempel frågor är i själva verket ett bra sätt att lära dig hur du skriver egna frågor. Naturligtvis, om du vill börja med ett tomt skript och skriva det själv, kan du stänga exempel frågorna. Klicka bara på **frågorna** överst på skärmen om du vill få tillbaka dem.

## <a name="log-analytics-interface"></a>Log Analytics gränssnitt
I följande bild identifieras de olika komponenterna i Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. översta åtgärds fältet
Kontroller för att arbeta med frågan i frågefönstret.

| Alternativ | Beskrivning |
|:---|:---|
| Omfång | Anger omfattningen av data som används för frågan. Detta kan vara alla data i en Log Analytics arbets yta eller data för en viss resurs över flera arbets ytor. Se [frågans omfång](./scope.md). |
| Knappen Kör | Klicka om du vill köra den valda frågan i frågefönstret. Du kan också trycka på SKIFT + RETUR för att köra en fråga. |
| Tids väljare | Välj tidsintervallet för de data som är tillgängliga för frågan. Detta åsidosätts om du inkluderar ett tids filter i frågan. Se [omfång och tidsintervall för logg frågor i Azure Monitor Log Analytics](./scope.md). |
| Knappen Spara | Spara frågan i Query Explorer för arbets ytan. |
 Knappen Kopiera | Kopiera en länk till frågan, frågetexten eller frågeresultatet till Urklipp. |
| Knappen Ny varnings regel | Skapa en ny flik med en tom fråga. |
| Knappen Exportera | Exportera resultatet av frågan till en CSV-fil eller frågan för att Power Query formel språk format för användning med Power BI. |
| Knappen Fäst på instrument panelen | Lägg till resultatet av frågan på en Azure-instrumentpanel. |
| Knappen Formatera fråga | Ordna den markerade texten för läsbarhet. |
| Exempel på frågor-knapp | Öppna dialog rutan exempel frågor som visas första gången du öppnar Log Analytics. |
| Knappen fråga i Utforskaren | Öppna **query Explorer** som ger åtkomst till sparade frågor i arbets ytan. |


### <a name="2-sidebar"></a>2. marginal List
Listor över tabeller i arbets ytan, exempel frågor och filter alternativ för den aktuella frågan.

| Flik | Beskrivning |
|:---|:---|
| Tabeller | Visar en lista över de tabeller som är en del av det valda omfånget. Välj **Gruppera efter** för att ändra grupperingen av tabellerna. Hovra över ett tabell namn för att visa en dialog ruta med en beskrivning av tabellen och alternativ för att visa dess dokumentation och för att förhandsgranska data. Expandera en tabell om du vill visa dess kolumner. Dubbelklicka på ett tabell-eller kolumn namn om du vill lägga till det i frågan. |
| Frågor | Lista med exempel frågor som du kan öppna i frågefönstret. Det här är samma lista som visas när du öppnar Log Analytics. Välj **Gruppera efter** för att ändra grupperingen av frågorna. Dubbelklicka på en fråga för att lägga till den i frågefönstret eller hovra över den för andra alternativ. |
| Filtrera | Skapar filter alternativ baserat på resultatet av en fråga. När du har kört en fråga visas kolumner med olika värden från resultaten. Välj ett eller flera värden och klicka sedan på **tillämpa & kör** för att lägga till ett **WHERE** -kommando i frågan och kör det igen. |

### <a name="3-query-window"></a>3. Query-fönster
I frågefönstret redigerar du din fråga. Detta inkluderar IntelliSense för KQL-kommandon och färg kodning för att förbättra läsbarheten. Klicka på **+** överst i fönstret för att öppna en annan flik.

Som ett enda fönster kan innehålla flera frågor. En fråga får inte innehålla tomma rader, så du kan avgränsa flera frågor i ett fönster med en eller flera tomma rader. Den aktuella frågan är den som har markören placerad var som helst.

Kör den aktuella frågan genom att klicka på knappen **Kör** eller trycka på SKIFT + RETUR.

### <a name="4-results-window"></a>4. resultat fönster
Resultatet av frågan visas i resultat fönstret. Som standard visas resultaten som en tabell. Om du vill visa som ett diagram väljer du antingen **diagram** i resultat fönstret eller lägger till ett **åter givnings** kommando i frågan.

#### <a name="results-view"></a>Resultat visning
Visar frågeresultat i en tabell som är ordnad efter kolumner och rader. Klicka till vänster om en rad för att expandera dess värden. Klicka på list rutan **kolumner** om du vill ändra listan med kolumner. Sortera resultaten genom att klicka på ett kolumn namn. Filtrera resultaten genom att klicka på tratten bredvid ett kolumn namn. Rensa filtren och Återställ sorteringen genom att köra frågan igen.

Välj **grupp kolumner** för att Visa grupperingstypen ovanför frågeresultaten. Gruppera resultaten efter valfri kolumn genom att dra den till fältet. Skapa kapslade grupper i resultatet genom att lägga till ytterligare kolumner. 

#### <a name="chart-view"></a>Diagramvy
Visar resultatet som en av flera tillgängliga diagram typer. Du kan ange diagram typen i ett **Render** -kommando i din fråga eller välja den i list rutan **visualiserings typ** .

| Alternativ | Beskrivning |
|:---|:---|
| **Typ av visualisering** | Typ av diagram som ska visas. |
| **X-axel** | Kolumn i de resultat som ska användas för X-axeln 
| **Y-axel** | Kolumn i de resultat som ska användas för Y-axeln. Detta är vanligt vis en numerisk kolumn. |
| **Uppdela per** | Kolumn i resultatet som definierar serien i diagrammet. En serie skapas för varje värde i kolumnen. |
| **Aggregering** | Typ av agg regering som ska utföras på de numeriska värdena i Y-axeln. |

## <a name="relationship-to-azure-data-explorer"></a>Relation till Azure Datautforskaren
Om du redan är bekant med Azure Datautforskaren Web UI ska du Log Analytics se bekant. Det beror på att det är byggt på Azure Datautforskaren och använder samma KQL (Kusto Query Language). Log Analytics lägger till funktioner som är speciella för Azure Monitor, till exempel filtrering efter tidsintervall och möjlighet att skapa en varnings regel från en fråga. Båda verktygen innehåller en utforskare som gör att du kan söka igenom strukturen för tillgängliga tabeller, men Azure Datautforskaren Web UI fungerar huvudsakligen med tabeller i Azure Datautforskaren-databaser medan Log Analytics fungerar med tabeller i en Log Analytics arbets yta. 

## <a name="next-steps"></a>Nästa steg
- Gå igenom en [själv studie kurs om hur du använder Log Analytics i Azure Portal](./log-analytics-tutorial.md).
- Gå igenom en [själv studie kurs om att skriva frågor](./get-started-queries.md).