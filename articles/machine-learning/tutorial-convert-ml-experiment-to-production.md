---
title: Konvertera Notebook-kod till Python-skript
titleSuffix: Azure Machine Learning
description: Sätt igång dina Machine Learning-experiment med en produktions klar kod med hjälp av kod mal len MLOpsPython. Du kan sedan testa, distribuera och automatisera koden.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: cdfeb2fdeefabb0d2d4af2fb63222adda5d023fb
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576032"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Självstudie: konvertera ML experiment till produktion python-kod

I den här självstudien får du lära dig hur du konverterar Juptyer-anteckningsböcker till Python-skript som gör det lättare att testa och automatisera dem med hjälp av MLOpsPython-kod mal len och Azure Machine Learning. Normalt används den här processen för att ta experiment/tränings kod från en Juptyer Notebook och konvertera den till Python-skript. Dessa skript kan sedan användas för att testa och CI/CD-automatisering i produktions miljön. 

Ett Machine Learning-projekt kräver experimentering där Hypotheses testas med flexibla verktyg som Jupyter Notebook med hjälp av verkliga data uppsättningar. När modellen är redo för produktion ska modell koden placeras i en produktions kod lagrings plats. I vissa fall måste modell koden konverteras till Python-skript som ska placeras i produktions kod lagrings platsen. I den här självstudien beskrivs en rekommenderad metod för att exportera experiment kod till Python-skript.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Rensa icke-väsentlig kod
> * ÅterJupyter Notebooks kod i functions
> * Skapa Python-skript för relaterade aktiviteter
> * Skapa enhetstester

## <a name="prerequisites"></a>Förutsättningar

- Generera [MLOpsPython-mallen](https://github.com/microsoft/MLOpsPython/generate) och Använd `experimentation/Diabetes Ridge Regression Training.ipynb` `experimentation/Diabetes Ridge Regression Scoring.ipynb` antecknings böckerna och. Dessa antecknings böcker används som exempel på konvertering från experiment till produktion. Du kan hitta de här antecknings böckerna på [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) .
- Installera `nbconvert`. Följ bara installations anvisningarna under avsnittet __Installera nbconvert__ på [installations](https://nbconvert.readthedocs.io/en/latest/install.html) sidan.

## <a name="remove-all-nonessential-code"></a>Ta bort all kod som inte behövs

Viss kod som skrivs under experimentet är endast avsedd för test ändamål. Det första steget för att konvertera experimentell kod till produktions koden är därför att ta bort den här icke-nödvändiga koden. Att ta bort icke-väsentlig kod gör också koden mer hanterbar. I det här avsnittet ska du ta bort kod från `experimentation/Diabetes Ridge Regression Training.ipynb` antecknings boken. Uttrycken som skriver ut formen på `X` och `y` och cell anropet `features.describe` är bara för data utforskning och kan tas bort. När du har tagit bort en kod `experimentation/Diabetes Ridge Regression Training.ipynb` som inte är viktig bör se ut som följande kod utan markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Omträds kod i functions

För det andra måste Jupyter-koden omstruktureras till functions. Omstrukturering av kod i funktioner gör enhets testningen enklare och gör koden mer hanterbar. I det här avsnittet kommer du att göra följande:

- Diabetes Ridge regression Training Notebook ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Antecknings boken diabetes Ridge regression poängsättning ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Diabetes Ridge regression Training Notebook in Functions

I `experimentation/Diabetes Ridge Regression Training.ipynb` utför du följande steg:

1. Skapa en funktion `split_data` som kallas för att dela data ramen i test-och träna data. Funktionen ska ta dataframe `df` som en parameter och returnera en ord lista som innehåller nycklarna `train` och `test` .

    Flytta koden i rubriken *dela data till inlärnings-och validerings uppsättningar* till `split_data` funktionen och ändra den för att returnera `data` objektet.

1. Skapa en funktion `train_model` som kallas, som tar parametrarna `data` och `args` returnerar en utbildad modell.

    Flytta koden under rubriken rubrik *inlärnings modell för träning* till `train_model` funktionen och ändra den för att returnera `reg_model` objektet. Ta bort `args` ord listan, värdena kommer från `args` parametern.

1. Skapa en funktion `get_model_metrics` som kallas, som tar parametrar `reg_model` och `data` och utvärderar modellen, returnerar sedan en ord lista med mått för den tränade modellen.

    Flytta koden under rubriken *validera modell på validerings uppsättning* till `get_model_metrics` funktionen och ändra den för att returnera `metrics` objektet.

De tre funktionerna bör vara följande:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

`experimentation/Diabetes Ridge Regression Training.ipynb`Utför följande steg fortfarande i:

1. Skapa en ny funktion `main` som kallas, som inte tar några parametrar och som inte returnerar något.
1. Flytta koden under rubriken "Läs in data" till `main` funktionen.
1. Lägg till anrop för de nyligen skrivna funktionerna i `main` funktionen:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Flytta koden under rubriken "Spara modell" till `main` funktionen.

`main`Funktionen bör se ut som följande kod:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

I det här skedet bör det inte finnas någon kod kvar i antecknings boken som inte är i en funktion, förutom import-instruktioner i den första cellen.

Lägg till en instruktion som anropar `main` funktionen.

```python
main()
```

Efter omstrukturering `experimentation/Diabetes Ridge Regression Training.ipynb` bör se ut som följande kod utan markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Diabetes Ridge Regressions-antecknings bok till Functions

I `experimentation/Diabetes Ridge Regression Scoring.ipynb` utför du följande steg:

1. Skapa en ny funktion `init` som kallas, som inte tar några parametrar och returnerar ingenting.
1. Kopiera koden under rubriken "läsa modell" till `init` funktionen.

`init`Funktionen bör se ut som följande kod:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

När `init` funktionen har skapats ersätter du all kod under rubriken "belastnings modell" med ett enda anrop till `init` enligt följande:

```python
init()
```

I `experimentation/Diabetes Ridge Regression Scoring.ipynb` utför du följande steg:

1. Skapa en ny funktion `run` som kallas, som tar `raw_data` och `request_headers` som parametrar och returnerar en ord lista med resultat enligt följande:

    ```python
    {"result": result.tolist()}
    ```

1. Kopiera koden under rubrikerna "Förbered data" och "Poäng data" i `run` funktionen.

    `run`Funktionen bör se ut som följande kod (kom ihåg att ta bort de instruktioner som anger variablerna `raw_data` och `request_headers` som kommer att användas senare när `run` funktionen anropas):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

När `run` funktionen har skapats ersätter du all kod under rubrikerna "Förbered data" och "Poäng data" med följande kod:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Föregående kod anger variabler `raw_data` och `request_header` anropar `run` funktionen med och och `raw_data` `request_header` skriver ut förutsägelserna.

Efter omstrukturering `experimentation/Diabetes Ridge Regression Scoring.ipynb` bör se ut som följande kod utan markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Kombinera relaterade funktioner i python-filer

Tredje, relaterade funktioner måste slås samman till python-filer för att bättre kunna använda kod åter användning. I det här avsnittet skapar du python-filer för följande antecknings böcker:

- Diabetes Ridge regression Training Notebook ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Antecknings boken diabetes Ridge regression poängsättning ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Skapa python-fil för notebook diabetes Ridge regression Training

Konvertera antecknings boken till ett körbart skript genom att köra följande instruktion i en kommando tolk som använder `nbconvert` paketet och sökvägen till `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Ta bort eventuella oönskade kommentarer när antecknings boken har konverterats till `train.py` . Ersätt anropet till `main()` i slutet av filen med ett villkorsstyrt anrop som följande kod:

```python
if __name__ == '__main__':
    main()
```

`train.py`Filen bör se ut som följande kod:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` kan nu anropas från en Terminal genom att köra `python train.py` .
Funktionerna från `train.py` kan också anropas från andra filer.

`train_aml.py`Filen som hittades i `diabetes_regression/training` katalogen i MLOpsPython-lagringsplatsen anropar de funktioner som definierats i `train.py` i kontexten för en Azure Machine Learning experiment körning. Funktionerna kan också anropas i enhets test, som beskrivs senare i den här hand boken.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Skapa python-fil för notebook diabetes Ridge regression Poängsättning

Konvertera din bärbara dator till ett körbart skript genom att köra följande instruktion i en kommando tolk som använder `nbconvert` paketet och sökvägen till `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Ta bort eventuella oönskade kommentarer när antecknings boken har konverterats till `score.py` . `score.py`Filen bör se ut som följande kod:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

`model`Variabeln måste vara global så att den visas i hela skriptet. Lägg till följande-instruktion i början av `init` funktionen:

```python
global model
```

När du har lagt till föregående instruktion `init` ska funktionen se ut som följande kod:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Skapa enhets test för varje python-fil

Fjärde, skapa enhets test för dina python-funktioner. Enhets test skyddar kod mot funktionella regressioner och gör det lättare att underhålla. I det här avsnittet ska du skapa enhets test för funktionerna i `train.py` .

`train.py` innehåller flera funktioner, men vi skapar bara ett enda enhets test för `train_model` funktionen med Pytest-ramverket i den här självstudien. Pytest är inte det enda testnings ramverket python unit, men det är en av de som används oftast. Mer information finns på [Pytest](https://pytest.org).

Ett enhets test innehåller vanligt vis tre huvudsakliga åtgärder:

- Ordna objekt – skapa och konfigurera nödvändiga objekt
- Agera på ett objekt
- Assert förväntat

Enhets testet anropar `train_model` med vissa hårdkodade data och argument och validerar att de fungerar `train_model` som förväntat genom att använda den resulterande tränade modellen för att göra en förutsägelse och jämföra denna förutsägelse till ett förväntat värde.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur du konverterar från ett experiment till produktions koden kan du läsa följande länkar för mer information och nästa steg:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): skapa en CI/CD-pipeline för att träna, utvärdera och distribuera din egen modell med hjälp av Azure pipelines och Azure Machine Learning
+ [Övervaka körningar och mått för Azure ML-experiment](./how-to-track-experiments.md)
+ Se [Övervaka och samla in data från webbtjänstslutpunkter i ML](./how-to-enable-app-insights.md)