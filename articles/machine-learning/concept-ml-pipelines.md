---
title: Vad är maskin inlärnings pipeliner?
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder maskin inlärnings pipeliner för att bygga, optimera och hantera arbets flöden för Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 01/12/2021
ms.custom: devx-track-python
ms.openlocfilehash: e3f92f445068b98c12069577ddf61a71568e403b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871561"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Vad är Azure Machine Learning pipelines?

I den här artikeln får du lära dig hur en maskin inlärnings pipeline hjälper dig att bygga, optimera och hantera arbets flödet för Machine Learning. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Vilken Azure pipeline-teknik ska jag använda?

Azure-molnet tillhandahåller flera typer av pipelines, var och en med olika ändamål. I följande tabell visas de olika pipelinen och vad de används för:

| Scenario | Primär persona | Azure-erbjudande | OSS-erbjudande | Kanoniskt rör | Styrkor | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Modell dirigering (maskin inlärning) | Dataexpert | Azure Machine Learning pipelines | Kubeflow-pipeline | Data > modell | Distribution, cachelagring, kod-första, återanvänd | 
| Data dirigering (Förbered data) | Datatekniker | [Azure Data Factory-pipeliner](../data-factory/concepts-pipelines-activities.md) | Apache-luft flöde | Data-> data | Starkt inskriven förflyttning, datainriktade aktiviteter |
| Code & app Orchestration (CI/CD) | App-utvecklare/OPS | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Kod + modell-> App/Service | Det mest öppna och flexibla aktivitets stödet, godkännande köer, faser med hantera | 

## <a name="what-can-machine-learning-pipelines-do"></a>Vad kan maskin inlärnings pipeliner göra?

En Azure Machine Learning pipeline är ett oberoende körbart arbets flöde för en fullständig Machine Learning-uppgift. Underaktiviteter kapslas in som en serie steg i pipelinen. En Azure Machine Learning pipeline kan vara lika enkelt som en som anropar ett Python-skript, så det _kan hända_ bara vad som helst. Pipelines _bör_ fokusera på Machine Learning-uppgifter som:

+ Förberedelse av data, inklusive import, validering och rensning, omvandling och transformering, normalisering och mellanlagring
+ Träningskonfiguration, inklusive parametrisera argument, sökvägar och loggnings-/rapporteringskonfigurationer
+ Utbildning och validering på ett effektivt och upprepade gånger. Effektiviteten kan komma från att ange vissa data del mängder, olika maskin varu beräknings resurser, distribuerad bearbetning och förlopps övervakning
+ Distribution, inklusive versionshantering, skalning, etablering och åtkomstkontroll

Oberoende steg gör det möjligt för flera data forskare att arbeta med samma pipeline samtidigt utan beskattnings beräknings resurser. Separata steg gör det också enkelt att använda olika beräknings typer/storlekar för varje steg.

Efter att pipelinen har utformats finns det ofta mer fin justering av pipelinens inlärnings slinga. När du kör en pipeline igen hoppar körningen till de steg som måste köras igen, till exempel ett uppdaterat utbildnings skript. Steg som inte behöver köras igen hoppas över. 

Med pipelines kan du välja att använda olika maskin vara för olika uppgifter. Azure koordinerar de olika [beräknings målen](concept-azure-machine-learning-architecture.md) som du använder, så att mellanliggande data flödar sömlöst till underordnade beräknings mål.

Du kan [spåra måtten för dina pipelines experiment](./how-to-track-experiments.md) direkt i Azure Portal eller i [landnings sidan för din arbets yta (för hands version)](https://ml.azure.com). När en pipeline har publicerats kan du konfigurera en REST-slutpunkt, vilket gör att du kan köra pipelinen igen från valfri plattform eller stack.

I korthet kan alla komplexa uppgifter i Machine Learning-livscykeln bli hjälpte med pipeliner. Andra Azure pipelines-tekniker har sina egna styrkor. [Azure Data Factory pipelines](../data-factory/concepts-pipelines-activities.md) perfekt vid arbete med data och [Azure-pipeliner](https://azure.microsoft.com/services/devops/pipelines/) är det rätta verktyget för kontinuerlig integrering och distribution. Men om fokus är maskin inlärning är Azure Machine Learning pipelines förmodligen det bästa valet för arbets flödes behoven. 

### <a name="analyzing-dependencies"></a>Analysera beroenden

Många programmerings eko system har verktyg som dirigerar resurs-, biblioteks-eller kompilerings beroenden. Dessa verktyg använder vanligt vis fil-tidsstämplar för att beräkna beroenden. När en fil ändras, uppdateras bara den och dess beroenden (hämtas, kompileras om eller paketeras). Azure Machine Learning pipelines utökar det här konceptet. Precis som traditionella build-verktyg beräknar pipelines beroenden mellan stegen och utför bara de nödvändiga Omberäkningarna. 

Beroende analysen i Azure Machine Learning pipelines är mer sofistikerade än enkla tidsstämplar. Varje steg kan köras i en annan maskin-och program varu miljö. Förberedelse av data kan vara en tids krävande process men behöver inte köras på maskin vara med kraftfulla GPU: er, vissa steg kan kräva en OS-specifik program vara, du kanske vill använda distribuerad utbildning och så vidare. 

Azure Machine Learning dirigerar automatiskt alla beroenden mellan stegen i pipeline. Den här dirigeringen kan omfatta att sätta upp och ned Docker-avbildningar, koppla och koppla från beräknings resurser och flytta data mellan stegen på ett konsekvent och automatiskt sätt.

### <a name="coordinating-the-steps-involved"></a>Samordna de steg som ingår

När du skapar och kör ett `Pipeline` -objekt inträffar följande steg på hög nivå:

+ För varje steg beräknar tjänsten kraven för:
    + Maskin varu beräknings resurser
    + OS-resurser (Docker avbildningar)
    + Program varu resurser (Conda/virtuell miljö-beroenden)
    + Indata 
+ Tjänsten fastställer beroenden mellan steg, vilket resulterar i ett dynamiskt körnings diagram
+ När varje nod i körnings diagrammet körs:
    + Tjänsten konfigurerar nödvändig maskin-och program varu miljö (kanske återanvänder befintliga resurser)
    + Steget körs, vilket ger loggnings-och övervaknings information till det innehåll ande `Experiment` objektet
    + När steget är klart förbereds utmatningarna som indata till nästa steg och/eller skrivs till lagring
    + Resurser som inte längre behövs har slutförts och kopplats från

![Pipeline-steg](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Skapa pipelines med python SDK

I [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)är en pipeline ett python-objekt som definierats i `azureml.pipeline.core` modulen. Ett [pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?preserve-view=true&view=azure-ml-py) -objekt innehåller en ordnad sekvens av ett eller flera [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py) -objekt. `PipelineStep`Klassen är abstrakt och de faktiska stegen kommer att vara av underklasser som [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?preserve-view=true&view=azure-ml-py), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?preserve-view=true&view=azure-ml-py)eller [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?preserve-view=true&view=azure-ml-py). [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?preserve-view=true&view=azure-ml-py) -klassen innehåller en återanvändbara sekvens med steg som kan delas mellan pipeliner. En `Pipeline` körs som en del av en `Experiment` .

En pipeline för Azure Machine Learning är kopplad till en Azure Machine Learning arbets yta och ett steg i pipeline-steget är associerat med ett beräknings mål som är tillgängligt på arbets ytan. Mer information finns i [skapa och hantera Azure Machine Learning arbets ytor i Azure Portal](./how-to-manage-workspace.md) eller [Vad är beräknings mål i Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>En enkel python-pipeline

Det här kodfragmentet visar de objekt och anrop som behövs för att skapa och köra ett `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
output_data = OutputFileDatasetConfig()
input_named = input_data.as_named_input('input')

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_named.as_download(), "--output", output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

Kodfragmentet börjar med vanliga Azure Machine Learning objekt, a `Workspace` , a `Datastore` , a, [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py)och en `Experiment` . Sedan skapar koden de objekt som ska behållas `input_data` och `output_data` . `input_data`Är en instans av [FileDataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py) och `output_data` är en instans av [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py). För `OutputFileDatasetConfig` standard beteendet är att kopiera utdata till `workspaceblobstore` data lagret under sökvägen `/dataset/{run-id}/{output-name}` , där `run-id` är körningens ID och `output-name` är ett automatiskt genererat värde om det inte anges av utvecklaren.

Matrisen `steps` innehåller ett enda element, ett `PythonScriptStep` som kommer att använda data objekt och köras på `compute_target` . Sedan instansierar koden `Pipeline` själva objektet och skickar i matrisen för arbets ytor och steg. Anropet till `experiment.submit(pipeline)` startar körningen av Azure ml-pipeline. Anropet till `wait_for_completion()` block tills pipelinen är slutförd. 

Om du vill veta mer om hur du ansluter din pipeline till dina data kan du läsa artikeln [data åtkomst i Azure Machine Learning](concept-data.md) och [Flytta data till och mellan ml steg för steg (python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Skapa pipelines med designern

Utvecklare som föredrar en visuell design yta kan använda Azure Machine Learning designer för att skapa pipeliner. Du kan komma åt det här verktyget från **Designer** -valet på arbets ytans start sida.  Med designern kan du dra och släppa steg till design ytan. 

När du visuellt utformar pipeliner visas indata och utdata för ett steg på ett synligt sätt. Du kan dra och släppa data anslutningar så att du snabbt kan förstå och ändra data flödet för din pipeline.

![Exempel på Azure Machine Learning designer](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Viktiga fördelar

De främsta fördelarna med att använda pipeliner för dina Machine Learning-arbetsflöden är:

|Nyckel förmån|Description|
|:-------:|-----------|
|**Obevakade &nbsp; körningar**|Schemalägg steg som ska köras parallellt eller i följd på ett tillförlitligt och obevakat sätt. Förberedelse av data och modellering kan senaste dagar eller veckor, och pipelines gör att du kan fokusera på andra uppgifter medan processen körs. |
|**Heterogena-beräkning**|Använd flera pipelines som är tillförlitligt koordinerade över heterogena och skalbara beräknings resurser och lagrings platser. Effektiv användning av tillgängliga beräknings resurser genom att köra enskilda pipeline-steg för olika beräknings mål, till exempel HDInsight, GPU: ar för data vetenskap och Databricks.|
|**Åter användning**|Skapa pipeline-mallar för vissa scenarier, till exempel omskolning och batch-poäng. Utlös publicerade pipelines från externa system via enkla REST-anrop.|
|**Spårning och versions hantering**|I stället för att manuellt spåra data och resultat Sök vägar när du itererar, använder du pipelines SDK för att explicit namnge och version av data källor, indata och utdata. Du kan också hantera skript och data separat för ökad produktivitet.|
| **Modulariteten** | Genom att avgränsa områden och isolera ändringar kan program vara utvecklas snabbare med högre kvalitet. | 
|**Samarbete**|Pipelines gör det möjligt för data experter att samar beta över alla områden i design processen för maskin inlärning, samtidigt som de kan arbeta med pipeline-steg samtidigt.|

## <a name="next-steps"></a>Nästa steg

Azure Machine Learning pipelines är en kraftfull funktion som börjar leverera värde i de tidiga utvecklings faserna. Värdet ökar när teamet och projektet växer. Den här artikeln innehåller förklaras hur pipeliner anges med Azure Machine Learning python SDK och dirigeras på Azure. Du har sett några enkla käll koder och har introducerats för några av de `PipelineStep` klasser som är tillgängliga. Du bör ha en uppfattning om när du ska använda Azure Machine Learning pipelines och hur Azure kör dem. 

+ Lär dig hur du [skapar din första pipeline](./how-to-create-machine-learning-pipelines.md).

+ Lär dig hur du [kör batch-förutsägelser på stora data](tutorial-pipeline-batch-scoring-classification.md ).

+ Se SDK-referens dokument för [pipeline-kärnan](/python/api/azureml-pipeline-core/?preserve-view=true&view=azure-ml-py) och [pipeline-steg](/python/api/azureml-pipeline-steps/?preserve-view=true&view=azure-ml-py).

+ Prova Jupyter Notebooks som demonstrerar [Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Lär dig hur du [Kör antecknings böcker för att utforska den här tjänsten](samples-notebooks.md).