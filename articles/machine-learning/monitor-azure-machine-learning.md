---
title: Övervaknings Azure Machine Learning | Microsoft Docs
description: Lär dig hur du använder Azure Monitor för att visa, analysera och skapa aviseringar för mått från Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/01/2020
ms.openlocfilehash: 183d8b415ccb35d22dcc23ccf11d0707ad0778dd
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97963663"
---
# <a name="monitor-azure-machine-learning"></a>Övervaka Azure Machine Learning

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Azure Machine Learning och hur du analyserar och varnar dessa data med Azure Monitor.

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för __Administratörer__, som beskriver övervakningen av Azure Machine Learning tjänsten och tillhör ande Azure-tjänster. Om du är __data expert__ eller __utvecklare__ och vill övervaka information som är unik för din *modell utbildning*, se följande dokument:
>
> * [Starta, övervaka och avbryta inlärnings körningar](how-to-manage-runs.md)
> * [Loggmått för träningskörningar](how-to-track-experiments.md)
> * [Spåra experiment med MLflow](how-to-use-mlflow.md)
> * [Visualisera körningar med TensorBoard](how-to-monitor-tensorboard.md)
>
> Om du vill övervaka information som genererats av modeller som har distribuerats som webb tjänster eller IoT Edge moduler, se [samla in modell data](how-to-enable-data-collection.md) och [övervaka med Application Insights](how-to-enable-app-insights.md).

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Azure Machine Learning skapar övervaknings data med hjälp av [Azure Monitor](../azure-monitor/overview.md), som är en fullständig stack övervaknings tjänst i Azure. Azure Monitor innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser. Den kan också övervaka resurser i andra moln och lokalt.

Börja med artikeln [övervakning av Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md), vilket beskriver följande begrepp:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva specifika data som samlats in för Azure Machine Learning. Dessa avsnitt innehåller också exempel på hur du konfigurerar data insamling och analyserar data med Azure-verktyg.

> [!TIP]
> För att förstå kostnader som är kopplade till Azure Monitor, se [användning och beräknade kostnader](../azure-monitor/platform/usage-estimated-costs.md). För att förstå hur lång tid det tar för dina data att visas i Azure Monitor, se [logg data](../azure-monitor/platform/data-ingestion-time.md)Inhämtnings tid.

## <a name="monitoring-data-from-azure-machine-learning"></a>Övervaknings data från Azure Machine Learning

Azure Machine Learning samlar in samma typer av övervaknings data som andra Azure-resurser som beskrivs i [övervaknings data från Azure-resurser](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Se [Azure Machine Learning övervaknings data referens](monitor-resource-reference.md) för en detaljerad referens för de loggar och mått som skapats av Azure Machine Learning.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Samling och routning

Plattforms mått och aktivitets loggen samlas in och lagras automatiskt, men kan dirigeras till andra platser med hjälp av en diagnostisk inställning.  

Resurs loggar samlas inte in och lagras förrän du skapar en diagnostisk inställning och dirigerar dem till en eller flera platser.

Mer information om hur du skapar en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](../azure-monitor/platform/diagnostic-settings.md) . När du skapar en diagnostisk inställning anger du vilka kategorier av loggar som ska samlas in. Kategorierna för Azure Machine Learning anges i [Azure Machine Learning övervaknings data referens](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> Att aktivera de här inställningarna kräver ytterligare Azure-tjänster (lagrings konto, händelsehubben eller Log Analytics), vilket kan öka din kostnad. Om du vill beräkna en uppskattad kostnad går du till [pris Kalkylatorn för Azure](https://azure.microsoft.com/pricing/calculator).

Du kan konfigurera följande loggar för Azure Machine Learning:

| Kategori | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning beräknings kluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning beräknings kluster. |
| AmlComputeJobEvent | Händelser från jobb som körs på Azure Machine Learning Compute. |

> [!NOTE]
> När du aktiverar mått i en diagnostisk inställning ingår dimensions information för närvarande inte som en del av informationen som skickas till ett lagrings konto, en Event Hub-eller Log Analytics.

De mått och loggar som du kan samla in beskrivs i följande avsnitt.

## <a name="analyzing-metrics"></a>Analyserar mått

Du kan analysera mått för Azure Machine Learning, tillsammans med mått från andra Azure-tjänster, genom att öppna **mått** från **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) .

En lista över de plattforms mått som samlas in finns i [övervaknings Azure Machine Learning data referens mått](monitor-resource-reference.md#metrics).

Alla mått för Azure Machine Learning finns i namn området **Machine Learning service Workspace**.

![Metrics Explorer med Machine Learning tjänst arbets ytan har valts](./media/monitor-azure-machine-learning/metrics.png)

För referens kan du se en lista över [alla resurs mått som stöds i Azure Monitor](../azure-monitor/platform/metrics-supported.md).

> [!TIP]
> Azure Monitor Mät data är tillgängliga i 90 dagar. Men när du skapar diagram kan endast 30 dagar visualiseras. Om du till exempel vill visualisera en 90-dagars period måste du dela upp den i tre diagram om 30 dagar inom 90-dagars period.
### <a name="filtering-and-splitting"></a>Filtrering och delning

För mått som stöder dimensioner kan du använda filter med hjälp av ett dimensions värde. Filtrera exempelvis **aktiva kärnor** för ett **kluster namn** `cpu-cluster` . 

Du kan också dela upp ett mått per dimension för att visualisera hur olika segment i måttet jämförs med varandra. Du kan till exempel dela upp **steg typen pipeline** för att se ett antal typer av steg som används i pipelinen.

Mer information om filtrering och delning finns i [avancerade funktioner i Azure Monitor](../azure-monitor/platform/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analysera loggar

Om du använder Azure Monitor Log Analytics måste du skapa en diagnostisk konfiguration och aktivera __skicka information till Log Analytics__. Mer information finns i avsnittet [samling och routning](#collection-and-routing) .

Data i Azure Monitor loggar lagras i tabeller, där varje tabell har en egen uppsättning unika egenskaper. Azure Machine Learning lagrar data i följande tabeller:

| Tabell | Beskrivning |
|:---|:---|
| AmlComputeClusterEvent | Händelser från Azure Machine Learning beräknings kluster. |
| AmlComputeClusterNodeEvent | Händelser från noder i ett Azure Machine Learning beräknings kluster. |
| AmlComputeJobEvent | Händelser från jobb som körs på Azure Machine Learning Compute. |

> [!IMPORTANT]
> När du väljer **loggar** från Azure Machine Learning-menyn öppnas Log Analytics med fråge omfånget som är inställt på den aktuella arbets ytan. Det innebär att logg frågor bara innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra databaser eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

En detaljerad referens för loggar och mått finns i [Azure Machine Learning övervaknings data referens](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

> [!IMPORTANT]
> När du väljer **loggar** från [tjänst namn]-menyn öppnas Log Analytics med fråge omfånget som är inställt på den aktuella Azure Machine Learning-arbetsytan. Det innebär att logg frågor bara innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra arbets ytor eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

Följande är frågor som du kan använda för att övervaka dina Azure Machine Learning-resurser: 

+ Hämta misslyckade jobb de senaste fem dagarna:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta poster för ett angivet jobb namn:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Hämta kluster händelser under de senaste fem dagarna för kluster där storleken på den virtuella datorn är Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Hämta noder som tilldelats de senaste åtta dagarna:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Aviseringar

Du kan få åtkomst till aviseringar för Azure Machine Learning genom att öppna **aviseringar** från **Azure Monitor** -menyn. Se [skapa, Visa och hantera mått aviseringar med hjälp av Azure Monitor](../azure-monitor/platform/alerts-metric.md) för information om hur du skapar aviseringar.

I följande tabell visas vanliga och rekommenderade mått för varnings regler för Azure Machine Learning:

| Aviseringstyp | Villkor | Beskrivning |
|:---|:---|:---|
| Modelldistribution misslyckades | Sammansättnings typ: Total, operator: större än, tröskelvärde: 0 | När en eller flera modell distributioner har misslyckats |
| Kvot användning i procent | Sammansättnings typ: genomsnitt, operator: större än, tröskelvärde: 90| När kvot användnings procenten är större än 90% |
| Oanvändbara noder | Sammansättnings typ: Total, operator: större än, tröskelvärde: 0 | När det finns en eller flera oanvändbara noder |

## <a name="next-steps"></a>Nästa steg

- En referens för loggar och mått finns i [övervaknings Azure Machine Learning data referens](monitor-resource-reference.md).
- Information om hur du arbetar med kvoter som är relaterade till Azure Machine Learning finns i [Hantera och begära kvoter för Azure-resurser](how-to-manage-quotas.md).
- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
