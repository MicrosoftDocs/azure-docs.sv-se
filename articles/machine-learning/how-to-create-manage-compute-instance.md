---
title: Skapa och hantera en beräknings instans
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och hanterar en Azure Machine Learning beräknings instans. Använd som utvecklings miljö eller som beräknings mål för utvecklings-och test syfte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: da6285b7a4b12e65a82475fb53e972067e1c4fe4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214872"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Skapa och hantera en Azure Machine Learning beräknings instans

Lär dig hur du skapar och hanterar en [beräknings instans](concept-compute-instance.md) i din Azure Machine Learning-arbetsyta.

Använd en beräknings instans som din fullständigt konfigurerade och hanterade utvecklings miljö i molnet. För utveckling och testning kan du också använda instansen som ett [inlärnings mål](concept-compute-target.md#train) eller för ett mål för ett [mål](concept-compute-target.md#deploy).   En beräknings instans kan köra flera jobb parallellt och har en jobbkö. Som utvecklings miljö kan en beräknings instans inte delas med andra användare i din arbets yta.

I den här artikeln kan du se hur du:

* Skapa en beräkningsinstans 
* Hantera (starta, stoppa, starta om, ta bort) en beräknings instans
* Få åtkomst till terminalfönstret 
* Installera R-eller python-paket
* Skapa nya miljöer eller Jupyter-kärnor

Beräknings instanser kan köra jobb på ett säkert sätt i en [virtuell nätverks miljö](how-to-secure-training-vnet.md), utan att företag behöver öppna SSH-portar. Jobbet körs i en behållare miljö och paketerar dina modell beroenden i en Docker-behållare. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="create"></a>Skapa

**Tids uppskattning**: cirka 5 minuter.

Att skapa en beräknings instans är en process som en gång för din arbets yta. Du kan återanvända beräkningen som en utvecklings arbets Station eller som ett beräknings mål för träning. Du kan ha flera beräknings instanser kopplade till din arbets yta.

De dedikerade kärnorna per region per VM-tullkvot och den totala regionala kvoten som gäller för skapande av beräknings instanser, är enhetliga och delade med Azure Machine Learning inlärnings kluster kvot. Att stoppa beräknings instansen frigör inte kvoten för att se till att du kommer att kunna starta om beräknings instansen. Observera att det inte går att ändra storlek på en beräknings instans för den virtuella datorn när den har skapats.

Följande exempel visar hur du skapar en beräknings instans:

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

Mer information om klasser, metoder och parametrar som används i det här exemplet finns i följande referens dokument:

* [ComputeInstance-klass](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance?preserve-view=true&view=azure-ml-py)
* [ComputeTarget. Create](/python/api/azureml-core/azureml.core.compute.computetarget?preserve-view=true&view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)?preserve-view=true&view=azure-ml-py#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Mer information finns i [AZ ml computetarget Create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext_azure_cli_ml_az_ml_computetarget_create_computeinstance) reference.

# <a name="studio"></a>[Studio](#tab/azure-studio)

I arbets ytan i Azure Machine Learning Studio skapar du en ny beräknings instans från antingen **Compute** -avsnittet eller i avsnittet **antecknings böcker** när du är redo att köra en av dina antecknings böcker.

Information om hur du skapar en beräknings instans i Studio finns i [skapa beräknings mål i Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#compute-instance).

---

Du kan också skapa en beräknings instans med en [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance). 

### <a name="create-on-behalf-of-preview"></a>Skapa på uppdrag av (för hands version)

Som administratör kan du skapa en beräknings instans på uppdrag av en data expert och tilldela den instansen till dem med:
* [Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Information om hur du hittar TenantID och ObjectID som behövs i den här mallen finns i [hitta ID-objekt-ID: n för konfiguration av autentisering](../healthcare-apis/find-identity-object-ids.md).  Du kan också hitta dessa värden i Azure Active Directory-portalen.
* REST-API

Data expert som du skapar beräknings instansen för behöver följande är [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) -behörigheter: 
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/start/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/stoppa/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/omstart/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/computes/applicationaccess/Action*

Data expert kan starta, stoppa och starta om beräknings instansen. De kan använda beräknings instansen för:
* Jupyter
* JupyterLab
* RStudio
* Integrerade antecknings böcker

## <a name="manage"></a>Hantera

Starta, stoppa, starta om och ta bort en beräknings instans. En beräknings instans skalar inte automatiskt ned, så se till att stoppa resursen för att förhindra pågående kostnader.

# <a name="python"></a>[Python](#tab/python)

I exemplen nedan är namnet på beräknings instansen **instansen**

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

I exemplen nedan är namnet på beräknings instansen **instansen**

* Stoppa

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Mer information finns i [AZ ml computetarget Stop computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop).

* Start 

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Mer information finns i [AZ ml computetarget start computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start).

* Starta om 

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Mer information finns i [AZ ml computetarget restart computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

* Ta bort

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Mer information finns i [AZ ml computetarget Delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)

I arbets ytan i Azure Machine Learning Studio väljer du **Compute** och sedan **beräknings instans** överst.

![Hantera en beräknings instans](./media/concept-compute-instance/manage-compute-instance.png)

Du kan utföra följande åtgärder:

* Skapa en ny beräknings instans 
* Uppdatera fliken beräknings instanser.
* Starta, stoppa och starta om en beräknings instans.  Du betalar för instansen när den körs. Stoppa beräknings instansen när du inte använder den för att minska kostnaderna. Att stoppa en beräknings instans frigör den. Starta den sedan igen när du behöver den.
* Ta bort en beräknings instans.
* Filtrera listan över beräknings instanser så att endast de som du har skapat visas.

För varje beräknings instans i arbets ytan som du skapade (eller som har skapats åt dig) kan du:

* Åtkomst Jupyter, JupyterLab, RStudio på beräknings instansen
* SSH till beräknings instans. SSH-åtkomst är inaktive rad som standard men kan aktive ras vid skapande av beräknings instanser. SSH-åtkomst sker via en funktion för offentlig/privat nyckel. På fliken får du information om SSH-anslutning, till exempel IP-adress, användar namn och port nummer.
* Hämta information om en angiven beräknings instans, till exempel IP-adress och region.

---

Med [Azure RBAC](../role-based-access-control/overview.md) kan du styra vilka användare i arbets ytan som kan skapa, ta bort, starta, stoppa och starta om en beräknings instans. Alla användare i arbets ytans deltagare och ägar roll kan skapa, ta bort, starta, stoppa och starta om beräknings instanser i arbets ytan. Men endast skaparen av en angiven beräknings instans, eller användaren som tilldelats om den skapades för deras räkning, tillåts komma åt Jupyter, JupyterLab och RStudio på den beräknings instansen. En beräknings instans är dedikerad till en enda användare som har rot åtkomst och kan terminalen i genom Jupyter/JupyterLab/RStudio. Compute-instansen kommer att ha enkel inloggning och alla åtgärder kommer att använda användarens identitet för Azure RBAC och för att köra experiment körningar. SSH-åtkomsten styrs via mekanismen för offentlig/privat nyckel.

De här åtgärderna kan styras av Azure RBAC:
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/läsning*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/ta bort*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/start/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/stoppa/åtgärd*
* *Microsoft. MachineLearningServices/arbets ytor/beräkningar/omstart/åtgärd*

## <a name="next-steps"></a>Nästa steg

* [Få åtkomst till beräknings instansens Terminal](how-to-access-terminal.md)
* [Skapa och hantera filer](how-to-manage-files.md)
* [Skicka in en utbildnings körning](how-to-set-up-training-targets.md)
