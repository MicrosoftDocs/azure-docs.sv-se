---
title: Logga & visa mått och loggfiler
titleSuffix: Azure Machine Learning
description: Aktivera loggning på dina ML-träningskörningar för att övervaka körningsmått i realtid och diagnostisera fel och varningar.
services: machine-learning
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bc7564a11f570833bd6cb10c14267e08c3b5753
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820556"
---
# <a name="log--view-metrics-and-log-files"></a>Logga & visa mått och loggfiler

Logga realtidsinformation med hjälp av både Python-standardloggningspaketet och Azure Machine Learning Python SDK-specifika funktioner. Du kan logga lokalt och skicka loggar till din arbetsyta i portalen.

Med loggarna kan du diagnostisera fel och varningar, eller spåra prestandamått som parametrar och modellprestanda. I den här artikeln får du lära dig hur du aktiverar loggning i följande scenarier:

> [!div class="checklist"]
> * Loggkörningsmått
> * Interaktiva träningssessioner
> * Skicka träningsjobb med ScriptRunConfig
> * Inbyggda `logging`-inställningar i Python
> * Loggning från fler källor


> [!TIP]
> Den här artikeln visar hur du övervakar modellträningsprocessen. Om du vill veta mer om att övervaka resursanvändning och händelser från Azure Machine Learning, till exempel kvoter, slutförda träningskörningar eller slutförda modelldistributioner, kan du läsa [Övervakning i Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Datatyper

Du kan logga flera datatyper, inklusive skalära värden, listor, tabeller, bilder, kataloger med mera. Mer information och Python-kodexempel för olika datatyper finns på [referenssidan Körningsklass](/python/api/azureml-core/azureml.core.run%28class%29).

### <a name="logging-run-metrics"></a>Loggningskörningsmått 

Använd följande metoder i loggnings-API:erna för att påverka måttvisualiseringarna. Observera [tjänstgränserna för](./resource-limits-quotas-capacity.md#metrics) dessa loggade mått. 

|Loggat värde|Exempelkod| Format i portalen|
|----|----|----|
|Logga en matris med numeriska värden| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|linjediagram med en variabel|
|Logga ett enda numeriskt värde med samma måttnamn upprepade gånger (som inifrån en for-loop)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Linjediagram med en variabel|
|Logga en rad med 2 numeriska kolumner upprepade gånger|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Linjediagram med två variabler|
|Loggtabell med 2 numeriska kolumner|`run.log_table(name='Sine Wave', value=sines)`|Linjediagram med två variabler|
|Loggavbildning|`run.log_image(name='food', path='./breadpudding.jpg', plot=None, description='desert')`|Använd den här metoden för att logga en bildfil eller ett matplotlib-diagram i körningen. Dessa avbildningar är synliga och jämförbara i körningsposten|

### <a name="logging-with-mlflow"></a>Logga med MLflow
Använd MLFlowLogger för att logga mått.

```python
from azureml.core import Run
# connect to the workspace from within your running code
run = Run.get_context()
ws = run.experiment.workspace

# workspace has associated ml-flow-tracking-uri
mlflow_url = ws.get_mlflow_tracking_uri()

#Example: PyTorch Lightning
from pytorch_lightning.loggers import MLFlowLogger

mlf_logger = MLFlowLogger(experiment_name=run.experiment.name, tracking_uri=mlflow_url)
mlf_logger._run_id = run.id
```

## <a name="view-run-metrics"></a>Visa körningsmått

## <a name="via-the-sdk"></a>Via the SDK
Du kan visa måtten för en tränad modell med hjälp av ```run.get_metrics()``` . Se exemplet nedan. 

```python
from azureml.core import Run
run = Run.get_context()
run.log('metric-name', metric_value)

metrics = run.get_metrics()
# metrics is of type Dict[str, List[float]] mapping mertic names
# to a list of the values for that metric in the given run.

metrics.get('metric-name')
# list of metrics in the order they were recorded
```

<a name="view-the-experiment-in-the-web-portal"></a>

## <a name="view-run-metrics-in-aml-studio-ui"></a>Visa körningsmått i AML Studio-användargränssnittet

Du kan bläddra bland slutförda körningsposter, inklusive loggade mått, i [Azure Machine Learning-studio](https://ml.azure.com).

Gå till **fliken** Experiment. Om du vill visa alla körningar i arbetsytan mellan experiment väljer du **fliken Alla körningar.** Du kan öka detaljgranska körningar för specifika experiment genom att använda experimentfiltret på den översta menyraden.

För den enskilda experimentvyn väljer du **fliken Alla** experiment. På instrumentpanelen för experimentkörningen kan du se spårade mått och loggar för varje körning. 

Du kan också redigera tabellen med körningslistan för att välja flera körningar och visa antingen det sista, lägsta eller högsta loggade värdet för dina körningar. Anpassa dina diagram för att jämföra loggade måttvärden och aggregeringar över flera körningar. Du kan rita flera mått på y-axeln i diagrammet och anpassa x-axeln för att rita dina loggade mått. 


### <a name="view-and-download-log-files-for-a-run"></a>Visa och ladda ned loggfiler för en körning 

Loggfiler är en viktig resurs för felsökning av Azure ML-arbetsbelastningar. När du har skickat ett träningsjobb går du nedåt till en specifik körning för att visa dess loggar och utdata:  

1. Gå till **fliken** Experiment.
1. Välj runID för en specifik körning.
1. Välj **Utdata och** loggar överst på sidan.
2. Välj **Ladda ned alla** för att ladda ned alla loggar till en zip-mapp.
3. Du kan också ladda ned enskilda loggfiler genom att välja loggfilen och välja **Ladda ned**

:::image type="content" source="media/how-to-log-view-metrics/download-logs.png" alt-text="Skärmbild av avsnittet Utdata och loggar för en körning.":::

Tabellerna nedan visar innehållet i loggfilerna i mapparna som visas i det här avsnittet.

> [!NOTE]
> Du ser inte nödvändigtvis alla filer för varje körning. Till exempel visas 20_image_build_log*.txt endast när en ny avbildning har skapats (t.ex. när du ändrar din miljö).

#### <a name="azureml-logs-folder"></a>`azureml-logs` Mappen

|Fil  |Description  |
|---------|---------|
|20_image_build_log.txt     | Docker-avbildningsbygglogg för träningsmiljön, valfritt, en per körning. Gäller endast när du uppdaterar din miljö. Annars återanvänder AML den cachelagrade avbildningen. Om det lyckas innehåller information om avbildningsregistret för motsvarande avbildning.         |
|55_azureml-execution-<node_id>.txt     | stdout/stderr-logg för värdverktyget, en per nod. Hämtar avbildningen till beräkningsmålet. Observera att den här loggen bara visas när du har skyddat beräkningsresurser.         |
|65_job_prep #<node_id>.txt     |   stdout/stderr-logg för skript för jobbförberedelse, en per nod. Ladda ned koden till beräkningsmålet och datalager (om så krävs).       |
|70_driver_log(_x).txt      |  stdout/stderr-logg från AML-kontrollskript och kundträningsskript, ett per process. **Standardutdata från skriptet. Den här filen är den plats där kodens loggar (till exempel utskriftsuttryck) visas.** I de flesta fall övervakar du loggarna här.       |
|70_mpi_log.txt     |   MPI-ramverkslogg, valfritt, en per körning. Endast för MPI-körning.   |
|75_job_post#<node_id>.txt     |  stdout/stderr-logg för jobbutgåpsskript, en per nod. Skicka loggar och släpp beräkningsresurserna tillbaka till Azure.        |
|process_info.jspå      |   visa vilken process som körs på vilken nod.  |
|process_status.jspå      | visa processstatus, till exempel om en process inte har startats, körs eller slutförts.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` Mappen

|Fil  |Description  |
|---------|---------|
|110_azureml.log      |         |
|job_prep_azureml.log     |   systemlogg för jobbförberedelse        |
|job_release_azureml.log     | systemlogg för jobbutgår        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` Mappen

När sidovagnen är aktiverad körs skript för jobbförberedelser och jobbutgåningar i sidovagnscontainern.  Det finns en mapp för varje nod. 

|Fil  |Description  |
|---------|---------|
|start_cms.txt     |  Logg över processen som startar när Sidovagnscontainer startar       |
|prep_cmd.txt      |   Logg för ContextManagers som anges `job_prep.py` när körs (en del av det här innehållet strömmas till `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Loggen för ComtextManagers avslutas `job_release.py` när körs        |

#### <a name="other-folders"></a>Andra mappar

För jobbträning i kluster med flera beräkningar finns det loggar för varje nod-IP. Strukturen för varje nod är samma som jobb med en nod. Det finns ytterligare en loggmapp för övergripande körning, stderr och stdout-loggar.

Azure Machine Learning loggar information från olika källor under träningen, till exempel AutoML eller Docker-containern som kör träningsjobbet. Många av dessa loggar är inte dokumenterade. Om du stöter på problem och kontaktar Microsofts support kan de använda loggarna under felsökningen.


## <a name="interactive-logging-session"></a>Interaktiv loggningssession

Interaktiva loggningssessioner används vanligtvis i miljöer med notebook-filer. Metoden [Experiment.start_logging()](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) startar en interaktiv loggningssession. Alla mått som loggas under sessionen läggs till i körningsposten i experimentet. Metoden [run.complete()](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) avslutar sessionerna och markerar körningen som slutförd.

## <a name="scriptrun-logs"></a>ScriptRun-loggar

I det här avsnittet lär du dig att lägga till loggningskod i körningar som skapas vid konfiguration med ScriptRunConfig. Du kan använda [**ScriptRunConfig**](/python/api/azureml-core/azureml.core.scriptrunconfig)-klassen till att kapsla in skript och miljöer för upprepningsbara körningar. Du kan också använda det här alternativet till att visa widgeten för Jupyter Notebooks vid övervakning.

I det här exemplet utförs en parameterrensning av alfavärden och resultaten samlas in med hjälp av metoden [run.log()](/python/api/azureml-core/azureml.core.run%28class%29#log-name--value--description----).

1. Skapa ett träningsskript som innehåller loggningslogiken `train.py`.

   [!code-python[](~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Skicka ```train.py```-skriptet för körning i en användarhanterad miljö. Hela skriptmappen skickas för träningen.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)]


   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    Parametern `show_output` aktiverar utförlig loggning så att du kan se information från träningsprocessen, samt information om eventuella fjärresurser eller beräkningsmål. Använd följande kod för att aktivera utförlig loggning när du skickar experimentet.

```python
run = exp.submit(src, show_output=True)
```

Du kan också använda samma parameter i funktionen `wait_for_completion` för den resulterande körningen.

```python
run.wait_for_completion(show_output=True)
```

## <a name="native-python-logging"></a>Intern Python-loggning

Vissa loggar i SDK:n kan innehålla ett fel som uppmanar dig att ange loggningsnivån DEBUG. Om du ska ange loggningsnivån lägger du till nedanstående kod i skriptet.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="other-logging-sources"></a>Andra loggningskällor

Azure Machine Learning kan också logga information från andra källor under träningen, till exempel automatiserade maskininlärningskörningar eller Docker-containrar som kör jobben. Dessa loggar dokumenteras inte, men om du stöter på problem och kontaktar Microsofts support kan de använda loggarna vid felsökning.

Information om loggning av mått i Azure Machine Learning-designern finns i [Loggning av mått i designern](how-to-track-designer-experiments.md)

## <a name="example-notebooks"></a>Exempelnotebook-filer

Följande notebook-filer demonstrerar begreppen i den här artikeln:
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Läs de här artiklarna om du vill lära dig mer om att använda Azure Machine Learning:

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien [Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).