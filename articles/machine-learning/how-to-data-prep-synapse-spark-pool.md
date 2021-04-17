---
title: Dataförändring med Apache Spark pooler (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du kopplar och startar Apache Spark för data wrangling med Azure Synapse Analytics och Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 1852bfdb4bf8513aaa5d43993e2b6ff804808dbd
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575657"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Koppla Apache Spark (drivs av Azure Synapse Analytics) för data wrangling (förhandsversion)

I den här artikeln får du lära dig hur du kopplar en Apache Spark-pool som drivs av [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) till din Azure Machine Learning-arbetsyta, så att du kan starta den och utföra data wrangling i stor skala. 

Den här artikeln innehåller vägledning för att utföra data wrangling-uppgifter interaktivt i en dedikerad Synapse-session i en Jupyter Notebook [med hjälp Azure Machine Learning Python SDK](/python/api/overview/azure/ml/). Om du föredrar att använda Azure Machine Learning pipelines kan du gå till Använda Apache Spark (drivs av Azure Synapse Analytics) i [din maskininlärningspipeline (förhandsversion).](how-to-use-synapsesparkstep.md)

Om du vill ha vägledning om hur du använder Azure Synapse Analytics med en Synapse-arbetsyta kan du gå [Azure Synapse Analytics för att komma igång.](../synapse-analytics/get-started.md)

>[!IMPORTANT]
> Integreringen Azure Machine Learning och Azure Synapse Analytics är i förhandsversion. Funktionerna i den här artikeln använder paketet `azureml-synapse` som innehåller experimentella [förhandsversionsfunktioner](/python/api/overview/azure/ml/#stable-vs-experimental) som kan ändras när som helst.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning och Azure Synapse Analytics -integrering (förhandsversion)

Med Azure Synapse Analytics integrering med Azure Machine Learning (förhandsversion) kan du koppla en Apache Spark-pool med stöd Azure Synapse för interaktiv datagranskning och förberedelse. Med den här integreringen kan du ha en dedikerad beräkning för data wrangling i stor skala i samma Python-notebook-dator som du använder för att träna dina maskininlärningsmodeller.

## <a name="prerequisites"></a>Förutsättningar

* Den [Azure Machine Learning Python SDK installerat](/python/api/overview/azure/ml/install). 

* [Skapa en Azure Machine Learning arbetsyta](how-to-manage-workspace.md?tabs=python).

* [Skapa en Azure Synapse Analytics arbetsyta i Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Skapa Apache Spark-pool med Azure Portal, webbverktyg eller Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Konfigurera utvecklingsmiljön för](how-to-configure-environment.md) att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning-beräkningsinstans](concept-compute-instance.md#create) med SDK:t redan installerat. 

* Installera `azureml-synapse` paketet (förhandsversion) med följande kod:

  ```python
  pip install azureml-synapse
  ```

* [Länka Azure Machine Learning arbetsyta och Azure Synapse Analytics arbetsyta](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Hämta en befintlig länkad tjänst
Innan du kan koppla en dedikerad beräkning för databearbetning måste du ha en ML-arbetsyta som är länkad till en Azure Synapse Analytics-arbetsyta. Detta kallas för en länkad tjänst. 

För att hämta och använda en befintlig länkad tjänst krävs **användar- eller deltagarbehörighet** till Azure Synapse Analytics arbetsyta.

Visa alla de länkade tjänster som är associerade med din maskininlärningsarbetsyta. 

```python
from azureml.core import LinkedService

LinkedService.list(ws)
```

Det här exemplet hämtar en befintlig länkad `synapselink1` tjänst, , från `ws` arbetsytan, , med [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) metoden .
```python
from azureml.core import LinkedService

linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Koppla Synapse Spark-pool som en beräkning

När du har hämtat den länkade tjänsten kopplar du en Synapse Apache Spark-pool som en dedikerad beräkningsresurs för dina databearbetningsuppgifter. 

Du kan koppla Apache Spark via,
* Azure Machine Learning-studio
* [Azure Resource Manager-mallar (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Den Azure Machine Learning Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Koppla en pool via studio
Följ de här stegen: 

1. Logga in på [Azure Machine Learning-studio](https://ml.azure.com/).
1. Välj **Länkade tjänster** i **avsnittet** Hantera i det vänstra fönstret.
1. Välj din Synapse-arbetsyta.
1. Välj **Anslutna Spark-pooler** längst upp till vänster. 
1. Välj **Bifoga.** 
1. Välj din Apache Spark i listan och ange ett namn.  
    1. Den här listan identifierar de tillgängliga Synapse Spark-pooler som kan kopplas till din beräkning. 
    1. Om du vill skapa en ny Synapse Spark-pool kan [du Apache Spark skapa en pool med Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Välj **Bifoga markerad.** 

### <a name="attach-a-pool-with-the-python-sdk"></a>Koppla en pool med Python SDK

Du kan också använda **Python SDK för** att koppla en Apache Spark pool. 

Följande kod, 
1. Konfigurerar [`SynapseCompute`](/python/api/azureml-core/azureml.core.compute.synapsecompute) med,

   1. [`LinkedService`](/python/api/azureml-core/azureml.core.linkedservice)som `linked_service` du antingen skapade eller hämtade i föregående steg. 
   1. Den typ av beräkningsmål som du vill koppla, `SynapseSpark`
   1. Namnet på Apache Spark poolen. Detta måste matcha en befintlig Apache Spark som finns i din Azure Synapse Analytics arbetsyta.
   
1. Skapar en [`ComputeTarget`](/python/api/azureml-core/azureml.core.computetarget) maskininlärning genom att skicka in, 
   1. Den arbetsyta för maskininlärning som du vill använda, `ws`
   1. Det namn som du vill referera till beräkningen i den Azure Machine Learning arbetsytan. 
   1. Den attach_configuration du angav när du konfigurerade Synapse Compute.
       1. Anropet till ComputeTarget.attach() är asynkront, så exemplet blockerar tills anropet har slutförts.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name=synapse_spark_pool_name) #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name= synapse_compute_name, 
                                       attach_configuration= attach_config
                                      )

synapse_compute.wait_for_completion()
```

Kontrollera Apache Spark poolen är ansluten.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-wrangling-tasks"></a>Starta Synapse Spark-pool för data wrangling-uppgifter

Om du vill börja förbereda data Apache Spark en pool anger du Apache Spark namn:

> [!IMPORTANT]
> Om du vill fortsätta Apache Spark datapoolen måste du ange vilken beräkningsresurs som ska användas i alla data wrangling-uppgifter med för enskilda rader med kod `%synapse` `%%synapse` och för flera rader. 

```python
%synapse start -c SynapseSparkPoolAlias
```

När sessionen startar kan du kontrollera sessionens metadata.

```python
%synapse meta
```

Du kan ange en [Azure Machine Learning miljö](concept-environments.md) som ska användas under Apache Spark sessionen. Endast Conda-beroenden som anges i miljön börjar gälla. Docker-avbildning stöds inte.

>[!WARNING]
>  Python-beroenden som anges i miljön Conda-beroenden stöds inte i Apache Spark pooler. För närvarande stöds endast fasta Python-versioner. Kontrollera Python-versionen genom att  `sys.version_info` inkludera i skriptet.

Följande kod skapar miljön, , som installerar `myenv` `azureml-core` version 1.20.0 och `numpy` version 1.17.0 innan sessionen börjar. Du kan sedan inkludera den här miljön i Apache Spark `start` sessionsutdrag.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

Om du vill börja förbereda data med Apache Spark-poolen och din anpassade miljö anger du namnet på Apache Spark-poolen och vilken miljö som ska användas under Apache Spark sessionen. Dessutom kan du ange ditt prenumerations-ID, resursgruppen för maskininlärningsarbetsytan och namnet på maskininlärningsarbetsytan.

```python
%synapse start -c SynapseSparkPoolAlias -e myenv -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName
```
## <a name="load-data-from-storage"></a>Läsa in data från lagring

När Apache Spark startar läser du in de data som du vill förbereda. Inläsning av data stöds för Azure Blob Storage och Azure Data Lake Storage generation 1 och 2.

Det finns två sätt att läsa in data från dessa lagringstjänster: 

* Läs in data direkt från lagringen med hjälp av dess HDFS-sökväg (Hadoop Distributed Files System).

* Läsa in data från en befintlig Azure Machine Learning [datauppsättning](how-to-create-register-datasets.md).

För att få åtkomst till dessa lagringstjänster behöver du **läsarbehörigheter för Storage Blob Data.** Om du planerar att skriva tillbaka data till dessa lagringstjänster behöver du behörighet som **Storage Blob Data-deltagare.** [Läs mer om lagringsbehörigheter och roller.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Läs in data med HDFS-sökvägen (Hadoop Distributed Files System)

Om du vill läsa in och läsa in data från lagringen med motsvarande HDFS-sökväg måste du ha dina autentiseringsuppgifter för dataåtkomst tillgängliga. Dessa autentiseringsuppgifter varierar beroende på din lagringstyp.  

Följande kod visar hur du läser data från **en Azure Blob Storage** till en Spark-dataram med antingen din SAS-token (signatur för delad åtkomst) eller åtkomstnyckel. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Följande kod visar hur du läser in data från **Azure Data Lake Storage Generation 1 (ADLS Gen 1) med** dina autentiseringsuppgifter för tjänstens huvudnamn. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Följande kod visar hur du läser in data från **Azure Data Lake Storage Generation 2 (ADLS Gen 2) med** dina autentiseringsuppgifter för tjänstens huvudnamn. 

```python
%%synapse

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
"https://login.microsoftonline.com/<tenant id>/oauth2/token")

df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Läsa in data från registrerade datauppsättningar

Du kan också hämta en befintlig registrerad datauppsättning på arbetsytan och förbereda data genom att konvertera den till en Spark-dataram.

I följande exempel autentiseras till arbetsytan, hämtar en registrerad TabularDataset, , som refererar till filer i Blob Storage och konverterar den till en `blob_dset` Spark DataFrame. När du konverterar dina datauppsättningar till en Spark-dataram kan du använda bibliotek `pyspark` för datautforskning och förberedelse.  

``` python
%%synapse

from azureml.core import Workspace, Dataset

subscription_id = "<enter your subscription ID>"
resource_group = "<enter your resource group>"
workspace_name = "<enter your workspace name>"

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Utföra dataförändringsuppgifter

När du har hämtat och utforskat dina data kan du utföra data wrangling-uppgifter.

Följande kod, expanderar HDFS-exemplet i föregående avsnitt och filtrerar data i Spark-dataramen, , baserat på kolumnen Omarifier och grupper som `df` listas efter **Ålder** 

```python
%%synapse

from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Spara data i lagringen och stoppa Spark-sessionen

När datautforskningen och förberedelsen är klar lagrar du dina förberedda data för senare användning i ditt lagringskonto i Azure.

I följande exempel skrivs förberedda data tillbaka till Azure Blob Storage och skriver över den ursprungliga `Titanic.csv` filen i `training_data` katalogen. Om du vill skriva tillbaka till lagringen behöver du **behörighet som Storage Blob Data-deltagare.** [Läs mer om lagringsbehörigheter och roller.](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

När du har slutfört dataförberedelserna och sparat dina förberedda data till lagringen slutar du använda Apache Spark poolen med följande kommando.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Skapa datauppsättning för att representera förberedda data

När du är redo att använda dina förberedda data för modellträning ansluter du till lagringen med [ett Azure Machine Learning-datalager](how-to-access-data.md)och anger vilka filer som du vill använda med en [Azure Machine Learning datauppsättning](how-to-create-register-datasets.md).

Följande kodexempel:

* Anta att du redan har skapat ett datalager som ansluter till lagringstjänsten där du sparade dina förberedda data.  
* Hämtar det befintliga `mydatastore` dataarkivet, , från arbetsytan med metoden `ws` get().
* Skapar en [FileDataset](how-to-create-register-datasets.md#filedataset), `train_ds` , som refererar till de förberedda datafiler som finns i `training_data` katalogen i `mydatastore` .  
* Skapar variabeln `input1` , som kan användas vid ett senare tillfälle för att göra datafilerna för `train_ds` datauppsättningen tillgängliga för ett beräkningsmål.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```
## <a name="use-a-scriptrunconfig-to-submit-an-experiment-run-to-a-synapse-spark-pool"></a>Använd en `ScriptRunConfig` för att skicka en experimentkörning till en Synapse Spark-pool

Du kan också [använda Det Synapse Spark-kluster](#attach-a-pool-with-the-python-sdk) som du kopplade tidigare som ett beräkningsmål för att skicka en experimentkörning med ett [ScriptRunConfig-objekt.](/python/api/azureml-core/azureml.core.scriptrunconfig)

```Python
from azureml.core import RunConfiguration
from azureml.core import ScriptRunConfig 
from azureml.core import Experiment

run_config = RunConfiguration(framework="pyspark")
run_config.target = synapse_compute_name

run_config.spark.configuration["spark.driver.memory"] = "1g" 
run_config.spark.configuration["spark.driver.cores"] = 2 
run_config.spark.configuration["spark.executor.memory"] = "1g" 
run_config.spark.configuration["spark.executor.cores"] = 1 
run_config.spark.configuration["spark.executor.instances"] = 1 

run_config.environment.python.conda_dependencies = conda_dep

script_run_config = ScriptRunConfig(source_directory = './code',
                                    script= 'dataprep.py',
                                    arguments = ["--tabular_input", input1, 
                                                 "--file_input", input2,
                                                 "--output_dir", output],
                                    run_config = run_config)
```

När `ScriptRunConfig` objektet har ställts in kan du skicka körningen.

```python
from azureml.core import Experiment 

exp = Experiment(workspace=ws, name="synapse-spark") 
run = exp.submit(config=script_run_config) 
run
```
Mer information, t.ex. `dataprep.py` skriptet som används i det här exemplet, finns i [exempelanteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb).

## <a name="example-notebooks"></a>Exempelnotebook-filer

I den [här exempelanteckningsboken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-synapse/spark_session_on_synapse_spark_pool.ipynb) finns fler begrepp och demonstrationer av Azure Synapse Analytics och Azure Machine Learning för integrering.

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-set-up-training-targets.md).
* [Träna med Azure Machine Learning datauppsättning](how-to-train-with-datasets.md)
