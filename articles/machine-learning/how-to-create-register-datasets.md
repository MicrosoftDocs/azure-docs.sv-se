---
title: Skapa Azure Machine Learning-datamängder
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar Azure Machine Learning för att komma åt dina data för körningar av maskininlärningsexperiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: f47d610a24de2cfc8f1131f61afc8c8173a34376
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786629"
---
# <a name="create-azure-machine-learning-datasets"></a>Skapa Azure Machine Learning-datamängder

I den här artikeln får du lära dig hur du skapar Azure Machine Learning för att komma åt data för dina lokala experiment eller fjärrexperiment med Azure Machine Learning Python SDK. Information om var datauppsättningar passar Azure Machine Learning det övergripande arbetsflödet för dataåtkomst finns i [artikeln Dataåtkomst på ett säkert](concept-data.md#data-workflow) sätt.

Genom att skapa en datamängd skapar du en referens till datakällans plats, tillsammans med en kopia av dess metadata. Eftersom data finns kvar på den befintliga platsen medför det inga extra lagringskostnader och du riskerar inte integriteten hos dina datakällor. Datauppsättningar utvärderas också lätt, vilket underlättar arbetsflödets prestandahastigheter. Du kan skapa datauppsättningar från datalager, offentliga URL:er och [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Om du vill ha lite kod [kan du skapa Azure Machine Learning datauppsättningar med Azure Machine Learning-studio.](how-to-connect-data-ui.md#create-datasets)

Med Azure Machine Learning datauppsättningar kan du:

* Behåll en enskild kopia av data i din lagring, som refereras av datauppsättningar.

* Få sömlös åtkomst till data under modellträningen utan att behöva bekymra dig om anslutningssträngar eller datasökvägar. [Läs mer om hur du tränar med datauppsättningar](how-to-train-with-datasets.md).

* Dela data och samarbeta med andra användare.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med datauppsättningar behöver du:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En [Azure Machine Learning arbetsyta](how-to-manage-workspace.md).

* [Sdk Azure Machine Learning för Python installerat](/python/api/overview/azure/ml/install), som innehåller paketet azureml-datasets.

    * Skapa en [Azure Machine Learning-beräkningsinstans](how-to-create-manage-compute-instance.md), som är en fullständigt konfigurerad och hanterad utvecklingsmiljö som innehåller integrerade notebook-filer och SDK som redan är installerade.

    **OR**

    * Arbeta med din egen Jupyter-anteckningsbok och installera SDK själv med [dessa instruktioner.](/python/api/overview/azure/ml/install)

> [!NOTE]
> Vissa datamängdsklasser har beroenden på [paketet azureml-dataprep,](https://pypi.org/project/azureml-dataprep/) som endast är kompatibelt med 64-bitars Python. För Linux-användare stöds dessa klasser endast på följande distributioner: Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) och CentOS (7). Om du använder distributioner som inte stöds följer du den här [guiden för](/dotnet/core/install/linux) att installera .NET Core 2.1 för att fortsätta. 

## <a name="compute-size-guidance"></a>Vägledning för beräkningsstorlek

När du skapar en datauppsättning granskar du beräkningskraften och storleken på dina data i minnet. Storleken på dina data i lagringen är inte samma som storleken på data i en dataram. Data i CSV-filer kan till exempel expandera upp till 10 x i en dataram, så en CSV-fil på 1 GB kan bli 10 GB i en dataram. 

Om dina data komprimeras kan de expanderas ytterligare. 20 GB relativt glesa data som lagras i komprimerat parquet-format kan expandera till ~800 GB i minnet. Eftersom Parquet-filer lagrar data i kolumnformat behöver du bara läsa in ~400 GB i minnet om du bara behöver hälften av kolumnerna.

[Läs mer om hur du optimerar databehandling i Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Datamängdstyper

Det finns två datauppsättningstyper, baserat på hur användarna använder dem i träningen: FileDatasets och TabularDatasets. Båda typerna kan användas i Azure Machine Learning träningsarbetsflöden som involverar, beräknare, AutoML, hyperDrive och pipelines. 

### <a name="filedataset"></a>FileDataset

En [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) refererar till en eller flera filer i dina datalager eller offentliga URL:er. Om dina data redan har rensats och är redo [](how-to-train-with-datasets.md#mount-vs-download) att användas i träningsexperiment kan du ladda ned eller montera filerna till din beräkning som ett FileDataset-objekt. 

Vi rekommenderar FileDatasets för dina maskininlärningsarbetsflöden, eftersom källfilerna kan ha alla format, vilket möjliggör ett bredare utbud av maskininlärningsscenarier, inklusive djupinlärning.

Skapa en FileDataset med [Python SDK eller](#create-a-filedataset) [Azure Machine Learning-studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

En [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) representerar data i tabellformat genom att parsa den angivna filen eller listan med filer. Detta ger dig möjlighet att materialisera data till en Pandas- eller Spark DataFrame så att du kan arbeta med välbekanta bibliotek för förberedelse av data och träning utan att behöva lämna notebook-datorn. Du kan skapa ett `TabularDataset` -objekt från .csv, .tsv, [.parquet,](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) [.jsonl-filer](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none--invalid-lines--error---encoding--utf8--)och från [SQL-frågeresultat](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Med TabularDatasets kan du ange en tidsstämpel från en kolumn i data eller var sökvägsmönstrets data lagras för att aktivera en tidsserieegenskaper. Den här specifikationen möjliggör enkel och effektiv filtrering efter tid. Ett exempel finns i [Tabular time series-related API demo with NOAA weather data](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Skapa en TabularDataset med [Python SDK eller](#create-a-tabulardataset) [Azure Machine Learning-studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> [Automatiserade](concept-automated-ml.md) ML-arbetsflöden som genereras via Azure Machine Learning-studio för närvarande endast stöd för TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Få åtkomst till datauppsättningar i ett virtuellt nätverk

Om arbetsytan finns i ett virtuellt nätverk måste du konfigurera datauppsättningen så att den hoppar över verifieringen. Mer information om hur du använder datalager och datauppsättningar i ett virtuellt nätverk finns i [Skydda en arbetsyta och associerade resurser](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Skapa datauppsättningar från datalager

För att data ska vara tillgängliga för Azure Machine Learning måste datauppsättningar skapas från sökvägar [i Azure Machine Learning eller](how-to-access-data.md) webb-URL:er. 

> [!TIP] 
> Du kan skapa datauppsättningar direkt från lagrings-URL:er med identitetsbaserad dataåtkomst. Läs mer i [Ansluta till lagring med identitetsbaserad dataåtkomst (förhandsversion)](how-to-identity-based-data-access.md)<br><br>
Den här funktionen är en [experimentell](/python/api/overview/azure/ml/#stable-vs-experimental) förhandsgranskningsfunktion som kan ändras när som helst. 

 
Så här skapar du datamängder från ett datalager med Python SDK:

1. Kontrollera att du har `contributor` eller åtkomst till den underliggande `owner` lagringstjänsten för ditt registrerade Azure Machine Learning datalager. [Kontrollera dina behörigheter för lagringskontot i Azure Portal](../role-based-access-control/check-access.md).

1. Skapa datauppsättningen genom att referera till sökvägar i datalagringen. Du kan skapa en datauppsättning från flera sökvägar i flera datalager. Det finns ingen hård gräns för antalet filer eller datastorlek som du kan skapa en datauppsättning från. 

> [!NOTE]
> För varje datasökväg skickas några begäranden till lagringstjänsten för att kontrollera om den pekar på en fil eller en mapp. Det här arbetet kan leda till försämrade prestanda eller fel. En datauppsättning som refererar till en mapp med 1 000 filer inuti anses referera till en datasökväg. Vi rekommenderar att du skapar en datauppsättning som refererar till färre än 100 sökvägar i datalager för optimala prestanda.

### <a name="create-a-filedataset"></a>Skapa en FileDataset

Använd metoden [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) i klassen för att läsa in filer i alla format och för att skapa en `FileDatasetFactory` oregistrerad FileDataset. 

Om lagringen finns bakom ett virtuellt nätverk eller en brandvägg anger du `validate=False` parametern i metoden `from_files()` . Detta kringgår det första valideringssteget och säkerställer att du kan skapa datauppsättningen från dessa säkra filer. Läs mer om hur du [använder datalager och datauppsättningar i ett virtuellt nätverk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Om du vill återanvända och dela datauppsättningar mellan experiment på din arbetsyta [registrerar du datauppsättningen](#register-datasets). 

> [!TIP] 
> Ladda upp filer från en lokal katalog och skapa en FileDataset i en enda metod med den offentliga [förhandsversionsmetoden, upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Den här metoden är [en experimentell](/python/api/overview/azure/ml/#stable-vs-experimental) förhandsgranskningsfunktion som kan ändras när som helst. 
> 
>  Den här metoden laddar upp data till din underliggande lagring och medför därmed lagringskostnader. 

### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Använd metoden i klassen för att läsa filer i [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) `TabularDatasetFactory` .csv- eller .tsv-format och för att skapa en oregistrerad TabularDataset. Om du vill läsa i filer från .parquet-format använder du [`from_parquet_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) metoden . Om du läser från flera filer aggregeras resultaten till en tabellrepresentation. 

Se [referensdokumentationen för TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) för information om filformat som stöds, samt syntax och designmönster. 

Om lagringen finns bakom ett virtuellt nätverk eller en brandvägg anger du `validate=False` parametern i metoden `from_delimited_files()` . Detta kringgår det första valideringssteget och säkerställer att du kan skapa datauppsättningen från dessa säkra filer. Läs mer om hur du använder [datalager och datauppsättningar i ett virtuellt nätverk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

Följande kod hämtar den befintliga arbetsytan och önskat datalager efter namn. Och skickar sedan datalager- och filplatserna till `path` parametern för att skapa en ny TabularDataset, `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Ange dataschema

När du skapar en TabularDataset härförs kolumndatatyper som standard automatiskt. Om de härdade typerna inte matchar dina förväntningar kan du uppdatera datauppsättningsschemat genom att ange kolumntyper med följande kod. Parametern `infer_column_type` gäller endast för datauppsättningar som skapats från avgränsade filer. [Läs mer om datatyper som stöds.](/python/api/azureml-core/azureml.data.dataset_factory.datatype)


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Överlevde|Pclass|Name|Sex|Ålder|SibSp|Parch|Biljett|Biljettpris|Stuga|Inlett
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falskt|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7.2500||S
1|2|Sant|1|Cumings, Fru John Bradley (Edit Briggs Th...|kvinna|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Sant|3|Henen, Miss. Laina|kvinna|26,0|0|0|STON/O2. 3101282|7.9250||S

Om du vill återanvända och dela datamängder mellan experiment på din arbetsyta [registrerar du datauppsättningen](#register-datasets).

## <a name="wrangle-data"></a>Wrangle data
När du har skapat [och registrerat](#register-datasets) din datauppsättning kan du läsa in den i anteckningsboken för data wrangling och [utforskning](#explore-data) före modellträningen. 

Om du inte behöver utföra någon data wrangling eller utforskning kan du se hur du använder datauppsättningar i dina träningsskript för att skicka ML-experiment i Träna med [datauppsättningar](how-to-train-with-datasets.md).

### <a name="filter-datasets-preview"></a>Filtrera datauppsättningar (förhandsversion)

Filtreringsfunktioner beror på vilken typ av datauppsättning du har. 
> [!IMPORTANT]
> Filtrering av datauppsättningar med förhandsversionsmetoden är [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) en experimentell [förhandsgranskningsfunktion](/python/api/overview/azure/ml/#stable-vs-experimental) och kan ändras när som helst. 
> 
**För TabularDatasets kan** du behålla eller ta bort [kolumner med metoderna keep_columns()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) [och drop_columns().](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-)

Om du vill filtrera bort rader efter ett specifikt kolumnvärde i en TabularDataset använder du [metoden filter()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (förhandsversion). 

I följande exempel returneras en oregistrerad datauppsättning baserat på de angivna uttrycken.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**I FileDatasets** motsvarar varje rad en sökväg till en fil, så filtrering efter kolumnvärde är inte användbart. Men du kan [filtrera() ut](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) rader efter metadata som CreationTime, Storlek osv.

I följande exempel returneras en oregistrerad datauppsättning baserat på de angivna uttrycken.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

**Märkta datauppsättningar som** skapas från [dataetikettprojekt är](how-to-create-labeling-projects.md) ett specialfall. Dessa datauppsättningar är en typ av TabularDataset som består av bildfiler. För dessa typer av datauppsättningar kan du [filtrera()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) bilder efter metadata och efter kolumnvärden som `label` och `image_details` .

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

### <a name="partition-data-preview"></a>Partitionera data (förhandsversion)

Du kan partitionera en datauppsättning genom att `partitions_format` inkludera parametern när du skapar en TabularDataset eller FileDataset. 

> [!IMPORTANT]
> Att skapa datamängdspartitioner är en experimentell förhandsgranskningsfunktion som kan ändras när som helst. [](/python/api/overview/azure/ml/#stable-vs-experimental) 

När du partitionera en datauppsättning extraheras partitionsinformationen för varje filsökväg till kolumner baserat på det angivna formatet. Formatet ska börja från positionen för den första partitionsnyckeln till slutet av filsökvägen. 

Till exempel, med tanke på sökvägen där partitionen är efter avdelningsnamn och tid, skapar strängkolumnen Department med värdet Accounts och `../Accounts/2019/01/01/data.jsonl` `partition_format='/{Department}/{PartitionDate:yyyy/MM/dd}/data.jsonl'` datetime-kolumnen PartitionDate med värdet `2019-01-01` .

Om dina data redan har befintliga partitioner och du vill bevara det formatet, inkluderar du parametern i metoden `partitioned_format` för att skapa en [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true--partition-format-none-) FileDataset. 

Om du vill skapa en TabularDataset som bevarar befintliga partitioner inkluderar du `partitioned_format` parametern [i from_parquet_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) eller [metoden from_delimited_files().](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false--empty-as-string-false--encoding--utf8--)

I följande exempel:
* Skapar en FileDataset från partitionerade filer.
* Hämtar partitionsnycklarna
* Skapar en ny indexerad FileDataset med hjälp av
 
```Python

file_dataset = Dataset.File.from_files(data_paths, partition_format = '{userid}/*.wav')
ds.register(name='speech_dataset')

# access partition_keys
indexes = file_dataset.partition_keys # ['userid']

# get all partition key value pairs should return [{'userid': 'user1'}, {'userid': 'user2'}]
partitions = file_dataset.get_partition_key_values()


partitions = file_dataset.get_partition_key_values(['userid'])
# return [{'userid': 'user1'}, {'userid': 'user2'}]

# filter API, this will only download data from user1/ folder
new_file_dataset = file_dataset.filter(ds['userid'] == 'user1').download()
```

Du kan också skapa en ny partitionsstruktur för TabularDatasets med [partitions_by().](/python/api/azureml-core/azureml.data.tabulardataset#partition-by-partition-keys--target--name-none--show-progress-true--partition-as-file-dataset-false-)

```Python

 dataset = Dataset.get_by_name('test') # indexed by country, state, partition_date

# call partition_by locally
new_dataset = ds.partition_by(name="repartitioned_ds", partition_keys=['country'], target=DataPath(datastore, "repartition"))
partition_keys = new_dataset.partition_keys # ['country']
```

>[!IMPORTANT]
> TabularDataset-partitioner kan också tillämpas Azure Machine Learning pipelines som indata till ParallelRunStep i många modellprogram. Se ett exempel i dokumentationen [för acceleratorn Många modeller.](https://github.com/microsoft/solution-accelerator-many-models/blob/master/01_Data_Preparation.ipynb)

## <a name="explore-data"></a>Utforska data

När du är klar med dataförändring kan du registrera din datauppsättning och sedan läsa in den i anteckningsboken för datagranskning innan modellträningen. [](#register-datasets)

För FileDatasets kan  du  antingen montera eller ladda ned datauppsättningen och använda python-biblioteken som du normalt använder för datagranskning. [Läs mer om att montera eller ladda ned](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

För TabularDatasets använder du metoden [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) för att visa dina data i en dataram. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Överlevde|Pclass|Name|Sex|Ålder|SibSp|Parch|Biljett|Biljettpris|Stuga|Inlett
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falskt|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7.2500||S
1|2|Sant|1|Cumings, Fru John Bradley (Edit Briggs Th...|kvinna|38.0|1|0|PC 17599|71.2833|C85|C
2|3|Sant|3|Henen, Miss. Laina|kvinna|26,0|0|0|STON/O2. 3101282|7.9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Skapa en datauppsättning från Pandas DataFrame

Om du vill skapa en TabularDataset från en Pandas-dataram i minnet skriver du data till en lokal fil, till exempel en csv, och skapar datauppsättningen från filen. Följande kod visar det här arbetsflödet.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Skapa och registrera en TabularDataset från en minnes-spark eller pandas-dataram med en enda metod med metoder för offentlig [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) förhandsversion, och [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Dessa registreringsmetoder är [experimentella](/python/api/overview/azure/ml/#stable-vs-experimental) förhandsgranskningsfunktioner och kan ändras när som helst. 
> 
>  Dessa metoder laddar upp data till din underliggande lagring och medför därför lagringskostnader. 

## <a name="register-datasets"></a>Registrera datauppsättningar

Registrera dina datauppsättningar med en arbetsyta för att slutföra skapandeprocessen. Använd metoden [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) för att registrera datauppsättningar med din arbetsyta för att dela dem med andra och återanvända dem i olika experiment på din arbetsyta:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Skapa datauppsättningar med Azure Resource Manager

Det finns många mallar på [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) som kan användas för att skapa datauppsättningar.

Information om hur du använder dessa mallar finns i [Använda en Azure Resource Manager för att skapa en arbetsyta för Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Skapa datauppsättningar från Azure Open Datasets

[Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) offentliga datauppsättningar som du kan använda för att lägga till scenariospecifika funktioner i maskininlärningslösningar för mer exakta modeller. Datauppsättningarna innehåller data från den offentliga domänen för väder, folkräkning, helgdagar, allmän säkerhet och plats som hjälper dig att träna maskininlärningsmodeller och förbättra prediktiva lösningar. Open Datasets finns i molnet på Microsoft Azure och ingår i både SDK och Studio.

Lär dig hur du [skapar Azure Machine Learning datauppsättningar från Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Träna med datauppsättningar

Använd dina datauppsättningar i dina maskininlärningsexperiment för att träna ML-modeller. [Läs mer om hur du tränar med datauppsättningar](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Versionsdatauppsättningar

Du kan registrera en ny datauppsättning med samma namn genom att skapa en ny version. En datauppsättningsversion är ett sätt att bokmärka tillståndet för dina data så att du kan tillämpa en specifik version av datauppsättningen för experimentering eller framtida reproduktion. Läs mer om [datauppsättningsversioner.](how-to-version-track-datasets.md)
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Nästa steg

* Lär [dig hur du tränar med datauppsättningar](how-to-train-with-datasets.md).
* Använd automatiserad [maskininlärning för att träna med TabularDatasets](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Fler exempel på datauppsättningsträning finns i [notebook-exempelanteckningsböckerna.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)
