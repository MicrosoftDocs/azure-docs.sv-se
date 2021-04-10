---
title: Distribuera och göra förutsägelser med ONNX
titleSuffix: SQL machine learning
description: Lär dig hur du tränar en modell, konverterar den till ONNX, distribuerar den till Azure SQL Edge eller Azure SQL-hanterad instans (för hands version) och kör sedan inbyggt förutsägelse på data med den överförda ONNX-modellen.
keywords: Distribuera SQL Edge
ms.prod: sql
ms.technology: machine-learning
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.date: 10/13/2020
ms.openlocfilehash: 755111b2fc48ec119c30d09f2e51b9db6c333848
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653218"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-and-sql-machine-learning"></a>Distribuera och göra förutsägelser med en ONNX-modell och SQL Machine Learning

I den här snabb starten får du lära dig hur du tränar en modell, konverterar den till ONNX, distribuerar den till [Azure SQL Edge](onnx-overview.md) eller [Azure SQL-hanterad instans (för hands version)](../azure-sql/managed-instance/machine-learning-services-overview.md)och sedan kör inbyggt förutsägelser på data med den överförda ONNX-modellen.

Den här snabb starten baseras på **scikit – lära** och använder [data uppsättningen Boston](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html).

## <a name="before-you-begin"></a>Innan du börjar

* Om du använder Azure SQL Edge och du inte har distribuerat en Azure SQL Edge-modul följer du stegen för att [Distribuera SQL Edge med hjälp av Azure Portal](deploy-portal.md).

* Installera [Azure Data Studio](/sql/azure-data-studio/download).

* Installera python-paket som krävs för den här snabb starten:

  1. Öppna en [ny antecknings bok](/sql/azure-data-studio/sql-notebooks) som är ansluten till python 3-kernel. 
  1. Klicka på **Hantera paket**
  1. På fliken **installerad** letar du efter följande python-paket i listan över installerade paket. Om något av dessa paket inte är installerat väljer du fliken **Lägg till ny** , söker efter paketet och klickar på **Installera**.
     - **scikit-learn**
     - **numpy**
     - **onnxmltools**
     - **onnxruntime**
     - **pyodbc**
     - **installations verktyg**
     - **skl2onnx**
     - **sqlalchemy**

* För varje skript del nedan anger du den i en cell i Azure Data Studio Notebook och kör cellen.

## <a name="train-a-pipeline"></a>Träna en pipeline

Dela data uppsättningen för att använda funktioner för att förutsäga median värdet för ett hus.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Utdata**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Skapa en pipeline för att träna LinearRegression-modellen. Du kan också använda andra Regressions modeller.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Kontrol lera modellens exakthet och beräkna sedan R2-poängen och genomsnitts meddelandet.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Utdata**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Konvertera modellen till ONNX

Konvertera data typerna till de SQL-datatyper som stöds. Den här konverteringen krävs även för andra dataframes.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Använd `skl2onnx` , konvertera LinearRegression-modellen till ONNX-formatet och spara den lokalt.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train), final_types=[('variable1',FloatTensorType([1,1]))])
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>Testa ONNX-modellen

När du har konverterat modellen till ONNX-format kan du göra så att modellen visar liten till ingen försämring i prestandan.

> [!NOTE]
> ONNX runtime använder Floats i stället för dubbla så att små avvikelser är möjliga.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Utdata**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>Infoga ONNX-modellen

Lagra modellen i en Azure SQL Edge-eller Azure SQL-hanterad instans i en `models` tabell i en databas `onnx` . I anslutnings strängen anger du **Server adressen**, **användar namnet** och **lösen ordet**.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Läsa in data

Läs in data i SQL.

Skapa först två tabeller, **funktioner** och **mål** för att lagra del mängder av data uppsättningen Boston.

* **Funktioner** innehåller alla data som används för att förutsäga målet, median värdet. 
* **Målet** innehåller median värdet för varje post i data uppsättningen. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Använd slutligen `sqlalchemy` för att infoga `x_train` Pandas- `y_train` dataframes i tabellerna `features` och `target` . 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

Nu kan du visa data i-databasen.

## <a name="run-predict-using-the-onnx-model"></a>Köra förutsägelse med ONNX-modellen

Med modellen i SQL kör du Native PREDICT på data med den överförda ONNX-modellen.

> [!NOTE]
> Ändra Notebook-kärnan till SQL för att köra den återstående cellen.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>Nästa steg

* [Machine Learning och AI med ONNX i SQL Edge](onnx-overview.md)
* [Machine Learning Services i Azure SQL-hanterad instans (för hands version)](../azure-sql/managed-instance/machine-learning-services-overview.md)
