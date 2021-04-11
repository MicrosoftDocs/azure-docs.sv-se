---
title: Skapa beräknings kluster
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar beräknings kluster i din Azure Machine Learning-arbetsyta. Använd beräknings klustret som ett beräknings mål för utbildning eller härledning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: 1e3549a6f5f4f9d7f6a6da574378c90c20e42dcf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169580"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>Skapa ett Azure Machine Learning beräknings kluster

Lär dig hur du skapar och hanterar ett [beräknings kluster](concept-compute-target.md#azure-machine-learning-compute-managed) i din Azure Machine Learning-arbetsyta.

Du kan använda Azure Machine Learning Compute Cluster för att distribuera en Training-eller batch-härledning över ett kluster av processor-eller GPU-datornoder i molnet. Mer information om de VM-storlekar som innehåller GPU: er finns i [GPU-optimerade storlekar för virtuella datorer](../virtual-machines/sizes-gpu.md). 

I den här artikeln får du lära dig att:

* Skapa ett beräkningskluster
* Sänk din beräknings kluster kostnad
* Konfigurera en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för klustret

## <a name="prerequisites"></a>Förutsättningar

* En Azure Machine Learning-arbetsyta. Mer information finns i [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

* [Azure CLI-tillägget för Machine Learning-tjänst](reference-azure-machine-learning-cli.md), [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)eller [Azure Machine Learning Visual Studio Code-tillägget](tutorial-setup-vscode-extension.md).

## <a name="what-is-a-compute-cluster"></a>Vad är ett beräknings kluster?

Azure Machine Learning Compute Cluster är en hanterad beräknings infrastruktur som gör att du enkelt kan skapa en beräkning med en enda eller flera noder. Beräkningen skapas i arbets ytans region som en resurs som kan delas med andra användare i din arbets yta. Beräkningen skalas upp automatiskt när ett jobb skickas och kan placeras i ett Azure-Virtual Network. Beräkningen körs i en behållare miljö och paketerar dina modell beroenden i en [Docker-behållare](https://www.docker.com/why-docker).

Compute-kluster kan köra jobb säkert i en [virtuell nätverks miljö](how-to-secure-training-vnet.md), utan att företag behöver öppna SSH-portar. Jobbet körs i en behållare miljö och paketerar dina modell beroenden i en Docker-behållare. 

## <a name="limitations"></a>Begränsningar

* Några av de scenarier som anges i det här dokumentet är markerade som för __hands version__. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Vi stöder för närvarande endast att skapa (och inte uppdatera) kluster med ARM-mallar [ https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/workspaces/computes?tabs=json ]. Vid uppdatering av beräkning rekommenderar vi att du använder SDK, CLI eller UX för tillfället.

* Azure Machine Learning Compute har standard gränser, till exempel antalet kärnor som kan allokeras. Mer information finns i [Hantera och begära kvoter för Azure-resurser](how-to-manage-quotas.md).

* Med Azure kan du placera _Lås_ på resurser, så att de inte kan tas bort eller är skrivskyddade. __Använd inte resurs lås till resurs gruppen som innehåller din arbets yta__. Genom att använda ett lås på den resurs grupp som innehåller arbets ytan kan du förhindra skalnings åtgärder för Azure ML-beräknings kluster. Mer information om hur du låser resurser finns i [låsa resurser för att förhindra oväntade ändringar](../azure-resource-manager/management/lock-resources.md).

> [!TIP]
> Kluster kan i allmänhet skala upp till 100 noder så länge som du har tillräckligt med kvot för antalet kärnor som krävs. Som standard konfigureras kluster för kommunikation mellan noder mellan noderna i klustret som stöd för MPI-jobb till exempel. Du kan dock skala dina kluster till tusentals noder genom att bara [höja ett support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)och begära att tillåta att en lista över din prenumeration, arbets yta eller ett särskilt kluster inaktive ras i kommunikation mellan noder. 


## <a name="create"></a>Skapa

**Tids uppskattning**: cirka 5 minuter.

Azure Machine Learning Compute kan återanvändas över körningar. Beräkningen kan delas med andra användare på arbets ytan och bevaras mellan körningar och automatiskt skalar noderna uppåt eller nedåt baserat på antalet körningar som skickats och max_nodes som angetts i klustret. Inställningen min_nodes styr de minsta tillgängliga noderna.

De dedikerade kärnorna per region per VM-tullkvot och den totala regionala kvoten som används för att skapa beräknings kluster, är enhetliga och delade med Azure Machine Learning utbildnings instans kvot för beräkning. 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Beräkningen skalas ned till noll noder när den inte används.   Dedikerade virtuella datorer skapas för att köra jobben efter behov.
    
# <a name="python"></a>[Python](#tab/python)

Om du vill skapa en beständig Azure Machine Learning beräknings resurs i python anger du **vm_size** och **max_nodes** egenskaper. Azure Machine Learning använder sedan smarta standardinställningar för de andra egenskaperna. 
    
* **vm_size**: VM-serien för noderna som skapats av Azure Machine Learning Compute.
* **max_nodes**: det högsta antalet noder som autoskalar upp till när du kör ett jobb på Azure Machine Learning beräkning.


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

Du kan också konfigurera flera avancerade egenskaper när du skapar Azure Machine Learning Compute. Med egenskaperna kan du skapa ett beständigt kluster med fast storlek eller inom en befintlig Azure-Virtual Network i din prenumeration.  Mer information finns i [AmlCompute-klassen](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute) .


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

Mer information finns i [AZ ml computetarget Create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Information om hur du skapar ett beräknings kluster i Studio finns i [skapa beräknings mål i Azure Machine Learning Studio](how-to-create-attach-compute-studio.md#amlcompute).

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Sänk din beräknings kluster kostnad

Du kan också välja att använda [virtuella datorer med låg prioritet](concept-plan-manage-cost.md#low-pri-vm) för att köra vissa eller alla arbets belastningar. De här virtuella datorerna har inte garanterad tillgänglighet och kan komma att blockeras när den används. Du måste starta om ett jobb som inte körs. 

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

I Studio väljer du **låg prioritet** när du skapar en virtuell dator.

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Konfigurera hanterad identitet

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* Konfigurera hanterad identitet i etablerings konfigurationen:  

    * Systemtilldelad hanterad identitet:
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * Användardefinierad hanterad identitet:
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* Lägg till hanterad identitet i ett befintligt beräknings kluster 
    
    * Systemtilldelad hanterad identitet:
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * Användardefinierad hanterad identitet:
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* Skapa ett nytt hanterat beräknings kluster med hanterad identitet

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

### <a name="managed-identity-usage"></a>Hanterad identitets användning

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="troubleshooting"></a>Felsökning

Det finns en chans att vissa användare som har skapat sin Azure Machine Learning-arbetsyta från Azure Portal innan GA-versionen kanske inte kan skapa AmlCompute på arbets ytan. Du kan antingen utlösa en supportbegäran mot tjänsten eller skapa en ny arbets yta via portalen eller SDK för att häva blockeringen direkt.

Om ditt Azure Machine Learning Compute-kluster ser fastnat i storleks ändring (0-> 0) för Node-tillståndet kan detta bero på att Azure-resursens lås är låst.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Nästa steg

Använd ditt beräknings kluster för att:

* [Skicka in en utbildnings körning](how-to-set-up-training-targets.md) 
* [Kör batch-härledning](./tutorial-pipeline-batch-scoring-classification.md).
