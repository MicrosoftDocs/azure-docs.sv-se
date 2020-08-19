---
title: Skapa Azure Machine Learning data uppsättningar för att få åtkomst till data
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar Azure Machine Learning data uppsättningar för att komma åt dina data för att köra maskin inlärnings experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 51e31977a5f31777252920f9cd18bf069bad6d1e
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507203"
---
# <a name="create-azure-machine-learning-datasets"></a>Skapa Azure Machine Learning-datamängder

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du skapar Azure Machine Learning data uppsättningar för att komma åt data för dina lokala eller fjärranslutna experiment. Information om var data uppsättningar passar i Azure Machine Learning programmets övergripande data åtkomst arbets flöde finns i artikeln [säker åtkomst till data](concept-data.md#data-workflow) .

Genom att skapa en datamängd skapar du en referens till datakällans plats, tillsammans med en kopia av dess metadata. Eftersom data behålls på den befintliga platsen debiteras du ingen extra lagrings kostnad och riskerar inte att skydda dina data källor. Även data uppsättningar är Lazy utvärderas, vilket bidrar till prestanda hastigheten för arbets flödet. Du kan skapa data uppsättningar från data lager, offentliga URL: er och [öppna data uppsättningar i Azure](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Med Azure Machine Learning data uppsättningar kan du:

* Behåll en enda kopia av data i din lagring som refereras till av data uppsättningar.

* Få sömlös åtkomst till data under modell träning utan att behöva oroa dig om anslutnings strängar eller data Sök vägar. [Lär dig mer om hur du tränar med data uppsättningar](how-to-train-with-datasets.md).

* Dela data och samar beta med andra användare.

## <a name="prerequisites"></a>Krav

Om du vill skapa och arbeta med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller paketet azureml-DataSets.

    * Skapa en [Azure Machine Learning beräknings instans](concept-compute-instance.md#managing-a-compute-instance), som är en helt konfigurerad och hanterad utvecklings miljö som innehåller integrerade antecknings böcker och SDK redan har installerats.

    **ELLER**

    * Arbeta med din egen Jupyter-anteckningsbok och installera SDK: n själv med [de här anvisningarna](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet, som endast är kompatibelt med 64-bitars python. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) och CentOS (7).

## <a name="compute-size-guidance"></a>Vägledning för beräknings storlek

När du skapar en data uppsättning granskar du bearbetnings kraften för bearbetningen och storleken på dina data i minnet. Storleken på dina data i lagringen är inte samma som storleken på data i en dataframe. Till exempel kan data i CSV-filer utökas upp till 10X i en dataframe, så en CSV-fil på 1 GB kan bli 10 GB i en dataframe. 

Om dina data är komprimerade kan de utökas ytterligare. 20 GB relativt sparse-data som lagras i ett komprimerat Parquet-format kan expanderas till ~ 800 GB i minnet. Eftersom Parquet-filer lagrar data i ett kolumn format, om du bara behöver hälften av kolumnerna, behöver du bara läsa in ~ 400 GB i minnet.

[Läs mer om hur du optimerar data bearbetning i Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Datamängdstyper

Det finns två typer av data uppsättningar, baserat på hur användarna använder dem i utbildningen. FileDatasets och TabularDatasets. Båda typerna kan användas i Azure Machine Learning utbildnings arbets flöden som innefattar, uppskattningar, AutoML, hyperDrive och pipeliner. 

### <a name="filedataset"></a>FileDataset

En [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) refererar till en eller flera filer i dina data lager eller offentliga URL: er. Om dina data redan har rensats och är redo att användas i övnings experiment, kan du [Hämta eller montera](how-to-train-with-datasets.md#mount-vs-download) filerna i beräkningen som ett FileDataset-objekt. 

Vi rekommenderar FileDatasets för dina Machine Learning-arbetsflöden, eftersom källfilerna kan vara i valfritt format, vilket möjliggör en större mängd maskin inlärnings scenarier, inklusive djup inlärning.

Skapa en FileDataset med [python SDK](#create-a-filedataset) eller [Azure Machine Learning Studio](#create-datasets-in-the-studio)

### <a name="tabulardataset"></a>TabularDataset

En [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa den angivna filen eller listan med filer. Detta ger dig möjlighet att materialisera data till en Pandas-eller Spark-DataFrame så att du kan arbeta med välbekanta data förberedelser och utbildnings bibliotek utan att behöva lämna din bärbara dator. Du kan skapa ett `TabularDataset` objekt från. csv-,. tsv-,. Parquet-,. JSON-filer och från [SQL-frågeresultat](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Med TabularDatasets kan du ange en tidstämpel från en kolumn i data eller från där Sök vägs data lagras för att aktivera en tids serie egenskap. Den här specifikationen möjliggör enkel och effektiv filtrering med tiden. Ett exempel finns i [tabell Time Series-relaterad API-demo med NOAA väderleks data](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Skapa en TabularDataset med [python SDK](#create-a-tabulardataset) eller [Azure Machine Learning Studio](#create-datasets-in-the-studio).

>[!NOTE]
> AutoML-arbetsflöden som genereras via Azure Machine Learning Studio stöder för närvarande endast TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Åtkomst till data uppsättningar i ett virtuellt nätverk

Om din arbets yta finns i ett virtuellt nätverk måste du konfigurera data uppsättningen för att hoppa över verifieringen. Mer information om hur du använder data lager och data uppsättningar i ett virtuellt nätverk finns i [nätverks isolering under träning &s härledning med privata virtuella nätverk](how-to-enable-virtual-network.md#use-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-via-the-sdk"></a>Skapa data uppsättningar via SDK

 För att data ska kunna nås av Azure Machine Learning måste data uppsättningar skapas från sökvägar i [Azure-datalager](how-to-access-data.md) eller offentliga webb adresser. 

Skapa data uppsättningar från ett [Azure-datalager](how-to-access-data.md) med python SDK:

1. Kontrol lera att du har `contributor` eller `owner` åtkomst till registrerade Azure-datalager.

2. Skapa data uppsättningen genom att referera till sökvägar i data lagret. Du kan skapa en data uppsättning från flera sökvägar i flera data lager. Det finns ingen hård gräns för antalet filer eller data storlekar som du kan skapa en data uppsättning från. 

> [!Note]
> För varje data Sök väg skickas några begär anden till lagrings tjänsten för att kontrol lera om det pekar på en fil eller mapp. Den här omkostnaderna kan leda till försämrade prestanda eller problem. En data uppsättning som refererar till en mapp med 1000 filer inuti betraktas som en referens till en data Sök väg. Vi rekommenderar att du skapar en data uppsättning som refererar mindre än 100 sökvägar i data lager för optimala prestanda.

### <a name="create-a-filedataset"></a>Skapa en FileDataset

Använd [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) metoden i `FileDatasetFactory` klassen för att läsa in filer i valfritt format och för att skapa en oregistrerad FileDataset. 

Om lagrings platsen ligger bakom ett virtuellt nätverk eller en brand vägg anger du parametern `validate=False` i din `from_files()` metod. Detta kringgår det första verifierings steget och garanterar att du kan skapa din data uppsättning från dessa säkra filer. Lär dig mer om hur du använder [data lager och data uppsättningar i ett virtuellt nätverk](how-to-enable-virtual-network.md#use-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Använd [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) -metoden i- `TabularDatasetFactory` klassen för att läsa filer i CSV-eller TSV-format och för att skapa en oregistrerad TabularDataset. Om du läser från flera filer aggregeras resultaten i en tabell representation. 

Om lagrings platsen ligger bakom ett virtuellt nätverk eller en brand vägg anger du parametern `validate=False` i din `from_delimited_files()` metod. Detta kringgår det första verifierings steget och garanterar att du kan skapa din data uppsättning från dessa säkra filer. Lär dig mer om hur du använder [data lager och data uppsättningar i ett virtuellt nätverk](how-to-enable-virtual-network.md#use-datastores-and-datasets).

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

Som standard härleds kolumn data typer automatiskt när du skapar en TabularDataset. Om de härledda typerna inte matchar dina förväntningar kan du ange kolumn typer med hjälp av följande kod. Parametern `infer_column_type` kan bara användas för data uppsättningar som skapats från avgränsade filer. [Läs mer om vilka data typer som stöds](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Tabbindex|PassengerId|Överlevt|Pclass|Namn|Sex|Ålder|SibSp|Parch|Biljett|Resa|Bagage|Har avlastat
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falskt|3|Braund, Mr. Owen Harris|man|22,0|1|0|A/5 21171|7,2500||S
1|2|Sant|1|Cumings, fru. John Bradley (Florence Briggs to...|kvinna|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Sant|3|Heikkinen, saknar. Laina|kvinna|26,0|0|0|STON/O2. 3101282|7,9250||S

### <a name="create-a-dataset-from-pandas-dataframe"></a>Skapa en data uppsättning från Pandas dataframe

Om du vill skapa en TabularDataset från en i minnet Pandas dataframe skriver du data till en lokal fil, t. ex. en CSV-fil, och skapar din data uppsättning från den filen. Följande kod visar det här arbets flödet.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

## <a name="register-datasets"></a>Registrera data uppsättningar

För att slutföra skapandet av processen registrerar du dina data uppsättningar med en arbets yta. Använd [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metoden för att registrera data uppsättningar med din arbets yta för att dela dem med andra och återanvända dem över experiment på din arbets yta:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

<a name="datasets-ui"></a>
## <a name="create-datasets-in-the-studio"></a>Skapa data uppsättningar i Studio
Följande steg och animering visar hur du skapar en data uppsättning i [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> Data uppsättningar som skapats med Azure Machine Learning Studio registreras automatiskt på arbets ytan.

![Skapa en data uppsättning med användar gränssnittet](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Så här skapar du en data uppsättning i Studio:
1. Logga in på https://ml.azure.com .
1. Välj **data uppsättningar** i avsnittet **till gångar** i det vänstra fönstret. 
1. Välj **skapa data uppsättning** för att välja källa för din data uppsättning. Källan kan vara lokala filer, ett data lager eller offentliga URL: er.
1. Välj **tabell** eller **fil** för data uppsättnings typ.
1. Välj **Nästa** för att öppna formuläret **data lager och fil markering** . I det här formuläret väljer du var du vill behålla din data uppsättning när du har skapat den, samt hur du väljer vilka datafiler som ska användas för data uppsättningen. 
    1. Aktivera hoppa över verifiering om dina data finns i ett virtuellt nätverk. Läs mer om [isolering av virtuella nätverk och sekretess](how-to-enable-virtual-network.md#machine-learning-studio).
1. Välj **Nästa** för att fylla i **inställningarna och för hands versionen** och **schema** formulären. de fylls i intelligent utifrån filtypen och du kan konfigurera data uppsättningen ytterligare innan du skapar den här typen av formulär. 
1. Klicka på **Nästa** för att granska formuläret **Bekräfta Detaljer** . Kontrol lera dina val och skapa en valfri data profil för din data uppsättning. Läs mer om [data profilering](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Välj **skapa** för att slutföra skapandet av data uppsättningen.

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
* Använd automatisk maskin inlärning för att [träna med TabularDatasets](https://aka.ms/automl-dataset).
* Fler exempel på data uppsättnings utbildning finns i [exempel antecknings böckerna](https://aka.ms/dataset-tutorial).
