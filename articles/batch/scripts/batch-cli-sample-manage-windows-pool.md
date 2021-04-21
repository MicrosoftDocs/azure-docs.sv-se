---
title: Exempel på Azure CLI-skript – Windows-pool i Batch
description: Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Windows-beräkningsnoder i Azure Batch.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 773699dde9342a4b230a08471a289a56fca7e308
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768223"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI-exempel: Skapa och hantera en Windows-pool i Azure Batch

Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Windows-beräkningsnoder i Azure Batch. En Windows-pool kan konfigureras på två sätt, med antingen en konfiguration för molntjänster eller en konfiguration av virtuell dator. Det här exemplet visar hur du skapar en Windows-pool med konfigurationen för molntjänster.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här självstudien kräver version 2.0.20 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion. |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Skapar en pool med beräkningsnoder.  |
| [az batch pool set](/cli/azure/batch/pool#az_batch_pool_set) | Uppdaterar egenskaperna för en pool.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Aktiverar automatisk skalning på en pool och tillämpar en formel.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Visar egenskaperna för en pool.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Inaktiverar automatisk skalning på en pool. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
