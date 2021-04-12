---
title: Samla in loggdata
titleSuffix: Azure Cognitive Search
description: Samla in och analysera loggdata genom att aktivera en diagnostisk inställning och Använd sedan Kusto-frågespråket för att utforska resultatet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f0d85f056cfaaa58fcc72eb9c2182b3e1a78affb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581608"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Samla in och analysera loggdata för Azure Kognitiv sökning

Diagnostik-eller drift loggar ger inblick i de detaljerade åtgärderna i Azure Kognitiv sökning och är användbara för övervakning av tjänstens hälsa och processer. Internt bevarar Microsoft bevarar system information på Server delen under en kort tids period (cirka 30 dagar), tillräckligt för undersökning och analys om du använder ett support ärende. Men om du vill ha ägarskap över drift data bör du konfigurera en diagnostisk inställning för att ange var loggnings information samlas in.

Diagnostisk loggning aktive ras genom integrering med [Azure Monitor](../azure-monitor/index.yml). 

När du ställer in diagnostikloggning uppmanas du att ange en lagrings funktion. I följande tabell räknas alternativen för att samla in och bevara data.

| Resurs | Används för |
|----------|----------|
| [Skicka till Log Analytics-arbetsytan](../azure-monitor/essentials/tutorial-resource-logs.md) | Händelser och mått skickas till en Log Analytics-arbetsyta, som kan frågas i portalen för att returnera detaljerad information. En introduktion finns i [Kom igång med Azure Monitor loggar](../azure-monitor/logs/log-analytics-tutorial.md) |
| [Arkivera med Blob Storage](../storage/blobs/storage-blobs-overview.md) | Händelser och mått arkiveras i en BLOB-behållare och lagras i JSON-filer. Loggarna kan vara ganska detaljerade (per timme/minut) som är användbara för att söka efter en speciell incident men inte för en öppen undersökning. Använd en JSON-redigerare för att visa en rå logg fil eller Power BI för att sammanställa och visualisera loggdata.|
| [Strömma till Händelsehubben](../event-hubs/index.yml) | Händelser och mät värden strömmas till en Azure Event Hubs-tjänst. Välj det här som en alternativ data insamlings tjänst för mycket stora loggar. |

## <a name="prerequisites"></a>Förutsättningar

Skapa resurser i förväg så att du kan välja en eller flera när du konfigurerar diagnostisk loggning.

+ [Skapa en Log Analytics-arbetsyta](../azure-monitor/logs/quick-create-workspace.md)

+ [Skapa ett lagringskonto](../storage/common/storage-account-create.md)

+ [Skapa en händelsehubb](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>Aktivera datainsamling

Diagnostikinställningar anger hur loggade händelser och mått samlas in.

1. Under **Övervakning** väljer du **Diagnostikinställningar**.

   ![Diagnostikinställningar](./media/search-monitor-usage/diagnostic-settings.png "Diagnostikinställningar")

1. Välj **+ Lägg till diagnostisk inställning**

1. Markera **Log Analytics**, välj din arbets yta och välj **OperationLogs** och **AllMetrics**.

   ![Konfigurera datainsamling](./media/search-monitor-usage/configure-storage.png "Konfigurera datainsamling")

1. Spara inställningen.

1. När loggning har Aktiver ATS använder du Sök tjänsten för att börja generera loggar och mått. Det tar tid innan loggade händelser och mått blir tillgängliga.

För Log Analytics kommer det att vara flera minuter innan data är tillgängliga, och du kan köra Kusto-frågor för att returnera data. Mer information finns i [övervaka fråge förfrågningar](search-monitor-logs.md).

För Blob Storage tar det en timme innan behållarna visas i Blob Storage. Det finns en BLOB, per timme, per behållare. Behållare skapas endast när det finns en aktivitet som ska loggas eller mätas. När data kopieras till ett lagrings konto formateras data som JSON och placeras i två behållare:

+ Insights-logs-operationlogs: för Sök trafik loggar
+ Insights-mått – pt1m: för mått

## <a name="query-log-information"></a>Fråga logg information

Två tabeller innehåller loggar och mått för Azure Kognitiv sökning: **AzureDiagnostics** och **AzureMetrics**.

1. Under **övervakning** väljer du **loggar**.

1. Ange **AzureMetrics** i frågefönstret. Kör den här enkla frågan för att bekanta dig med de data som samlas in i den här tabellen. Bläddra i tabellen om du vill visa mått och värden. Observera antalet poster överst, och om tjänsten har samlat in måtten för ett tag kan du behöva justera tidsintervallet för att få en hanterbar data uppsättning.

   ![AzureMetrics-tabell](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics-tabell")

1. Ange följande fråga för att returnera en tabell resultat uppsättning.

   ```kusto
   AzureMetrics
   | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. Upprepa föregående steg, som börjar med **AzureDiagnostics** , för att returnera alla kolumner i informations syfte, följt av en mer selektiv fråga som hämtar mer intressant information.

   ```kusto
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics-tabell](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics-tabell")

## <a name="kusto-query-examples"></a>Exempel på Kusto-frågor

Om du har aktiverat diagnostikloggning kan du fråga **AzureDiagnostics** efter en lista över åtgärder som kördes på din tjänst och när. Du kan också korrelera aktiviteter för att undersöka prestanda ändringar.

#### <a name="example-list-operations"></a>Exempel: list åtgärder 

Returnera en lista över åtgärder och antal var och en.

```kusto
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>Exempel: korrelera åtgärder

Korrelera förfrågan med indexerings åtgärder och återge data punkter i ett tids diagram för att se att åtgärderna är sammanfallna.

```kusto
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>Loggade åtgärder

Loggade händelser som registrerats av Azure Monitor inkluderar de som är relaterade till indexering och frågor. **AzureDiagnostics** -tabellen i Log Analytics samlar in drift data som rör frågor och indexering.

| OperationName | Description |
|---------------|-------------|
| ServiceStats | Den här åtgärden är ett rutin anrop för att [Hämta tjänst statistik](/rest/api/searchservice/get-service-statistics), antingen kallat direkt eller implicit för att fylla i en portal översikts sida när den läses in eller uppdateras. |
| Fråga. search |  Fråga begär Anden mot ett index se [övervaka frågor](search-monitor-queries.md) för information om loggade frågor.|
| Indexering. index  | Den här åtgärden är ett anrop för att [lägga till, uppdatera eller ta bort dokument](/rest/api/searchservice/addupdate-or-delete-documents). |
| index. Prototyp | Det här är ett index som skapats av guiden Importera data. |
| Indexerare. skapa | Skapa en indexerare explicit eller implicit via guiden Importera data. |
| Indexerare. Hämta | Returnerar namnet på en indexerare när indexeraren körs. |
| Indexerare. status | Returnerar status för en indexerare när indexeraren körs. |
| Data källor. get | Returnerar namnet på data källan när en indexerare körs.|
| Index. get | Returnerar namnet på ett index när en indexerare körs. |

## <a name="log-schema"></a>Logg schema

Om du skapar anpassade rapporter överensstämmer de data strukturer som innehåller Azure Kognitiv sökning loggdata med schemat nedan. För Blob Storage har varje BLOB ett rot objekt som kallas **poster** som innehåller en matris med logg objekt. Varje Blob innehåller poster för alla åtgärder som ägde rum under samma timme.

Följande tabell är en del av listan över fält som är vanliga för resurs loggning.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| timeGenerated |datetime |"2018-12-07T00:00:43.6872559 Z" |Tidsstämpel för åtgärden |
| resourceId |sträng |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDARD/PROVIDERS/<br/> Utforskaren. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |Din ResourceId |
| operationName |sträng |"Fråga. search" |Åtgärdens namn |
| operationVersion |sträng |"2020-06-30" |Den API-version som används |
| category |sträng |"OperationLogs" |konstant |
| resultType |sträng |Resultatet |Möjliga värden: lyckades eller misslyckades |
| resultSignature |int |200 |HTTP-resultat kod |
| . Durationms |int |50 |Åtgärdens varaktighet i millisekunder |
| properties |objekt |Se följande tabell |Objekt som innehåller åtgärds information |

### <a name="properties-schema"></a>Egenskaps schema

Egenskaperna nedan är bara för Azure-Kognitiv sökning.

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| Description_s |sträng |"Hämta/Indexes (' Content ')/docs" |Åtgärdens slut punkt |
| Documents_d |int |42 |Antal bearbetade dokument |
| IndexName_s |sträng |"test-index" |Namnet på det index som är associerat med åtgärden |
| Query_s |sträng |"? search = AzureSearch&$count = True&API-version = 2020-06-30" |Frågeparametrar |

## <a name="metrics-schema"></a>Mått schema

Måtten fångas för fråge förfrågningar och mäts inom en minut. Varje mått visar de lägsta, högsta och genomsnittliga värdena per minut. Mer information finns i [övervaka fråge förfrågningar](search-monitor-queries.md).

| Namn | Typ | Exempel | Anteckningar |
| --- | --- | --- | --- |
| resourceId |sträng |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/STANDARD/PROVIDERS/<br/>Utforskaren. SEARCH/SEARCHSERVICES/SEARCHSERVICE " |resurs-ID |
| metricName |sträng |Svars tid |namnet på måttet |
| time |datetime |"2018-12-07T00:00:43.6872559 Z" |åtgärdens tidsstämpel |
| genomsnitt |int |64 |Medelvärdet för RAW-exemplen i mått tidsintervallet, enheter i sekunder eller procent, beroende på måttet. |
| min |int |37 |Det minsta värdet för RAW-exemplen i Metric-tidsintervallet, enheter i sekunder. |
| max |int |78 |Det maximala värdet för RAW-exemplen i Metric-tidsintervallet, enheter i sekunder.  |
| totalt |int |258 |Det totala värdet för RAW-exemplen i Metric-tidsintervallet, enheter i sekunder.  |
| count |int |4 |Antalet mått som har avsänts från en nod till loggen inom ett minut intervall.  |
| timegrain |sträng |"PT1M" |Tids kornig het för måttet i ISO 8601. |

Det är vanligt att frågor körs i millisekunder, så endast frågor som mäter hur många sekunder visas i mått som frågor per sekund.

För **Sök frågor per sekund** -mått är minimivärdet det lägsta värdet för Sök frågor per sekund som registrerades under den minuten. Samma gäller för det högsta värdet. Average, är aggregatet över hela minuten. Inom en minut kan du till exempel ha ett mönster som liknar detta: en sekund med hög belastning som är max för SearchQueriesPerSecond, följt av 58 sekunders genomsnittlig belastning och slutligen en sekund med bara en fråga, vilket är minimivärdet.

För **begränsade Sök frågor i procent**, lägsta, högsta, genomsnitt och total, har alla samma värde: procent andelen Sök frågor som har begränsats från det totala antalet Sök frågor under en minut.

## <a name="view-raw-log-files"></a>Visa obehandlade loggfiler

Blob Storage används för att arkivera loggfiler. Du kan Visa logg filen med valfri JSON-redigerare. Om du inte har en sådan rekommenderar vi [Visual Studio Code](https://code.visualstudio.com/download).

1. I Azure Portal öppnar du ditt lagrings konto. 

2. Klicka på **blobbar** i det vänstra navigerings fönstret. Du bör se **Insights-logs-operationlogs** och **Insights-Metrics-pt1m**. Dessa behållare skapas av Azure Kognitiv sökning när loggdata exporteras till Blob Storage.

3. Klicka på mapphierarkin tills du når. JSON-filen.  Använd snabb menyn för att hämta filen.

När filen har hämtats öppnar du den i en JSON-redigerare för att visa innehållet.

## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det kan du läsa grunderna i Sök tjänst övervakningen för att lära dig mer om alla funktioner för överblick.

> [!div class="nextstepaction"]
> [Övervaka åtgärder och aktiviteter i Azure Kognitiv sökning](search-monitor-usage.md)