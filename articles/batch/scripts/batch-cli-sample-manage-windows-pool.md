---
title: Exempel på Azure CLI-skript – Windows-pool i batch
description: Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Windows-beräkningsnoder i Azure Batch.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: fb18f9d8777c17d31a3ab246603df0d9fa162467
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100947"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>CLI-exempel: Skapa och hantera en Windows-pool i Azure Batch

Det här skriptet visar några av de kommandon som är tillgängliga i Azure CLI för att skapa och hantera en pool med Windows-beräkningsnoder i Azure Batch. En Windows-pool kan konfigureras på två sätt, med antingen en konfiguration för molntjänster eller en konfiguration av virtuell dator. Det här exemplet visar hur du skapar en Windows-pool med konfigurationen för molntjänster.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- I den här självstudien krävs version 2.0.20 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

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
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion. |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Skapar en pool med beräkningsnoder.  |
| [az batch pool set](/cli/azure/batch/pool#az-batch-pool-set) | Uppdaterar egenskaperna för en pool.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-enable) | Aktiverar automatisk skalning på en pool och tillämpar en formel.  |
| [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show) | Visar egenskaperna för en pool.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az-batch-pool-autoscale-disable) | Inaktiverar automatisk skalning på en pool. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
