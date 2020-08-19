---
title: Träna djup inlärning PyTorch-modeller
titleSuffix: Azure Machine Learning
description: Lär dig hur du kör dina PyTorch-utbildnings skript i företags skala med hjälp av Azure Machine Learning PyTorch uppskattnings klass.  Exempel skripten klassificerar kyckling-och kalkon bilder för att bygga ett djup inlärnings neurala nätverk baserat på PyTorch i guiden för överförings inlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: peterlu
author: peterclu
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 28a2d5c34da9f7996524d29a88c7969adf197b38
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270656"
---
# <a name="train-pytorch-deep-learning-models-at-scale-with-azure-machine-learning"></a>Utbilda Pytorch djup inlärnings modeller i stor skala med Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du kör dina [PyTorch](https://pytorch.org/) -utbildnings skript i företags skala med hjälp av Azure Machine Learning [PyTorchs uppskattnings](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) klass.  

Exempel skripten i den här artikeln används för att klassificera kyckling-och kalkon bilder för att bygga ett djup inlärnings neurala nätverk baserat på PyTorch för överförings [inlärning.](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) 

Oavsett om du tränar en djup inlärnings PyTorch-modell från grunden eller om du använder en befintlig modell i molnet kan du använda Azure Machine Learning för att skala ut utbildnings jobb för öppen källkod med elastiska moln beräknings resurser. Du kan bygga, distribuera, hantera och övervaka modeller av produktions klass med Azure Machine Learning. 

Lär dig mer om [djup inlärning vs Machine Learning](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Krav

Kör den här koden i någon av följande miljöer:

- Azure Machine Learning beräknings instans – inga hämtningar eller installationer behövs

    - Slutför [självstudien: installations miljö och arbets yta](tutorial-1st-experiment-sdk-setup.md) för att skapa en dedikerad Notebook-server som är förinstallerad med SDK och exempel lagrings plats.
    - I mappen exempel djup inlärning på Notebook-servern hittar du en slutförd och utökad antecknings bok genom att gå till den här katalogen: **How-to-use-azureml > Training-with-djupgående-learning > träna-pytorch parameter-finjustera-Deploy-with-** Folder. 
 
 - Din egen Jupyter Notebook Server

    - [Installera Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Skapa en konfigurations fil för arbets ytor](how-to-configure-environment.md#workspace).
    - [Hämta exempel skript filen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch)`pytorch_train.py`
     
    Du kan också hitta en slutförd [Jupyter Notebook version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/pytorch/deployment/train-hyperparameter-tune-deploy-with-pytorch/train-hyperparameter-tune-deploy-with-pytorch.ipynb) av den här guiden på sidan med GitHub-exempel. Antecknings boken innehåller utökade avsnitt som täcker intelligenta parametrar, modell distribution och Notebook-widgetar.

## <a name="set-up-the-experiment"></a>Konfigurera experimentet

I det här avsnittet anges övnings experimentet genom att läsa in de nödvändiga python-paketen, initiera en arbets yta, skapa ett experiment och ladda upp utbildnings data och utbildnings skript.

### <a name="import-packages"></a>Importera paket

Importera först de nödvändiga python-biblioteken.

```Python
import os
import shutil

from azureml.core.workspace import Workspace
from azureml.core import Experiment

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import PyTorch
```

### <a name="initialize-a-workspace"></a>Initiera en arbets yta

[Azure Machine Learning-arbetsytan](concept-workspace.md) är resursen på den översta nivån för tjänsten. Det ger dig en central plats för att arbeta med alla artefakter som du skapar. I python SDK har du åtkomst till arbets ytans artefakter genom att skapa ett [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) objekt.

Skapa ett objekt för arbets ytan från `config.json` filen som skapats i [avsnittet krav](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Skapa ett djup inlärnings experiment

Skapa ett experiment och en mapp för att lagra dina utbildnings skript. I det här exemplet skapar du ett experiment med namnet "pytorch-fåglar".

```Python
project_folder = './pytorch-birds'
os.makedirs(project_folder, exist_ok=True)

experiment_name = 'pytorch-birds'
experiment = Experiment(ws, name=experiment_name)
```

### <a name="get-the-data"></a>Hämta data

Data uppsättningen består av ungefär 120 utbildnings avbildningar för kalkoner och kycklingar, med 100 verifierings avbildningar för varje klass. Vi kommer att ladda ned och extrahera data uppsättningen som en del av vårt övnings skript `pytorch_train.py` . Avbildningarna är en delmängd av de [Öppna avbildningarna i V5-uppsättningen](https://storage.googleapis.com/openimages/web/index.html).

### <a name="prepare-training-scripts"></a>Förbereda utbildnings skript

I den här självstudien har övnings skriptet `pytorch_train.py` redan angetts. I praktiken kan du ta med ett anpassat utbildnings skript, som det är och köra det med Azure Machine Learning.

Ladda upp utbildnings skriptet Pytorch `pytorch_train.py` .

```Python
shutil.copy('pytorch_train.py', project_folder)
```

Men om du vill använda funktioner för Azure Machine Learning spårning och mått måste du lägga till en liten pris kod i ditt utbildnings skript. Exempel på mått spårning hittar du i `pytorch_train.py` .

## <a name="create-a-compute-target"></a>Skapa ett beräknings mål

Skapa ett beräknings mål för ditt PyTorch-jobb som ska köras. I det här exemplet skapar du ett GPU-aktiverat Azure Machine Learning beräknings kluster.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Mer information om beräknings mål finns i artikeln [Vad är en Compute Target](concept-compute-target.md) -artikel.

## <a name="create-a-pytorch-estimator"></a>Skapa en PyTorch-uppskattning

[PyTorch-uppskattningen](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) ger ett enkelt sätt att starta ett PyTorch utbildnings jobb på ett beräknings mål.

PyTorch-uppskattningen implementeras via den generiska [`estimator`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) klassen, som kan användas för att stödja eventuella ramverk. Mer information om tränings modeller med hjälp av den generiska uppskattningen finns i [träna modeller med Azure Machine Learning med hjälp av uppskattning](how-to-train-ml-models.md)

Om ditt utbildnings skript behöver ytterligare pip-eller Conda-paket för att kunna köras, kan du ha paketen installerade på den resulterande Docker-avbildningen genom att skicka namnen via `pip_packages` `conda_packages` argumenten och.

```Python
script_params = {
    '--num_epochs': 30,
    '--output_dir': './outputs'
}

estimator = PyTorch(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='pytorch_train.py',
                    use_gpu=True,
                    pip_packages=['pillow==5.4.1'])
```

> [!WARNING]
> Azure Machine Learning kör utbildnings skript genom att kopiera hela käll katalogen. Om du har känsliga data som du inte vill överföra använder du en [. IGNORE-fil](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) eller inkluderar den inte i käll katalogen. I stället kan du komma åt dina data med hjälp av ett data [lager](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).

Mer information om hur du anpassar din python-miljö finns i [skapa och hantera miljöer för utbildning och distribution](how-to-use-environments.md).

## <a name="submit-a-run"></a>Skicka in en körning

[Kör-objektet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) tillhandahåller gränssnittet till körnings historiken medan jobbet körs och när det har slutförts.

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

När körningen körs går den igenom följande steg:

- **Förbereder**: en Docker-avbildning skapas enligt PyTorch-uppskattningen. Avbildningen överförs till arbets ytans behållar register och cachelagras för senare körningar. Loggarna strömmas också till körnings historiken och kan visas för att övervaka förloppet.

- **Skalning**: klustret försöker skala upp om det batch AI klustret kräver fler noder för att köra körning än vad som är tillgängligt.

- **Körs**: alla skript i mappen skript överförs till Compute-målet, data lager monteras eller kopieras och entry_script körs. Utdata från STDOUT och./logs-mappen strömmas till körnings historiken och kan användas för att övervaka körningen.

- **Efter bearbetning**: mappen./outputs i körningen kopieras till körnings historiken.

## <a name="register-or-download-a-model"></a>Registrera eller ladda ned en modell

När du har tränat modellen kan du registrera den på din arbets yta. Med modell registreringen kan du lagra och version av dina modeller i din arbets yta för att förenkla [modell hantering och distribution](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='pt-dnn', model_path='outputs/')
```

> [!TIP]
> Den modell som du precis har registrerat har distribuerats exakt på samma sätt som andra registrerade modeller i Azure Machine Learning, oavsett vilken uppskattning som du använde för utbildning. Distributions anvisningar innehåller ett avsnitt om att registrera modeller, men du kan hoppa direkt till att [skapa ett beräknings mål](how-to-deploy-and-where.md#choose-a-compute-target) för distribution, eftersom du redan har en registrerad modell.

Du kan också hämta en lokal kopia av modellen med hjälp av objektet kör. I övnings skriptet bevarar `pytorch_train.py` ett PyTorch Save-objekt modellen till en lokal mapp (lokal till beräknings målet). Du kan använda kör-objektet för att ladda ned en kopia.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Distribuerad träning

[`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)Uppskattningen stöder också distribuerad utbildning mellan processor-och GPU-kluster. Du kan enkelt köra distribuerade PyTorch-jobb och Azure Machine Learning hanterar dirigeringen åt dig.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) är en öppen källkod som alla reducerar ramverk för distribuerad utbildning som utvecklas av Uber. Den erbjuder en enkel väg till distribuerade GPU PyTorch-jobb.

Om du vill använda Horovod anger du ett [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) objekt för `distributed_training` parametern i PyTorch-konstruktorn. Den här parametern säkerställer att Horovod-biblioteket installeras för användning i ditt utbildnings skript.


```Python
from azureml.train.dnn import PyTorch

estimator= PyTorch(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.4',
                      use_gpu=True)
```
Horovod och dess beroenden kommer att installeras åt dig, så att du kan importera det i utbildnings skriptet på `train.py` följande sätt:

```Python
import torch
import horovod
```
## <a name="export-to-onnx"></a>Exportera till ONNX

Du kan optimera härledningen med [ONNX-körningen](concept-onnx.md)genom att konvertera din tränade PyTorch-modell till ONNX-formatet. Härlednings-eller modell poängsättning är den fas där den distribuerade modellen används för förutsägelse, oftast på produktions data. Se [självstudien](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) för ett exempel.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du tränat och registrerat en djup inlärning, neurala Network med PyTorch på Azure Machine Learning. Om du vill lära dig hur du distribuerar en modell fortsätter du till vår modell distributions artikel.

> [!div class="nextstepaction"]
> [Hur och var modeller ska distribueras](how-to-deploy-and-where.md)
* [Spåra körnings mått under träning](how-to-track-experiments.md)
* [Justering av hyperparametrar](how-to-tune-hyperparameters.md)
* [Distribuera en tränad modell](how-to-deploy-and-where.md)
* [Referens arkitektur för distribuerad djup inlärnings utbildning i Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)

