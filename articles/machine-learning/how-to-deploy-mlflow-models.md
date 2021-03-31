---
title: Distribuera MLflow-modeller som webb tjänster
titleSuffix: Azure Machine Learning
description: Konfigurera MLflow med Azure Machine Learning för att distribuera dina ML-modeller som en Azure-webbtjänst.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: c45b819f9fc02fae40c2bf7fc5c2247c8c0a6147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517488"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>Distribuera MLflow-modeller som Azure-webbtjänster (för hands version)

I den här artikeln lär du dig hur du distribuerar din [MLflow](https://www.mlflow.org) -modell som en Azure-webbtjänst, så att du kan utnyttja och tillämpa Azure Machine Learning modell hanterings-och data funktions identifierings funktioner i dina produktions modeller.

Azure Machine Learning erbjuder distributions konfigurationer för:
* Azure Container Instance (ACI) som är ett lämpligt alternativ för en snabb distribution av utveckling och testning.
* Azure Kubernetes service (AKS) som rekommenderas för skalbara produktions distributioner.
> [!TIP]
> Informationen i det här dokumentet är främst avsedd för data experter och utvecklare som vill distribuera sin MLflow-modell till en Azure Machine Learning webb tjänst slut punkt. Om du är en administratör som vill övervaka resursanvändningen och händelser från Azure Machine Learning, till exempel kvoter, slutförda utbildningar eller slutförda modell distributioner, se [övervakning Azure Machine Learning](monitor-azure-machine-learning.md).
## <a name="mlflow-with-azure-machine-learning-deployment"></a>MLflow med Azure Machine Learning distribution

MLflow är ett bibliotek med öppen källkod för hantering av livs cykeln för maskin inlärnings experiment. Integrationen med Azure Machine Learning gör att du kan utöka den här hanteringen utanför modell utbildningen till distributions fasen i produktions modellen.

Följande diagram visar att med MLflow distributions-API och Azure Machine Learning kan du distribuera modeller som skapats med populära ramverk, t. ex. PyTorch, Tensorflow, scikit-lär, osv., som Azure-webbtjänster och hantera dem i din arbets yta. 

![ distribuera mlflow-modeller med Azure Machine Learning](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> Som ett bibliotek med öppen källkod ändras MLflow ofta. De funktioner som gjorts tillgängliga via Azure Machine Learning-och MLflow-integreringen bör därför betraktas som en för hands version och stöds inte fullt ut av Microsoft.

## <a name="prerequisites"></a>Förutsättningar

* En Machine Learning-modell. Om du inte har en tränad modell hittar du det exempel på bärbara datorer som bäst passar ditt beräknings scenario i [lagrings platsen](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) och följer anvisningarna. 
* [Konfigurera MLflow tracking URI för att ansluta Azure Machine Learning](how-to-use-mlflow.md#track-local-runs).
* Installera paketet `azureml-mlflow`. 
    * Det här paketet kommer automatiskt in i `azureml-core` [Azure Machine Learning python SDK](/python/api/overview/azure/ml/install), som ger anslutningen för MLflow åtkomst till din arbets yta.
* Se vilka [åtkomst behörigheter du behöver för att utföra dina MLflow-åtgärder med din arbets yta](how-to-assign-roles.md#mlflow-operations). 

## <a name="deploy-to-azure-container-instance-aci"></a>Distribuera till Azure Container Instance (ACI)

För att distribuera MLflow-modellen till en Azure Machine Learning-webbtjänst måste din modell konfigureras med [MLflow-spårnings-URI för att ansluta till Azure Machine Learning](how-to-use-mlflow.md). 

Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

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

## <a name="deploy-to-azure-kubernetes-service-aks"></a>Distribuera till Azure Kubernetes service (AKS)

För att distribuera MLflow-modellen till en Azure Machine Learning-webbtjänst måste din modell konfigureras med [MLflow-spårnings-URI för att ansluta till Azure Machine Learning](how-to-use-mlflow.md). 

Om du vill distribuera till AKS måste du först skapa ett AKS-kluster. Skapa ett AKS-kluster med metoden [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget#create-workspace--name--provisioning-configuration-) . Det kan ta 20-25 minuter att skapa ett nytt kluster.

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
Konfigurera distributions konfigurationen med metoden [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Du kan också lägga till taggar och beskrivningar för att hålla reda på din webb tjänst.

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

Om du inte planerar att använda den distribuerade webb tjänsten använder `service.delete()` du för att ta bort den från din bärbara dator.  Mer information finns i dokumentationen för [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--).

## <a name="example-notebooks"></a>Exempelnotebook-filer

[MLflow med Azure Machine Learning antecknings böcker](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) visar och utökar begrepp som presenteras i den här artikeln.

> [!NOTE]
> En community-driven databas med exempel som använder mlflow finns på https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Nästa steg

* [Hantera dina modeller](concept-model-management-and-deployment.md).
* Övervaka dina produktions modeller för [data avvikelser](./how-to-enable-data-collection.md).
* [Spåra Azure Databricks körs med MLflow](how-to-use-mlflow-azure-databricks.md).

