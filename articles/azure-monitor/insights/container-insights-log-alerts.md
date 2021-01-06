---
title: Logg aviseringar från Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskriver hur du skapar anpassade logg aviseringar för minnes-och processor användning från Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 131f5ebc0f72afce381b4b82d6fe50a5d5e37123
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901512"
---
# <a name="how-to-create-log-alerts-from-azure-monitor-for-containers"></a>Så här skapar du logg aviseringar från Azure Monitor för behållare

Azure Monitor för behållare övervakar prestandan för behållar arbets belastningar som distribueras till hanterade eller självhanterade Kubernetes-kluster. För att varna om vad som är viktigt beskriver den här artikeln hur du skapar loggbaserade aviseringar i följande situationer med AKS-kluster:

- När processor-eller minnes användning på klusternoder överskrider ett tröskelvärde
- När processor-eller minnes användning på en behållare inom en styrenhet överskrider ett tröskelvärde jämfört med en gräns som har angetts för motsvarande resurs
- Antal noder för *Notrappstegs* status
- *Misslyckades*, *väntar*, *okänd*, *körs* eller *lyckades* Pod-fas antal
- När ledigt disk utrymme på klusternoder överskrider ett tröskelvärde

Om du vill varna för hög processor-eller minnes användning eller ont om ledigt disk utrymme på klusternoder, använder du frågorna som finns för att skapa en måtta avisering eller en mått mätnings avisering. Medan mått aviseringar har kortare svars tider än logg aviseringar innehåller logg aviseringar avancerade frågor och fler riktigt ambitiös. Logg aviserings frågor jämför ett datum/tid-värde för närvarande genom att använda operatorn *nu* och gå tillbaka en timme. (Azure Monitor för behållare lagrar alla datum i UTC-format (Coordinated Universal Time).)

Om du inte är bekant med Azure Monitor aviseringar, se [Översikt över aviseringar i Microsoft Azure](../platform/alerts-overview.md) innan du börjar. Om du vill veta mer om aviseringar som använder logg frågor, se [logg aviseringar i Azure Monitor](../platform/alerts-unified-log.md). Mer information om mått aviseringar finns [i mått varningar i Azure Monitor](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Sök frågor för resurs användnings logg

Frågorna i det här avsnittet stöder varje aviserings scenario. De används i steg 7 i avsnittet [skapa avisering](#create-an-alert-rule) i den här artikeln.

Följande fråga beräknar genomsnittlig processor användning som medelvärde för medlems nodernas CPU-användning varje minut.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

Följande fråga beräknar genomsnittlig minnes användning som medelvärde för medlems nodernas minnes användning varje minut.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Följande frågor använder plats hållarnas värden \<your-cluster-name> och \<your-controller-name> för att representera ditt kluster och din kontrollant. Ersätt dem med värden som är speciella för din miljö när du konfigurerar aviseringar.

Följande fråga beräknar den genomsnittliga CPU-användningen för alla behållare i en kontrollant som ett genomsnitt av processor användningen för varje behållar instans i en kontrollant varje minut. Måttet är en procent andel av gränsen som har kon figurer ATS för en behållare.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Följande fråga beräknar den genomsnittliga minnes användningen för alla behållare i en kontrollant som ett genomsnitt av minnes användningen för varje behållar instans i en kontrollant varje minut. Måttet är en procent andel av gränsen som har kon figurer ATS för en behållare.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

Följande fråga returnerar alla noder och antal som har statusen *klar* och *notrappsteg*.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
Följande fråga returnerar Pod fas antalet baserat på alla faser: *misslyckad*, *väntar*, *okänd*, *körs* eller *lyckades*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Om du vill varna om vissa Pod-faser, till exempel *väntande*, *misslyckad* eller *okänd*, ändrar du den sista raden i frågan. Till exempel för att varna vid *FailedCount* -användning: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

Följande fråga returnerar diskar för klusternoder som överskrider 90% ledigt utrymme. Om du vill hämta klustrets ID kör du först följande fråga och kopierar värdet från `ClusterId` egenskapen:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Skapa en varningsregel

Det här avsnittet beskriver hur du skapar en mått mätnings aviserings regel med hjälp av prestanda data från Azure Monitor för behållare. Du kan använda den här grundläggande processen med en mängd olika logg frågor för att få aviseringar om olika prestanda räknare. Använd en av logg Sök frågorna som tillhandahölls tidigare för att börja med. Information om hur du skapar med en ARM-mall finns i exempel på hur du skapar [logg aviseringar med hjälp av Azure Resource Template](../platform/alerts-log-create-templates.md)

>[!NOTE]
>Följande procedur för att skapa en aviserings regel för användning av container resurser kräver att du växlar till en ny logg aviserings-API enligt beskrivningen i [switch API-inställningar för logg aviseringar](../platform/alerts-log-api-switch.md).
>

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. I Azure Portal söker du efter och väljer **Log Analytics arbets ytor**.
3. I listan med Log Analytics arbets ytor väljer du den arbets yta som stöder Azure Monitor för behållare. 
4. I fönstret till vänster väljer du **loggar** för att öppna sidan Azure Monitor loggar. Du använder den här sidan för att skriva och köra Azures logg frågor.
5. På sidan **loggar** klistrar du in en av [frågorna](#resource-utilization-log-search-queries) som tillhandahölls tidigare i fältet **Sök fråga** och väljer sedan **Kör** för att validera resultatet. Om du inte utför det här steget är alternativet **+ ny avisering** inte tillgängligt för att välja.
6. Välj **+ ny avisering** om du vill skapa en logg avisering.
7. I avsnittet **villkor** väljer du **varje gång den anpassade logg sökningen är \<logic undefined>** fördefinierat anpassat logg villkor. Typ av Sök signal för **anpassad logg** väljs automatiskt eftersom vi skapar en varnings regel direkt från sidan Azure Monitor loggar.  
8. Klistra in en av [frågorna](#resource-utilization-log-search-queries) som tillhandahölls tidigare i fältet **Sök fråga** .
9. Konfigurera aviseringen på följande sätt:

    1. Välj **mått mått** i list rutan **baserad på** . En mått mätning skapar en avisering för varje objekt i frågan som har ett värde över det angivna tröskelvärdet.
    1. För **villkor** väljer du **större än** och anger **75** som ett ursprungligt bas linje **tröskelvärde** för aviseringar om processor och minnes användning. Ange **90** för varningen för lågt disk utrymme. Eller ange ett annat värde som uppfyller dina kriterier.
    1. I avsnittet **Utlös avisering baserad på** väljer du **efterföljande överträdelser**. I list rutan väljer du **större än** och anger **2**.
    1. Om du vill konfigurera en avisering för container-CPU eller minnes användning, under **mängd på**, väljer du **ContainerName**. Välj **ClusterId** för att konfigurera för klusternodens varning för låg disk.
    1. I avsnittet **utvärdera baserat på** anger du värdet för **period** till **60 minuter**. Regeln körs var 5: e minut och returnerar poster som har skapats under den senaste timmen från aktuell tid. Ange tids perioden till ett brett fönster konto för potentiell data svars tid. Det säkerställer också att frågan returnerar data för att undvika ett falskt negativt meddelande om att aviseringen aldrig utlöses.

10. Slutför varnings regeln genom att välja **klar** .
11. Ange ett namn i fältet **namn på aviserings regel** . Ange en **Beskrivning** som innehåller information om aviseringen. Och välj lämplig allvarlighets grad från de tillhandahållna alternativen.
12. Om du vill aktivera varnings regeln direkt accepterar du standardvärdet för **Aktivera regel vid skapande**.
13. Välj en befintlig **Åtgärds grupp** eller skapa en ny grupp. Det här steget säkerställer att samma åtgärder vidtas varje gång en avisering utlöses. Konfigurera baserat på hur IT-eller DevOps Operations-teamet hanterar incidenter.
14. Välj **skapa aviserings regel** för att slutföra aviserings regeln. Den börjar köras omedelbart.

## <a name="next-steps"></a>Nästa steg

- Visa [exempel på logg frågor](container-insights-log-search.md#search-logs-to-analyze-data) för att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisering eller analys av klustren.

- Mer information om Azure Monitor och hur du övervakar andra aspekter av ditt Kubernetes-kluster finns i [Visa Kubernetes kluster prestanda](container-insights-analyze.md) och [Visa Kubernetes kluster hälsa](./container-insights-overview.md).
