---
title: Förbered data med Spark-pooler (för hands version)
titleSuffix: Azure Machine Learning
description: Lär dig hur du kopplar Spark-pooler för förberedelse av data med Azure Synapse och Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/02/2021
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 87e03b6aee122c5a26d4388ca8b570aa6cdf7b55
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663807"
---
# <a name="attach-synapse-spark-pools-for-data-preparation-with-azure-synapse-preview"></a>Bifoga Synapse Spark-pooler för förberedelse av data med Azure Synapse (för hands version)

I den här artikeln får du lära dig hur du ansluter och startar en Apache Spark pool som backas upp av [Azure DataSynapses](/synapse-analytics/overview-what-is.md) för förberedelse av data. 

>[!IMPORTANT]
> Azure Machine Learning-och Azure Synapse-integrering är i för hands version. De funktioner som beskrivs i den här artikeln använder `azureml-synapse` paketet som [](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) innehåller funktioner för för hands version som kan ändras när som helst.

## <a name="azure-machine-learning-and-azure-synapse-integration-preview"></a>Azure Machine Learning-och Azure Synapse-integration (för hands version)

Med Azure Synapse-integreringen med Azure Machine Learning (för hands version) kan du koppla en Apache Spark pool som backas upp av Azure Synapse för interaktiv data utforskning och förberedelser. Med den här integrationen kan du ha en dedikerad beräkning för data förberedelse i stor skala, allt inom samma python-anteckningsbok som du använder för att träna dina Machine Learning-modeller.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md?tabs=python).

* [Skapa en Synapse-arbetsyta i Azure Portal](../synapse-analytics/quickstart-create-workspace.md).

* [Skapa Apache Spark pool med Azure Portal, webb verktyg eller Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* [Installera Azure Machine Learning python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) som innehåller `azureml-synapse` paketet (förhands granskning). 
    * Du kan också installera det själv, men det är bara kompatibelt med SDK-versionerna 1,20 eller högre. 
        ```python
        pip install azureml-synapse
        ```

## <a name="link-machine-learning-workspace-and-synapse-assets"></a>Länka Machine Learning-arbetsyta och Synapse-tillgångar

Innan du kan ansluta en Synapse Spark-pool för förberedelse av data måste din Azure Machine Learning-arbetsyta vara länkad till din Azure Synapse-arbetsyta. 

Du kan länka din ML-arbets yta och Synapse-arbetsytan via [python SDK](#link-sdk) eller [Azure Machine Learning Studio](#link-studio). 

> [!IMPORTANT]
> För att du ska kunna länka till Synapse-arbetsytan måste du beviljas **ägar** rollen för Synapse-arbetsytan. Kontrol lera åtkomsten i [Azure Portal](https://ms.portal.azure.com/).
>
> Om du inte är **ägare** till Synapse-arbetsytan, men vill använda en befintlig länkad tjänst, kan du läsa [Hämta en befintlig länkad tjänst](#get-an-existing-linked-service).


<a name="link-sdk"></a>
### <a name="link-workspaces-with-the-python-sdk"></a>Länka arbets ytor med python SDK

Följande kod använder [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) klasserna och för, 

* Länka din Machine Learning-arbetsyta `ws` med din Azure Synapse-arbetsyta. 
* Registrera din Synapse-arbetsyta med Azure Machine Learning som en länkad tjänst.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```
> [!IMPORTANT] 
> En hanterad identitet, `system_assigned_identity_principal_id` skapas för varje länkad tjänst. Den här hanterade identiteten måste beviljas rollen **Synapse Apache Spark administratör** för Synapse-arbetsytan innan du startar Synapse-sessionen. [Tilldela rollen Synapse Apache Spark-administratör till den hanterade identiteten i Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Använd om du vill hitta `system_assigned_identity_principal_id` en speciell länkad tjänst `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

<a name="link-studio"></a>
### <a name="link-workspaces-via-studio"></a>Länka arbets ytor via Studio

Länka din Machine Learning-arbetsyta och Synapse-arbetsyta via Azure Machine Learning Studio med följande steg: 

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **länkade tjänster** i avsnittet **Hantera** i det vänstra fönstret.
1. Välj **Lägg till integrering**.
1. Fyll i fälten i formuläret **länk arbets yta**

   |Fält| Beskrivning    
   |---|---
   |Namn| Ange ett namn för den länkade tjänsten. Det här namnet är vad som ska användas för att referera till den här länkade tjänsten.
   |Prenumerationens namn | Välj namnet på din prenumeration som är kopplad till Machine Learning-arbetsytan. 
   |Synapse-arbetsyta | Välj den Synapse-arbetsyta som du vill länka till. 
   
1. Välj **Nästa** för att öppna formuläret **Välj Spark-pooler (valfritt)** . I det här formuläret väljer du vilken Synapse Spark-pool som ska kopplas till din arbets yta

1. Välj **Nästa** för att öppna **gransknings** formuläret och kontrol lera dina val. 
1. Välj **skapa** för att slutföra processen för att skapa länkade tjänster.

## <a name="get-an-existing-linked-service"></a>Hämta en befintlig länkad tjänst

För att hämta och använda en befintlig länkad tjänst krävs **användar-eller deltagar** behörighet för arbets ytan Synapse.

I det här exemplet hämtas en befintlig länkad tjänst, `synapselink1` från arbets ytan, `ws` med- [`get()`](/python/api/azureml-core/azureml.core.linkedservice?preserve-view=true&view=azure-ml-py#get-workspace--name-) metoden.
```python
linked_service = LinkedService.get(ws, 'synapselink1')
```

### <a name="manage-linked-services"></a>Hantera länkade tjänster

Om du vill ta bort länken till dina arbets ytor använder du `unregister()` metoden

``` python
linked_service.unregister()
```

Visa alla länkade tjänster som är kopplade till Machine Learning-arbetsytan. 

```python
LinkedService.list(ws)
```
 
## <a name="attach-synapse-spark-pool-as-a-compute"></a>Koppla Synapse Spark-pool som en beräkning

När dina arbets ytor är länkade ansluter du en Synapse Spark-pool som en dedikerad beräknings resurs för dina data förberedelse aktiviteter. 

Du kan ansluta Synapse Spark-pooler via,
* Azure Machine Learning-studio
* [Azure Resource Manager-mallar (ARM)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)
* Python SDK 

Följ dessa steg om du vill ansluta en Synapse Spark-pool med hjälp av Studio. 

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **länkade tjänster** i avsnittet **Hantera** i det vänstra fönstret.
1. Välj din Synapse-arbetsyta.
1. Välj **anslutna Spark-pooler** längst upp till vänster. 
1. Välj **bifoga**. 
1. Välj din Synapse Spark-pool i listan och ange ett namn.  
    1. Den här listan identifierar de tillgängliga Synapse Spark-pooler som kan kopplas till din beräkning. 
    1. Information om hur du skapar en ny Synapse Spark-pool finns i [skapa Apache Spark pool med Synapse Studio](../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
1. Välj **koppla markerade**. 


Du kan också använda **python SDK** för att ansluta en Synapse Spark-pool. 

Följande kod, 
1. Konfigurerar SynapseCompute med,

   1. LinkedService, `linked_service` som du antingen skapade eller hämtade i föregående steg. 
   1. Den typ av beräknings mål som du vill koppla. `SynapseSpark`
   1. Namnet på Synapse Spark-poolen. Detta måste matcha en befintlig Apache Spark-pool som finns på din Synapse-arbetsyta.
   
1. Skapar en Machine Learning-ComputeTarget genom att skicka in, 
   1. Den Machine Learning-arbetsyta som du vill använda, `ws`
   1. Det namn som du vill referera till beräkningen i Machine Learning-arbetsytan. 
   1. Attach_configuration som du angav när du konfigurerade din SynapseCompute.
       1. Anropet till ComputeTarget. attach () är asynkront, så exempel blocken tills anropet har slutförts.

```python
from azureml.core.compute import SynapseCompute, ComputeTarget

attach_config = SynapseCompute.attach_configuration(linked_service, #Linked synapse workspace alias
                                                    type='SynapseSpark', #Type of assets to attach
                                                    pool_name="<Synapse Spark pool name>") #Name of Synapse spark pool 

synapse_compute = ComputeTarget.attach(workspace= ws,                
                                       name='<Synapse Spark pool alias in Azure ML>', 
                                       attach_configuration=attach_config
                                      )

synapse_compute.wait_for_completion()
```

Kontrol lera att Synapse Spark-poolen är ansluten.

```python
ws.compute_targets['Synapse Spark pool alias']
```

## <a name="launch-synapse-spark-pool-for-data-preparation-tasks"></a>Starta Synapse Spark-pool för uppgifter för förberedelse av data

Du kan ange en [Azure Machine Learning-miljö](concept-environments.md) som ska användas under Synapse-sessionen. Endast Conda-beroenden som angetts i miljön börjar gälla. Docker-avbildningen stöds inte.

>[!WARNING]
>  Python-beroenden som anges i miljö Conda-beroenden stöds inte i Synapse Spark-pooler. För närvarande stöds endast fasta python-versioner. Kontrol lera python-versionen genom  `sys.version_info` att inkludera i skriptet.

Följande kod skapar miljön, `myenv` som installerar `azureml-core` version 1.20.0 och `numpy` version 1.17.0 innan sessionen börjar. Du kan sedan ta med den här miljön i Synapse-sessionens `start` instruktion.

```python

from azureml.core import Workspace, Environment

# creates environment with numpy and azureml-core dependencies
ws = Workspace.from_config()
env = Environment(name="myenv")
env.python.conda_dependencies.add_pip_package("azureml-core==1.20.0")
env.python.conda_dependencies.add_conda_package("numpy==1.17.0")
env.register(workspace=ws)
```

För att börja förbereda data med Synapse Spark-poolen anger du namnet på Synapse Spark-poolen och anger ditt prenumerations-ID, resurs gruppen Machine Learning-arbetsyta, namnet på Machine Learning-arbetsytan och vilken miljö som ska användas under Synapse-sessionen. 

> [!IMPORTANT]
> Om du vill fortsätta använda Synapse Spark-poolen måste du ange vilken beräknings resurs som ska användas i data förberedelse aktiviteterna med `%synapse` för enskilda kodrader och `%%synapse` för flera rader. 

```python
%synapse start -c SynapseSparkPoolAlias -s AzureMLworkspaceSubscriptionID -r AzureMLworkspaceResourceGroupName -w AzureMLworkspaceName -e myenv
```

När sessionen har startats kan du kontrol lera sessionens metadata.

```python
%synapse meta
```

## <a name="load-data-from-storage"></a>Läs in data från lagring

När din Synapse Spark-session startar läser du i de data som du vill förbereda. Data inläsning stöds för Azure Blob Storage och Azure Data Lake Storage generation 1 och 2.

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
sc._jsc.hadoopConfiguration().set("fs.azure.sas.<container name>.<storage account name>.blob.core.windows.net", "sas token")

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

## <a name="perform-data-preparation-tasks"></a>Utföra uppgifter för förberedelse av data

När du har hämtat och utforskat dina data kan du utföra uppgifter för förberedelse av data.

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

När du har slutfört förberedelse av data och sparat dina för beredda data till lagring, slutar du att använda Synapse Spark-poolen med följande kommando.

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

## <a name="example-notebook"></a>Exempelnotebook-fil

I den här [slut antecknings boken för slutanvändare](../synapse-analytics/overview-what-is.md) finns ett detaljerat kod exempel på hur du utför förberedelse av data och modell utbildning från en enda bärbar dator med Azure Synapse och Azure Machine Learning.

## <a name="next-steps"></a>Nästa steg

* [Träna en modell](how-to-set-up-training-targets.md).
* [Träna med Azure Machine Learning data uppsättning](how-to-train-with-datasets.md)
* [Skapa en Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md).

