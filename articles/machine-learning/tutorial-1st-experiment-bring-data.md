---
title: 'Självstudie: Använd dina egna data'
titleSuffix: Azure Machine Learning
description: Del 4 i Azure Machine Learning kom igång-serien visar hur du kan använda dina egna data i en fjärran sluten utbildning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: tracking-python
ms.openlocfilehash: bbe28f4fda32ce7d55a437e4ac944dc206f436ee
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522367"
---
# <a name="tutorial-use-your-own-data-part-4-of-4"></a>Självstudie: Använd dina egna data (del 4 av 4)

Den här självstudien visar hur du laddar upp och använder dina egna data för att träna maskin inlärnings modeller i Azure Machine Learning.

Den här självstudien är *del 4 i en själv studie serie i fyra delar* där du får lära dig grunderna i Azure Machine Learning och fullständiga jobbbaserade Machine Learning-uppgifter i Azure. Den här självstudien bygger på det arbete som du avslutade i [del 1: konfiguration](tutorial-1st-experiment-sdk-setup-local.md), [del 2: kör "Hello World!"](tutorial-1st-experiment-hello-world.md)och [del 3: träna en modell](tutorial-1st-experiment-sdk-train.md).

I [del 3: träna en modell](tutorial-1st-experiment-sdk-train.md)har data hämtats via den inbyggda `torchvision.datasets.CIFAR10` metoden i PyTorch-API: et. I många fall vill du dock använda dina egna data i en fjärran sluten utbildning. Den här artikeln visar det arbets flöde som du kan använda för att arbeta med dina egna data i Azure Machine Learning.

I den här kursen får du:

> [!div class="checklist"]
> * Konfigurera ett utbildnings skript för att använda data i en lokal katalog.
> * Testa utbildnings skriptet lokalt.
> * Ladda upp data till Azure.
> * Skapa ett kontroll skript.
> * Förstå de nya Azure Machine Learning begreppen (skicka parametrar, data uppsättningar, data lager).
> * Skicka in och kör ditt utbildnings skript.
> * Visa kodens utdata i molnet.

## <a name="prerequisites"></a>Förutsättningar

* Slut för ande av [del 3](tutorial-1st-experiment-sdk-train.md) av serien.

## <a name="adjust-the-training-script"></a>Justera övnings skriptet

Nu har du ditt utbildnings skript (självstudie/src/träna. py) som körs i Azure Machine Learning och du kan övervaka modell prestanda. Låt oss Parameterisera övnings skriptet genom att introducera argument. Genom att använda argument kan du enkelt jämföra olika grundparametrar.

Vårt utbildnings skript har nu ställts in för att ladda ned CIFAR10-datauppsättningen på varje körning. Följande python-kod har justerats för att läsa data från en katalog.

>[!NOTE] 
> Användningen av `argparse` parameterizes-skriptet.

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/code/pytorch-cifar10-your-data/train.py":::

### <a name="understanding-the-code-changes"></a>Förstå kod ändringarna

Koden i `train.py` har använt `argparse` biblioteket för att konfigurera `data_path` , `learning_rate` och `momentum` .

```python
# .... other code
parser = argparse.ArgumentParser()
parser.add_argument('--data_path', type=str, help='Path to the training data')
parser.add_argument('--learning_rate', type=float, default=0.001, help='Learning rate for SGD')
parser.add_argument('--momentum', type=float, default=0.9, help='Momentum for SGD')
args = parser.parse_args()
# ... other code
```

`train.py`Skriptet har också anpassats för att uppdatera optimeringen så att de använder de användardefinierade parametrarna:

```python
optimizer = optim.SGD(
    net.parameters(),
    lr=args.learning_rate,     # get learning rate from command-line argument
    momentum=args.momentum,    # get momentum from command-line argument
)
```
> [!div class="nextstepaction"]
> [Jag justerade det utbildnings skript](?success=adjust-training-script#test-locally) [Jag stötte på ett problem](https://www.research.net/r/7C6W7BQ?issue=adjust-training-script)

## <a name="test-the-script-locally"></a><a name="test-locally"></a> Testa skriptet lokalt

Ditt skript accepterar nu _data Sök vägen_ som ett argument. Börja med genom att testa det lokalt. Lägg till i din själv studie katalog struktur en mapp med namnet `data` . Katalog strukturen bör se ut så här:

:::image type="content" source="media/tutorial-1st-experiment-bring-data/directory-structure.png" alt-text="Katalog strukturen visar. azureml, data och src-under kataloger":::


Om du inte körde `train.py` lokalt i den föregående själv studie kursen har du inte `data/` katalogen. I det här fallet kör du `torchvision.datasets.CIFAR10` metoden lokalt med `download=True` i `train.py` skriptet.

För att köra lokalt, se till att du avslutar själv studie miljön och aktiverar den nya Conda-miljön:

```bash
conda deactivate                # If you are still using the tutorial environment, exit it
```

```bash
conda env create -f .azureml/pytorch-env.yml    # create the new conda environment with updated dependencies
```

```bash
conda activate pytorch-aml-env          # activate new conda environment
```

För att köra det ändrade tränings skriptet lokalt, anropa:

```bash
python src/train.py --data_path ./data --learning_rate 0.003 --momentum 0.92
```

Du undviker att ladda ned CIFAR10-datauppsättningen genom att skicka in en lokal sökväg till data. Du kan också experimentera med olika värden för _inlärnings frekvens_ _och_ avvikande egenskaper utan att behöva hårdkoda dem i övnings skriptet.

> [!div class="nextstepaction"]
> [Jag testade skriptet lokalt](?success=test-locally#upload) [Jag stötte på ett problem](https://www.research.net/r/7C6W7BQ?issue=test-locally)

## <a name="upload-the-data-to-azure"></a><a name="upload"></a> Ladda upp data till Azure

Om du vill köra skriptet i Azure Machine Learning måste du göra dina utbildnings data tillgängliga i Azure. Din Azure Machine Learning-arbetsyta levereras med ett _standard_ data lager. Det här är ett Azure Blob Storage-konto där du kan lagra utbildnings data.

>[!NOTE] 
> Med Azure Machine Learning kan du ansluta andra molnbaserade data lager som lagrar dina data. Mer information finns i dokumentationen för [data lager](./concept-data.md).  

Skapa ett nytt python-kontroll skript `05-upload-data.py` som anropas i `tutorial` katalogen:

:::code language="python" source="~/MachineLearningNotebooks/tutorials/get-started-day1/IDE-users/05-upload-data.py":::

`target_path`Värdet anger sökvägen till data lagret där CIFAR10-data ska överföras.

>[!TIP] 
> När du använder Azure Machine Learning för att överföra data kan du använda [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ladda upp Ad hoc-filer. Om du behöver ett ETL-verktyg kan du använda [Azure Data Factory](../data-factory/introduction.md) för att mata in dina data i Azure.

I fönstret som har den aktiverade *tutorial1* Conda-miljön kör du python-filen för att överföra data. (Uppladdningen ska vara snabb, mindre än 60 sekunder.)

```bash
python 05-upload-data.py
```

Du bör se följande standardutdata:

```txt
Uploading ./data\cifar-10-batches-py\data_batch_2
Uploaded ./data\cifar-10-batches-py\data_batch_2, 4 files out of an estimated total of 9
.
.
Uploading ./data\cifar-10-batches-py\data_batch_5
Uploaded ./data\cifar-10-batches-py\data_batch_5, 9 files out of an estimated total of 9
Uploaded 9 files
```

> [!div class="nextstepaction"]
> [Jag laddade upp de data](?success=upload-data#control-script) [Jag stötte på ett problem](https://www.research.net/r/7C6W7BQ?issue=upload-data)

## <a name="create-a-control-script"></a><a name="control-script"></a> Skapa ett kontroll skript

När du har gjort det tidigare skapar du ett nytt python-kontroll skript med namnet `06-run-pytorch-data.py` :

```python
# 06-run-pytorch-data.py
from azureml.core import Workspace
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import ScriptRunConfig
from azureml.core import Dataset

if __name__ == "__main__":
    ws = Workspace.from_config()
    datastore = ws.get_default_datastore()
    dataset = Dataset.File.from_files(path=(datastore, 'datasets/cifar10'))

    experiment = Experiment(workspace=ws, name='day1-experiment-data')

    config = ScriptRunConfig(
        source_directory='./src',
        script='train.py',
        compute_target='cpu-cluster',
        arguments=[
            '--data_path', dataset.as_named_input('input').as_mount(),
            '--learning_rate', 0.003,
            '--momentum', 0.92],
    )
    # set up pytorch environment
    env = Environment.from_conda_specification(
        name='pytorch-env',
        file_path='./.azureml/pytorch-env.yml'
    )
    config.run_config.environment = env

    run = experiment.submit(config)
    aml_url = run.get_portal_url()
    print("Submitted to compute cluster. Click link below")
    print("")
    print(aml_url)
```

### <a name="understand-the-code-changes"></a>Förstå kod ändringarna

Kontroll skriptet liknar det som finns i [del 3 i den här serien](tutorial-1st-experiment-sdk-train.md), med följande nya rader:

:::row:::
   :::column span="":::
      `dataset = Dataset.File.from_files( ... )`
   :::column-end:::
   :::column span="2":::
      En [data uppsättning](/python/api/azureml-core/azureml.core.dataset.dataset) används för att referera till de data som du överförde till Azure Blob Storage. Data uppsättningar är ett abstraktions lager ovanpå dina data som är utformade för att förbättra tillförlitligheten och tillförlitligheten.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      `config = ScriptRunConfig(...)`
   :::column-end:::
   :::column span="2":::
      [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) har ändrats för att innehålla en lista över argument som ska skickas till `train.py` . `dataset.as_named_input('input').as_mount()`Argumentet innebär att den angivna katalogen kommer att _monteras_ på beräknings målet.
   :::column-end:::
:::row-end:::

> [!div class="nextstepaction"]
> [Jag skapade kontroll skriptet som](?success=control-script#submit-to-cloud) [Jag stötte på ett problem](https://www.research.net/r/7C6W7BQ?issue=control-script)

## <a name="submit-the-run-to-azure-machine-learning"></a><a name="submit-to-cloud"></a> Skicka in körningen till Azure Machine Learning

Skicka nu om körningen för att använda den nya konfigurationen:

```bash
python 06-run-pytorch-data.py
```

I den här koden skrivs en URL ut till experimentet i Azure Machine Learning Studio. Om du går till länken kan du se din kod som körs.

> [!div class="nextstepaction"]
> [Jag skickade om körningen som](?success=submit-to-cloud#inspect-log) [Jag stötte på ett problem](https://www.research.net/r/7C6W7BQ?issue=submit-to-cloud)

### <a name="inspect-the-log-file"></a><a name="inspect-log"></a> Granska logg filen

I Studio går du till experimentet-körningen (genom att välja föregående URL-utdata) följt av **utdata + loggar**. Välj `70_driver_log.txt` filen. Du bör se följande utdata:

```txt
Processing 'input'.
Processing dataset FileDataset
{
  "source": [
    "('workspaceblobstore', 'datasets/cifar10')"
  ],
  "definition": [
    "GetDatastoreFiles"
  ],
  "registration": {
    "id": "XXXXX",
    "name": null,
    "version": null,
    "workspace": "Workspace.create(name='XXXX', subscription_id='XXXX', resource_group='X')"
  }
}
Mounting input to /tmp/tmp9kituvp3.
Mounted input to /tmp/tmp9kituvp3 as folder.
Exit __enter__ of DatasetContextManager
Entering Run History Context Manager.
Current directory:  /mnt/batch/tasks/shared/LS_root/jobs/dsvm-aml/azureml/tutorial-session-3_1600171983_763c5381/mounts/workspaceblobstore/azureml/tutorial-session-3_1600171983_763c5381
Preparing to call script [ train.py ] with arguments: ['--data_path', '$input', '--learning_rate', '0.003', '--momentum', '0.92']
After variable expansion, calling script [ train.py ] with arguments: ['--data_path', '/tmp/tmp9kituvp3', '--learning_rate', '0.003', '--momentum', '0.92']

Script type = None
===== DATA =====
DATA PATH: /tmp/tmp9kituvp3
LIST FILES IN DATA PATH...
['cifar-10-batches-py', 'cifar-10-python.tar.gz']
```

Information

- Azure Machine Learning har monterat Blob Storage till beräknings klustret automatiskt åt dig.
- Det som ``dataset.as_named_input('input').as_mount()`` används i kontroll skriptet matchar monterings punkten.

> [!div class="nextstepaction"]
> [Jag kontrollerade logg filen som](?success=inspect-log#clean-up-resources) [Jag stötte på ett problem](https://www.research.net/r/7C6W7BQ?issue=inspect-log)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Du kan också behålla resursgruppen men ta bort en enstaka arbetsyta. Visa arbetsytans egenskaper och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien såg vi hur du laddar upp data till Azure med hjälp av `Datastore` . Data lagret hanteras som moln lagring för din arbets yta, vilket ger dig en beständig och flexibel plats för att skydda dina data.

Du har sett hur du ändrar ditt utbildnings skript för att acceptera en data Sök väg via kommando raden. Genom `Dataset` att använda, kunde du montera en katalog till fjärrkörningen. 

Nu när du har en modell kan du lära dig:

* [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).
