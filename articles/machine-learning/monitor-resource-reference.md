---
title: Övervaka Azure Machine Learning data referens | Microsoft Docs
description: Referens dokumentation för övervakning av Azure Machine Learning. Lär dig mer om data & resurser som samlas in och är tillgängliga i Azure Monitor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/02/2020
ms.openlocfilehash: f9f25ec2dd8fb8b859f7863c1e695e22cb9a9cec
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575074"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Övervaka data referens för Azure Machine Learning

Lär dig mer om de data och resurser som samlats in av Azure Monitor från arbets ytan Azure Machine Learning. Mer information om hur du samlar in och analyserar övervaknings data finns i [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Mått

I det här avsnittet visas alla automatiskt insamlade plattforms mått som samlas in för Azure Machine Learning. Resurs leverantören för dessa mått är [Microsoft. MachineLearningServices/arbets ytor](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modell**

| Metric | Enhet | Description |
| ----- | ----- | ----- |
| Modell distributionen misslyckades | Antal | Antalet modell distributioner som misslyckades. |
| Modell distribution har startat | Antal | Antalet modell distributioner som har startats. |
| Modell distributionen lyckades | Antal | Antalet modell distributioner som har slutförts. |
| Modell registreringen misslyckades | Antal | Antalet modell registreringar som misslyckades. |
| Modell registreringen har slutförts | Antal | Antalet modell registreringar som har slutförts. |

**Kvot**

Kvot information gäller endast för Azure Machine Learning beräkning.

| Metric | Enhet | Description |
| ----- | ----- | ----- |
| Aktiva kärnor | Antal | Antalet aktiva beräknings kärnor. |
| Aktiva noder | Antal | Antalet aktiva noder. |
| Inaktiva kärnor | Antal | Antalet inaktiva beräknings kärnor. |
| Inaktiva noder | Antal | Antalet inaktiva Compute-noder. |
| Lämnar kärnor | Antal | Antalet lämnar kärnor. |
| Lämnar noder | Antal | Antalet noder som lämnar. |
| Blockerade kärnor | Antal | Antalet blockerade kärnor. |
| Misslyckade noder | Antal | Antalet noder som har åsidosatts. |
| Kvot användning i procent | Procent | Procent andelen kvot som används. |
| Totalt antal kärnor | Antal | Totalt antal kärnor. |
| Totalt antal noder | Antal | Totalt antal noder. |
| Oanvändbara kärnor | Antal | Antalet oanvändbara kärnor. |
| Oanvändbara noder | Antal | Antalet oanvändbara noder. |

**Resurs**

| Metric | Enhet | Description |
| ----- | ----- | ----- |
| CpuUtilization | Procent | Hur mycket processor användnings procent som används för en viss nod under en körning/ett jobb. Det här måttet publiceras bara när ett jobb körs på en nod. Ett jobb kan använda en eller flera noder. Det här måttet publiceras per nod. |
| GpuUtilization | Procent | Hur mycket GPU-minne som utnyttjades för en viss nod under en körning/ett jobb. En nod kan ha en eller flera GPU: er. Det här måttet publiceras per GPU per nod. |

**Kör**

Information om utbildning körs.

| Metric | Enhet | Description |
| ----- | ----- | ----- |
| Slutförda körningar | Antal | Antalet slutförda körningar. |
| Misslyckade körningar | Antal | Antalet misslyckade körningar. |
| Startade körningar | Antal | Antalet startade körningar. |

## <a name="metric-dimensions"></a>Mått dimensioner

Mer information om vilka mått dimensioner som finns i [flerdimensionella mått](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning har följande dimensioner kopplade till sina mått.

| Dimension | Description |
| ---- | ---- |
| Klusternamn | Namnet på beräknings kluster resursen. Tillgängligt för alla kvot mått. |
| Namn på virtuell dator familj | Namnet på den VM-serie som används av klustret. Tillgängligt för kvot användning i procent. |
| VM-prioritet | Den virtuella datorns prioritet. Tillgängligt för kvot användning i procent.
| CreatedTime | Endast tillgängligt för CpuUtilization och GpuUtilization. |
| DeviceId | ID för enheten (GPU). Endast tillgängligt för GpuUtilization. |
| NodeId | ID för noden som skapades där jobbet körs. Endast tillgängligt för CpuUtilization och GpuUtilization. |
| RunId | ID för körning/jobb. Endast tillgängligt för CpuUtilization och GpuUtilization. |
| ComputeType | Den beräknings typ som används för körningen. Endast tillgängligt för slutförda körningar, misslyckade körningar och startade körningar. |
| PipelineStepType | Den typ av [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?preserve-view=true&view=azure-ml-py) som används i körningen. Endast tillgängligt för slutförda körningar, misslyckade körningar och startade körningar. |
| PublishedPipelineId | ID för den publicerade pipelinen som används i körningen. Endast tillgängligt för slutförda körningar, misslyckade körningar och startade körningar. |
| RunType | Typ av körning. Endast tillgängligt för slutförda körningar, misslyckade körningar och startade körningar. |

Giltiga värden för dimensionen RunType är:

| Värde | Beskrivning |
| ----- | ----- |
| Experiment | Icke-pipeline körs. |
| PipelineRun | En pipeline-körning som är överordnad en StepRun. |
| StepRun | En körning för en pipeline-åtgärd. |
| ReusedStepRun | En körning för ett pipeline-steg som återanvänder en tidigare körning. |

## <a name="activity-log"></a>Aktivitetslogg

I följande tabell visas de åtgärder som är relaterade till Azure Machine Learning som kan skapas i aktivitets loggen.

| Åtgärd | Description |
|:---|:---|
| Skapar eller uppdaterar en Machine Learning arbets yta | En arbets yta har skapats eller uppdaterats |
| CheckComputeNameAvailability | Kontrol lera om ett beräknings namn redan används |
| Skapar eller uppdaterar beräknings resurserna | En beräknings resurs har skapats eller uppdaterats |
| Tar bort beräknings resurserna | En beräknings resurs har tagits bort |
| Visa en lista över hemligheter | På åtgärd som har listats hemligheter för en Machine Learning arbets yta |

## <a name="resource-logs"></a>Resursloggar

I det här avsnittet visas de typer av resurs loggar som du kan samla in för Azure Machine Learning arbets ytan.

Resource Provider och typ: [Microsoft. MachineLearningServices/Workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Kategori | Visningsnamn |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Scheman

Följande scheman används i Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>AmlComputeJobEvents-tabell

| Egenskap | Beskrivning |
|:--- |:---|
| TimeGenerated | Tid när logg posten skapades |
| OperationName | Namnet på åtgärden som är associerad med logg händelsen |
| Kategori | Namnet på logg händelsen, AmlComputeClusterNodeEvent |
| JobId | ID för jobbet som skickats |
| ExperimentId | ID för experimentet |
| ExperimentName | Experimentets namn |
| CustomerSubscriptionId | SubscriptionId där experiment och jobb skickas in |
| WorkspaceName | Namnet på Machine Learning-arbetsytan |
| ClusterName | Namn på klustret |
| ProvisioningState | Tillstånd för jobb sändningen |
| ResourceGroupName | Namnet på resurs gruppen |
| JobName | Jobbets namn |
| ClusterId | ID för klustret |
| Typ | Typ av jobb händelse. Till exempel JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | Status för jobbet (körningen). Till exempel i kö, körs, lyckades, misslyckades |
| ErrorDetails | Information om jobbfel |
| CreationApiVersion | API-version som används för att skapa jobbet |
| ClusterResourceGroupName | Resurs grupps namn för klustret |
| TFWorkerCount | Antal TF-arbetare |
| TFParameterServerCount | Antal TF-parameter Server |
| ToolType | Typ av verktyg som används |
| RunInContainer | Flagga som beskriver om jobb ska köras inuti en behållare |
| JobErrorMessage | detaljerat meddelande vid jobbfel |
| NodeId | ID för noden som skapades där jobbet körs |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents-tabell

| Egenskap | Beskrivning |
|:--- |:--- |
| TimeGenerated | Tid när logg posten skapades |
| OperationName | Namnet på åtgärden som är associerad med logg händelsen |
| Kategori | Namnet på logg händelsen, AmlComputeClusterNodeEvent |
| ProvisioningState | Etablerings status för klustret |
| ClusterName | Namn på klustret |
| ClusterType | Typ av kluster |
| CreatedBy | Användare som skapade klustret |
| CoreCount | Antal kärnor i klustret |
| VmSize | Klustrets virtuella dator storlek |
| VmPriority | Prioritet för de noder som skapats i ett dedikerat kluster/LowPriority |
| ScalingType | Typ av kluster skalning manuellt/automatiskt |
| InitialNodeCount | Antal inledande noder i klustret |
| MinimumNodeCount | Lägsta antal noder i klustret |
| MaximumNodeCount | Maximalt antal noder för klustret |
| NodeDeallocationOption | Hur noden ska frigöras |
| Publisher | Utgivare av kluster typen |
| Erbjudande | Erbjudande som klustret skapas med |
| Sku | SKU för den nod/VM som skapats i klustret |
| Version | Versionen av avbildningen som används när nod/VM skapas |
| SubnetId | SubnetId för klustret |
| AllocationState | Kluster tilldelnings status |
| CurrentNodeCount | Aktuellt antal noder för klustret |
| TargetNodeCount | Antalet mål noder i klustret vid skalning upp/ned |
| Typ | Typ av händelse när klustret skapas. |
| NodeIdleTimeSecondsBeforeScaleDown | Inaktiv tid i sekunder innan klustret skalas ned |
| PreemptedNodeCount | Antal misslyckade noder i klustret |
| IsResizeGrow | Flagga som indikerar att klustret skalas upp |
| VmFamilyName | Namn på VM-serien för de noder som kan skapas i klustret |
| LeavingNodeCount | Lämnar klustrets antal noder |
| UnusableNodeCount | Klustret har oanvändbart antal noder |
| IdleNodeCount | Antal inaktiva noder i klustret |
| RunningNodeCount | Antal noder i klustret som körs |
| PreparingNodeCount | Antalet noder i klustret förbereds |
| QuotaAllocated | Allokerad kvot till klustret |
| QuotaUtilized | Använd kvot för klustret |
| AllocationStateTransitionTime | Över gångs tid från ett tillstånd till ett annat |
| ClusterErrorCodes | Felkod som togs emot vid skapande eller skalning av kluster |
| CreationApiVersion | API-version som används när klustret skapas |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents-tabell

| Egenskap | Beskrivning |
|:--- |:--- |
| TimeGenerated | Tid när logg posten skapades |
| OperationName | Namnet på åtgärden som är associerad med logg händelsen |
| Kategori | Namnet på logg händelsen, AmlComputeClusterNodeEvent |
| ClusterName | Namn på klustret |
| NodeId | ID för klusternoden som skapades |
| VmSize | Nodens VM-storlek |
| VmFamilyName | VM-serien som noden tillhör |
| VmPriority | Prioritet för noden som skapade dedikerad/LowPriority |
| Publisher | Utgivare av VM-avbildningen. Till exempel Microsoft-dsvm |
| Erbjudande | Erbjudande som är associerat med skapande av virtuell dator |
| Sku | SKU för den nod/VM som skapats |
| Version | Versionen av avbildningen som används när nod/VM skapas |
| ClusterCreationTime | Tid när klustret skapades |
| ResizeStartTime | Tid när kluster skalar upp/ned startas |
| ResizeEndTime | Tid när kluster skalar upp/ned avslutas |
| NodeAllocationTime | Tid när noden allokerades |
| NodeBootTime | Tid när noden startades |
| StartTaskStartTime | Tid när uppgiften tilldelades till en nod och startades |
| StartTaskEndTime | Tid när uppgiften som tilldelats till en nod slutade |
| TotalE2ETimeInSeconds | Noden total tid var aktiv |


## <a name="see-also"></a>Se även

- En beskrivning av övervaknings Azure Machine Learning finns i [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md) .
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) .
