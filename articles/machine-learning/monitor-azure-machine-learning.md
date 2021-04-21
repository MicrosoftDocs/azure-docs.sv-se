---
title: Övervakning Azure Machine Learning | Microsoft Docs
description: Lär dig hur du använder Azure Monitor för att visa, analysera och skapa aviseringar för mått från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: e5fd0fdd5a6f9a4a7537a844b096efdfef253638
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816862"
---
# <a name="monitor-azure-machine-learning"></a>Övervaka Azure Machine Learning

När du har kritiska program och affärsprocesser som förlitar sig på Azure-resurser vill du övervaka resursernas tillgänglighet, prestanda och drift. Den här artikeln beskriver övervakningsdata som genereras av Azure Machine Learning och hur du analyserar och varnar om dessa data med hjälp Azure Monitor.

> [!TIP]
> Informationen i det här dokumentet är främst avsedd __för__ administratörer eftersom den beskriver övervakning av Azure Machine Learning Service och associerade Azure-tjänster. Om du är __dataexpert eller__ __utvecklare__ och vill övervaka information som är specifik för dina *modellträningskörningar* kan du se följande dokument:
>
> * [Starta, övervaka och avbryta träningskörningar](how-to-track-monitor-analyze-runs.md)
> * [Loggmått för träningskörningar](how-to-log-view-metrics.md)
> * [Spåra experiment med MLflow](how-to-use-mlflow.md)
> * [Visualisera körningar med TensorBoard](how-to-monitor-tensorboard.md)
>
> Om du vill övervaka information som genereras av modeller som distribuerats som webbtjänster eller IoT Edge-moduler kan du se Samla in [modelldata](how-to-enable-data-collection.md) [och Övervaka med Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Azure Machine Learning skapar övervakningsdata med [hjälp Azure Monitor](../azure-monitor/overview.md), vilket är en fullständig stackövervakningstjänst i Azure. Azure Monitor innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser. Den kan också övervaka resurser i andra moln och lokalt.

Börja med artikeln [Övervaka Azure-resurser Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), som beskriver följande begrepp:

- Vad är Azure Monitor?
- Kostnader som är kopplade till övervakning
- Övervakningsdata som samlas in i Azure
- Konfigurera datainsamling
- Standardverktyg i Azure för analys och avisering om övervakningsdata

Följande avsnitt bygger på den här artikeln genom att beskriva de specifika data som samlas in för Azure Machine Learning. De här avsnitten innehåller också exempel på hur du konfigurerar datainsamling och analyserar dessa data med Azure-verktyg.

> [!TIP]
> Information om kostnader för Azure Monitor finns i [Användning och uppskattade kostnader.](../azure-monitor//usage-estimated-costs.md) Information om hur lång tid det tar för dina data att Azure Monitor finns i [Inmatningstid för loggdata.](../azure-monitor/logs/data-ingestion-time.md)

## <a name="monitoring-data-from-azure-machine-learning"></a>Övervaka data från Azure Machine Learning

Azure Machine Learning samlar in samma typer av övervakningsdata som andra Azure-resurser som beskrivs i [Övervaka data från Azure-resurser.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

Se [Azure Machine Learning referens för övervakningsdata](monitor-resource-reference.md) för en detaljerad referens till loggar och mått som skapats av Azure Machine Learning.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Insamling och routning

Plattformsmått och aktivitetsloggen samlas in och lagras automatiskt, men kan dirigeras till andra platser med hjälp av en diagnostikinställning.  

Resursloggar samlas inte in och lagras förrän du skapar en diagnostikinställning och dirigerar dem till en eller flera platser.

Se Skapa diagnostikinställning för att samla [in plattformsloggar](../azure-monitor/essentials/diagnostic-settings.md) och mått i Azure för detaljerad process för att skapa en diagnostikinställning med hjälp av Azure Portal, CLI eller PowerShell. När du skapar en diagnostikinställning anger du vilka kategorier av loggar som ska samlas in. Kategorierna för Azure Machine Learning listas i [referensen för Azure Machine Learning övervakningsdata.](monitor-resource-reference.md#resource-logs)

> [!IMPORTANT]
> För att aktivera de här inställningarna krävs ytterligare Azure-tjänster (lagringskonto, händelsehubb eller Log Analytics), vilket kan öka dina kostnader. Om du vill beräkna en beräknad kostnad kan du gå till [priskalkylatorn för Azure.](https://azure.microsoft.com/pricing/calculator)

Du kan konfigurera följande loggar för Azure Machine Learning:

| Kategori | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning beräkningskluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning beräkningskluster. |
| AmlComputeJobEvent | Händelser från jobb som körs Azure Machine Learning beräkning. |

> [!NOTE]
> När du aktiverar mått i en diagnostikinställning ingår för närvarande inte dimensionsinformation som en del av informationen som skickas till ett lagringskonto, en händelsehubb eller en logganalys.

De mått och loggar som du kan samla in beskrivs i följande avsnitt.

## <a name="analyzing-metrics"></a>Analysera mått

Du kan analysera mått för Azure Machine Learning, tillsammans med mått från  andra Azure-tjänster, genom att öppna **Mått Azure Monitor** menyn. Se [Komma igång med Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) mer information om hur du använder det här verktyget.

En lista över de plattformsmått som samlas in finns i [Monitoring Azure Machine Learning data reference metrics](monitor-resource-reference.md#metrics)(Övervakningsdatareferensmått).

Alla mått för Azure Machine Learning finns i namnområdet för **Machine Learning Service Workspace**.

![Metrics Explorer med Machine Learning tjänstarbetsyta vald](./media/monitor-azure-machine-learning/metrics.png)

Som referens kan du se en lista över [alla resursmått som stöds i Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

> [!TIP]
> Azure Monitor måttdata är tillgängliga i 90 dagar. Men när du skapar diagram kan bara 30 dagar visualiseras. Om du till exempel vill visualisera en 90-dagarsperiod måste du dela upp den i tre diagram med 30 dagar inom 90-dagarsperioden.
### <a name="filtering-and-splitting"></a>Filtrering och delning

För mått som stöder dimensioner kan du använda filter med ett dimensionsvärde. Du kan till exempel **filtrera Active Cores** efter **klusternamnet** `cpu-cluster` . 

Du kan också dela upp ett mått efter dimension för att visualisera hur olika måttsegment är i jämförelse med varandra. Du kan till exempel dela upp **Typen av pipelinesteg** för att se antalet typer av steg som används i pipelinen.

Mer information om filtrering och delning finns i [Avancerade funktioner i Azure Monitor](../azure-monitor/essentials/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analysera loggar

Om Azure Monitor Log Analytics måste du skapa en diagnostikkonfiguration och aktivera __Skicka information till Log Analytics.__ Mer information finns i avsnittet [Samling och routning.](#collection-and-routing)

Data i Azure Monitor-loggar lagras i tabeller, där varje tabell har en egen uppsättning unika egenskaper. Azure Machine Learning lagrar data i följande tabeller:

| Tabell | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning beräkningskluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning beräkningskluster. |
| AmlComputeJobEvent | Händelser från jobb som körs Azure Machine Learning beräkning. |

> [!IMPORTANT]
> När du väljer **Loggar** från Azure Machine Learning öppnas Log Analytics med frågeomfånget inställt på den aktuella arbetsytan. Det innebär att loggfrågor endast innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra  databaser eller data från andra Azure-tjänster väljer du Loggar **på Azure Monitor** menyn. Mer [information finns i Log query scope and time range in Azure Monitor Log Analytics (Log query scope and time range i log query scope](../azure-monitor/logs/scope.md) and time range in Azure Monitor Log Analytics).

En detaljerad referens till loggarna och måtten finns i Azure Machine Learning [referens för övervakningsdata.](monitor-resource-reference.md)

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

> [!IMPORTANT]
> När du väljer **Loggar** på menyn [service-name] öppnas Log Analytics med frågeomfånget inställt på den Azure Machine Learning arbetsytan. Det innebär att loggfrågor endast innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra  arbetsytor eller data från andra Azure-tjänster väljer du Loggar **på Azure Monitor menyn.** Mer [information finns i Log query scope and time range in Azure Monitor Log Analytics (Log query scope and time range i log query scope](../azure-monitor/logs/scope.md) and time range in Azure Monitor Log Analytics).

Följande är frågor som du kan använda för att övervaka dina Azure Machine Learning resurser: 

+ Hämta misslyckade jobb under de senaste fem dagarna:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta poster för ett specifikt jobbnamn:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta klusterhändelser under de senaste fem dagarna för kluster där VM-storleken är Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Hämta noder som har allokerats under de senaste åtta dagarna:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Aviseringar

Du kan komma åt aviseringar för Azure Machine Learning genom **att öppna** Aviseringar från **Azure Monitor** menyn. Se [Skapa, visa och hantera måttaviseringar med hjälp Azure Monitor](../azure-monitor/alerts/alerts-metric.md) information om hur du skapar aviseringar.

I följande tabell visas vanliga och rekommenderade måttaviseringsregler för Azure Machine Learning:

| Aviseringstyp | Villkor | Description |
|:---|:---|:---|
| Det gick inte att distribuera modellen | Sammansättningstyp: Total, Operator: Större än, Tröskelvärde: 0 | När en eller flera modelldistributioner har misslyckats |
| Kvotanvändning i procent | Sammansättningstyp: Genomsnitt, Operator: Större än, Tröskelvärde: 90| När kvotens utnyttjandeprocent är större än 90 % |
| Oanvändbara noder | Sammansättningstyp: Total, Operator: Större än, Tröskelvärde: 0 | När det finns en eller flera oanvändbara noder |

## <a name="next-steps"></a>Nästa steg

- En referens till loggarna och måtten finns i [Monitoring Azure Machine Learning data reference](monitor-resource-reference.md).
- Information om hur du arbetar med kvoter relaterade Azure Machine Learning finns i [Hantera och begära kvoter för Azure-resurser.](how-to-manage-quotas.md)
- Mer information om övervakning av Azure-resurser finns [i Övervaka Azure-resurser med Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
