---
title: Skapa och hantera en beräkningsinstans
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och hanterar Azure Machine Learning en beräkningsinstans. Använd som utvecklingsmiljö eller som beräkningsmål för utveckling/testning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 5ac525ae062efca25601c9e63a5c8f16f2be29be
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861236"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Skapa och hantera en Azure Machine Learning beräkningsinstans

Lär dig hur du skapar och hanterar en [beräkningsinstans](concept-compute-instance.md) i Azure Machine Learning arbetsyta.

Använd en beräkningsinstans som din fullständigt konfigurerade och hanterade utvecklingsmiljö i molnet. För utveckling och testning kan du också använda instansen som ett [träningsbearbetningsmål](concept-compute-target.md#train) eller som [ett inferensmål.](concept-compute-target.md#deploy)   En beräkningsinstans kan köra flera jobb parallellt och har en jobbkö. Som en utvecklingsmiljö kan en beräkningsinstans inte delas med andra användare på din arbetsyta.

I den här artikeln kan du se hur du:

* Skapa en beräkningsinstans 
* Hantera (starta, stoppa, starta om, ta bort) en beräkningsinstans
* Öppna terminalfönstret 
* Installera R- eller Python-paket
* Skapa nya miljöer eller Jupyter-kernels

Beräkningsinstanser kan köra jobb på ett säkert sätt [i en virtuell nätverksmiljö](how-to-secure-training-vnet.md)utan att företag behöver öppna SSH-portar. Jobbet körs i en containermiljö och paketerar dina modellberoenden i en Docker-container. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)

* [Azure CLI-tillägget för Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Skapa

**Uppskattad** tidsuppskattning: Cirka 5 minuter.

Att skapa en beräkningsinstans är en enda process för din arbetsyta. Du kan återanvända beräkningen som en arbetsstation för utveckling eller som ett beräkningsmål för träning. Du kan ha flera beräkningsinstanser kopplade till din arbetsyta.

De dedikerade kärnorna per region per VM-familj och den totala regionala kvoten, som gäller för skapande av beräkningsinstanser, är enhetliga och delas med Azure Machine Learning beräkningsklusterkvoten för träning. Om du stoppar beräkningsinstansen frigörs inte kvoten för att säkerställa att du kan starta om beräkningsinstansen. Observera att det inte går att ändra storleken på den virtuella datorn för beräkningsinstansen när den har skapats.

Följande exempel visar hur du skapar en beräkningsinstans:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referensdokument:

* [ComputeInstance-klass](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Mer information finns i referensen [az ml computetarget create computeinstance.](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_computeinstance)

# <a name="studio"></a>[Studio](#tab/azure-studio)

I arbetsytan i Azure Machine Learning-studio du en ny **beräkningsinstans** från antingen avsnittet **Compute** eller i avsnittet Notebooks när du är redo att köra en av dina notebook-datorer.

Information om hur du skapar en beräkningsinstans i studio finns i [Skapa beräkningsmål i Azure Machine Learning-studio](how-to-create-attach-compute-studio.md#compute-instance).

---

Du kan också skapa en beräkningsinstans med en [Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Skapa för (förhandsversion)

Som administratör kan du skapa en beräkningsinstans åt en dataexpert och tilldela instansen till dem med:
* [Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Mer information om hur du hittar det TenantID och ObjectID som behövs i den här mallen finns i Hitta ID:t för [identitetsobjekt för autentiseringskonfiguration.](../healthcare-apis/fhir/find-identity-object-ids.md)  Du hittar även dessa värden i Azure Active Directory portalen.
* REST-API

Dataexperten som du skapar beräkningsinstansen för behöver följande [azure-behörigheter för rollbaserad åtkomstkontroll (Azure RBAC):](../role-based-access-control/overview.md) 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

Dataexperten kan starta, stoppa och starta om beräkningsinstansen. De kan använda beräkningsinstansen för:
* Jupyter
* JupyterLab
* RStudio
* Integrerade notebook-datorer

## <a name="manage"></a>Hantera

Starta, stoppa, starta om och ta bort en beräkningsinstans. En beräkningsinstans skalas inte ned automatiskt, så se till att stoppa resursen för att förhindra löpande avgifter.

> [!TIP]
> Beräkningsinstansen har en OS-disk på 120 GB. Om diskutrymmet tar slut använder du [terminalen](how-to-access-terminal.md) för att rensa minst 1–2 GB innan du stoppar eller startar om beräkningsinstansen.

# <a name="python"></a>[Python](#tab/python)

I exemplen nedan är namnet på beräkningsinstansen **instansen**

* Hämta status

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Stoppa

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Start

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Starta om

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Ta bort

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name 
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

I exemplen nedan är namnet på beräkningsinstansen **instansen**

* Stoppa

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Mer information finns i [az ml computetarget stop computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

* Start 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Mer information finns i [az ml computetarget start computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

* Starta om 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Mer information finns i [az ml computetarget restart computeinstance](/cli/azure/ml/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

* Ta bort

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Mer information finns i [az ml computetarget delete computeinstance](/cli/azure/ml/computetarget#az_ml_computetarget_delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

I arbetsytan i Azure Machine Learning-studio väljer **du Compute** och sedan **Beräkningsinstans** högst upp.

![Hantera en beräkningsinstans](./media/concept-compute-instance/manage-compute-instance.png)

Du kan utföra följande åtgärder:

* Skapa en ny beräkningsinstans 
* Uppdatera fliken beräkningsinstanser.
* Starta, stoppa och starta om en beräkningsinstans.  Du betalar för instansen när den körs. Stoppa beräkningsinstansen när du inte använder den för att minska kostnaderna. Om du stoppar en beräkningsinstans så frisplaceras den. Starta den sedan igen när du behöver den.
* Ta bort en beräkningsinstans.
* Filtrera listan över beräkningsinstanser så att endast de som du har skapat visas.

För varje beräkningsinstans på arbetsytan som du skapade (eller som har skapats åt dig) kan du:

* Få åtkomst till Jupyter, JupyterLab, RStudio på beräkningsinstansen
* SSH till beräkningsinstans. SSH-åtkomst är inaktiverat som standard men kan aktiveras när beräkningsinstansen skapas. SSH-åtkomsten är via mekanismen för offentlig/privat nyckel. På fliken visas information om SSH-anslutning, till exempel IP-adress, användarnamn och portnummer.
* Få information om en specifik beräkningsinstans, till exempel IP-adress och region.

---


[Med Azure RBAC](../role-based-access-control/overview.md) kan du styra vilka användare på arbetsytan som kan skapa, ta bort, starta, stoppa och starta om en beräkningsinstans. Alla användare i arbetsytans deltagare och ägarroll kan skapa, ta bort, starta, stoppa och starta om beräkningsinstanser på arbetsytan. Det är dock bara skaparen av en specifik beräkningsinstans, eller den användare som tilldelats om den har skapats för deras räkning, som får åtkomst till Jupyter, JupyterLab och RStudio på den beräkningsinstansen. En beräkningsinstans är dedikerad till en enskild användare som har rotåtkomst och kan terminala in via Jupyter/JupyterLab/RStudio. Beräkningsinstansen har enkel inloggning och alla åtgärder använder användarens identitet för Azure RBAC och attribution av experimentkörningar. SSH-åtkomst styrs via mekanismen för offentliga/privata nycklar.

Dessa åtgärder kan styras av Azure RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

## <a name="next-steps"></a>Nästa steg

* [Få åtkomst till terminalen för beräkningsinstansen](how-to-access-terminal.md)
* [Skapa och hantera filer](how-to-manage-files.md)
* [Skicka en träningskörning](how-to-set-up-training-targets.md)
