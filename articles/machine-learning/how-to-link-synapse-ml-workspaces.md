---
title: Skapa en länkad tjänst med Synapse och Azure Machine Learning arbetsytor (förhandsversion)
titleSuffix: Azure Machine Learning
description: Lär dig hur du länkar Azure Synapse och Azure Machine Learning arbetsytor för en enhetlig datahanteringsupplevelse.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 23184eee67013e39400446db5f744dd0ddb7bc50
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575750"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Länka Azure Synapse Analytics och Azure Machine Learning arbetsytor (förhandsversion)

I den här artikeln får du lära dig hur du skapar en länkad tjänst som [länkar Azure Synapse Analytics](/azure/synapse-analytics/overview-what-is) arbetsyta och [Azure Machine Learning arbetsyta](concept-workspace.md).

När din Azure Machine Learning-arbetsyta är länkad till din Azure Synapse-arbetsyta kan du koppla en Apache Spark-pool som en dedikerad beräkning för databearbetning i stor skala eller utföra modellträning från samma Python-notebook-filer.

Du kan länka ML-arbetsytan och Synapse-arbetsytan via [Python SDK](#link-sdk) eller [Azure Machine Learning-studio](#link-studio).

Du kan också länka arbetsytor och koppla en Synapse Spark-pool med en [arm Azure Resource Manager mall (single Azure Resource Manager).](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)

>[!IMPORTANT]
> Integreringen Azure Machine Learning och Azure Synapse är i offentlig förhandsversion. Funktionerna som presenteras i paketet `azureml-synapse` är [experimentella förhandsversionsfunktioner](/python/api/overview/azure/ml/#stable-vs-experimental) och kan ändras när som helst.

## <a name="prerequisites"></a>Förutsättningar

* [Skapa en Azure Machine Learning arbetsyta](how-to-manage-workspace.md?tabs=python).

* [Skapa en Synapse-arbetsyta i Azure Portal](/azure/synapse-analytics/quickstart-create-workspace).

* [Skapa Apache Spark-pool med Azure Portal, webbverktyg eller Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Installera [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro)

* Åtkomst till [Azure Machine Learning-studio](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Länka arbetsytor med Python SDK

> [!IMPORTANT]
> Om du vill länka till Synapse-arbetsytan måste du beviljas rollen **Ägare** för Synapse-arbetsytan. Kontrollera din åtkomst i [Azure Portal](https://ms.portal.azure.com/).
>
> Om du inte är **ägare och** bara är deltagare **i** Synapse-arbetsytan kan du bara använda befintliga länkade tjänster. Se hur du [försöker igen och använder en befintlig länkad tjänst.](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)

Följande kod använder klasserna [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) och [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) för att,

* Länka din arbetsyta för `ws` maskininlärning till din Azure Synapse arbetsyta.
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
> En hanterad `system_assigned_identity_principal_id` identitet, , skapas för varje länkad tjänst. Den här hanterade identiteten måste **beviljas Synapse Apache Spark** administratörsrollen för Synapse-arbetsytan innan du startar Synapse-sessionen. [Tilldela Synapse Apache Spark administratörsrollen till den hanterade identiteten i Synapse Studio](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Om du vill hitta `system_assigned_identity_principal_id` för en specifik länkad tjänst använder du `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Hantera länkade tjänster

Visa alla de länkade tjänster som är associerade med din maskininlärningsarbetsyta.

```python
LinkedService.list(ws)
```

Om du vill ta bort länken till dina arbetsytor använder du `unregister()` metoden

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Länka arbetsytor via Studio

Länka din maskininlärningsarbetsyta och Synapse-arbetsytan via Azure Machine Learning-studio med följande steg: 

1. Logga in på [Azure Machine Learning-studio](https://ml.azure.com/).
1. Välj **Länkade tjänster** i avsnittet **Hantera** i det vänstra fönstret.
1. Välj **Lägg till integration.**
1. I formuläret **Länka arbetsyta** fyller du i fälten

    |Fält| Beskrivning    
    |---|---
    |Name| Ange ett namn för den länkade tjänsten. Det här namnet är det som används för att referera till den här specifika länkade tjänsten.
    |Prenumerationens namn | Välj namnet på din prenumeration som är associerad med din maskininlärningsarbetsyta. 
    |Synapse-arbetsyta | Välj den Synapse-arbetsyta som du vill länka till.
    
1. Välj **Nästa för** att öppna formuläret Välj **Spark-pooler (valfritt).** I det här formuläret väljer du vilken Synapse Spark-pool som ska anslutas till din arbetsyta

1. Välj **Nästa** för att **öppna formuläret** Granska och kontrollera dina val.
1. Välj **Skapa för** att slutföra processen med att skapa den länkade tjänsten.

## <a name="next-steps"></a>Nästa steg

* [Bifoga Synapse Spark-pooler för förberedelse av data Azure Synapse (förhandsversion)](how-to-data-prep-synapse-spark-pool.md).
* [Så här använder du Apache Spark i din maskininlärningspipeline med Azure Synapse (förhandsversion)](how-to-use-synapsesparkstep.md)
* [Träna en modell](how-to-set-up-training-targets.md).
