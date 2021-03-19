---
title: 'Själv studie kurs om bild klassificering: träna modeller'
titleSuffix: Azure Machine Learning
description: Använd Azure Machine Learning för att träna en bild klassificerings modell med scikit – lär dig i en python-Jupyter Notebook. Den här självstudien är del ett av två.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 09/28/2020
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 85dea807ee09338e7f0e9e388f6b196fd3beef33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588672"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn"></a>Självstudie: träna bild klassificerings modeller med MNIST-data och scikit-lär 


I den här självstudien ska du träna en maskininlärningsmodell på fjärranslutna beräkningsresurser. Du använder arbets flödet för utbildning och distribution för Azure Machine Learning i en python-Jupyter Notebook.  Du kan sedan använda anteckningsboken som en mall för att träna din egen maskininlärningsmodell med egna data. Den här självstudien är **del ett i en själv studie serie i två delar**.  

Den här självstudien tränar en enkel logistik regression med hjälp av [MNIST](http://yann.lecun.com/exdb/mnist/) -datauppsättningen och [scikit – lära](https://scikit-learn.org) med Azure Machine Learning. MNIST är en populär datauppsättning som består av 70 000 gråskalebilder. Varje bild är en handskriven siffra på 28 × 28 pixlar, som representerar ett tal från noll till nio. Målet är att skapa en klassificerare för flera klasser som identifierar siffran som en viss bild representerar.

Läs hur du vidtar följande åtgärder:

> [!div class="checklist"]
> * Konfigurera din utvecklingsmiljö.
> * Kom åt och utforska data.
> * Träna en enkel logistik Regressions modell på ett fjärran slutet kluster.
> * Granska träningsresultaten och registrera den bästa modellen.

Du lär dig hur du väljer en modell och distribuerar den i [del två av den här självstudien](tutorial-deploy-models-with-aml.md).

Om du inte har någon Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag.

>[!NOTE]
> Koden i den här artikeln har testats med [Azure Machine Learning SDK](/python/api/overview/azure/ml/intro) -version 1.13.0.

## <a name="prerequisites"></a>Förutsättningar

* Slutför [självstudien: kom igång med att skapa ditt första Azure ml-experiment](tutorial-1st-experiment-sdk-setup.md) för att:
    * Skapa en arbetsyta
    * Klona självstudiernas antecknings böcker till din mapp i arbets ytan.
    * Skapa en molnbaserad beräknings instans.

* Öppna mappen *img-klassifikation-part1-Training. ipynb* i dina klonade *självstudier/mnist* . 


Självstudien och den medföljande **utils.py** -filen finns också på [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) om du vill använda den i din egen [lokala miljö](how-to-configure-environment.md#local). Kör `pip install azureml-sdk[notebooks] azureml-opendatasets matplotlib` för att installera beroenden för den här självstudien.

> [!Important]
> Resten av den här artikeln innehåller samma innehåll som du ser i antecknings boken.  
>
> Växla till Jupyter Notebook nu om du vill läsa när du kör koden. 
> Om du vill köra en enda kod cell i en bärbar dator klickar du på cellen kod och trycker på **SKIFT + RETUR**. Du kan också köra hela antecknings boken genom att välja **Kör alla** från det översta verktygsfältet.

## <a name="set-up-your-development-environment"></a><a name="start"></a>Ställt in din utvecklingsmiljö

All konfiguration under utvecklingsarbetet kan utföras i en Python-anteckningsbok. Konfigurationen inkluderar följande åtgärder:

* Importera Python-paket.
* Ansluta till en arbetsyta så att din lokala dator kan kommunicera med fjärranslutna resurser.
* Skapa ett experiment för att spåra alla dina körningar.
* Skapa ett fjärrberäkningsmål som ska användas för träning.

### <a name="import-packages"></a>Importera paket

Importera Python-paket som du behöver i den här sessionen. Visa även Azure Machine Learning SDK-versionen:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Anslut till en arbetsyta

Skapa ett arbetsyteobjekt från den befintliga arbetsytan. `Workspace.from_config()` läser filen **config.jspå** och läser in informationen i ett objekt med namnet `ws` :

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

>[!NOTE]
> Du kan bli ombedd att autentisera till din arbets yta första gången du kör följande kod. Följ anvisningarna på skärmen.

### <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment för att spåra körningarna på arbetsytan. En arbetsyta kan ha flera experiment:

```python
from azureml.core import Experiment
experiment_name = 'Tutorial-sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Skapa eller koppla ett befintligt beräknings mål

Genom att använda Azure Machine Learning Compute, en hanterad tjänst så kan datavetare träna maskininlärningsmodeller i kluster med virtuella Azure-datorer. Exempel innefattar virtuella datorer med GPU-stöd. I den här självstudien ska du skapa Azure Machine Learning Compute som din träningsmiljö. Du kommer att skicka python-kod som ska köras på den här virtuella datorn senare i självstudien. 

Koden nedan skapar beräkningsklustren åt dig om de inte redan finns på din arbetsyta. Det konfigurerar ett kluster som skalar ned till 0 när det inte används och kan skala upp till högst 4 noder. 

 **Det tar cirka fem minuter att skapa beräknings målet.** Om beräknings resursen redan finns i arbets ytan använder koden den och hoppar över skapande processen.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
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
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Nu har du de nödvändiga paketen och beräkningsresurserna för att träna en modell i molnet. 

## <a name="explore-data"></a>Utforska data

Innan du tränar en modell så måste du förstå de data som du använder för att träna den. I det här avsnittet lär du dig att:

* Hämta MNIST-datauppsättningen.
* Visa några exempelbilder.

### <a name="download-the-mnist-dataset"></a>Ladda ned MNIST-datauppsättningen

Använd Azures öppna data uppsättningar för att hämta RAW MNIST-datafilerna. [Azure Open-datauppsättningar](../open-datasets/overview-what-are-open-datasets.md) är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner till maskin inlärnings lösningar för mer exakta modeller. Varje data uppsättning har en motsvarande klass, `MNIST` i det här fallet för att hämta data på olika sätt.

Den här koden hämtar data som ett `FileDataset` objekt, vilket är en underordnad klass till `Dataset` . En `FileDataset` refererar till en eller flera filer i alla format i dina data lager eller offentliga URL: er. Klassen ger dig möjlighet att ladda ned eller montera filerna i beräkningen genom att skapa en referens till data käll platsen. Dessutom registrerar du data uppsättningen på din arbets yta för enkel hämtning under utbildningen.

Följ anvisningarna [för att](how-to-create-register-datasets.md) lära dig mer om data uppsättningar och deras användning i SDK.

```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)

mnist_file_dataset = mnist_file_dataset.register(workspace=ws,
                                                 name='mnist_opendataset',
                                                 description='training and test dataset',
                                                 create_new_version=True)
```

### <a name="display-some-sample-images"></a>Visa några exempelbilder

Läs in de komprimerade filerna i `numpy`-matriser. Använd därefter `matplotlib` till att rita 30 slumpmässiga bilder från datauppsättningen med sina etiketter ovanför dem. Det här steget kräver en `load_data`-funktion som ingår i en `utils.py`-fil. Den här filen finns med i exempelmappen. Kontrollera att den finns i samma mapp som den här anteckningsboken. Funktionen `load_data` parsar enkelt de komprimerade filerna till numpy-matriser.

```python
# make sure utils.py is in the same directory as this code
from utils import load_data
import glob


# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder,"**/train-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder,"**/train-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)


# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Ett slumpmässigt urval av bilder visas:

![Ett slumpmässigt urval av bilder](./media/tutorial-train-models-with-aml/digits.png)

Nu har du en uppfattning om hur dessa bilder ser ut och det förväntade förutsägelseresultatet.

## <a name="train-on-a-remote-cluster"></a>Träna i ett fjärrkluster

För den här uppgiften skickar du jobbet som ska köras på det kluster för fjärr utbildning som du ställer in tidigare.  Du skickar ett jobb genom att:
* Skapa en katalog
* Skapa ett träningsskript
* Skapa en konfiguration för skript körning
* Skicka jobbet

### <a name="create-a-directory"></a>Skapa en katalog

Skapa en katalog för att leverera den nödvändiga koden från din dator till fjärresursen.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Skapa ett träningsskript

Innan du skickar jobbet till klustret skapar du ett träningsskript. Kör följande kod för att skapa ett träningsskript med namnet `train.py` i katalogen som du nyss skapade.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np
import glob

from sklearn.linear_model import LogisticRegression
import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the dataset to mount or download, and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
X_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0], False) / 255.0
y_train = load_data(glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)
y_test = load_data(glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0], True).reshape(-1)

print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Observera hur skriptet hämtar data och sparar modeller:

+ Träningsskriptet läser ett argument för att hitta katalogen som innehåller data. När du skickar jobbet senare pekar du på datalagret för det här argumentet: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ Övnings skriptet sparar din modell i en katalog med namnet **outputs**. Allt som skrivs i den här katalogen överförs automatiskt till din arbetsyta. Du kommer åt din modell från den här katalogen senare i självstudien. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Utbildnings skriptet kräver `utils.py` att filen läser in data mängden korrekt. Följande kod kopieras `utils.py` till `script_folder` så att filen kan nås tillsammans med övnings skriptet på fjär resursen.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="configure-the-training-job"></a>Konfigurera utbildnings jobbet

Skapa ett [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) -objekt om du vill ange konfigurations information för ditt utbildnings jobb, inklusive ditt utbildnings skript, vilken miljö som ska användas och vilket beräknings mål som ska köras. Konfigurera ScriptRunConfig genom att ange:

* Katalogen som innehåller dina skript. Alla filer i den här katalogen laddas upp till klusternoderna för körning.
* Beräkningsmålet. I det här fallet använder du Azure Machine Learning-beräkningsklustret som du skapade.
* Utbildnings skriptets namn, **Train.py**.
* En miljö som innehåller de bibliotek som krävs för att köra skriptet.
* Argument krävs från övnings skriptet.

I den här självstudien är det här målet AmlCompute. Alla filer i skriptmappen laddas upp till klusternoderna för körning. **--Data_folder** är inställd på att använda data uppsättningen.

Börja med att skapa miljön som innehåller: scikit--biblioteket, azureml-dataset-runtime som krävs för att få åtkomst till data uppsättningen och AzureML-standardvärden som innehåller beroenden för loggnings mått. Azureml-defaults innehåller också de beroenden som krävs för att distribuera modellen som en webb tjänst senare i del 2 av självstudien.

När miljön har definierats kan du registrera den med arbets ytan och sedan använda den igen i del 2 av självstudien.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# to install required packages
env = Environment('tutorial-env')
cd = CondaDependencies.create(pip_packages=['azureml-dataset-runtime[pandas,fuse]', 'azureml-defaults'], conda_packages=['scikit-learn==0.22.1'])

env.python.conda_dependencies = cd

# Register environment to re-use later
env.register(workspace=ws)
```

Skapa sedan ScriptRunConfig genom att ange utbildnings skriptet, beräkna målet och miljön.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', mnist_file_dataset.as_mount(), '--regularization', 0.5]

src = ScriptRunConfig(source_directory=script_folder,
                      script='train.py', 
                      arguments=args,
                      compute_target=compute_target,
                      environment=env)
```

### <a name="submit-the-job-to-the-cluster"></a>Skicka jobbet till klustret

Kör experimentet genom att skicka ScriptRunConfig-objektet:

```python
run = exp.submit(config=src)
run
```

Eftersom anropet är asynkront så returneras statusen **Förbereder** eller **Körs** så snart jobbet har startats.

## <a name="monitor-a-remote-run"></a>Övervaka en fjärrkörning

Den första körningen tar totalt **cirka 10 minuter**. Men för efterföljande körningar så länge skriptberoendena inte ändras så återanvänds samma avbildning. Starttiden för containern är mycket snabbare.

Vad händer medan du väntar:

- **Skapa bild**: en Docker-avbildning skapas som matchar python-miljön som anges i Azure ml-miljön. Avbildningen laddas upp till arbetsytan. Det tar **cirka fem minuter** att skapa och överföra avbildningen.

  Den här fasen sker en gång för varje Python-miljö eftersom containern cachelagras för efterföljande körningar. När avbildningen skapas strömmas loggar till körningshistoriken. Du kan övervaka förloppet för avbildningsgenereringen med de här loggarna.

- **Skalning**: om fjärrklusteret kräver fler noder för att köra körningen än vad som är tillgängligt, läggs ytterligare noder till automatiskt. Skalningen tar normalt **cirka fem minuter.**

- **Körs**: i det här steget skickas nödvändiga skript och filer till Compute-målet. Datalagren monteras eller kopieras därefter. Och sedan körs **entry_script**. Medan jobbet körs strömmas **STDOUT** och katalogen **./logs** till körnings historiken. Du kan övervaka körningens förlopp med hjälp av de här loggarna.

- **Efter bearbetning**: katalogen **./outputs** i körningen kopieras till körnings historiken i din arbets yta, så att du kan komma åt dessa resultat.

Du kan kontrollera förloppet för ett jobb som körs på flera olika sätt. Den här självstudien använder en Jupyter-widget samt en `wait_for_completion`-metod.

### <a name="jupyter-widget"></a>Jupyter-widget

Se förloppet för [widgeten](/python/api/azureml-widgets/azureml.widgets)kör med en Jupyter-widget. Precis som körningsöverföringen så är widgeten asynkron och tillhandahåller liveuppdateringar var 10:e till 15:e sekund tills jobbet slutförts:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Widgeten kommer att se ut så här i slutet av utbildningen:

![Anteckningsbok-widget](./media/tutorial-train-models-with-aml/widget.png)

Om du vill avbryta en körning kan du följa [instruktionerna](./how-to-manage-runs.md).

### <a name="get-log-results-upon-completion"></a>Hämta loggresultat när åtgärden har slutförts

Modellträningen och övervakningen sker i bakgrunden. Vänta tills modellen har slutfört träningen innan du kör mer kod. Använd `wait_for_completion` för att visa när modellträningen är klar:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Visa körningsresultat

Nu har du en modell som har tränats på ett fjärrkluster. Hämta modellens precision:

```python
print(run.get_metrics())
```

Resultatet visar att fjärrmodellen har en riktighet på 0.9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

I nästa självstudie får du analysera den här modellen i detalj.

## <a name="register-model"></a>Registrera modellen

Det sista steget i träningsskriptet skrev filen `outputs/sklearn_mnist_model.pkl` i en katalog med namnet `outputs` på den virtuella datorn i klustret där jobbet körs. `outputs` är en särskild katalog i det avseende att allt innehåll i den här katalogen överförs automatiskt till din arbetsyta. Det här innehållet visas i körningsposten i experimentet under din arbetsyta. Därför är modellfilen nu även tillgänglig på din arbetsyta.

Du kan se filer som hör till den körningen:

```python
print(run.get_file_names())
```

Registrera modellen på arbetsytan så att du eller andra medarbetare senare kan fråga, utforska och distribuera modellen:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Du kan också ta bort bara Azure Machine Learning-beräkningsklustret. Autoskalning är dock aktiverat och det minsta antalet kluster är noll. Så den här resursen debiteras inte ytterligare beräkningsavgifter när den inte används:

```python
# Optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Nästa steg

I den här Azure Machine Learning själv studie kursen har du använt python för följande uppgifter:

> [!div class="checklist"]
> * Konfigurera din utvecklingsmiljö.
> * Kom åt och utforska data.
> * Träna flera modeller i ett fjärrkluster med det populära scikit-learn-maskininlärningsbiblioteket
> * Granska träningsinformationen och registrera den bästa modellen.

Nu är du redo att distribuera den registrerade modellen genom att följa anvisningarna i nästa del av den här självstudieserien:

> [!div class="nextstepaction"]
> [Självstudie 2 – Distribuera modeller](tutorial-deploy-models-with-aml.md)
