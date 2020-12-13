---
title: 'Självstudie: skapa en förutsägelse modell med en bärbar dator (del 1 av 2)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och distribuerar en maskin inlärnings modell med hjälp av kod i en Jupyter Notebook, så att du kan använda den för att förutsäga resultat i Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f8209c0d26cf8c572d10666696231b0468cfcbc6
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370331"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-notebook-part-1-of-2"></a>Självstudie: Power BI integration – skapa en förutsägelse modell med en bärbar dator (del 1 av 2)

I den första delen av den här självstudien tränar du och distribuerar en förutsägelse maskin inlärnings modell med hjälp av kod i en Jupyter Notebook. I del 2 använder du sedan modellen för att förutse resultatet i Microsoft Power BI.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa en Jupyter Notebook
> * Skapa en Azure Machine Learning beräknings instans
> * Träna en Regressions modell med scikit – lära
> * Distribuera modellen till en slut punkt för real tids Poäng

Det finns tre olika sätt att skapa och distribuera den modell du använder i Power BI.  Den här artikeln beskriver alternativ A: träna och distribuera modeller med antecknings böcker.  Det här alternativet visar en kod-första redigerings upplevelse med Jupyter-anteckningsböcker som finns i Azure Machine Learning Studio. 

I stället kan du använda:

* [Alternativ B: träna och distribuera modeller med designern](tutorial-power-bi-designer-model.md)– en låg kod redigerings upplevelse med hjälp av designer (ett användar gränssnitt med dra och släpp).
* [Alternativ C: träna och distribuera modeller med automatiserad ml](tutorial-power-bi-automated-model.md) – en redigering utan kod som fullständigt automatiserar data förberedelser och modell träning.


## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration ([en kostnads fri utvärderings version finns tillgänglig](https://aka.ms/AMLFree)). 
- En Azure Machine Learning-arbetsyta. Om du inte redan har en arbets yta, följer du [hur du skapar en Azure Machine Learning-arbetsyta](./how-to-manage-workspace.md#create-a-workspace).
- Introduktions kunskap om python-språket och Machine Learning-arbetsflöden.

## <a name="create-a-notebook-and-compute"></a>Skapa en bärbar dator och beräkna

På Start sidan för [Azure Machine Learning Studio](https://ml.azure.com) väljer du **Skapa ny** följt av **antecknings bok**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Skärm bild som visar hur du skapar en bärbar dator":::
 
En dialog ruta visas där du kan **skapa en ny fil** :

1. Ett fil namn för din bärbara dator (till exempel `my_model_notebook` )
1. Ändra **fil typen** till **antecknings bok**

Välj **Skapa**. Därefter måste du skapa en beräkning och koppla den till din bärbara dator för att kunna köra kod celler. Det gör du genom att välja plus ikonen överst i antecknings boken:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Skärm bild som visar hur man skapar en beräknings instans":::

Sedan på sidan **skapa beräknings instans** :

1. Välj en storlek för virtuell CPU-dator – i den här självstudien visas en **Standard_D11_v2** (två kärnor, 14 GB RAM).
1. Välj **Nästa**. 
1. På sidan **Konfigurera inställningar** anger du ett giltigt **beräknings namn** (giltiga tecken är versaler och gemener, siffror och tecknet).
1. Välj **Skapa**.

Du kanske märker att den antecknings bok som cirkeln bredvid **Compute** har varit cyan, vilket indikerar att beräknings instansen skapas:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Skärm bild som visar den beräkning som skapas":::

> [!NOTE]
> Det kan ta cirka 2-4 minuter innan beräkningen är etablerad.

När beräkningen är etablerad kan du använda antecknings boken för att köra kod celler. Skriv till exempel i cellen:

```python
import numpy as np

np.sin(3)
```

Följt av **SHIFT-ENTER** (eller **kontroll-ange** eller Välj knappen Spela upp bredvid cellen). Du bör se följande utdata:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Skärm bild som visar cell körning":::

Nu är du redo att bygga en Machine Learning modell!

## <a name="build-a-model-using-scikit-learn"></a>Bygg en modell med scikit – lära

I den här självstudien använder du [diabetes-datauppsättningen](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html), som görs tillgänglig i [Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/). 


### <a name="import-data"></a>Importera data

Importera dina data genom att kopiera och klistra in koden nedan i en ny **kod cell** i din bärbara dator:

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df`Data ramen Pandas innehåller 10 bas linje inmatnings variabler (till exempel ålder, kön, body mass index, Genomsnittligt blod tryck och sex blod Serums mått). `y_df`Pandas data Frame är mål variabeln som innehåller ett kvantitativt mått på sjukdoms förloppet ett år efter bas linjen. Det finns totalt 442 poster.

### <a name="train-model"></a>Träningsmodell

Skapa en ny **kod cell** i din bärbara dator och kopiera och klistra in kodfragmentet nedan, som skapar en Ridge regression-modell och som serialiserar modellen med python: s Pickle-format:

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X,y)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registrera modellen

Förutom innehållet i själva modell filen lagrar din registrerade modell även modell metadata-modell beskrivning, taggar och Ramverks information – som är användbar när du hanterar och distribuerar modeller i din arbets yta. Med taggar kan du till exempel kategorisera dina modeller och tillämpa filter när du anger modeller i din arbets yta. Om du markerar den här modellen med ramverket scikit – lär du dig också att distribuera den som en webb tjänst, eftersom vi kommer att se senare.

Kopiera och klistra in koden nedan i en ny **kod cell** i din bärbara dator:

```python
import sklearn

from azureml.core import Workspace
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

ws = Workspace.from_config()

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version=sklearn.__version__,  # Version of scikit-learn used to create the model.
                       sample_input_dataset=X,
                       sample_output_dataset=y,
                       resource_configuration=ResourceConfiguration(cpu=2, memory_in_gb=4),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})

print('Name:', model.name)
print('Version:', model.version)
```

Du kan också visa modellen i Azure Machine Learning Studio genom att navigera till **slut punkter** i den vänstra menyn:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Skärm bild som visar modell":::

### <a name="define-the-scoring-script"></a>Definiera bedömnings skriptet

När du distribuerar en modell som ska integreras i Microsoft Power BI måste du definiera ett python- *bedömnings skript* och en anpassad miljö. Bedömnings skriptet innehåller två funktioner:

- `init()` – den här funktionen körs när tjänsten startas. Den här funktionen läser in modellen (Observera att modellen hämtas automatiskt från modell registret) och deserialiserar den.
- `run(data)` -funktionen körs när ett anrop görs till tjänsten med indata som kräver poäng. 

>[!NOTE]
> Vi använder python dekoratörer för att definiera schemat för indata-och utdata, vilket är viktigt för att Microsoft Power BI-integrationen ska fungera.

Kopiera och klistra in koden nedan i en ny **kod cell** i din bärbara dator. Kodfragmentet nedan har ett cell Magic som skriver koden till en sparad namngiven score.py.

```python
%%writefile score.py

import json
import pickle
import numpy as np
import pandas as pd
import os
import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    path = os.getenv('AZUREML_MODEL_DIR') 
    model_path = os.path.join(path, 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    "AGE": 5,
    "SEX": 2,
    "BMI": 3.1,
    "BP": 3.1,
    "S1": 3.1,
    "S2": 3.1,
    "S3": 3.1,
    "S4": 3.1,
    "S5": 3.1,
    "S6": 3.1
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])

# To indicate that we support a variable length of data input,
# set enforce_shape=False
@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        print("input_data....")
        print(data.columns)
        print(type(data))
        result = model.predict(data)
        print("result.....")
        print(result)
    # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definiera den anpassade miljön

Härnäst måste vi definiera miljön för att få en modell som vi behöver definiera i den här miljön python-paketen som krävs av bedömnings skriptet (score.py) som definieras ovan, till exempel Pandas, scikit-lära osv.

För att definiera miljön, kopiera och klistra in koden nedan i en ny **kod cell** i din bärbara dator:

```python
from azureml.core.model import InferenceConfig
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies

environment = Environment('my-sklearn-environment')
environment.python.conda_dependencies = CondaDependencies.create(pip_packages=[
    'azureml-defaults',
    'inference-schema[numpy-support]',
    'joblib',
    'numpy',
    'pandas',
    'scikit-learn=={}'.format(sklearn.__version__)
])

inference_config = InferenceConfig(entry_script='./score.py',environment=environment)
```

### <a name="deploy-the-model"></a>Distribuera modellen

Distribuera modellen genom att kopiera och klistra in koden nedan i en ny **kod cell** i din bärbara dator:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Det kan ta 2-4 minuter för tjänsten att distribueras.

Du bör se följande utdata från en lyckad distribuerad tjänst:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Du kan också Visa tjänsten i Azure Machine Learning Studio genom att navigera till **slut punkter** i den vänstra menyn:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Skärm bild som visar slut punkt":::

Vi rekommenderar att du testar webb tjänsten för att säkerställa att den fungerar som förväntat. Gå tillbaka till din bärbara dator genom att välja **antecknings böcker** på den vänstra menyn i Azure Machine Learning Studio. Kopiera och klistra in koden nedan i en ny **kod cell** i din bärbara dator för att testa tjänsten:

```python
import json


input_payload = json.dumps({
    'data': X_df[0:2].values.tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

Utdata bör se ut som följande JSON-struktur: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Nästa steg

I den här självstudien fick du lära dig hur du skapar och distribuerar en modell på ett sådant sätt att de kan användas av Microsoft Power BI. I nästa avsnitt får du lära dig hur du använder den här modellen från en Power BI rapport.

> [!div class="nextstepaction"]
> [Självstudie: använda modell i Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
