---
title: Visualisera experiment med TensorBoard
titleSuffix: Azure Machine Learning
description: Starta TensorBoard för att visualisera experiment körnings historik och identifiera potentiella områden för justering och omträning av en egen parameter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: minxia
ms.author: minxia
ms.date: 02/27/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 56b411a490fe1a0c45ea4dd183d88defd61a2fde
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640859"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Visualisera experiment körningar och mät värden med TensorBoard och Azure Machine Learning


I den här artikeln får du lära dig hur du visar experiment körningar och mått i TensorBoard med hjälp av [ `tensorboard` paketet](/python/api/azureml-tensorboard/) i huvud Azure Machine Learning SDK. När du har kontrollerat att experimentet har körts kan du bättre justera och träna dina maskin inlärnings modeller.

[TensorBoard](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) är en uppsättning webb program för att kontrol lera och förstå din experiment struktur och prestanda.

Hur du startar TensorBoard med Azure Machine Learning experiment beror på typen av experiment:
+ Om experimentet sparar loggfiler som kan användas av TensorBoard, till exempel PyTorch, Kedjorer och TensorFlow experiment, kan du [Starta TensorBoard direkt](#launch-tensorboard) från experimentets körnings historik. 

+ Använd [ `export_to_tensorboard()` metoden](#export) för att exportera körnings historiken som TensorBoard loggar och starta TensorBoard från där, för experiment som inte har en intern utmatning av TensorBoard användnings bara filer, t. ex. Scikit – lära eller Azure Machine Learning experiment. 

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill övervaka processen för modell inlärning. Om du är administratör som är intresse rad av övervakning av resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda inlärnings körningar eller slutförd modell distribution, se [övervaknings Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar

* För att starta TensorBoard och Visa dina experiment körnings historik måste experimenten tidigare ha aktiverat loggning för att spåra dess mått och prestanda.  
* Koden i det här dokumentet kan köras i någon av följande miljöer: 
    * Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs
        * Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
        * I mappen exempel på Notebook-servern hittar du två slutförda och expanderade antecknings böcker genom att gå till följande kataloger:
            * **instruktioner för att använda-azureml > spårnings-och-övervaka-experiment > tensorboard > export-Run-History-till-tensorboard > export-Run-History-till-tensorboard. ipynb**
            * **instruktioner för att använda-azureml > spårnings-och-övervaka-experiment > tensorboard > tensorboard > tensorboard. ipynb**
    * Din egen Juptyer Notebook-Server
       * [Installera Azure Machine Learning SDK](/python/api/overview/azure/ml/install) med `tensorboard` extra
        * [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).  
        * [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Alternativ 1: Visa körnings historik direkt i TensorBoard

Det här alternativet används för experiment som skriver utdata i loggfiler som kan användas av TensorBoard, till exempel PyTorch, kedjor och TensorFlow experiment. Om detta inte är fallet använder [du `export_to_tensorboard()` metoden](#export) i stället.

I följande exempel kod används [MNIST demo experiment](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) från TensorFlow-lagringsplatsen i ett fjärrberäknings mål, Azure Machine Learning beräkning. Nu ska vi konfigurera och starta en körning för att träna TensorFlow-modellen och sedan starta TensorBoard mot detta TensorFlow experiment.

### <a name="set-experiment-name-and-create-project-folder"></a>Ange experiment namn och skapa projektmappen

Här namnger vi experimentet och skapar dess mapp. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Hämta experiment koden för TensorFlow demo

TensorFlow-lagringsplatsen har en MNIST-demo med omfattande TensorBoard Instrumentation. Du behöver inte heller ändra någon av den här demonstrationens kod för att den ska fungera med Azure Machine Learning. I följande kod laddar vi ned MNIST-koden och sparar den i vår nyligen skapade experiment-mapp.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
I MNIST-kod filen mnist_with_summaries. py, Observera att det finns rader som anropar `tf.summary.scalar()` ,  `tf.summary.histogram()` `tf.summary.FileWriter()` osv. Dessa metoder grupperar, loggar och tagar nyckel mått för dina experiment i körnings historiken. `tf.summary.FileWriter()`Är särskilt viktigt när den serialiserar data från de inloggade experiment måtten, vilket gör att TensorBoard kan generera visualiseringar av dem.

 ### <a name="configure-experiment"></a>Konfigurera experiment

I följande konfigurerar vi våra experiment och konfigurerar kataloger för loggar och data. Loggarna överförs till körnings historiken, som TensorBoard kommer åt senare.

> [!Note]
> För det här TensorFlow-exemplet måste du installera TensorFlow på den lokala datorn. Dessutom måste TensorBoard-modulen (d.v.s. den som ingår i TensorFlow) vara tillgänglig för den här Notebook-kärnan, eftersom den lokala datorn är vad som körs TensorBoard.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to the run history,
# and thus, made accessible to our TensorBoard instance.
args = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Skapa ett kluster för experimentet
Vi skapar ett AmlCompute-kluster för det här experimentet, men dina experiment kan skapas i valfri miljö och du kan fortfarande starta TensorBoard mot experiment körnings historiken. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpu-cluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="configure-and-submit-training-run"></a>Konfigurera och skicka utbildnings körning

Konfigurera ett utbildnings jobb genom att skapa ett ScriptRunConfig-objekt.

```Python
from azureml.core import ScriptRunConfig
from azureml.core import Environment

# Here we will use the TensorFlow 2.2 curated environment
tf_env = Environment.get(ws, 'AzureML-TensorFlow-2.2-GPU')

src = ScriptRunConfig(source_directory=exp_dir,
                      script='mnist_with_summaries.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
run = exp.submit(src)
```

### <a name="launch-tensorboard"></a>Starta TensorBoard

Du kan starta TensorBoard under din körning eller när den är klar. I följande skapar vi en TensorBoard objekt instans, `tb` som använder experiment körnings historiken som lästs in i `run` och sedan startar TensorBoard med- `start()` metoden. 
  
[TensorBoard-konstruktorn](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard) tar en matris med körningar, så var noga med att skicka den som en matris med ett enda element.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

> [!Note]
> I det här exemplet användes TensorFlow, så att TensorBoard kan användas lika enkelt med PyTorch eller en kedja. TensorFlow måste vara tillgängligt på den dator som kör TensorBoard, men det är inte nödvändigt på datorn som utför PyTorch-eller kedje bearbetnings beräkningar. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Alternativ 2: exportera historik som logg att visa i TensorBoard

Följande kod konfigurerar ett exempel experiment, påbörjar loggnings processen med hjälp av Azure Machine Learning API: er för körnings historik och exporterar experiment körnings historiken till loggar som kan användas av TensorBoard för visualisering. 

### <a name="set-up-experiment"></a>Konfigurera experiment

Följande kod ställer in ett nytt experiment och namnger körnings katalogen `root_run` . 

```python
from azureml.core import Workspace, Experiment
import azureml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Här läser vi in diabetes-datauppsättningen – en inbyggd liten data uppsättning som medföljer scikit och delar upp den i test-och inlärnings uppsättningar.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Köra experiment-och logg mått

För den här koden tränar vi en linjär Regressions modell och logg nyckel mått, alpha-koefficienten `alpha` och genomsnitts fel i kvadratvärdet, `mse` i körnings historiken.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run:
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Exportera körningar till TensorBoard

Med SDK: s metod för [export_to_tensorboard ()](/python/api/azureml-tensorboard/azureml.tensorboard.export) kan vi exportera körnings historiken för vårt Azure Machine Learning-experiment till tensorboard-loggar, så att vi kan visa dem via tensorboard.  

I följande kod skapar vi mappen `logdir` i vår aktuella arbets katalog. I den här mappen kommer vi att exportera våra experiment körnings historik och loggar från `root_run` och sedan markera att kör som slutförd. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

> [!Note]
> Du kan också exportera en viss körning till TensorBoard genom att ange namnet på körningen  `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Starta och stoppa TensorBoard
När vår körnings historik för det här experimentet har exporter ATS kan vi starta TensorBoard med metoden [Start ()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#start-start-browser-false-) . 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

När du är klar, se till att anropa metoden [Stop ()](/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard#stop--) för TensorBoard-objektet. Annars fortsätter TensorBoard att köras tills du stänger av Notebook-kärnan. 

```python
tb.stop()
```

## <a name="next-steps"></a>Nästa steg

I den här instruktionen har du skapat två experiment och lärt dig hur du startar TensorBoard mot sina körnings historik för att identifiera områden för möjlig justering och omträning. 

* Om du är nöjd med din modell kan du gå vidare till vår [distribution av en modell](how-to-deploy-and-where.md) artikel. 
* Lär dig mer om justering av en [parameter](how-to-tune-hyperparameters.md).
