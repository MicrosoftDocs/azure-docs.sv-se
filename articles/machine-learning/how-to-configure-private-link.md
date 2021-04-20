---
title: Konfigurera en privat slutpunkt
titleSuffix: Azure Machine Learning
description: Använd Azure Private Link för att få säker åtkomst Azure Machine Learning din arbetsyta från ett virtuellt nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 02/09/2021
ms.openlocfilehash: 0ea4e3ae0113608203dad63f636ae4adb4eeff9b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107737522"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace"></a>Konfigurera Azure Private Link för en Azure Machine Learning arbetsyta

I det här dokumentet får du lära dig hur du använder Azure Private Link med din Azure Machine Learning arbetsyta. Information om hur du skapar ett virtuellt nätverk för Azure Machine Learning finns i Översikt [över isolering och sekretess för virtuella nätverk](how-to-network-security-overview.md)

Azure Private Link kan du ansluta till din arbetsyta med hjälp av en privat slutpunkt. Den privata slutpunkten är en uppsättning privata IP-adresser i ditt virtuella nätverk. Du kan sedan begränsa åtkomsten till din arbetsyta så att den endast sker över de privata IP-adresserna. Private Link minskar risken för data exfiltrering. Mer information om privata slutpunkter finns i [Azure Private Link](../private-link/private-link-overview.md) artikeln.

> [!IMPORTANT]
> Azure Private Link påverkar inte Azure-kontrollplanet (hanteringsåtgärder), till exempel att ta bort arbetsytan eller hantera beräkningsresurser. Det kan till exempel vara att skapa, uppdatera eller ta bort ett beräkningsmål. Dessa åtgärder utförs över det offentliga Internet som vanligt. Dataplansåtgärder, till exempel att använda Azure Machine Learning-studio, API:er (inklusive publicerade pipelines) eller SDK använder den privata slutpunkten.
>
> Du kan stöta på problem med att försöka komma åt den privata slutpunkten för din arbetsyta om du använder Mozilla Firefox. Det här problemet kan vara relaterat till DNS över HTTPS i Mozilla. Vi rekommenderar att Microsoft Edge eller Google Chrome som en lösning.

## <a name="prerequisites"></a>Förutsättningar

* Om du planerar att använda en privat länkaktiverad arbetsyta med en kund hanterad nyckel måste du begära den här funktionen med hjälp av en supportbegäran. Mer information finns i [Hantera och öka kvoter.](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases)

* Du måste ha ett befintligt virtuellt nätverk för att skapa den privata slutpunkten i. Du måste också [inaktivera nätverksprinciper för privata slutpunkter innan](../private-link/disable-private-endpoint-network-policy.md) du lägger till den privata slutpunkten.
## <a name="limitations"></a>Begränsningar

* Det finns Azure Machine Learning en arbetsyta med privat länk i de Azure Government regionerna.
* Om du aktiverar offentlig åtkomst för en arbetsyta som skyddas med privat länk och använder Azure Machine Learning-studio via det offentliga Internet kan vissa funktioner som designern inte komma åt dina data. Det här problemet inträffar när data lagras på en tjänst som skyddas bakom det virtuella nätverket. Till exempel ett Azure Storage konto.

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Skapa en arbetsyta som använder en privat slutpunkt

Använd någon av följande metoder för att skapa en arbetsyta med en privat slutpunkt. Var och en av dessa __metoder kräver ett befintligt virtuellt nätverk:__

> [!TIP]
> Om du vill skapa en arbetsyta, en privat slutpunkt och ett virtuellt nätverk samtidigt kan du gå till Använda en [Azure Resource Manager-mall](how-to-create-workspace-template.md)för att skapa en arbetsyta för Azure Machine Learning .

# <a name="python"></a>[Python](#tab/python)

I Azure Machine Learning Python SDK finns klassen [PrivateEndpointConfig,](/python/api/azureml-core/azureml.core.privateendpointconfig) som kan användas med [Workspace.create()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) för att skapa en arbetsyta med en privat slutpunkt. Den här klassen kräver ett befintligt virtuellt nätverk.

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.create(name='myworkspace',
    subscription_id='<my-subscription-id>',
    resource_group='myresourcegroup',
    location='eastus2',
    private_endpoint_config=pe,
    private_endpoint_auto_approval=True,
    show_output=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure CLI-tillägget för maskininlärning](reference-azure-machine-learning-cli.md) tillhandahåller [kommandot az ml workspace create.](/cli/azure/ext/azure-cli-ml/ml/workspace#ext_azure_cli_ml_az_ml_workspace_create) Följande parametrar för det här kommandot kan användas för att skapa en arbetsyta med ett privat nätverk, men det kräver ett befintligt virtuellt nätverk:

* `--pe-name`: Namnet på den privata slutpunkt som skapas.
* `--pe-auto-approval`: Om privata slutpunktsanslutningar till arbetsytan ska godkännas automatiskt.
* `--pe-resource-group`: Resursgruppen som den privata slutpunkten ska skapas i. Måste vara samma grupp som innehåller det virtuella nätverket.
* `--pe-vnet-name`: Det befintliga virtuella nätverk som den privata slutpunkten ska skapas i.
* `--pe-subnet-name`: Namnet på undernätet som den privata slutpunkten ska skapas i. Standardvärdet är `default`.

Dessa parametrar är utöver andra obligatoriska parametrar för create-kommandot. Följande kommando skapar till exempel en ny arbetsyta i regionen USA, västra med hjälp av en befintlig resursgrupp och ett VNet:

```azurecli
az ml workspace create -r myresourcegroup \
    -l westus \
    -n myworkspace \
    --pe-name myprivateendpoint \
    --pe-auto-approval \
    --pe-resource-group myresourcegroup \
    --pe-vnet-name myvnet \
    --pe-subnet-name mysubnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

På __fliken__ Nätverk i Azure Machine Learning-studio kan du konfigurera en privat slutpunkt. Det kräver dock ett befintligt virtuellt nätverk. Mer information finns i [Skapa arbetsytor i portalen](how-to-manage-workspace.md).

---

## <a name="add-a-private-endpoint-to-a-workspace"></a>Lägga till en privat slutpunkt till en arbetsyta

Använd någon av följande metoder för att lägga till en privat slutpunkt i en befintlig arbetsyta:

> [!WARNING]
>
> Om du har några befintliga beräkningsmål som är associerade med den här arbetsytan och de inte ligger bakom samma virtuella nätverk som den privata slutpunkten skapas i, fungerar de inte.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
from azureml.core import PrivateEndPointConfig

pe = PrivateEndPointConfig(name='myprivateendpoint', vnet_name='myvnet', vnet_subnet_name='default')
ws = Workspace.from_config()
ws.add_private_endpoint(private_endpoint_config=pe, private_endpoint_auto_approval=True, show_output=True)
```

Mer information om de klasser och metoder som används i det här exemplet finns [i PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) och [Workspace.add_private_endpoint](/python/api/azureml-core/azureml.core.workspace(class)#add-private-endpoint-private-endpoint-config--private-endpoint-auto-approval-true--location-none--show-output-true--tags-none-).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure CLI-tillägget för maskininlärning](reference-azure-machine-learning-cli.md) tillhandahåller [kommandot az ml workspace private-endpoint add.](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint#ext_azure_cli_ml_az_ml_workspace_private_endpoint_add)

```azurecli
az ml workspace private-endpoint add -w myworkspace  --pe-name myprivateendpoint --pe-auto-approval true --pe-vnet-name myvnet
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Från arbetsytan Azure Machine Learning i portalen väljer du Privata __slutpunktsanslutningar__ och sedan __+ Privat slutpunkt.__ Använd fälten för att skapa en ny privat slutpunkt.

* När du väljer __Region__ väljer du samma region som ditt virtuella nätverk. 
* När du __väljer Resurstyp__ använder du __Microsoft.MachineLearningServices/workspaces__. 
* Ange namnet __på__ din arbetsyta för Resurs.

Välj slutligen Skapa __för__ att skapa den privata slutpunkten.

---

## <a name="remove-a-private-endpoint"></a>Ta bort en privat slutpunkt

Använd någon av följande metoder för att ta bort en privat slutpunkt från en arbetsyta:

# <a name="python"></a>[Python](#tab/python)

Använd [Workspace.delete_private_endpoint_connection för](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) att ta bort en privat slutpunkt.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
# get the connection name
_, _, connection_name = ws.get_details()['privateEndpointConnections'][0]['id'].rpartition('/')
ws.delete_private_endpoint_connection(private_endpoint_connection_name=connection_name)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure CLI-tillägget för maskininlärning](reference-azure-machine-learning-cli.md) tillhandahåller [kommandot az ml workspace private-endpoint delete.](/cli/azure/ext/azure-cli-ml/ml/workspace/private-endpoint#ext_azure_cli_ml_az_ml_workspace_private_endpoint_delete)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Från arbetsytan Azure Machine Learning i portalen väljer du __Privata slutpunktsanslutningar__ och sedan den slutpunkt som du vill ta bort. Välj slutligen Ta __bort__.

---

## <a name="using-a-workspace-over-a-private-endpoint"></a>Använda en arbetsyta via en privat slutpunkt

Eftersom kommunikation till arbetsytan endast tillåts från det virtuella nätverket måste alla utvecklingsmiljöer som använder arbetsytan vara medlemmar i det virtuella nätverket. Till exempel en virtuell dator i det virtuella nätverket.

> [!IMPORTANT]
> För att undvika tillfälligt avbrott i anslutningen rekommenderar Microsoft att du rensar DNS-cachen på datorer som ansluter till arbetsytan när du har Private Link. 

Information om Azure Virtual Machines finns i [Virtual Machines dokumentationen](../virtual-machines/index.yml).

## <a name="enable-public-access"></a>Aktivera offentlig åtkomst

I vissa situationer kanske du vill tillåta att någon ansluter till din skyddade arbetsyta via en offentlig slutpunkt i stället för via det virtuella nätverket. När du har konfigurerat en arbetsyta med en privat slutpunkt kan du välja att aktivera offentlig åtkomst till arbetsytan. Om du gör det tas inte den privata slutpunkten bort. All kommunikation mellan komponenter bakom det virtuella nätverket är fortfarande skyddad. Den ger endast offentlig åtkomst till arbetsytan, förutom privat åtkomst via det virtuella nätverket.

> [!WARNING]
> När du ansluter via den offentliga slutpunkten kommer vissa funktioner i Studio inte att komma åt dina data. Det här problemet inträffar när data lagras på en tjänst som skyddas bakom det virtuella nätverket. Till exempel ett Azure Storage konto. Observera också att funktionerna i Jupyter/JupyterLab/RStudio och att notebook-datorer som körs inte fungerar.

Använd följande steg för att aktivera offentlig åtkomst till en privat länkaktiverad arbetsyta:

# <a name="python"></a>[Python](#tab/python)

Använd [Workspace.delete_private_endpoint_connection för](/python/api/azureml-core/azureml.core.workspace(class)#delete-private-endpoint-connection-private-endpoint-connection-name-) att ta bort en privat slutpunkt.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
ws.update(allow_public_access_when_behind_vnet=True)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Azure CLI-tillägget för maskininlärning](reference-azure-machine-learning-cli.md) tillhandahåller [kommandot az ml workspace update.](/cli/azure/ext/azure-cli-ml/ml/workspace#ext_azure_cli_ml_az_ml_workspace_update) Om du vill aktivera offentlig åtkomst till arbetsytan lägger du till parametern `--allow-public-access true` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

Det finns för närvarande inget sätt att aktivera den här funktionen med hjälp av portalen.

---


## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skyddar din Azure Machine Learning finns i artikeln Virtual network isolation and privacy overview (Virtuell [nätverksisolering och sekretessöversikt).](how-to-network-security-overview.md)

* Om du planerar att använda en anpassad DNS-lösning i ditt virtuella nätverk kan du se [hur du använder en arbetsyta med en anpassad DNS-server.](how-to-custom-dns.md)
