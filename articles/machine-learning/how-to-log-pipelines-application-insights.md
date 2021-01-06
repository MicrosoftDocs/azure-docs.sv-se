---
title: Övervaka & samla in pipeline-loggfiler
titleSuffix: Azure Machine Learning
description: Lägg till loggning i din utbildning och pipeliner för batch-Poäng och Visa de loggade resultaten i Application Insights.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.date: 08/11/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c0cb4527349b09ed8e794cc55dee6f9e54f8a7d4
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937373"
---
# <a name="collect-machine-learning-pipeline-log-files-in-application-insights-for-alerts-and-debugging"></a>Samla in loggfiler för Machine Learning-pipeline i Application Insights för aviseringar och fel sökning


Python-biblioteket för [openräkning](https://opencensus.io/quickstart/python/) kan användas för att dirigera loggar till Application Insights från dina skript. Genom att aggregera loggar från pipeline-körningar på en plats kan du bygga frågor och diagnostisera problem. Med hjälp av Application Insights kan du spåra loggar över tid och jämföra pipeline-loggar i flera körningar.

Om du loggar in på samma plats får du en historik över undantag och fel meddelanden. Eftersom Application Insights integreras med Azure-aviseringar kan du även skapa aviseringar baserat på Application Insights frågor.

## <a name="prerequisites"></a>Krav

* Följ stegen för att skapa en [Azure Machine Learning](./how-to-manage-workspace.md) arbets yta och [skapa din första pipeline](./how-to-create-your-first-pipeline.md)
* [Konfigurera utvecklings miljön](./how-to-configure-environment.md) för att installera Azure Machine Learning SDK.
* Installera [Openräkningens Azure Monitor export](https://pypi.org/project/opencensus-ext-azure/) paket lokalt:
  ```python
  pip install opencensus-ext-azure
  ```
* Skapa en [Application Insights-instans](../azure-monitor/app/opencensus-python.md) (det här dokumentet innehåller också information om hur du hämtar anslutnings strängen för resursen)

## <a name="getting-started"></a>Komma igång

Det här avsnittet är en introduktion till hur du använder openräkning från en Azure Machine Learning pipeline. En detaljerad själv studie kurs finns i [Openinventering Azure Monitor exportörer](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)

Lägg till en PythonScriptStep i din Azure ML-pipeline. Konfigurera din [RunConfiguration](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py) med beroendet av openinventering-ext-Azure. Konfigurera `APPLICATIONINSIGHTS_CONNECTION_STRING` miljövariabeln.

```python
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import RunConfiguration
from azureml.pipeline.core import Pipeline
from azureml.pipeline.steps import PythonScriptStep

# Connecting to the workspace and compute target not shown

# Add pip dependency on OpenCensus
dependencies = CondaDependencies()
dependencies.add_pip_package("opencensus-ext-azure>=1.0.1")
run_config = RunConfiguration(conda_dependencies=dependencies)

# Add environment variable with Application Insights Connection String
# Replace the value with your own connection string
run_config.environment.environment_variables = {
    "APPLICATIONINSIGHTS_CONNECTION_STRING": 'InstrumentationKey=00000000-0000-0000-0000-000000000000'
}

# Configure step with runconfig
sample_step = PythonScriptStep(
        script_name="sample_step.py",
        compute_target=compute_target,
        runconfig=run_config
)

# Submit new pipeline run
pipeline = Pipeline(workspace=ws, steps=[sample_step])
pipeline.submit(experiment_name="Logging_Experiment")
```

Skapa en fil med namnet `sample_step.py`. Importera AzureLogHandler-klassen för att dirigera loggar till Application Insights. Du måste också importera python-loggningsdatabasen.

```python
from opencensus.ext.azure.log_exporter import AzureLogHandler
import logging
```

Lägg sedan till AzureLogHandler i python-loggen.

```python
logger = logging.getLogger(__name__)
logger.setLevel(logging.DEBUG)
logger.addHandler(logging.StreamHandler())

# Assumes the environment variable APPLICATIONINSIGHTS_CONNECTION_STRING is already set
logger.addHandler(AzureLogHandler())
logger.warning("I will be sent to Application Insights")
```

## <a name="logging-with-custom-dimensions"></a>Logga med anpassade dimensioner
 
Som standard har loggar som vidarebefordras till Application Insights inte tillräckligt med kontext för att spåra tillbaka till körningen eller experimentet. Ytterligare fält krävs för att göra det åtgärds bara att åtgärda problem. 

Om du vill lägga till dessa fält kan du lägga till anpassade dimensioner för att ge kontext till ett logg meddelande. Ett exempel är när någon vill visa loggar i flera steg i samma pipeline-körning.

Anpassade dimensioner utgör en ord lista med nyckel värdes par (lagrade som sträng, sträng). Ord listan skickas sedan till Application Insights och visas som en kolumn i frågeresultatet. De enskilda dimensionerna kan användas som [frågeparametrar](#additional-helpful-queries).

### <a name="helpful-context-to-include"></a>Användbart sammanhang för att inkludera

| Fält                          | Uppföljning/exempel                                                                                                                                                                       |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| parent_run_id                  | Kan fråga efter loggar med samma parent_run_id för att se loggar över tid för alla steg, i stället för att behöva gå in i varje enskilt steg                                        |
| step_id                        | Kan fråga efter loggar med samma step_id för att se var ett problem har inträffat med en smal omfattning för bara det enskilda steget                                                        |
| step_name                      | Kan fråga efter loggar för att se steg prestanda över tid. Hjälper dig också att hitta en step_id för de senaste körningarna utan simhopp till portalens användar gränssnitt                                          |
| experiment_name                | Kan fråga över loggar för att se experimentets prestanda över tid. Hjälper dig också att hitta en parent_run_id eller step_id för de senaste körningarna utan simhopp till portalens användar gränssnitt                   |
| run_url                 | Kan tillhandahålla en länk direkt tillbaka till körnings undersökningen. |

**Andra användbara fält**

De här fälten kan kräva ytterligare kod Instrumentation och tillhandahålls inte av körnings kontexten.

| Fält                   | Uppföljning/exempel                                                                                                                                                                                                           |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| build_url/build_version | Om du använder CI/CD för att distribuera, kan det här fältet korrelera loggar till den kod version som tillhandahöll steget och pipeline-logiken. Den här länken kan hjälpa dig att diagnostisera problem eller identifiera modeller med specifika egenskaper (log/Metric-värden) |
| run_type                       | Kan skilja mellan olika modell typer eller utbildningar jämfört med poängsättnings körningar |

### <a name="creating-a-custom-dimensions-dictionary"></a>Skapa en anpassad dimensions ord lista

```python
from azureml.core import Run

run = Run.get_context(allow_offline=False)

custom_dimensions = {
    "parent_run_id": run.parent.id,
    "step_id": run.id,
    "step_name": run.name,
    "experiment_name": run.experiment.name,
    "run_url": run.parent.get_portal_url(),
    "run_type": "training"
}

# Assumes AzureLogHandler was already registered above
logger.info("I will be sent to Application Insights with Custom Dimensions", extra= {"custom_dimensions":custom_dimensions})
```

## <a name="opencensus-python-logging-considerations"></a>Överväganden om python-loggning i openräkning

AzureLogHandler för openräkning används för att dirigera python-loggar till Application Insights. Därför bör python-loggningens olika delarna övervägas. När en loggare skapas har den en standard logg nivå som visar loggar som är större än eller lika med den nivån. En referens för att använda python-loggnings funktioner är [loggnings Cookbook](https://docs.python.org/3/howto/logging-cookbook.html).

`APPLICATIONINSIGHTS_CONNECTION_STRING`Miljö variabeln krävs för Openinventerings biblioteket. Vi rekommenderar att du ställer in den här miljövariabeln i stället för att skicka den som en pipeline-parameter för att undvika att skicka i klartext-anslutningssträngar.

## <a name="querying-logs-in-application-insights"></a>Fråga efter loggar i Application Insights

Loggarna som dirigeras till Application Insights visas under spår eller undantag. Se till att ändra tids perioden så att du kan ta med din pipeline-körning.

![Resultat av Application Insights fråga](./media/how-to-debug-pipelines-application-insights/traces-application-insights-query.png)

Resultatet i Application Insights visar logg meddelandet och nivån, fil Sök vägen och kod numret. Den visar även eventuella anpassade dimensioner som ingår. I den här bilden visar ord listan customDimensions nyckel/värde-par från föregående [kod exempel](#creating-a-custom-dimensions-dictionary).

### <a name="additional-helpful-queries"></a>Ytterligare användbara frågor

Några av frågorna nedan använder "customDimensions. level". Dessa allvarlighets nivåer motsvarar den nivå som python-loggen ursprungligen skickades med. Mer information om frågor finns i [Azure Monitor logg frågor](/azure/data-explorer/kusto/query/).

| Användningsfall                                                               | Söka i data                                                                                              |
|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Logg resultat för en speciell anpassad dimension, till exempel "parent_run_id" | <pre>traces \| <br>where customDimensions.parent_run_id == '931024c2-3720-11ea-b247-c49deda841c1</pre> |
| Logg resultat för all utbildning körs under de senaste 7 dagarna                     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.run_type == 'training'</pre>           |
| Logga resultat med severityLevel-fel under de senaste 7 dagarna              | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR'                     |
| Antal logg resultat med severityLevel-fel under de senaste 7 dagarna     | <pre>traces \| <br>where timestamp > ago(7d) <br>and customDimensions.Level == 'ERROR' \| <br>summarize count()</pre> |

## <a name="next-steps"></a>Efterföljande moment

När du har loggat in Application Insights-instansen kan de användas för att ange [Azure Monitor aviseringar](../azure-monitor/platform/alerts-overview.md#what-you-can-alert-on) baserat på frågeresultat.

Du kan också lägga till resultat från frågor till en [Azure-instrumentpanel](../azure-monitor/learn/tutorial-app-dashboards.md#add-logs-query) för ytterligare insikter.
