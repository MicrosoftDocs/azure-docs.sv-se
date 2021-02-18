---
title: Mått, aviseringar och diagnostiska loggar
description: Registrera och analysera diagnostiska logg händelser för Azure Batch konto resurser som pooler och uppgifter.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: 83411d7018155955f5be71bd41803e510edbc9da
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592679"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch-mått, aviseringar och loggar för diagnostisk utvärdering och övervakning

Den här artikeln förklarar hur du övervakar ett batch-konto med hjälp av funktioner i [Azure Monitor](../azure-monitor/overview.md). Azure Monitor samlar in [Mät värden](../azure-monitor/essentials/data-platform-metrics.md) och [diagnostikloggar](../azure-monitor/essentials/platform-logs-overview.md) för resurser i batch-kontot. Samla in och Använd dessa data på flera olika sätt för att övervaka ditt batch-konto och diagnostisera problem. Du kan också konfigurera [mått varningar](../azure-monitor/alerts/alerts-overview.md) så att du får meddelanden när ett mått når ett angivet värde.

## <a name="batch-metrics"></a>Batch-mått

Mått är Azure-telemetridata (kallas även prestanda räknare) som genereras av dina Azure-resurser och som används av tjänsten Azure Monitor. Exempel på mått i ett batch-konto är pool för att skapa händelser, Low-Priority antal noder och aktivitetens kompletta händelser.

Se [listan över de batch-mått som stöds](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts).

Mått är:

- Aktive rad som standard i varje batch-konto utan ytterligare konfiguration
- Genereras var 1 minut
- Sparas inte automatiskt, men har en rullande historik på 30 dagar. Du kan spara aktivitets mått som en del av diagnostisk loggning.

## <a name="view-batch-metrics"></a>Visa batch-mått

På sidan Azure Portal visar **översikts** sidan för kontot nyckel Node-, Core-och aktivitets mått som standard.

Visa alla batch-kontots mått i Azure Portal:

1. I Azure Portal väljer du **alla tjänster**  >  **batch-konton** och väljer sedan namnet på batch-kontot.
2. Gå till **Övervakning** och välj **Mått**.
3. Välj **Lägg till mått** och välj sedan ett mått i list rutan.
4. Välj ett **agg regerings** alternativ för måttet. Använd den **genomsnittliga** agg regeringen för beräknings mått (t. ex. "dedikerat antal" eller "antal med låg prioritet"). För händelsebaserade mått (t. ex. När du ändrar storlek på en slutförd pool), använder du **Count agg Count**.

   > [!WARNING]
   > Använd inte agg regeringen "sum", som lägger till värdena för alla data punkter som tas emot under perioden i diagrammet.

5. Upprepa steg 3 och 4 om du vill lägga till ytterligare mått.

Du kan också hämta mått via programmering med Azure Monitor API: er. Ett exempel finns i [hämta Azure Monitor mått med .net](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

### <a name="batch-metric-reliability"></a>Batch-måttets tillförlitlighet

Mått kan hjälpa dig att identifiera trender och kan användas för data analys. Det är viktigt att Observera att mått leveransen inte är garanterad och kan vara beroende av leverans i annan ordning, data förlust och/eller duplicering. Därför rekommenderas inte användning av enskilda händelser för att varna eller utlösa funktioner. Mer information om hur du anger tröskelvärden för aviseringar finns i nästa avsnitt.

De värden som har spridits under de senaste 3 minuterna kan fortfarande aggregeras, så Mät värden kan underhållas under denna tidsram.

## <a name="batch-metric-alerts"></a>Batch Metric-aviseringar

Du kan konfigurera mått för nära real tids *aviseringar* som utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen genererar ett meddelande när aviseringen är "aktive rad" (när tröskelvärdet överskrids och varnings villkoret är uppfyllt) samt när det är "löst" (när tröskelvärdet överskrids igen och villkoret inte längre uppfylls).

Aviseringar som utlöser sig på en enskild data punkt rekommenderas inte, eftersom måtten är beroende av leverans, data förlust och/eller duplicering. När du skapar aviseringar kan du använda tröskelvärden för att redovisa dessa inkonsekvenser.

Du kanske t. ex. vill konfigurera en måtta avisering när antalet låg prioritets kärnor är en viss nivå, så att du kan justera sammansättningarna för dina pooler. För bästa resultat ställer du in en period på 10 eller mer minuter, där varningar utlöses om det genomsnittliga antalet låg prioritets kärnor sjunker under tröskelvärdet för hela perioden. Detta gör det möjligt för mått att aggregera så att du får mer exakta resultat.

Så här konfigurerar du en mått avisering i Azure Portal:

1. Välj **alla tjänster**  >  **batch-konton** och välj sedan namnet på batch-kontot.
2. Under **övervakning** väljer du **aviseringar** och väljer sedan **ny aviserings regel**.
3. Klicka på **Välj villkor** och välj sedan ett mått. Bekräfta värdena för **diagram period**, **tröskel typ**, **operator** och **sammansättnings typ** och ange ett **tröskelvärde**. Välj sedan **Done** (Klar).
4. Lägg till en åtgärds grupp i aviseringen antingen genom att välja en befintlig åtgärds grupp eller skapa en ny åtgärds grupp.
5. I avsnittet **aviserings regel information** anger du ett namn och en **Beskrivning** för **varnings regeln** och väljer **allvarlighets grad**
6. Välj **Skapa varningsregel**.

Mer information om hur du skapar mått aviseringar finns i [förstå hur mått varningar fungerar i Azure Monitor](../azure-monitor/alerts/alerts-metric-overview.md) och [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

Du kan också konfigurera en nästan real tids avisering med hjälp av Azure Monitor [REST API](/rest/api/monitor/). Mer information finns i [Översikt över aviseringar i Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Om du vill inkludera jobb, uppgift eller leverantörsspecifik information i dina aviseringar kan du läsa informationen om Sök frågor i [svara på händelser med Azure Monitor aviseringar](../azure-monitor/alerts/tutorial-response.md).

## <a name="batch-diagnostics"></a>Batch-diagnostik

Diagnostikloggar innehåller information som släpps av Azure-resurser som beskriver driften av varje resurs. För batch kan du samla in följande loggar:

- **Tjänst loggar** händelser som genereras av tjänsten Azure Batch under en enskild batch-resurss livs längd, till exempel en pool eller uppgift.
- **Mått** loggar på konto nivå.

Inställningar för att aktivera insamling av diagnostikloggar är inte aktiverade som standard. Aktivera diagnostiska inställningar uttryckligen för varje batch-konto som du vill övervaka.

### <a name="log-destinations"></a>Logg destinationer

Ett vanligt scenario är att välja ett Azure Storage konto som mål för loggen. Om du vill lagra loggar i Azure Storage skapar du kontot innan du aktiverar samling av loggar. Om du har kopplat ett lagrings konto till ditt batch-konto kan du välja det kontot som mål för loggen.

Alternativt kan du:

- Strömma logg händelser för batch-diagnostikloggar till en [Azure Event Hub](../event-hubs/event-hubs-about.md). Event Hubs kan mata in miljon tals händelser per sekund, som du sedan kan omvandla och lagra med valfri analys-Provider i real tid.
- Skicka diagnostikloggar till [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md)där du kan analysera dem eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan debiteras ytterligare kostnader för att lagra eller bearbeta diagnostikdata med Azure-tjänster.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av batch-diagnostikloggar

Följ stegen nedan om du vill skapa en ny diagnostisk inställning i Azure Portal.

1. I Azure Portal väljer du **alla tjänster**  >  **batch-konton** och väljer sedan namnet på batch-kontot.
2. Under **Övervakning** väljer du **Diagnostikinställningar**.
3. I **diagnostikinställningar** väljer du **Lägg till diagnostisk inställning**.
4. Ange ett namn för inställningen.
5. Välj ett mål: **Skicka till Log Analytics**, **arkivera till ett lagrings konto** eller **strömma till en Event Hub**. Om du väljer ett lagrings konto kan du välja att ange en bevarande princip. Om du inte anger ett antal dagar för kvarhållning behålls data under lagrings kontots livs längd.
6. Välj **ServiceLog**, **AllMetrics** eller båda.
7. Välj **Spara** för att skapa den diagnostiska inställningen.

Du kan också [Aktivera samling genom Azure Monitor i Azure Portal](../azure-monitor/essentials/diagnostic-settings.md) för att konfigurera diagnostikinställningar, med hjälp av en [Resource Manager-mall](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)eller med Azure PowerShell eller Azure CLI. Mer information finns i [Översikt över Azures plattforms loggar](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Åtkomst till diagnostikloggar i Storage

Om du arkiverar batch-diagnostikloggar i ett lagrings konto skapas en lagrings behållare i lagrings kontot så snart en relaterad händelse inträffar. Blobbar skapas enligt följande namn mönster:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Exempel:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Varje `PT1H.json` BLOB-fil innehåller JSON-formaterade händelser som inträffat inom den timme som anges i BLOB-URL: en (till exempel `h=12` ). Under den aktuella timmen läggs händelser till i `PT1H.json` filen när de inträffar. Minut värdet ( `m=00` ) är alltid `00` , eftersom diagnostiska logg händelser bryts till enskilda blobbar per timme. (Alla tider är i UTC-tid.)

Nedan visas ett exempel på en `PoolResizeCompleteEvent` post i en `PT1H.json` loggfil. Den innehåller information om aktuella och mål för dedikerade och låg prioritets noder, samt start-och slut tid för åtgärden:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Mer information om schemat för diagnostikloggar i lagrings kontot finns i [arkivera Azures resurs loggar till lagrings kontot](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Använd Storage-API: er för att komma åt loggarna i ditt lagrings konto program mässigt.

### <a name="service-log-events"></a>Tjänst logg händelser

Azure Batch tjänst loggar, om de samlas in, innehåller händelser som avsänts av tjänsten Azure Batch under en enskild batch-resurs, till exempel en pool eller uppgift. Varje händelse som släpps av batch är inloggad i JSON-format. Detta är till exempel bröd texten i en exempel händelse för att **skapa en pool**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Tjänst logg händelser som genereras av batch-tjänsten inkluderar följande:

- [Skapa pool](batch-pool-create-event.md)
- [Start för borttagning av pool](batch-pool-delete-start-event.md)
- [Borttagning av pool slutförd](batch-pool-delete-complete-event.md)
- [Start av poolens storleks ändring](batch-pool-resize-start-event.md)
- [Storleks ändring av pool slutförd](batch-pool-resize-complete-event.md)
- [Autoskalning av pool](batch-pool-autoscale-event.md)
- [Uppgiftens start](batch-task-start-event.md)
- [Uppgiften slutförd](batch-task-complete-event.md)
- [Åtgärden kunde inte utföras](batch-task-fail-event.md)
- [Aktivitets schema fungerar inte](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Nästa steg

- Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
- Lär dig mer om att [övervaka batch-lösningar](monitoring-overview.md).