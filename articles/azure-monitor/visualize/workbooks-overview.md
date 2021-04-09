---
title: Översikt över Azure Monitor-arbetsböcker
description: Lär dig hur arbets böcker ger en flexibel arbets yta för data analys och hur du skapar rika visuella rapporter i Azure Portal.
manager: carmonm
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: e401eb4a5608db6e5767298f53046099a42679b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100621497"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor arbets böcker

Arbetsböcker ger en flexibel arbetsyta för dataanalys och skapandet av gedigna visuella rapporter i Azure-portalen. De gör att du kan trycka på flera data källor i Azure och kombinera dem till enhetliga interaktiva upplevelser. 

Här är en video genom gång av hur du skapar arbets böcker.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Datakällor

Arbetsböcker kan köra frågor mot data från flera källor i Azure. Författare av arbetsböcker kan transformera dessa data för att ge insikter om tillgänglighet, prestanda, användning och övergripande hälsa för de underliggande komponenterna. Till exempel analyserar prestanda loggar från virtuella datorer för att identifiera höga processor-eller låga minnes instanser och visa resultaten som ett rutnät i en interaktiv rapport.
  
Den verkliga kraften hos arbetsböcker är dock möjligheten att kombinera data från olika källor till en enda rapport. På så sätt kan du skapa sammansatta resursvyer eller ansluta mellan resurser som möjliggör bättre data och insikter som annars skulle vara omöjlig.

Arbetsböcker är för närvarande kompatibla med följande datakällor:

* [Loggar](../visualize/workbooks-data-sources.md#logs)
* [Mått](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Aviseringar (för hands version)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Arbets belastnings hälsa](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure-datautforskaren](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualiseringar

Arbets böcker ger en omfattande uppsättning funktioner för visualisering av dina data. Detaljerade exempel på varje visualiserings typ kan du se exempel länkarna nedan:

* [Text](../visualize/workbooks-text-visualizations.md)
* [Diagram](../visualize/workbooks-chart-visualizations.md)
* [Rutnät](../visualize/workbooks-grid-visualizations.md)
* [Paneler](../visualize/workbooks-tile-visualizations.md)
* [Träd](../visualize/workbooks-tree-visualizations.md)
* [Diagram](../visualize/workbooks-graph-visualizations.md)
* [Sammansatt fält](../visualize/workbooks-composite-bar.md)

![Exempel på arbets boks visualiseringar](./media/workbooks-overview/visualizations.png)

## <a name="getting-started"></a>Komma igång

För att utforska arbets bokens upplevelse går du först till Azure Monitors tjänsten. Detta kan göras genom att skriva **Monitor** i sökrutan i Azure Portal.

Välj sedan **arbets böcker**.

![Skärm bild av knappen arbets böcker markerad i en röd ruta](./media/workbooks-overview/workbooks.png)

### <a name="gallery"></a>Galleri

Det tar dig till galleriet för arbets böcker:

![Skärm bild av vyn Azure Monitor arbets böcker Galleri](./media/workbooks-overview/gallery.png)

### <a name="workbooks-versus-workbook-templates"></a>Arbets böcker jämfört med mallar för arbets böcker

Du kan se en _arbets bok_ i grönt och ett antal _mallar för arbets böcker_ i lila. Mallar fungerar som granskade rapporter som har utformats för flexibel åter användning av flera användare och team. När du öppnar en mall skapas en tillfällig arbets bok som är ifylld med mallens innehåll. 

Du kan justera den mallbaserade arbets bokens parametrar och utföra analyser utan att behöva bryta den framtida rapporterings upplevelsen för kollegor. Om du öppnar en mall gör du några justeringar och väljer sedan ikonen Spara som du kommer att spara mallen som en arbets bok som sedan visas i grönt, vilket gör att den ursprungliga mallen inte vidrörs. 

Under huven skiljer sig mallarna också från sparade arbets böcker. När du sparar en arbets bok skapas en associerad Azure Resource Manager resurs, medan den tillfälliga arbets boken som skapas när du bara öppnar en mall inte har någon unik resurs kopplad till sig. Mer information om hur åtkomst kontroll hanteras i arbets böcker finns i artikeln om [åtkomst kontroll för arbets böcker](../visualize/workbooks-access-control.md).

### <a name="exploring-a-workbook-template"></a>Utforska en mall för arbets böcker

Välj **program haveri analys** för att se en av standard mallarna för programbegäran.

![Skärm bild av mall för program haveri analys](./media/workbooks-overview/failure-analysis.png)

Som tidigare nämnts skapar den här mallen en tillfällig arbets bok så att du kan interagera med. Som standard öppnas arbets boken i läsläge som bara visar informationen för den avsedda analys upplevelsen som skapades av den ursprungliga mal len författare.

När det gäller den här arbets boken är upplevelsen interaktiv. Du kan justera prenumerationen, mål programmen och tidsintervallet för de data som du vill visa. När du har gjort dessa val är rutnätet för HTTP-förfrågningar också interaktivt genom att välja en enskild rad som ska ändra vilka data som återges i de två diagrammen längst ned i rapporten.

### <a name="editing-mode"></a>Redigerings läge

Du måste växla till redigerings läge genom att välja **Redigera** för att förstå hur den här arbets boks mal len placeras tillsammans.

![Skärm bild av knappen Redigera i arbets böcker.](./media/workbooks-overview/edit.png)

När du har växlat till redigerings läget visas ett antal **redigerings** rutor till höger som motsvarar varje enskild aspekt av arbets boken.

![Skärm bild av knappen Redigera](./media/workbooks-overview/edit-mode.png)

Om vi väljer knappen Redigera omedelbart under rutnätet med begär ande data kan vi se att den här delen av arbets boken består av en Kusto-fråga mot data från en Application Insights-resurs.

![Skärm bild av underliggande Kusto-fråga](./media/workbooks-overview/kusto.png)

Om du klickar på de andra **redigerings** knapparna till höger visas ett antal kärn komponenter som utgör arbets böcker som markdown [text rutor](../visualize/workbooks-text-visualizations.md), gränssnitts element för [parameter val](../visualize/workbooks-parameters.md) och andra [typer av diagram/visualiseringar](#visualizations). 

Utforska de färdiga mallarna i redigerings läge och ändra dem efter dina behov och spara din egen anpassade arbets bok är ett utmärkt sätt att börja med att lära dig om vad som är möjligt med Azure Monitor arbets böcker.

## <a name="pinning-visualizations"></a>Fästa visualiseringar

Steg för text, frågor och mått i en arbets bok kan fästas med hjälp av knappen Fäst på dessa objekt när arbets boken är i PIN-läge, eller om arbets bokens författare har aktiverat inställningar för det elementet för att göra PIN-ikonen synlig. 

Om du vill komma åt PIN-läget klickar du på **Redigera** för att ange redigerings läge och väljer den blå PIN-ikonen i det översta fältet. En enskild PIN-ikon visas sedan ovanför varje motsvarande arbets boks dels *redigerings* ruta på höger sida av skärmen.

![PIN-upplevelse](./media/workbooks-overview/pin-experience.png)

> [!NOTE]
> Arbets bokens status sparas vid tidpunkten för PIN-koden, och fästa arbets böcker på en instrument panel uppdateras inte om den underliggande arbets boken ändras. För att kunna uppdatera en fast arbets bok del måste du ta bort och fästa om den delen.

## <a name="dashboard-time-ranges"></a>Tids intervall för instrument panelen

Den fästa arbets bokens fråga delar kommer att respektera instrument panelens tidsintervall om det fasta objektet har kon figurer ATS för att använda en *tids intervalls* parameter. Instrument panelens tidsintervalls värde används som tids intervall parameter värde och eventuell ändring av instrument panelens tidsintervall innebär att det fästa objektet uppdateras. Om en fast del använder instrument panelens tidsintervall visas under rubriken för den fasta del uppdateringen för att visa instrument panelens tidsintervall när tidsintervallet ändras. 

Dessutom uppdateras fasta arbets boks delar med hjälp av en tidsintervalls parameter automatiskt enligt en hastighet som fastställs av instrument panelens tidsintervall. Den senaste gången då frågan kördes visas i under rubriken för den fästa delen.

Om ett fäst steg har ett explicit set-tidsintervall (använder inte en tidsintervall-parameter) används alltid det tidsintervallet för instrument panelen, oavsett inställningarna för instrument panelen. Under rubriken för den fästa delen kommer inte att visa instrument panelens tidsintervall, och frågan uppdateras inte automatiskt på instrument panelen. Under rubriken visar den tidpunkt då frågan kördes senast.

> [!NOTE]
> Frågor som använder data källan för *koppling* stöds inte för närvarande när du fäster på instrument paneler.

## <a name="sharing-workbook-templates"></a>Dela mallar för arbets böcker

När du börjar skapa egna mallar för arbets böcker kanske du vill dela den med större community. Om du vill veta mer och utforska andra mallar som inte ingår i standard galleriet för Azure Monitor besöker du vår [GitHub-lagringsplats](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Om du vill bläddra bland befintliga arbets böcker går du till [arbets boks biblioteket](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) på GitHub.

## <a name="next-step"></a>Nästa steg

* [Kom igång](#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](../visualize/workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.