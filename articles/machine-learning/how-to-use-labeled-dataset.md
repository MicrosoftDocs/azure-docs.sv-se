---
title: Skapa och utforska data uppsättningar med etiketter
titleSuffix: Azure Machine Learning
description: Lär dig hur du exporterar data etiketter från Azure Machine Learning att märka projekt och använda dem för Machine Learning-uppgifter.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: 97922cc8693cddcb13350117b6c17c4220f75337
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227984"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Skapa och utforska Azure Machine Learning data uppsättning med etiketter

I den här artikeln får du lära dig hur du exporterar data etiketter från ett Azure Machine Learning data märknings projekt och läser in dem i populära format, till exempel en Pandas-dataframe för data utforskning eller en Torchvision-datauppsättning för avbildnings omvandling. 

## <a name="what-are-datasets-with-labels"></a>Vad är data uppsättningar med etiketter 

Vi refererar till Azure Machine Learning data uppsättningar med etiketter som etiketterade data uppsättningar. Dessa angivna data uppsättnings typer med märkta data uppsättningar skapas bara som utdata från Azure Machine Learning data märknings projekt. Skapa ett projekt med data etiketter med [de här stegen](how-to-create-labeling-projects.md). Machine Learning stöder data märknings projekt för bild klassificering, antingen flera etiketter eller flera klasser och objekt identifiering tillsammans med gränser rutor.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://aka.ms/AMLFree) innan du börjar.
* [Azure Machine Learning SDK för python](/python/api/overview/azure/ml/intro)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).
    * Installera [Azure-contrib-dataset-](/python/api/azureml-contrib-dataset/) paketet
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* Åtkomst till ett projekt med Azure Machine Learning data etiketter. Om du inte har ett etikett projekt kan du skapa ett med [de här stegen](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exportera data etiketter 

När du har slutfört ett data etiketts projekt kan du exportera etikett data från ett etikett projekt. På så sätt kan du samla både referensen till data och dess etiketter och exportera dem i [Coco-format](http://cocodataset.org/#format-data) eller som en Azure Machine Learning data uppsättning. Använd knappen **Exportera** på sidan **projekt information** i ditt projekt med etiketter.

### <a name="coco"></a>COCO 

 COCO-filen skapas i standard-BLOB-arkivet för Azure Machine Learning arbets ytan i en mapp i *export-/Coco*. 
 
>[!NOTE]
>I objekt identifierings projekt normaliseras de exporterade "bbox": [x, y, width, height] "-värden i COCO-filen. De skalas till 1. Exempel: en avgränsnings ruta på (10, 10) plats, med 30 pixlars bredd, 60 bild punkter höjd i en bild av 640x480 bild punkter (0,015625). 0,02083, 0,046875, 0,125). Eftersom coordintes är normaliserat visas det som "0,0" som "width" och "Height" för alla bilder. Den faktiska bredden och höjden kan hämtas med hjälp av python-biblioteket som OpenCV eller Pillow (PIL).

### <a name="azure-machine-learning-dataset"></a>Azure Machine Learning data uppsättning

Du kan komma åt den exporterade Azure Machine Learning data uppsättningen i avsnittet **data uppsättningar** i Azure Machine Learning Studio. På sidan data uppsättnings **information** finns också exempel kod för att få åtkomst till dina etiketter från python.

![Exporterad data uppsättning](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Utforska etiketterade data uppsättningar

Läs in dina märkta data uppsättningar i en Pandas dataframe-eller Torchvision-datauppsättning för att dra nytta av populära bibliotek med öppen källkod för data utforskning, samt PyTorch tillhandahållna bibliotek för avbildnings omvandling och utbildning.

### <a name="pandas-dataframe"></a>Pandas dataframe

Du kan läsa in märkta data uppsättningar i en Pandas-dataframe med [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) metoden från- `azureml-contrib-dataset` klassen. Installera-klassen med följande Shell-kommando: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Namn rymden azureml. contrib ändras ofta, medan vi arbetar för att förbättra tjänsten. Därför bör allt i det här namn området betraktas som en för hands version och stöds inte fullt ut av Microsoft.

Azure Machine Learning erbjuder följande fil hanterings alternativ för fil strömmar vid konvertering till en Pandas-dataframe.
* Hämta: Ladda ned dina datafiler till en lokal sökväg.
* Montera: montera dina datafiler till en monterings punkt. Monteringen fungerar bara för Linux-baserad beräkning, inklusive Azure Machine Learning VM för bärbara datorer och Azure Machine Learning Compute.

I följande kod `animal_labels` är data uppsättningen utdata från ett etikett projekt som tidigare sparats på arbets ytan.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Torchvision data uppsättningar

Du kan läsa in märkta data uppsättningar i Torchvision data uppsättning med metoden [to_torchvision ()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset#to-torchvision--) även från `azureml-contrib-dataset` klassen. Om du vill använda den här metoden måste du ha [PyTorch](https://pytorch.org/) installerat. 

I följande kod `animal_labels` är data uppsättningen utdata från ett etikett projekt som tidigare sparats på arbets ytan.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Nästa steg

* Se [data uppsättningen with Labels Notebook](/azure/machine-learning/how-to-use-labeled-dataset) for Complete Training-exemplet.
