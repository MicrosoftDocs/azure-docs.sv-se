---
title: 'Skapa python-modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen skapa python-modell i Azure Machine Learning för att skapa en anpassad modell eller data bearbetnings modul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/18/2020
ms.openlocfilehash: 03f80ebeb7b30d8c0fe14060335541d77b7523b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898622"
---
# <a name="create-python-model-module"></a>Skapa python modell-modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Lär dig hur du använder modulen skapa python-modell för att skapa en modell som inte är tränad från ett Python-skript. Du kan basera modellen på valfri elev som ingår i ett python-paket i Azure Machine Learning designer-miljön. 

När du har skapat modellen kan du använda [träna modell](train-model.md) för att träna modellen på en data uppsättning, t. ex. en annan elev i Azure Machine Learning. Den tränade modellen kan skickas till [Poäng modell](score-model.md) för att göra förutsägelser. Sedan kan du spara den tränade modellen och publicera bedömnings arbets flödet som en webb tjänst.

> [!WARNING]
> För närvarande går det inte att ansluta den här modulen för att **finjustera modulen modellens egenskaper** eller skicka resultatet av en python-modell för att [utvärdera modellen](evaluate-model.md). Om du behöver justera de båda parametrarna eller utvärdera en modell kan du skriva ett anpassat Python-skript med hjälp av [köra Python-skript](execute-python-script.md) -modulen.


## <a name="configure-the-module"></a>Konfigurera modulen

Användningen av den här modulen kräver en mellanliggande eller expert kunskap om python. Modulen stöder användning av valfri elev som ingår i python-paketen som redan har installerats i Azure Machine Learning. Se listan med förinstallerade python-paket i [köra Python-skript](execute-python-script.md).

> [!NOTE]
> Var noga med att skriva skriptet och se till att det inte finns något syntaxfel, till exempel att använda ett icke-deklarerat objekt eller en icke-importerad modul.

> [!NOTE]
> Betala också extra uppmärksamhet till listan förinstallerade moduler i [köra Python-skript](execute-python-script.md). Importera endast förinstallerade moduler. Installera inte extra paket, till exempel "pip install xgboost" i det här skriptet, annars uppstår fel vid läsning av modeller i moduler som är i inström.
  
Den här artikeln visar hur du använder **create python-modellen** med en enkel pipeline. Här är ett diagram över pipelinen:

![Diagram över skapa python-modell](./media/module/create-python-model.png)

1. Välj **skapa python-modell** och redigera skriptet för att implementera modellerings-eller data hanterings processen. Du kan basera modellen på valfri elev som ingår i ett python-paket i Azure Machine Learnings miljön.

> [!NOTE]
> Betala extra uppmärksamhet på kommentarerna i exempel koden för skriptet och kontrol lera att skriptet följer kraven, inklusive klass namnet, metoderna och Metodsignaturen. Överträdelsen leder till undantag. 

   Följande exempel kod för Naive Bayes-klassificeraren i två klasser använder det populära *sklearn* -paketet:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

2. Anslut den **skapa python modell** -modul som du nyss skapade för att **träna modell** och **Poäng modell**.

3. Om du behöver utvärdera modellen lägger du till en [köra python-skriptfil](execute-python-script.md) och redigerar python-skriptet.

   Följande skript är exempel på utvärderings kod:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function MUST have two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 