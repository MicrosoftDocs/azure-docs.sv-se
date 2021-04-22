---
title: Träna med datauppsättningar för maskininlärning
titleSuffix: Azure Machine Learning
description: Lär dig hur du gör dina data tillgängliga för din lokala beräkning eller fjärrbearbetning för modellträning med Azure Machine Learning datauppsättningar.
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
ms.openlocfilehash: 0b2bb49863e07e6f06512e868ed12ecf00cc11c2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872397"
---
# <a name="train-models-with-azure-machine-learning-datasets"></a>Träna modeller med Azure Machine Learning datauppsättningar 

I den här artikeln får du lära dig hur du arbetar [med Azure Machine Learning för att](/python/api/azureml-core/azureml.core.dataset%28class%29) träna maskininlärningsmodeller.  Du kan använda datauppsättningar i ditt lokala eller fjärranslutna beräkningsmål utan att behöva bekymra dig om anslutningssträngar eller datasökvägar. 

Azure Machine Learning datauppsättningar ger en sömlös integrering Azure Machine Learning med träningsfunktioner som [ScriptRunConfig,](/python/api/azureml-core/azureml.core.scriptrunconfig) [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive) [och Azure Machine Learning pipelines](./how-to-create-machine-learning-pipelines.md).

Om du inte är redo att göra dina data tillgängliga för modellträning, men vill läsa in dina data i anteckningsboken för datagranskning, kan du läsa mer i utforska data i [datamängden](how-to-create-register-datasets.md#explore-data). 

## <a name="prerequisites"></a>Förutsättningar

Om du vill skapa och träna med datauppsättningar behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnadsfria eller betalda versionen Azure Machine Learning](https://aka.ms/AMLFree) idag.

* En [Azure Machine Learning arbetsyta](how-to-manage-workspace.md).

* Den [Azure Machine Learning SDK för Python installerad](/python/api/overview/azure/ml/install) (>= 1.13.0), som innehåller `azureml-datasets` paketet.

> [!Note]
> Vissa datamängdsklasser har beroenden på [paketet azureml-dataprep.](https://pypi.org/project/azureml-dataprep/) För Linux-användare stöds dessa klasser endast på följande distributioner: Red Hat Enterprise Linux, Ubuntu, Fedora och CentOS.

## <a name="consume-datasets-in-machine-learning-training-scripts"></a>Använda datauppsättningar i maskininlärningsträningsskript

Om du har strukturerade data som ännu inte har registrerats som en datauppsättning skapar du en TabularDataset och använder den direkt i ditt träningsskript för ditt lokala eller fjärranslutna experiment.

I det här exemplet skapar du en oregistrerad [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) och anger den som ett skriptargument i [ScriptRunConfig-objektet](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) för träning. Om du vill återanvända denna TabularDataset med andra experiment på arbetsytan kan du se [hur du registrerar datauppsättningar på din arbetsyta.](how-to-create-register-datasets.md#register-datasets)

### <a name="create-a-tabulardataset"></a>Skapa en TabularDataset

Följande kod skapar en oregistrerad TabularDataset från en webbadress.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-objekt ger möjlighet att läsa in data i tabularDataset till en Pandas- eller Spark DataFrame så att du kan arbeta med välbekanta bibliotek för förberedelse av data och träning utan att behöva lämna notebook-datorn.

### <a name="access-dataset-in-training-script"></a>Komma åt datauppsättningen i träningsskriptet

Följande kod konfigurerar ett skriptargument `--input-data` som du anger när du konfigurerar din träningskörning (se nästa avsnitt). När tabelldatauppsättningen skickas som argumentvärde matchar Azure ML det till ID för datauppsättningen, som du sedan kan använda för att komma åt datauppsättningen i ditt träningsskript (utan att behöva hårdkoda namnet eller ID:t för datauppsättningen i skriptet). Den använder sedan metoden [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) för att läsa in datauppsättningen i en Pandas-dataram för ytterligare datagranskning och förberedelse före träning.

> [!Note]
> Om din ursprungliga datakälla innehåller NaN, tomma strängar eller tomma värden ersätts dessa värden som `to_pandas_dataframe()` ett *Null-värde* när du använder .

Om du behöver läsa in förberedda data i en ny datauppsättning från en minnes minnesbelastad Pandas-dataram skriver du data till en lokal fil, till exempel en parquet, och skapar en ny datauppsättning från den filen. Läs mer om [hur du skapar datauppsättningar](how-to-create-register-datasets.md).

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

### <a name="configure-the-training-run"></a>Konfigurera träningskörningen

Ett [ScriptRunConfig-objekt](/python/api/azureml-core/azureml.core.scriptrun) används för att konfigurera och skicka träningskörningen.

Den här koden skapar ett ScriptRunConfig-objekt, `src` , som anger

* En skriptkatalog för dina skript. Alla filer i den här katalogen laddas upp till klusternoderna för körning.
* Träningsskriptet, *train_titanic.py*.
* Indatauppsättningen för träning, `titanic_ds` , som ett skriptargument. Azure ML löser detta med motsvarande ID för datauppsättningen när den skickas till skriptet.
* Beräkningsmålet för körningen.
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

## <a name="mount-files-to-remote-compute-targets"></a>Montera filer till fjärrbearbetningsmål

Om du har ostrukturerade data skapar du en [FileDataset](/python/api/azureml-core/azureml.data.filedataset) och monterar eller laddar ned dina datafiler för att göra dem tillgängliga för fjärrbearbetningsmålet för träning. Lär dig mer om när du ska [använda montera eller ladda ned](#mount-vs-download) för dina fjärranslutna träningsexperiment. 

I följande exempel: 

* Skapar indatafilenDataset, `mnist_ds` , för dina träningsdata.
* Anger var träningsresultat ska skrivas och för att befordra dessa resultat som en FileDataset.
* Monterar indatauppsättningen till beräkningsmålet.

> [!Note]
> Om du använder en anpassad Docker-basavbildning måste du installera Fuse via som ett beroende `apt-get install -y fuse` för att datauppsättningsmonteringen ska fungera. Lär dig hur du [skapar en anpassad byggavbildning.](how-to-deploy-custom-docker-image.md#build-a-custom-base-image)

I notebook-exemplet finns i [Så här konfigurerar du en träningskörning med indata och utdata](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/scriptrun-with-data-input-output/how-to-use-scriptrun.ipynb).

### <a name="create-a-filedataset"></a>Skapa en FileDataset

I följande exempel skapas en oregistrerad FileDataset `mnist_data` från webbadresser. Den här FileDataset är indata för din träningskörning.

Läs mer om [hur du skapar datauppsättningar](how-to-create-register-datasets.md) från andra källor.

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
### <a name="where-to-write-training-output"></a>Var du skriver träningsutdata

Du kan ange var du vill skriva dina träningsresultat med ett [OutputFileDatasetConfig-objekt](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). 

Med OutputFileDatasetConfig-objekt kan du: 

* Montera eller ladda upp utdata från en körning till den molnlagring som du anger.
* Spara utdata som en FileDataset till följande lagringstyper som stöds:
    * Azure-blob
    * Azure-filresurs
    * Azure Data Lake Storage generation 1 och 2
* Spåra datakällan mellan träningskörningar.

Följande kod anger att träningsresultat ska sparas som en FileDataset i `outputdataset` mappen i standardblobdatalagringen, `def_blob_store` . 

```python
from azureml.core import Workspace
from azureml.data import OutputFileDatasetConfig

ws = Workspace.from_config()

def_blob_store = ws.get_default_datastore()
output = OutputFileDatasetConfig(destination=(def_blob_store, 'sample/outputdataset'))
```

### <a name="configure-the-training-run"></a>Konfigurera träningskörningen

Vi rekommenderar att du anger datauppsättningen som ett argument när du monterar `arguments` via -parametern för `ScriptRunConfig` konstruktorn. På så sätt får du datasökvägen (monteringspunkten) i träningsskriptet via argument. På så sätt kan du använda samma träningsskript för lokal felsökning och fjärrträning på valfri molnplattform.

I följande exempel skapas en ScriptRunConfig som skickar FileDataset via `arguments` . När du har skickat körningen monteras de datafiler som datauppsättningen refererar till till beräkningsmålet och träningsresultat sparas i den angivna mappen `mnist_ds` `outputdataset` i standarddatalagringen.

```python
from azureml.core import ScriptRunConfig

input_data= mnist_ds.as_named_input('input').as_mount()# the dataset will be mounted on the remote compute 

src = ScriptRunConfig(source_directory=script_folder,
                      script='dummy_train.py',
                      arguments=[input_data, output],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="simple-training-script"></a>Enkelt träningsskript

Följande skript skickas via ScriptRunConfig. Den läser `mnist_ds ` datauppsättningen som indata och skriver filen till `outputdataset` mappen i standardblobdataarkivet, `def_blob_store` .

```Python
%%writefile $source_directory/dummy_train.py

# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License.
import sys
import os

print("*********************************************************")
print("Hello Azure ML!")

mounted_input_path = sys.argv[1]
mounted_output_path = sys.argv[2]

print("Argument 1: %s" % mounted_input_path)
print("Argument 2: %s" % mounted_output_path)
    
with open(mounted_input_path, 'r') as f:
    content = f.read()
    with open(os.path.join(mounted_output_path, 'output.csv'), 'w') as fw:
        fw.write(content)
```

## <a name="mount-vs-download"></a>Montera jämfört med ladda ned

Montering eller nedladdning av filer av val annat format stöds för datauppsättningar som skapats från Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database och Azure Database for PostgreSQL. 

När du **monterar** en datauppsättning kopplar du de filer som datauppsättningen refererar till till en katalog (monteringspunkt) och gör dem tillgängliga på beräkningsmålet. Montering stöds för Linux-baserade beräkningar, inklusive Azure Machine Learning Compute, virtuella datorer och HDInsight. 

När du **laddar** ned en datauppsättning laddas alla filer som datauppsättningen refererar till ned till beräkningsmålet. Nedladdning stöds för alla beräkningstyper. 

Om skriptet bearbetar alla filer som datauppsättningen refererar till och beräkningsdisken kan passa din fullständiga datauppsättning, rekommenderar vi att du laddar ned för att undvika belastningen på strömmande data från lagringstjänster. Om datastorleken överskrider beräkningsdiskens storlek går det inte att ladda ned. I det här scenariot rekommenderar vi montering eftersom endast de datafiler som används av skriptet läses in vid bearbetningen.

Följande kod monteras i `dataset` temp-katalogen på `mounted_path`

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

## <a name="get-datasets-in-machine-learning-scripts"></a>Hämta datauppsättningar i maskininlärningsskript

Registrerade datauppsättningar är tillgängliga både lokalt och via fjärranslutning i beräkningskluster som Azure Machine Learning beräkning. Om du vill komma åt din registrerade datauppsättning mellan experiment använder du följande kod för att komma åt din arbetsyta och hämta datauppsättningen som användes i den tidigare skickade körningen. Som standard [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset#get-by-name-workspace--name--version--latest--) returnerar metoden i `Dataset` klassen den senaste versionen av datauppsättningen som är registrerad på arbetsytan.

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

## <a name="access-source-code-during-training"></a>Få åtkomst till källkoden under träningen

Azure Blob Storage har högre dataflödeshastighet än en Azure-filresurs och skalas till ett stort antal jobb som startas parallellt. Därför rekommenderar vi att du konfigurerar dina körningar att använda Blob Storage för att överföra källkodsfiler.

Följande kodexempel anger i körningskonfigurationen vilket blobdatalager som ska användas för källkodsöverföringar.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook-exempel

+ Fler exempel och begrepp för datauppsättningar finns i [notebook-datauppsättningen.](https://aka.ms/dataset-tutorial)
+ Se hur du [parametrisar datauppsättningar i dina ML-pipelines.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb)

## <a name="troubleshooting"></a>Felsökning

* **Initieringen av datauppsättningen misslyckades: Tiden** för att vänta på att monteringspunkten ska vara klar har gått ut: 
  * Om du inte har några [](../virtual-network/network-security-groups-overview.md) regler för utgående nätverkssäkerhetsgrupp och använder uppdaterar du och dess beroenden så att de är de senaste för den specifika delversionen. Om du använder den i en körning återskapar du miljön så att den får den senaste korrigeringen `azureml-sdk>=1.12.0` med `azureml-dataset-runtime` korrigeringen. 
  * Om du använder `azureml-sdk<1.12.0` uppgraderar du till den senaste versionen.
  * Om du har utgående NSG-regler kontrollerar du att det finns en regel för utgående trafik som tillåter all trafik för tjänsttaggen `AzureResourceMonitor` .

### <a name="overloaded-azurefile-storage"></a>Överbelastad AzureFile-lagring

Om du får ett felmeddelande `Unable to upload project files to working directory in AzureFile because the storage is overloaded` kan du använda följande lösningar.

Om du använder filresurs för andra arbetsbelastningar, till exempel dataöverföring, rekommenderar vi att du använder blobar så att filresursen kan användas för att skicka körningar. Du kan också dela upp arbetsbelastningen mellan två olika arbetsytor.

### <a name="passing-data-as-input"></a>Skicka data som indata

*  **TypeError: FileNotFound: Ingen** sådan fil eller katalog: Det här felet inträffar om filsökvägen som du anger inte finns där filen finns. Du måste se till att sättet du refererar till filen är konsekvent med var du monterade datauppsättningen på beräkningsmålet. För att säkerställa ett deterministiskt tillstånd rekommenderar vi att du använder den abstrakta sökvägen när du monterar en datauppsättning till ett beräkningsmål. I följande kod monterar vi till exempel datauppsättningen under roten för filsystemet i beräkningsmålet, `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Om du inte inkluderar det inledande snedstrecket "/" måste du ange ett prefix för arbetskatalogen, t.ex. i beräkningsmålet för att ange var du vill att datauppsättningen ska `/mnt/batch/.../tmp/dataset` monteras.


## <a name="next-steps"></a>Nästa steg

* [Träna maskininlärningsmodeller](how-to-configure-auto-train.md#data-source-and-format) automatiskt med TabularDatasets.

* [Träna bildklassificeringsmodeller](https://aka.ms/filedataset-samplenotebook) med FileDatasets.

* [Träna med datauppsättningar med hjälp av pipelines](./how-to-create-machine-learning-pipelines.md).
