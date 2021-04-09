---
title: Profil modell minne och CPU-användning
titleSuffix: Azure Machine Learning
description: Lär dig att profilera din modell före distributionen. Profilering bestämmer modellens minne och CPU-användning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93320411"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Profilera din modell för att fastställa resursutnyttjande

Den här artikeln visar hur du kan profilera en maskin inlärning till modell för att avgöra hur mycket processor och minne du behöver allokera för modellen när du distribuerar den som en webb tjänst.

## <a name="prerequisites"></a>Förutsättningar

I den här artikeln förutsätter vi att du har tränat och registrerat en modell med Azure Machine Learning. I [exempel kursen](how-to-train-scikit-learn.md) hittar du ett exempel på utbildning och hur du registrerar en scikit-modell med Azure Machine Learning.

## <a name="limitations"></a>Begränsningar

* Profilering fungerar inte när Azure Container Registry (ACR) för din arbets yta ligger bakom ett virtuellt nätverk.

## <a name="run-the-profiler"></a>Kör profileraren

När du har registrerat din modell och för berett de andra komponenterna som krävs för distributionen kan du fastställa den processor och det minne som den distribuerade tjänsten kommer att behöva. Profilering testar tjänsten som kör din modell och returnerar information som processor användning, minnes användning och svars fördröjning. Den innehåller också en rekommendation för CPU och minne baserat på resursanvändningen.

För att du ska kunna profilera din modell behöver du:
* En registrerad modell.
* En konfigurations konfiguration som baseras på definitionen av ditt Start skript och en härlednings miljö.
* En tabell data uppsättning med en kolumn, där varje rad innehåller en sträng som representerar exempel begär ande data.

> [!IMPORTANT]
> Nu stöder vi bara profilering av tjänster som förväntar sig att deras begär ande data ska vara en sträng, till exempel: sträng serialiserad JSON, text, sträng serialiserad bild osv. Innehållet för varje rad i data uppsättningen (sträng) placeras i bröd texten i HTTP-begäran och skickas till tjänsten som kapslar in modellen för poängsättning.

> [!IMPORTANT]
> Vi stöder bara profilering av upp till 2 processorer i ChinaEast2 och USGovArizona region.

Nedan visas ett exempel på hur du kan skapa en data uppsättning för indata för att profilera en tjänst som förväntar sig att inkommande begär ande data ska innehålla serialiserad JSON. I det här fallet skapade vi en data uppsättning baserade 100 instanser av samma data innehåll för begäran. I verkliga scenarier rekommenderar vi att du använder större data uppsättningar som innehåller olika indata, särskilt om din resursanvändning/beteende för modellen är indata beroende.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

När du har data uppsättningen som innehåller exempel på begär ande data, skapar du en konfigurations konfiguration. Konfigurationen av konfigurationen baseras på score.py och miljö definitionen. Följande exempel visar hur du skapar en konfiguration för konfiguration och körning av en konfigurations profil:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


Följande kommando visar hur du profilerar en modell med hjälp av CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Om du vill spara informationen som returneras av profilering använder du taggar eller egenskaper för modellen. Genom att använda taggar eller egenskaper lagras data med modellen i modell registret. Följande exempel visar hur du lägger till en ny tagg som innehåller `requestedCpu` `requestedMemoryInGb` informationen och:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>Nästa steg

* [Felsöka en misslyckad distribution](how-to-troubleshoot-deployment.md)
* [Distribuera till Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Skapa klient program för att använda webb tjänster](how-to-consume-web-service.md)
* [Uppdatera webbtjänst](how-to-deploy-update-web-service.md)
* [Så här distribuerar du en modell med en anpassad Docker-avbildning](how-to-deploy-custom-docker-image.md)
* [Använda TLS för att skydda en webbtjänst via Azure Machine Learning](how-to-secure-web-service.md)
* [Övervaka dina Azure Machine Learning modeller med Application Insights](how-to-enable-app-insights.md)
* [Samla in data för modeller i produktion](how-to-enable-data-collection.md)
* [Skapa händelse aviseringar och utlösare för modell distributioner](how-to-use-event-grid.md)

