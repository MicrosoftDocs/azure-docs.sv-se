---
title: Utlös Azure Machine Learning pipelines
titleSuffix: Azure Machine Learning
description: Med utlösta pipelines kan du automatisera rutinmässiga, tids krävande uppgifter, till exempel data bearbetning, utbildning och övervakning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 01/29/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3ecf4458b052f4fdc0eb2e6e697b0468c71ce9c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519664"
---
# <a name="trigger-machine-learning-pipelines"></a>Utlös ande maskin inlärnings pipeliner

I den här artikeln får du lära dig hur du program mässigt schemalägger en pipeline för att köras på Azure. Du kan skapa ett schema baserat på förfluten tid eller ändringar i fil systemet. Tidsbaserade scheman kan användas för att ta hand om rutin uppgifter, till exempel övervakning av data drift. Ändrings scheman kan användas för att reagera på oregelbundna eller oförutsägbara ändringar, till exempel nya data som laddas upp eller gamla data redige ras. När du har lärt dig hur du skapar scheman får du lära dig hur du hämtar och inaktiverar dem. Slutligen får du lära dig hur du använder andra Azure-tjänster, Azure Logic app och Azure Data Factory för att köra pipeliner. En Azure Logic-app gör det möjligt för mer komplex att utlösa logik eller beteende. Med Azure Data Factory pipelines kan du anropa en pipeline för maskin inlärning som en del av en större pipeline för data dirigering.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree).

* En python-miljö där Azure Machine Learning SDK för python är installerat. Mer information finns i [skapa och hantera återanvändbara miljöer för utbildning och distribution med Azure Machine Learning.](how-to-use-environments.md)

* En Machine Learning arbets yta med en publicerad pipeline. Du kan använda en inbyggd pipeline för att [skapa och köra Machine Learning med Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md).

## <a name="trigger-pipelines-with-azure-machine-learning-sdk-for-python"></a>Utlös pipelines med Azure Machine Learning SDK för python

Om du vill schemalägga en pipeline behöver du en referens till din arbets yta, identifieraren för den publicerade pipelinen och namnet på det experiment som du vill skapa schemat i. Du kan hämta dessa värden med följande kod:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Skapa ett schema

Om du vill köra en pipeline regelbundet skapar du ett schema. En `Schedule` kopplar en pipeline, ett experiment och en utlösare. Utlösaren kan antingen vara en `ScheduleRecurrence` som beskriver vänte tiden mellan körningar eller en lagrings Sök väg som anger en katalog som ska bevaka ändringar. I båda fallen behöver du pipeline-identifieraren och namnet på experimentet som schemat ska skapas i.

Importera och klasser överst i python-filen `Schedule` `ScheduleRecurrence` :

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Skapa ett tidsbaserat schema

`ScheduleRecurrence`Konstruktorn har ett obligatoriskt `frequency` argument som måste vara en av följande strängar: "minut", "Hour", "Day", "Week" eller "Month". Det kräver också ett heltals `interval` argument som anger hur många av `frequency` enheterna som ska förflyta mellan schema startar. Valfria argument ger dig mer information om start tider, enligt beskrivningen i [SCHEDULERECURRENCE SDK-dokument](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence).

Skapa en `Schedule` som börjar köras var 15: e minut:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Skapa ett ändrings baserat schema

Pipelines som utlöses av fil ändringar kan vara mer effektiva än tidsbaserade scheman. När du vill göra något innan en fil ändras eller när en ny fil läggs till i en data katalog kan du Förbearbeta filen. Du kan övervaka ändringar i ett data lager eller ändringar i en angiven katalog i data lagret. Om du övervakar en angiven katalog kommer ändringar i under kataloger i den katalogen _inte_ att utlösa någon körning.

Om du vill skapa en fil som `Schedule` är aktive rad måste du ange `datastore` parametern i anropet till [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Om du vill övervaka en mapp anger du `path_on_datastore` argumentet.

Med `polling_interval` argumentet kan du ange, i minuter, den frekvens som data lagret är markerat för ändringar.

Om pipelinen har konstruerats med en [Datapath](/python/api/azureml-core/azureml.data.datapath.datapath) - [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter)kan du ange variabeln till namnet på den ändrade filen genom att ange `data_path_parameter_name` argumentet.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Valfria argument när du skapar ett schema

Förutom argumenten som beskrivits tidigare kan du ange `status` argumentet till `"Disabled"` för att skapa ett inaktivt schema. Slutligen kan `continue_on_step_failure` du skicka ett booleskt värde som åsidosätter förloppets standard funktions sätt.

## <a name="view-your-scheduled-pipelines"></a>Visa dina schemalagda pipelines

Navigera till Azure Machine Learning i webbläsaren. I avsnittet **slut punkter** i navigerings fönstret väljer du pipeline- **slutpunkter**. Då går du till en lista över de pipeliner som publicerats i arbets ytan.

:::image type="content" source="./media/how-to-trigger-published-pipeline/scheduled-pipelines.png" alt-text="Sidan pipeliner i AML":::

På den här sidan kan du se översikts information om alla pipeliner på arbets ytan: namn, beskrivningar, status och så vidare. Gå in genom att klicka i din pipeline. På den resulterande sidan finns det mer information om din pipeline och du kan öka detalj nivån i enskilda körningar.

## <a name="deactivate-the-pipeline"></a>Inaktivera pipelinen

Om du har en `Pipeline` som har publicerats, men inte schemalagt, kan du inaktivera den med:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Om pipelinen är schemalagd måste du först avbryta schemat. Hämta schemats identifierare från portalen eller genom att köra:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

När du har angett `schedule_id` att du vill inaktivera kör du:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Om du sedan kör `Schedule.list(ws)` igen bör du hämta en tom lista.

## <a name="use-azure-logic-apps-for-complex-triggers"></a>Använd Azure Logic Apps för komplexa utlösare 

Mer komplexa utlösnings regler eller beteenden kan skapas med hjälp av en [Azure Logic-app](../logic-apps/logic-apps-overview.md).

Om du vill använda en Azure Logic-app för att utlösa en Machine Learning pipeline behöver du REST-slutpunkten för en publicerad Machine Learning-pipeline. [Skapa och publicera din pipeline](./how-to-create-machine-learning-pipelines.md). Hitta sedan REST-slutpunkten för din `PublishedPipeline` med hjälp av pipeline-ID:

```python
# You can find the pipeline ID in Azure Machine Learning studio

published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
published_pipeline.endpoint 
```

## <a name="create-a-logic-app"></a>Skapa en logikapp

Skapa nu en [Azure Logic app](../logic-apps/logic-apps-overview.md) -instans. Om du vill kan du [använda en integrerings tjänst miljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) och [Konfigurera en kundhanterad nyckel](../logic-apps/customer-managed-keys-integration-service-environment.md) för användning av din Logic app.

När du har etablerat din Logi Kap par kan du använda de här stegen för att konfigurera en utlösare för din pipeline:

1. [Skapa en systemtilldelad hanterad identitet](../logic-apps/create-managed-service-identity.md) för att ge appen åtkomst till din Azure Machine Learning-arbetsyta.

1. Navigera till vyn Logic App Designer och välj den tomma Logic app-mallen. 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/blank-template.png" alt-text="Tom mall":::

1. I designern söker du efter **BLOB**. Markera kryss rutan **när en BLOB läggs till eller ändras (endast egenskaper)** utlösas och Lägg till den här utlösaren i din Logic app.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/add-trigger.png" alt-text="Lägg till utlösare":::

1. Fyll i anslutnings informationen för det Blob Storage-konto som du vill övervaka för BLOB-tillägg eller-ändringar. Välj den behållare som ska övervakas. 
 
    Välj **intervall** och **frekvens** för att söka efter uppdateringar som fungerar för dig.  

    > [!NOTE]
    > Den här utlösaren övervakar den valda behållaren men övervakar inte undermappar.

1. Lägg till en HTTP-åtgärd som ska köras när en ny eller ändrad BLOB identifieras. Välj **+ nytt steg** och Sök sedan efter och välj http-åtgärd.

  > [!div class="mx-imgBorder"]
  > :::image type="content" source="media/how-to-trigger-published-pipeline/search-http.png" alt-text="Sök efter HTTP-åtgärd":::

  Använd följande inställningar för att konfigurera din åtgärd:

  | Inställning | Värde | 
  |---|---|
  | HTTP-åtgärd | POST |
  | URI |slut punkten till den publicerade pipelinen som du hittade som en [förutsättning](#prerequisites) |
  | Autentiseringsläge | Hanterad identitet |

1. Konfigurera ditt schema för att ange värdet för alla [Datapath-PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) som du kan ha:

    ```json
    {
      "DataPathAssignments": {
        "input_datapath": {
          "DataStoreName": "<datastore-name>",
          "RelativePath": "@{triggerBody()?['Name']}" 
        }
      },
      "ExperimentName": "MyRestPipeline",
      "ParameterAssignments": {
        "input_string": "sample_string3"
      },
      "RunSource": "SDK"
    }
    ```

    Använd `DataStoreName` du har lagt till i din arbets yta som en [förutsättning](#prerequisites).
     
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="media/how-to-trigger-published-pipeline/http-settings.png" alt-text="HTTP-inställningar":::

1. Välj **Spara** så är ditt schema nu klart.

> [!IMPORTANT]
> Om du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera åtkomst till din pipeline, anger du [behörigheterna för ditt pipeline-scenario (utbildning eller poängsättning)](how-to-assign-roles.md#common-scenarios).

## <a name="call-machine-learning-pipelines-from-azure-data-factory-pipelines"></a>Anropa maskin inlärnings pipeliner från Azure Data Factory pipelines

I en Azure Data Factory pipelinen kör *Machine Learning kör pipeline* -aktiviteten en Azure Machine Learning pipeline. Du hittar den här aktiviteten på sidan för Data Factory sidan redigering i kategorin *Machine Learning* :

:::image type="content" source="media/how-to-trigger-published-pipeline/azure-data-factory-pipeline-activity.png" alt-text="Skärm bild som visar aktiviteten ML pipeline i Azure Data Factory redigerings miljö":::

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde du Azure Machine Learning SDK för python för att schemalägga en pipeline på två olika sätt. Ett schema återkommer baserat på förfluten Klock tid. Det andra schemat körs om en fil ändras på en angiven `Datastore` eller i en katalog i arkivet. Du såg hur du använder portalen för att undersöka pipelinen och de enskilda körningarna. Du har lärt dig hur du inaktiverar ett schema så att pipelinen slutar köras. Slutligen skapade du en Azure Logic-app för att utlösa en pipeline. 

Mer information finns i:

> [!div class="nextstepaction"]
> [Använd Azure Machine Learning pipelines för batch-Poäng](tutorial-pipeline-batch-scoring-classification.md)

* Läs mer om [pipelines](concept-ml-pipelines.md)
* Lär dig mer om att [utforska Azure Machine Learning med Jupyter](samples-notebooks.md)