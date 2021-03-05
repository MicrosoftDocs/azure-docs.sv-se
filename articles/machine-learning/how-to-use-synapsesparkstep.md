---
title: Använda Apache Spark i en Machine Learning-pipeline (för hands version)
titleSuffix: Azure Machine Learning
description: Länka din Azure Synapse Analytics-arbetsyta till din Azure Machine Learning-pipeline för att använda Apache Spark för data behandling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f52686f991e3d14a8cde82c602b182874305f27d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184108"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Använda Apache Spark (drivs av Azure Synapse Analytics) i din Machine Learning pipeline (för hands version)

I den här artikeln får du lära dig hur du använder Apache Spark pooler som drivs av Azure Synapse Analytics som beräknings mål för steget förberedelse av data i en Azure Machine Learning pipeline. Du får lära dig hur en enskild pipeline kan använda beräknings resurser som passar för det aktuella steget, till exempel data förberedelse eller utbildning. Du ser hur data förbereds för Spark-steget och hur de skickas till nästa steg. 

## <a name="prerequisites"></a>Förutsättningar

* Skapa en [Azure Machine Learning arbets yta](how-to-manage-workspace.md) för att lagra alla dina pipeline-resurser.

* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning beräknings instans](concept-compute-instance.md) med SDK redan installerad.

* Skapa en Azure Synapse Analytics-arbetsyta och Apache Spark pool (se [snabb start: skapa en server lös Apache Spark pool med Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Länka din Azure Machine Learning arbets yta och Azure Synapse Analytics-arbetsytan 

Du skapar och administrerar Apache Spark pooler i en Azure Synapse Analytics-arbetsyta. Om du vill integrera en Apache Spark pool med en Azure Machine Learning arbets yta måste du länka till Azure Synapse Analytics-arbetsytan. 

Du kan ansluta en Apache Spark pool via användar gränssnittet för Azure Machine Learning Studio med hjälp av sidan **länkade tjänster** . Du kan också göra det via **beräknings** sidan med alternativet **bifoga beräkning** .

Du kan också bifoga en Apache Spark pool via SDK (enligt nedan) eller via en ARM-mall (se den här [exempel arm-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)). 

Du kan använda kommando raden för att följa ARM-mallen, lägga till den länkade tjänsten och bifoga Apache Spark-poolen med följande kod:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> För att du ska kunna länka till Azure Synapse Analytics-arbetsytan måste du ha ägar rollen i Azure Synapse Analytics-arbetsytans resurs. Kontrol lera åtkomsten i Azure Portal.
> Den länkade tjänsten får en systemtilldelad identitet (SAI) när du skapar den. Du måste tilldela den här länk tjänsten SAI rollen "Synapse Apache Spark Administrator" från Synapse Studio så att den kan skicka Spark-jobbet (se [Hantera SYNAPSE RBAC-roll tilldelningar i Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Du måste också ge användaren Azure Machine Learning arbets ytan roll "deltagare" från Azure Portal av resurs Management.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Skapa eller Hämta länken mellan din Azure Synapse Analytics-arbetsyta och din Azure Machine Learning-arbetsyta

Du kan hämta länkade tjänster i din arbets yta med kod som:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Börja `Workspace.from_config()` med att komma åt din Azure Machine Learning-arbetsyta med hjälp av konfigurationen i `config.json` (se [Självstudier: komma igång med Azure Machine Learning i utvecklings miljön](tutorial-1st-experiment-sdk-setup-local.md)). Sedan skriver koden ut alla länkade tjänster som är tillgängliga i arbets ytan. Slutligen `LinkedService.get()` hämtar en länkad tjänst med namnet `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Koppla din Apache Spark-pool som ett beräknings mål för Azure Machine Learning

Om du vill använda din Apache Spark-pool för att sätta ett steg i din Machine Learning-pipeline måste du koppla den som en `ComputeTarget` för pipeline-steget, som du ser i följande kod.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(
        linked_service = linked_service,
        type="SynapseSpark",
        pool_name="spark01") # This name comes from your Synapse workspace

synapse_compute=ComputeTarget.attach(
        workspace=ws,
        name='link1-spark01',
        attach_configuration=attach_config)

synapse_compute.wait_for_completion()
```

Det första steget är att konfigurera `SynapseCompute` . `linked_service`Argumentet är det `LinkedService` objekt som du skapade eller hämtade i föregående steg. `type`Argumentet måste vara `SynapseSpark` . `pool_name`Argumentet i `SynapseCompute.attach_configuration()` måste matcha det med en befintlig pool i din Azure Synapse Analytics-arbetsyta. Mer information om hur du skapar en Apache Spark-pool i Azure Synapse Analytics-arbetsytan finns i [snabb start: skapa en server lös Apache Spark pool med Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md). Typen av `attach_config` är `ComputeTargetAttachConfiguration` .

När konfigurationen har skapats skapar du en maskin inlärning `ComputeTarget` genom att skicka in `Workspace` , `ComputeTargetAttachConfiguration` och det namn som du vill referera till i beräkningen i Machine Learning-arbetsytan. Anropet till `ComputeTarget.attach()` är asynkront, så exempel blocken tills anropet har slutförts.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Skapa en `SynapseSparkStep` som använder den länkade Apache Spark poolen

Exempel jobbet för notebook [Spark på Apache Spark-poolen](https://github.com/azure/machinelearningnotebooks) definierar en enkel Machine Learning-pipeline. Först definierar antecknings boken ett data förberedelse steg som drivs av den som `synapse_compute` definierades i föregående steg. Antecknings boken definierar sedan ett utbildnings steg som drivs av ett beräknings mål som passar bättre för träning. I exempel antecknings boken används Titanic överlevnads databas för att demonstrera indata och utdata. den rensar egentligen inte data eller skapar en förutsägelse modell. Eftersom det inte finns någon verklig utbildning i det här exemplet använder inlärnings steget en billig, PROCESSORbaserade beräknings resurs.

Data flödar till en Machine Learning-pipeline med hjälp av `DatasetConsumptionConfig` objekt, som kan innehålla tabell data eller uppsättningar av filer. Data kommer ofta från filer i Blob Storage i data lagret för en arbets yta. Följande kod visar en typisk kod för att skapa indatatyper för en Machine Learning-pipeline:

```python
from azureml.core import Dataset

datastore = ws.get_default_datastore()
file_name = 'Titanic.csv'

titanic_tabular_dataset = Dataset.Tabular.from_delimited_files(path=[(datastore, file_name)])
step1_input1 = titanic_tabular_dataset.as_named_input("tabular_input")

# Example only: it wouldn't make sense to duplicate input data, especially one as tabular and the other as files
titanic_file_dataset = Dataset.File.from_files(path=[(datastore, file_name)])
step1_input2 = titanic_file_dataset.as_named_input("file_input").as_hdfs()
```

Koden ovan förutsätter att filen `Titanic.csv` är i Blob Storage. Koden visar hur du läser filen som en `TabularDataset` och en `FileDataset` . Den här koden är enbart i demonstrations syfte, eftersom det skulle vara förvirrande att duplicera indata eller för att tolka en enskild data källa som både en tabell som innehåller en resurs och precis som en fil.

> [!Important]
> `FileDataset`Din `azureml-core` version måste vara minst för att det ska gå att använda en as-indatamängd `1.20.0` . Hur du anger detta med hjälp av `Environment` klassen beskrivs nedan.

När ett steg är klart kan du välja att lagra utdata med hjälp av kod som liknar:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

I det här fallet lagras data i `datastore` i en fil som kallas `test` och är tillgängliga i Machine Learning-arbetsytan som en `Dataset` med namnet `registered_dataset` .

Förutom data kan ett pipeline-steg ha svar på python-beroenden per steg. Enskilda `SynapseSparkStep` objekt kan också ange exakt Apache Spark konfiguration för Azure-Synapse. Detta visas i följande kod, som anger att `azureml-core` paket versionen måste vara minst `1.20.0` . (Som tidigare nämnts krävs detta krav för `azureml-core` att använda en `FileDataset` som inmatad.)

```python
from azureml.core.environment import Environment
from azureml.pipeline.steps import SynapseSparkStep

env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core>=1.20.0")

step_1 = SynapseSparkStep(name = 'synapse-spark',
                          file = 'dataprep.py',
                          source_directory="./code", 
                          inputs=[step1_input1, step1_input2],
                          outputs=[step1_output],
                          arguments = ["--tabular_input", step1_input1, 
                                       "--file_input", step1_input2,
                                       "--output_dir", step1_output],
                          compute_target = 'link1-spark01',
                          driver_memory = "7g",
                          driver_cores = 4,
                          executor_memory = "7g",
                          executor_cores = 2,
                          num_executors = 1,
                          environment = env)
```

Koden ovan anger ett enda steg i Azure Machine Learning-pipeline. Det här steget `environment` anger en angiven `azureml-core` version och kan lägga till andra Conda-eller pip-beroenden vid behov.

`SynapseSparkStep`Zip och upload överförs från den lokala datorn till under katalogen `./code` . Katalogen kommer att återskapas på Compute-servern och steget kommer att köra filen `dataprep.py` från den katalogen. `inputs`Och `outputs` i det steget är de `step1_input1` , `step1_input2` och objekt som `step1_output` tidigare diskuterats. Det enklaste sättet att komma åt dessa värden i `dataprep.py` skriptet är att associera dem med namnet `arguments` .

Nästa uppsättning argument för `SynapseSparkStep` konstruktorn kontroll Apache Spark. `compute_target`Är det `'link1-spark01'` som vi kopplade till som ett beräknings mål tidigare. De andra parametrarna anger vilka minnen och kärnor som vi vill använda.

I exempel antecknings boken används följande kod för `dataprep.py` :

```python
import os
import sys
import azureml.core
from pyspark.sql import SparkSession
from azureml.core import Run, Dataset

print(azureml.core.VERSION)
print(os.environ)

import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--tabular_input")
parser.add_argument("--file_input")
parser.add_argument("--output_dir")
args = parser.parse_args()

# use dataset sdk to read tabular dataset
run_context = Run.get_context()
dataset = Dataset.get_by_id(run_context.experiment.workspace,id=args.tabular_input)
sdf = dataset.to_spark_dataframe()
sdf.show()

# use hdfs path to read file dataset
spark= SparkSession.builder.getOrCreate()
sdf = spark.read.option("header", "true").csv(args.file_input)
sdf.show()

sdf.coalesce(1).write\
.option("header", "true")\
.mode("append")\
.csv(args.output_dir)
```

Detta "förberedelse av data"-skriptet gör ingen riktig datatransformering, men visar hur du hämtar data, konverterar dem till en spark-dataframe och hur du utför vissa grundläggande Apache Spark modifieringar. Du kan hitta utdata i Azure Machine Learning Studio genom att öppna den underordnade körningen, välja fliken **utdata + loggar** och öppna `logs/azureml/driver/stdout` filen, som du ser i följande bild.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Skärm bild av Studio som visar STDOUT-fliken för underordnad körning":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Använda `SynapseSparkStep` i en pipeline

Andra steg i pipelinen kan ha sina egna unika miljöer och köras på olika beräknings resurser som är lämpliga för den aktuella aktiviteten. Exempel antecknings boken kör "övnings steg" i ett litet CPU-kluster:

```python
from azureml.core.compute import AmlCompute

cpu_cluster_name = "cpucluster"

if cpu_cluster_name in ws.compute_targets:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
else:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', max_nodes=1)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    print('Allocating new CPU compute cluster')

cpu_cluster.wait_for_completion(show_output=True)

step2_input = step1_output.as_input("step2_input").as_download()

step_2 = PythonScriptStep(script_name="train.py",
                          arguments=[step2_input],
                          inputs=[step2_input],
                          compute_target=cpu_cluster_name,
                          source_directory="./code",
                          allow_reuse=False)
```

Koden ovan skapar den nya beräknings resursen om det behövs. Sedan `step1_output` konverteras resultatet till inmatat i övnings steget. `as_download()`Alternativet innebär att data kommer att flyttas till beräknings resursen, vilket leder till snabbare åtkomst. Om informationen var så stor att den inte kunde få plats på den lokala beräknings hård disken använder du `as_mount()` alternativet för att strömma data via det säkra fil systemet. Det `compute_target` andra steget är `'cpucluster'` , inte den `'link1-spark01'` resurs som du använde i steget förberedelse av data. Det här steget använder ett enkelt program i `train.py` stället för det `dataprep.py` du använde i föregående steg. Du kan se information om `train.py` i exempel antecknings boken.

När du har definierat alla steg kan du skapa och köra din pipeline. 

```
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Ovanstående kod skapar en pipeline som består av steget förberedelse av data på Apache Spark pooler som drivs av Azure Synapse Analytics ( `step_1` ) och övnings steg ( `step_2` ). Azure beräknar körnings diagrammet genom att undersöka data beroenden mellan stegen. I det här fallet finns det bara ett enkelt beroende som `step2_input` kräver det `step1_output` .

Anropet till `pipeline.submit` skapar, om det behövs, ett experiment som anropas `synapse-pipeline` och påbörjar ett asynkront körnings försök i det. Enskilda steg i pipelinen körs som underordnade körningar av den här huvud körningen och kan övervakas och granskas på sidan experiment i Studio.

## <a name="next-steps"></a>Nästa steg

* [Publicera och spåra maskin inlärnings pipeliner](how-to-deploy-pipelines.md) 
* [Övervaka Azure Machine Learning](monitor-azure-machine-learning.md)
* [Använd automatisk ML i en Azure Machine Learning pipeline i python](how-to-use-automlstep-in-pipelines.md)
