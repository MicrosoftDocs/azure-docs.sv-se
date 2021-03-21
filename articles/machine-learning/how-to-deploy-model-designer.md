---
title: Använda Studio för att distribuera modeller som har tränats i designern
titleSuffix: Azure Machine Learning
description: Använd Azure Machine Learning Studio för att distribuera Machine Learning-modeller utan att skriva en enda rad kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio, designer
ms.openlocfilehash: 35acfc51ae76fdacef11f03b1fbd91ad58650ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97722631"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Använda Studio för att distribuera modeller som har tränats i designern

I den här artikeln får du lära dig hur du distribuerar en designer-modell som en slut punkt i real tid i Azure Machine Learning Studio.

När du har registrerat eller laddat ned kan du använda designer utbildade modeller precis som vilken annan modell som helst. Exporterade modeller kan distribueras i användnings fall som sakernas internet (IoT) och lokala distributioner.

Distribution i Studio består av följande steg:

1. Registrera den tränade modellen.
1. Hämta start skriptet och Conda beroende filen för modellen.
1. Valfritt Konfigurera Start skriptet.
1. Distribuera modellen till ett beräknings mål.

Du kan också distribuera modeller direkt i designern för att hoppa över modell registrering och fil hämtnings steg. Detta kan vara användbart för snabb distribution. Mer information finns i [distribuera en modell med designern](tutorial-designer-automobile-price-deploy.md).

Modeller som har tränats i designern kan också distribueras via SDK eller kommando rads gränssnittet (CLI). Mer information finns i [distribuera din befintliga modell med Azure Machine Learning](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Förutsättningar

* [En Azure Machine Learning-arbetsyta](how-to-manage-workspace.md)

* En slutförd utbildnings pipeline som innehåller en av följande moduler:
    - [Träna modell modul](./algorithm-module-reference/train-model.md)
    - [Träna avvikelse identifiering modell modul](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Träna klustring modell modul](./algorithm-module-reference/train-clustering-model.md)
    - [Träna modulen Pytorch modell](./algorithm-module-reference/train-pytorch-model.md)
    - [Träna SVD-Rekommenderad modul](./algorithm-module-reference/train-svd-recommender.md)
    - [Träna Vowpal Wabbit Model-modul](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Träna wide & djup modell modul](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Registrera modellen

När utbildnings pipelinen är klar registrerar du den tränade modellen på din Azure Machine Learning-arbetsyta för att få åtkomst till modellen i andra projekt.

1. Välj [modulen träna modell](./algorithm-module-reference/train-model.md).
1. Välj fliken **utdata + loggar** i den högra rutan.
1. Välj ikonen **Registrera modell** ikon ![ på kugg hjuls ikonen ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Skärm bild av den högra rutan i modulen träna modell](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Ange ett namn för din modell och välj sedan **Spara**.

När du har registrerat din modell kan du hitta den på sidan **modeller** till gångar i Studio.
    
![Skärm bild av registrerad modell på sidan modeller till gångar](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Hämta start skript filen och Conda-beroende filen

Du behöver följande filer för att distribuera en modell i Azure Machine Learning Studio:

- **Start skript fil** – läser in den tränade modellen, bearbetar indata från begär Anden, utför inferences i real tid och returnerar resultatet. Designern genererar automatiskt en `score.py` Start skript fil när modulen **träna modell** slutförs.

- **Conda-beroende fil** – anger vilka pip-och Conda-paket som din WebService är beroende av. Designern skapar automatiskt en `conda_env.yaml` fil när modulen **träna modell** slutförs.

Du kan ladda ned dessa två filer i den högra rutan i modulen **träna modell** :

1. Välj **träningsmodellmodulen**.
1. På fliken **utdata + loggar** väljer du mappen `trained_model_outputs` .
1. Ladda ned `conda_env.yaml` filen och `score.py` filen.

    ![Skärm bild av hämtnings bara filer för distribution i den högra rutan](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Du kan också hämta filerna från sidan **modeller** till gångar när du har registrerat din modell:

1. Gå till sidan **modeller** till gångar.
1. Välj den modell som du vill distribuera.
1. Välj fliken **artefakter** .
1. Välj mappen `trained_model_outputs`.
1. Ladda ned `conda_env.yaml` filen och `score.py` filen.  

    ![Skärm bild av Ladda ned filer för distribution på modell informations sida](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> `score.py`Filen ger nästan samma funktioner som modulerna för **Poäng modell** . Vissa moduler som till exempel har [SVD-rekommendation](./algorithm-module-reference/score-svd-recommender.md), [score wide och djup rekommendation](./algorithm-module-reference/score-wide-and-deep-recommender.md)och [Poäng Vowpal Wabbit modell](./algorithm-module-reference/score-vowpal-wabbit-model.md) har parametrar för olika bedömnings lägen. Du kan också ändra parametrarna i Entry-skriptet.
>
>Mer information om hur du anger parametrar i `score.py` filen finns i avsnittet [Konfigurera Entry-skriptet](#configure-the-entry-script).

## <a name="deploy-the-model"></a>Distribuera modellen

När du har hämtat de nödvändiga filerna är du redo att distribuera modellen.

1. På sidan **modeller** till gångar väljer du den registrerade modellen.
1. Välj knappen **distribuera** .
1. I menyn konfiguration anger du följande information:

    - Mata in ett namn för slut punkten.
    - Välj om du vill distribuera modellen till [Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md) eller [Azure Container instance](how-to-deploy-azure-container-instance.md).
    - Överför `score.py` för **Start skript filen**.
    - Ladda upp `conda_env.yml` filen för **Conda-beroenden**. 

    >[!TIP]
    > I **Avancerad** inställning kan du ange processor-/minnes kapacitet och andra parametrar för distribution. De här inställningarna är viktiga för vissa modeller, till exempel PyTorch-modeller, som använder stora mängder memery (cirka 4 GB).

1. Välj **distribuera** för att distribuera din modell som en slut punkt i real tid.

    ![Skärm bild av distribuera modell på sidan modell till gång](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Använda real tids slut punkten

När distributionen har slutförts kan du hitta real tids slut punkten på till gångs sidan för **slut punkter** . När så är fallet hittar du en REST-slutpunkt som klienter kan använda för att skicka förfrågningar till real tids slut punkten. 

> [!NOTE]
> Designern genererar också en exempel data-JSON-fil för testning, som du kan hämta `_samples.json` i mappen **trained_model_outputs** .

Använd följande kod exempel för att använda en slut punkt i real tid.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Använd dator vision relaterad slut punkter i real tid

När du förbrukar dator visioner som är relaterade till real tids slut punkter måste du konvertera avbildningar till byte, eftersom webb tjänsten endast accepterar sträng som indata. Följande är exempel koden:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Konfigurera Start skriptet

Vissa moduler i designern som till exempel från [SVD-rekommendation](./algorithm-module-reference/score-svd-recommender.md), [score wide-och djup rekommendation](./algorithm-module-reference/score-wide-and-deep-recommender.md)och [Poäng Vowpal Wabbit modell](./algorithm-module-reference/score-vowpal-wabbit-model.md) har parametrar för olika bedömnings lägen. 

I det här avsnittet får du lära dig att uppdatera parametrarna i Start skript filen.

I följande exempel uppdateras standard beteendet för en utbildad **bred & djup rekommendations** modell. Som standard `score.py` instruerar filen webb tjänsten att förutse klassificering mellan användare och objekt. 

Du kan ändra start skript filen för att göra objekt rekommendationer och returnera rekommenderade objekt genom att ändra `recommender_prediction_kind` parametern.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

För **breda & djup rekommenderarare** och **Vowpal Wabbit** -modeller kan du konfigurera bedömnings läges parametern med följande metoder:

- Parameter namnen är de små och under streck kombinationerna av parameter namn för [score Vowpal Wabbit-modellen](./algorithm-module-reference/score-vowpal-wabbit-model.md) och [resultat bred och djup rekommendation](./algorithm-module-reference/score-wide-and-deep-recommender.md).
- Parameter värden för mode-typ är strängar med motsvarande alternativ namn. Använd **rekommendations förutsägelse typ** i ovanstående koder som exempel kan värdet vara `'Rating Prediction'` eller `'Item Recommendation'` . Andra värden är inte tillåtna.

Parameter namn och värden kan vara mindre uppenbara, och du kan leta upp tabellerna **nedan för att** bestämma hur parametrar ska ställas in.

| Parameter namn i [Scores SVD-rekommendation](./algorithm-module-reference/score-svd-recommender.md)                           | Parameter namn i Start skript filen |
| ------------------------------------------------------------ | --------------------------------------- |
| Typ av Rekommenderad förutsägelse                                  | prediction_kind                         |
| Val av rekommenderat objekt                                   | recommended_item_selection              |
| Minsta storlek på rekommendations bassäng för en enskild användare    | min_recommendation_pool_size            |
| Maximalt antal objekt att rekommendera till en användare               | max_recommended_item_count              |
| Om de förväntade klassificeringarna för objekten ska returneras tillsammans med etiketterna | return_ratings                          |

Följande kod visar hur du ställer in parametrar för en SVD-rekommendation, som använder alla sex parametrar för att rekommendera klassificerade objekt med förutsägande klassificeringar.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Nästa steg

* [Träna en modell i designern](tutorial-designer-automobile-price-train-score.md)
* [Distribuera modeller med Azure Machine Learning SDK](how-to-deploy-and-where.md)
* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
