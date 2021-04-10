---
title: Azure Monitor arbets böcker med anpassade parametrar
description: Förenkla komplex rapportering med förbyggda och anpassade parameterstyrda arbets böcker
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: 2cb284e1978ad6c890835318c51c6095891397cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723018"
---
# <a name="interactive-workbooks"></a>Interaktiva arbetsböcker

Med arbets böcker kan författare skapa interaktiva rapporter och upplevelser för sina konsumenter. Interaktivitet stöds på flera sätt.

## <a name="parameter-changes"></a>Parameter ändringar

När en arbets boks användare uppdaterar en parameter uppdateras alla kontroller som använder parametern automatiskt och ritas om för att återspegla det nya läget. Så här fungerar de flesta av Azure Portal-rapporterna stöd för interaktivitet. Arbets böcker ger detta i ett rakt framåt-sätt med minimal användar ansträngning.

Lär dig mer om [parametrar i arbets böcker](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Rutnät, panel, diagram markeringar

Med arbets böcker kan författare skapa scenarier där man klickar på en rad i ett rutnät uppdaterar efterföljande diagram baserat på radens innehåll.

En användare kan till exempel ha ett rutnät som visar en lista med begär Anden och vissa statistik som antal misslyckade. De kan konfigureras så att de klickar på en rad som motsvarar en begäran, vilket leder till att detaljerade diagram nedan uppdaterar för att filtrera ned till just den begäran.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Konfigurera interaktivitet i rutnäts rad klickar du på

1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken _Lägg till fråga_ för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som _logg_, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. `Run query` Visa resultaten
6. Välj ikonen _Avancerade inställningar_ i frågans sidfot (ikonen ser ut som ett kugg hjul). Då öppnas fönstret avancerade inställningar.
7. Kontrol lera inställningen: `When an item is selected, export a parameter` .
8. Under den inställning som du har markerat väljer du *Lägg till parameter* och fyller den med informationen nedan.
    1. Fält att exportera: `Request`
    2. Parameter namn: `SelectedRequest`
    3. Standardvärde: `All requests`
9. Välja Spara

    ![Skärm bild som visar avancerad redigerare med inställningar för att exportera fält som parametrar.](./media/workbooks-interactive/export-parameters-add.png)

10. Välj `Done Editing`.
11. Lägg till en annan fråges kontroll med steg 2 och 3.
12. Använd Frågeredigeraren för att ange KQL för din analys.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. `Run query` för att se resultatet.
14. Ändra _visualisering_ till `Area chart` .
15. Välj en rad att välja i det första rutnätet. Observera hur ytdiagram nedan filtrerar till den valda begäran.

Den resulterande rapporten ser ut så här i redigerings läge:

![Skärm bild av de första två fråga i redigerings läge.](./media/workbooks-interactive//interactivity-grid-create.png)

Bilden nedan visar en mer avancerad interaktiv rapport i Läs läge baserat på samma principer. I rapporten används rutnäts klickningar för att exportera parametrar, vilket i sin tur används i två diagram och ett textblock.

![Skärm bild som visar en rapport med rutnäts klick i läsläge.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportera innehållet i en hel rad

Ibland är det önskvärt att exportera hela innehållet i den markerade raden i stället för bara en viss kolumn. I sådana fall lämnar du `Field to export` egenskapen unset i steg 7,1 ovan. I arbets böckerna exporteras hela rad innehållet som en JSON-parameter till-parametern.

Använd `todynamic` funktionen för att parsa JSON och få åtkomst till de enskilda kolumnerna på den refererande KQL-kontrollen.

## <a name="grid-cell-clicks"></a>Rutnäts cell klickningar

Med arbets böcker kan författare lägga till interaktivitet via en särskild typ av rutnäts kolumn åter givning som kallas `link renderer` . En länk åter givning konverterar en rutnäts cell till en hyperlänk baserat på innehållet i cellen. Arbets böcker har stöd för många typer av länk åter givning, inklusive de som tillåter att öppna resurs översikts blad, egenskaps uppsättnings visnings program, App Insights-sökning, användning, transaktions spårning osv.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Konfigurera interaktivitet med cell klickningar i rutnät

1. Ändra arbets boken till redigerings läge genom att klicka på objektet _Redigera_ verktygsfält.
2. Använd länken _Lägg till fråga_ för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som _logg_, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. `Run query` Visa resultaten
6. Välj _kolumn inställningar_ för att öppna fönstret inställningar.
7. I avsnittet _kolumner_ ställer du in:
    1. _Exempel_ på kolumn åter givning: `Link` , Visa för att öppna: `Cell Details` , länk etikett: `Sample`
    2. _Antal_ -kolumn åter givning: `Bar` , färgpalett: `Blue` , minimalt värde: `0`
    3. _Begäran_ -kolumn åter givning: `Automatic`
    4. Välj _Spara och Stäng_ för att tillämpa ändringarna

    ![Skärm bild av kolumn inställningens flik.](./media/workbooks-interactive/column-settings.png)

8. Klicka på en av `Sample` länkarna i rutnätet. Då öppnas ett fönster med information om en exempel förfrågan.

    ![Skärm bild av informations fönstret i exempel förfrågan.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Länka åter givnings åtgärder

| Länk åtgärd | Åtgärd vid klickning |
|:------------- |:-------------|
| `Generic Details` | Visar rad värden i en flik för egenskaps rutnät |
| `Cell Details` | Visar cellvärdet i en Sammanhangs flik för egenskaps rutnät. Användbart när cellen innehåller en dynamisk typ med information (t. ex. JSON med begär ande egenskaper som plats, roll instans osv.). |
| `Cell Details` | Visar cellvärdet i en Sammanhangs flik för egenskaps rutnät. Användbart när cellen innehåller en dynamisk typ med information (t. ex. JSON med begär ande egenskaper som plats, roll instans osv.). |
| `Custom Event Details` | Öppnar Application Insights Sök information med det anpassade händelse-ID: t ( `itemId` ) i cellen |
| `* Details` | Liknar anpassad händelse information, förutom för beroenden, undantag, sid visningar, begär Anden och spår. |
| `Custom Event User Flows` | Öppnar Application Insights Användarflödens upplevelsen som är pivoterad på det anpassade händelse namnet i cellen |
| `* User Flows` | Liknar anpassade händelse Användarflöden utom undantag, sid visningar och begär Anden |
| `User Timeline` | Öppnar användarens tids linje med användar-ID (user_Id) i cellen |
| `Session Timeline` | Öppnar den Application Insights Sök upplevelsen för värdet i cellen (du kan till exempel söka efter texten "ABC" där ABC är värdet i cellen) |
| `Resource overview` | Öppna resursens översikt i portalen baserat på resurs-ID-värdet i cellen |

## <a name="conditional-visibility"></a>Villkorlig synlighet

Med hjälp av arbets boken kan användare se till att vissa kontroller visas eller försvinner baserat på parametrarnas värden. På så sätt kan skribenter se olika ut baserat på användarens indata-eller telemetri-tillstånd. Ett exempel visar att konsumenterna bara är en sammanfattning när saker är bra men Visa fullständig information när något är fel.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Konfigurera interaktivitet med villkorsstyrd synlighet

1. Följ stegen i avsnittet [Konfigurera interaktivitet i rutnäts rad och klicka på](#setting-up-interactivity-on-grid-row-click) avsnitt för att ställa in två interaktiva kontroller.
2. Lägg till en ny parameter högst upp:
    1. Namn: `ShowDetails`
    2. Parameter typ: `Drop down`
    3. Kunna `checked`
    4. Hämta data från: `JSON`
    5. JSON-ineffekt: `["Yes", "No"]`
    6. Spara för att genomföra ändringar.

    ![När du har valt knappen Lägg till parameter visas rutan Redigera parameter.](./media/workbooks-interactive/edit-parameter.png)

3. Ange parameter värde till `Yes`

    ![Ovanför knappen klar redigering är en listruta med vilken du kan ange parameter värde](./media/workbooks-interactive/set-parameter.png)

4. I kontrollen fråga med ytdiagram väljer du ikonen _Avancerade inställningar_ (kugg hjuls ikonen)
5. Kontrol lera inställningen `Make this item conditionally visible`
    1. Det här objektet visas om `ShowDetails` parameter värde `equals``Yes`
6. Välj _klar redigering_ för att genomföra ändringarna.
7. Välj _klar redigering_ i arbets bokens verktygsfält för att ange Läs läge.
8. Växla värdet för parametern `ShowDetails` till `No` . Observera att diagrammet nedan försvinner.

Bilden nedan visar det synliga fallet där `ShowDetails` är `Yes`

![Skärm bild som visar den villkorliga synbarheten där diagrammet är synligt](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

Bilden nedan visar det dolda fallet där `ShowDetails` är `No`

![Skärm bild som visar den villkorliga synbarheten där diagrammet är dolt](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interaktivitet med flera markeringar i rutnät och diagram

Frågor och mått steg kan också exportera en eller flera parametrar när en rad (eller flera rader) är markerad.

![Skärm bild som visar inställningarna för export parametrar med flera parametrar. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. I steget fråga som visar rutnätet går du till avancerade inställningar.
2. Markera kryss `When items are selected, export parameters` rutan. Ytterligare kontroller kommer att visas.
3. Markera kryss `allow selection of multiple values` rutan.
    1. Den visade visualiseringen tillåter att värden för flera val och exporterade parametrar är matriser med värden, t. ex. När du använder list rutor med flera val.
    2. Om alternativet är avmarkerat kommer visnings visualiseringen bara att respektera det senast markerade objektet. Exportera bara ett enskilt värde i taget.
4. Använd knappen *Lägg till parameter* för varje parameter som du vill exportera. Ett popup-fönster visas med inställningarna för den parameter som ska exporteras.

När ett val har Aktiver ATS kan författaren ange vilket fält i original data som ska exporteras. Fälten inkluderar parameter namn, parameter typ och standardvärde som ska användas om inget är markerat (valfritt).

När flera markeringar är aktiverade anger författaren vilket fält i original data som ska exporteras. Fälten inkluderar parameter namn, parameter typ, citat med och-avgränsare. Värdena för citat med och avgränsare används när du svänger av pilknapparna till text när de ersätts i en fråga. I Multi-SELECT om inga värden är markerade är standardvärdet en tom matris.

> [!NOTE]
> För att välja flera värden exporteras endast unika värden. värden för utdata visas inte, t. ex. 1, 1, 2, 1. du får "1, 2" som värden för utdata.

Du kan lämna inställningen "fält för export" tom i export inställningarna. Om du gör det exporteras alla tillgängliga fält i data som ett stringified JSON-objekt med nyckel: värde-par. För rutnät och titlar är detta alla fält i rutnätet. För diagram är de tillgängliga fälten x, y, Series och Label (beroende på typ av diagram).

Även om standard beteendet är att exportera en parameter som text, om du vet att fältet är ett prenumerations-eller resurs-ID, använder du som export parameter typ. Detta gör att parametern kan användas nedströms på platser som kräver dessa typer av parametrar.

## <a name="next-steps"></a>Nästa steg

* [Kom igång](./workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](./workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.