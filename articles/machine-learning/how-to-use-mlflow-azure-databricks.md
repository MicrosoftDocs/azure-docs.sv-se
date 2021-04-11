---
title: MLflow-spårning för Azure Databricks ML experiment
titleSuffix: Azure Machine Learning
description: Konfigurera MLflow med Azure Machine Learning för att logga mått och artefakter från Azure Databricks ml-experiment.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: a894a46ce7c78b65dde80c52f9516fcbe4d27bcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521007"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Spåra Azure Databricks ML experiment med MLflow och Azure Machine Learning (förhands granskning)

I den här artikeln lär du dig hur du aktiverar MLflow för spårnings-och loggnings-API: t, gemensamt känt som [MLflow spårning](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), för att ansluta dina Azure DATABRICKS (ADB) experiment, MLflow och Azure Machine Learning.

[MLflow](https://www.mlflow.org) är ett bibliotek med öppen källkod för hantering av livs cykeln för maskin inlärnings experiment. MLFlow-spårning är en komponent i MLflow som loggar och spårar utbildningens körnings mått och modell artefakter. Läs mer om [Azure Databricks och MLflow](/azure/databricks/applications/mlflow/). 

Se [spåra experiment körningar med MLflow och Azure Machine Learning](how-to-use-mlflow.md) för ytterligare MLflow och integrering av Azure Machine learnings funktioner.

>[!NOTE]
> Som ett bibliotek med öppen källkod ändras MLflow ofta. De funktioner som gjorts tillgängliga via Azure Machine Learning-och MLflow-integreringen bör därför betraktas som en för hands version och stöds inte fullt ut av Microsoft.

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill övervaka processen för modell inlärning. Om du är en administratör som vill övervaka resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda utbildningar eller slutförda modell distributioner, se [övervakning Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar

* Installera paketet `azureml-mlflow`. 
    * Det här paketet kommer automatiskt in i `azureml-core` [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install), som ger anslutningen för MLflow åtkomst till din arbets yta.
* En [Azure Databricks arbets yta och ett kluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal).
* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
    * Se vilka [åtkomst behörigheter du behöver för att utföra dina MLflow-åtgärder med din arbets yta](how-to-assign-roles.md#mlflow-operations).

## <a name="track-azure-databricks-runs"></a>Spåra Azure Databricks körningar

Med MLflow spårning med Azure Machine Learning kan du lagra de inloggade måtten och artefakterna från dina Azure Databricks körs i både din: 

* Azure Databricks arbets yta.
* Azure Machine Learning-arbetsyta

När du har skapat din Azure Databricks arbets yta och ett kluster 

1. Installera *azureml-mlflow-* biblioteket från PyPi för att säkerställa att klustret har åtkomst till de nödvändiga funktionerna och klasserna.

1. Konfigurera din experiment antecknings bok.

1. Anslut Azure Databricks arbets ytan och Azure Machine Learning arbets ytan.

Ytterligare information för de här stegen finns i följande avsnitt så att du kan köra dina MLflow-experiment med Azure Databricks. 

## <a name="install-libraries"></a>Installera bibliotek

Om du vill installera bibliotek i klustret går du till fliken **bibliotek** och väljer **installera ny**

 ![mlflow med Azure databricks](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

I fältet **paket** skriver du azureml-mlflow och väljer sedan installera. Upprepa det här steget vid behov för att installera andra ytterligare paket till klustret för experimentet.

 ![Mlflow-bibliotek för Azure DB](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Konfigurera din bärbara dator 

När ditt ADB-kluster har kon figurer ATS, 
1. Välj **arbets ytor** i det vänstra navigerings fönstret. 
1. Expandera List menyn för arbets ytor och välj **Importera**
1. Dra och släpp, eller bläddra för att hitta, din experiment-anteckningsbok för att importera din ADB-arbetsyta.
1. Välj **Importera**. Din experiment antecknings bok öppnas automatiskt.
1. Under anteckningsbok-titeln längst upp till vänster väljer du det kluster som du vill koppla till din experiment antecknings bok. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Anslut dina Azure Databricks och Azure Machine Learning arbets ytor

Genom att länka din ADB-arbetsyta till din Azure Machine Learning-arbetsyta kan du spåra dina experiment data på arbets ytan Azure Machine Learning.

För att länka din ADB-arbetsyta till en ny eller befintlig Azure Machine Learning arbets yta, 
1. Logga in på [Azure Portal](https://ms.portal.azure.com).
1. Gå till **översikts** sidan för din ADB-arbetsyta.
1. Välj knappen **länk Azure Machine Learning arbets yta** längst ned till höger. 

 ![Länka Azure DB och Azure Machine Learning arbets ytor](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>MLflow spårning i dina arbets ytor

När du har instansierat din arbets yta anges MLflow-spårning automatiskt som spåras på följande platser:

* Arbets ytan länkad Azure Machine Learning.
* Din ursprungliga ADB-arbetsyta. 

Alla dina experiment hamnar i den hanterade Azure Machine Learning spårnings tjänsten.

Följande kod ska finnas i din experiment antecknings bok för att hämta din länkade Azure Machine Learning-arbetsyta. 

Den här koden, 

*  Hämtar information om din Azure-prenumeration för att skapa en instans av din Azure Machine Learning-arbetsyta. 

* Förutsätter att du har en befintlig resurs grupp och Azure Machine Learning arbets ytan, annars kan du [skapa dem](how-to-manage-workspace.md). 

* Anger experimentets namn. Detta `user_name` är konsekvent med den `user_name` som är kopplad till Azure Databricks arbets ytan.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Ställ in MLflow-spårning för att bara spåra i din Azure Machine Learning-arbetsyta

Om du föredrar att hantera dina spårade experiment på en central plats kan du ange MLflow spårning så att den **bara** spårar i din Azure Machine Learning-arbetsyta. 

Inkludera följande kod i skriptet:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Importera `mlflow` med hjälp av MLflow Logging-API: er i ditt utbildnings skript och börja logga dina körnings mått. I följande exempel loggas måttet för epoken. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Registrera modeller med MLflow

När din modell har tränats kan du logga och registrera dina modeller på Server dels spårnings servern med- `mlflow.<model_flavor>.log_model()` metoden. `<model_flavor>`, refererar till ramverket som är associerat med modellen. [Lär dig vilka modell-varianter som stöds](https://mlflow.org/docs/latest/models.html#model-api). 

Server dels spårnings servern är Azure Databricks arbets ytan som standard. Om du inte väljer att [Ange MLflow spårning för att bara spåra i din Azure Machine Learning-arbetsyta](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), är Server dels spårnings servern Azure Machine Learning arbets yta.   

* **Om en registrerad modell med namnet inte finns** registrerar metoden en ny modell, skapar version 1 och returnerar ett ModelVersion MLflow-objekt. 

* **Om det redan finns en registrerad modell med namnet** skapar metoden en ny modell version och returnerar versions objekt. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Skapa slut punkter för MLflow-modeller

När du är redo att skapa en slut punkt för dina ML-modeller. Du kan distribuera som, 

* En Azure Machine Learning Request-Response webb tjänst för interaktiv poäng. Med den här distributionen kan du utnyttja och använda Azure Machine Learning modell hantering samt funktioner för data Avkännings avkänning i dina produktions modeller. 

* MLFlow modell objekt, som kan användas i direkt uppspelnings-eller batch-pipeliner som python Functions eller Pandas UDF: er i Azure Databricks-arbetsyta.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Distribuera modeller till Azure Machine Learning slut punkter 
Du kan använda API: et [mlflow. azureml. Deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) för att distribuera en modell till din Azure Machine Learning-arbetsyta. Om du bara har registrerat modellen på Azure Databricks-arbetsytan, enligt beskrivningen i avsnittet [Registrera modeller med MLflow](#register-models-with-mlflow) , anger du `model_name` parametern för att registrera modellen i Azure Machine Learning arbets yta. 

Azure Databricks körningar kan distribueras till följande slut punkter, 
* [Azure Container instance](how-to-deploy-mlflow-models.md#deploy-to-azure-container-instance-aci)
* [Azure Kubernetes Service](how-to-deploy-mlflow-models.md#deploy-to-azure-kubernetes-service-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Distribuera modeller till ADB-slutpunkter för batch-Poäng 

Du kan välja Azure Databricks kluster för batch-poäng. MLFlow-modellen läses in och används som Spark Pandas UDF för att Poäng för nya data. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda de loggade måtten och artefakterna i din arbets yta är möjligheten att ta bort dem individuellt otillgänglig. Ta i stället bort resurs gruppen som innehåller lagrings kontot och arbets ytan, så du debiteras inte några avgifter:

1. I Azure-portalen väljer du **Resursgrupper** längst till vänster.

   ![Ta bort i Azure Portal](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Välj den resursgrupp i listan som du har skapat.

1. Välj **Ta bort resursgrupp**.

1. Ange resursgruppsnamnet. Välj sedan **Ta bort**.

## <a name="example-notebooks"></a>Exempelnotebook-filer

[MLflow med Azure Machine Learning antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) visar och utökar begrepp som presenteras i den här artikeln.

## <a name="next-steps"></a>Nästa steg
* [Distribuera MLflow-modeller som en Azure-webbtjänst](how-to-deploy-mlflow-models.md). 
* [Hantera dina modeller](concept-model-management-and-deployment.md).
* [Spåra experiment körningar med MLflow och Azure Machine Learning](how-to-use-mlflow.md). 
* Läs mer om [Azure Databricks och MLflow](/azure/databricks/applications/mlflow/).