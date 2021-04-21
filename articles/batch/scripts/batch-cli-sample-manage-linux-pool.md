---
title: Exempel på Azure CLI-skript – Linux-pool i Batch
description: Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Linux-beräkningsnoder i Azure Batch.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b5e1bdccefffa7803fbe744e27c1b36ca719560d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768295"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI-exempel: Skapa och hantera en Linux-pool i Azure Batch

Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Linux-beräkningsnoder i Azure Batch.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här självstudien kräver version 2.0.20 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Visar en lista över tillgängliga SKU:er för nodagent och avbildningsinformation.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Skapar en pool med beräkningsnoder.  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | Ändrar storlek på hur många virtuella datorer som körs i den angivna poolen.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Visar egenskaperna för en pool.  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | Visar en lista med alla beräkningsnoder i den angivna poolen.  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | Startar om den angivna beräkningsnoden.  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | Tar bort de listade noderna från den angivna poolen.  |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
