---
title: Felsökning av ParallelRunStep
titleSuffix: Azure Machine Learning
description: Tips för att felsöka när du får fel med hjälp av ParallelRunStep i Machine Learning-pipeliner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: trmccorm
author: tmccrmck
ms.date: 09/23/2020
ms.openlocfilehash: ee41ae2a705ceaa0e9742c91552d6bdae26820ce
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690285"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Felsökning av ParallelRunStep

I den här artikeln får du lära dig hur du felsöker när du får fel med klassen [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep?preserve-view=true&view=azure-ml-py) från [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Allmänna tips om hur du felsöker en pipeline finns i [Felsöka maskin inlärnings pipeliner](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Testa skript lokalt

 Din ParallelRunStep körs som ett steg i ML-pipelines. Du kanske vill [testa dina skript lokalt](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) som ett första steg.

##  <a name="script-requirements"></a>Skript krav

Skriptet för en `ParallelRunStep` *måste innehålla* två funktioner:
- `init()`: Använd den här funktionen för eventuell kostsam eller vanlig förberedelse för senare härledning. Använd till exempel den för att läsa in modellen i ett globalt objekt. Den här funktionen kommer endast att anropas en gång i början av processen.
-  `run(mini_batch)`: Funktionen kommer att köras för varje `mini_batch` instans.
    -  `mini_batch`: `ParallelRunStep` anropar Run-metoden och skickar antingen en lista eller en Pandas `DataFrame` som ett argument till metoden. Varje post i mini_batch är en fil Sök väg om indata är en `FileDataset` eller en Pandas `DataFrame` om indata är en `TabularDataset` .
    -  `response`: Run ()-metoden ska returnera en Pandas `DataFrame` eller en matris. För append_row output_action läggs dessa returnerade element till i den gemensamma utdatafilen. För summary_only ignoreras innehållet i elementen. För alla utdata-åtgärder anger varje returnerat utdata en lyckad körning av indata-element i mini-batch för indata. Se till att tillräckligt med data inkluderas i körnings resultatet för att mappa indata till att köra resultatet av utdata. Kör utdata skrivs i utdatafilen och är inte garanterat i ordning, du bör använda vissa nycklar i utdata för att mappa den till indata.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
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

### <a name="parameters-for-parallelrunconfig"></a>Parametrar för ParallelRunConfig

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

### <a name="parameters-for-creating-the-parallelrunstep"></a>Parametrar för att skapa ParallelRunStep

Skapa ParallelRunStep med hjälp av skriptet, miljö konfigurationen och parametrarna. Ange det beräknings mål som du redan har kopplat till din arbets yta som mål för körning av ditt härlednings skript. Använd `ParallelRunStep` för att skapa pipeline-steget för batch-härledning, som tar alla följande parametrar:
- `name`: Namnet på steget med följande namngivnings begränsningar: unika, 3-32 tecken och regex ^ \[ a-z \] ([-a-Z0-9] * [a-Z0-9])? $.
- `parallel_run_config`: Ett `ParallelRunConfig` objekt som definieras tidigare.
- `inputs`: En eller flera data uppsättningar med en enkel Azure Machine Learning typ som ska partitioneras för parallell bearbetning.
- `side_inputs`: En eller flera referens data eller data uppsättningar som används som sid indata utan att behöva partitioneras.
- `output`: Ett `OutputFileDatasetConfig` objekt som representerar sökvägen till den katalog där utdata ska lagras.
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

## <a name="debugging-scripts-from-remote-context"></a>Felsöka skript från fjärr kontext

Över gången från att felsöka ett bedömnings skript lokalt för att felsöka ett bedömnings skript i en faktisk pipeline kan vara ett svårt kliv. Information om hur du hittar dina loggar i portalen finns i  [avsnittet om att felsöka skript från en fjärrkontext i avsnittet Machine Learning-pipeline](how-to-debug-pipelines.md). Informationen i avsnittet gäller även för en ParallelRunStep.

Logg filen innehåller till exempel `70_driver_log.txt` information från den kontrollant som startar ParallelRunStep-koden.

På grund av den distribuerade typen av ParallelRunStep-jobb finns det loggar från flera olika källor. Två konsoliderade filer skapas dock som tillhandahåller information på hög nivå:

- `~/logs/job_progress_overview.txt`: Den här filen innehåller en information på hög nivå om antalet mini-batchar (även kallade aktiviteter) som skapats hittills och antalet mini-batchar som bearbetats hittills. I det här slutet visas resultatet av jobbet. Om jobbet misslyckades visas fel meddelandet och var du ska starta fel sökningen.

- `~/logs/sys/master_role.txt`: Den här filen ger huvudnoden (kallas även Orchestrator) vyn för jobbet som körs. Innehåller skapande av aktiviteter, förlopps övervakning, körnings resultat.

Loggar som genereras från ett Entry-skript med hjälp av EntryScript-och Print-instruktioner finns i följande filer:

- `~/logs/user/entry_script_log/<ip_address>/<process_name>.log.txt`: De här filerna är loggarna som skrivs från entry_script med hjälp av EntryScript-hjälpen.

- `~/logs/user/stdout/<ip_address>/<process_name>.stdout.txt`: De här filerna är loggarna från STDOUT (t. ex. utskrifts uttryck) för entry_script.

- `~/logs/user/stderr/<ip_address>/<process_name>.stderr.txt`: De här filerna är loggarna från stderr av entry_script.

En kortfattad förståelse för fel i skriptet finns i:

- `~/logs/user/error.txt`: Den här filen kommer att försöka sammanfatta felen i skriptet.

För mer information om fel i skriptet, finns det:

- `~/logs/user/error/`: Innehåller fullständiga stack-spår för undantag som genereras vid inläsning och körning av Entry-skript.

När du behöver en fullständig förståelse för hur varje nod kör Poäng skriptet kan du titta på de enskilda process loggarna för varje nod. Process loggarna finns i `sys/node` mappen grupperade efter arbetsnoder:

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Den här filen innehåller detaljerad information om varje mini-batch när den hämtas eller slutförs av en anställd. För varje mini-batch innehåller den här filen:

    - IP-adressen och PID för arbets processen. 
    - Det totala antalet objekt, antalet behandlade objekt och antalet misslyckade objekt.
    - Tid för start tid, varaktighet, bearbetnings tid och körnings metod.

Du kan också visa resultaten av periodiska kontroller av resursanvändningen för varje nod. Loggfilerna och installationsfilerna finns i följande mapp:

- `~/logs/perf`: Ange `--resource_monitor_interval` om du vill ändra kontroll intervallet i sekunder. Standard intervallet är `600` cirka 10 minuter. Om du vill stoppa övervakningen ställer du in värdet på `0` . Varje `<ip_address>` mapp innehåller:

    - `os/`: Information om alla processer som körs i noden. En kontroll kör ett operativ system kommando och sparar resultatet i en fil. I Linux är kommandot `ps` . Använd i Windows `tasklist` .
        - `%Y%m%d%H`: Namnet på den underordnade mappen är tiden till timmen.
            - `processes_%M`: Filen slutar med minuten för kontroll tiden.
    - `node_disk_usage.csv`: Detaljerad disk användning för noden.
    - `node_resource_usage.csv`: Översikt över resursanvändning för noden.
    - `processes_resource_usage.csv`: Översikt över resursanvändning för varje process.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Hur gör jag för att logg från mitt användar skript från en fjärran sluten kontext?

ParallelRunStep kan köra flera processer på en nod baserat på process_count_per_node. För att kunna organisera loggar från varje process på noden och kombinera print-och log-uttryck, rekommenderar vi att du använder ParallelRunStep-loggning som visas nedan. Du får en loggare från EntryScript och gör loggarna visas i **loggarna/mappen användare** i portalen.

**Ett exempel på ett post skript med hjälp av loggarna:**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """ Initialize the node."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """ Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="how-could-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Hur kan jag skicka in en indata, till exempel en eller flera filer som innehåller en uppslags tabell, till alla mina anställda?

Användaren kan skicka referens data till skript med hjälp av side_inputs parametern ParalleRunStep. Alla data uppsättningar som tillhandahålls som side_inputs kommer att monteras på varje arbetsnod. Användaren kan hämta platsen för montering genom att skicka argumentet.

Skapa en [data uppsättning](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py) som innehåller referens data och registrera den med din arbets yta. Skicka den till- `side_inputs` parametern för din `ParallelRunStep` . Dessutom kan du lägga till dess sökväg i `arguments` avsnittet för att enkelt få åtkomst till dess monterade sökväg:

```python
label_config = label_ds.as_named_input("labels_input")
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

När du har åtkomst till den i ditt härlednings skript (till exempel i metoden init ()) enligt följande:

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Hur använder jag indata-datauppsättningar med autentisering av tjänstens huvud namn?

Användaren kan skicka indata-datauppsättningar med autentisering av tjänstens huvud namn som används i arbets ytan. Om du använder sådan data uppsättning i ParallelRunStep måste data uppsättningen registreras för att kunna konstruera ParallelRunStep-konfigurationen.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="next-steps"></a>Nästa steg

* Se dessa [Jupyter-anteckningsböcker som demonstrerar Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)

* Se SDK-referensen för hjälp med paketet [azureml-pipeline-steg](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py) . Visa referens [dokumentation](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep?preserve-view=true&view=azure-ml-py) för ParallelRunStep-klassen.

* Följ den [avancerade självstudien](tutorial-pipeline-batch-scoring-classification.md) om hur du använder pipelines med ParallelRunStep. I självstudien visas hur du skickar en annan fil som indata.
