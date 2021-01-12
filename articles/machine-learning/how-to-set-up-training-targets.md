---
title: Konfigurera en tränings körning
titleSuffix: Azure Machine Learning
description: Träna din Machine Learning-modell på olika utbildnings miljöer (beräknings mål). Du kan enkelt växla mellan utbildnings miljöer.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: a5764a9f230540d58edf71e8c00781e86589aa9a
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070175"
---
# <a name="configure-and-submit-training-runs"></a>Konfigurera och skicka träningskörningar

I den här artikeln får du lära dig hur du konfigurerar och skickar Azure Machine Learning körs för att träna dina modeller. Kodfragment i kod förklarar viktiga delar av konfigurationen och sändningen av ett utbildnings skript.  Använd sedan ett av [exempel antecknings böckerna](#notebooks) för att hitta kompletta arbets exempel.

När det gäller utbildning är det vanligt att starta på den lokala datorn och sedan skala ut till ett molnbaserade kluster. Med Azure Machine Learning kan du köra skriptet på olika beräknings mål utan att behöva ändra ditt utbildnings skript.

Allt du behöver göra är att definiera miljön för varje beräknings mål i en **skript körnings konfiguration**.  När du sedan vill köra ditt utbildnings experiment på ett annat beräknings mål anger du körnings konfigurationen för den beräkningen.

## <a name="prerequisites"></a>Förutsättningar

* Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree) idag
* [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (>= 1.13.0)
* En [Azure Machine Learning arbets yta](how-to-manage-workspace.md), `ws`
* Ett beräknings mål, `my_compute_target` .  [Skapa ett beräknings mål](how-to-create-attach-compute-studio.md) 

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>Vad är en skript körnings konfiguration?
En [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) används för att konfigurera den information som krävs för att skicka in en utbildning som en del av ett experiment.

Du skickar in ditt utbildnings experiment med ett ScriptRunConfig-objekt.  Det här objektet innehåller:

* **source_directory**: käll katalogen som innehåller ditt utbildnings skript
* **skript**: det utbildnings skript som ska köras
* **compute_target**: det beräknings mål som ska köras
* **miljö**: miljön som ska användas när skriptet körs
* och vissa ytterligare konfigurerbara alternativ (se [referens dokumentationen](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) för mer information)

## <a name="train-your-model"></a><a id="submit"></a>Träna modellen

Kod mönstret för att skicka en tränings körning är detsamma för alla typer av beräknings mål:

1. Skapa ett experiment att köra
1. Skapa en miljö där skriptet ska köras
1. Skapa en ScriptRunConfig som anger beräknings mål och miljö
1. Skicka körningen
1. Vänta tills körningen har slutförts

Eller så kan du:

* Skicka en HyperDrive-körning för inställning av min [parameter](how-to-tune-hyperparameters.md).
* Skicka ett experiment via [vs Code-tillägget](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model).

## <a name="create-an-experiment"></a>Skapa ett experiment

Skapa ett experiment i din arbets yta.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'
experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="select-a-compute-target"></a>Välj ett beräknings mål

Välj det beräknings mål där ditt utbildnings skript ska köras. Om du inte anger något beräknings mål i ScriptRunConfig, eller om `compute_target='local'` , kommer Azure ml att köra skriptet lokalt. 

I exempel koden i den här artikeln förutsätter vi att du redan har skapat ett beräknings mål `my_compute_target` från avsnittet "förutsättningar".

## <a name="create-an-environment"></a>Skapa en miljö
Azure Machine Learning [miljöer](concept-environments.md) är en inkapsling av miljön där din Machine Learning-utbildning sker. De specificerar python-paketen, Docker-avbildningen, miljövariablerna och program varu inställningarna kring dina utbildnings-och Poäng skript. De anger också körningar (python, Spark eller Docker).

Du kan antingen definiera en egen miljö eller använda en Azure ML-granskad miljö. [Granskade miljöer](./how-to-use-environments.md#use-a-curated-environment) är fördefinierade miljöer som är tillgängliga i arbets ytan som standard. De här miljöerna backas upp av cachelagrade Docker-avbildningar som minskar kostnaden för att köra förberedelser. Se [Azure Machine Learning granskade miljöer](./resource-curated-environments.md) för en fullständig lista över tillgängliga granskade miljöer.

För ett fjärrberäknings mål kan du använda någon av dessa populära granskade miljöer för att börja med:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Mer information och information om miljöer finns [i skapa & använda program varu miljöer i Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a><a name="local"></a>Lokalt beräknings mål

Om ditt beräknings mål är din **lokala dator** ansvarar du för att se till att alla nödvändiga paket är tillgängliga i python-miljön där skriptet körs.  Använd `python.user_managed_dependencies` för att använda din aktuella python-miljö (eller python på den sökväg som du anger).

```python
from azureml.core import Environment

myenv = Environment("user-managed-env")
myenv.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
# myenv.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-the-script-run-configuration"></a>Skapa skript körnings konfigurationen

Nu när du har ett beräknings mål ( `my_compute_target` ) och miljö ( `myenv` ) skapar du en skript körnings konfiguration som kör ditt utbildnings skript ( `train.py` ) som finns i din `project_folder` katalog:

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=project_folder,
                      script='train.py',
                      compute_target=my_compute_target,
                      environment=myenv)

# Set compute target
# Skip this if you are running on your local computer
script_run_config.run_config.target = my_compute_target
```

Om du inte anger någon miljö skapas en standard miljö åt dig.

Om du har kommando rads argument som du vill skicka till ditt utbildnings skript kan du ange dem via **`arguments`** parametern för ScriptRunConfig-konstruktorn, t. ex. `arguments=['--arg1', arg1_val, '--arg2', arg2_val]` .

Om du vill åsidosätta den maximala standard tiden som tillåts för körningen kan du göra det via **`max_run_duration_seconds`** parametern. Systemet kommer att försöka att automatiskt avbryta körningen om det tar längre tid än det här värdet.

### <a name="specify-a-distributed-job-configuration"></a>Ange en konfiguration för distribuerat jobb
Om du vill köra ett distribuerat utbildnings jobb anger du den distribuerade jobbbaserade konfigurationen till **`distributed_job_config`** parametern. Konfigurations typerna som stöds är [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py)och [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py). 

Mer information och exempel på hur du kör distribuerade Horovod-, TensorFlow-och PyTorch-jobb finns i:

* [Utbilda TensorFlow-modeller](./how-to-train-tensorflow.md#distributed-training)
* [Utbilda PyTorch-modeller](./how-to-train-pytorch.md#distributed-training)

## <a name="submit-the-experiment"></a>Skicka experimentet

```python
run = experiment.submit(config=src)
run.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> När du skickar utbildningen skapas en ögonblicks bild av den katalog som innehåller dina utbildnings skript och skickas till beräknings målet. Den lagras också som en del av experimentet i din arbets yta. Om du ändrar filer och skickar körningen igen kommer bara de ändrade filerna att överföras.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Mer information om ögonblicks bilder finns i [ögonblicks bilder](concept-azure-machine-learning-architecture.md#snapshots).

> [!IMPORTANT]
> **Särskilda mappar** Två mappar, *utdata* och *loggar*, tar emot särskild behandling av Azure Machine Learning. När du skriver filer till mappar med namnet *utdata* och *loggar* som är relativa till rot katalogen ( `./outputs` och `./logs` respektive) överförs filerna automatiskt till din körnings historik så att du har åtkomst till dem när körningen är färdig.
>
> Om du vill skapa artefakter under träning (till exempel modell filer, kontroll punkter, datafiler eller ritade bilder) skriver du dessa till `./outputs` mappen.
>
> På samma sätt kan du skriva loggar från din utbildning som körs till `./logs` mappen. Om du vill använda Azure Machine Learning [TensorBoard-integrering](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/tensorboard/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) kontrollerar du att du skriver dina TensorBoard-loggar till den här mappen. När körningen pågår kommer du att kunna starta TensorBoard och strömma dessa loggar.  Senare kommer du även att kunna återställa loggarna från alla tidigare körningar.
>
> Till exempel för att ladda ned en fil som skrivits till mappen *utdata* till din lokala dator efter att din fjärran sluten utbildning har körts: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

## <a name="git-tracking-and-integration"></a><a id="gitintegration"></a>Git-spårning och integrering

När du startar en utbildning som kör där käll katalogen är en lokal git-lagringsplats, lagras information om lagrings platsen i körnings historiken. Mer information finns i [git-integrering för Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="notebook-examples"></a><a name="notebooks"></a>Exempel på bärbara datorer

I de här antecknings böckerna finns exempel på hur du konfigurerar körningar i olika utbildnings scenarier:
* [Utbildning på olika beräknings mål](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Utbildning med ML-ramverk](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)
* [Självstudier/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="troubleshooting"></a>Felsökning

 * **ModuleErrors (ingen modul med namnet)**: om du kör i ModuleErrors när du skickar experiment i Azure ml förväntar sig övnings skriptet ett paket som ska installeras, men det läggs inte till. När du har angett paket namnet installerar Azure ML paketet i den miljö som används för att köra din utbildning.

    Om du använder uppskattningar för att skicka experiment kan du ange ett paket namn via `pip_packages` eller `conda_packages` parameter i uppskattningen baserat på från vilken källa du vill installera paketet. Du kan också ange en YML-fil med alla dina beroenden med `conda_dependencies_file` eller lista alla dina pip-krav i en txt-fil med hjälp av `pip_requirements_file` parametern. Om du har ett eget Azure ML-miljö objekt som du vill åsidosätta standard avbildningen som används av uppskattaren kan du ange den miljön via- `environment` parametern för den uppskattade konstruktorn.
    
    Azure ML-underhållna Docker-avbildningar och deras innehåll kan visas i [azureml-behållare](https://github.com/Azure/AzureML-Containers).
    De Ramverks-/regionsspecifika beroendena visas i respektive Ramverks dokumentation:
    *  [Chainer](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    * [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    *  [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks)
    
    > [!Note]
    > Om du tror att ett visst paket är tillräckligt vanligt för att läggas till i Azure ML-underhållna bilder och miljöer kan du generera ett GitHub-problem i [azureml-behållare](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (namn har inte definierats), AttributeError (objektet har inget attribut)**: det här undantaget bör komma från dina utbildnings skript. Du kan titta på loggfilerna från Azure Portal för att få mer information om det angivna namnet inte är definierat eller ett attributvärde. Från SDK kan du använda `run.get_details()` för att titta på fel meddelandet. Detta visar även alla loggfiler som genererats för din körning. Se till att ta en titt på ditt utbildnings skript och åtgärda felet innan du skickar om körningen. 


* **Körning eller experimentering**: experiment kan arkiveras med hjälp av metoden [experiment. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) eller från fliken experiment i Azure Machine Learning Studio-klienten via "arkivera experiment"-knappen. Den här åtgärden döljer experimentet från List frågor och vyer, men tar inte bort den.

    Permanent borttagning av enskilda experiment eller körningar stöds för närvarande inte. Mer information om hur du tar bort arbets ytans till gångar finns i [Exportera eller ta bort data för Machine Learning service-arbetsytan](how-to-export-delete-data.md).

* **Mått dokumentet är för stort**: Azure Machine Learning har interna gränser för mått objekts storlek som kan loggas samtidigt från en utbildnings körning. Om du ser felet ”Måttdokumentet är för stort” när ett mått med listor som värdetyp kan du prova att dela upp listan i mindre segment, till exempel:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internt sammanfogar Azure ML blocken med samma måttnamn i en sammanhängande lista.

* **Körningen Miss `jwt.exceptions.DecodeError` lyckas med**: exakt fel meddelande: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` . 
    
    Överväg att uppgradera till den senaste versionen av azureml-Core: `pip install -U azureml-core` .
    
    Om du kör det här problemet för lokala körningar kontrollerar du vilken version av PyJWT som är installerad i din miljö där du startar körningarna. De versioner av PyJWT som stöds är < 2.0.0. Avinstallera PyJWT från miljön om versionen är >= 2.0.0. Du kan kontrol lera versionen av PyJWT, avinstallera och installera rätt version på följande sätt:
    1. Starta ett kommando gränssnitt, aktivera Conda-miljö där azureml-Core är installerat.
    2. Ange `pip freeze` och leta efter `PyJWT` , om den hittas, ska den version som visas < 2.0.0
    3. Om den listade versionen inte är en version som stöds går du till `pip uninstall PyJWT` kommando gränssnittet och anger y för bekräftelse.
    4. Installera med `pip install 'PyJWT<2.0.0'`
    
    Överväg att använda den senaste versionen av azureml-core i den aktuella miljön om du skickar en miljö som skapats av användare med din körning. Versioner >= 1.18.0 azureml-Core redan PyJWT < 2.0.0. Om du behöver använda en version av azureml-Core < 1.18.0 i den miljö som du skickar, måste du ange PyJWT < 2.0.0 i dina pip-beroenden.

* **Compute Target tar lång tid att starta**: Docker-avbildningarna för beräknings mål läses in från Azure Container Registry (ACR). Azure Machine Learning skapar som standard en ACR som använder tjänst nivån *Basic* . Att ändra ACR för arbets ytan till standard-eller Premium-nivån kan minska den tid det tar att bygga och läsa in bilder. Mer information finns i [Azure Container Registry tjänst nivåer](../container-registry/container-registry-skus.md).

## <a name="next-steps"></a>Nästa steg

* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* Se hur du tränar modeller med speciella ML-ramverk, till exempel [Scikit – lära](how-to-train-scikit-learn.md), [TensorFlow](how-to-train-tensorflow.md)och [PyTorch](how-to-train-pytorch.md).
* Lär dig hur du [effektivt justerar disponeringsparametrarna](how-to-tune-hyperparameters.md) för att bygga bättre modeller.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* Visa SDK-referens för [ScriptRunConfig-klass](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) .
* [Använda Azure Machine Learning med virtuella Azure-nätverk](./how-to-network-security-overview.md)
