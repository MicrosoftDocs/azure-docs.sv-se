---
title: Översikt över Azure Monitor-arbetsböcker
description: Lär dig hur arbetsböcker ger en flexibel arbetsyta för dataanalys och skapandet av omfattande visuella rapporter i Azure Portal.
services: azure-monitor
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 3d75d7605ba082aac84973aef247de79d55b4c9c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482780"
---
# <a name="azure-monitor-workbooks"></a>Azure Monitor arbetsböcker

Arbetsböcker ger en flexibel arbetsyta för dataanalys och skapandet av gedigna visuella rapporter i Azure-portalen. De gör att du kan utnyttja flera datakällor från Azure och kombinera dem till enhetliga interaktiva upplevelser.

Här är en videogenomströmning om hur du skapar arbetsböcker.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B4Ap]

## <a name="data-sources"></a>Datakällor

Arbetsböcker kan köra frågor mot data från flera källor i Azure. Författare av arbetsböcker kan transformera dessa data för att ge insikter om tillgänglighet, prestanda, användning och övergripande hälsa för de underliggande komponenterna. Du kan till exempel analysera prestandaloggar från virtuella datorer för att identifiera instanser med hög cpu- eller minnesanvändning och visa resultatet som ett rutnät i en interaktiv rapport.
  
Den verkliga kraften hos arbetsböcker är dock möjligheten att kombinera data från olika källor till en enda rapport. På så sätt kan du skapa sammansatta resursvyer eller kopplingar mellan resurser, vilket möjliggör mer omfattande data och insikter som annars skulle vara omöjliga.

Arbetsböcker är för närvarande kompatibla med följande datakällor:

* [Loggar](../visualize/workbooks-data-sources.md#logs)
* [Mått](../visualize/workbooks-data-sources.md#metrics)
* [Azure Resource Graph](../visualize/workbooks-data-sources.md#azure-resource-graph)
* [Aviseringar (förhandsversion)](../visualize/workbooks-data-sources.md#alerts-preview)
* [Hälsotillstånd för arbetsbelastning](../visualize/workbooks-data-sources.md#workload-health)
* [Azure Resource Health](../visualize/workbooks-data-sources.md#azure-resource-health)
* [Azure-datautforskaren](../visualize/workbooks-data-sources.md#azure-data-explorer)

## <a name="visualizations"></a>Visualiseringar

Arbetsböcker ger en omfattande uppsättning funktioner för att visualisera dina data. Detaljerade exempel på varje visualiseringstyp finns i länkarna nedan:

* [Text](../visualize/workbooks-text-visualizations.md)
* [Diagram](../visualize/workbooks-chart-visualizations.md)
* [Rutnät](../visualize/workbooks-grid-visualizations.md)
* [Paneler](../visualize/workbooks-tile-visualizations.md)
* [Träd](../visualize/workbooks-tree-visualizations.md)
* [Grafer](../visualize/workbooks-graph-visualizations.md)
* [Sammansatt fält](../visualize/workbooks-composite-bar.md)

:::image type="content" source="./media/workbooks-overview/visualizations.png" alt-text="Exempel på arbetsboksvisualiseringar." border="false" lightbox="./media/workbooks-overview/visualizations.png":::

### <a name="pinning-visualizations"></a>Fästa visualiseringar

Text-, fråge- och måttsteg i en arbetsbok kan fästas med hjälp av fästknappen på dessa objekt när arbetsboken är i fästläge, eller om arbetsbokens författare har aktiverat inställningar för det elementet för att göra fästikonen synlig.

Om du vill komma åt pin-läget **klickar du** på Redigera för att gå in i redigeringsläge och väljer den blå fästikonen i det översta fältet. En enskild fästikon visas sedan ovanför varje motsvarande arbetsboksdels *Redigeringsruta* till höger på skärmen.

:::image type="content" source="./media/workbooks-overview/pin-experience.png" alt-text="Skärmbild av fästupplevelsen." border="false":::

> [!NOTE]
> Arbetsbokens tillstånd sparas vid tidpunkten för PIN-koden och fästa arbetsböcker på en instrumentpanel uppdateras inte om den underliggande arbetsboken ändras. För att kunna uppdatera en fäst arbetsboksdel måste du ta bort och fästa den delen igen.

## <a name="getting-started"></a>Komma igång

Om du vill utforska arbetsboksupplevelsen navigerar du först till Azure Monitor tjänsten. Det kan du göra genom att **skriva Monitor** i sökrutan i Azure Portal.

Välj sedan **Arbetsböcker.**

:::image type="content" source="./media/workbooks-overview/workbooks.png" alt-text="Skärmbild av knappen Arbetsböcker markerad i en röd ruta." border="false":::

### <a name="gallery"></a>Galleri

Galleriet gör det praktiskt att organisera, sortera och hantera arbetsböcker av alla typer.

:::image type="content" source="./media/workbooks-overview/gallery-all-tab.png" alt-text="Skärmbild av galleriet på fliken Alla." lightbox="media/workbooks-overview/gallery-all-tab.png":::

#### <a name="gallery-tabs"></a>Galleriflikar

Det finns fyra flikar i galleriet som hjälper dig att organisera arbetsbokstyper.

| Flik              | Beskrivning                                       |
|------------------|---------------------------------------------------|
| Alla | Visar de fyra översta objekten för varje typ – arbetsböcker, offentliga mallar och mina mallar. Arbetsböcker sorteras efter ändringsdatum, så du ser de senaste åtta ändrade arbetsböckerna.|
| Arbetsböcker | Visar listan över alla tillgängliga arbetsböcker som du har skapat eller som delas med dig. |
| Offentliga mallar | Visar en lista över alla tillgängliga som är redo att användas och komma igång med funktionella arbetsboksmallar som publicerats av Microsoft. Grupperat efter kategori. |
| Mina mallar | Visar listan över alla tillgängliga distribuerade arbetsboksmallar som du har skapat eller som delas med dig. Grupperat efter kategori. |

#### <a name="features"></a>Funktioner

* På varje flik finns det ett rutnät med information om arbetsböckerna. Den innehåller beskrivning, senaste ändringsdatum, taggar, prenumeration, resursgrupp, region och delat tillstånd. Du kan också sortera arbetsböckerna efter den här informationen.
* Filtrera efter resursgrupp, prenumerationer, arbetsboks-/mallnamn eller mallkategori.
* Välj flera arbetsböcker att ta bort eller massborttagning.
* Varje arbetsbok har en snabbmeny (ellips/tre punkter i slutet) och om du väljer den öppnas en lista över snabbåtgärder.
    * Visa resurs – Åtkomst till arbetsbokens resursflik för att se arbetsbokens resurs-ID, lägga till taggar, hantera lås osv.
    * Ta bort eller byt namn på arbetsboken.
    * Fäst arbetsboken på instrumentpanelen.

### <a name="workbooks-versus-workbook-templates"></a>Arbetsböcker jämfört med arbetsboksmallar

Du kan se en _arbetsbok_ i grönt och ett antal _arbetsboksmallar_ i lila. Mallar fungerar som curated reports som är utformade för flexibel återanvändning av flera användare och team. När du öppnar en mall skapas en tillfällig arbetsbok som fylls i med innehållet i mallen.

Du kan justera parametrarna för den mallbaserade arbetsboken och utföra analyser utan att behöva bry dig om framtida rapportering för kollegor. Om du öppnar en mall gör du vissa justeringar och väljer sedan ikonen Spara. Då sparas mallen som en arbetsbok som sedan visas i grönt så att den ursprungliga mallen inte ändras.

Under huven skiljer sig mallarna också från sparade arbetsböcker. När du sparar en arbetsbok skapas en Azure Resource Manager resurs, medan den tillfälliga arbetsboken som skapas när du precis öppnar en mall inte har någon unik resurs kopplad till den. Mer information om hur åtkomstkontroll hanteras i arbetsböcker finns i artikeln om [arbetsböckers åtkomstkontroll.](../visualize/workbooks-access-control.md)

### <a name="exploring-a-workbook-template"></a>Utforska en arbetsboksmall

Välj **Analys av programfel** för att se en av standardmallarna för programarbetsböcker.

:::image type="content" source="./media/workbooks-overview/failure-analysis.png" alt-text="Skärmbild av mallen för analys av programfel." border="false" lightbox="./media/workbooks-overview/failure-analysis.png":::

Som tidigare nämnts skapas en tillfällig arbetsbok när du öppnar mallen så att du kan interagera med den. Som standard öppnas arbetsboken i läsläge som endast visar informationen för den avsedda analysupplevelsen som skapades av den ursprungliga mallförfattaren.

När det gäller den här specifika arbetsboken är upplevelsen interaktiv. Du kan justera prenumerationen, målapparna och tidsperioden för de data som du vill visa. När du har gjort dessa val är rutnätet för HTTP-begäranden också interaktivt, vilket innebär att om du väljer en enskild rad ändras vilka data som återges i de två diagrammen längst ned i rapporten.

### <a name="editing-mode"></a>Redigeringsläge

För att förstå hur den här arbetsboksmallen är sammanförd måste du växla till redigeringsläge genom att välja **Redigera.**

:::image type="content" source="./media/workbooks-overview/edit.png" alt-text="Skärmbild av redigeringsknappen i arbetsböcker." border="false" :::

När du har växlat till redigeringsläge ser du att ett antal **redigeringsrutor** visas till höger som motsvarar varje enskild aspekt av arbetsboken.

:::image type="content" source="./media/workbooks-overview/edit-mode.png" alt-text="Skärmbild av knappen Redigera." border="false" lightbox="./media/workbooks-overview/edit-mode.png":::

Om vi väljer redigeringsknappen direkt under rutnätet med begärandedata kan vi se att den här delen av arbetsboken består av en Kusto-fråga mot data från en Application Insights resurs.

:::image type="content" source="./media/workbooks-overview/kusto.png" alt-text="Skärmbild av underliggande Kusto-fråga." border="false" lightbox="./media/workbooks-overview/kusto.png":::

Om du **väljer** de andra Redigeringsknapparna till höger visas ett antal kärnkomponenter som utgör arbetsböcker som markdown-baserade [textrutor,](../visualize/workbooks-text-visualizations.md)gränssnittselement för [parameterval](../visualize/workbooks-parameters.md) och andra [diagram-/visualiseringstyper.](#visualizations)

Att utforska de fördefinierade mallarna i redigeringsläge och sedan ändra dem efter dina behov och spara din egen anpassade arbetsbok är ett utmärkt sätt att börja lära dig om vad som är möjligt med Azure Monitor arbetsböcker.

## <a name="dashboard-time-ranges"></a>Tidsintervall för instrumentpanel

Fästa arbetsboksfrågedelar respekterar instrumentpanelens tidsperiod om det fästa objektet är konfigurerat för att använda en parameter *för tidsintervall.* Instrumentpanelens tidsintervallvärde används som tidsintervallparameterns värde, och eventuella ändringar av instrumentpanelens tidsperiod gör att det fästa objektet uppdateras. Om en fäst del använder instrumentpanelens tidsperiod visas underrubriken för uppdateringen av den fästa delen för att visa instrumentpanelens tidsperiod när tidsperioden ändras.

Dessutom uppdateras fästa arbetsboksdelar med en parameter för ett tidsintervall automatiskt med en hastighet som bestäms av instrumentpanelens tidsperiod. Den senaste gången frågan kördes visas i underrubriken för den fästa delen.

Om ett fäst steg har ett uttryckligen anställt tidsintervall (använder inte en parameter för tidsperiod) används alltid det tidsperioden för instrumentpanelen, oavsett instrumentpanelens inställningar. Underrubriken för den fästa delen visar inte instrumentpanelens tidsperiod och frågan uppdateras inte automatiskt på instrumentpanelen. Underrubriken visar den senaste gången frågan kördes.

> [!NOTE]
> Frågor som använder *sammanfogningsdatakällan* stöds inte för närvarande när du fäster på instrumentpaneler.

## <a name="sharing-workbook-templates"></a>Dela arbetsboksmallar

När du har börjat skapa egna arbetsboksmallar kanske du vill dela den med den bredare communityn. Om du vill veta mer och utforska andra mallar som inte ingår i standardgallerivyn Azure Monitor vår [GitHub-lagringsplats](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md). Om du vill bläddra bland befintliga arbetsböcker går du [till arbetsboksbiblioteket](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks) på GitHub.

## <a name="next-step"></a>Nästa steg

* [Kom igång med](#visualizations) att lära dig mer om arbetsböcker med många omfattande visualiseringsalternativ.
* [Kontrollera](../visualize/workbooks-access-control.md) och dela åtkomst till dina arbetsboksresurser.