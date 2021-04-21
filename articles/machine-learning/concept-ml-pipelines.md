---
title: Vad är pipelines för maskininlärning?
titleSuffix: Azure Machine Learning
description: Lär dig hur maskininlärningspipelines hjälper dig att skapa, optimera och hantera arbetsflöden för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 57f5da06909436e0cbce92559c29c309ca9e20e3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819240"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Vad är Azure Machine Learning pipelines?

I den här artikeln får du lära dig hur en pipeline för maskininlärning hjälper dig att skapa, optimera och hantera ditt arbetsflöde för maskininlärning. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Vilken Azure-pipelineteknik ska jag använda?

Azure-molnet tillhandahåller flera typer av pipelines, var och en med olika syfte. I följande tabell visas de olika pipelines och vad de används för:

| Scenario | Primär persona | Azure-erbjudande | OSS-erbjudande | Kanonisk pipe | Styrkor | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modellorkestrering (maskininlärning) | Dataexpert | Azure Machine Learning Pipelines | Kubeflow-pipelines | Data – > modell | Distribution, cachelagring, kod först, återanvändning | 
| Dataorkestrering (dataförberedelser) | Datatekniker | [Azure Data Factory-pipeliner](../data-factory/concepts-pipelines-activities.md) | Apache Airflow | Data – > data | Starkt typad rörelse, datacentrerade aktiviteter |
| Kod & apporkestrering (CI/CD) | Apputvecklare/ops | [Azure-pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kod + modell - > App/Service | De flesta öppna och flexibla aktivitetssupport, godkännandeköer, faser med gating | 

## <a name="what-can-machine-learning-pipelines-do"></a>Vad kan maskininlärningspipelines göra?

En Azure Machine Learning pipeline är ett oberoende körbart arbetsflöde för en fullständig maskininlärningsuppgift. Underaktiviteter kapslas in som en serie steg i pipelinen. En Azure Machine Learning pipeline kan vara så enkel som en som anropar ett Python-skript, så _kan göra_ vad som helst. Pipelines _bör fokusera_ på maskininlärningsuppgifter som:

+ Förberedelse av data, inklusive import, validering och rensning, omvandling och transformering, normalisering och mellanlagring
+ Träningskonfiguration, inklusive parametrisera argument, sökvägar och loggnings-/rapporteringskonfigurationer
+ Träna och verifiera effektivt och upprepade gånger. Effektiviteten kan komma från att ange specifika dataunderuppsättningar, olika beräkningsresurser för maskinvara, distribuerad bearbetning och förloppsövervakning
+ Distribution, inklusive versionshantering, skalning, etablering och åtkomstkontroll

Oberoende steg gör att flera dataforskare kan arbeta med samma pipeline samtidigt utan att överskatta beräkningsresurser. Separata steg gör det också enkelt att använda olika beräkningstyper/storlekar för varje steg.

När pipelinen har utformats finns det ofta mer finjustering runt träningsloopen för pipelinen. När du kör en pipeline igen hoppar körningen till de steg som behöver köras igen, till exempel ett uppdaterat träningsskript. Steg som inte behöver köras igen hoppas över. 

Med pipelines kan du välja att använda olika maskinvara för olika uppgifter. Azure samordnar de olika [beräkningsmål som du](concept-azure-machine-learning-architecture.md) använder, så dina mellanliggande data flödar sömlöst till underordnade beräkningsmål.

Du kan [spåra måtten för dina pipelineexperiment direkt](./how-to-log-view-metrics.md) i Azure Portal eller på arbetsytans [landningssida (förhandsversion).](https://ml.azure.com) När en pipeline har publicerats kan du konfigurera en REST-slutpunkt så att du kan köra pipelinen igen från valfri plattform eller stack.

Kort sagt kan alla komplexa uppgifter i maskininlärningslivscykeln hjälpas med pipelines. Andra Azure-pipelinetekniker har sina egna styrkor. [Azure Data Factory är bra](../data-factory/concepts-pipelines-activities.md) på att arbeta med data och [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) är rätt verktyg för kontinuerlig integrering och distribution. Men om ditt fokus är maskininlärning är Azure Machine Learning pipelines förmodligen det bästa valet för dina arbetsflödesbehov. 

### <a name="analyzing-dependencies"></a>Analysera beroenden

Många programmeringsekosystem har verktyg som orkestrering av resurs-, biblioteks- eller kompileringsberoenden. I allmänhet använder de här verktygen filtidsstämplar för att beräkna beroenden. När en fil ändras uppdateras bara den och dess beroenden (hämtas, kompileras om eller paketeras). Azure Machine Learning utökar det här konceptet. Precis som traditionella byggverktyg beräknar pipelines beroenden mellan steg och utför endast de nödvändiga omberäkningarna. 

Beroendeanalysen i Azure Machine Learning är dock mer avancerad än enkla tidsstämplar. Varje steg kan köras i olika maskin- och programvarumiljöer. Förberedelse av data kan vara en tidskrävande process men behöver inte köras på maskinvara med kraftfulla GPU:er, vissa steg kan kräva OS-specifik programvara, du kanske vill använda distribuerad träning och så vidare. 

Azure Machine Learning automatiskt alla beroenden mellan pipelinestegen. Den här orkestreringslösningen kan omfatta att snurra upp och ned Docker-avbildningar, koppla och koppla från beräkningsresurser och flytta data mellan stegen på ett konsekvent och automatiskt sätt.

### <a name="coordinating-the-steps-involved"></a>Samordna de steg som ingår

När du skapar och kör `Pipeline` ett objekt sker följande avancerade steg:

+ För varje steg beräknar tjänsten kraven för:
    + Beräkningsresurser för maskinvara
    + OS-resurser (Docker-avbildningar)
    + Programvaruresurser (Conda/virtualenv-beroenden)
    + Indata 
+ Tjänsten avgör beroenden mellan steg, vilket resulterar i en graf för dynamisk körning
+ När varje nod i körningsdiagrammet körs:
    + Tjänsten konfigurerar den nödvändiga maskin- och programvarumiljön (kanske återanvända befintliga resurser)
    + Steget körs, vilket ger loggnings- och övervakningsinformation till dess innehållande `Experiment` objekt
    + När steget har slutförts förbereds dess utdata som indata till nästa steg och/eller skrivs till lagringen
    + Resurser som inte längre behövs slutförs och frånkopplade

![Pipelinesteg](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Skapa pipelines med Python SDK

I Azure Machine Learning [Python SDK](/python/api/overview/azure/ml/install)är en pipeline ett Python-objekt som definierats i `azureml.pipeline.core` modulen. Ett [Pipeline-objekt](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) innehåller en ordnad sekvens av ett eller flera [PipelineStep-objekt.](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) Klassen är abstrakt och de faktiska stegen kommer att vara av underklasser som `PipelineStep` [EstimatorStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep) [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)eller [DataTransferStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep) Klassen [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) innehåller en återanvändbar sekvens med steg som kan delas mellan pipelines. En `Pipeline` körs som en del av en `Experiment` .

En Azure Machine Learning-pipeline associeras med en Azure Machine Learning arbetsyta och ett pipelinesteg associeras med ett beräkningsmål som är tillgängligt på den arbetsytan. Mer information finns i [Skapa och hantera Azure Machine Learning arbetsytor i Azure Portal](./how-to-manage-workspace.md) eller Vad är [beräkningsmål i Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>En enkel Python-pipeline

Det här kodfragmentet visar de objekt och anrop som behövs för att skapa och köra en `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Kodfragmentet börjar med vanliga Azure Machine Learning objekt, `Workspace` en `Datastore` , en [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)och en `Experiment` . Sedan skapar koden objekten som ska innehålla `input_data` och `prepped_data_path` . är `input_data` en instans av [FileDataset och](/python/api/azureml-core/azureml.data.filedataset) är en instans av `prepped_data_path`  [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). För standardbeteendet är att kopiera utdata till datalagringen under sökvägen , där är Körningens ID och är ett automatiskt genererat värde om det inte anges `OutputFileDatasetConfig` `workspaceblobstore` av `/dataset/{run-id}/{output-name}` `run-id` `output-name` utvecklaren.

Koden för förberedelse av data (visas inte), skriver avgränsade filer till `prepped_data_path` . Dessa utdata från dataförberedelsesteget `prepped_data` skickas som till träningssteget. 

Matrisen `steps` innehåller de två `PythonScriptStep` s, och `dataprep_step` `train_step` . Azure Machine Learning analyserar databeroendet för `prepped_data` och körs `dataprep_step` före `train_step` . 

Sedan instansierar koden själva objektet `Pipeline` och går in i arbetsytan och stegmatrisen. Anropet till `experiment.submit(pipeline)` startar Azure ML-pipelinekörningen. Anropet `wait_for_completion()` till blockerar tills pipelinen har slutförts. 

Mer information om hur du ansluter din pipeline till dina data finns i artiklarna [Dataåtkomst](concept-data.md) i Azure Machine Learning och Flytta data till och mellan steg i [ML-pipeline (Python).](how-to-move-data-in-out-of-pipelines.md) 

## <a name="building-pipelines-with-the-designer"></a>Skapa pipelines med designern

Utvecklare som föredrar en visuell designyta kan använda Azure Machine Learning för att skapa pipelines. Du kan komma åt det här verktyget **från Designer-valet** på startsidan för din arbetsyta.  Med designern kan du dra och släppa steg på designytan. 

När du utformar pipelines visuellt visas indata och utdata för ett steg tydligt. Du kan dra och släppa dataanslutningar så att du snabbt kan förstå och ändra dataflödet i din pipeline.

![Azure Machine Learning designerexempel](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Viktiga fördelar

De främsta fördelarna med att använda pipelines för dina arbetsflöden för maskininlärning är:

|Viktig fördel|Description|
|:-------:|-----------|
|**Obevakade &nbsp; körningar**|Schemalägg stegen så att de körs parallellt eller i följd på ett tillförlitligt och obevakat sätt. Förberedelse och modellering av data kan ta dagar eller veckor, och med pipelines kan du fokusera på andra uppgifter medan processen körs. |
|**Heterogen beräkning**|Använd flera pipelines som på ett tillförlitligt sätt koordineras över heterogena och skalbara beräkningsresurser och lagringsplatser. Använd tillgängliga beräkningsresurser effektivt genom att köra enskilda pipelinesteg på olika beräkningsmål, till exempel HDInsight, virtuella GPU-datorer för datavetenskap och Databricks.|
|**Återanvändning**|Skapa pipeline-mallar för specifika scenarier, till exempel omträning och batchbedömning. Utlösa publicerade pipelines från externa system via enkla REST-anrop.|
|**Spårning och versionshantering**|I stället för att manuellt spåra data och resultatsökvägar när du itererar använder du pipelines SDK för att uttryckligen namnge och versionsutse dina datakällor, indata och utdata. Du kan också hantera skript och data separat för ökad produktivitet.|
| **Modularitet** | Genom att separera områden av problem och isolera ändringar kan programvaran utvecklas snabbare med högre kvalitet. | 
|**Samarbete**|Med pipelines kan dataexperter samarbeta inom alla delar av designprocessen för maskininlärning samtidigt som de kan arbeta med pipelinesteg.|

## <a name="next-steps"></a>Nästa steg

Azure Machine Learning pipelines är en kraftfull anläggning som börjar leverera värde i de tidiga utvecklingsstegen. Värdet ökar när teamet och projektet växer. Den här artikeln har förklarat hur pipelines anges med Azure Machine Learning Python SDK och dirigeras i Azure. Du har sett lite enkel källkod och introducerats för några av de `PipelineStep` klasser som är tillgängliga. Du bör ha en känsla för när du ska Azure Machine Learning pipelines och hur Azure kör dem. 

+ Lär dig hur [du skapar din första pipeline.](./how-to-create-machine-learning-pipelines.md)

+ Lär dig hur [du kör batchförutsägelser på stora data.](tutorial-pipeline-batch-scoring-classification.md )

+ Se SDK-referensdokumenten för [pipelinekärna och](/python/api/azureml-pipeline-core/) [pipelinesteg.](/python/api/azureml-pipeline-steps/)

+ Prova jupyter-exempelanteckningsböcker som visar [Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Lär dig hur du [kör notebook-datorer för att utforska den här tjänsten.](samples-notebooks.md)