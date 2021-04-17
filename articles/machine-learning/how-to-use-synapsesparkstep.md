---
title: Använda Apache Spark i en pipeline för maskininlärning (förhandsversion)
titleSuffix: Azure Machine Learning
description: Länka din Azure Synapse Analytics till din Azure Machine Learning-pipeline för att använda Apache Spark för datamanipulering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, synapse-azureml
ms.openlocfilehash: 2c69ec853cdeeed6f9e28fb9f2884053580ce08e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576388"
---
# <a name="how-to-use-apache-spark-powered-by-azure-synapse-analytics-in-your-machine-learning-pipeline-preview"></a>Så här använder du Apache Spark (drivs av Azure Synapse Analytics) i din maskininlärningspipeline (förhandsversion)

I den här artikeln får du lära dig hur du använder Apache Spark-pooler som drivs av Azure Synapse Analytics som beräkningsmål för ett dataförberedelsesteg i en Azure Machine Learning pipeline. Du får lära dig hur en enda pipeline kan använda beräkningsresurser som passar för det specifika steget, till exempel förberedelse av data eller träning. Du får se hur data förbereds för Spark-steget och hur de skickas till nästa steg. 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Förutsättningar

* Skapa en [Azure Machine Learning för](how-to-manage-workspace.md) att innehålla alla dina pipelineresurser.

* [Konfigurera utvecklingsmiljön för](how-to-configure-environment.md) att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning-beräkningsinstans](concept-compute-instance.md) med SDK:n redan installerad.

* Skapa en Azure Synapse Analytics arbetsyta Apache Spark en pool (se [Snabbstart: Skapa en serverlös Apache Spark-pool med Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)). 

## <a name="link-your-azure-machine-learning-workspace-and-azure-synapse-analytics-workspace"></a>Länka din Azure Machine Learning arbetsyta och Azure Synapse Analytics arbetsyta 

Du skapar och administrerar dina Apache Spark i en Azure Synapse Analytics arbetsyta. Om du vill Apache Spark en pool Azure Machine Learning en arbetsyta måste du [länka till Azure Synapse Analytics arbetsyta](how-to-link-synapse-ml-workspaces.md). 

Du kan koppla en Apache Spark-pool via Azure Machine Learning-studio användargränssnitt med hjälp av **sidan Länkade** tjänster. Du kan också göra det via **sidan Beräkning** med **alternativet Bifoga** beräkning.

Du kan också koppla en Apache Spark-pool via SDK (se nedan) eller via en ARM-mall (se den här [ARM-exempelmallen).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json) 

Du kan använda kommandoraden för att följa ARM-mallen, lägga till den länkade tjänsten och koppla Apache Spark poolen med följande kod:

```bash
az deployment group create --name --resource-group <rg_name> --template-file "azuredeploy.json" --parameters @"azuredeploy.parameters.json"
```

> [!Important]
> Om du vill länka Azure Synapse Analytics arbetsytan måste du ha rollen Ägare i Azure Synapse Analytics arbetsytan. Kontrollera din åtkomst i Azure Portal.
> Den länkade tjänsten får en system tilldelad identitet (SAI) när du skapar den. Du måste tilldela den här länktjänsten SAI rollen "Synapse Apache Spark administrator" från Synapse Studio så att den kan skicka Spark-jobbet (se Hantera [Synapse RBAC-rolltilldelningar i Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)). Du måste också ge användaren av Azure Machine Learning rollen "Deltagare" från Azure Portal av resurshantering.

## <a name="create-or-retrieve-the-link-between-your-azure-synapse-analytics-workspace-and-your-azure-machine-learning-workspace"></a>Skapa eller hämta länken mellan din Azure Synapse Analytics och din Azure Machine Learning arbetsyta

Du kan hämta länkade tjänster på arbetsytan med kod som:

```python
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

ws = Workspace.from_config()

for service in LinkedService.list(ws) : 
    print(f"Service: {service}")

# Retrieve a known linked service
linked_service = LinkedService.get(ws, 'synapselink1')
```

Först kommer du åt din Azure Machine Learning med hjälp av konfigurationen `Workspace.from_config()` `config.json` i (se [Självstudie: Komma igång med Azure Machine Learning i din utvecklingsmiljö).](tutorial-1st-experiment-sdk-setup-local.md) Sedan skriver koden ut alla länkade tjänster som är tillgängliga i arbetsytan. Slutligen hämtar `LinkedService.get()` en länkad tjänst med namnet `'synapselink1'` . 

## <a name="attach-your-apache-spark-pool-as-a-compute-target-for-azure-machine-learning"></a>Koppla apache spark-poolen som ett beräkningsmål för Azure Machine Learning

Om du vill använda Apache Spark-poolen för att driva ett steg i din maskininlärningspipeline måste du koppla den som en `ComputeTarget` för pipelinesteget, enligt följande kod.

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

Det första steget är att konfigurera `SynapseCompute` . Argumentet `linked_service` är det objekt som du skapade eller hämtade i föregående `LinkedService` steg. Argumentet `type` måste vara `SynapseSpark` . Argumentet `pool_name` i måste matcha det för en befintlig pool i din Azure Synapse Analytics `SynapseCompute.attach_configuration()` arbetsyta. Mer information om hur du skapar en Apache Spark-pool i Azure Synapse Analytics-arbetsytan finns i [Snabbstart: Skapa en serverlös Apache Spark-pool](../synapse-analytics/quickstart-create-apache-spark-pool-studio.md)med Synapse Studio . Typen av `attach_config` är `ComputeTargetAttachConfiguration` .

När konfigurationen har skapats skapar du en maskininlärning genom att skicka in , och namnet som du vill referera till beräkningen i machine `ComputeTarget` `Workspace` `ComputeTargetAttachConfiguration` learning-arbetsytan. Anropet `ComputeTarget.attach()` till är asynkront, så exemplet blockerar tills anropet har slutförts.

## <a name="create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool"></a>Skapa en `SynapseSparkStep` som använder den länkade Apache Spark poolen

Spark-exempeljobbet [i Apache Spark-poolen definierar](https://github.com/azure/machinelearningnotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_job_on_synapse_spark_pool.ipynb) en enkel maskininlärningspipeline. Först definierar notebook-datorn ett dataförberedelsesteg som `synapse_compute` drivs av det som definierades i föregående steg. Sedan definierar notebook-datorn ett träningssteg som drivs av ett beräkningsmål som passar bättre för träning. Exempelanteckningsboken använder Titanics överlevnadsdatabas för att demonstrera indata och utdata. Den rensar inte data eller gör en förutsägelsemodell. Eftersom det inte finns någon verklig träning i det här exemplet använder träningssteget en kostnadseffektiv processorbaserad beräkningsresurs.

Data flödar till en maskininlärningspipeline `DatasetConsumptionConfig` via objekt, som kan innehålla tabelldata eller filuppsättningar. Data kommer ofta från filer i bloblagring i en arbetsytas datalager. Följande kod visar några vanliga kod för att skapa indata för en maskininlärningspipeline:

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

Koden ovan förutsätter att filen finns `Titanic.csv` i Blob Storage. Koden visar hur du läser filen som en `TabularDataset` och som en `FileDataset` . Den här koden är endast i demonstrationssyfte, eftersom det skulle vara förvirrande att duplicera indata eller tolka en enda datakälla som både en tabellinnehållande resurs och precis som en fil.

> [!Important]
> För att kunna använda `FileDataset` en som indata `azureml-core` måste din version vara minst `1.20.0` . Hur du anger detta med `Environment` klassen beskrivs nedan.

När ett steg är klart kan du välja att lagra utdata med hjälp av kod som liknar följande:

```python
from azureml.data import HDFSOutputDatasetConfig
step1_output = HDFSOutputDatasetConfig(destination=(datastore,"test")).register_on_complete(name="registered_dataset")
```

I det här fallet skulle data lagras i i en fil med namnet och vara tillgängliga i `datastore` `test` machine learning-arbetsytan `Dataset` som en med namnet `registered_dataset` .

Förutom data kan ett pipelinesteg ha Python-beroenden per steg. Enskilda `SynapseSparkStep` objekt kan även Azure Synapse Apache Spark exakt konfiguration. Detta visas i följande kod, som anger att `azureml-core` paketversionen måste vara minst `1.20.0` . (Som tidigare nämnts krävs det här `azureml-core` kravet för för att använda en som `FileDataset` indata.)

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

Koden ovan anger ett enda steg i Azure Machine Learning-pipelinen. Det här steget anger `environment` en specifik version och kan lägga till andra `azureml-core` conda- eller pip-beroenden efter behov.

`SynapseSparkStep`zippa och ladda upp underkatalogen från den lokala `./code` datorn. Katalogen återskapas på beräkningsservern och steget kör filen `dataprep.py` från den katalogen. Och `inputs` i det steget är `outputs` `step1_input1` objekten , och som `step1_input2` `step1_output` diskuterats tidigare. Det enklaste sättet att komma åt dessa värden i `dataprep.py` skriptet är att associera dem med med namnet `arguments` .

Nästa uppsättning argument till `SynapseSparkStep` konstruktorkontrollen Apache Spark. `compute_target`är den som vi kopplade som ett `'link1-spark01'` beräkningsmål tidigare. De andra parametrarna anger det minne och de kärnor som vi vill använda.

Exempelanteckningsboken använder följande kod för `dataprep.py` :

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

Det här skriptet för förberedelse av data gör ingen verklig datatransformering, men illustrerar hur du hämtar data, konverterar dem till en Spark-dataram och hur du gör vissa grundläggande Apache Spark manipulering. Du hittar utdata i Azure Machine Learning Studio genom att öppna den underordnade körningen, välja fliken **Utdata +** loggar och öppna filen, som du ser `logs/azureml/driver/stdout` i följande bild.

:::image type="content" source="media/how-to-use-synapsesparkstep/synapsesparkstep-stdout.png" alt-text="Skärmbild av Studio som visar stdout-fliken för underordnad körning":::

## <a name="use-the-synapsesparkstep-in-a-pipeline"></a>Använda `SynapseSparkStep` i en pipeline

I följande exempel används utdata från `SynapseSparkStep` som skapades i föregående [avsnitt](#create-a-synapsesparkstep-that-uses-the-linked-apache-spark-pool). Andra steg i pipelinen kan ha sina egna unika miljöer och köras på olika beräkningsresurser som är lämpliga för den uppgift som finns till hands. Exempelanteckningsboken kör "träningssteget" på ett litet CPU-kluster:

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

Koden ovan skapar den nya beräkningsresursen om det behövs. Sedan `step1_output` konverteras resultatet till indata för träningssteget. Alternativet `as_download()` innebär att data flyttas till beräkningsresursen, vilket ger snabbare åtkomst. Om data var så stora att de inte ryms på den lokala beräkningshårdsenheten skulle du använda alternativet för att strömma `as_mount()` data via FUSE-filsystemet. Det `compute_target` andra steget är , inte den resurs som du använde i steget för förberedelse av `'cpucluster'` `'link1-spark01'` data. I det här steget används ett enkelt program `train.py` i stället för det som du använde i föregående `dataprep.py` steg. Du kan se information om i `train.py` exempelanteckningsboken.

När du har definierat alla steg kan du skapa och köra din pipeline. 

```python
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[step_1, step_2])
pipeline_run = pipeline.submit('synapse-pipeline', regenerate_outputs=True)
```

Koden ovan skapar en pipeline som består av dataförberedelsesteget i Apache Spark-pooler som drivs av Azure Synapse Analytics ( `step_1` ) och träningssteget ( `step_2` ). Azure beräknar körningsdiagrammet genom att undersöka databeroendena mellan stegen. I det här fallet finns det bara ett enkelt beroende som `step2_input` nödvändigtvis kräver `step1_output` .

Anropet `pipeline.submit` till skapar vid behov ett experiment med namnet och `synapse-pipeline` asynkront inleder en Körning i det. Enskilda steg i pipelinen körs som underordnade körningar av den här huvudkörningen och kan övervakas och granskas på experimentsidan i Studio.

## <a name="next-steps"></a>Nästa steg

* [Publicera och spåra pipelines för maskininlärning](how-to-deploy-pipelines.md) 
* [Övervaka Azure Machine Learning](monitor-azure-machine-learning.md)
* [Använda automatiserad ML i en Azure Machine Learning-pipeline i Python](how-to-use-automlstep-in-pipelines.md)
