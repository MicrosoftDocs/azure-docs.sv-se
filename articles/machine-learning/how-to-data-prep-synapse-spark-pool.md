---
title: Data datatransformering med Apache Spark pooler (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du ansluter och startar Apache Spark pooler för data datatransformering med Azure Synapse Analytics och Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: acd8df620e23ee4ebc103d8910c6443f47ffa141
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503835"
---
# <a name="attach-apache-spark-pools-powered-by-azure-synapse-analytics-for-data-wrangling-preview"></a>Bifoga Apache Spark pooler (drivs av Azure Synapse Analytics) för data datatransformering (för hands version)

I den här artikeln får du lära dig hur du ansluter och startar en Apache Spark pool som drivs av [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) för data datatransformering i stor skala. 

Den här artikeln innehåller rikt linjer för att utföra data datatransformering-aktiviteter interaktivt inom en dedikerad Synapse-session i en Jupyter Notebook. Om du föredrar att använda Azure Machine Learning pipelines, se [hur du använder Apache Spark (drivs av Azure Synapse Analytics) i din Machine Learning pipeline (för hands version)](how-to-use-synapsesparkstep.md).

>[!IMPORTANT]
> Azure Machine Learning-och Azure Synapse Analytics-integrering är i för hands version. De funktioner som beskrivs i den här artikeln använder `azureml-synapse` paketet som [](/python/api/overview/azure/ml/#stable-vs-experimental) innehåller funktioner för för hands version som kan ändras när som helst.

## <a name="azure-machine-learning-and-azure-synapse-analytics-integration-preview"></a>Azure Machine Learning och Azure Synapse Analytics-integration (för hands version)

Med Azure Synapse Analytics-integreringen med Azure Machine Learning (för hands version) kan du koppla en Apache Spark pool som backas upp av Azure Synapse för interaktiv data utforskning och förberedelser. Med den här integrationen kan du ha en dedikerad beräkning för data datatransformering i stor skala, allt inom samma python-anteckningsbok som du använder för att träna dina Machine Learning-modeller.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md?tabs=python).

* [Skapa en Azure Synapse Analytics-arbetsyta i Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Skapa Apache Spark pool med Azure Portal, webb verktyg eller Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning beräknings instans](concept-compute-instance.md#create) med SDK redan installerad. 

* Installera `azureml-synapse` paketet (förhands granskning) med följande kod:

  ```python
  pip install azureml-synapse
  ```

* [Länka Azure Machine Learning arbets yta och Azure Synapse Analytics-arbetsyta](how-to-link-synapse-ml-workspaces.md).

## <a name="get-an-existing-linked-service"></a>Hämta en befintlig länkad tjänst
Innan du kan koppla en dedikerad beräkning för data datatransformering måste du ha en ML-arbetsyta som är länkad till en Azure Synapse Analytics-arbetsyta, vilket kallas för en länkad tjänst. 

Om du vill hämta och använda en befintlig länkad tjänst måste du ha behörighet som **användare eller deltagare** i Azure Synapse Analytics-arbetsytan.

Visa alla länkade tjänster som är kopplade till Machine Learning-arbetsytan. 

```python
LinkedService.list(ws)
```

I det här exemplet hämtas en befintlig länkad tjänst, `synapselink1` från arbets ytan, `ws` med- [`get()`](/python/api/azureml-core/azureml.core.linkedservice#get-workspace--name-) metoden.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Koppla Synapse Spark-pool som en beräkning

När du har hämtat den länkade tjänsten ansluter du en Synapse Apache Spark-pool som en dedikerad beräknings resurs för dina data datatransformering uppgifter. 

Du kan koppla Apache Spark pooler via,
* Azure Machine Learning-studio
* [Azure Resource Manager-mallar (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

### <a name="attach-a-pool-via-the-studio"></a>Koppla en pool via Studio
Gör så här: 

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **länkade tjänster** i avsnittet **Hantera** i det vänstra fönstret.
1. Välj din Synapse-arbetsyta.
1. Välj **anslutna Spark-pooler** längst upp till vänster. 
1. Välj **bifoga**. 
1. Välj din Apache Spark-pool i listan och ange ett namn.  
    1. Den här listan identifierar de tillgängliga Synapse Spark-pooler som kan kopplas till din beräkning. 
    1. Information om hur du skapar en ny Synapse Spark-pool finns i [skapa Apache Spark pool med Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Välj **koppla markerade**. 

### <a name="attach-a-pool-with-the-python-sdk"></a>Koppla en pool med python SDK

Du kan också använda **python SDK** för att koppla en Apache Spark pool. 

Följande kod, 
1. Konfigurerar SynapseCompute med,

   1. LinkedService, `linked_service` som du antingen skapade eller hämtade i föregående steg. 
   1. Den typ av beräknings mål som du vill koppla. `SynapseSpark`
   1. Namnet på den Apache Spark poolen. Detta måste matcha en befintlig Apache Spark pool som finns i din Azure Synapse Analytics-arbetsyta.
   
1. Skapar en Machine Learning-ComputeTarget genom att skicka in, 
   1. Den Machine Learning-arbetsyta som du vill använda, `ws`
   1. Det namn som du vill referera till beräkningen i Azure Machine Learning-arbetsytan. 
   1. Den attach_configuration du angav när du konfigurerade Synapse-beräkningen.
       1. Anropet till ComputeTarget. attach () är asynkront, så exempel blocken tills anropet har slutförts.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name="<Synapse Spark pool alias in Azure ML>", 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Kontrol lera att Apache Spark-poolen är ansluten.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Starta Synapse Spark-pool för uppgifter för förberedelse av data

Du kan ange en [Azure Machine Learning-miljö](concept-environments.md) som ska användas under Apache Spark-sessionen. Endast Conda-beroenden som angetts i miljön börjar gälla. Docker-avbildningen stöds inte.

>[!WARNING]
>  Python-beroenden som anges i miljö Conda-beroenden stöds inte i Apache Spark pooler. För närvarande stöds endast fasta python-versioner. Kontrol lera python-versionen genom  `sys.version_info` att inkludera i skriptet.

Följande kod skapar miljön, `myenv` som installerar `azureml-core` version 1.20.0 och `numpy` version 1.17.0 innan sessionen börjar. Du kan sedan ta med den här miljön i Apache Spark-session- `start` instruktionen.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

För att börja förbereda data med Apache Spark pool anger du namnet på Apache Spark bassäng och anger ditt prenumerations-ID, resurs gruppen Machine Learning-arbetsyta, namnet på Machine Learning-arbetsytan och vilken miljö som ska användas under den Apache Spark sessionen. 

> [!IMPORTANT]
> Om du vill fortsätta använda Apache Spark-poolen måste du ange vilken beräknings resurs som ska användas i dina data datatransformering uppgifter med `%synapse` för enskilda kodrader och `%%synapse` för flera rader. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

När sessionen har startats kan du kontrol lera sessionens metadata.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Läs in data från lagring

När din Apache Spark-session startar läser du i de data som du vill förbereda. Data inläsning stöds för Azure Blob Storage och Azure Data Lake Storage generation 1 och 2.

Det finns två sätt att läsa in data från dessa lagrings tjänster: 

* Läs in data direkt från lagrings platsen med hjälp av en HDFS-sökväg (Hadoop Distributed Files System).

* Läs in data från en befintlig [Azure Machine Learning data uppsättning](how-to-create-register-datasets.md).

Du behöver **Storage BLOB data Reader** -behörigheter för att få åtkomst till dessa lagrings tjänster. Om du planerar att skriva data tillbaka till dessa lagrings tjänster måste du ha behörighet som **deltagare i Storage BLOB-data** . [Läs mer om lagrings behörigheter och roller](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

### <a name="load-data-with-hadoop-distributed-files-system-hdfs-path"></a>Läsa in data med HDFS-sökväg (Hadoop Distributed Files System)

Om du vill läsa in och läsa data från lagrings platsen med motsvarande HDFS-sökväg måste du ha dina autentiseringsuppgifter för autentisering av data åtkomst tillgänglig. Dessa autentiseringsuppgifter skiljer sig åt beroende på din lagrings typ.  

Följande kod visar hur du läser data från en **Azure Blob Storage** till en spark-dataframe med antingen din SAS-token (signatur för delad åtkomst) eller åtkomst nyckel. 

```python
%%synapse

# setup access key or SAS token
sc._jsc.hadoopConfiguration().set("fs.azure.account.key.<storage account name>.blob.core.windows.net", "<access key>")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "<sas token>")

# read from blob 
df = spark.read.option("header", "true").csv("wasbs://demo@dprepdata.blob.core.windows.net/Titanic.csv")
```

Följande kod visar hur du läser data i från **Azure Data Lake Storage generation 1 (ADLS gen 1)** med dina autentiseringsuppgifter för tjänstens huvud namn. 

```python

# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.access.token.provider.type","ClientCredential")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.client.id", "<client id>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.credential", "<client secret>")

sc._jsc.hadoopConfiguration().set("fs.adl.account.<storage account name>.oauth2.refresh.url",
https://login.microsoftonline.com/<tenant id>/oauth2/token)

df = spark.read.csv("adl://<storage account name>.azuredatalakestore.net/<path>")

```

Följande kod visar hur du läser data i från **Azure Data Lake Storage generation 2 (ADLS gen 2)** med dina autentiseringsuppgifter för tjänstens huvud namn. 

```python
# setup service principal which has access of the data
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type.<storage account name>.dfs.core.windows.net","OAuth")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth.provider.type.<storage account name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.id.<storage account name>.dfs.core.windows.net", "<client id>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.secret.<storage account name>.dfs.core.windows.net", "<client secret>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.oauth2.client.endpoint.<storage account name>.dfs.core.windows.net",
https://login.microsoftonline.com/<tenant id>/oauth2/token)


df = spark.read.csv("abfss://<container name>@<storage account>.dfs.core.windows.net/<path>")

```

### <a name="read-in-data-from-registered-datasets"></a>Läsa in data från registrerade data uppsättningar

Du kan också hämta en befintlig registrerad data uppsättning i din arbets yta och utföra förberedelse av data genom att konvertera den till en spark-dataframe.  

I följande exempel hämtas en registrerad TabularDataset, `blob_dset` som refererar till filer i Blob Storage, och konverterar den till en spark-dataframe. När du konverterar dina data uppsättningar till en spark-dataframe kan du utnyttja `pyspark` data utforsknings-och förberedelse bibliotek.  

``` python

%%synapse
from azureml.core import Workspace, Dataset

dset = Dataset.get_by_name(ws, "blob_dset")
spark_df = dset.to_spark_dataframe()
```

## <a name="perform-data-wrangling-tasks"></a>Utföra data datatransformering-uppgifter

När du har hämtat och utforskat dina data kan du utföra data datatransformering-uppgifter.

Följande kod expanderar på HDFS-exemplet i föregående avsnitt och filtrerar data i Spark-dataframe, `df` baserat på den **efterlevande** kolumnen och grupper som listas efter **ålder**

```python
%%synapse
from pyspark.sql.functions import col, desc

df.filter(col('Survived') == 1).groupBy('Age').count().orderBy(desc('count')).show(10)

df.show()

```

## <a name="save-data-to-storage-and-stop-spark-session"></a>Spara data till lagring och stoppa Spark-session

När data utforskningen och förberedelsen är slutförda lagrar du dina för beredda data för senare användning i ditt lagrings konto i Azure.

I följande exempel skrivs de för beredda data tillbaka till Azure Blob Storage och skriver över den ursprungliga `Titanic.csv` filen i `training_data` katalogen. Om du vill skriva tillbaka till lagring måste du ha behörighet som **deltagare i Storage BLOB-data** . [Läs mer om lagrings behörigheter och roller](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues).

```python
%% synapse

df.write.format("csv").mode("overwrite").save("wasbs://demo@dprepdata.blob.core.windows.net/training_data/Titanic.csv")
```

När du har slutfört förberedelse av data och sparat dina för beredda data till lagring, slutar du att använda Apache Spark-poolen med följande kommando.

```python
%synapse stop
```

## <a name="create-dataset-to-represent-prepared-data"></a>Skapa data uppsättning för att representera för beredda data

När du är redo att använda dina för beredda data för modell träning ansluter du till lagringen med ett [Azure Machine Learning data lager](how-to-access-data.md)och anger vilka filer som du vill använda med en [Azure Machine Learning data uppsättning](how-to-create-register-datasets.md).

Följande kod exempel,

* Förutsätter att du redan har skapat ett data lager som ansluter till lagrings tjänsten där du sparade dina för beredda data.  
* Hämtar det befintliga data lagret, `mydatastore` från arbets ytan, `ws` med metoden Get ().
* Skapar en [FileDataset](how-to-create-register-datasets.md#filedataset), `train_ds` som refererar till de för beredda datafiler som finns i `training_data` katalogen i `mydatastore` .  
* Skapar variabeln `input1` som kan användas vid ett senare tillfälle för att göra datafilerna för data `train_ds` uppsättningen tillgängliga för ett beräknings mål.

```python
from azureml.core import Datastore, Dataset

datastore = Datastore.get(ws, datastore_name='mydatastore')

datastore_paths = [(datastore, '/training_data/')]
train_ds = Dataset.File.from_files(path=datastore_paths, validate=True)
input1 = train_ds.as_mount()

```

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-set-up-training-targets.md).
* [Träna med Azure Machine Learning data uppsättning](how-to-train-with-datasets.md)
