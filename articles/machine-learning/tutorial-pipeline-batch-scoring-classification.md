---
title: 'Självstudie: ML pipelines för batch-Poäng'
titleSuffix: Azure Machine Learning
description: I den här självstudien skapar du en maskin inlärnings pipeline för att utföra batch-poäng. Fokusera på Machine Learning i stället för infrastruktur och automatisering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 10/13/2020
ms.custom: contperf-fy20q4, devx-track-python
ms.openlocfilehash: 916064742e69b7d355a0c7e541d4f2270e8854f4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134457"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Självstudie: Bygg en Azure Machine Learning pipeline för batch-Poäng

I den här avancerade självstudien får du lära dig hur du skapar en [Azure Machine Learning pipeline](concept-ml-pipelines.md) för att köra ett batch-bedömnings jobb. Maskin inlärnings pipeliner optimerar arbets flödet med hastighet, portabilitet och åter användning, så att du kan fokusera på maskin inlärning i stället för infrastruktur och automatisering. När du har skapat och publicerat en pipeline konfigurerar du en REST-slutpunkt som du kan använda för att utlösa pipelinen från alla HTTP-bibliotek på vilken plattform som helst. 

Exemplet använder en förtränad in(convolutionalt neurala [-](https://arxiv.org/abs/1512.00567) nätverks modell som implementerats i Tensorflow för att klassificera omärkta bilder. 

I den här självstudien slutför du följande uppgifter:

> [!div class="checklist"]
> * Konfigurera arbetsyta 
> * Hämta och lagra exempel data
> * Skapa data mängds objekt för att hämta och mata ut data
> * Ladda ned, Förbered och registrera modellen på din arbets yta
> * Etablera beräknings mål och skapa ett bedömnings skript
> * Använd `ParallelRunStep` klassen för asynkron batch-Poäng
> * Skapa, köra och publicera en pipeline
> * Aktivera en REST-slutpunkt för pipelinen

Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte redan har en Azure Machine Learning arbets yta eller en virtuell dator i datorn, fyller du [i del 1 i installations guiden för](tutorial-1st-experiment-sdk-setup.md).
* När du är klar med installations självstudien använder du samma Notebook-Server för att öppna *självstudierna/Machine-Learning-pipelines-Advanced/tutorial-pipeline-batch-scoring-Classification. ipynb* Notebook.

Om du vill köra själv studie kursen i din egen [lokala miljö](how-to-configure-environment.md#local)kan du komma åt själv studie kursen om [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Kör `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` för att hämta de nödvändiga paketen.

## <a name="configure-workspace-and-create-a-datastore"></a>Konfigurera arbets ytan och skapa ett data lager

Skapa ett objekt för arbets ytan från den befintliga Azure Machine Learning-arbetsytan.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> Det här kodfragmentet förväntar sig att arbets ytans konfiguration sparas i den aktuella katalogen eller dess överordnade. Mer information om hur du skapar en arbets yta finns i [skapa och hantera Azure Machine Learning arbets ytor](how-to-manage-workspace.md). Mer information om hur du sparar konfigurationen till filen finns i [skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).

## <a name="create-a-datastore-for-sample-images"></a>Skapa ett data lager för exempel bilder

På `pipelinedata` kontot hämtar du ImageNet för utvärdering av offentliga data från den `sampledata` offentliga BLOB-behållaren. Anropa `register_azure_blob_container()` för att göra data tillgängliga för arbets ytan under namnet `images_datastore` . Ange sedan standard data lagret för arbets ytan som utdata-datalager. Använd utdata-datalagret för att räkna ut utdata i pipelinen.

Mer information om hur du kommer åt data finns i [så här kommer du åt data](./how-to-access-data.md).

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Skapa data mängds objekt

När du skapar pipeliner `Dataset` används objekt för att läsa data från data lager för arbets ytor och `OutputFileDatasetConfig` objekt används för att överföra mellanliggande data mellan pipeline-steg.

> [!Important]
> I batch-bedömnings exemplet i den här självstudien används endast ett pipeline-steg. I användnings fall som har flera steg innehåller det typiska flödet följande steg:
>
> 1. Använd `Dataset` objekt som *indata* för att hämta rå data, utföra en del omvandling och sedan *skriva ut* med ett `OutputFileDatasetConfig` objekt.
>
> 2. Använd `OutputFileDatasetConfig` *objektet utdata* i föregående steg som ett *indata-objekt*. Upprepa det för efterföljande steg.

I det här scenariot skapar du `Dataset` objekt som motsvarar data lager kataloger för både indata och klassificerings etiketter (y-test-värden). Du skapar också ett `OutputFileDatasetConfig` objekt för utdata från batch-poängen.

```python
from azureml.core.dataset import Dataset
from azureml.data import OutputFileDatasetConfig

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = OutputFileDatasetConfig(name="scores")
```

Registrera data uppsättningarna på arbets ytan om du vill återanvända den senare. Det här är valfritt.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Hämta och registrera modellen

Hämta den förtränade Tensorflow-modellen och Använd den för batch-Poäng i en pipeline. Skapa först en lokal katalog där du lagrar modellen. Hämta och extrahera sedan modellen.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Registrera sedan modellen på arbets ytan så att du enkelt kan hämta modellen i pipeline-processen. I den `register()` statiska funktionen `model_name` är parametern den nyckel som du använder för att hitta din modell i SDK: n.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Skapa och koppla fjärrberäknings målet

Det går inte att köra maskin inlärnings pipeliner lokalt, så du kör dem på moln resurser eller *fjärranslutna beräknings mål*. Ett fjärran sluten beräknings mål är en återanvändbar virtuell beräknings miljö där du kör experiment och Machine Learning-arbetsflöden. 

Kör följande kod för att skapa ett GPU-aktiverat [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py) mål och koppla det sedan till din arbets yta. Mer information om beräknings mål finns i den [konceptuella artikeln](./concept-compute-target.md).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Skriv ett bedömnings skript

Om du vill göra en bedömning skapar du ett kommando bedömnings skript som heter `batch_scoring.py` och skriver det sedan till den aktuella katalogen. Skriptet använder inmatnings bilder, använder klassificerings modellen och matar sedan ut förutsägelserna till en resultat fil.

`batch_scoring.py`Skriptet använder följande parametrar, som skickas från `ParallelRunStep` dig senare:

- `--model_name`: Namnet på den modell som används.
- `--labels_dir`: `labels.txt` Filens plats.

Pipeline-infrastrukturen använder `ArgumentParser` klassen för att skicka parametrar till pipeline-steg. I följande kod tilldelas till exempel det första argumentet `--model_name` egenskaps-ID `model_name` . I `init()` funktionen `Model.get_model_path(args.model_name)` används för att få åtkomst till den här egenskapen.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> Pipelinen i den här självstudien har bara ett steg och skriver utdata till en fil. För pipeline i flera steg använder du också `ArgumentParser` för att definiera en katalog för att skriva utdata som indata för efterföljande steg. Ett exempel på att skicka data mellan flera pipeline-steg med hjälp av `ArgumentParser` design mönstret finns i [antecknings boken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Bygg pipelinen

Innan du kör pipelinen skapar du ett-objekt som definierar python-miljön och skapar de beroenden som ditt `batch_scoring.py` skript kräver. Det nödvändiga huvud beroendet är Tensorflow, men du installerar `azureml-core` och `azureml-dataprep[fuse]` som krävs av ParallelRunStep. Ange också Docker och Docker-GPU-stöd.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Skapa konfigurationen för att packa upp skriptet

Skapa pipeline-steget med skriptet, miljö konfigurationen och parametrarna. Ange det beräknings mål som du redan har kopplat till din arbets yta.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Skapa pipeline-steget

Ett pipeline-steg är ett objekt som kapslar in allt du behöver för att köra en pipeline, inklusive:

* Miljö-och beroende inställningar
* Beräknings resursen som ska köra pipelinen på
* Indata och utdata och eventuella anpassade parametrar
* Referens till ett skript eller en SDK-logik som ska köras under steget

Flera klasser ärver från den överordnade klassen [`PipelineStep`](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?preserve-view=true&view=azure-ml-py) . Du kan välja klasser för att använda specifika ramverk eller stackar för att bygga ett steg. I det här exemplet använder du `ParallelRunStep` klassen för att definiera din steg logik genom att använda ett anpassat Python-skript. Om ett argument i skriptet antingen är indata till steget eller utdata från steget, måste argumentet definieras *både* i `arguments` matrisen *och* i respektive `input` `output` parameter. 

I scenarier där det finns mer än ett steg blir en objekt referens i `outputs` matrisen tillgänglig som *inmatad* för ett efterföljande pipeline-steg.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

En lista över alla klasser du kan använda för olika steg typer finns i [steg paketet](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py).

## <a name="submit-the-pipeline"></a>Skicka pipelinen

Kör nu pipelinen. Börja med att skapa ett `Pipeline` objekt med hjälp av din arbets ytas referens och det pipeline-steg som du skapade. `steps`Parametern är en matris med steg. I det här fallet finns det bara ett steg för batch-poäng. Placera stegen i den här matrisen för att bygga pipeliner som har flera steg.

Använd sedan `Experiment.submit()` funktionen för att skicka pipelinen för körning. `wait_for_completion`Funktionen matar ut loggar under pipeline-Bygg processen. Du kan använda loggarna för att se aktuell status.

> [!IMPORTANT]
> Den första pipeline-körningen tar ungefär *15 minuter*. Alla beroenden måste hämtas, en Docker-avbildning skapas och python-miljön har skapats och skapats. Att köra pipelinen igen får betydligt kortare tid eftersom resurserna återanvänds i stället för att skapas. Den totala körnings tiden för pipelinen beror dock på arbets belastningen för dina skript och de processer som körs i varje pipeline-steg.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Hämta och granska utdata

Kör följande kod för att ladda ned utdatafilen som skapas från `batch_scoring.py` skriptet. Utforska sedan resultat resultaten.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publicera och köra från en REST-slutpunkt

Kör följande kod för att publicera pipelinen till din arbets yta. I din arbets yta i Azure Machine Learning Studio kan du se metadata för pipelinen, inklusive körnings historik och varaktighet. Du kan också köra pipelinen manuellt från Studio.

Genom att publicera pipelinen kan du använda en REST-slutpunkt som du kan använda för att köra pipelinen från alla HTTP-bibliotek på vilken plattform som helst.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Om du vill köra pipelinen från REST-slutpunkten behöver du ett OAuth2 Bearer-huvud. I följande exempel används interaktiv autentisering (för illustrations ändamål), men för de flesta produktions scenarier som kräver automatiserad eller riktad autentisering, använder du tjänstens huvud namns autentisering enligt [beskrivningen i den här artikeln](how-to-setup-authentication.md).

Autentisering av tjänstens huvud konto innebär att skapa en *app-registrering* i *Azure Active Directory*. Först genererar du en klient hemlighet och sedan beviljar du *rollen* som tjänst huvud namn till din Machine Learning-arbetsyta. Använd- [`ServicePrincipalAuthentication`](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) klassen för att hantera ditt autentiserings flöde. 

Både [`InteractiveLoginAuthentication`](/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?preserve-view=true&view=azure-ml-py) och `ServicePrincipalAuthentication` ärver från `AbstractAuthentication` . I båda fallen använder du [`get_authentication_header()`](/python/api/azureml-core/azureml.core.authentication.abstractauthentication?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-authentication-header--) funktionen på samma sätt för att hämta rubriken:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Hämta REST-URL: en från `endpoint` egenskapen för det publicerade pipeline-objektet. Du kan också hitta REST-URL: en i din arbets yta i Azure Machine Learning Studio. 

Bygg en HTTP POST-begäran till slut punkten. Ange ditt Authentication-huvud i begäran. Lägg till ett JSON-nyttolast-objekt med experimentets namn.

Gör begäran för att utlösa körningen. Ta med kod för att komma åt `Id` nyckeln från svars ord listan för att hämta värdet för körnings-ID: t.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Använd körnings-ID: t för att övervaka statusen för den nya körningen. Den nya körningen tar en till 10-15 min för slut. 

Den nya körningen ser ut ungefär som den pipeline du körde tidigare i självstudien. Du kan välja att inte Visa hela utdata.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Rensa resurser

Slutför inte det här avsnittet om du planerar att köra andra Azure Machine Learning själv studie kurser.

### <a name="stop-the-compute-instance"></a>Stoppa beräknings instansen

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Ta bort allt

Om du inte planerar att använda de resurser som du skapade, tar du bort dem så att du inte debiteras:

1. I Azure Portal väljer du **resurs grupper** på den vänstra menyn.
1. I listan över resurs grupper väljer du den resurs grupp som du skapade.
1. Välj **Ta bort resursgrupp**.
1. Ange resursgruppsnamnet. Välj sedan **ta bort**.

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa egenskaperna för arbets ytan och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien om maskin inlärning har du följande uppgifter:

> [!div class="checklist"]
> * Skapade en pipeline med miljö beroenden som ska köras på en fjärran sluten GPU Compute-resurs.
> * Skapade ett bedömnings skript för att köra batch-förutsägelser med hjälp av en förtränad Tensorflow-modell.
> * Publicerade en pipeline och har aktiverat den för att köras från en REST-slutpunkt.

Fler exempel på hur du skapar pipelines med Machine Learning SDK finns i [Notebook-lagringsplatsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
