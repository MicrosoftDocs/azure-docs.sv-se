---
title: 'Självstudie: skapa en förutsägelse modell med en bärbar dator (del 1 av 2)'
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och distribuerar en maskin inlärnings modell genom att använda kod i en Jupyter Notebook. Skapa också ett bedömnings skript som definierar indata och utdata för enkel integrering i Microsoft Power BI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 00d5fa43245fb25b8ee99a0523d680bef891b71e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387010"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-with-a-jupyter-notebook-part-1-of-2"></a>Självstudie: Power BI integration – skapa en förutsägelse modell med en Jupyter Notebook (del 1 av 2)

I del 1 av den här självstudien tränar du och distribuerar en förutsägelse maskin inlärnings modell genom att använda kod i en Jupyter Notebook. Du skapar också ett bedömnings skript för att definiera in-och utdata-schemat för modellen för integrering i Power BI.  I del 2 använder du modellen för att förutsäga resultat i Microsoft Power BI.

I den här kursen får du:

> [!div class="checklist"]
> * Skapa en Jupyter Notebook.
> * Skapa en Azure Machine Learning beräknings instans.
> * Träna en Regressions modell med hjälp av scikit-lära.
> * Skriv ett bedömnings skript som definierar indata och utdata för enkel integrering i Microsoft Power BI.
> * Distribuera modellen till en slut punkt för real tids resultat.

Det finns tre sätt att skapa och distribuera den modell som du kommer att använda i Power BI.  Den här artikeln beskriver "alternativ A: träna och distribuera modeller med hjälp av antecknings böcker".  Det här alternativet är en kod-första redigerings upplevelse. Den använder Jupyter-anteckningsböcker som finns i Azure Machine Learning Studio. 

Men du kan i stället använda något av de andra alternativen:

* [Alternativ B: träna och distribuera modeller med hjälp av Azure Machine Learning designer](tutorial-power-bi-designer-model.md). Den här redigerings upplevelsen med låg kod använder ett användar gränssnitt med dra och släpp-funktionen.
* [Alternativ C: träna och distribuera modeller med hjälp av automatisk maskin inlärning](tutorial-power-bi-automated-model.md). Den här redigeringen av den här processen ger helt automatiserad data förberedelse och modell träning.


## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte redan har en prenumeration kan du använda en [kostnads fri utvärderings version](https://aka.ms/AMLFree). 
- En Azure Machine Learning-arbetsyta. Om du inte redan har en arbets yta, se [skapa och hantera Azure Machine Learning arbets ytor](./how-to-manage-workspace.md#create-a-workspace).
- Introduktions kunskap om python-språket och Machine Learning-arbetsflöden.

## <a name="create-a-notebook-and-compute"></a>Skapa en bärbar dator och beräkna

På [**Azure Machine Learning Studio**](https://ml.azure.com) start sida väljer du **Skapa ny**  >  **antecknings bok**:

:::image type="content" source="media/tutorial-power-bi/create-new-notebook.png" alt-text="Skärm bild som visar hur du skapar en antecknings bok.":::
 
På sidan **skapa en ny fil** :

1. Namnge din bärbara dator (till exempel *my_model_notebook*).
1. Ändra **fil typen** till **antecknings bok**.
1. Välj **Skapa**. 
 
Om du vill köra kod celler skapar du en beräknings instans och kopplar den till din bärbara dator. Börja med att välja plus ikonen överst i antecknings boken:

:::image type="content" source="media/tutorial-power-bi/create-compute.png" alt-text="Skärm bild som visar hur du skapar en beräknings instans.":::

På sidan **skapa beräknings instans** :

1. Välj en storlek på virtuell CPU-dator. I den här självstudien kan du välja en **Standard_D11_v2**, med 2 kärnor och 14 GB RAM.
1. Välj **Nästa**. 
1. Ange ett giltigt **beräknings namn** på sidan **Konfigurera inställningar** . Giltiga tecken är stora och små bokstäver, siffror och bindestreck (-).
1. Välj **Skapa**.

I antecknings boken kan du märka cirkeln bredvid **Compute** -inaktive rad Cyan. Den här färg ändringen visar att beräknings instansen skapas:

:::image type="content" source="media/tutorial-power-bi/creating.png" alt-text="Skärm bild som visar en beräkning som skapas.":::

> [!NOTE]
> Compute-instansen kan ta 2 till 4 minuter att tillhandahållas.

När beräkningen har allokerats kan du använda antecknings boken för att köra kod celler. I cellen kan du till exempel skriva följande kod:

```python
import numpy as np

np.sin(3)
```

Välj sedan Skift + Retur (eller Välj CTRL + RETUR eller Välj knappen **spela upp** bredvid cellen). Du bör se följande utdata:

:::image type="content" source="media/tutorial-power-bi/simple-sin.png" alt-text="Skärm bild som visar resultatet av en cell.":::

Nu är du redo att bygga en maskin inlärnings modell.

## <a name="build-a-model-by-using-scikit-learn"></a>Bygg en modell med hjälp av scikit – lära

I den här självstudien använder du [diabetes-datauppsättningen](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html). Den här data uppsättningen är tillgänglig i [Azure Open-datauppsättningar](https://azure.microsoft.com/services/open-datasets/).


### <a name="import-data"></a>Importera data

Om du vill importera dina data kopierar du följande kod och klistrar in den i en ny *kod cell* i din bärbara dator.

```python
from azureml.opendatasets import Diabetes

diabetes = Diabetes.get_tabular_dataset()
X = diabetes.drop_columns("Y")
y = diabetes.keep_columns("Y")
X_df = X.to_pandas_dataframe()
y_df = y.to_pandas_dataframe()
X_df.info()
```

`X_df`Data ramen Pandas innehåller 10 bas linje inmatnings variabler. Dessa variabler omfattar ålder, kön, body mass index, Genomsnittligt blod tryck och sex mätningar av blod serum. `y_df`Pandas data ram är mål variabeln. Det innehåller ett kvantitativt mått på sjukdoms förloppet ett år efter bas linjen. Data ramen innehåller 442 poster.

### <a name="train-the-model"></a>Träna modellen

Skapa en ny *kod cell* i din bärbara dator. Kopiera sedan följande kod och klistra in den i cellen. Det här kodfragmentet skapar en Ridge regression-modell och serialiserar modellen med hjälp av python Pickle-formatet.

```python
import joblib
from sklearn.linear_model import Ridge

model = Ridge().fit(X_df,y_df)
joblib.dump(model, 'sklearn_regression_model.pkl')
```

### <a name="register-the-model"></a>Registrera modellen

Förutom innehållet i själva modell filen kommer din registrerade modell att lagra metadata. Metadata innehåller modell beskrivning, taggar och Ramverks information. 

Metadata är användbara när du hanterar och distribuerar modeller i din arbets yta. Genom att använda taggar kan du till exempel kategorisera dina modeller och tillämpa filter när du listar modeller i din arbets yta. Om du markerar den här modellen med ramverket scikit – lär du dig också att distribuera den som en webb tjänst.

Kopiera följande kod och klistra in den i en ny *kod cell* i din bärbara dator.

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

Du kan också visa modellen i Azure Machine Learning Studio. I menyn till vänster väljer du **modeller**:

:::image type="content" source="media/tutorial-power-bi/model.png" alt-text="Skärm bild som visar hur du visar en modell.":::

## <a name="define-the-scoring-script"></a>Definiera bedömnings skriptet

När du distribuerar en modell som ska integreras i Power BI måste du definiera ett python- *bedömnings skript* och en anpassad miljö. Bedömnings skriptet innehåller två funktioner:

- `init()`Funktionen körs när tjänsten startas. Den läser in modellen (som hämtas automatiskt från modell registret) och deserialiserar den.
- `run(data)`Funktionen körs när ett anrop till tjänsten innehåller indata som måste poängas. 

>[!NOTE]
> Python-dekoratörer i koden nedan definierar schemat för in-och utdata, vilket är viktigt för att integrera med Power BI.

Kopiera följande kod och klistra in den i en ny *kod cell* i din bärbara dator. Följande kodfragment innehåller cell Magic som skriver koden till en fil med namnet *score.py*.

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
    # You can return any data type, as long as it can be serialized by JSON.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

### <a name="define-the-custom-environment"></a>Definiera den anpassade miljön

Definiera sedan miljön för att Poäng modellen. I miljön definierar du python-paketen, till exempel Pandas och scikit-lär, som bedömnings skriptet (*score.py*) kräver.

Om du vill definiera miljön kopierar du följande kod och klistrar in den i en ny *kod cell* i din bärbara dator.

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

Om du vill distribuera modellen kopierar du följande kod och klistrar in den i en ny *kod cell* i din bärbara dator:

```python
service_name = 'my-diabetes-model'

service = Model.deploy(ws, service_name, [model], inference_config, overwrite=True)
service.wait_for_deployment(show_output=True)
```

>[!NOTE]
> Det kan ta 2 till 4 minuter att distribuera tjänsten.

Om tjänsten distribueras korrekt bör du se följande utdata:

```txt
Tips: You can try get_logs(): https://aka.ms/debugimage#dockerlog or local deployment: https://aka.ms/debugimage#debug-locally to debug if deployment takes longer than 10 minutes.
Running......................................................................................
Succeeded
ACI service creation operation finished, operation "Succeeded"
```

Du kan också Visa tjänsten i Azure Machine Learning Studio. I menyn till vänster väljer du **slut punkter**:

:::image type="content" source="media/tutorial-power-bi/endpoint.png" alt-text="Skärm bild som visar hur du visar tjänsten.":::

Vi rekommenderar att du testar webb tjänsten för att säkerställa att den fungerar som förväntat. Om du vill returnera din bärbara dator går du till Azure Machine Learning Studio och väljer **antecknings böcker** i menyn till vänster. Kopiera sedan följande kod och klistra in den i en ny *kod cell* i din bärbara dator för att testa tjänsten.

```python
import json

input_payload = json.dumps({
    'data': X_df[0:2].values.tolist()
})

output = service.run(input_payload)

print(output)
```

Utdata bör se ut som den här JSON-strukturen: `{'predict': [[205.59], [68.84]]}` .

## <a name="next-steps"></a>Nästa steg

I den här självstudien fick du lära dig hur du skapar och distribuerar en modell så att den kan användas av Power BI. I nästa avsnitt får du lära dig hur du använder den här modellen i en Power BI rapport.

> [!div class="nextstepaction"]
> [Självstudie: använda en modell i Power BI](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
