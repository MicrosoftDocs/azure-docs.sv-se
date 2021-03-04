---
title: Mät värden för Azure Monitor mått och Visa förklaring
description: Detaljerad information om hur måtten sammanställs i Azure Monitor
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b7e9318ee34836f8fbd2ae7a330134d8174e6a60
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031403"
---
# <a name="azure-monitor-metrics-metrics-aggregation-and-display-explained"></a>Mät värden för Azure Monitor mått och Visa förklaring

I den här artikeln förklaras sammanställningen av mått i Azure Monitor Time-Series-databasen som backar Azure Monitor [plattforms mått](../data-platform.md) och [anpassade mått](../essentials/metrics-custom-overview.md). Den här artikeln gäller även standard [Application Insights mått](../app/app-insights-overview.md). 

Detta är ett komplext ämne och inte nödvändigt för att förstå all information i den här artikeln för att använda Azure Monitor mått effektivt.

## <a name="overview-and-terms"></a>Översikt och villkor

När du lägger till ett mått i ett diagram väljer Metric Explorer automatiskt sin standard agg regering. Standardvärdet är meningsfullt i de grundläggande scenarierna, men du kan använda olika agg regeringar för att få mer insikter om måttet. Om du visar olika agg regeringar i ett diagram måste du förstå hur Mät Utforskaren hanterar dem.

Låt oss definiera några villkor tydligt först:

- **Metric-värde** – ett enda Mät värde som samlas in för en viss resurs.
- **Tids period** – en allmän tids period.
- **Tidsintervall – tids** perioden mellan insamlingen av två metriska värden. 
- **Tidsintervall** – den tids period som visas i ett diagram. Normalt är standard 24 timmar. Endast vissa intervall är tillgängliga. 
- **Tids kornig het** eller **tidskornig het** – den tids period som används för att summera värden, tillsammans för att tillåta visning i ett diagram. Endast vissa intervall är tillgängliga. Det aktuella minimivärdet är 1 minut. Värdet för tids kornig het måste vara mindre än det valda tidsintervallet för att vara användbart, annars visas bara ett värde för hela diagrammet. 
- **Agg regerings typ** – en typ av statistik som beräknas från flera metriska värden.  
- **Aggregate** – processen att ta flera indatavärden och sedan använda dem för att skapa ett enda utdata-värde via de regler som definieras av agg regerings typen. Du kan till exempel ta medelvärdet av flera värden.  

Mått är en serie Mät värden som har registrerats med regelbundna tidsintervall. När du ritar ett diagram sammanställs värdena för de valda måtten separat under tids kornig het (även kallat tids kornig het). Du väljer storlek på tids kornig het med hjälp av [panelen Metrics Explorer tids väljare](../essentials/metrics-getting-started.md#select-a-time-range). Om du inte gör något explicit val väljs tids kornig het automatiskt baserat på det valda tidsintervallet. När du har valt så sammanställs Mät värdena som fångades under varje tids kornig het och placeras i diagrammet-en Datapoint per intervall.

## <a name="aggregation-types"></a>Agg regerings typer 

Det finns fem grundläggande agg regerings typer i mått Utforskaren. Mått Utforskaren döljer agg regeringar som är irrelevanta och inte kan användas för ett specifikt mått. 

- **Sum** – summan av alla värden som har samlats in under samlings intervallet. Kallas ibland total aggregation.
- **Count** – antalet mått som har hämtats över samlings intervallet. Count ser inte ut som värdet för måttet, bara antalet poster. 
- **Genomsnitt** – medelvärdet av mått värden som har samlats in under samlings intervallet. För de flesta mått är det här värdet sum/Count. 
- **Min** – det minsta värdet som fångas över agg regerings intervallet.
- **Max** – det största värdet som har hämtats över samlings intervallet.

Anta till exempel att ett diagram visar det totala måttet för **nätverket** för en virtuell dator med hjälp av **Sum** -aggregering under de senaste 24 timmarna. Tidsintervallet och granularitet kan ändras från det övre högra hörnet i diagrammet som visas på följande skärm bild.

:::image type="content" source="media/metrics-aggregation-explained/time-range-granularity-picker.png" alt-text="Skärm bild som visar väljare för tidsintervall och tids kornig het" border="true":::

Tids kornig het = 30 minuter och tidsintervallet = 24 timmar:

- Diagrammet ritas från 48 datapoints. Det är 24 timmar x 2 Datapoints per timme (60 min/30 min) sammanställd 1-minuters datapoints. 
- Linje diagrammet ansluter 48 punkter i diagram området. 
- Varje Datapoint representerar summan av alla utgående byte som skickats ut under varje 30-minuters period. 


 :::image type="content" source="media/metrics-aggregation-explained/24-hour-30-min-gran.png" alt-text="Skärm bild som visar data i ett linje diagram med tids gränsen 24 timmar och tids kornig het på 30 minuter" border="true" lightbox="media/metrics-aggregation-explained/24-hour-30-min-gran.png":::

*Klicka på bilderna i det här avsnittet om du vill se fler versioner.*

Om du växlar tids kornig het till 15 minuter ritas diagrammet från 96 sammanställda data punkter. Det vill säga 60 min/15min = 4 Datapoints per timme x 24 timmar.

:::image type="content" source="media/metrics-aggregation-explained/24-hour-15-min-gran.png" alt-text="Skärm bild som visar data i ett linje diagram som är inställda på 24-timmarsformat och tids kornig het på 15 minuter" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

För tids kornig het på 5 minuter får du 24 x (60/5) = 288 punkter. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-5-min-gran.png" alt-text="Skärm bild som visar data i ett linje diagram med tids gränsen 24 timmar och tids kornig het på 5 minuter" border="true" lightbox="media/metrics-aggregation-explained/24-hour-15-min-gran.png":::

För tids kornig het på 1 minut (minsta möjliga i diagrammet) får du 24 x 60/1 = 1440 punkter. 

:::image type="content" source="media/metrics-aggregation-explained/24-hour-1-min-gran.png" alt-text="Skärm bild som visar data i ett linje diagram som är inställda på 24-timmarsformat och tids kornig het på 1 minut" border="true" lightbox="media/metrics-aggregation-explained/24-hour-1-min-gran.png":::

Diagrammen ser olika ut för dessa summor som de visas i föregående skärm bilder.  Observera att den här virtuella datorn har mycket utdata under en tids period i förhållande till resten av tidsfönstret.  

Med tids kornig het kan du justera förhållandet "signal till brus" i ett diagram. Högre agg regeringar tar bort brus och jämnar ut toppar.  Lägg märke till variationerna i det nedersta diagrammet i 1 minut och hur de utjämnas när du går till högre granularitet värden. 

Detta Utjämnings beteende är viktigt när du skickar dessa data till andra system, till exempel aviseringar. Vanligt vis vill du vanligt vis inte aviseras med mycket korta toppar i CPU-tid över 90%. Men om processorn ligger på 90% i 5 minuter är det troligt vis viktigt. Om du ställer in en varnings regel för processor (eller något mått) kan tids kornigheten större minska antalet falska varningar som du får. 

Det är viktigt att fastställa vad som är "normal" för din arbets belastning för att veta vilka tidsintervall som är bäst. Detta är en av fördelarna med [dynamiska varningar](../alerts/alerts-dynamic-thresholds.md), som är ett annat ämne som inte beskrivs här.  

## <a name="how-the-system-collects-metrics"></a>Hur systemet samlar in mått

Data insamling varierar beroende på mått. Det finns två typer av samlings perioder.

### <a name="measurement-collection-frequency"></a>Frekvens för mätnings insamling 

- **Normalt** – måttet samlas in med ett konsekvent tidsintervall som inte varierar.

- **Aktivitets baserad** – måttet samlas in baserat på när en transaktion av en viss typ inträffar. Varje transaktion har en mått post och en tidstämpel. De samlas inte in med jämna mellanrum, så det finns ett varierande antal poster under en viss tids period. 

### <a name="granularity"></a>Precision

Det minsta tidsintervallet är 1 minut, men det underliggande systemet kan samla in data snabbare beroende på måttet. Till exempel spåras CPU-procent var 15: e sekund med jämna mellanrum. Eftersom HTTP-haverier spåras som transaktioner kan de enkelt överskrida många mer än en minut. Andra mått som SQL Storage samlas in var 20: e minut. Det här alternativet är upp till den enskilda resurs leverantören och typen. De flesta försöker ange minsta möjliga intervall.

### <a name="dimensions-splitting-and-filtering"></a>Dimensioner, dela och filtrera

Måtten fångas för varje enskild resurs. Den nivå där måtten samlas in, lagras och kan visas som diagram kan variera. Den här nivån representeras av ytterligare mått som är tillgängliga i **mått dimensioner**. Varje enskild resurs leverantör får definiera hur detaljerad information som de samlar in är. Azure Monitor definierar bara hur sådan information ska visas och lagras. 

När du diagramerar ett mått i Metric Explorer kan du välja att dela upp diagrammet med en dimension.  Genom att dela ett diagram innebär det att du tittar på underliggande data för mer information och ser att data visas eller filtreras i Metric Explorer.

Till exempel har [Microsoft. API Management/Service](./metrics-supported.md#microsoftapimanagementservice) *plats* som en dimension för många mått. 

- **Kapaciteten** är ett sådant mått. Om du använder dimensionen *plats* kan det underliggande systemet lagra en mått post för kapaciteten för varje plats, i stället för bara en för den sammanlagda mängden. Sedan kan du hämta eller dela upp informationen i ett mått diagram.  

- Om du tittar på den **totala varaktigheten för gateway-begäranden** finns det två dimensions *platser* och *värdnamn*, och du får reda på platsen för en varaktighet och vilket värdnamn den kom från.  

- Ett av de flexibla måtten, **förfrågningarna**, har 7 olika dimensioner. 
 
Se artikeln Azure Monitor [mått som stöds](./metrics-supported.md) för information om varje mått och de dimensioner som är tillgängliga. Dessutom kan dokumentationen för varje resurs leverantör och typ ge ytterligare information om dimensionerna och vad de mäter.

Du kan använda dela och filtrera tillsammans för att säga upp ett problem. Nedan visas ett exempel på en bild som visar *genomsnitts diskens Skriv byte* för en grupp med virtuella datorer i en resurs grupp. Vi har samlat samman alla virtuella datorer med det här måttet, men vi kanske vill kunna se vilka som faktiskt är ansvariga för topparna runt 06:00. Är de samma dator? Hur många datorer ingår?  

:::image type="content" source="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png" alt-text="Skärm bild som visar totalt antal skrivna byte för disk för alla virtuella datorer i Contosos hotell resurs grupp" border="true" lightbox="media/metrics-aggregation-explained/total-disk write-bytes-all-VMs.png":::

*Klicka på bilderna i det här avsnittet om du vill se fler versioner.*

När vi använder delning kan vi se underliggande data, men det är en del av en rörig. Det finns 20 virtuella datorer som sammanställs i diagrammet ovan. I det här fallet har vi använt vår mus för att hovra över den stora belastningen på 06:00 som talar om för oss att CH-DCVM11 är orsaken. men det är svårt att se resten av de data som är kopplade till den virtuella datorn på grund av andra virtuella datorer som fyller diagrammet. 

:::image type="content" source="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png" alt-text="Skärm bild som visar disk-skrivna byte för alla virtuella datorer i Contosos hotell resurs grupp dela efter namn på virtuell dator" border="true" lightbox="media/metrics-aggregation-explained/split-total-disk write-bytes-all-VMs.png":::

Med filtrering kan vi rensa diagrammet för att se vad som händer. Du kan markera eller avmarkera de virtuella datorer som du vill se. Lägg märke till de prickade linjerna. De nämns i ett senare avsnitt.

:::image type="content" source="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png" alt-text="Skärm bild som visar disk-skrivna byte för alla virtuella datorer i Contoso hotell resurs grupp dela och filtrerat efter namn på virtuell dator" border="true" lightbox="media/metrics-aggregation-explained/split-filter-total-disk write-bytes-all-VMs.png":::

Mer information om hur du visar delade dimensions data i ett Metric Explorer-diagram finns i [avancerade funktioner i mått Utforskaren – filter och delning](../essentials/metrics-charts.md#filters).

### <a name="null-and-zero-values"></a>NULL-värden och nollvärden

När systemet förväntar sig mått data från en resurs men inte tar emot den, registreras ett NULL-värde.  NULL skiljer sig från värdet noll, vilket blir viktigt vid beräkningen av agg regeringar och diagram. NULL-värden räknas inte som giltiga mått. 

NULLVÄRDEN visas på olika sätt i olika diagram. Punkt diagram hoppar över att visa en punkt i diagrammet. Stapeldiagram hoppar över att visa fältet. I linje diagram kan NULL visas som [prickade eller streckade linjer](../essentials/metrics-troubleshoot.md#chart-shows-dashed-line) som de som visas i skärm bilden i föregående avsnitt. När du beräknar genomsnitt som innehåller NULL-värden finns det färre data punkter att ta medelvärdet av.  Det här beteendet kan ibland leda till oväntade släpp värden i ett diagram, men vanligt vis mindre än om värdet har konverterats till noll och används som en giltig Datapoint.  

[Anpassade mått](../essentials/metrics-custom-overview.md) använder alltid null-värden när inga data tas emot. Med [plattforms mått](../data-platform.md)bestämmer varje resurs leverantör om du vill använda nollor eller null-värden baserat på vad som är mest begripligt för ett specifikt mått.

Azure Monitor aviseringar använder de värden som resurs leverantören skriver till mått databasen, så det är viktigt att veta hur resurs leverantören hanterar NULL-värden genom att först visa data.

## <a name="how-aggregation-works"></a>Så här fungerar agg regering

Mått diagram i föregående system visar olika typer av sammanställda data. Systemet församlar data i förväg så att de begärda diagrammen kan visa snabbare utan mycket upprepad beräkning.  

I det här exemplet:

- Vi samlar in ett **fiktivt** transaktions mått som kallas **http-problem** 
- *Server* är en dimension för **http-felen** .
- Vi har 3 servrar – Server A, B och C.

För att förenkla förklaringen börjar vi bara med agg regerings typen SUM. 

### <a name="sub-minute-to-1-minute-aggregation"></a>Under minut till 1 minut agg regering

Första rå metriska data samlas in och lagras i Azure Monitor Metrics-databasen. I det här fallet har varje server transaktions poster lagrade med en tidsstämpel eftersom *servern* är en dimension. Med tanke på att den minsta tids perioden du kan visa som en kund är 1 minut, summeras de här tidsstämplarna först i måtten på 1 minut för varje enskild server.  Sammansättnings processen för Server B visas i bilden nedan. Servrar A och C görs på samma sätt och har olika data.  

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction.png" alt-text="Skärm bild som visar transaktions poster i steg om 1 minut. " border="false":::

De resulterande 1 minuters mängd värdena lagras som nya poster i mått databasen, så att de kan samlas in för senare beräkningar. 

:::image type="content" source="media/metrics-aggregation-explained/sub-minute-transaction-dimension-aggregated.png" alt-text="Skärm bild som visar flera 1 minuters sammanställda poster i dimension för servern. Server A, B och C visas individuellt" border="false":::

### <a name="dimension-aggregation"></a>Dimensions agg regering

Beräkningarna på 1 minut komprimeras sedan av dimensionen och lagras igen som enskilda poster.   I det här fallet sammanställs alla data från alla enskilda servrar i ett intervall på 1 minut och lagras i mått databasen för användning i senare agg regeringar.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-transaction-dimension-flattened-aggregated.png" alt-text="Skärm bild som visar flera 1 minuters sammanställda poster för Server A, B och C sammanlagt till 1 minuts alla servrar, hela" border="false":::

För tydlighetens skull visar följande tabell metoden för agg regerings metoden.

| Period   | Server A | Server B | Server C | Sum (A + B + C)|
| -------- | -------- | -------- | -------- | --------   |  
| Minut 1 | 1        | 1        | 1        | 3          |
| Minut 2 | 0        | 5        | 1        | 6          |
| Minut 3 | 0        | 5        | 1        | 6          |
| Minut 4 | 2        | 3        | 4        | 9          |
| Minut 5 | 1        | 0        | 3        | 4          |
| Minute 6 | 1        | 0        | 4        | 5          |
| Minute 7 | 1        | 2        | 4        | 7          |
| Minut 8 | 0        | 1        | 0        | 1          |
| Minut 9 | 1        | 1        | 4        | 6          |
| Minut 10| 2        | 1        | 0        | 3          |

Endast en dimension visas ovan, men samma agg regerings-och lagrings process sker för **alla dimensioner** som ett mått stöder.

- Samla in värden i mängd 1-minuters uppsättning av dimensionen. Lagra dessa värden. 
- Dölj dimensionen i en sammanställd summa på 1 minut. Lagra dessa värden. 

Låt oss introducera en annan dimension av HTTP-problem som kallas NetworkAdapter. Låt oss säga att vi hade ett varierande antal kort per server.

- Server A har 1 kort
- Server B har 2 nätverkskort
- Server C har 3 kort

Vi samlar in data för följande transaktioner separat. De markeras med:

- En tid
- Ett värde
- Servern som transaktionen kom från
- Det kort som transaktionen kom från

Var och en av dessa under minuters strömmar summeras sedan i tids serie värden för 1 minut och lagras i Azure Monitor Metric-databasen:

- Server A, nätverkskort 1
- Server B, kort 1
- Server B, kort 2
- Server C, kort 1
- Server C, nätverkskort 2
- Server C, nätverkskort 3

Dessutom lagras även följande komprimerade agg regeringar:

- Server A, nätverkskort 1 (eftersom det inte finns något att komprimera, lagras det igen)
- Server B, kort 1 + 2
- Server C, nätverkskort 1 + 2 + 3
- ALLA servrar, alla kort

Detta visar att mät värden med ett stort antal dimensioner har ett större antal agg regeringar. Det är inte viktigt att känna till alla permutationer och bara förstå orsaken. Systemet vill ha både individuella data och aggregerade data som lagras för snabb hämtning för att få åtkomst till ett diagram. Systemet väljer antingen den mest relevanta lagrade agg regeringen eller underliggande rå data beroende på vad du väljer att visa. 

### <a name="aggregation-with-no-dimensions"></a>Agg regering utan dimensioner

Eftersom det här måttet har en dimensions *Server* kan du komma åt underliggande data för Server a, B och C ovan via delning och filtrering, vilket förklaras tidigare i den här artikeln. Om måttet inte hade någon *Server* som dimension, kunde du som kund bara komma åt de sammanställda 1-minuters summor som visas i svart i diagrammet. Det vill säga värdena 3, 6, 6, 9 osv. Systemet skulle inte heller göra det underliggande arbetet för att aggregera delnings värden som den aldrig använder dem i Metric Explorer eller skicka dem via måtten REST API. 

## <a name="viewing-time-granularities-above-1-minute"></a>Visa tids kornig het över 1 minut

Om du ber om mått med större precision använder systemet 1 minuts sammanlagda summor för att beräkna summorna för de större tids kornigheten.  Med streckade linjer visas summerings metoden för tids kornig het på 2 minuter och 5 minuter. Återigen visar vi bara summan av agg regerings typen för enkelhetens skull.

:::image type="content" source="media/metrics-aggregation-explained/1-minute-to-2-min-5-min.png" alt-text="Skärm bild som visar flera 1 minuters sammanställda poster över dimension för server som sammanställs till 2 – min-och 5-minuters period." border="false":::

För tids kornig het på 2 minuter.

| Period       | Otillbörlig       
| -------------|-------------|  
| Minut 1 & 2 | (3 + 6) = 9 |
| Minut 3 & 4 | (6 + 9) = 15|
| Minut 4 & 5 | (4 + 5) = 9 |
| Minute 6 & 7 | (7 + 1) = 8 |
| Minut 8 & 9 | (6 + 3) = 9 |

För tids kornig het på 5 minuter.

| Period              |            Otillbörlig        |
|---------------------|------------------------|  
| Minut 1 till 5  | 3 + 6 + 6 + 9 + 4 = 28 |
| Minut 6 till 10 | 5 + 7 + 1 + 6 + 3 = 22 |

Systemet använder de lagrade aggregerade data som ger bästa prestanda. 

Nedan visas det större diagrammet för agg regerings processen på 1 minut, med några av pilarna som lämnats ut för att förbättra läsbarheten.

:::image type="content" source="media/metrics-aggregation-explained/sum-aggregation-full.png" alt-text="Skärm bild som visar konsolidering av föregående 3 skärm bilder. Flera 1 minuts sammanställda poster över dimension för server som sammanställs i intervall på 1 minut, 2 minuter och 5 minuter. Server A, B och C visas individuellt" border="false":::

## <a name="more-complex-example"></a>Mer avancerat exempel

Följande är ett större exempel som använder värden för ett fiktivt mått som kallas HTTP-svars tid i millisekunder.  Här introducerar vi ytterligare komplexitets nivåer.

1. Vi visar agg regering för sum, Count, min och max och beräkningen för genomsnitt.
2. Vi visar NULL-värden och hur de påverkar beräkningar. 

Betänk följande exempel. Rutorna och pilarna visar exempel på hur värdena aggregeras och beräknas. 

Samma församlings process för 1 minut enligt beskrivningen i föregående avsnitt inträffar för summor, antal, minimum och maximum.  Genomsnitt är dock inte församlad. Den räknas om med hjälp av aggregerade data för att undvika beräknings fel.
 
:::image type="content" source="media/metrics-aggregation-explained/full-aggregation-example-all-types.png" alt-text="Skärm bild som visar ett komplext exempel på agg regering och beräkning av sum, Count, min, max och genomsnitt från 1 minut till 10 minuter." border="false" lightbox="media/metrics-aggregation-explained/full-aggregation-example-all-types.png":::

Överväg minut 6 för 1 minuts agg regering som är markerat ovan. Den här minuten är den punkt där Server B gick offline och slutade rapportera data, kanske på grund av en omstart. 

Från minut 6 ovan är de beräknade 1-minuters agg regerings typerna: 

| Sammansättningstyp | Värde        | Obs! |
|------------------|--------------|-------|
| Sum              | 53 + 20 = 73 | |
| Antal            | 2            | Visar resultatet av NULL-värden.  Värdet skulle ha varit 3 om servern hade varit online.  |
| Minimum          | 20           | |
| Maximal          | 53           | |
| Genomsnitt          | 73/2       | Alltid summan dividerat med antalet. Den lagras aldrig och räknas alltid om för varje tids kornig het med hjälp av de aggregerade talen för denna kornig het. Observera omberäkningen för tids kornig het på 5 minuter och 10 minuter som är markerade ovan. |

Den röda text färgen indikerar värden som kan anses vara utanför det normala intervallet och visar hur de sprids (eller Miss söker) när tids kornigheten går upp. Observera hur *minsta* och *högsta* anger att det finns underliggande avvikelser, medan *genomsnittet* och *summorna* förlorar den informationen när tids kornigheten går upp.  

Du kan också se att NULL-värden ger en bättre beräkning av genomsnitt än om nollor används i stället.  

> [!NOTE] 
> Även om det inte är fallet i det här exemplet är *antalet* lika med *Sum* i fall där ett mått alltid fångas med värdet 1. Detta är vanligt när ett mått spårar förekomsten av en transaktions händelse – till exempel antalet HTTP-problem som nämns i föregående exempel i den här artikeln.

## <a name="next-steps"></a>Nästa steg

- [Komma igång med Metrics Explorer](../essentials/metrics-getting-started.md)
- [Avancerad mått Utforskaren](../essentials/metrics-charts.md)
