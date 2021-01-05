---
title: Distribuera ML-experiment med MLflow
titleSuffix: Azure Machine Learning
description: Konfigurera MLflow med Azure Machine Learning för att distribuera dina ML-modeller som en webb tjänst.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: b905b050752e2a6b7acd11e82420c0b0203dfcd1
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882202"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>Distribuera MLflow-modeller med Azure Machine Learning (för hands version)

I den här artikeln lär du dig hur du distribuerar din [MLflow](https://www.mlflow.org) -modell som en Azure Machine Learning-webb tjänst, så att du kan använda och använda Azure Machine learnings funktioner för modell hantering och data identifiering i dina produktions modeller.

Azure Machine Learning erbjuder distributions konfigurationer för:
* Azure Container Instance (ACI) som är ett lämpligt alternativ för en snabb distribution av utveckling och testning.
* Azure Kubernetes service (AKS) som rekommenderas för skalbara produktions distributioner.

MLflow är ett bibliotek med öppen källkod för hantering av livs cykeln för maskin inlärnings experiment. Integrationen med Azure Machine Learning gör att du kan utöka den här hanteringen utanför modell utbildnings fasen till distributions fasen i produktions modellen.

>[!NOTE]
> Som ett bibliotek med öppen källkod ändras MLflow ofta. De funktioner som gjorts tillgängliga via Azure Machine Learning-och MLflow-integreringen bör därför betraktas som en för hands version och stöds inte fullt ut av Microsoft.

Följande diagram visar att med MLflow distributions-API och Azure Machine Learning kan du distribuera modeller som skapats med populära ramverk, t. ex. PyTorch, Tensorflow, scikit-lär, osv., som Azure Machine Learning webb tjänster och hantera dem i din arbets yta. 

![ distribuera mlflow-modeller med Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill distribuera sin MLflow-modell till en Azure Machine Learning webb tjänst slut punkt. Om du är en administratör som vill övervaka resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda utbildningar eller slutförda modell distributioner, se [övervakning Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Förutsättningar

* En Machine Learning-modell. Om du inte har en tränad modell hittar du det exempel på bärbara datorer som bäst passar ditt beräknings scenario i [lagrings platsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) och följer anvisningarna. 
* [Konfigurera MLflow tracking URI för att ansluta Azure Machine Learning](how-to-use-mlflow.md#track-local-runs).
* Installera paketet `azureml-mlflow`. 
    * Det här paketet kommer automatiskt in i `azureml-core` [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), som ger anslutningen för MLflow åtkomst till din arbets yta.
* Se vilka [åtkomst behörigheter du behöver för att utföra dina MLflow-åtgärder med din arbets yta](how-to-assign-roles.md#mlflow-operations). 

## <a name="deploy-to-aci"></a>Distribuera till ACI

För att distribuera MLflow-modellen till en Azure Machine Learning-webbtjänst måste din modell konfigureras med [MLflow-spårnings-URI för att ansluta till Azure Machine Learning](how-to-use-mlflow.md). 

Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Registrera och distribuera sedan modellen i ett steg med MLflow- [distributions](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) metod för Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>Distribuera till AKS

För att distribuera MLflow-modellen till en Azure Machine Learning-webbtjänst måste din modell konfigureras med [MLflow-spårnings-URI för att ansluta till Azure Machine Learning](how-to-use-mlflow.md). 

Om du vill distribuera till AKS måste du först skapa ett AKS-kluster. Skapa ett AKS-kluster med metoden [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Det kan ta 20-25 minuter att skapa ett nytt kluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Registrera och distribuera sedan modellen i ett steg med MLflow- [distributions](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) metod för Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Tjänste distributionen kan ta flera minuter.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att använda den distribuerade webb tjänsten använder `service.delete()` du för att ta bort den från din bärbara dator.  Mer information finns i dokumentationen för [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Exempelnotebook-filer

[MLflow med Azure Machine Learning antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) visar och utökar begrepp som presenteras i den här artikeln.

> [!NOTE]
> En community-driven databas med exempel som använder mlflow finns på https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Nästa steg

* [Hantera dina modeller](concept-model-management-and-deployment.md).
* Övervaka dina produktions modeller för [data avvikelser](./how-to-enable-data-collection.md).
* [Spåra Azure Databricks körs med MLflow](how-to-use-mlflow-azure-databricks.md).

