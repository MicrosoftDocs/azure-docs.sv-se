---
title: Log Analytics-självstudie
description: Lär dig i den här självstudien om hur du använder funktioner i Log Analytics i Azure Monitor för att skapa en körnings logg fråga och analysera resultatet i Azure Portal.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/07/2020
ms.openlocfilehash: a5788e5ac64c1c74a06c94c5634a05315ee098b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799300"
---
# <a name="log-analytics-tutorial"></a>Log Analytics-självstudie
Log Analytics är ett verktyg i Azure Portal för att redigera och köra logg frågor från data som samlas in av Azure Monitor loggar och interaktivt analysera resultaten. Du kan använda Log Analytics frågor för att hämta poster som matchar vissa villkor, identifiera trender, analysera mönster och tillhandahålla en mängd olika insikter om dina data. 

Den här självstudien vägleder dig genom Log Analytics-gränssnittet, hjälper dig att komma igång med några grundläggande frågor och visar hur du kan arbeta med resultaten. Du kommer att få lära dig följande:

> [!div class="checklist"]
> * Förstå logg data schema
> * Skriv och kör enkla frågor och ändra tidsintervallet för frågor
> * Filtrera, sortera och gruppera frågeresultat
> * Visa, ändra och dela visuella objekt av frågeresultat
> * Läs in, exportera och kopiera frågor och resultat

> [!IMPORTANT]
> I den här självstudien används funktioner i Log Analytics för att skapa och köra en fråga i stället för att arbeta med själva frågan. Du utnyttjar Log Analytics funktioner för att bygga en fråga och använder en annan exempel fråga. När du är redo att lära dig syntaxen för frågor och börja redigera själva frågan, går du igenom [självstudien om Kusto-frågespråket](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). Den här självstudien vägleder dig genom flera exempel frågor som du kan redigera och köra i Log Analytics, med flera av de funktioner som du lär dig i den här självstudien.


## <a name="prerequisites"></a>Förutsättningar
I den här självstudien används [Log Analytics demo miljö](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), som innehåller massor av exempel data som stöder exempel frågorna. Du kan också använda en egen Azure-prenumeration, men du kanske inte har data i samma tabeller.

## <a name="open-log-analytics"></a>Öppna Log Analytics
Öppna [Log Analytics demo miljö](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) eller Välj **loggar** på Azure Monitor-menyn i din prenumeration. Detta ställer in det ursprungliga omfånget på en Log Analytics arbets yta, vilket innebär att din fråga väljer från alla data i den arbets ytan. Om du väljer **loggar** från en Azure-resurs meny, anges området till endast poster från den resursen. Se [området för logg frågor](./scope.md) för mer information om omfånget.

Du kan visa omfånget i det övre vänstra hörnet på skärmen. Om du använder en egen miljö ser du ett alternativ för att välja ett annat omfång, men det här alternativet är inte tillgängligt i demo miljön.

[![Frågeomfång](media/log-analytics-tutorial/scope.png)](media/log-analytics-tutorial/scope.png#lightbox)

## <a name="table-schema"></a>Tabell schema
På vänster sida av skärmen finns fliken **tabeller** där du kan granska de tabeller som är tillgängliga i det aktuella omfånget. De är grupperade efter **lösning** som standard, men du ändrar deras gruppering eller filtrerar dem. 

Expandera **logg hanterings** lösningen och leta upp tabellen **AzureActivity** . Du kan expandera tabellen om du vill visa dess schema eller hovra över dess namn för att visa mer information om den. 

[![Tabellvy](media/log-analytics-tutorial/table-details.png)](media/log-analytics-tutorial/table-details.png#lightbox)

Klicka på **Läs mer** om du vill gå till tabell referensen som dokumenterar varje tabell och kolumner. Klicka på **Förhandsgranska data** om du vill ha en snabb titt på några av de senaste posterna i tabellen. Detta kan vara användbart för att säkerställa att det här är de data du förväntar dig innan du kör en fråga med den.

[![Exempeldata](media/log-analytics-tutorial/sample-data.png)](media/log-analytics-tutorial/sample-data.png#lightbox)

## <a name="write-a-query"></a>Skriv en fråga
Nu ska vi gå vidare och skriva en fråga med hjälp av tabellen **AzureActivity** . Dubbelklicka på namnet för att lägga till det i frågefönstret. Du kan också skriva direkt i fönstret och sedan få IntelliSense som hjälper dig att fylla i tabell namnen i de aktuella omfattnings-och KQL-kommandona.

Det här är den enklaste frågan som vi kan skriva. Det returnerar bara alla poster i en tabell. Kör det genom att klicka på knappen **Kör** eller genom att trycka på SKIFT + RETUR med markören placerad var som helst i frågetexten.

[![Frågeresultat](media/log-analytics-tutorial/query-results.png)](media/log-analytics-tutorial/query-results.png#lightbox)

Du kan se att vi har resultat. Antalet poster som returneras av frågan visas i det nedre högra hörnet. 

## <a name="filter"></a>Filtrera

Vi lägger till ett filter i frågan för att minska antalet poster som returneras. Välj fliken **filter** i det vänstra fönstret. Detta visar olika kolumner i frågeresultaten som du kan använda för att filtrera resultaten. De översta värdena i dessa kolumner visas med antalet poster med det värdet. Klicka på **administrativ** under **CategoryValue** och **Använd & kör**. 

[![Frågefönstret](media/log-analytics-tutorial/query-pane.png)](media/log-analytics-tutorial/query-pane.png#lightbox)

En **WHERE** -instruktion läggs till i frågan med det värde som du har valt. Resultaten innehåller nu bara poster med det värdet så att du kan se att antalet poster är reducerat.

[![Frågeresultat filtrerade](media/log-analytics-tutorial/query-results-filter-01.png)](media/log-analytics-tutorial/query-results-filter-01.png#lightbox)


## <a name="time-range"></a>Tidsintervall
Alla tabeller i en Log Analytics-arbetsyta har en kolumn med namnet **TimeGenerated** som är den tidpunkt då posten skapades. Alla frågor har ett tidsintervall som begränsar resultatet till poster med ett **TimeGenerated** -värde inom intervallet. Tidsintervallet kan antingen anges i frågan eller med väljaren överst på skärmen.

Frågan returnerar som standard poster Formulär de senaste 24 timmarna. Välj List rutan **tidsintervall** och ändra den till **7 dagar**. Returnera resultaten genom att klicka på **Kör** igen. Du kan se att resultaten returneras, men vi har ett meddelande som visar att vi inte ser alla resultat. Detta beror på att Log Analytics kan returnera högst 30 000 poster och frågan returnerade fler poster än den. 

[![Tidsintervall](media/log-analytics-tutorial/query-results-max.png)](media/log-analytics-tutorial/query-results-max.png#lightbox)


## <a name="multiple-query-conditions"></a>Villkor för flera frågor
Vi minskar våra resultat ytterligare genom att lägga till ett annat filter villkor. En fråga kan innehålla valfritt antal filter för att ange exakt de poster som du vill ha. Välj **lyckades** under **ActivityStatusValue** och klicka på **tillämpa & kör**. 

[![Frågeresultat flera filter](media/log-analytics-tutorial/query-results-filter-02.png)](media/log-analytics-tutorial/query-results-filter-02.png#lightbox)


## <a name="analyze-results"></a>Analysera resultat
Förutom att hjälpa dig att skriva och köra frågor, innehåller Log Analytics funktioner för att arbeta med resultaten. Starta genom att expandera en post för att visa värdena för alla kolumner.

[![Expandera post](media/log-analytics-tutorial/expand-record.png)](media/log-analytics-tutorial/expand-record.png#lightbox)

Klicka på kolumn namnet för att sortera resultaten efter kolumnen. Klicka på filter ikonen bredvid den för att ange ett filter villkor. Detta liknar att lägga till ett filter villkor i själva frågan, förutom att det här filtret rensas om frågan körs igen. Använd den här metoden om du snabbt vill analysera en uppsättning poster som en del av den interaktiva analysen.

Du kan till exempel ange ett filter i kolumnen **CallerIpAddress** för att begränsa posterna till en enskild anropare. 

[![Filter för frågeresultat](media/log-analytics-tutorial/query-results-filter.png)](media/log-analytics-tutorial/query-results-filter.png#lightbox)

I stället för att filtrera resultaten kan du gruppera poster efter en viss kolumn. Rensa filtret som du nyss skapade och aktivera sedan skjutreglaget **grupp kolumner** . 

[![Grupp kolumner](media/log-analytics-tutorial/query-results-group-columns.png)](media/log-analytics-tutorial/query-results-group-columns.png#lightbox)

Dra nu kolumnen **CallerIpAddress** till grupperingsfältet. Resultaten är nu ordnade efter den kolumnen och du kan komprimera varje grupp för att hjälpa dig med din analys.

[![Gruppera resultat grupperade](media/log-analytics-tutorial/query-results-grouped.png)](media/log-analytics-tutorial/query-results-grouped.png#lightbox)

## <a name="work-with-charts"></a>Arbeta med diagram
Nu ska vi titta på en fråga som använder numeriska data som vi kan visa i ett diagram. I stället för att skapa en fråga väljer vi en exempel fråga.

Klicka på **frågor** i det vänstra fönstret. I det här fönstret finns exempelfrågor som du kan lägga till i frågefönstret. Om du använder en egen arbets yta bör du ha en mängd olika frågor i flera kategorier, men om du använder demo miljön kan du bara se en enskild **Log Analytics arbets ytans** kategori. Expandera den för att Visa frågorna i kategorin.

Klicka på frågan **antal begär Anden efter ResponseCode**. Frågan läggs till i frågefönstret. Observera att den nya frågan skiljs från den andra med en tom rad. En fråga i KQL avslutas när den påträffar en tom rad, så att dessa visas som separata frågor. 

[![Ny fråga](media/log-analytics-tutorial/example-query.png)](media/log-analytics-tutorial/example-query.png#lightbox)

Den aktuella frågan är den som markören är placerad på. Du kan se att den första frågan är markerad och anger att den är den aktuella frågan. Klicka någonstans i den nya frågan för att markera den och klicka sedan på **Kör** -knappen för att köra den.

[![Diagram över frågeresultat](media/log-analytics-tutorial/example-query-output-chart.png)](media/log-analytics-tutorial/example-query-output-chart.png#lightbox)

Observera att utdata är ett diagram i stället för en tabell som den sista frågan. Det beror på att exempel frågan använder kommandot [Render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) i slutet. Observera att det finns olika alternativ för att arbeta med diagrammet, till exempel att ändra det till en annan typ.

Försök att välja **resultat** för att visa utdata från frågan som en tabell. 

[![Tabellen frågeresultat](media/log-analytics-tutorial/example-query-output-table.png)](media/log-analytics-tutorial/example-query-output-table.png#lightbox)



## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du använder Log Analytics slutför du självstudierna för att använda logg frågor.
> [!div class="nextstepaction"]
> [Skriv Azure Monitor logg frågor](get-started-queries.md)
