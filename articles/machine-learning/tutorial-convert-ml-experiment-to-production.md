---
title: Konvertera notebook-kod till Python-skript
titleSuffix: Azure Machine Learning
description: Omvandla dina experimentella notebook-datorer för maskininlärning till produktionsklar kod med hjälp av MLOpsPython-kodmallen. Du kan sedan testa, distribuera och automatisera koden.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 37778bc096c9089e3706907fcdd6b9c816cc5fbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817494"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Självstudie: Konvertera ML-experiment till Python-produktionskod

I den här självstudien får du lära dig hur du konverterar Juptyer-anteckningsböcker till Python-skript så att det kan testas och automatiseras med hjälp av MLOpsPython-kodmallen och Azure Machine Learning. Den här processen används vanligtvis för att ta experimenterings-/träningskod från en Juptyer-anteckningsbok och konvertera den till Python-skript. Dessa skript kan sedan användas för testning och CI/CD-automatisering i produktionsmiljön. 

Ett maskininlärningsprojekt kräver experimentering där hypoteser testas med flexibla verktyg som Jupyter Notebook använda verkliga datauppsättningar. När modellen är klar för produktion bör modellkoden placeras i en lagringsplats för produktionskod. I vissa fall måste modellkoden konverteras till Python-skript för att placeras på lagringsplatsen för produktionskoden. Den här självstudien beskriver en rekommenderad metod för hur du exporterar experimenteringskod till Python-skript.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ren nonessential-kod
> * Omstrukturera Jupyter Notebook kod till funktioner
> * Skapa Python-skript för relaterade uppgifter
> * Skapa enhetstester

## <a name="prerequisites"></a>Förutsättningar

- Generera [MLOpsPython-mallen och](https://github.com/microsoft/MLOpsPython/generate) använd `experimentation/Diabetes Ridge Regression Training.ipynb` notebook-apparna `experimentation/Diabetes Ridge Regression Scoring.ipynb` och . Dessa notebook-datorer används som ett exempel på konvertering från experimentering till produktion. Du hittar de här notebook-anteckningsböckerna på [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) .
- Installera `nbconvert`. Följ endast installationsanvisningarna i avsnittet __Installera nbconvert__ på [sidan](https://nbconvert.readthedocs.io/en/latest/install.html) Installation.

## <a name="remove-all-nonessential-code"></a>Ta bort all nonessential-kod

En del kod som skrivs under experimenteringen är endast avsedd för undersökande ändamål. Därför är det första steget att konvertera experimentell kod till produktionskod att ta bort den här nonessential-koden. Om du tar bort den nonessential-koden blir den också mer hanterbar. I det här avsnittet tar du bort kod från `experimentation/Diabetes Ridge Regression Training.ipynb` anteckningsboken. De instruktioner som skriver ut formen `X` på och `y` och cellanropet `features.describe` är bara för datautforskning och kan tas bort. När du har tagit bort den nonessential `experimentation/Diabetes Ridge Regression Training.ipynb` koden bör se ut som följande kod utan markdown:

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

## <a name="refactor-code-into-functions"></a>Omstrukturera kod till funktioner

För det andra måste Jupyter-koden omstruktureras till funktioner. Genom att omstrukturera kod till funktioner blir det enklare att testa enheter och göra koden mer hanterbar. I det här avsnittet ska du omstrukturera:

- Notebook-programmet Diabetes Regression Regression Training( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Notebook-programmet Diabetes Regression Scoring( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Omstrukturera notebook-datorn Diabetes Regression Regression Training till funktioner

I `experimentation/Diabetes Ridge Regression Training.ipynb` utför du följande steg:

1. Skapa en funktion som kallas `split_data` för att dela upp dataramen i test- och träna data. Funktionen ska ta dataframe som `df` en parameter och returnera en ordlista som innehåller nycklarna och `train` `test` .

    Flytta koden under rubriken *Dela upp data i tränings- och valideringsuppsättningar* till funktionen och ändra den så att den `split_data` returnerar `data` objektet.

1. Skapa en funktion med namnet `train_model` , som tar parametrarna och och `data` `args` returnerar en tränad modell.

    Flytta koden under rubriken Training *Model on Training Set (Träningsmodell på* träningsuppsättning) till funktionen och ändra den så att den `train_model` returnerar `reg_model` objektet. Ta `args` bort ordlistan. Värdena kommer från `args` parametern .

1. Skapa en funktion med namnet , som tar parametrar och , och utvärderar modellen och returnerar sedan en `get_model_metrics` ordlista med mått för den `reg_model` `data` tränade modellen.

    Flytta koden under rubriken *Validate Model on Validation Set* (Validera modell vid valideringsuppsättning) till funktionen och ändra den så att den `get_model_metrics` returnerar `metrics` objektet.

De tre funktionerna ska vara följande:

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

Slutför följande `experimentation/Diabetes Ridge Regression Training.ipynb` steg i :

1. Skapa en ny funktion med namnet `main` , som inte tar några parametrar och returnerar ingenting.
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

Funktionen `main` bör se ut som följande kod:

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

I det här skedet bör det inte finnas någon kod kvar i anteckningsboken som inte finns i en funktion, förutom import-instruktioner i den första cellen.

Lägg till en -instruktion som anropar `main` funktionen .

```python
main()
```

Efter omstruktureringen bör `experimentation/Diabetes Ridge Regression Training.ipynb` se ut som följande kod utan markdown:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Omstrukturera notebook-datorn Diabetes Regression Regression Scoring till funktioner

I `experimentation/Diabetes Ridge Regression Scoring.ipynb` utför du följande steg:

1. Skapa en ny funktion med `init` namnet , som inte tar några parametrar och inte returnerar någonting.
1. Kopiera koden under rubriken "Läs in modell" till `init` funktionen .

Funktionen `init` bör se ut som följande kod:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

När funktionen har skapats ersätter du all kod under rubriken "Load Model" (Läs in modell) med `init` ett enda anrop till enligt `init` följande:

```python
init()
```

I `experimentation/Diabetes Ridge Regression Scoring.ipynb` utför du följande steg:

1. Skapa en ny funktion med `run` namnet , som tar och som parametrar och `raw_data` `request_headers` returnerar en ordlista med resultat enligt följande:

    ```python
    {"result": result.tolist()}
    ```

1. Kopiera koden under rubrikerna "Förbered data" och "Poängdata" till `run` funktionen.

    Funktionen bör se ut som följande kod (Kom ihåg att ta bort de instruktioner som anger variablerna och , som kommer att användas senare `run` `raw_data` när funktionen `request_headers` `run` anropas):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

När funktionen `run` har skapats ersätter du all kod under rubrikerna "Förbered data" och "Poängdata" med följande kod:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Föregående kod anger variabler och `raw_data` `request_header` , anropar funktionen med och `run` och skriver ut `raw_data` `request_header` förutsägelserna.

Efter omstruktureringen bör `experimentation/Diabetes Ridge Regression Scoring.ipynb` se ut som följande kod utan markdown:

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

## <a name="combine-related-functions-in-python-files"></a>Kombinera relaterade funktioner i Python-filer

För det tredje måste relaterade funktioner sammanfogas i Python-filer för att bättre hjälpa till att återanvända kod. I det här avsnittet skapar du Python-filer för följande notebook-filer:

- Notebook-programmet Diabetes Regression Regression Training( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Notebook-programmet Diabetes Regression Scoring( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Skapa Python-fil för notebook-filen Diabetes Regression Regression Training

Konvertera notebook-filen till ett körbart skript genom att köra följande instruktion i en kommandotolk, som `nbconvert` använder -paketet och sökvägen för `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

När notebook-filen har konverterats till `train.py` tar du bort eventuella oönskade kommentarer. Ersätt `main()` anropet till i slutet av filen med ett villkorsstyrd anrop som i följande kod:

```python
if __name__ == '__main__':
    main()
```

Filen `train.py` bör se ut som följande kod:

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

`train.py` kan nu anropas från en terminal genom att köra `python train.py` .
Funktionerna från `train.py` kan också anropas från andra filer.

Filen `train_aml.py` som finns i katalogen på MLOpsPython-lagringsplatsen anropar funktionerna som definierats i kontexten för `diabetes_regression/training` en Azure Machine Learning `train.py` experimentkörning. Funktionerna kan också anropas i enhetstester som tas upp senare i den här guiden.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Skapa Python-fil för notebook-filen Diabetes Regression Regression Scoring

Du kan dölja notebook-filen för ett körbart skript genom att köra följande instruktion i en kommandotolk som använder `nbconvert` -paketet och sökvägen för `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

När notebook-filen har konverterats till `score.py` tar du bort eventuella oönskade kommentarer. Filen `score.py` bör se ut som följande kod:

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

Variabeln `model` måste vara global så att den syns i hela skriptet. Lägg till följande -instruktion i början av `init` funktionen:

```python
global model
```

När du har lagt till föregående `init` -instruktion bör funktionen se ut som följande kod:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Skapa enhetstester för varje Python-fil

För det fjärde skapar du enhetstester för dina Python-funktioner. Enhetstester skyddar koden mot funktionella regressioner och gör det enklare att underhålla. I det här avsnittet skapar du enhetstester för funktionerna i `train.py` .

`train.py` innehåller flera funktioner, men vi skapar bara ett enda enhetstest för funktionen med hjälp `train_model` av Pytest-ramverket i den här självstudien. Pytest är inte det enda ramverket för testning av Python-enheter, men det är ett av de vanligaste. Mer information finns i [Pytest](https://pytest.org).

Ett enhetstest innehåller vanligtvis tre huvudåtgärder:

- Ordna objekt – skapa och konfigurera nödvändiga objekt
- Agera på ett objekt
- Bekräfta vad som förväntas

Enhetstestet anropar med vissa hårdkodade data och argument och validerar det som fungerade som förväntat genom att använda den tränade modellen för att göra en förutsägelse och jämföra förutsägelsen med `train_model` `train_model` ett förväntat värde.

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

Nu när du förstår hur du konverterar från ett experiment till produktionskod kan du läsa följande länkar för mer information och nästa steg:

+ [MLOpsPython:](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md)Skapa en CI/CD-pipeline för att träna, utvärdera och distribuera din egen modell med hjälp av Azure Pipelines och Azure Machine Learning
+ [Övervaka körningar och mått för Azure ML-experiment](./how-to-log-view-metrics.md)
+ Se [Övervaka och samla in data från webbtjänstslutpunkter i ML](./how-to-enable-app-insights.md)