---
title: Konfigurera utbildning & härlednings mål
titleSuffix: Azure Machine Learning
description: Lägg till beräknings resurser (beräknings mål) till din arbets yta för att använda Machine Learning-utbildning och-härledning
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: c25f3965775c6518629c92ccc371855d9178e648
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033722"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Konfigurera beräknings mål för modell utbildning och distribution

Lär dig hur du kopplar Azure Compute-resurser till din Azure Machine Learning-arbetsyta.  Sedan kan du använda dessa resurser som utbildning och härlednings [mål](concept-compute-target.md) i dina Machine Learning-uppgifter.

I den här artikeln lär du dig hur du konfigurerar din arbets yta att använda dessa beräknings resurser:

* Din lokala dator
* Virtuella fjärrdatorer
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container-instans

Om du vill använda beräknings mål som hanteras av Azure Machine Learning, se:


* [Azure Machine Learning-beräkningsinstans](how-to-create-manage-compute-instance.md)
* [Azure Machine Learning beräknings kluster](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes service-kluster](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Begränsningar

* **Skapa inte flera, samtidiga bilagor till samma beräkning** från din arbets yta. Du kan till exempel ansluta ett Azure Kubernetes service-kluster till en arbets yta med två olika namn. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

    Om du vill återansluta ett beräknings mål, till exempel för att ändra TLS eller en annan kluster konfigurations inställning, måste du först ta bort den befintliga bilagan.

## <a name="whats-a-compute-target"></a>Vad är ett beräknings mål?

Med Azure Machine Learning kan du träna din modell på en mängd olika resurser eller miljöer, som sammankallas för [__beräknings mål__](concept-azure-machine-learning-architecture.md#compute-targets). Ett beräknings mål kan vara en lokal dator eller en moln resurs, t. ex. en Azure Machine Learning Compute, Azure HDInsight eller en virtuell dator.  Du kan också använda beräknings mål för modell distribution enligt beskrivningen i ["var och hur du distribuerar dina modeller"](how-to-deploy-and-where.md).


## <a name="local-computer"></a><a id="local"></a>Lokal dator

När du använder den lokala datorn för **utbildning** behöver du inte skapa något beräknings mål.  [Skicka bara utbildnings körningen](how-to-set-up-training-targets.md) från den lokala datorn.

När du använder den lokala datorn för att få en **härledning** måste du ha Docker installerat. Utför distributionen genom att använda [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py#deploy-configuration-port-none-) för att definiera den port som webb tjänsten ska använda. Använd sedan den normala distributions processen enligt beskrivningen i [Distribuera modeller med Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Virtuella fjärrdatorer

Azure Machine Learning också stöd för att ta med din egen beräknings resurs och koppla den till din arbets yta. En sådan resurs typ är en godtycklig virtuell fjärrdator, så länge den är tillgänglig från Azure Machine Learning. Resursen kan vara en virtuell Azure-dator, en fjärrserver i din organisation eller lokalt. Mer specifikt, baserat på IP-adress och autentiseringsuppgifter (användar namn och lösen ord eller SSH-nyckel), kan du använda valfri tillgänglig virtuell dator för fjärrkörningar.

Du kan använda en systemskapad Conda-miljö, en redan befintlig python-miljö eller en Docker-behållare. Om du vill köra på en Docker-behållare måste du ha en Docker-motor som körs på den virtuella datorn. Den här funktionen är särskilt användbar när du vill ha en mer flexibel, molnbaserad utvecklings-eller experiment miljö än din lokala dator.

Använd Azure-Data Science Virtual Machine (DSVM) som den virtuella Azure-dator som du väljer för det här scenariot. Den här virtuella datorn är en förkonfigurerad miljö för data vetenskap och AI-utveckling i Azure. Den virtuella datorn innehåller ett granskat val av verktyg och ramverk för hela livs cykeln för Machine Learning-utveckling. Mer information om hur du använder DSVM med Azure Machine Learning finns i [Konfigurera en utvecklings miljö](./how-to-configure-environment.md#dsvm).

1. **Skapa**: skapa en DSVM innan du använder den för att träna din modell. Information om hur du skapar den här resursen finns i [etablera data science Virtual Machine för Linux (Ubuntu)](./data-science-virtual-machine/dsvm-ubuntu-intro.md).

    > [!WARNING]
    > Azure Machine Learning stöder bara virtuella datorer som kör **Ubuntu**. När du skapar en virtuell dator eller väljer en befintlig virtuell dator måste du välja en virtuell dator som använder Ubuntu.
    > 
    > Azure Machine Learning kräver också att den virtuella datorn har en __offentlig IP-adress__.

1. **Bifoga**: om du vill koppla en befintlig virtuell dator som ett beräknings mål måste du ange resurs-ID, användar namn och lösen ord för den virtuella datorn. Resurs-ID: t för den virtuella datorn kan konstrueras med prenumerations-ID, resurs grupp namn och namn på virtuell dator med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du koppla DSVM till din arbets yta [med Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Skapa inte flera, samtidiga bilagor till samma DSVM från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

1. **Konfigurera**: skapa en körnings konfiguration för DSVM Compute Target. Docker och Conda används för att skapa och konfigurera utbildnings miljön på DSVM.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight är en populär plattform för stor data analys. Plattformen ger Apache Spark som kan användas för att träna din modell.

1. **Skapa**: skapa HDInsight-klustret innan du använder det för att träna din modell. Information om hur du skapar ett spark på HDInsight-kluster finns i [skapa ett Spark-kluster i HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Azure Machine Learning kräver att HDInsight-klustret har en __offentlig IP-adress__.

    När du skapar klustret måste du ange ett SSH-användarnamn och-lösen ord. Anteckna dessa värden eftersom du behöver dem för att använda HDInsight som ett beräknings mål.
    
    När klustret har skapats ansluter du till det med hostname \<clustername> -SSH.azurehdinsight.net, där \<clustername> är det namn som du har angett för klustret. 

1. **Bifoga**: om du vill ansluta ett HDInsight-kluster som ett beräknings mål måste du ange resurs-ID, användar namn och lösen ord för HDInsight-klustret. Resurs-ID för HDInsight-klustret kan konstrueras med prenumerations-ID, resurs grupp namn och HDInsight-kluster namn med följande sträng format: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Eller så kan du ansluta HDInsight-klustret till din arbets yta [med Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Skapa inte flera, samtidiga bilagor till samma HDInsight från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

1. **Konfigurera**: skapa en körnings konfiguration för HDI Compute Target. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Nu när du har kopplat beräkningen och konfigurerat din körning är nästa steg att [Skicka utbildningen](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch används för att köra storskaliga parallella program och HPC-program (data behandling med höga prestanda) i molnet. AzureBatchStep kan användas i en Azure Machine Learning pipeline för att skicka jobb till en Azure Batch pool med datorer.

Om du vill bifoga Azure Batch som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

-    **Azure Batch Compute Name**: ett eget namn som ska användas för data bearbetningen inom arbets ytan
-    **Azure Batch konto namn**: namnet på det Azure Batch kontot
-    **Resurs grupp**: den resurs grupp som innehåller det Azure Batch kontot.

Följande kod visar hur du kopplar Azure Batch som ett beräknings mål:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma Azure Batch från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks är en Apache Spark-baserad miljö i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa en Azure Databricks arbets yta innan du använder den. Information om hur du skapar en arbets ytas resurs finns i [köra ett Spark-jobb på Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) -dokument.

Om du vill bifoga Azure Databricks som ett beräknings mål anger du följande information:

* __Databricks Compute Name__: det namn som du vill tilldela till den här beräknings resursen.
* __Databricks namn på arbets yta__: namnet på arbets ytan Azure Databricks.
* __Databricks__: åtkomst-token som används för att autentisera till Azure Databricks. Om du vill generera en åtkomsttoken, se [Authentication](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html) -dokumentet.

Följande kod visar hur du kopplar Azure Databricks som ett beräknings mål med Azure Machine Learning SDK (Databricks-__arbetsytan måste finnas i samma prenumeration som din AML-arbetsyta__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-databricks) på GitHub.

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma Azure Databricks från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics är en stor data analys plattform i Azure-molnet. Den kan användas som ett beräknings mål med en Azure Machine Learning pipeline.

Skapa ett Azure Data Lake Analytics konto innan du använder det. Information om hur du skapar den här resursen finns i dokumentet [Kom igång med Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) .

Om du vill bifoga Data Lake Analytics som ett beräknings mål måste du använda Azure Machine Learning SDK och ange följande information:

* __Compute-namn__: det namn som du vill tilldela till den här beräknings resursen.
* __Resurs grupp__: den resurs grupp som innehåller det data Lake Analytics kontot.
* __Konto namn__: namnet på data Lake Analytics kontot.

Följande kod visar hur du kopplar Data Lake Analytics som ett beräknings mål:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Ett mer detaljerat exempel finns i en [exempel antecknings bok](https://aka.ms/pl-adla) på GitHub.

> [!WARNING]
> Skapa inte flera, samtidiga bilagor till samma ADLA från din arbets yta. Varje ny bilaga kommer att dela upp de tidigare befintliga bifogade filerna.

> [!TIP]
> Azure Machine Learning pipelines kan bara arbeta med data som lagras i standard data lagret för det Data Lake Analytics kontot. Om de data du behöver arbeta med finns i ett lager som inte är standard kan du använda en [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?preserve-view=true&view=azure-ml-py) för att kopiera data före träning.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container-instans

Azure Container Instances (ACI) skapas dynamiskt när du distribuerar en modell. Du kan inte skapa eller koppla ACI till din arbets yta på något annat sätt. Mer information finns i [distribuera en modell till Azure Container instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Med Azure Kubernetes service (AKS) kan du välja mellan olika konfigurations alternativ när de används med Azure Machine Learning. Mer information finns i [så här skapar och ansluter du Azure Kubernetes-tjänsten](how-to-create-attach-kubernetes.md).


## <a name="notebook-examples"></a>Exempel på bärbara datorer

Se dessa antecknings böcker för exempel på utbildning med olika beräknings mål:
* [instruktion för att använda – azureml/utbildning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [Självstudier/img-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

* Använd beräknings resursen för att [Konfigurera och skicka en utbildnings körning](how-to-set-up-training-targets.md).
* [Självstudie: träna en modell](tutorial-train-models-with-aml.md) använder ett hanterat beräknings mål för att träna en modell.
* Lär dig hur du [effektivt justerar disponeringsparametrarna](how-to-tune-hyperparameters.md) för att bygga bättre modeller.
* När du har en tränad modell lär du dig [hur och var modeller ska distribueras](how-to-deploy-and-where.md).
* [Använda Azure Machine Learning med virtuella Azure-nätverk](./how-to-network-security-overview.md)