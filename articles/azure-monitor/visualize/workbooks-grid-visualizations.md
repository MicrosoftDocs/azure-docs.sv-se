---
title: Rutnäts visualiseringar för Azure Monitor boken
description: Lär dig mer om alla visualiseringar i Azure Monitor arbets bok rutnät.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100622624"
---
# <a name="grid-visualizations"></a>Rutnätsvisualiseringar

Rutnät eller tabeller är ett vanligt sätt att presentera data för användare. Med arbets böcker kan användarna individuellt formatera kolumnerna i rutnätet för att ge ett omfattande användar gränssnitt för sina rapporter.

Exemplet nedan visar ett rutnät som kombinerar ikoner, termiska kartor och Spark-staplar för att presentera komplex information. Arbets boken innehåller också sortering, en sökruta och en go-till-Analytics-knapp.

[![Skärm bild av logga baserat rutnät](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Lägga till ett logg baserat rutnät

1. Ändra arbets boken till redigerings läge genom att klicka på objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange KQL för din analys (till exempel virtuella datorer med minne under ett tröskelvärde)
5. Ange visualiseringen till **rutnät**
6. Ange andra parametrar om det behövs, t. ex. tidsintervall, storlek, färgpalett och förklaring.

[![Skärm bild av fråga om logg beroende rutnät](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Parametrar för logg diagram

| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ av fråga som ska användas. | Logg, Azure Resource Graph osv. |
| `Resource Type` | Resurs typen som mål. | Application Insights, Log Analytics eller Azure-First |
| `Resources` | En uppsättning resurser som värden ska hämtas från. | MyApp1 |
| `Time Range` | Tids perioden för att Visa logg diagrammet. | Senaste timmen, senaste 24 timmarna osv. |
| `Visualization` | Visualiseringen som ska användas. | Rutnät |
| `Size` | Kontrollens lodräta storlek. | Liten, medel, stor eller fullständig |
| `Query` | Alla KQL-frågor som returnerar data i det format som förväntas av diagrammets visualisering. | _begär Anden \| sammanfatta begär Anden = Count () efter namn_ |

## <a name="simple-grid"></a>Enkelt rutnät

Arbets böcker kan återge KQL resultat som en enkel tabell. I rutnätet nedan visas antalet förfrågningar och unika användare per begär ande typ i en app.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Skärm bild av ett logg baserat rutnät i redigerings läge](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Rutnäts format

En vanlig tabell visar data, men det är svårt att läsa och insikter är inte alltid tydligare. Att formatera rutnätet kan hjälpa dig att göra det lättare att läsa och tolka data.

Nedan visas samma rutnät från föregående avsnitt som termiska kartor.

[![Skärm bild av ett logg baserat rutnät med kolumner som är formaterade som termiska kartor](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Här är samma rutnät formaterat som staplar: [ ![ skärm bild av ett logg baserat rutnät med kolumner formaterade som staplar](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>Formatera en rutnäts kolumn

1. Välj knappen **kolumn inställning** i verktygsfältet fråga kontroll.
2. I *Redigera kolumn inställningarna* väljer du kolumnen till format.
3. Välj en kolumn åter givning (till exempel termisk karta, stapel, stapel under osv.) och relaterade inställningar för att formatera kolumnen.

Nedan visas ett exempel som formaterar kolumnen *begäran* som ett fält:

[![Skärm bild av ett logg baserat rutnät med kolumnen förfrågan som ett fält.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Kolumn åter givning

| Kolumn åter givning | Förklaring | Ytterligare alternativ |
|:------------- |:-------------|:-------------|
| `Automatic` | Standard – använder den lämpligaste renderaren baserat på kolumn typen.  |  |
| `Text` | Återger kolumn värden som text. | |
| `Right Aligned` | Liknar text, förutom att den är högerjusterad. | |
| `Date/Time` | Renderar en läsbar datum tids sträng. | |
| `Heatmap` | Färger i rutnäts cellerna baserat på cellens värde. | Färgpalett och minsta/högsta värde som används för skalning. |
| `Bar` | Återger ett fält bredvid cellen baserat på cellens värde. | Färgpalett och minsta/högsta värde som används för skalning. |
| `Bar underneath` | Återger ett fält nära cellens nederkant baserat på cellens värde. | Färgpalett och minsta/högsta värde som används för skalning. |
| `Composite bar` | Återger en sammansatt stapel med de angivna kolumnerna på raden. Mer information finns i [sammansatt stapel](workbooks-composite-bar.md) . | Kolumner med motsvarande färger för att återge fältet och en etikett som ska visas överst i fältet. |
| `Spark bars` | Återger ett Spark-fält i cellen baserat på värdena för en dynamisk matris i cellen. Till exempel kolumnen trend från skärm bilden överst. | Färgpalett och minsta/högsta värde som används för skalning. |
| `Spark lines` | Återger en spark-rad i cellen baserat på värdena för en dynamisk matris i cellen. | Färgpalett och minsta/högsta värde som används för skalning. |
| `Icon` | Återger ikoner baserat på text värden i cellen. De värden som stöds är:,,,,,,,,, `cancelled` `critical` `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` , `success` , `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,, och.|  |
| `Link` | Återger en länk som när användaren klickar på eller utför en konfigurerbar åtgärd. Använd detta om du *bara* vill att objektet ska vara en länk.  Någon av de andra typerna kan *också* vara en länk med hjälp av `Make this item a link` inställningen. Mer information finns i [länk åtgärder](#link-actions) nedan. |  |
| `Location` | Återger ett eget namn på Azure-regionen baserat på ett regions-ID. |  |
| `Resource type` | Återger en egen resurs typs sträng baserat på ett resurs typs-ID  |  |
| `Resource` | Återger ett eget resurs namn och en länk baserat på ett resurs-ID  | Alternativ för att Visa resurs typs ikonen  |
| `Resource group` | Återger ett eget resurs grupp namn och en länk baserat på ett resurs grupps-ID. Om cellens värde inte är en resurs grupp, konverteras det till ett.  | Alternativ för att Visa resurs grupps ikonen  |
| `Subscription` | Återger ett eget prenumerations namn och länkar baserat på ett prenumerations-ID.  om cellens värde inte är en prenumeration kommer den att konverteras till ett.  | Alternativ för att Visa prenumerations ikonen.  |
| `Hidden` | Döljer kolumnen i rutnätet. Användbart när standard frågan returnerar fler kolumner än vad som behövs, men en projekt borta är inte önskad |  |

### <a name="link-actions"></a>Länka åtgärder

Om `Link` åter givningen är markerad eller om kryss rutan *gör det här objektet till en länk* är markerad, kan författaren konfigurera en länk åtgärd som ska utföras när cellen markeras. Detta tar vanligt vis användaren till en annan vy med kontext som kommer från cellen eller kan öppna en URL.

### <a name="custom-formatting"></a>Anpassad formatering

Med arbets böcker kan användarna också ange talformateringen för sina cell värden. De kan göra detta genom att klicka på kryss rutan *anpassad formatering* när den är tillgänglig.

| Formateringsalternativ | Förklaring |
|:------------- |:-------------|
| `Units` | Enheterna för kolumnen – olika alternativ för procent, antal, tid, byte, antal/tid, byte/tid osv. Till exempel kan enheten för ett värde på 1234 anges till millisekunder och den återges som 1,234 s. |
| `Style` | Det format som ska återges som decimal, valuta, procent. |
| `Show group separator` | Kryss ruta för att Visa grupp avgränsare. Återger 1234 som 1 234 i USA. |
| `Minimum integer digits` | Minsta antal heltals siffror som ska användas (standard 1). |
| `Minimum fractional digits` | Minsta antal bråk tals siffror som ska användas (standard 0).  |
| `Maximum fractional digits` | Högsta antal bråk tals siffror som ska användas. |
| `Minimum significant digits` | Minsta antal signifikanta siffror som ska användas (standard 1). |
| `Maximum significant digits` | Maximalt antal signifikanta siffror som ska användas. |
| `Custom text for missing values` | Om en data punkt inte har något värde, visar du den här anpassade texten i stället för en tom. |

### <a name="custom-date-formatting"></a>Anpassad formatering av datum

När författaren har angett att en kolumn har angetts till datum/tid åter givning, kan författaren ange anpassade datum format alternativ med hjälp av kryss rutan *anpassad datum format* .

| Formateringsalternativ | Förklaring |
|:------------- |:-------------|
| `Style` | Formatet för att återge ett datum som kort, långt, fullständigt format eller en tid som kort-eller lång tids format. |
| `Show time as` | Tillåter att författaren väljer mellan att visa tiden i lokal tid (standard) eller som UTC. Beroende på vilket datum format formatet är markerat kan UTC/Time Zone-informationen inte visas. |

## <a name="custom-column-width-setting"></a>Inställning för anpassad kolumn bredd

Författaren kan anpassa bredden på en kolumn i rutnätet med hjälp av fältet för *anpassad kolumn bredd* i *kolumn inställningar*.

![Skärm bild av kolumn inställningar med fältet anpassad kolumn bredd visas i en röd ruta](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Om fältet är svart, bestäms bredden automatiskt baserat på antalet tecken i kolumnen och antalet synliga kolumner. Standardenheten är "CH" (tecken).

Om du väljer knappen för blått **(aktuell bredd)** i etiketten fylls textfältet med den markerade kolumnens aktuella bredd. Om det finns ett värde i fältet anpassad bredd utan mått enhet, används standardvärdet.

De tillgängliga mått enheterna är:

| Måttenhet | Definition           |
|:--------------------|:---------------------|
| ch                  | tecken (standard) |
| px                  | punkt               |
| fr                  | bråk enheter     |
| %                   | percentage           |

Verifiering av indata – om valideringen Miss lyckas visas ett meddelande om röd vägledning under fältet, men användaren kan fortfarande använda bredden. Om ett värde finns i inmatat kommer det att tolkas. Om ingen giltig mått enhet hittas används standardvärdet.

Det finns ingen minsta/högsta bredd eftersom detta görs till författarens eget gottfinnande. Fältet för anpassad kolumn bredd har inaktiverats för dolda kolumner.

## <a name="examples"></a>Exempel

### <a name="spark-lines-and-bar-underneath"></a>Spark-linjer och fält under

I exemplet nedan visas antalet förfrågningar och dess trend efter namn på begäran.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Skärm bild av ett logg baserat rutnät med en stapel under och en spark-linje](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Termisk karta med delade skalor och anpassad formatering

I det här exemplet visas olika varaktighets mått och antal för begäran. Termisk karta åter givning använder de lägsta värdena som angetts i inställningarna eller beräknar ett minsta och högsta värde för kolumnen och tilldelar en bakgrunds färg från den valda paletten för cellen baserat på cellens värde i förhållande till det minsta och högsta värdet för kolumnen.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Skärm bild av ett logg baserat rutnät med en termisk karta som har en delad skala över kolumner](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

I exemplet ovan används en delad palett (grön eller röd) och skala för att färgstaplarna (medelvärde, median, P80, P95 och P99). En separat palett (blå) som används för kolumnen begäran.

#### <a name="shared-scale"></a>Delad skala

Så här får du en delad skala:

1. Använd reguljära uttryck för att markera fler än en kolumn för att tillämpa en inställning på. Ange till exempel kolumn namnet för `Mean|Median|p80|p95|p99` att markera alla.
2. Ta bort standardinställningarna för de enskilda kolumnerna.

Det innebär att den nya inställningen för flera kolumner används för att inkludera en delad skala.

[![Skärm bild av en logg baserat rutnäts inställning för att få en delad skala över kolumner](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Ikoner som representerar status

I exemplet nedan visas anpassad status för förfrågningar baserat på P95 varaktighet.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Skärm bild av ett logg baserat rutnät med en termisk karta som har en delad skala över kolumner som använder frågan ovan.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Namn på ikoner som stöds är:,,, `cancelled` `critical` ,,,, `disabled` `error` `failed` `info` `none` `pending` , `stopped` , `question` , `success` , `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` `Blank` ,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,

### <a name="using-thresholds-with-links"></a>Använda tröskelvärden med länkar

I anvisningarna nedan visas hur du använder tröskelvärden med länkar för att tilldela ikoner och öppna olika arbets böcker. Varje länk i rutnätet öppnar en annan arbetsboksmall för den Application Insights resursen.

1. Ändra arbets boken till redigerings läge genom att välja *Redigera* verktygsfälts objekt.
2. Välj **Lägg till** och *Lägg till fråga*.
3. Ändra *data källan* till "JSON" och *visualisering* till "Grid".
4. Ange följande fråga.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Kör fråga.
6. Välj **kolumn inställningar** för att öppna inställningarna.
7. Välj "namn" från *kolumner*.
8. Välj "tröskelvärden" under *kolumn åter givning*.
9.  Ange och välj följande *tröskelvärdes inställningar*.
   
    | Operator | Värde   | Ikoner   |
    |----------|---------|---------|
    | ==       | varning | Varning |
    | ==       | fel   | Misslyckad  |

    ![Skärm bild av fliken Redigera kolumn inställningar med inställningarna ovan.](./media/workbooks-grid-visualizations/column-settings.png)

    Behåll standard raden som den är. Du kan ange vilken text du vill. Text kolumnen tar ett sträng format som inmatade och fyller den med kolumnens värde och enhet om det anges. Om till exempel en varning är kolumnvärdet som texten kan vara " {0} {1} länk!" visas den som "varnings länk!".
10. Välj rutan *gör det här objektet till en länk* .
    1. Under *vy som ska öppnas väljer du*"arbets bok (mall)".
    2. Under *länk värde kommer från* väljer du länk.
    3. Välj *bladet öppna länk i sammanhang* .
    4. Välj följande inställningar i *länk inställningar för arbets boken*
        1. Under *mall-ID kommer från* väljer du kolumn.
        2. Under *kolumn* väljer du länk.

    ![Skärm bild av länk inställningar med ovanstående inställningar.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Välj "länka" från *kolumner*. Under Inställningar bredvid *kolumn åter givning* väljer du **(Dölj kolumn)**.
1. Om du vill ändra visnings namnet för kolumnen "namn" väljer du fliken **Etiketter** . På raden med "name" som *kolumn-ID*, under * kolumn etikett anger du det namn som du vill visa.
2. Välj **Använd**

![Skärm bild av ett tröskelvärde i rutnät med ovanstående inställningar](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Procent andel av bråk delar

Fraktions enheten (FR) är en ofta använd dynamisk mått enhet i olika typer av rutnät. När fönstrets storlek/upplösning ändras ändras även fr bredd.

Skärm bilden nedan visar en tabell med åtta kolumner som är 1fr bredd och alla samma bredd. När fönstrets storlek ändras ändras bredden på varje kolumn proportionellt.

[![Skärm bild av kolumner i rutnät med kolumn bredds värde för 1fr](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

Bilden nedan visar samma tabell, förutom att den första kolumnen är inställd på 50% bredd. Detta anger att kolumnen är hälften av den totala rutnäts bredden dynamiskt. Att ändra storlek på fönstret fortsätter att behålla 50% bredd, såvida inte fönstrets storlek är för litet. Dessa dynamiska kolumner har en minsta bredd baserat på deras innehåll. Den återstående 50% av rutnätet delas upp av de åtta totala bråk enheterna. Kolumnen "typ" nedan är inställd på 2fr, så det tar upp en fjärdedel av det återstående utrymmet. Eftersom de andra kolumnerna är 1fr, tar de upp en åttondel av den högra halvan av rutnätet.

[![Skärm bild av kolumner i rutnät med 1 kolumn bredds värde på 50% och resten som 1fr varje](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

Det går att kombinera bredden fr,%, px och CH, och fungerar på samma sätt som i föregående exempel. De bredder som anges av de statiska enheterna (CH och px) är hårda konstanter som inte ändras även om fönstret/upplösningen ändras. Kolumnerna som anges av% tar upp procent andelen baserat på den totala rutnäts bredden (kanske inte är exakt på grund av tidigare lägsta bredd). Kolumnerna som anges med Frankrike delar bara upp det återstående rutnäts utrymmet baserat på antalet bråk delar som de har tilldelats.

[![Skärm bild av kolumner i rutnätet med sortiment med olika bredd enheter som används](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar ett [träd i arbets böcker](workbooks-tree-visualizations.md).
* Lär dig hur du skapar [text parametrar för en arbets bok](workbooks-text.md).