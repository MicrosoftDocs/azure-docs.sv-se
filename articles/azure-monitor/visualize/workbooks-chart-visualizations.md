---
title: Azure Monitor visualiseringar i arbets boks diagram
description: Lär dig mer om alla visualiseringar i Azure Monitor arbets bok diagram.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100624598"
---
# <a name="chart-visualizations"></a>Diagramvisualiseringar

Med arbets böcker kan övervaknings data visas som diagram. De diagram typer som stöds är linje, stapel, stapel kategoriska, Area, punkt diagram, cirkel och tid. Författare kan välja att anpassa höjden, bredden, färgpaletten, förklaringen, titlar, inga data meddelanden osv. i diagrammet och anpassa axel typer och serie färger med hjälp av diagram inställningar.

Arbets böcker stöder diagram för både loggar och metriska data källor.

## <a name="log-charts"></a>Logg diagram

Azure Monitor-loggar ger resurser till gång till information om arbets uppgifter för deras appar och infrastruktur. Till skillnad från mått samlas logg information inte in som standard och kräver en viss typ av samling. Men när befintliga loggar ger mycket information om status för den resurs och de data som är användbara för diagnostik. Arbets böcker gör det möjligt att presentera loggdata som visuella diagram för användar analys.

### <a name="adding-a-log-chart"></a>Lägga till ett logg diagram

Exemplet nedan visar trenden för förfrågningar till en app under de senaste dagarna.

1. Ändra arbets boken till redigerings läge genom att välja objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till fråga** för att lägga till en logg frågas kontroll i arbets boken.
3. Välj typ av fråga som **logg**, resurs typ (till exempel Application Insights) och de resurser som ska riktas mot målet.
4. Använd Frågeredigeraren för att ange [KQL](/azure/kusto/query/) för din analys (till exempel trend för förfrågningar).
5. Ange visualiseringen till något av: **Area**, **bar**, **bar (kategoriska)**, **linje**, **cirkel**, **punkt** eller **tid**.
6. Ange andra parametrar om det behövs, till exempel tidsintervall, visualisering, storlek, färgpalett och förklaring.

[![Skärm bild av logg diagram i redigerings läge](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Parametrar för logg diagram

| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `Query Type` | Typ av fråga som ska användas. | Logg, Azure Resource Graph osv. |
| `Resource Type` | Resurs typen som mål. | Application Insights, Log Analytics eller Azure-First |
| `Resources` | En uppsättning resurser som värden ska hämtas från. | MyApp1 |
| `Time Range` | Tids perioden för att Visa logg diagrammet. | Senaste timmen, senaste 24 timmarna osv. |
| `Visualization` | Visualiseringen som ska användas. | Yt-, stapel-, linje-, cirkel-, punkt-, Time-, bar-kategoriska |
| `Size` | Kontrollens lodräta storlek. | Liten, medel, stor eller fullständig |
| `Color palette` | Den färgpalett som ska användas i diagrammet. Ignoreras i multi-Metric-eller segment läge. | Blått, grönt, rött osv. |
| `Legend` | Sammansättnings funktionen som ska användas för förklaringen. | Sum eller medelvärdet av värden eller Max, min, First, sista värdet |
| `Query` | Alla KQL-frågor som returnerar data i det format som förväntas av diagrammets visualisering. | _begär Anden om \| make-serien = Count () standard = 0 på tidsstämpeln från sedan (1d) till Now () steg 1H_ |

### <a name="time-series-charts"></a>Tids serie diagram

Tids serie diagram som yta, stapel, linje, punkt och tid kan enkelt skapas med hjälp av frågans kontroll i arbets böcker. Nyckeln har tid-och mått information i resultat uppsättningen.

#### <a name="simple-time-series"></a>Enkel tids serie

Frågan nedan returnerar en tabell med två kolumner: *tidsstämpel* och *begär Anden*. Frågevyn använder *tidsstämpel* för X-axeln och *begär Anden* för Y-axeln.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Skärm bild av ett enkelt logg rads diagram för tids serier.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Tids serier med flera mått

Frågan nedan returnerar en tabell med tre kolumner: *tidsstämpel*, *begär Anden* och *användare*. Frågevyn använder *timestamp* för X-axeln och *begär* att  &  *användare* som separat serie på Y-axeln.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Skärm bild av en tids serie med flera måtts logg linje diagram.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Segmenterad tids serie

Frågan nedan returnerar en tabell med tre kolumner: *timestamp*, *requests* och *RequestName* där *RequestName* är en kategoriska-kolumn med namnen på begär Anden. Kontrollen fråga här använder *timestamp* för X-axeln och lägger till en serie per värde för *RequestName*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Skärm bild av ett segmenterat tids serie logg rads diagram.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Sammanfatta vs. skapa-serien

I exemplen i föregående avsnitt används `summarize` operatorn eftersom det är enklare att förstå. Sammanfattningen har dock en större begränsning eftersom den utelämnar raden result om det inte finns några objekt i Bucket. Det kan påverka diagrammets tids period, beroende på om de tomma buckets ligger längst fram eller Backside i tidsintervallet.

Det är vanligt vis bättre att använda `make-series` operatorn för att skapa Time Series-data, som har möjlighet att ange standardvärden för tomma buckets.

Följande fråga använder `make-series` operatorn.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

Frågan nedan visar ett liknande diagram med `summarize` operatorn

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Även om den underliggande resultat uppsättningen är annorlunda. Alla användare har att göra är att ställa in visualiseringen på yt, linje, stapel eller tid och arbets böcker som tar hand om resten.

[![Skärm bild av ett logg rads diagram som har skapats från en fråga-serien](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Kategoriska stapeldiagram eller histogram

Kategoriska-diagram låter användare representera en dimension eller kolumn på X-axeln i ett diagram, vilket är särskilt användbart i histogram. Exemplet nedan visar distributionen av begär Anden från resultat koden.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Frågan returnerar två kolumner: *begär* mått och *resultat* kategori. Varje värde i *resultat* kolumnen får sin egen stapel i diagrammet med höjden proportionellt mot *måtten för begär Anden*.

[![Skärm bild av ett kategoriska stapeldiagram för förfrågningar efter resultat kod](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Cirkeldiagram

Cirkel diagram tillåter visualiseringen av numerisk proportion. I exemplet nedan visas andelen av förfrågningarna från resultat koden.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

Frågan returnerar två kolumner: *begär* mått och *resultat* kategori. Varje värde i kolumnen *result* får sin egen sektor i cirkeln med en storlek som är proportionell mot *begär* ande måttet.

[![Skärm bild av ett cirkel diagram med segment som representerar resultat kod](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Mått diagram

De flesta Azure-resurser genererar mått information om tillstånd och hälsa (till exempel processor användning, lagrings tillgänglighet, antal databas transaktioner, misslyckade app-begäranden osv.). Arbets böcker tillåter visualiseringen av dessa data som tids serie diagram.)

### <a name="adding-a-metric-chart"></a>Lägga till ett mått diagram

I följande exempel visas antalet transaktioner i ett lagrings konto under den föregående timmen. På så sätt kan lagrings ägaren se transaktions trenden och leta efter avvikelser i beteendet.

1. Ändra arbets boken till redigerings läge genom att välja objektet **Redigera** verktygsfält.
2. Använd länken **Lägg till mått** om du vill lägga till en mått kontroll i arbets boken.
3. Välj en resurs typ (t. ex. lagrings konto), de resurser som ska riktas till, mått namn området och namnet och vilken agg regering som ska användas.
4. Ange andra parametrar vid behov, t. ex. tidsintervall, delning för, visualisering, storlek och färgpalett.

[![Skärm bild av mått diagram i redigerings läge](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Mått diagram parametrar

| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `Resource Type` | Resurs typen som mål. | Lagring eller virtuell dator. |
| `Resources` | En uppsättning resurser som värden ska hämtas från. | MyStorage1 |
| `Namespace` | Namn området med måttet. | Lagring > BLOB |
| `Metric` | Måttet som ska visualiseras. | Lagrings > BLOB-> transaktioner |
| `Aggregation` | Sammansättnings funktionen som ska användas för måttet. | Sum, Count, Average osv. |
| `Time Range` | Tids perioden för att Visa måttet i. | Senaste timmen, senaste 24 timmarna osv. |
| `Visualization` | Visualiseringen som ska användas. | Yt-, stapel-, linje-, punkt-, rutnät |
| `Split By` | Du kan också dela måttet på en dimension. | Transaktioner per geo-typ |
| `Size` | Kontrollens lodräta storlek. | Liten, medel eller stor |
| `Color palette` | Den färgpalett som ska användas i diagrammet. Ignoreras om `Split by` parametern används. | Blått, grönt, rött osv. |

### <a name="examples"></a>Exempel

Transaktioner dela efter API-namn som ett linje diagram:

[![Skärm bild av ett mått linje diagram för lagrings transaktioner dela efter API-namn](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Transaktioner som delas av svars typen som ett stort stapeldiagram:

[![Skärm bild av ett stort Metric-diagram för lagrings transaktioner som delas av svars typ](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Genomsnittlig latens som ett punkt diagram:

[![Skärm bild av ett mått punkt diagram för lagrings fördröjning](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Diagram inställningar

Författare kan använda diagram inställningar för att anpassa vilka fält som används i diagram axlarna, Axel enheter, anpassad formatering, intervall, grupperingsintervall, förklaringar och serie färger.

### <a name="the-settings-tab"></a>Fliken Inställningar

Fliken inställningar styr:

- Axel inställningarna, inklusive vilka fält, anpassad formatering som gör det möjligt för användarna att ange talformateringen till axelvärdena och anpassade intervall.
- Grupperingsinställningar, inklusive vilket fält, gränserna innan en "andra" grupp skapas.
- Inställningar för förklaring, inklusive visning av mått (serie namn, färger och siffror) längst ned och/eller en förklaring (serie namn och färger).

![Skärm bild av diagram inställningar.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Anpassad formatering

Formateringsalternativ för tal inkluderar:

| Formateringsalternativ             | Förklaring                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | Enheterna för kolumnen – olika alternativ för procent, antal, tid, byte, antal/tid, byte/tid osv. Till exempel kan enheten för ett värde på 1234 anges till millisekunder och den återges som 1,234 s.                                  |
| `Style`                      | Det format som ska återges som decimal, valuta, procent.                                               |
| `Show group separator`       | Kryss ruta för att Visa grupp avgränsare. Återger 1234 som 1 234 i USA.                                    |
| `Minimum integer digits`     | Minsta antal heltals siffror som ska användas (standard 1).                                                   |
| `Minimum fractional digits`  | Minsta antal bråk tals siffror som ska användas (standard 0).                                                |
| `Maximum fractional digits`  | Högsta antal bråk tals siffror som ska användas.                                                            |
| `Minimum significant digits` | Minsta antal signifikanta siffror som ska användas (standard 1).                                               |
| `Maximum significant digits` | Maximalt antal signifikanta siffror som ska användas.                                                           |

![Skärm bild av inställningarna för X-axeln.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>Fliken serie

På fliken serie inställning kan du justera etiketter och färger som visas för serier i diagrammet.

- `Series name`Fältet används för att matcha en serie i data och om den matchas visas visnings etiketten och färgen.
- `Comment`Fältet är användbart för mallar som författare, eftersom den här kommentaren kan användas av översättare för att lokalisera visnings etiketter.

![Skärm bild av inställningar för serie.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du skapar en [panel i arbets böcker](workbooks-tile-visualizations.md).
- Lär dig hur du skapar [interaktiva arbets böcker](workbooks-interactive.md).