---
title: Skapa beräkningskluster
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar beräkningskluster i din Azure Machine Learning arbetsyta. Använd beräkningsklustret som ett beräkningsmål för träning eller slutsatsledning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: f61936e622a539b29c6788f631df5de42bb2f242
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861255"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Skapa ett Azure Machine Learning beräkningskluster

Lär dig hur du skapar och hanterar ett [beräkningskluster](concept-compute-target.md#azure-machine-learning-compute-managed) i din Azure Machine Learning arbetsyta.

Du kan använda Azure Machine Learning beräkningskluster för att distribuera en tränings- eller batch inferensprocess över ett kluster med CPU- eller GPU-beräkningsnoder i molnet. Mer information om VM-storlekar som inkluderar GPU:er finns i [STORLEKAR för GPU-optimerade virtuella datorer.](../virtual-machines/sizes-gpu.md) 

I den här artikeln får du lära dig att:

* Skapa ett beräkningskluster
* Sänk kostnaden för beräkningskluster
* Konfigurera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för klustret

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i Skapa [en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)

* [Azure CLI-tillägget för Machine Learning service](reference-azure-machine-learning-cli.md), Azure Machine Learning Python [SDK](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

* Om du använder Python SDK [ställer du in utvecklingsmiljön med en arbetsyta](how-to-configure-environment.md).  När din miljö har ställts in ansluter du till arbetsytan i Python-skriptet:

    ```python
    from azureml.core import Workspace
    
    ws = Workspace.from_config() 
    ```

## <a name="what-is-a-compute-cluster"></a>Vad är ett beräkningskluster?

Azure Machine Learning ett beräkningskluster är en hanterad beräkningsinfrastruktur som gör att du enkelt kan skapa en beräkning med en eller flera noder. Beräkningen skapas i din arbetsyteregion som en resurs som kan delas med andra användare i din arbetsyta. Beräkningen skalas upp automatiskt när ett jobb skickas och kan läggas i ett Azure-Virtual Network. Beräkningen körs i en containermiljö och paketerar modellberoendena i en [Docker-container.](https://www.docker.com/why-docker)

Beräkningskluster kan köra jobb på ett säkert sätt [i en virtuell nätverksmiljö](how-to-secure-training-vnet.md)utan att företag behöver öppna SSH-portar. Jobbet körs i en containermiljö och paketerar dina modellberoenden i en Docker-container. 

## <a name="limitations"></a>Begränsningar

* Några av de scenarier som anges i det här dokumentet är markerade som __förhandsversion.__ Förhandsversionsfunktioner tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Vi stöder för närvarande endast skapande (och inte uppdatering) av kluster via ARM-mallar [ https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json ]. För att uppdatera beräkning rekommenderar vi att du använder SDK, CLI eller UX för tillfället.

* Azure Machine Learning Compute har standardgränser, till exempel antalet kärnor som kan allokeras. Mer information finns i Hantera [och begära kvoter för Azure-resurser.](how-to-manage-quotas.md)

* Med Azure kan du placera _lås_ på resurser så att de inte kan tas bort eller är skrivskyddade. __Använd inte resurslås för den resursgrupp som innehåller din arbetsyta.__ Om du använder ett lås för resursgruppen som innehåller arbetsytan förhindras skalningsåtgärder för Azure ML-beräkningskluster. Mer information om hur du låser resurser finns i [Låsa resurser för att förhindra oväntade ändringar.](../azure-resource-manager/management/lock-resources.md)

> [!TIP]
> Kluster kan vanligtvis skala upp till 100 noder så länge du har tillräckligt med kvot för antalet kärnor som krävs. Som standard konfigureras kluster med kommunikation mellan noder aktiverat mellan noderna i klustret för att till exempel stödja MPI-jobb. Du kan dock skala dina kluster till 1 000-tals noder genom att helt enkelt skapa en [supportbiljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)och begära att din prenumeration eller arbetsyta listas, eller ett specifikt kluster för inaktivering av kommunikation mellan noder.


## <a name="create"></a>Skapa

**Tidsuppskattning:** Cirka 5 minuter.

Azure Machine Learning Compute kan återanvändas mellan körningar. Beräkningen kan delas med andra användare på arbetsytan och bevaras mellan körningar, automatiskt skala noder uppåt eller nedåt baserat på antalet körningar som skickats och max_nodes angetts i klustret. Inställningen min_nodes kontrollerar de minsta noderna som är tillgängliga.

De dedikerade kärnorna per region per VM-familj och den totala regionala kvoten, som gäller för skapande av beräkningskluster, är enhetliga och delas med Azure Machine Learning träningskvot för beräkningsinstanser. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Beräkningen skalas automatiskt ned till noll noder när den inte används.   Dedikerade virtuella datorer skapas för att köra dina jobb efter behov.
    
# <a name="python"></a>[Python](#tab/python)


Om du vill skapa en beständig Azure Machine Learning Compute-resurs i Python **anger du vm_size** och **max_nodes** egenskaper. Azure Machine Learning sedan smarta standardvärden för de andra egenskaperna.
    
* **vm_size:** Den virtuella datorfamiljen för noderna som skapats av Azure Machine Learning Compute.
* **max_nodes:** Det maximala antalet noder som ska skalas upp automatiskt till när du kör ett jobb på Azure Machine Learning Compute.

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Du kan också konfigurera flera avancerade egenskaper när du skapar Azure Machine Learning Compute. Med egenskaperna kan du skapa ett beständigt kluster med fast storlek eller i ett befintligt Azure-Virtual Network i din prenumeration.  Mer information [finns i klassen AmlCompute.](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Mer information finns i [az ml computetarget create amlcompute](/cli/azure/ml/computetarget/create#az_ml_computetarget_create_amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Information om hur du skapar ett beräkningskluster i Studio finns [i Skapa beräkningsmål i Azure Machine Learning-studio](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Sänk kostnaden för beräkningskluster

Du kan också välja att använda [lågprioriterade virtuella datorer](concept-plan-manage-cost.md#low-pri-vm) för att köra vissa eller alla dina arbetsbelastningar. De här virtuella datorerna har inte garanterad tillgänglighet och kan tas i bruk när de används. Du måste starta om ett avinbestämt jobb. 

Använd något av följande sätt för att ange en virtuell dator med låg prioritet:
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ange `vm-priority` :
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[Studio](#tab/azure-studio)

I Studio väljer du Låg **prioritet när** du skapar en virtuell dator.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Konfigurera hanterad identitet

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Konfigurera hanterad identitet i etableringskonfigurationen:  

    * System tilldelad hanterad identitet som skapats i en arbetsyta med namnet `ws`
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```
    
    * Användar tilldelad hanterad identitet som skapats i en arbetsyta med namnet `ws`
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Lägga till hanterad identitet i ett befintligt beräkningskluster med namnet `cpu_cluster`
    
    * System tilldelad hanterad identitet:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Användar tilldelad hanterad identitet:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Skapa ett nytt hanterat beräkningskluster med hanterad identitet

  * Användartilldelad hanterad identitet

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * Systemtilldelad hanterad identitet

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* Lägg till en hanterad identitet i ett befintligt kluster:

    * Användartilldelad hanterad identitet
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * Systemtilldelad hanterad identitet

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[Studio](#tab/azure-studio)

Se [Konfigurera hanterad identitet i Studio](how-to-create-attach-compute-studio.md#managed-identity).

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>Hanterad identitetsanvändning

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Felsökning

Det finns en risk att vissa användare som skapade sin Azure Machine Learning-arbetsyta från Azure Portal innan ga-versionen kanske inte kan skapa AmlCompute på den arbetsytan. Du kan antingen skapa en supportbegäran mot tjänsten eller skapa en ny arbetsyta via portalen eller SDK för att avblockera dig själv direkt.

Om ditt Azure Machine Learning beräkningsklustret har fastnat vid storleksändringen (0–> 0) för nodtillståndet kan detta bero på Azure-resurslås.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Nästa steg

Använd beräkningsklustret för att:

* [Skicka en träningskörning](how-to-set-up-training-targets.md) 
* [Kör batch-inferens](./tutorial-pipeline-batch-scoring-classification.md).
