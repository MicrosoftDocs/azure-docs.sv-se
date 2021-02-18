---
title: Azure Monitor Visa designer till arbets böcker över gångs guide
description: Över gång från vyer till arbets böcker i Azure Monitor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: d6731673c2b9918b3b19d14f45d51a6424ef86f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624911"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor Visa designer till arbets böcker över gångs guide
[View Designer](view-designer.md) är en funktion i Azure Monitor som gör att du kan skapa anpassade vyer som hjälper dig att visualisera data i arbets ytan Log Analytics, med diagram, listor och tids linjer. De har överförts till arbets böcker för att tillhandahålla en flexibel arbets yta för data analys och skapandet av omfattande visuella rapporter i Azure Portal. Den här artikeln hjälper dig att göra över gången från Visa designer till arbets böcker. 


## <a name="workbooks-overview"></a>Översikt över arbets böcker
[Arbets böcker](../insights/vminsights-workbooks.md) kombinerar text, [logg frågor](/azure/data-explorer/kusto/query/), mått och parametrar till omfattande interaktiva rapporter. Grupp medlemmar med samma åtkomst till Azure-resurser kan också redigera arbets böcker.

Arbets böcker är användbara för scenarier som:

-   Utforska användningen av den virtuella datorn när du inte känner till mått på intresse i förväg: processor användning, disk utrymme, minne, nätverks beroenden osv. Till skillnad från andra verktyg för användnings analys kan du med arbets böcker kombinera flera olika typer av visualiseringar och analyser, vilket gör dem fantastiska för den här typen av kostnads fri utforskning.
-   Vi förklarar ditt team hur en nyligen etablerad virtuell dator presterar genom att visa mått för nyckel räknare och andra logg händelser.
-   Dela resultatet av ett experiment med att ändra storlek på den virtuella datorn med andra medlemmar i din grupp. Du kan förklara målen för experimentet med text och sedan Visa alla användnings mått och analys frågor som används för att utvärdera experimentet, tillsammans med tydliga anrop för anrop för om varje mått var över eller under målet.
-   Rapporterar effekten av ett avbrott i användningen av din virtuella dator, kombinera data, text förklaring och en beskrivning av nästa steg för att förhindra avbrott i framtiden.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Varför ska jag konvertera Visa designer-instrumentpaneler till arbets böcker?

Visa Designer ger möjlighet att generera olika frågebaserade vyer och visualiseringar. Men många anpassningar på hög nivå är dock begränsade, som till exempel formatering av rutnät och ikoner eller val av alternativ grafik för att representera data. View Designer är begränsat till totalt nio distinkta paneler för att representera dina data.

Arbetsböcker är en plattform som ger dig tillgång till den fulla potentialen av dina data. arbets böcker behåller inte bara alla funktioner, utan har även stöd för ytterligare funktioner via text, statistik, parametrar och mycket mer. Till exempel kan användare med arbets böcker konsolidera tätare rutnät och lägga till sökfält för att enkelt filtrera och analysera data. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Fördelar med att använda arbets böcker över View Designer

* Stöder både loggar och mått.
* Tillåter både personliga vyer för enskild åtkomst kontroll och delade arbets böcker.
* Alternativ för anpassad layout med flikar, storleks kontroll och skalnings kontroller.
* Stöd för frågor över flera Log Analytics arbets ytor, Application Insights program och prenumerationer.
* Aktiverar anpassade parametrar som uppdaterar associerade diagram och visualiseringar dynamiskt.
* Stöd för Template Gallery från offentliga GitHub.

Även om den här guiden innehåller enkla steg för att direkt återskapa flera av de vyer som används ofta, kan du använda arbets böcker för att kunna skapa och utforma någon av sina egna anpassade visualiseringar och mått. Följande skärm bild är från [arbets ytans användnings mall](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) och visar ett exempel på vad arbets böcker kan skapa:


![Exempel på arbets boks program](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Börja använda arbets böcker
Öppna arbets böcker från arbets böckerna är aktiverade i Log Analytics arbets ytor som ett objekt i navigerings fältet på sidan, direkt under platsen View Designer.

![Navigering för arbets böcker](media/view-designer-conversion-overview/workbooks-nav.png)

När du har valt ett galleri visas en lista över alla sparade arbets böcker och mallar för din arbets yta.

![Galleri för arbets böcker](media/view-designer-conversion-overview/workbooks-gallery.png)

Om du vill starta en ny arbets bok kan du välja den **tomma** mallen under **snabb start** eller den **nya** ikonen i det övre navigerings fältet. Om du vill visa mallar eller återgå till sparade arbets böcker markerar du objektet i galleriet eller söker efter namnet i Sök fältet.

Om du vill spara en arbets bok måste du spara rapporten med en speciell rubrik, prenumeration, resurs grupp och plats.
Arbets boken fylls i automatiskt i samma inställningar som arbets ytan LA, med samma prenumeration, resurs grupp, men användare kan ändra dessa rapport inställningar. Arbets böcker sparas som standard i *Mina rapporter*, åtkomliga endast av den enskilda användaren. De kan också sparas direkt till delade rapporter eller delas senare.

![Spara arbets böcker](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Nästa steg

- [Konverteringsalternativ](view-designer-conversion-options.md)