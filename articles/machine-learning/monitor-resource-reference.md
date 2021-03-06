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
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031077"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Övervaka data referens för Azure Machine Learning

Lär dig mer om de data och resurser som samlats in av Azure Monitor från arbets ytan Azure Machine Learning. Mer information om hur du samlar in och analyserar övervaknings data finns i [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Mått

I det här avsnittet visas alla automatiskt insamlade plattforms mått som samlas in för Azure Machine Learning. Resurs leverantören för dessa mått är [Microsoft. MachineLearningServices/arbets ytor](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modell**

| Metric | Enhet | Beskrivning |
|--|--|--|
| Modell registreringen har slutförts | Antal | Antal modell registreringar som har slutförts på den här arbets ytan |
| Modell registreringen misslyckades | Antal | Antal modell registreringar som misslyckades på den här arbets ytan |
| Modelldistribution Startad | Antal | Antal modell distributioner som startats på den här arbets ytan |
| Modelldistribution lyckades | Antal | Antal modell distributioner som har slutförts på den här arbets ytan |
| Modelldistribution misslyckades | Antal | Antal modell distributioner som misslyckades på den här arbets ytan |

**Kvot**

Kvot information gäller endast för Azure Machine Learning beräkning.

| Metric | Enhet | Beskrivning |
|--|--|--|
| Totalt antal noder | Antal | Totalt antal noder. Den här summan innehåller några aktiva noder, inaktiva noder, oanvändbara noder, vilande noder, lämnar noder |
| Aktiva noder | Antal | Antal aktiva noder. De noder som aktivt kör ett jobb. |
| Inaktiva noder | Antal | Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb, men som kan ta emot nya jobb om de är tillgängliga. |
| Oanvändbara noder | Antal | Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett problem som inte kan matchas. De här noderna återanvänds av Azure. |
| Misslyckade noder | Antal | Antal noder som har åsidosatts. De här noderna är de noder med låg prioritet som tas bort från den tillgängliga Node-poolen. |
| Lämnar noder | Antal | Antal noder som lämnar. Att lämna noder är de noder som precis har avslutat bearbetningen av ett jobb och kommer att gå in i inaktivt läge. |
| Totalt antal kärnor | Antal | Antal total kärnor |
| Aktiva kärnor | Antal | Antal aktiva kärnor |
| Inaktiva kärnor | Antal | Antal inaktiva kärnor |
| Oanvändbara kärnor | Antal | Antal oanvändbara kärnor |
| Blockerade kärnor | Antal | Antal blockerade kärnor |
| Lämnar kärnor | Antal | Antal lämnar kärnor |
| Kvot användning i procent | Antal | Procent av kvoten som används |

**Resurs**

| Metric| Enhet | Beskrivning |
|--|--|--|
| CpuUtilization | Antal | Procent andel användning på en processor nod. Användningen rapporteras med en minuters intervall. |
| GpuUtilization | Antal | Procent andel användning på en GPU-nod. Användningen rapporteras med en minuters intervall. |
| GpuMemoryUtilization | Antal | Procent andel minnes användning på en GPU-nod. Användningen rapporteras med en minuters intervall. |
| GpuEnergyJoules | Antal | Intervall energi i joules på en GPU-nod. Energi rapporteras med en minuters intervall. |

**Kör**

Information om utbildningar körs för arbets ytan.

| Metric | Enhet | Beskrivning |
|--|--|--|
| Avbrutna körningar | Antal | Antalet körningar som har avbrutits för den här arbets ytan. Antalet uppdateras när en körning har avbrutits. |
| Avbryt begärda körningar | Antal | Antal körningar där Cancel begärdes för den här arbets ytan. Antalet uppdateras när en avbrottsbegäran tas emot för en körning. |
| Slutförda körningar | Antal | Antalet körningar har slutförts för den här arbets ytan. Antalet uppdateras när en körning har slutförts och utdata har samlats in. |
| Misslyckade körningar | Antal | Antalet körningar som misslyckades för den här arbets ytan. Antalet uppdateras när en körning Miss lyckas. |
| Slutför körningar | Antal | Antal körningar som har angetts i slutfört tillstånd för den här arbets ytan. Antalet uppdateras när en körning har slutförts men insamlingen fortfarande pågår. | 
| Svarar inte körningar | Antal | Antal körningar som inte svarar på den här arbets ytan. Antalet uppdateras när en körning inte övergår i tillstånd. |
| Inte startade körningar | Antal | Antal körningar i inte start läge för den här arbets ytan. Antalet uppdateras när en begäran tas emot för att skapa en körning men körnings information ännu inte har fyllts i. |
| Förbereder körningar | Antal | Antal körningar som förbereds för den här arbets ytan. Antalet uppdateras när en körning går in i förberedelse tillstånd medan körnings miljön förbereds. |
| Etablerings körningar | Antal | Antal körningar som håller på att etablering för den här arbets ytan. Antalet uppdateras när en körning väntar på skapande eller etablering av beräknings mål. |
| Köade körningar | Antal | Antal körningar som har placerats i kö för den här arbets ytan. Antalet uppdateras när en körning placeras i kö i beräknings mål. Kan inträffa vid väntan på att nödvändiga datornoder ska vara klara. |
| Startade körningar | Antal | Antal körningar som körs för den här arbets ytan. Antalet uppdateras när körningen börjar köras på nödvändiga resurser. |
| Startar körningar | Antal | Antal körningar som har startats för den här arbets ytan. Antalet uppdateras när begäran om att skapa körnings-och körnings information, till exempel körnings-ID, har fyllts i |
| Fel | Antal | Antal körnings fel i den här arbets ytan. Antalet uppdateras när körningen påträffar ett fel. |
| Varningar | Antal | Antal körnings varningar i den här arbets ytan. Antalet uppdateras när en körning påträffar en varning. |

## <a name="metric-dimensions"></a>Mått dimensioner

Mer information om vilka mått dimensioner som finns i [flerdimensionella mått](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning har följande dimensioner kopplade till sina mått.

| Dimension | Beskrivning |
| ---- | ---- |
| Klusternamn | Namnet på beräknings kluster resursen. Tillgängligt för alla kvot mått. |
| Namn på virtuell dator familj | Namnet på den VM-serie som används av klustret. Tillgängligt för kvot användning i procent. |
| VM-prioritet | Den virtuella datorns prioritet. Tillgängligt för kvot användning i procent.
| CreatedTime | Endast tillgängligt för CpuUtilization och GpuUtilization. |
| DeviceId | ID för enheten (GPU). Endast tillgängligt för GpuUtilization. |
| NodeId | ID för noden som skapades där jobbet körs. Endast tillgängligt för CpuUtilization och GpuUtilization. |
| RunId | ID för körning/jobb. Endast tillgängligt för CpuUtilization och GpuUtilization. |
| ComputeType | Den beräknings typ som används för körningen. Endast tillgängligt för slutförda körningar, misslyckade körningar och startade körningar. |
| PipelineStepType | Den typ av [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) som används i körningen. Endast tillgängligt för slutförda körningar, misslyckade körningar och startade körningar. |
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

| Åtgärd | Beskrivning |
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
