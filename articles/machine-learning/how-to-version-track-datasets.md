---
title: Versions hantering för data uppsättning
titleSuffix: Azure Machine Learning
description: Lär dig mer om att versioner av Machine Learning-datauppsättningar och hur versioner fungerar med maskin inlärnings pipeliner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: fde25e4ba75bfb86c9837582d7168f85335836b6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520548"
---
# <a name="version-and-track-azure-machine-learning-datasets"></a>Version och spårning Azure Machine Learning data uppsättningar

I den här artikeln får du lära dig hur du använder version och spår Azure Machine Learning data uppsättningar för reproducerbarhet. Versions hantering av data uppsättningar är ett sätt att ange data uppsättningens tillstånd så att du kan använda en angiven version av data uppsättningen för framtida experiment.

Scenarier för typiska versioner:

* När nya data är tillgängliga för omskolning
* När du använder olika metoder för data förberedelse eller funktions teknik

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för den här självstudien:

- [Azure Machine Learning SDK för python installerat](/python/api/overview/azure/ml/install). Det här SDK: t innehåller paketet [azureml-DataSets](/python/api/azureml-core/azureml.core.dataset) .
    
- En [Azure Machine Learning-arbetsyta](concept-workspace.md). Hämta ett befintligt namn genom att köra följande kod eller [skapa en ny arbets yta](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- En [Azure Machine Learning data uppsättning](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrera och hämta data uppsättnings versioner

Genom att registrera en data uppsättning kan du version, åter användning och dela den över experiment och med kollegor. Du kan registrera flera data uppsättningar med samma namn och hämta en speciell version efter namn och versions nummer.

### <a name="register-a-dataset-version"></a>Registrera en data uppsättnings version

Följande kod registrerar en ny version av `titanic_ds` data uppsättningen genom `create_new_version` att ange parametern till `True` . Om det inte finns någon befintlig `titanic_ds` data uppsättning registrerad i arbets ytan skapar koden en ny data uppsättning med namnet `titanic_ds` och anger dess version till 1.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Hämta en data uppsättning efter namn

Som standard returnerar metoden [get_by_name ()](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) i `Dataset` klassen den senaste versionen av data uppsättningen som är registrerad i arbets ytan. 

Följande kod hämtar version 1 av `titanic_ds` data uppsättningen.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Metod tips för versions hantering

När du skapar en data uppsättnings version skapar du *inte* en extra kopia av data med arbets ytan. Eftersom data uppsättningar är referenser till data i din lagrings tjänst har du en enda källa för sanningen, som hanteras av din lagrings tjänst.

>[!IMPORTANT]
> Om data som refereras till av data mängden skrivs över eller tas bort, återställs *inte* ändringen om du anropar en angiven version av data uppsättningen.

När du läser in data från en data uppsättning laddas alltid det aktuella data innehåll som refereras till av data uppsättningen. Om du vill se till att varje data uppsättnings version är reproducerbar, rekommenderar vi att du inte ändrar data innehåll som data uppsättnings versionen refererar till. När nya data kommer in, sparar du nya datafiler i en separat datamapp och skapar sedan en ny data uppsättnings version som innehåller data från den nya mappen.

Följande bild och exempel kod visar det rekommenderade sättet att strukturera datamapparna och skapa data uppsättnings versioner som refererar till dessa mappar:

![Mappstruktur](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>Version en ML datauppsättning för pipeline-utdata

Du kan använda en data uppsättning som indata och utdata för varje steg i [ml pipeline](concept-ml-pipelines.md) . När du kör pipelines igen registreras utdata för varje pipeline-steg som en ny data uppsättnings version.

ML-pipeliner fyller i resultatet för varje steg i en ny mapp varje gång pipelinen återkör. Med det här beteendet kan data uppsättningarna som skapas av data vara reproducerbara. Läs mer om [data uppsättningar i pipelines](./how-to-create-machine-learning-pipelines.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>Spåra data i experimenten

Azure Machine Learning spårar dina data i experimentet som indata och utdata.  

Här följer några scenarier där dina data spåras som en **indata-datauppsättning**. 

* Som ett `DatasetConsumptionConfig` objekt via antingen- `inputs` eller- `arguments` parametern för ditt `ScriptRunConfig` objekt när du skickar experimentet. 

* När metoder som, som get_by_name () eller get_by_id () anropas i skriptet. I det här scenariot visas namnet som tilldelats data uppsättningen när du registrerade den till arbets ytan. 

Här följer några scenarier där dina data spåras som en **data uppsättning för utdata**.  

* Skicka ett `OutputFileDatasetConfig` objekt via antingen `outputs` parametern eller `arguments` när du skickar en experiment körning. `OutputFileDatasetConfig` objekt kan också användas för att bevara data mellan pipeline-steg. Se [Flytta data mellan ml steg för pipeline.](how-to-move-data-in-out-of-pipelines.md)
  
* Registrera en data uppsättning i skriptet. I det här scenariot visas namnet som tilldelats data uppsättningen när du registrerade den till arbets ytan. I följande exempel `training_ds` är det namnet som skulle visas.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Skicka underordnad körning med en oregistrerad data uppsättning i skriptet. Detta resulterar i en anonym Sparad data uppsättning.

### <a name="trace-datasets-in-experiment-runs"></a>Spåra data uppsättningar i experiment körningar

För varje Machine Learning experiment kan du enkelt spåra data uppsättningarna som används som indata med experiment- `Run` objektet.

I följande kod används [`get_details()`](/python/api/azureml-core/azureml.core.run.run#get-details--) metoden för att spåra vilka indata-datauppsättningar som användes när experimentet kördes:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Du kan också hitta `input_datasets` från experiment med hjälp av [Azure Machine Learning Studio](). 

Följande bild visar var du hittar indata-datauppsättningen för ett experiment på Azure Machine Learning Studio. I det här exemplet går du till fönstret **experiment** och öppnar fliken **Egenskaper** för en speciell körning av experimentet `keras-mnist` .

![Indata-datauppsättningar](./media/how-to-version-track-datasets/input-datasets.png)

Använd följande kod för att registrera modeller med data uppsättningar:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Efter registreringen kan du se en lista över modeller som registrerats med data uppsättningen med hjälp av python eller gå till [Studio](https://ml.azure.com/).

Följande vy är från fönstret **data uppsättningar** under **till gångar**. Välj data uppsättningen och välj sedan fliken **modeller** för en lista med de modeller som är registrerade med data uppsättningen. 

![Data uppsättnings modeller för indata](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Nästa steg

* [Träna med datauppsättningar](how-to-train-with-datasets.md)
* [Fler exempel på data uppsättnings antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)