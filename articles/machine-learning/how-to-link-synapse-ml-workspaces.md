---
title: Skapa en länkad tjänst med Synapse och Azure Machine Learning arbets ytor (förhands granskning)
titleSuffix: Azure Machine Learning
description: Lär dig hur du länkar Azure-Synapse och Azure Machine Learning arbets ytor för en enhetlig data datatransformering upplevelse.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: dd62fb5e3c7450d50b9837ee5484ca480cab78aa
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640839"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Länka Azure Synapse Analytics och Azure Machine Learning-arbetsytor (för hands version)

I den här artikeln får du lära dig hur du skapar en länkad tjänst som länkar din [Azure Synapse Analytics](/synapse-analytics/overview-what-is.md) -arbetsyta och [Azure Machine Learning arbets ytan](concept-workspace.md).

Med din Azure Machine Learning-arbetsyta som är länkad till din Azure Synapse-arbetsyta kan du koppla en Apache Spark pool som en dedikerad beräkning för data datatransformering i skala och utföra modell träning från samma bärbara dator.

Du kan länka din ML-arbets yta och Synapse-arbetsytan via [python SDK](#link-sdk) eller [Azure Machine Learning Studio](#link-studio).

Du kan också länka arbets ytor och koppla en Synapse Spark-pool med en enda [Azure Resource Manager-mall (arm)](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> Azure Machine Learning-och Azure Synapse-integrering finns i en offentlig för hands version. De `azureml-synapse` funktioner som presenteras från paketet är [experimentella](/python/api/overview/azure/ml/#stable-vs-experimental) för hands versions funktioner och kan ändras när som helst.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md?tabs=python).

* [Skapa en Synapse-arbetsyta i Azure Portal](/azure/synapse-analytics/quickstart-create-workspace).

* [Skapa Apache Spark pool med Azure Portal, webb verktyg eller Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Installera [Azure Machine Learning python SDK](/python/api/overview/azure/ml/intro)

* Åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Länka arbets ytor med python SDK

> [!IMPORTANT]
> För att du ska kunna länka till Synapse-arbetsytan måste du beviljas **ägar** rollen för Synapse-arbetsytan. Kontrol lera åtkomsten i [Azure Portal](https://ms.portal.azure.com/).
>
> Om du inte är **ägare** och bara är **deltagare** i Synapse-arbetsytan kan du bara använda befintliga länkade tjänster. Se hur du kan hämta [och använda en befintlig länkad tjänst](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service).

Följande kod använder [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) klasserna och för,

* Länka din Machine Learning-arbetsyta `ws` med din Azure Synapse-arbetsyta.
* Registrera din Synapse-arbetsyta med Azure Machine Learning som en länkad tjänst.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> En hanterad identitet, `system_assigned_identity_principal_id` skapas för varje länkad tjänst. Den här hanterade identiteten måste beviljas rollen **Synapse Apache Spark administratör** för Synapse-arbetsytan innan du startar Synapse-sessionen. [Tilldela rollen Synapse Apache Spark-administratör till den hanterade identiteten i Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Använd om du vill hitta `system_assigned_identity_principal_id` en speciell länkad tjänst `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Hantera länkade tjänster

Visa alla länkade tjänster som är kopplade till Machine Learning-arbetsytan.

```python
LinkedService.list(ws)
```

Om du vill ta bort länken till dina arbets ytor använder du `unregister()` metoden

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Länka arbets ytor via Studio

Länka din Machine Learning-arbetsyta och Synapse-arbetsyta via Azure Machine Learning Studio med följande steg: 

1. Logga in på [Azure Machine Learning Studio](https://ml.azure.com/).
1. Välj **länkade tjänster** i avsnittet **Hantera** i det vänstra fönstret.
1. Välj **Lägg till integrering**.
1. Fyll i fälten i formuläret **länk arbets yta**

    |Fält| Beskrivning    
    |---|---
    |Name| Ange ett namn för den länkade tjänsten. Det här namnet är vad som ska användas för att referera till den här länkade tjänsten.
    |Prenumerationens namn | Välj namnet på din prenumeration som är kopplad till Machine Learning-arbetsytan. 
    |Synapse-arbetsyta | Välj den Synapse-arbetsyta som du vill länka till.
    
1. Välj **Nästa** för att öppna formuläret **Välj Spark-pooler (valfritt)** . I det här formuläret väljer du vilken Synapse Spark-pool som ska kopplas till din arbets yta

1. Välj **Nästa** för att öppna **gransknings** formuläret och kontrol lera dina val.
1. Välj **skapa** för att slutföra processen för att skapa länkade tjänster.

## <a name="next-steps"></a>Nästa steg

* [Bifoga Synapse Spark-pooler för förberedelse av data med Azure Synapse (för hands version)](how-to-data-prep-synapse-spark-pool.md).
* [Använda Apache Spark i din Machine Learning-pipeline med Azure Synapse (för hands version)](how-to-use-synapsesparkstep.md)
* [Träna en modell](how-to-set-up-training-targets.md).
