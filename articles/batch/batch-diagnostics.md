---
title: Mått, aviseringar och diagnostikloggar
description: Registrera och analysera diagnostiklogghändelser för att Azure Batch-kontoresurser som pooler och uppgifter.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 61aaca84b609aaf7513c6de6f0f7e73aef5a5efe
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389323"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batchmått, aviseringar och loggar för diagnostisk utvärdering och övervakning

Azure Monitor samlar in [mått och](../azure-monitor/essentials/data-platform-metrics.md) [diagnostikloggar](../azure-monitor/essentials/platform-logs-overview.md) för resurser i ditt Azure Batch konto.

Du kan samla in och använda dessa data på flera olika sätt för att övervaka Ditt Batch-konto och diagnostisera problem. Du kan också konfigurera [måttaviseringar](../azure-monitor/alerts/alerts-overview.md) så att du får meddelanden när ett mått når ett angivet värde.

## <a name="batch-metrics"></a>Batch-mått

[Mått är](../azure-monitor/essentials/data-platform-metrics.md)  Azure-telemetridata (kallas även prestandaräknare) som genereras av dina Azure-resurser och används av Azure Monitor tjänsten. Exempel på mått i ett Batch-konto är Händelser för att skapa pooler, Low-Priority antal noder och Slutföra aktivitetshändelser. De här måtten kan hjälpa dig att identifiera trender och kan användas för dataanalys.

Se listan [över Batch-mått som stöds.](../azure-monitor/essentials/metrics-supported.md#microsoftbatchbatchaccounts)

Mått är:

- Aktiverat som standard i varje Batch-konto utan ytterligare konfiguration
- Genereras var 1 minut
- Sparas inte automatiskt, men har en rullande 30-dagars historik. Du kan bevara aktivitetsmått som en del av diagnostisk loggning.

## <a name="view-batch-metrics"></a>Visa Batch-mått

I Azure Portal visar **sidan Översikt** för Batch-kontot nyckelnods-, kärn- och uppgiftsmått som standard.

Så här visar du ytterligare mått för ett Batch-konto:

1. I den Azure Portal väljer du **Alla**  >  **tjänster Batch-konton** och sedan namnet på ditt Batch-konto.
1. Gå till **Övervakning** och välj **Mått**.
1. Välj **Lägg till** mått och välj sedan ett mått i listrutan.
1. Välj ett **aggregeringsalternativ** för måttet. För antalsbaserade mått (t.ex. "Antal dedikerade kärnor" eller "Antal noder med låg prioritet") använder du **genomsnittsaggregeringen.** För händelsebaserade mått (t.ex. "Slutför händelser för storleksändring av pool") använder du **sammansättningen** Antal. Undvik att använda **sammansättningen** Sum, som summerar värdena för alla datapunkter som tas emot under diagrammets period.
1. Upprepa steg 3 och 4 om du vill lägga till ytterligare mått.

Du kan också hämta mått programmatiskt med de Azure Monitor API:erna. Ett exempel finns i [Hämta Azure Monitor mått med .NET](/samples/azure-samples/monitor-dotnet-metrics-api/monitor-dotnet-metrics-api/).

> [!NOTE]
> Mått som har rapporterats under de senaste 3 minuterna kan fortfarande aggregeras, så värden kan underrapporteras under den här tidsramen. Måttleverans garanteras inte och kan påverkas av leverans utanför order, dataförlust eller duplicering.

## <a name="batch-metric-alerts"></a>Batch-måttaviseringar

Du kan konfigurera måttaviseringar i nära realtid som utlöses när värdet för ett angivet mått passerar ett tröskelvärde som du tilldelar. Aviseringen genererar ett meddelande när aviseringen är "Aktiverad" (när tröskelvärdet uppnås och aviseringsvillkoret uppfylls) samt när det är "Löst" (när tröskelvärdet har uppnåtts igen och villkoret inte längre uppfylls).

Eftersom måttleverans kan vara föremål för inkonsekvenser, till exempel leverans utanför beställning, dataförlust eller duplicering, rekommenderar vi att du undviker aviseringar som utlöses på en enda datapunkt. Använd istället tröskelvärden för att ta hänsyn till eventuella inkonsekvenser, till exempel leverans utanför order, dataförlust och duplicering under en viss tidsperiod.

Du kanske till exempel vill konfigurera en måttavisering när antalet kärnor med låg prioritet sjunker till en viss nivå, så att du kan justera sammansättningen av dina pooler. För bästa resultat anger du en period på 10 eller fler minuter, där aviseringen utlöses om det genomsnittliga antalet kärnor med låg prioritet sjunker under tröskelvärdet för hela perioden. Det gör att måtten kan aggregeras så att du får mer korrekta resultat.

Så här konfigurerar du en måttavisering i Azure Portal:

1. Välj **Alla tjänster**  >  **Batch-konton** och välj sedan namnet på ditt Batch-konto.
1. Under **Övervakning** väljer du **Aviseringar** och sedan Ny **aviseringsregel.**
1. Välj **Lägg till** villkor och välj sedan ett mått.
1. Välj önskade värden för **Diagramperiod,** **Tröskelvärde,** **Operator** och **Sammansättningstyp.**
1. Ange ett **tröskelvärde och** välj **Enhet för** tröskelvärdet.  Välj sedan **Done** (Klar).
1. Lägg till [en åtgärdsgrupp](../azure-monitor/alerts/action-groups.md) i aviseringen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.
1. I avsnittet **Information om aviseringsregel** anger du namnet **på aviseringsregeln** och **beskrivningen**. Om du vill att aviseringen ska aktiveras omedelbart ser du till att **rutan Aktivera aviseringsregel när den** skapas är markerad.
1. Välj **Skapa varningsregel**.

Mer information om hur du skapar måttaviseringar finns i Förstå hur måttaviseringar fungerar [i Azure Monitor](../azure-monitor/alerts/alerts-metric-overview.md) och Skapa, visa och hantera [måttaviseringar med hjälp av Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

Du kan också konfigurera en avisering nästan i realtid med hjälp av [Azure Monitor REST API](/rest/api/monitor/). Mer information finns i [Översikt över aviseringar i Microsoft Azure](../azure-monitor/alerts/alerts-overview.md). Om du vill inkludera jobb-, uppgifts- eller poolspecifik information i dina aviseringar kan du se [Svara på händelser med Azure Monitor Aviseringar](../azure-monitor/alerts/tutorial-response.md).

## <a name="batch-diagnostics"></a>Batch-diagnostik

[Diagnostikloggar](../azure-monitor/essentials/platform-logs-overview.md) innehåller information som genereras av Azure-resurser som beskriver hur varje resurs fungerar. För Batch kan du samla in följande loggar:

- **ServiceLog:** [händelser som genereras av Batch-tjänsten](#service-log-events) under livslängden för en enskild resurs, till exempel en pool eller uppgift.
- **AllMetrics:** Mått på Batch-kontonivå.

Du måste uttryckligen aktivera diagnostikinställningar för varje Batch-konto som du vill övervaka.

### <a name="log-destination-options"></a>Alternativ för loggmål

Ett vanligt scenario är att välja ett Azure Storage-konto som loggmål. Om du vill lagra loggar Azure Storage skapar du kontot innan du aktiverar insamling av loggar. Om du har associerat ett lagringskonto med ditt Batch-konto kan du välja det kontot som loggmål.

Alternativt kan du:

- Strömma Batch-diagnostiklogghändelser till en [Azure Event Hub](../event-hubs/event-hubs-about.md). Event Hubs kan mata in miljontals händelser per sekund, som du sedan kan transformera och lagra med valfri leverantör av realtidsanalys.
- Skicka diagnostikloggar [till Azure Monitor,](../azure-monitor/logs/log-query-overview.md)där du kan analysera dem eller exportera dem för analys i Power BI eller Excel.

> [!NOTE]
> Du kan medföra ytterligare kostnader för att lagra eller bearbeta diagnostikloggdata med Azure-tjänster.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Aktivera insamling av Batch-diagnostikloggar

Följ stegen nedan om du vill skapa en Azure Portal diagnostikinställning i listan.

1. I den Azure Portal väljer du **Alla tjänster**  >  **Batch-konton** och sedan namnet på ditt Batch-konto.
2. Under **Övervakning** väljer du **Diagnostikinställningar**.
3. I **Diagnostikinställningar väljer** du Lägg **till diagnostikinställning**.
4. Ange ett namn för inställningen.
5. Välj ett mål: **Skicka till Log Analytics,** **Arkivera till ett lagringskonto** eller **Strömma till en händelsehubb.** Om du väljer ett lagringskonto kan du välja hur många dagar som data ska behållas för varje logg. Om du inte anger ett antal dagar för kvarhållning bevaras data under lagringskontots livslängd.
6. Välj **ServiceLog**, **AllMetrics** eller båda.
7. Välj **Spara** för att skapa diagnostikinställningen.

Du kan också aktivera logginsamling genom att skapa [diagnostikinställningar i Azure Portal](../azure-monitor/essentials/diagnostic-settings.md), med en [Resource Manager mall](../azure-monitor/essentials/resource-manager-diagnostic-settings.md)eller med hjälp Azure PowerShell eller Azure CLI. Mer information finns i Översikt [över Azure-plattformsloggar.](../azure-monitor/essentials/platform-logs-overview.md)

### <a name="access-diagnostics-logs-in-storage"></a>Få åtkomst till diagnostikloggar i lagring

Om du [arkiverar Batch-diagnostikloggar](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)i ett lagringskonto skapas en lagringscontainer i lagringskontot så fort en relaterad händelse inträffar. Blobar skapas enligt följande namngivningsmönster:

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

Varje `PT1H.json` blobfil innehåller JSON-formaterade händelser som inträffat inom den timme som anges i blob-URL:en (till exempel `h=12` ). Under den aktuella timmen läggs händelser till i filen `PT1H.json` när de inträffar. Minutvärdet ( `m=00` ) är alltid , eftersom `00` diagnostiklogghändelser delas upp i enskilda blobar per timme. (Alla tider är i UTC.)

Nedan visas ett exempel på en `PoolResizeCompleteEvent` post i en `PT1H.json` loggfil. Den innehåller information om aktuellt och målantal dedikerade noder och lågprioriterade noder, samt start- och sluttid för åtgärden:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Om du vill komma åt loggarna i ditt lagringskonto programmatiskt använder du Storage-API:erna.

### <a name="service-log-events"></a>Tjänstlogghändelser

Azure Batch-tjänstloggar innehåller händelser som genereras av Batch-tjänsten under en enskild Batch-resurss livslängd, till exempel en pool eller uppgift. Varje händelse som genereras av Batch loggas i JSON-format. Det här är till exempel brödtexten i en exempelpool **för att skapa händelse**:

```json
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Tjänstlogghändelser som genereras av Batch-tjänsten omfattar följande:

- [Skapa pool](batch-pool-create-event.md)
- [Start av borttagning av pool](batch-pool-delete-start-event.md)
- [Borttagning av pool slutförd](batch-pool-delete-complete-event.md)
- [Start för storleksändring av pool](batch-pool-resize-start-event.md)
- [Storleksändringen för poolen är klar](batch-pool-resize-complete-event.md)
- [Automatisk skalning av pool](batch-pool-autoscale-event.md)
- [Uppgiftsstart](batch-task-start-event.md)
- [Uppgiften är klar](batch-task-complete-event.md)
- [Aktiviteten misslyckades](batch-task-fail-event.md)
- [Uppgiftsschemat misslyckas](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Nästa steg

- Läs om tillgängliga [Batch-API:er och verktyg](batch-apis-tools.md) för att skapa Batch-lösningar.
- Läs mer om [övervakning av Batch-lösningar.](monitoring-overview.md)
