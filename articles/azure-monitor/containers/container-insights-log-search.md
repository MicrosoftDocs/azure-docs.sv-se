---
title: Så här frågar du efter loggar från container Insights | Microsoft Docs
description: Behållar insikter samlar in statistik och loggdata och den här artikeln beskriver posterna och innehåller exempel frågor.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: c2b7331255e1109f27f89a84d66e25eb07a20569
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201387"
---
# <a name="how-to-query-logs-from-container-insights"></a>Så här frågar du efter loggar från behållar insikter

Container Insights samlar in prestanda statistik, inventerings data och hälso tillstånds information från behållar värdar och behållare. Data samlas in var tredje minut och vidarebefordras till Log Analytics arbets ytan i Azure Monitor. Dessa data är tillgängliga för [fråga](../logs/log-query-overview.md) i Azure Monitor. Du kan använda dessa data i scenarier som omfattar migrerings planering, kapacitets analys, identifiering och prestanda fel sökning på begäran.

## <a name="container-records"></a>Container poster

I följande tabell ges information om poster som samlas in av behållar insikter. En lista över kolumn beskrivningar finns i referensen för tabellerna [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) och [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) .

| Data | Datakälla | Datatyp | Fält |
|------|-------------|-----------|--------|
| Container inventering | Kubelet | `ContainerInventory` | TimeGenerated, dator, namn, ContainerHostname, image, ImageTag, ContainerState, ExitCode, EnvironmentVar, kommando, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Behållar logg | Docker | `ContainerLog` | TimeGenerated, dator, avbildnings-ID, namn, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Inventering av container nod | Kube-API | `ContainerNodeInventory`| TimeGenerated, dator, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Inventering av poddar i ett Kubernetes-kluster | Kube-API | `KubePodInventory` | TimeGenerated, dator, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, container status, ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, kluster namn, PodIp, SourceSystem |
| Inventering av noder som ingår i ett Kubernetes-kluster | Kube-API | `KubeNodeInventory` | TimeGenerated, dator, kluster namn, ClusterId, LastTransitionTimeReady, etiketter, status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
|Inventering av beständiga volymer i ett Kubernetes-kluster |Kube-API |`KubePVInventory` | TimeGenerated, PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, ClusterId, kluster namn, _ResourceId, SourceSystem |
| Kubernetes-händelser | Kube-API | `KubeEvents` | TimeGenerated, dator, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, meddelande, SourceSystem | 
| Tjänster i Kubernetes-klustret | Kube-API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Prestanda mått för noder som ingår i Kubernetes-klustret | Användnings statistik hämtas från cAdvisor och begränsningar från Kube-API: et | `Perf \| where ObjectName == "K8SNode"` | Dator, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Prestanda mått för container delar i Kubernetes-klustret | Användnings statistik hämtas från cAdvisor och begränsningar från Kube-API: et | `Perf \| where ObjectName == "K8SContainer"` | CounterName &#40;cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Anpassade mått ||`InsightsMetrics` | Dator, namn, namn område, ursprung, SourceSystem, Taggar<sup>1</sup>, TimeGenerated, typ, Va, _ResourceId | 

<sup>1</sup> egenskapen *taggar* representerar [flera dimensioner](../essentials/data-platform-metrics.md#multi-dimensional-metrics) för motsvarande mått. Mer information om de mått som samlas in och lagras i `InsightsMetrics` tabellen och en beskrivning av post egenskaperna finns i [Översikt över InsightsMetrics](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Sök i loggar för att analysera data

Azure Monitor loggar kan hjälpa dig att söka efter trender, diagnostisera Flask halsar, prognostisera eller korrelera data som kan hjälpa dig att avgöra om den aktuella kluster konfigurationen fungerar optimalt. Fördefinierade loggs ökningar tillhandahålls så att du direkt kan börja använda eller anpassa för att returnera den information som du vill.

Du kan analysera data interaktivt i arbets ytan genom att välja alternativet **Visa Kubernetes händelse loggar** eller **Visa behållar loggar** i fönstret för förhands granskning i list rutan **Visa i Analytics** . Sidan **loggs ökning** visas till höger om Azure Portal sidan som du var på.

![Analysera data i Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)

Behållaren loggar utdata som vidarebefordras till din arbets yta är STDOUT och STDERR. Eftersom Azure Monitor övervakar Azure-hanterad Kubernetes (AKS) samlas Kube inte in idag på grund av den stora mängden genererade data. 

### <a name="example-log-search-queries"></a>Exempel på loggs öknings frågor

Det är ofta användbart att skapa frågor som börjar med ett exempel eller två och sedan ändra dem så att de passar dina behov. För att hjälpa till att bygga mer avancerade frågor kan du experimentera med följande exempel frågor:

### <a name="list-all-of-a-containers-lifecycle-information"></a>Lista all information om livs cykeln för en behållare

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Kubernetes-händelser

``` kusto
KubeEvents_CL
| where not(isempty(Namespace_s))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>Bild inventering

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>Container-CPU

**Välj visnings alternativ för linje diagram**

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Container minne

**Välj visnings alternativ för linje diagram**

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Begär Anden per minut med anpassade mått

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```

## <a name="query-prometheus-metrics-data"></a>Fråga Prometheus Metrics-data

Följande exempel är en Prometheus mått fråga som visar disk läsningar per sekund per disk per nod.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Om du vill visa Prometheus-mått som har klippts av Azure Monitor filtrerade efter namnrymd anger du "Prometheus". Här är en exempel fråga för att Visa Prometheus-mått från `default` namn området Kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Prometheus-data kan också direkt frågas efter namn.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Fråga efter konfiguration eller OLE-fel

Följande exempel fråga returnerar informations händelser från tabellen för att undersöka eventuella konfigurations-eller kassations fel `KubeMonAgentEvents` .

```
KubeMonAgentEvents | where Level != "Info" 
```

Resultatet visar resultat som liknar följande exempel:

![Logga frågeresultat av informations händelser från agenten](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Nästa steg

Behållar insikter innehåller inte en fördefinierad uppsättning aviseringar. Läs om hur du skapar rekommenderade aviseringar för hög processor-och minnes användning för att få stöd för DevOps eller operativa processer och procedurer i avsnittet [skapa prestanda aviseringar med behållar insikter](./container-insights-log-alerts.md) .