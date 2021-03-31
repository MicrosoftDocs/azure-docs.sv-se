---
title: Experiment spårning och distribution av modeller
titleSuffix: Azure Data Science Virtual Machine
description: Lär dig hur du spårar och loggar experiment från Data Science Virtual Machine med Azure Machine Learning och/eller MLFlow.
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 3795d531c5c4c543587ab817c05cd1cfeea6be06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518546"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Spåra experiment och distribuera modeller i Azure Machine Learning

Förbättra skapande processen för modeller genom att spåra experiment och övervaka körnings mått. I den här artikeln lär du dig att lägga till loggnings kod i ditt utbildnings skript med [MLflow](https://mlflow.org/) -API: et och spåra experimentet i Azure Machine Learning.

Följande diagram illustrerar att med MLflow spårning spårar du ett Experiments körnings mått och lagrings modell artefakter i din Azure Machine Learning-arbetsyta.

![spåra experiment](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Förutsättningar

* Du måste [etablera en Azure Machine Learning-arbetsyta](../how-to-manage-workspace.md#create-a-workspace)

## <a name="create-a-new-notebook"></a>Skapa en ny notebook

Azure Machine Learning-och MLFlow SDK är förinstallerade på Data Science VM och kan nås i **azureml_py36_ \*** Conda-miljön. I JupyterLab klickar du på starta och väljer följande kernel:

![Val av kernel](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>Konfigurera arbets ytan

Gå till [Azure Portal](https://portal.azure.com) och välj den arbets yta som du etablerade som en del av förutsättningarna. Du ser __nedladdnings config.jspå__ (se nedan) – Ladda ned config och se till att den lagras i din arbets katalog på DSVM.

![Hämta konfigurations fil](./media/how-to-track-experiments/experiment-tracking-2.png)

Konfigurationen innehåller information som arbets ytans namn, prenumeration osv. det innebär att du inte behöver hårdkoda dessa parametrar.

## <a name="track-dsvm-runs"></a>Spåra DSVM-körningar

Lägg till följande kod i antecknings boken (eller skriptet) för att ange AzureML-arbetsytans objekt.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
Spårnings-URI: n är giltig till en timme eller mindre. Om du startar om skriptet efter inaktivitet använder du get_mlflow_tracking_uri API för att hämta en ny URI.

### <a name="load-the-data"></a>Läsa in data

I det här exemplet används diabetes-datauppsättningen, en välkänd liten data uppsättning som medföljer scikit-information. Den här cellen läser in data uppsättningen och delar den i slumpmässiga inlärnings-och test uppsättningar.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Lägg till spårning

Lägg till experiment uppföljning med Azure Machine Learning SDK och ladda upp en beständiga modell i experiment körnings posten. Följande kod lägger till loggar och laddar upp en modell fil till experimentet. Modellen registreras också i Azure Machine Learning Model-registret.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Visa körningar i Azure Machine Learning

Du kan visa experimentet som körs i [Azure Machine Learning Studio](https://ml.azure.com). Klicka på __experiment__ på menyn till vänster och välj "experiment_with_mlflow" (eller om du valde att namnge ditt experiment på ett annat sätt i ovanstående kodfragment, klickar du på det namn som används):

![Välj experiment](./media/how-to-track-experiments/mlflow-experiments.png)

Du bör se det loggade medeltalet i kvadratvärdet (MSE):

![GÅNG](./media/how-to-track-experiments/mlflow-experiments-2.png)

Om du klickar på Kör visas annan information och även Pickle-modellen i __utmatningar + loggar__

## <a name="deploy-model-in-azure-machine-learning"></a>Distribuera modell i Azure Machine Learning

I det här avsnittet beskriver vi hur du distribuerar modeller som har tränats på en DSVM till Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>Steg 1: skapa en Härlednings beräkning

På den vänstra menyn i [azureml Studio](https://ml.azure.com) klickar du på __Compute__ och sedan på fliken __härlednings kluster__ . Klicka sedan på __+ ny__ enligt beskrivningen nedan:

![Skapa en Härlednings beräkning](./media/how-to-track-experiments/mlflow-experiments-6.png)

I fönstret __nytt__ fyllnings kluster fyllnings information för:

* Compute-namn
* Kubernetes-tjänst – Välj Skapa ny
* Välj region
* Välj den virtuella datorns storlek (i den här självstudien är standardvärdet för Standard_D3_v2 tillräckligt)
* Kluster syfte – Välj __dev-test__
* Antalet noder måste vara lika med __1__
* Nätverks konfiguration – grundläggande

Klicka sedan på __skapa__.

![beräknings information](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Steg 2: Distribuera ingen kod härlednings tjänst

När vi registrerade modellen i vår kod med `register_model` , angav vi ramverket som sklearn. Azure Machine Learning stöder inga kod distributioner för följande ramverk:

* scikit-learn
* Tensorflow SaveModel-format
* ONNX modell format

Ingen kod distribution innebär att du kan distribuera direkt från modell artefakten utan att behöva ange något speciellt bedömnings skript.

Om du vill distribuera diabetes-modellen går du till den vänstra menyn i [Azure Machine Learning Studio](https://ml.azure.com) och väljer __modeller__. Klicka sedan på den registrerade diabetes_model:

![Välj modell](./media/how-to-track-experiments/mlflow-experiments-3.png)

Klicka sedan på knappen __distribuera__ i fönstret modell information:

![Distribuera](./media/how-to-track-experiments/mlflow-experiments-4.png)

Vi kommer att distribuera modellen till det utgångs bara kluster (Azure Kubernetes-tjänsten) som vi skapade i steg 1. Fyll i informationen nedan genom att ange ett namn för tjänsten och namnet på AKS beräknings kluster (skapas i steg 1). Vi rekommenderar också att du ökar __kapaciteten för processor reserven__ till 1 (från 0,1) och __kapaciteten för minnes reserven__ till 1 (från 0,5) – du kan göra den här ökningen genom att klicka på __Avancerat__ och fylla i informationen. Klicka sedan på __distribuera__.

![distribuera information](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Steg 3: förbruka

När modellen har distribuerats, bör du se följande (för att komma till den här sidan klickar du på slut punkter i den vänstra menyn > klickar sedan på namnet på den distribuerade tjänsten):

![Använda modell](./media/how-to-track-experiments/mlflow-experiments-8.png)

Du bör se att distributions tillståndet går från __över gången__ till __felfri__. Dessutom tillhandahåller det här informations avsnittet REST-slutpunkter och Swagger-URL: er som en programutvecklare kan använda för att integrera din ML-modell i sina appar.

Du kan testa slut punkten med [Postman](https://www.postman.com/)eller så kan du använda azureml SDK:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Steg 4: Rensa

Ta bort den Härlednings beräkning som du skapade i steg 1 så att du inte debiteras pågående beräknings kostnader. På den vänstra menyn i Azure Machine Learning Studio klickar du på beräknings > utgångs kluster > väljer Compute > ta bort.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [Distribuera modeller i azureml](../how-to-deploy-and-where.md)
