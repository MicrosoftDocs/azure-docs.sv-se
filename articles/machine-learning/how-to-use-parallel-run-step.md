---
title: Köra batch-förutsägelser för Big data
titleSuffix: Azure Machine Learning
description: Lär dig hur du kan få inferences asynkront på stora mängder data med hjälp av ParallelRunStep i Azure Machine Learning. ParallelRunStep tillhandahåller parallella bearbetnings funktioner i rutan och optimerar för hög genom strömning, brand-och-glömma-härledning för stora data användnings fall.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: dddb332498f41437eba77d75c38218c58b8c8379
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507122"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Kör batch-härledning på stora mängder data med hjälp av Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln visar hur du kör Azure Machine Learnings modellen parallellt och snabbt utvärderar stora mängder data. 

Inferencing över stora data mängder eller med komplicerade modeller kan vara tids krävande. Med `ParallelRunStep` klassen kan du utföra bearbetning parallellt, vilket kan leda till övergripande resultat snabbare. Även om det är ganska snabbare att köra en enda utvärdering, kan många scenarier (objekt identifiering, video bearbetning, naturligt språk bearbetning osv.) köra många utvärderingar. 

Med är `ParallelRunStep` det enkelt att skala batch-inferences till stora kluster datorer. Sådana kluster kan hantera terabyte av strukturerade eller ostrukturerade data med förbättrad produktivitet och optimerad kostnad.

I den här artikeln får du lära dig följande uppgifter:

> 1. Konfigurera Machine Learning-resurser.
> 1. Konfigurera indata och utdata för batch-datahärledning.
> 1. Förbered den förtränade avbildnings klassificerings modellen baserat på [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) -datauppsättningen. 
> 1.  Skriv ditt härlednings skript.
> 1. Skapa en [pipeline för maskin inlärning](concept-ml-pipelines.md) som innehåller ParallelRunStep och kör batch-härledning på MNIST-testavbildningar. 
> 1. Skicka en ny körnings härledning igen med nya indata och parametrar. 
> 1. Granska resultaten.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* För en guidad snabb start slutför du [installations självstudien](tutorial-1st-experiment-sdk-setup.md) om du inte redan har en Azure Machine Learning-arbetsyta. 

* Information om hur du hanterar din egen miljö och beroenden finns i [instruktionen så här](how-to-configure-environment.md) konfigurerar du din egen lokala miljö.

## <a name="set-up-machine-learning-resources"></a>Konfigurera Machine Learning-resurser

Följande åtgärder konfigurerar de Machine Learning-resurser som du behöver för att köra en pipeline för batch-härledning:

- Anslut till en arbets yta.
- Skapa eller koppla en befintlig beräknings resurs.

### <a name="configure-workspace"></a>Konfigurera arbetsyta

Skapa ett arbetsyteobjekt från den befintliga arbetsytan. `Workspace.from_config()` läser config.jspå filen och läser in informationen i ett objekt med namnet ws.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Det här kodfragmentet förväntar sig att arbets ytans konfiguration sparas i den aktuella katalogen eller dess överordnade. Mer information om hur du skapar en arbets yta finns i [skapa och hantera Azure Machine Learning arbets ytor](how-to-manage-workspace.md). Mer information om hur du sparar konfigurationen till filen finns i [skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Skapa ett beräknings mål

I Azure Machine Learning refererar *beräkning* (eller *beräknings mål*) till de datorer eller kluster som utför beräknings stegen i din Machine Learning-pipeline. Kör följande kod för att skapa ett CPU-baserat [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) -mål.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Konfigurera indata och utdata

### <a name="create-a-datastore-with-sample-images"></a>Skapa ett data lager med exempel bilder

Hämta utvärderings versionen av MNIST från den offentliga BLOB-behållaren `sampledata` på ett konto med namnet `pipelinedata` . Skapa ett data lager med namnet `mnist_datastore` som pekar på den här behållaren. I följande anrop till `register_azure_blob_container` anger `overwrite` flaggan för att `True` skriva över alla data lager som skapades tidigare med det namnet. 

Du kan ändra det här steget så att det pekar på din BLOB-behållare genom att ange dina egna värden för `datastore_name` , `container_name` och `account_name` .

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Ange sedan standard data lagret för arbets ytan som utdata-datalager. Du använder den för att få en utmatnings effekt.

När du skapar din arbets yta är [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)   och [blob-lagringen](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)   kopplade till arbets ytan som standard. Azure Files är standard data lagret för en arbets yta, men du kan också använda Blob Storage som ett data lager. Mer information finns i [alternativ för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Skapa data inmatningar

Indata för batch-härledning är de data som du vill partitionera för parallell bearbetning. En pipeline för batch-härledning accepterar data indata via [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) .

`Dataset` är för att utforska, transformera och hantera data i Azure Machine Learning. Det finns två typer: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) och [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) . I det här exemplet ska du använda `FileDataset` som indata. `FileDataset` ger dig möjlighet att ladda ned eller montera filerna i din beräkning. Genom att skapa en data uppsättning skapar du en referens till data käll platsen. Om du har tillämpat transformeringar av under inställningar till data uppsättningen lagras de även i data uppsättningen. Data behålls på den befintliga platsen, så ingen extra lagrings kostnad uppstår.

Mer information om Azure Machine Learning data uppsättningar finns i [skapa och komma åt data uppsättningar (för hands version)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

För att kunna använda dynamiska data inmatningar när du kör pipeline för batch-härledning kan du definiera indata `Dataset` som en [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) . Du kan ange indata-datauppsättningen varje gång du skickar en körnings pipeline för batch-härledning.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Skapa utdata

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) objekt används för att överföra mellanliggande data mellan pipeline-steg. I det här exemplet använder du den för att ge en utmatnings effekt.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
```

## <a name="prepare-the-model"></a>Förbereda modellen

[Ladda ned den förtränade avbildnings klassificerings modellen](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)och extrahera den sedan till `models` katalogen.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Registrera sedan modellen med arbets ytan så att den är tillgänglig för din beräknings resurs.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Skriv ditt härlednings skript

>[!Warning]
>Följande kod är bara ett exempel som används av [exempel antecknings boken](https://aka.ms/batch-inference-notebooks) . Du måste skapa ditt eget skript för ditt scenario.

Skriptet *måste innehålla* två funktioner:
- `init()`: Använd den här funktionen för eventuell kostsam eller vanlig förberedelse för senare härledning. Använd till exempel den för att läsa in modellen i ett globalt objekt. Den här funktionen kommer endast att anropas en gång i början av processen.
-  `run(mini_batch)`: Funktionen kommer att köras för varje `mini_batch` instans.
    -  `mini_batch`: `ParallelRunStep` anropar Run-metoden och skickar antingen en lista eller en Pandas `DataFrame` som ett argument till metoden. Varje post i mini_batch är en fil Sök väg om indata är en `FileDataset` eller en Pandas `DataFrame` om indata är en `TabularDataset` .
    -  `response`: Run ()-metoden ska returnera en Pandas `DataFrame` eller en matris. För append_row output_action läggs dessa returnerade element till i den gemensamma utdatafilen. För summary_only ignoreras innehållet i elementen. För alla utdata-åtgärder anger varje returnerat utdata en lyckad körning av indata-element i mini-batch för indata. Se till att tillräckligt med data inkluderas i körnings resultatet för att mappa indata till att köra resultatet av utdata. Kör utdata skrivs i utdatafilen och är inte garanterat i ordning, du bör använda vissa nycklar i utdata för att mappa den till indata.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Om du har en annan fil eller mapp i samma katalog som ditt härlednings skript kan du referera till den genom att söka efter den aktuella arbets katalogen.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Skapa och kör pipelinen som innehåller ParallelRunStep

Nu har du allt du behöver: data inmatningar, modell, utdata och ditt skript för härledning. Nu ska vi bygga pipelinen för batch-härledning som innehåller ParallelRunStep.

### <a name="prepare-the-environment"></a>Förbereda miljön

Börja med att ange beroenden för skriptet. På så sätt kan du installera pip-paket samt konfigurera miljön.

Inkludera alltid **azureml-Core** och **azureml-dataset-runtime [Pandas, säkring]** i PIP-paket listan. Om du använder en anpassad Docker-avbildning (user_managed_dependencies = true) bör du även ha Conda installerat.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Ange parametrar med ParallelRunConfig

`ParallelRunConfig` är den viktigaste konfigurationen för `ParallelRunStep` instansen i Azure Machine Learning pipelinen. Du använder den för att omsluta ditt skript och konfigurera nödvändiga parametrar, inklusive alla följande poster:
- `entry_script`: Ett användar skript som en lokal fil Sök väg som ska köras parallellt på flera noder. Om `source_directory` det finns använder du en relativ sökväg. Annars använder du valfri sökväg som är tillgänglig på datorn.
- `mini_batch_size`: Den mini-batch-storlek som skickas till ett enda `run()` anrop. (valfritt; standardvärdet är `10` filer för `FileDataset` och `1MB` for `TabularDataset` .)
    - För är `FileDataset` det antalet filer med minimivärdet `1` . Du kan kombinera flera filer i en mini-batch.
    - För är `TabularDataset` det data storleken. Exempel värden är `1024` , `1024KB` , `10MB` och `1GB` . Det rekommenderade värdet är `1MB` . Mini-batch från `TabularDataset` kommer aldrig att överskrida fil gränser. Om du till exempel har CSV-filer med olika storlekar är den minsta filen 100 KB och störst är 10 MB. Om du anger `mini_batch_size = 1MB` kommer filer med en storlek som är mindre än 1 MB att behandlas som en mini-batch. Filer med en storlek som är större än 1 MB delas upp i flera mini-batchar.
- `error_threshold`: Antalet post-och fil haverier `TabularDataset` `FileDataset` som ska ignoreras under bearbetningen. Om antalet fel för hela inflödet överskrider det här värdet kommer jobbet att avbrytas. Fel tröskeln är för alla indatatyper och inte för enskilda mini-batchar som skickas till- `run()` metoden. Intervallet är `[-1, int.max]` . `-1`Delen indikerar att ignorera alla avbrott under bearbetningen.
- `output_action`: Ett av följande värden anger hur utdata ska ordnas:
    - `summary_only`: Användar skriptet kommer att lagra utdata. `ParallelRunStep` kommer bara att använda utdata för fel tröskel beräkningen.
    - `append_row`: För alla indata skapas bara en fil i mappen utdata för att lägga till alla utdata avgränsade med rad.
- `append_row_file_name`: Om du vill anpassa namnet på utdatafilen för append_row output_action (valfritt; standardvärde `parallel_run_step.txt` ).
- `source_directory`: Sökvägar till mappar som innehåller alla filer som ska köras på beräknings målet (valfritt).
- `compute_target`: `AmlCompute` Stöds endast.
- `node_count`: Antalet beräknade datornoder som ska användas för att köra användar skriptet.
- `process_count_per_node`: Antalet processer per nod. Bästa praxis är att ställa in på antalet GPU eller CPU en nod har (valfritt; standardvärde är `1` ).
- `environment`: Python-miljöns definition. Du kan konfigurera den att använda en befintlig python-miljö eller konfigurera en tillfällig miljö. Definitionen är också ansvarig för att ange nödvändiga program beroenden (valfritt).
- `logging_level`: Logg utförlighet. Värden i ökande utförlighet är: `WARNING` , `INFO` och `DEBUG` . (valfritt; standardvärdet är `INFO` )
- `run_invocation_timeout`: `run()` Tids gränsen för metod anrop i sekunder. (valfritt; standardvärdet är `60` )
- `run_max_try`: Maximalt antal försök `run()` för en mini-batch. A `run()` Miss lyckas om ett undantag genereras eller om inget returneras när `run_invocation_timeout` nås (valfritt `3` ). 

Du kan ange `mini_batch_size` , `node_count` ,,, `process_count_per_node` `logging_level` `run_invocation_timeout` och `run_max_try` som `PipelineParameter` , så att du kan finjustera parametervärdena när du skickar om en pipeline-körning. I det här exemplet använder du `PipelineParameter` för `mini_batch_size` och `Process_count_per_node` och du kommer att ändra dessa värden när du skickar om en körning senare. 

I det här exemplet förutsätter vi att du använder `digit_identification.py` skriptet som diskuterades tidigare. Om du använder ett eget skript ändrar du `source_directory` parametrarna och `entry_script` .

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Skapa ParallelRunStep

Skapa ParallelRunStep med hjälp av skriptet, miljö konfigurationen och parametrarna. Ange det beräknings mål som du redan har kopplat till din arbets yta som mål för körning av ditt härlednings skript. Använd `ParallelRunStep` för att skapa pipeline-steget för batch-härledning, som tar alla följande parametrar:
- `name`: Namnet på steget med följande namngivnings begränsningar: unika, 3-32 tecken och regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: Ett `ParallelRunConfig` objekt som definieras tidigare.
- `inputs`: En eller flera data uppsättningar med en enkel Azure Machine Learning typ som ska partitioneras för parallell bearbetning.
- `side_inputs`: En eller flera referens data eller data uppsättningar som används som sid indata utan att behöva partitioneras.
- `output`: Ett `PipelineData` objekt som motsvarar utdata-katalogen.
- `arguments`: En lista över argument som skickas till användar skriptet. Använd unknown_args för att hämta dem i ditt Entry-skript (valfritt).
- `allow_reuse`: Om steget ska återanvända tidigare resultat när det körs med samma inställningar/indata. Om den här parametern är är `False` en ny körning alltid att skapas för det här steget under pipeline-körningen. (valfritt; standardvärdet är `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>Skapa och kör pipelinen

Kör nu pipelinen. Börja med att skapa ett [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) objekt med hjälp av din arbets ytans referens och det pipeline-steg som du skapade. `steps`Parametern är en matris med steg. I det här fallet finns det bara ett steg för batch-härledning. Placera stegen i den här matrisen för att bygga pipeliner som har flera steg.

Använd sedan `Experiment.submit()` funktionen för att skicka pipelinen för körning.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Övervaka jobbets härlednings jobb

Det kan ta lång tid att slutföra en batch-härledning. Det här exemplet övervakar förloppet med hjälp av en Jupyter-widget. Du kan också övervaka jobbets förlopp genom att använda:

* Azure Machine Learning Studio. 
* Konsolens utdata från [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) objektet.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Skicka en körning igen med nya data inmatningar och parametrar

Eftersom du har gjort indatana och flera konfigureras som kan `PipelineParameter` du skicka om en körning av en batch-härledning med en annan data uppsättning och finjustera parametrarna utan att behöva skapa en helt ny pipeline. Du kommer att använda samma data lager men använda bara en enda bild som data inmatning.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>Visa resultatet

Resultaten från ovanstående körning skrivs till den `DataStore` som anges i `PipelineData` objektet som utdata, som i det här fallet kallas *inferences*. Resultaten lagras i standard-BLOB-behållaren, du kan navigera till ditt lagrings konto och Visa Storage Explorer, fil Sök vägen är azureml-blobstore-*GUID*/azureml/*RunId* / *output_dir*.

Du kan också hämta dessa data för att visa resultatet. Nedan visas exempel koden för att visa de första 10 raderna.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>Nästa steg

Om du vill se den här processens arbets slut till slut punkt, kan du testa den [bärbara datorn](https://aka.ms/batch-inference-notebooks). 

För fel sökning och fel söknings vägledning för ParallelRunStep, se [instruktionen instruktion](how-to-debug-parallel-run-step.md).

För fel sökning och fel söknings vägledning för pipelines, se [instruktionen instruktion](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

