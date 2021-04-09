---
title: Övervaka frågor
titleSuffix: Azure Cognitive Search
description: Övervaka frågans mått för prestanda och data flöde. Samla in och analysera indata från frågesträngar i resurs loggar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592386"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Övervaka fråge förfrågningar i Azure Kognitiv sökning

Den här artikeln förklarar hur du mäter frågans prestanda och volym med hjälp av mått och resurs loggning. Här beskrivs också hur du samlar in de indata som används i frågor – nödvändig information när du behöver utvärdera verktyget och effektiviteten hos din Sök sökkorpus.

Historiska data som matas in i mått bevaras i 30 dagar. För längre kvarhållning eller för att rapportera om drift data och frågesträngar, måste du aktivera en [diagnostisk inställning](search-monitor-logs.md) som anger ett lagrings alternativ för att spara loggade händelser och mått.

Villkor som maximerar integriteten för data mätning är:

+ Använd en fakturerings bar tjänst (en tjänst som skapats antingen på Basic-eller standard-nivån). Den kostnads fria tjänsten delas av flera prenumeranter, som introducerar en viss mängd flyktiga som inläsnings SKIFT.

+ Använd en enda replik och partition, om möjligt, för att skapa en innesluten och isolerad miljö. Om du använder flera repliker, genomsnitts frågans mått över flera noder, vilket kan minska precisionen i resultatet. På samma sätt innebär flera partitioner att data delas, med möjlighet att vissa partitioner kan ha olika data om indexeringen också pågår. Vid justering av frågans prestanda ger en enda nod och partition en mer stabil miljö för testning.

> [!Tip]
> Med ytterligare kod och Application Insights på klient sidan kan du också samla in genomklickning-data för djupare insikt i vad som är intressant för dina program användare. Mer information finns i [Sök efter trafik analys](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Fråga volym (frågor per sekund)

Volymen mäts som **Sök frågor per sekund** (frågor per sekund), ett inbyggt mått som kan rapporteras som ett medelvärde, antal, minimala eller maximala värden för frågor som körs i ett minut fönster. En-minuters intervall (TimeGrain = "PT1M") för mått är fasta i systemet.

Det är vanligt att frågor körs i millisekunder, så endast frågor som mäter hur många sekunder visas i mått.

| Sammansättningstyp | Beskrivning |
|------------------|-------------|
| Genomsnitt | Genomsnittligt antal sekunder inom en minut under vilken frågekörningen genomfördes.|
| Antal | Antalet mått som har spridits till loggen inom en minuters intervall. |
| Maximal | Det högsta antalet Sök frågor per sekund som registrerats under en minut. |
| Minimum | Det lägsta antalet Sök frågor per sekund som registrerats under en minut.  |
| Sum | Summan av alla frågor som körs inom minuten.  |

Inom en minut kan du till exempel ha ett mönster som liknar detta: en sekund med hög belastning som är max för SearchQueriesPerSecond, följt av 58 sekunders genomsnittlig belastning och slutligen en sekund med bara en fråga, vilket är minimivärdet.

Ett annat exempel: om en nod avger 100-mått, där värdet för varje mått är 40, så är "count" den 100, "sum" är 4000, "Average" är 40 och "Max" är 40.

## <a name="query-performance"></a>Frågeprestanda

I hela världen mäts frågans prestanda som Sök fördröjning (hur lång tid en fråga tar att slutföra) och begränsade frågor som släppts som ett resultat av resurs konkurrens.

### <a name="search-latency"></a>Sök svars tid

| Sammansättningstyp | Svarstid | 
|------------------|---------|
| Genomsnitt | Genomsnittlig fråge varaktighet i millisekunder. | 
| Antal | Antalet mått som har spridits till loggen inom en minuters intervall. |
| Maximal | Den längsta aktiva frågan i exemplet. | 
| Minimum | Kortast körning av fråga i exemplet.  | 
| Totalt | Total körnings tid för alla frågor i exemplet, som körs inom intervallet (en minut).  |

Tänk på följande exempel på **Sök fördröjnings** mått: 86 frågor har samplats, med en genomsnittlig varaktighet på 23,26 millisekunder. Minst 0 anger att vissa frågor har släppts. Den längsta aktiva frågan tog 1000 millisekunder att slutföra. Den totala körnings tiden var 2 sekunder.

![Latens-aggregeringar](./media/search-monitor-usage/metrics-latency.png "Latens-aggregeringar")

### <a name="throttled-queries"></a>Begränsade frågor

Begränsade frågor refererar till frågor som släpps i stället för process. I de flesta fall är begränsningen en normal del av att köra tjänsten.  Det är inte nödvändigt vis något att indikera att det inte finns något fel.

Begränsningen inträffar när antalet begär Anden som för närvarande bearbetas överskrider de tillgängliga resurserna. Du kan se en ökning av begränsade begär anden när en replik tas bort från rotationen eller under indexeringen. Både fråge-och indexerings begär Anden hanteras av samma uppsättning resurser.

Tjänsten bestämmer om begär Anden ska släppas utifrån resursförbrukning. Procent andelen resurser som förbrukas över minne, CPU och disk-i/o beräknas under en viss tids period. Om den här procent andelen överskrider ett tröskelvärde begränsas alla begär anden till indexet tills volymen av begär Anden minskas. 

Beroende på din klient kan en begränsad begäran anges på följande sätt:

+ En tjänst returnerar ett fel "du skickar för många begär Anden. Försök igen senare.” 
+ En tjänst returnerar en 503-felkod som anger att tjänsten inte är tillgänglig för tillfället. 
+ Om du använder portalen (till exempel search Explorer) ignoreras frågan tyst och du måste klicka på Sök igen.

För att bekräfta begränsade frågor, Använd **begränsade Sök frågor** mått. Du kan utforska mått i portalen eller skapa ett varnings mått enligt beskrivningen i den här artikeln. Använd *Total* för frågor som släppts inom samplings intervallet för att hämta procent andelen frågor som inte kördes.

| Sammansättningstyp | Begränsning |
|------------------|-----------|
| Genomsnitt | Procent andel frågor som tagits bort inom intervallet. |
| Antal | Antalet mått som har spridits till loggen inom en minuters intervall. |
| Maximal | Procent andel frågor som tagits bort inom intervallet.|
| Minimum | Procent andel frågor som tagits bort inom intervallet. |
| Totalt | Procent andel frågor som tagits bort inom intervallet. |

För **begränsade Sök frågor i procent**, lägsta, högsta, genomsnitt och total, har alla samma värde: procent andelen Sök frågor som har begränsats från det totala antalet Sök frågor under en minut.

I följande skärm bild är det första talet antalet (eller antalet mått som skickas till loggen). Ytterligare agg regeringar, som visas högst upp eller vid hovring över måttet, inklusive medelvärde, maximum och total. I det här exemplet släpptes inga förfrågningar.

![Begränsade agg regeringar](./media/search-monitor-usage/metrics-throttle.png "Begränsade agg regeringar")

## <a name="explore-metrics-in-the-portal"></a>Utforska mått i portalen

För en snabb titt på aktuella siffror visar fliken **övervakning** på sidan för tjänst översikt tre mått (**Sök svars tid**, **Sök frågor per sekund (per söknings enhet)**, **begränsade Sök frågor i procent**) över fasta intervall mätt i timmar, dagar och veckor, med alternativet att ändra agg regerings typen.

Öppna Metrics Explorer från **övervaknings** menyn för djupare utforskning, så att du kan skikta, zooma in och visualisera data för att utforska trender eller avvikelser. Lär dig mer om Metrics Explorer genom att slutföra den här [självstudien om hur du skapar ett mått diagram](../azure-monitor/essentials/tutorial-metrics-explorer.md).

1. Under avsnittet övervakning väljer du **mått** för att öppna mått Utforskaren med omfånget som angetts till din Sök tjänst.

1. Under mått väljer du ett från List rutan och granskar listan över tillgängliga agg regeringar för en önskad typ. Agg regeringen definierar hur insamlade värden kommer att samplas över varje tidsintervall.

   ![Mått Utforskaren för frågor per sekund-mått](./media/search-monitor-usage/metrics-explorer-qps.png "Mått Utforskaren för frågor per sekund-mått")

1. Ange tidsintervall i det övre högra hörnet.

1. Välj en visualisering. Standardvärdet är ett linje diagram.

1. Skikt ytterligare agg regeringar genom att välja **Lägg till mått** och välja olika agg regeringar.

1. Zooma in i ett intresse områden i linje diagrammet. Placera mus pekaren i början av ytan, klicka och håll ned vänster MUSKNAPP, dra till den andra sidan av arean och släpp knappen. Diagrammet kommer att zooma in inom det tidsintervallet.

## <a name="identify-strings-used-in-queries"></a>Identifiera strängar som används i frågor

När du aktiverar resurs loggning fångar systemet fråge förfrågningar i **AzureDiagnostics** -tabellen. Som en förutsättning måste du redan ha aktiverat [resurs loggning](search-monitor-logs.md), ange en Log Analytics-arbetsyta eller ett annat lagrings alternativ.

1. Under avsnittet övervakning väljer du **loggar** för att öppna ett tomt frågefönster i Log Analytics.

1. Kör följande uttryck för att söka efter fråga. Sök åtgärder, returnera en tabell resultat uppsättning som består av åtgärds namnet, frågesträngen, det efterfrågade indexet och antalet dokument som hittades. De sista två satserna utesluter frågesträngar som består av en tom eller ospecificerad sökning, över ett exempel index, vilket minskar bruset i resultatet.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Du kan också ange ett kolumn filter på *Query_s* om du vill söka över en speciell syntax eller sträng. Du kan till exempel filtrera över *är lika* med `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Loggade frågesträngar](./media/search-monitor-usage/log-query-strings.png "Loggade frågesträngar")

Även om den här tekniken fungerar för ad hoc-undersökning kan du med hjälp av skapa en rapport konsolidera och presentera frågesträngarna i en layout mer gynnsamt analys.

## <a name="identify-long-running-queries"></a>Identifiera tids krävande frågor

Lägg till kolumnen varaktighet för att hämta numren för alla frågor, inte bara de som hämtas som ett mått. Sortering av dessa data visar vilka frågor som tar den längsta att slutföra.

1. Under avsnittet övervakning väljer du **loggar** för att fråga efter logg information.

1. Kör följande fråga för att returnera frågor, sorterade efter varaktighet i millisekunder. De längsta körnings frågorna visas överst.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Sortera frågor efter varaktighet](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Sortera frågor efter varaktighet")

## <a name="create-a-metric-alert"></a>Skapa en måtta avisering

En måtta-varning fastställer ett tröskelvärde då du antingen får ett meddelande eller utlöser en korrigerings åtgärd som du definierar i förväg. 

För en Sök tjänst är det vanligt att skapa en mått avisering för Sök fördröjning och begränsade frågor. Om du vet när frågor släpps kan du söka efter lösningar som minskar belastningen eller ökar kapaciteten. Om till exempel begränsade frågor ökar under indexeringen kan du skjuta upp det tills du får en fråga om aktivitetens under sidor.

När du överför gränserna för en viss konfiguration av replik-partitionen, är det också användbart att ställa in aviseringar för frågor per sekund (Query Volume Thresholds).

1. Under avsnittet övervakning väljer du **aviseringar** och klickar sedan på **+ ny varnings regel**. Se till att Sök tjänsten är vald som resurs.

1. Klicka på **Lägg till** under villkor.

1. Konfigurera signal logik. Välj **mått** i signal typ och välj sedan signalen.

1. När du har valt signalen kan du använda ett diagram för att visualisera historiska data för ett informerat beslut om hur du ska gå vidare med att konfigurera villkor.

1. Rulla sedan ned till aviserings logiken. För koncept bevis kan du ange ett artificiellt lågt värde för test ändamål.

   ![Aviserings logik](./media/search-monitor-usage/alert-logic-qps.png "Aviserings logik")

1. Ange eller skapa sedan en åtgärds grupp. Detta är svaret på anropet när tröskelvärdet är uppfyllt. Det kan vara ett push-meddelande eller ett automatiskt svar.

1. Sista, ange aviserings information. Namnge och beskriv aviseringen, tilldela ett allvarlighets värde och ange om regeln ska skapas i ett aktiverat eller inaktiverat tillstånd.

   ![Aviserings information](./media/search-monitor-usage/alert-details.png "Aviseringsinformation")

Om du har angett ett e-postmeddelande får du ett e-postmeddelande från "Microsoft Azure" med ämnes raden "Azure: activated allvarlighets grad: 3 `<your rule name>` ".

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du läsa grunderna i Sök tjänst övervakningen för att lära dig mer om alla funktioner för överblick.

> [!div class="nextstepaction"]
> [Övervaka åtgärder och aktiviteter i Azure Kognitiv sökning](search-monitor-usage.md)