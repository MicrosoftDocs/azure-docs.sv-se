---
title: Använd automatiserad ML i ML-pipelines
titleSuffix: Azure Machine Learning
description: Med AutoMLStep kan du använda Automatisk maskin inlärning i dina pipeliner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 06/15/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c2fc0b0bc1b59bcb3fa4a84235135d9b8ff1fc27
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510257"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Använd automatisk ML i en Azure Machine Learning pipeline i python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Med hjälp av den automatiserade ML-funktionen i Azure Machine Learning kan du upptäcka modeller med höga prestanda utan att du behöver implementera om varje möjlig metod. Tillsammans med Azure Machine Learning pipelines kan du skapa arbets flöden som kan användas för att snabbt identifiera algoritmen som fungerar bäst för dina data. I den här artikeln får du lära dig hur du effektivt ansluter ett steg för förberedelse av data till ett automatiserat ML-steg. Med automatisk ML kan du snabbt upptäcka vilken algoritm som passar bäst för dina data, samtidigt som du lägger på vägen till MLOps och modellens livs cykel driftsättning med pipelines.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En Azure Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).  

* Grundläggande förtrogenhet med Azures [automatiserade funktioner för maskin inlärning](concept-automated-ml.md) och [Machine Learning-pipeline](concept-ml-pipelines.md) och SDK.

## <a name="review-automated-mls-central-classes"></a>Granska automatiserade MLs centrala klasser

Automatiserad ML i en pipeline representeras av ett `AutoMLStep` objekt. `AutoMLStep`Klassen är en underklass till `PipelineStep` . En graf med `PipelineStep` objekt definierar en `Pipeline` .

Det finns flera underklasser för `PipelineStep` . Förutom den `AutoMLStep` här artikeln visas en `PythonScriptStep` för data förberedelse och en annan för att registrera modellen.

Det bästa sättet att flytta data _till_ en ml-pipeline är med `Dataset` objekt. Det bästa sättet att flytta data _mellan_ stegen är med `PipelineData` objekt. För att kunna användas med `AutoMLStep` `PipelineData` måste objektet transformeras till ett- `PipelineOutputTabularDataset` objekt. Mer information finns i [indata och utdata från ml-pipeliner](how-to-move-data-in-out-of-pipelines.md).

`AutoMLStep`Konfigureras via ett- `AutoMLConfig` objekt. `AutoMLConfig` är en flexibel klass som beskrivs i [Konfigurera automatiserade ml-experiment i python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#configure-your-experiment-settings). 

En `Pipeline` körning i en `Experiment` . Pipelinen `Run` har, för varje steg, ett underordnat objekt `StepRun` . Utdata från den automatiserade ML `StepRun` är inlärnings mått och modell med högsta prestanda.

För att göra det, skapar den här artikeln en enkel pipeline för en klassificerings uppgift. Uppgiften förutsäger Titanic överlevnad, men vi diskuterar inte data eller uppgift, förutom vid överföring.

## <a name="get-started"></a>Kom igång

### <a name="retrieve-initial-dataset"></a>Hämta ursprunglig data mängd

Ett ML-arbetsflöde börjar ofta med tidigare befintliga bas linje data. Detta är ett användbart scenario för en registrerad data uppsättning. Data uppsättningar är synliga i arbets ytan, support versions hantering och kan interaktivt utforskas. Det finns många sätt att skapa och fylla i en data uppsättning, enligt beskrivningen i [skapa Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md). Eftersom vi använder python SDK för att skapa vår pipeline använder du SDK för att ladda ned bas linje data och registrera den med namnet titanic_ds.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

Koden loggar först in på Azure Machine Learning arbets ytan som definierats i **config.jspå** (en förklaring finns i [Självstudier: komma igång med att skapa ditt första ml-experiment med python SDK](tutorial-1st-experiment-sdk-setup.md)). Om det inte redan finns en data uppsättning med namnet `'titanic_ds'` registrerad, skapas en. Koden laddar ned CSV-data från webben, använder dem för att instansiera en `TabularDataset` och sedan registrerar data uppsättningen med arbets ytan. Slutligen `Dataset.get_by_name()` tilldelar funktionen `Dataset` till `titanic_ds` . 

### <a name="configure-your-storage-and-compute-target"></a>Konfigurera lagrings-och beräknings mål

Ytterligare resurser som pipelinen behöver är lagrings utrymme och Azure Machine Learning beräknings resurser i allmänhet. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Mellanliggande data mellan förberedelsen av data och det automatiserade ML-steget kan lagras i arbets ytans standard data lager, så vi behöver inte göra mer än anrop till `get_default_datastore()` `Workspace` objektet. 

Därefter kontrollerar koden om AML Compute Target `'cpu-cluster'` redan finns. Annars anger vi att vi vill ha ett litet CPU-baserat beräknings mål. Om du planerar att använda automatiserade ML djup inlärnings funktioner (till exempel text funktionalisering med DNN-stöd) bör du välja en beräkning med stark GPU-support, enligt beskrivningen i [GPU-optimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/sizes-gpu). 

Kod blocken tills målet har allokerats och skriver ut information om det just skapade Compute-målet. Slutligen hämtas det namngivna beräknings målet från arbets ytan och tilldelas `compute_target` . 

### <a name="configure-the-training-run"></a>Konfigurera utbildnings körningen

Nästa steg är att se till att fjärran sluten utbildning har alla beroenden som krävs av inlärnings stegen. Beroenden och körnings kontexten anges genom att skapa och konfigurera ett `RunConfiguration` objekt. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk[automl,explain]', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

Koden ovan visar två alternativ för att hantera beroenden. Som presenteras `USE_CURATED_ENV = True` baseras konfigurationen på en granskad miljö. Granskade miljöer är "förkortade" med vanliga bibliotek som är beroende av varandra och kan vara mycket snabbare att ta online. De granskade miljöerna har färdiga Docker-avbildningar i [Microsoft container Registry](https://hub.docker.com/publishers/microsoftowner). Den angivna sökvägen om du ändrar `USE_CURATED_ENV` till `False` visar mönstret för explicit inställning av beroenden. I det scenariot skapas och registreras en ny anpassad Docker-avbildning i en Azure Container Registry i din resurs grupp (se [Introduktion till privata Docker-behållar register i Azure](https://docs.microsoft.com/azure/container-registry/container-registry-intro)). Det kan ta några minuter att skapa och registrera den här avbildningen. 

## <a name="prepare-data-for-automated-machine-learning"></a>Förbereda data för automatisk maskin inlärning

### <a name="write-the-data-preparation-code"></a>Skriv data förberedelse koden

Data uppsättningen för bas linjen Titanic består av blandade numeriska data och text data, med vissa värden saknas. För att förbereda den för automatisk maskin inlärning kommer steget data förberedelse pipeline att:

- Fyll data som saknas med antingen slumpmässiga data eller en kategori som motsvarar "okänd"
- Transformera kategoriska-data till heltal
- Släpp kolumner som vi inte tänker använda
- Dela data i utbildnings-och test uppsättningar
- Skriv transformerade data till `PipelineData` utmatnings Sök vägar

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

Kodfragmentet ovan är ett fullständigt, men minimalt, exempel på förberedelse av data för Titanic-data. Kodfragmentet börjar med ett Jupyter "Magic Command" för att mata ut koden till en fil. Om du inte använder en Jupyter-anteckningsbok tar du bort den raden och skapar filen manuellt.

De olika `prepare_` funktionerna i ovanstående kodfragment ändrar relevant kolumn i data uppsättningen för indata. Dessa funktioner fungerar på alla data när de har ändrats till ett Pandas- `DataFrame` objekt. I varje fall är data som saknas fyllda med representativa slumpmässiga data eller kategoriska data som indikerar "okänt". Textbaserade kategoriska-data mappas till heltal. Kolumner som inte längre behövs skrivs över eller tas bort. 

När koden definierar förberedelse funktionerna för data, parsar koden indata-argumentet, som är den sökväg som vi vill skriva våra data i. (De här värdena bestäms av de `PipelineData` objekt som kommer att diskuteras i nästa steg.) Koden hämtar den registrerade `'titanic_cs'` `Dataset` , konverterar den till en Pandas `DataFrame` och anropar de olika data förberedelse funktionerna. 

Eftersom `output_path` är fullständigt kvalificerad `os.makedirs()` används funktionen för att förbereda katalog strukturen. I det här läget kan du använda `DataFrame.to_csv()` för att skriva utdata, men Parquet-filer är mer effektiva. Den här effektiviteten skulle förmodligen vara irrelevant med en liten data uppsättning, men användning av **PyArrow** -paketets `from_pandas()` och `write_table()` funktioner är bara några fler tangenttryckningar än `to_csv()` .

Parquet-filer stöds internt av det automatiserade ML-steget som beskrivs nedan, så ingen särskild bearbetning krävs för att använda dem. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Skriv pipeline-steget data förberedelse ( `PythonScriptStep` )

Den data förberedelse kod som beskrivs ovan måste vara kopplad till ett `PythonScripStep` objekt som ska användas med en pipeline. Sökvägen till vilken Parquet data-Preparation-utdata skrivs, genereras av ett- `PipelineData` objekt. Resurserna som för bereddes tidigare, till exempel,, `ComputeTarget` `RunConfig` och `'titanic_ds' Dataset` används för att slutföra specifikationen.

```python
from azureml.pipeline.core import PipelineData
from azureml.pipeline.steps import PythonScriptStep

prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```

`prepped_data_path`Objektet är av typen `PipelineOutputFileDataset` . Observera att den anges i både `arguments` `outputs` argumenten och. Om du granskar föregående steg ser du att i data förberedelse koden är värdet för argumentet `'--output_path'` den fil Sök väg som Parquet-filen skrevs till. 

## <a name="train-with-automlstep"></a>Träna med AutoMLStep

Att konfigurera ett steg med en automatisk ML-pipeline görs med- `AutoMLConfig` klassen. Den här flexibla klassen beskrivs i [Konfigurera automatiserade ml-experiment i python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Data indata och utdata är de enda konfigurations aspekter som kräver särskild uppmärksamhet i en ML-pipeline. Indata och utdata för `AutoMLConfig` i pipelines beskrivs i detalj nedan. Utöver data är en fördel med ML-pipeliner möjligheten att använda olika beräknings mål för olika steg. Du kan välja att bara använda en kraftfullare `ComputeTarget` för den automatiserade ml-processen. Det är lika enkelt som att tilldela en mer kraftfull `RunConfiguration` till `AutoMLConfig` objektets `run_configuration` parameter.

### <a name="send-data-to-automlstep"></a>Skicka data till `AutoMLStep`

I en ML-pipeline måste indata vara ett `Dataset` objekt. Det bästa sättet är att tillhandahålla indata i form av `PipelineOutputTabularDataset` objekt. Du skapar ett objekt av den typen med `parse_parquet_files()` eller `parse_delimited_files()` på en `PipelineOutputFileDataset` , till exempel `prepped_data_path` objektet.

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

I kodfragmentet ovan skapas en högkvalitativ `PipelineOutputTabularDataset` `PipelineOutputFileDataset` åtgärd från utmatningen av steget data förberedelse.

Ett annat alternativ är att använda `Dataset` objekt som registrerats i arbets ytan:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Jämför de två teknikerna:

| Teknik | Förmåner och nack delar | 
|-|-|
|`PipelineOutputTabularDataset`| Högre prestanda | 
|| Naturlig väg från `PipelineData` | 
|| Data har inte sparats efter att pipelinen har körts |
|| [Notebook som visar `PipelineOutputTabularDataset` teknik](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registrerad `Dataset` | Lägre prestanda |
| | Kan genereras på många sätt | 
| | Data sparas och visas i hela arbets ytan |
| | [Antecknings boken visar registrerad `Dataset` teknik](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)

### <a name="specify-automated-ml-outputs"></a>Ange automatiserade ML-utdata

Utdatan i `AutoMLStep` är de slutliga Mät resultaten för modellen med högre prestanda och den modellen. Om du vill använda dessa utdata i ytterligare pipeline-steg förbereder du `PipelineData` objekt för att ta emot dem.

```python
from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                           datastore=datastore,
                           pipeline_output_name='metrics_output',
                           training_output=TrainingOutput(type='Metrics'))
model_data = PipelineData(name='best_model_data',
                           datastore=datastore,
                           pipeline_output_name='model_output',
                           training_output=TrainingOutput(type='Model'))
```

I kodfragmentet ovan skapas de två `PipelineData` objekten för mått och modellens utdata. Varje namn är tilldelat till standard data lagret som hämtades tidigare och som är kopplat till det som finns i `type` `TrainingOutput` `AutoMLStep` . Eftersom vi tilldelar `pipeline_output_name` dessa `PipelineData` objekt kommer deras värden att vara tillgängliga inte bara från det enskilda pipeline-steget, men från pipelinen som helhet, som kommer att diskuteras nedan i avsnittet "undersöka pipeline-resultat". 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Konfigurera och skapa steget automatiserad ML-pipeline

När indata och utdata har definierats är det dags att skapa `AutoMLConfig` och `AutoMLStep` . Informationen om konfigurationen är beroende av din uppgift, enligt beskrivningen i [Konfigurera automatiserade ml-experiment i python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train). Följande fragment visar en enkel konfiguration för aktiviteten Titanic överlevnads klassificering.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
Kodfragmentet visar en idiom som ofta används med `AutoMLConfig` . Argument som är mer flytande (ish) anges i en separat ord lista medan värdena som är mindre sannolika ändras anges direkt i `AutoMLConfig` konstruktorn. I det här fallet `automl_settings` anger du en kort körning: körningen stoppas efter bara 2 iterationer eller 15 minuter, beroende på vilket som kommer först.

`automl_settings`Ord listan skickas till `AutoMLConfig` konstruktorn som kwargs. De andra parametrarna är inte komplexa:

- `task` är inställt på `classification` för det här exemplet. Andra giltiga värden är `regression` och `forecasting`
- `path` och `debug_log` beskriver sökvägen till projektet och en lokal fil som felsöknings information skrivs till 
- `compute_target` är den tidigare definierade `compute_target` att, i det här exemplet är en billig CPU-baserad dator. Om du använder AutoML djup inlärnings funktioner vill du ändra beräknings målet till att vara GPU-baserat
- `featurization` är inställt på `auto` . Mer information finns i avsnittet [data funktionalisering](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#data-featurization) i konfigurations dokumentet för AUTOMATISERAd ml 
- `training_data` är inställt på objekt som har `PipelineOutputTabularDataset` skapats från utdata för steget förberedelse av data 
- `label_column_name` anger vilken kolumn vi är intresserade av för förutsägelse 

`AutoMLStep`Själva tar `AutoMLConfig` och har, som utdata, de objekt som har `PipelineData` skapats för att lagra mått och modell data. 

>[!Important]
> Du måste ange `enable_default_model_output` och `enable_default_metrics_output` `True` endast om du använder  `AutoMLStepRun` .

I det här exemplet utför den automatiserade ML-processen kors valideringar på `training_data` . Du kan styra antalet kors valideringar med `n_cross_validations` argumentet. Om du redan har delat upp dina utbildnings data som en del av stegen för förberedelse av data, kan du ställa in `validation_data` på egen hand `Dataset` .

Ibland kan du se användning `X` av data funktioner och `y` data etiketter. Den här tekniken är föråldrad och du bör använda `training_data` för ininformation. 

## <a name="register-the-model-generated-by-automated-ml"></a>Registrera modellen som genererats av automatisk ML 

Det sista steget i en Basic ML-pipeline registrerar den skapade modellen. Genom att lägga till modellen i arbets ytans modell register, är den tillgänglig i portalen och kan användas med en version. Registrera modellen genom att skriva en annan `PythonScriptStep` som tar `model_data` utdata från `AutoMLStep` .

### <a name="write-the-code-to-register-the-model"></a>Skriv koden för att registrera modellen

En modell registreras i en `Workspace` . Du är förmodligen bekant med `Workspace.from_config()` att använda för att logga in på din arbets yta på din lokala dator, men det finns ett annat sätt att hämta arbets ytan från en pågående ml-pipeline. `Run.get_context()`Hämtar aktiv `Run` . Det här `run` objektet ger åtkomst till många viktiga objekt, inklusive det `Workspace` som används här.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>Skriv PythonScriptStep-koden

I modell registreringen `PythonScriptStep` används en `PipelineParameter` för ett av dess argument. Pipeline-parametrar är argument till pipelines som enkelt kan ställas in vid körnings tillfället. När de har deklarerats skickas de som vanliga argument. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Skapa och kör din automatiserade ML-pipeline

Att skapa och köra en pipeline som innehåller en `AutoMLStep` är inte en annan än en normal pipeline. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

Koden ovan kombinerar stegen förberedelse av data, automatiserad ML och modell registrering i ett `Pipeline` objekt. Sedan skapas ett- `Experiment` objekt. `Experiment`Konstruktorn hämtar det namngivna experimentet om det finns eller skapar det om det behövs. Den skickas `Pipeline` till `Experiment` och skapar ett `Run` objekt som asynkront kör pipelinen. `wait_for_completion()`Funktionen blockerar tills körningen är klar.

### <a name="examine-pipeline-results"></a>Granska resultat från pipelinen 

När `run` du är klar kan du hämta `PipelineData` objekt som har tilldelats en `pipeline_output_name` . Du kan hämta resultaten och läsa in dem för vidare bearbetning.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Hämtade filer skrivs till under katalogen `azureml/{run.id}/` . Mått filen är JSON-formaterad och kan konverteras till en Pandas-dataframe för undersökning.

För lokal bearbetning kan du behöva installera relevanta paket, till exempel Pandas, Pickle, AzureML SDK och så vidare. I det här exemplet är det troligt att den bästa modellen som hittas av automatisk ML är beroende av XGBoost.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

Kodfragmentet ovan visar vilken mått fil som läses in från dess plats i Azure-datalagret. Du kan också läsa in den från den hämtade filen, som du ser i kommentaren. När du har deserialiserat den och konverterat den till en Pandas-DataFrame kan du se detaljerade mått för varje iteration av det automatiserade ML-steget.

Modell filen kan avserialiseras till ett `Model` objekt som du kan använda för inferencing, ytterligare mått analys och så vidare. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Mer information om hur du läser in och arbetar med befintliga modeller finns i [använda en befintlig modell med Azure Machine Learning](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Hämta resultatet av en automatisk ML-körning

Om du har följt anvisningarna i artikeln har du ett instansierat `run` objekt. Men du kan också hämta slutförda `Run` objekt från `Workspace` ett `Experiment` objekts sätt.

Arbets ytan innehåller en fullständig post för alla experiment och-körningar. Du kan antingen använda portalen för att hitta och hämta utdata från experiment eller använda kod. Använd Azure Machine Learning för att hitta ID: t för den körning som du är intresse rad av för att få åtkomst till posterna från en historisk körning. Med det ID: t kan du välja den information `run` som finns i `Workspace` och `Experiment` .

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Du skulle behöva ändra strängarna i ovanstående kod till information om din historiska körning. Kodfragmentet ovan förutsätter att du har tilldelat dig det som är `ws` relevant i `Workspace` Normal `from_config()` . Experimentet som är intresse rad hämtas direkt och koden påträffar `Run` av sitt intresse genom att matcha `run.id` värdet.

När du har ett `Run` -objekt kan du hämta mått och modell. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Varje- `Run` objekt innehåller `StepRun` objekt som innehåller information om det enskilda pipeline-steget körs. `run`Söker efter `StepRun` objektet för `AutoMLStep` . Måtten och modellen hämtas med hjälp av sina standard namn, som är tillgängliga även om du inte skickar `PipelineData` objekt till- `outputs` parametern för `AutoMLStep` . 

Slutligen hämtas faktiska mått och modell till din lokala dator, enligt beskrivningen i avsnittet "Undersök resultat från pipelines" ovan.

## <a name="next-steps"></a>Efterföljande moment

- Kör den här Jupyter Notebook som visar ett [komplett exempel på automatiserad ml i en pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) som använder regression för att förutsäga taxi-priser
- [Skapa automatiserade ML-experiment utan att skriva kod](how-to-use-automated-ml-for-ml-models.md)
- Utforska en mängd olika [Jupyter-anteckningsböcker som demonstrerar automatiserade ml](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Läs om hur du integrerar din pipeline i till [slutpunkt-till-slutpunkt-MLOps](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#automate-the-ml-lifecycle) eller undersöker [MLOps GitHub-lagringsplatsen](https://github.com/Microsoft/MLOpspython) 
