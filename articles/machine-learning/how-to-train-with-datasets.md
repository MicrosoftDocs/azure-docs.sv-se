---
title: Träna med Machine Learning-datauppsättningar
titleSuffix: Azure Machine Learning
description: Lär dig hur du gör dina data tillgängliga för din lokala eller fjärrberäkning för modell utbildning med Azure Machine Learning data uppsättningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 15bad877be00e143ce6f6956a4e1f23378c275c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521789"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Träna modeller med Azure Machine Learning data uppsättningar 

I den här artikeln får du lära dig hur du arbetar med [Azure Machine Learning data uppsättningar](/python/api/azureml-core/azureml.core.dataset%28class%29) för att träna maskin inlärnings modeller.  Du kan använda data uppsättningar i det lokala eller fjärranslutna beräknings målet utan att behöva oroa dig om anslutnings strängar eller data Sök vägar. 

Azure Machine Learning data uppsättningar ger en sömlös integrering med Azure Machine Learning inlärnings funktioner som [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig), [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive) och [Azure Machine Learning pipelines](./how-to-create-machine-learning-pipelines.md).

Om du inte är redo att göra dina data tillgängliga för modell utbildning, men vill läsa in data till din bärbara dator för data utforskning, se så här [utforskar du data i din data uppsättning](how-to-create-register-datasets.md#explore-data). 

## <a name="prerequisites"></a>Förutsättningar

För att skapa och träna med data uppsättningar behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](/python/api/overview/azure/ml/install) (>= 1.13.0), som innehåller `azureml-datasets` paketet.

> [!Note]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](/python/api/azureml-dataprep/) paketet. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Använda data uppsättningar i skript för Machine Learning-utbildning

Om du har strukturerade data som ännu inte har registrerats som en data uppsättning skapar du en TabularDataset och använder den direkt i ditt utbildnings skript för ditt lokala eller fjärranslutna experiment.

I det här exemplet skapar du ett oregistrerat [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) och anger det som ett skript argument i ScriptRunConfig-objektet för träning. Om du vill återanvända den här TabularDataset med andra experiment i din arbets yta, se [så här registrerar du data uppsättningar på din arbets yta](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Följande kod skapar ett oregistrerat TabularDataset från en webb-URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-objekt ger möjlighet att läsa in data i din TabularDataset i en Pandas-eller Spark-DataFrame så att du kan arbeta med välbekanta data förberedelse-och utbildnings bibliotek utan att behöva lämna din antecknings bok.

### <a name="access-dataset-in-training-script"></a>Åtkomst data uppsättning i övnings skript

Följande kod konfigurerar ett skript argument `--input-data` som du anger när du konfigurerar din utbildnings körning (se nästa avsnitt). När tabell data uppsättningen skickas som argumentvärdet kommer Azure ML att matcha detta med ID för data uppsättningen, som du sedan kan använda för att få åtkomst till data uppsättningen i ditt utbildnings skript (utan att behöva hårdkoda namnet eller ID: t för data uppsättningen i skriptet). Den använder sedan [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metoden för att läsa in data uppsättningen i en Pandas-dataframe för ytterligare data utforskning och förberedelser innan utbildning.

> [!Note]
> Om den ursprungliga data källan innehåller NaN, tomma strängar eller tomma värden, `to_pandas_dataframe()` ersätts dessa värden som ett *Null* -värde.

Om du behöver läsa in de data som för bereddes i en ny data uppsättning från en minnes intern Pandas-dataframe skriver du data till en lokal fil, t. ex. en Parquet och skapar en ny data uppsättning från den filen. Läs mer om [hur du skapar data uppsättningar](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Konfigurera utbildnings körningen

Ett [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun) -objekt används för att konfigurera och skicka utbildnings körningen.

Den här koden skapar ett ScriptRunConfig-objekt, `src` som anger

* En skript katalog för dina skript. Alla filer i den här katalogen laddas upp till klusternoderna för körning.
* Övnings skriptet *train_titanic. py*.
* Data uppsättningen för indata för träning, `titanic_ds` som ett skript argument. Azure ML kommer att matcha detta med motsvarande ID för data uppsättningen när den skickas till skriptet.
* Compute-målet för körningen.
* Miljön för körningen.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Montera filer på fjärranslutna beräknings mål

Om du har ostrukturerade data kan du skapa en [FileDataset](/python/api/azureml-core/azureml.data.filedataset) och antingen montera eller ladda ned dina datafiler så att de blir tillgängliga för ditt fjärrberäknings mål för utbildning. Lär dig mer om hur du använder [montering jämfört med att ladda ned](#mount-vs-download) för dina experiment. 

I följande exempel skapas en FileDataset och monterar data uppsättningen till Compute-målet genom att skicka den som ett argument till övnings skriptet. 

> [!Note]
> Om du använder en anpassad Docker-bas avbildning måste du installera säkring via `apt-get install -y fuse` som ett beroende för att data uppsättnings montering ska fungera. Lär dig hur du [skapar en anpassad versions avbildning](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Skapa en FileDataset

I följande exempel skapas en oregistrerad FileDataset från webb-URL: er. Lär dig mer om [hur du skapar data uppsättningar](how-to-create-register-datasets.md) från andra källor.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>Konfigurera utbildnings körningen

Vi rekommenderar att du skickar data uppsättningen som ett argument när du monterar via `arguments` parametern för `ScriptRunConfig` konstruktorn. På så sätt får du data Sök vägen (monterings punkten) i utbildnings skriptet via argument. På så sätt kan du använda samma utbildnings skript för lokal fel sökning och fjärran sluten utbildning på vilken moln plattform som helst.

I följande exempel skapas en ScriptRunConfig som passerar i FileDataset via `arguments` . När du har skickat körningen kommer datafiler som refereras till data uppsättningen att `mnist_ds` monteras till beräknings målet.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-data-in-your-training-script"></a>Hämta data i ditt utbildnings skript

Följande kod visar hur du hämtar data i skriptet.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Montering mot nedladdning

Det finns stöd för att montera eller hämta filer av alla format för data uppsättningar som skapats från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. 

När du **monterar** en data uppsättning bifogar du filerna som data uppsättningen refererar till i en katalog (monterings punkt) och gör den tillgänglig på beräknings målet. Montering stöds för Linux-baserade beräkningar, inklusive Azure Machine Learning Compute, Virtual Machines och HDInsight. 

När du **hämtar** en data uppsättning laddas alla filer som refereras till av data uppsättningen ned till beräknings målet. Hämtning stöds för alla beräknings typer. 

Om skriptet bearbetar alla filer som refereras av data uppsättningen, och din beräknings disk kan passa din fullständiga data uppsättning, rekommenderas nedladdningen för att undvika att strömma data strömmas från lagrings tjänsterna. Om data storleken överskrider beräknings disk storleken är det inte möjligt att hämta. I det här scenariot rekommenderar vi montering eftersom endast de datafiler som används av skriptet läses in vid tidpunkten för bearbetningen.

Följande kod monteras `dataset` i Temp-katalogen på `mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="get-datasets-in-machine-learning-scripts"></a>Hämta data uppsättningar i Machine Learning-skript

Registrerade data uppsättningar kan nås både lokalt och via fjärr anslutning på beräknings kluster som Azure Machine Learning Compute. Använd följande kod för att komma åt din arbets yta och hämta den data uppsättning som användes i din tidigare skickade körning för att få åtkomst till din registrerade data uppsättning över experiment. Som standard [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) returnerar metoden i `Dataset` klassen den senaste versionen av data uppsättningen som är registrerad på arbets ytan.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="access-source-code-during-training"></a>Åtkomst käll kod under träning

Azure Blob Storage har högre överföringshastigheter än en Azure-filresurs och kommer att skalas till ett stort antal jobb som startas parallellt. Därför rekommenderar vi att du konfigurerar dina körningar för att använda blob-lagring för överföring av källfiler.

I följande kod exempel anges i den körnings konfiguration som BLOB data lager ska användas för käll kods överföringar.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Exempel på bärbara datorer

+ [Antecknings böckerna för data uppsättningen](https://aka.ms/dataset-tutorial) demonstrerar och utökar koncepten i den här artikeln.
+ Se hur du [parametrize data uppsättningar i dina ml-pipeliner](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="troubleshooting"></a>Felsökning

* **Det gick inte att initiera data uppsättningen: väntar på att en tids gräns för monterings punkten skulle bli klar**: 
  * Om du inte har några utgående regler för [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) och använder `azureml-sdk>=1.12.0` , uppdatera `azureml-dataset-runtime` och dess beroenden som de senaste för den speciella del versionen, eller om du använder den i en körning, måste du återskapa din miljö så att den kan ha den senaste korrigeringen med korrigeringen. 
  * Om du använder `azureml-sdk<1.12.0` uppgraderar du till den senaste versionen.
  * Om du har utgående NSG-regler kontrollerar du att det finns en utgående regel som tillåter all trafik för tjänst tag gen `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Överlagrad AzureFile-lagring

Använd följande lösningar om du får ett fel meddelande `Unable to upload project files to working directory in AzureFile because the storage is overloaded` .

Om du använder fil resurs för andra arbets belastningar, till exempel data överföring, är rekommendationen att använda blobbar så att fil resursen är kostnads fri att användas för att skicka körningar. Du kan också dela upp arbets belastningen mellan två olika arbets ytor.

### <a name="passing-data-as-input"></a>Skicka data som indata

*  **TypeError: FileNotFound: det finns ingen sådan fil eller katalog**: det här felet uppstår om fil Sök vägen som du anger inte är den plats där filen finns. Du måste kontrol lera att det sätt som du refererar till filen är konsekvent med var du monterade data uppsättningen på beräknings målet. För att säkerställa ett deterministiskt tillstånd rekommenderar vi att du använder den abstrakta sökvägen när du monterar en data uppsättning till ett beräknings mål. I följande kod monterar du till exempel data uppsättningen under roten i fil systemet för beräknings målet `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Om du inte tar med det inledande snedstrecket "/" måste du ange prefix för arbets katalogen t. ex. `/mnt/batch/.../tmp/dataset` på beräknings målet för att ange var du vill att data uppsättningen ska monteras.


## <a name="next-steps"></a>Nästa steg

* [Träna maskin inlärnings modeller automatiskt](how-to-auto-train-remote.md) med TabularDatasets.

* [Träna bild klassificerings modeller](https://aka.ms/filedataset-samplenotebook) med FileDatasets.

* [Träna med data uppsättningar med pipelines](./how-to-create-machine-learning-pipelines.md).