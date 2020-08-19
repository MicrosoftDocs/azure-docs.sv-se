---
title: Skapa data uppsättningar med Azure Open-datauppsättningar
titleSuffix: Azure Open Datasets
description: Lär dig hur du skapar en Azure Machine Learning data uppsättning från Azure Open DataSet.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: 96eeafd7e434e2c3a42311999fcd4979531df2aa
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584887"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Skapa Azure Machine Learning data uppsättningar från Azure Open data uppsättningar
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du kan använda granskade berikade data i dina lokala eller fjärranslutna Machine Learning-experiment med [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) data uppsättningar och [Azure Open-datauppsättningar](https://docs.microsoft.com/azure/open-datasets/). 

Genom att skapa en [Azure Machine Learning data uppsättning](../machine-learning/how-to-create-register-datasets.md)skapar du en referens till data källans plats, tillsammans med en kopia av dess metadata. Eftersom data uppsättningar är Lazy utvärderas och data finns kvar på sin befintliga plats,
* Det kostar ingen extra lagrings kostnad.
* Riskerar inte att oavsiktligt ändra dina ursprungliga data källor. 
* Förbättra prestanda hastigheten i ML-arbetsflöde.

Information om var data uppsättningar passar i Azure Machine Learning programmets övergripande data åtkomst arbets flöde finns i artikeln [säker åtkomst till data](../machine-learning/concept-data.md#data-workflow) .

Azure Open-datauppsättningar är granskade offentliga data uppsättningar som du kan använda för att lägga till scenario-/regionsspecifika funktioner för att utöka dina förutsägelse lösningar och förbättra deras noggrannhet. Se [katalogen öppna data uppsättningar](https://azure.microsoft.com/services/op[en-datasets/catalog/) för offentliga domän data som kan hjälpa dig att träna Machine Learning-modeller, t. ex.:

* [baserad](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [räkning](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [högtid](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [allmän säkerhet](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* location

Öppna data uppsättningar finns i molnet på Microsoft Azure och ingår i både [Azure Machine Learning python SDK](#create-datasets-with-the-sdk) och [Azure Machine Learning Studio](#create-datasets-with-the-studio).


## <a name="prerequisites"></a>Förutsättningar

I den här artikeln behöver du:

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

* En [Azure Machine Learning-arbetsyta](../machine-learning/how-to-manage-workspace.md).

* [Azure Machine Learning SDK för python installerat](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), som innehåller `azureml-datasets` paketet.

    * Skapa en [Azure Machine Learning beräknings instans](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), som är en helt konfigurerad och hanterad utvecklings miljö som innehåller integrerade antecknings böcker och SDK redan har installerats.

    **ELLER**

    * Arbeta i din egen python-miljö och installera SDK: n själv med [de här anvisningarna](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Vissa data uppsättnings klasser är beroende av [azureml-nu-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) paketet, som endast är kompatibelt med 64-bitars python. För Linux-användare stöds dessa klasser endast för följande distributioner: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) och CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Skapa data uppsättningar med SDK

Om du vill skapa Azure Machine Learning data uppsättningar via Azure Open dataset-klasser i python SDK, kontrollerar du att du har installerat paketet med `pip install azureml-opendatasets` . Varje diskret data uppsättning representeras av sin egen klass i SDK och vissa klasser är tillgängliga som antingen en Azure Machine Learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)eller båda. En fullständig lista över klasser finns i [referens dokumentationen](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) `opendatasets` .

Du kan hämta vissa `opendatasets` klasser som antingen en `TabularDataset` eller `FileDataset` , vilket gör att du kan manipulera och/eller ladda ned filerna direkt. Andra klasser kan **bara** hämta en data uppsättning genom att använda- `get_tabular_dataset()` eller- `get_file_dataset()` funktionerna från- `Dataset` klassen i python SDK.

Följande kod visar att `opendatasets` klassen MNIST kan returnera antingen en `TabularDataset` eller `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

I det här exemplet är diabetes- `opendatasets` klassen bara tillgänglig som en `TabularDataset` , och därför används av `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Registrera data uppsättningar

Registrera en Azure Machine Learning data uppsättning med din arbets yta, så att du kan dela dem med andra och återanvända dem för experiment i din arbets yta. När du registrerar en Azure Machine Learning data uppsättning som skapats från öppna data uppsättningar hämtas inga data direkt, men data kommer att kommas åt senare när de begärs (under utbildning, till exempel) från en central lagrings plats.

Om du vill registrera dina data uppsättningar med en arbets yta använder du- [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metoden. 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Skapa data uppsättningar med Studio

Du kan också skapa Azure Machine Learning data uppsättningar från Azure Open data uppsättningar med [Azure Machine Learning Studio](https://ml.azure.com), ett konsoliderat webb gränssnitt som innehåller maskin inlärnings verktyg för att utföra data vetenskaps scenarier för utbildnings nivåer för data vetenskap.

> [!Note]
> Data uppsättningar som skapats med Azure Machine Learning Studio registreras automatiskt på arbets ytan.

1. I arbets ytan väljer du fliken **data uppsättningar** under **till gångar**. Välj **från öppna data uppsättningar**på list menyn **skapa data uppsättning** .

    ![Öppna data uppsättning med användar gränssnittet](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Välj en data uppsättning genom att välja dess panel. (Du kan välja att filtrera med hjälp av Sök fältet.) Välj **Nästa**.

    ![Välj data uppsättning](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Välj ett namn under vilket du vill registrera data uppsättningen och filtrera sedan data genom att använda de tillgängliga filtren. I det här fallet filtrerar du tids perioden till ett år och lands koden för den **offentliga helgdags** data uppsättningen till endast USA. Se [Azure Open DataSets-katalogen](https://azure.microsoft.com/services/op[en-datasets/catalog/) för data information, till exempel fält beskrivningar och datum intervall. Välj **Skapa**.

    ![Ange data uppsättnings parametrar och skapa data uppsättning](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Data uppsättningen är nu tillgänglig i din arbets yta under **data uppsättningar**. Du kan använda den på samma sätt som andra data uppsättningar som du har skapat.


## <a name="access-datasets-for-your-experiments"></a>Få åtkomst till data uppsättningar för dina experiment

Använd dina data uppsättningar i Machine Learning-experiment för att träna ML-modeller. [Lär dig mer om hur du tränar med data uppsättningar](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Exempelnotebook-filer

Exempel och demonstrationer av funktioner för öppen data uppsättningar finns i dessa [exempel antecknings böcker](samples.md).

## <a name="next-steps"></a>Nästa steg

* [Träna din första ml-modell](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* [Träna med data uppsättningar](../machine-learning/how-to-train-with-datasets.md).

* [Skapa en Azure Machine Learning-datauppsättning](../machine-learning/how-to-create-register-datasets.md).



