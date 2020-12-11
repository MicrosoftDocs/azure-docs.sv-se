---
title: Skapa Azure Machine Learning data uppsättningar för att få åtkomst till data
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar Azure Machine Learning data uppsättningar för att komma åt dina data för att köra maskin inlärnings experiment.
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
ms.openlocfilehash: 28e70a5d5a6ac4cd51f5ed3fc85afd47a5af68d8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033280"
---
# <a name="create-azure-machine-learning-datasets"></a>Skapa Azure Machine Learning-datamängder



I den här artikeln får du lära dig hur du skapar Azure Machine Learning data uppsättningar för att komma åt data för dina lokala eller fjärrexperiment med Azure Machine Learning python SDK. Information om var data uppsättningar passar i Azure Machine Learning programmets övergripande data åtkomst arbets flöde finns i artikeln [säker åtkomst till data](concept-data.md#data-workflow) .

Genom att skapa en datamängd skapar du en referens till datakällans plats, tillsammans med en kopia av dess metadata. Eftersom data behålls på den befintliga platsen debiteras du ingen extra lagrings kostnad och riskerar inte att skydda dina data källor. Även data uppsättningar är Lazy utvärderas, vilket bidrar till prestanda hastigheten för arbets flödet. Du kan skapa data uppsättningar från data lager, offentliga URL: er och [öppna data uppsättningar i Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

För en låg kod upplevelse [skapar du Azure Machine Learning data uppsättningar med Azure Machine Learning Studio.](how-to-connect-data-ui.md#create-datasets)

Med Azure Machine Learning data uppsättningar kan du:

* Behåll en enda kopia av data i din lagring som refereras till av data uppsättningar.

* Få sömlös åtkomst till data under modell träning utan att behöva oroa dig om anslutnings strängar eller data Sök vägar. [Lär dig mer om hur du tränar med data uppsättningar](how-to-train-with-datasets.md).

* Dela data och samar beta med andra användare.

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och arbeta med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), som innehåller paketet azureml-DataSets.

    * Skapa en [Azure Machine Learning beräknings instans](how-to-create-manage-compute-instance.md), som är en helt konfigurerad och hanterad utvecklings miljö som innehåller integrerade antecknings böcker och SDK redan har installerats.

    **OR**

    * Arbeta med din egen Jupyter-anteckningsbok och installera SDK: n själv med [de här anvisningarna](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

> [!NOTE]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) paketet, som endast är kompatibelt med 64-bitars python. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) och CentOS (7). Om du använder distributioner som inte stöds följer du [den här guiden](/dotnet/core/install/linux) för att installera .net Core 2,1 för att gå vidare. 

## <a name="compute-size-guidance"></a>Vägledning för beräknings storlek

När du skapar en data uppsättning granskar du bearbetnings kraften för bearbetningen och storleken på dina data i minnet. Storleken på dina data i lagringen är inte samma som storleken på data i en dataframe. Till exempel kan data i CSV-filer utökas upp till 10X i en dataframe, så en CSV-fil på 1 GB kan bli 10 GB i en dataframe. 

Om dina data är komprimerade kan de utökas ytterligare. 20 GB relativt sparse-data som lagras i ett komprimerat Parquet-format kan expanderas till ~ 800 GB i minnet. Eftersom Parquet-filer lagrar data i ett kolumn format, om du bara behöver hälften av kolumnerna, behöver du bara läsa in ~ 400 GB i minnet.

[Läs mer om hur du optimerar data bearbetning i Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Datamängdstyper

Det finns två typer av data uppsättningar, baserat på hur användarna använder dem i utbildningen. FileDatasets och TabularDatasets. Båda typerna kan användas i Azure Machine Learning utbildnings arbets flöden som innefattar, uppskattningar, AutoML, hyperDrive och pipeliner. 

### <a name="filedataset"></a>FileDataset

En [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset?preserve-view=true&view=azure-ml-py) refererar till en eller flera filer i dina data lager eller offentliga URL: er. Om dina data redan har rensats och är redo att användas i övnings experiment, kan du [Hämta eller montera](how-to-train-with-datasets.md#mount-vs-download) filerna i beräkningen som ett FileDataset-objekt. 

Vi rekommenderar FileDatasets för dina Machine Learning-arbetsflöden, eftersom källfilerna kan vara i valfritt format, vilket möjliggör en större mängd maskin inlärnings scenarier, inklusive djup inlärning.

Skapa en FileDataset med [python SDK](#create-a-filedataset) eller [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

En [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Detta ger dig möjlighet att materialisera data till en Pandas-eller Spark-DataFrame så att du kan arbeta med välbekanta data förberedelser och utbildnings bibliotek utan att behöva lämna din bärbara dator. Du kan skapa ett `TabularDataset` objekt från. csv-,. tsv-,. Parquet-,. JSON-filer och från [SQL-frågeresultat](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Med TabularDatasets kan du ange en tidstämpel från en kolumn i data eller från där Sök vägs data lagras för att aktivera en tids serie egenskap. Den här specifikationen möjliggör enkel och effektiv filtrering med tiden. Ett exempel finns i [tabell Time Series-relaterad API-demo med NOAA väderleks data](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Skapa en TabularDataset med [python SDK](#create-a-tabulardataset) eller [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> AutoML-arbetsflöden som genereras via Azure Machine Learning Studio stöder för närvarande endast TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Åtkomst till data uppsättningar i ett virtuellt nätverk

Om din arbets yta finns i ett virtuellt nätverk måste du konfigurera data uppsättningen för att hoppa över verifieringen. Mer information om hur du använder data lager och data uppsättningar i ett virtuellt nätverk finns i [skydda en arbets yta och tillhör ande resurser](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets"></a>Skapa datauppsättningar

För att data ska kunna nås av Azure Machine Learning måste data uppsättningar skapas från sökvägar i [Azure-datalager](how-to-access-data.md) eller offentliga webb adresser. 

Skapa data uppsättningar från ett [Azure-datalager](how-to-access-data.md) med python SDK:

1. Kontrol lera att du har `contributor` eller `owner` åtkomst till registrerade Azure-datalager.

2. Skapa data uppsättningen genom att referera till sökvägar i data lagret. Du kan skapa en data uppsättning från flera sökvägar i flera data lager. Det finns ingen hård gräns för antalet filer eller data storlekar som du kan skapa en data uppsättning från. 

> [!NOTE]
> För varje data Sök väg skickas några begär anden till lagrings tjänsten för att kontrol lera om det pekar på en fil eller mapp. Den här omkostnaderna kan leda till försämrade prestanda eller problem. En data uppsättning som refererar till en mapp med 1000 filer inuti betraktas som en referens till en data Sök väg. Vi rekommenderar att du skapar en data uppsättning som refererar mindre än 100 sökvägar i data lager för optimala prestanda.

### <a name="create-a-filedataset"></a>Skapa en FileDataset

Använd [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) metoden i `FileDatasetFactory` klassen för att läsa in filer i valfritt format och för att skapa en oregistrerad FileDataset. 

Om lagrings platsen ligger bakom ett virtuellt nätverk eller en brand vägg anger du parametern `validate=False` i din `from_files()` metod. Detta kringgår det första verifierings steget och garanterar att du kan skapa din data uppsättning från dessa säkra filer. Lär dig mer om hur du [använder data lager och data uppsättningar i ett virtuellt nätverk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Om du vill återanvända och dela data uppsättningar mellan experiment i arbets ytan [registrerar du din data uppsättning](#register-datasets). 

> [!TIP] 
> Ladda upp filer från en lokal katalog och skapa en FileDataset i en enda metod med den offentliga förhands gransknings metoden [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Den här metoden är en [experimentell](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) förhands gransknings funktion och kan ändras när som helst. 
> 
>  Med den här metoden överförs data till det underliggande lagrings utrymmet, och därför debiteras lagrings kostnaderna. 
### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Använd [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) -metoden i- `TabularDatasetFactory` klassen för att läsa filer i CSV-eller TSV-format och för att skapa en oregistrerad TabularDataset. Om du läser från flera filer aggregeras resultaten i en tabell representation. 

Om lagrings platsen ligger bakom ett virtuellt nätverk eller en brand vägg anger du parametern `validate=False` i din `from_delimited_files()` metod. Detta kringgår det första verifierings steget och garanterar att du kan skapa din data uppsättning från dessa säkra filer. Lär dig mer om hur du använder [data lager och data uppsättningar i ett virtuellt nätverk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

Följande kod hämtar den befintliga arbets ytan och önskat data lager efter namn. Och skickar sedan data lagret och fil platserna till `path` parametern för att skapa en ny TabularDataset `weather_ds` .

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

Som standard härleds kolumn data typer automatiskt när du skapar en TabularDataset. Om de härledda typerna inte matchar dina förväntningar kan du ange kolumn typer med hjälp av följande kod. Parametern `infer_column_type` kan bara användas för data uppsättningar som skapats från avgränsade filer. [Läs mer om vilka data typer som stöds](/python/api/azureml-core/azureml.data.dataset_factory.datatype?preserve-view=true&view=azure-ml-py).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Tabbindex|PassengerId|Överlevt|Pclass|Name|Sex|Ålder|SibSp|Parch|Biljett|Resa|Bagage|Har avlastat
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falskt|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Sant|1|Cumings, fru. John Bradley (Florence Briggs to...|kvinna|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Sant|3|Heikkinen, saknar. Laina|kvinna|26,0|0|0|STON/O2. 3101282|7,9250||S

[Registrera din data uppsättning](#register-datasets)för att återanvända och dela data uppsättningar mellan experiment i din arbets yta.

## <a name="create-a-dataset-from-pandas-dataframe"></a>Skapa en data uppsättning från Pandas dataframe

Om du vill skapa en TabularDataset från en i minnet Pandas dataframe skriver du data till en lokal fil, t. ex. en CSV-fil, och skapar din data uppsättning från den filen. Följande kod visar det här arbets flödet.

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
> Skapa och registrera en TabularDataset från en i Memory Spark-eller Pandas-dataframe med en enda metod med offentliga för hands metoder [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods) och [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods) . Dessa register metoder är [experimentella](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) för hands versions funktioner och kan ändras när som helst. 
> 
>  Dessa metoder laddar upp data till din underliggande lagring och resulterar i en lagrings kostnad. 

## <a name="register-datasets"></a>Registrera data uppsättningar

För att slutföra skapandet av processen registrerar du dina data uppsättningar med en arbets yta. Använd [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) metoden för att registrera data uppsättningar med din arbets yta för att dela dem med andra och återanvända dem över experiment på din arbets yta:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Skapa data uppsättningar med Azure Resource Manager

Det finns ett antal mallar i [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) som kan användas för att skapa data uppsättningar.

Information om hur du använder dessa mallar finns i [använda en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-with-azure-open-datasets"></a>Skapa data uppsättningar med Azure Open-datauppsättningar

[Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/) är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner till maskin inlärnings lösningar för mer exakta modeller. Data uppsättningar omfattar data från offentliga domäner för väder, räkning, helger, offentlig säkerhet och plats som hjälper dig att träna maskin inlärnings modeller och utöka förutsägelse lösningar. Öppna data uppsättningar finns i molnet på Microsoft Azure och ingår i både SDK och Studio.

Lär dig hur du skapar [Azure Machine Learning data uppsättningar från Azure Open data uppsättningar](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Träna med datauppsättningar

Använd dina data uppsättningar i Machine Learning-experiment för att träna ML-modeller. [Lär dig mer om hur du tränar med data uppsättningar](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>Versions data uppsättningar

Du kan registrera en ny data uppsättning med samma namn genom att skapa en ny version. En data uppsättnings version är ett sätt att lägga till en data uppsättnings tillstånd så att du kan använda en angiven version av data uppsättningen för experimentering eller framtida åter givning. Läs mer om [data uppsättnings versioner](how-to-version-track-datasets.md).
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

* Lär dig [hur du tränar med data uppsättningar](how-to-train-with-datasets.md).
* Använd automatisk maskin inlärning för att [träna med TabularDatasets](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Fler exempel på data uppsättnings utbildning finns i [exempel antecknings böckerna](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
