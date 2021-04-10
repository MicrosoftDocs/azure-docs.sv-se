---
title: Övervaka och Visa ML-körnings loggar & mått
titleSuffix: Azure Machine Learning
description: Övervaka dina ML-experiment och Visa körnings mått med Jupyter-widgetar och Azure Machine Learning Studio.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c258ac62617cb6ac954e0b8c59928225c7f477b1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105935586"
---
# <a name="monitor-and-view-ml-run-logs-and-metrics"></a>Övervaka och Visa ML körnings loggar och mått

Lär dig hur du övervakar Azure Machine Learning kör och visar deras loggar. 

När du kör ett experiment strömmas loggar och mått åt dig.  Dessutom kan du lägga till egna.  Mer information finns i [Aktivera loggning i Azure ml-utbildning](how-to-track-experiments.md).

Loggarna kan hjälpa dig att diagnostisera fel och varningar för din körning. Prestanda mått som parametrar och modell precision hjälper dig att spåra och övervaka dina körningar.

I den här artikeln får du lära dig hur du visar loggar med följande metoder:

> [!div class="checklist"]
> * Övervakare körs i Studio
> * Övervakare som körs med hjälp av Jupyter Notebook-widgeten
> * Övervaka automatiserade maskin inlärnings körningar
> * Visa utgående loggar vid slut för ande
> * Visa utgående loggar i Studio

Allmän information om hur du hanterar dina experiment finns i [starta, övervaka och avbryta inlärnings körningar](how-to-manage-runs.md).

## <a name="monitor-runs-using-the-jupyter-notebook-widget"></a>Övervaka körningar med hjälp av widgeten Jupyter Notebook

När du använder **ScriptRunConfig** -metoden för att skicka körningar kan du se förloppet för körningen med hjälp av [Jupyter-widgeten](/python/api/azureml-widgets/azureml.widgets). Precis som körningsöverföringen är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till var 15:e sekund tills jobbet har slutförts.

Visa widgeten Jupyter i väntan på att körningen ska slutföras.
    
```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

![Skärm bild av widgeten Jupyter Notebook](./media/how-to-track-experiments/run-details-widget.png)

Du kan också hämta en länk till samma visning i din arbets yta.

```python
print(run.get_portal_url())
```

## <a name="monitor-automated-machine-learning-runs"></a>Övervaka automatiserade maskin inlärnings körningar

För automatisk maskin inlärning körs, för att få åtkomst till diagram från en tidigare körning, ersätter du `<<experiment_name>>` med lämpligt experiment namn:

```python
from azureml.widgets import RunDetails
from azureml.core.run import Run

experiment = Experiment (workspace, <<experiment_name>>)
run_id = 'autoML_my_runID' #replace with run_ID
run = Run(experiment, run_id)
RunDetails(run).show()
```

![Jupyter Notebook-widget för automatiserad Machine Learning](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)

## <a name="show-output-upon-completion"></a>Visa utdata vid slut för ande

När du använder **ScriptRunConfig** kan du använda ```run.wait_for_completion(show_output = True)``` för att visa när modell träningen är klar. ```show_output```Flaggan ger dig utförliga utdata. Mer information finns i avsnittet ScriptRunConfig i [så här aktiverar du loggning](how-to-track-experiments.md#scriptrun-logs).

<a id="queryrunmetrics"></a>

## <a name="view-run-metrics"></a>Visa körnings mått

## <a name="via-the-sdk"></a>Via SDK
Du kan visa måtten för en utbildad modell med ```run.get_metrics()``` . Se exemplet nedan. 

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

## <a name="view-run-records-in-the-studio"></a>Visa körnings poster i Studio

Du kan bläddra bland slutförda körnings poster, inklusive loggade mått, i [Azure Machine Learning Studio](https://ml.azure.com).

Gå till fliken **experiment** . Om du vill visa alla dina körningar i arbets ytan över experiment väljer du fliken **alla körningar** . Du kan öka detalj nivån för körningar för vissa experiment genom att använda experiment filtret i den översta meny raden.

Välj fliken **alla experiment** i den enskilda experiment visningen. På instrument panelen för experimentet kan du se spårade mått och loggar för varje körning. 

Du kan också redigera tabellen kör lista för att välja flera körningar och Visa antingen det senaste, lägsta eller högsta loggade värdet för dina körningar. Anpassa dina diagram för att jämföra de inloggade måtten och agg regeringar för flera körningar. 

![Kör information i Azure Machine Learning Studio](media/how-to-track-experiments/experimentation-tab.gif)

### <a name="view-and-download-log-files-for-a-run"></a>Visa och hämta loggfiler för en körning 

Loggfiler är en viktig resurs för fel sökning av Azure ML-arbetsbelastningar. Öka detalj nivån till en speciell körning för att visa dess loggar och utdata:  

1. Gå till fliken **experiment** .
1. Välj runID för en speciell körning.
1. Välj **utdata och loggar** överst på sidan.
2. Välj **Ladda ned alla** för att ladda ned alla loggar till en zip-mapp.

:::image type="content" source="media/how-to-monitor-view-training-logs/download-logs.png" alt-text="Skärm bild av avsnittet utdata och loggar i en körning.":::

Tabellerna nedan visar innehållet i loggfilerna i de mappar som visas i det här avsnittet.

> [!NOTE]
> Du kommer inte nödvändigt vis att se alla filer för varje körning. Till exempel visas 20_image_build_log *. txt bara när en ny avbildning skapas (t. ex. När du ändrar miljön).

#### <a name="azureml-logs-folder"></a>`azureml-logs` projektbevakningsmappen

|Fil  |Beskrivning  |
|---------|---------|
|20_image_build_log.txt     | Logg för Docker-avbildnings skapande för tränings miljön, valfritt, en per körning. Gäller endast när du uppdaterar din miljö. Annars återanvänds den cachelagrade avbildningen i AML. Om det lyckas innehåller avbildnings register information för motsvarande avbildning.         |
|55_azureml-körning-<node_id # C1.txt     | STDOUT/stderr logg över värd verktyget, ett per nod. Hämtar bild till beräknings mål. OBS! den här loggen visas bara när du har skyddat beräknings resurser.         |
|65_job_prep-<node_id # C1.txt     |   STDOUT/stderr-logg för jobb förberedelse skript, ett per nod. Ladda ned koden för att beräkna mål och data lager (om det behövs).       |
|70_driver_log (_x). txt      |  STDOUT/stderr-logg från AML kontroll skript och kund övnings skript, ett per process. **Detta är standard utmatningen från skriptet. Detta är kodens loggar (t. ex. utskrifts uttryck) som visas.** I de flesta fall ska du övervaka loggarna här.       |
|70_mpi_log.txt     |   MPI Framework-logg, valfritt, en per körning. Endast för MPI-körning.   |
|75_job_post-<node_id # C1.txt     |  STDOUT/stderr-logg för jobb frigörelse skript, ett per nod. Skicka loggar, släpp upp beräknings resurserna på Azure.        |
|process_info.jspå      |   Visa vilken process som körs på vilken nod.  |
|process_status.jspå      | Visa process status, dvs. om en process inte har startats, körs eller slutförts.         |

#### <a name="logs--azureml-folder"></a>`logs > azureml` projektbevakningsmappen

|Fil  |Beskrivning  |
|---------|---------|
|110_azureml. log      |         |
|job_prep_azureml. log     |   system logg för jobb förberedelse        |
|job_release_azureml. log     | system logg för jobb lansering        |

#### <a name="logs--azureml--sidecar--node_id-folder"></a>`logs > azureml > sidecar > node_id` projektbevakningsmappen

När sidvagn är aktiverat körs jobb förberedelse-och jobb publicerings skripten i en sidvagn-behållare.  Det finns en mapp för varje nod. 

|Fil  |Beskrivning  |
|---------|---------|
|start_cms.txt     |  Logg för processen som startar när den sidvagn-behållaren startar       |
|prep_cmd.txt      |   Logg för ContextManagers som anges när körs `job_prep.py` (en del av detta kommer att strömmas till `azureml-logs/65-job_prep` )       |
|release_cmd.txt     |  Logg för ComtextManagers `job_release.py` avslutas när körs        |

#### <a name="other-folders"></a>Andra mappar

För jobb utbildning i multicompute-kluster finns loggar för varje nod-IP. Strukturen för varje nod är samma som för ett enda Node-jobb. Det finns ytterligare en loggfil-mapp för övergripande körnings-, stderr-och STDOUT-loggar.

Azure Machine Learning loggar information från en rad olika källor under utbildningen, till exempel AutoML eller Docker-behållaren som kör övnings jobbet. Många av dessa loggar dokumenteras inte. Om du stöter på problem och kontaktar Microsoft-supporten kan det hända att de kan använda dessa loggar under fel sökning.

## <a name="monitor-a-compute-cluster"></a>Övervaka ett beräknings kluster

Använd följande steg för att övervaka körningar för ett bestämt beräknings mål från webbläsaren:

1. I [Azure Machine Learning Studio](https://ml.azure.com/)väljer du din arbets yta och väljer sedan __Beräkna__ på sidans vänstra sida.

1. Välj __utbildnings kluster__ om du vill visa en lista med beräknings mål som används för utbildning. Välj sedan klustret.

    ![Välj utbildnings kluster](./media/how-to-track-experiments/select-training-compute.png)

1. Välj __körningar__. Listan över körningar som använder det här klustret visas. Om du vill visa information om en speciell körning använder du länken i kolumnen __Kör__ . Om du vill visa information om experimentet använder du länken i kolumnen __experimentera__ .

    ![Välj körningar för utbildnings kluster](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Eftersom inlärnings mål är en delad resurs kan de ha flera körningar i kö eller aktiva vid en specifik tidpunkt.
    > 
    > En körning kan innehålla underordnade körningar, så ett utbildnings jobb kan resultera i flera poster.

När en körning har slutförts visas den inte längre på den här sidan. Om du vill visa information om slutförda körningar går du till avsnittet __experiment__ i Studio och väljer experimentet och kör. Mer information finns i avsnittet [Visa mått för slutförda körningar](#view-the-experiment-in-the-web-portal).


## <a name="next-steps"></a>Nästa steg

Prova följande steg för att lära dig hur du använder Azure Machine Learning:

* Lär dig hur du [spårar experiment och aktiverar loggar i Azure Machine Learning designer](how-to-track-designer-experiments.md).

* Se ett exempel på hur du registrerar den bästa modellen och distribuerar den i självstudien [Träna en bildklassificeringsmodell med Azure Machine Learning](tutorial-train-models-with-aml.md).
