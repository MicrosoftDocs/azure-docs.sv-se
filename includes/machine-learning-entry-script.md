---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93325296"
---
Startskriptet tar emot data som skickas till en distribuerad webbtjänst och skickar dem vidare till modellen. Skriptet tar sedan emot svaret som returneras av modellen och returnerar det till klienten. *Skriptet är bara för din modell*. Den måste förstå de data som modellen förväntar sig och returnerar.

De två saker du behöver utföra i ditt Entry-skript är:

1. Läser in din modell (med hjälp av en funktion som kallas `init()` )
1. Köra din modell på indata (med hjälp av en funktion som kallas `run()` )

Vi går igenom de här stegen i detalj.

### <a name="writing-init"></a>Skriver init () 

#### <a name="loading-a-registered-model"></a>Läser in en registrerad modell

Dina registrerade modeller lagras på en sökväg som refereras till av en miljö variabel som kallas `AZUREML_MODEL_DIR` . Mer information om den exakta katalog strukturen finns i [hitta modell filer i ditt inläggs skript](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>Läser in en lokal modell

Om du valt att registrera din modell och godkände din modell som en del av din käll katalog kan du läsa den på samma sätt som du skulle lokalt genom att skicka sökvägen till modellen i förhållande till ditt bedömnings skript. Om du till exempel har en katalog som är strukturerad som:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

Du kan läsa in dina modeller med följande python-kod:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Skriver körning ()

`run()` körs varje gång din modell tar emot en begäran om bedömning och förväntar sig att bröd texten i begäran är ett JSON-dokument med följande struktur:

```json
{
    "data": <model-specific-data-structure>
}

```

Indata till `run()` är en python-sträng som innehåller allt efter nyckeln "data".

I följande exempel visas hur du läser in en registrerad scikit-läre modell och visar den med numpy-data:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Mer avancerade exempel, inklusive automatisk Swagger av schema generering och binär data (d.v.s. avbildning) finns [i artikeln om avancerad registrering av skript](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)