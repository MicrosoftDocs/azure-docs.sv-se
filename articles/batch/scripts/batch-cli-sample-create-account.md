---
title: Exempel på Azure CLI-skript – Skapa Batch-konto – Batch-tjänst
description: Det här skriptet skapar ett Azure Batch-konto i Batch-tjänstläget och visar hur du ställer frågor eller uppdaterar olika egenskaper för kontot.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3d978cf56e92abf9824c116e51f94d53bbe14bbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768362"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-exempel: Skapa ett Batch-konto i Batch-tjänstläge

Det här skriptet skapar ett Azure Batch-konto i Batch-tjänstläget och visar hur du ställer frågor eller uppdaterar olika egenskaper för kontot. När du skapar ett Batch-konto i standardläget för Batch-tjänsten tilldelas dess beräkningsnoder internt av Batch-tjänsten. Tilldelade beräkningsnoder regleras av en separat vCPU-kvort (kärna) och kontot kan autentiseras antingen via autentiseringsuppgifter för delad nyckel eller en Azure Active Directory-token.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här självstudien kräver version 2.0.20 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

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
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Skapar ett lagringskonto. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Uppdaterar egenskaperna för Batch-kontot.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | Hämtar information om det angivna Batch-kontot.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | Hämtar åtkomstnycklar för det angivna Batch-kontot.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
