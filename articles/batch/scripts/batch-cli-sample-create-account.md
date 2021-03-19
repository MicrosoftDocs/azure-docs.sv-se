---
title: Exempel på Azure CLI-skript – skapa batch-konto – batch-tjänst
description: Det här skriptet skapar ett Azure Batch-konto i Batch-tjänstläget och visar hur du ställer frågor eller uppdaterar olika egenskaper för kontot.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2349b6b373f271a5aa0f169e5a9ebc9f58f6f608
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93076818"
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI-exempel: Skapa ett Batch-konto i Batch-tjänstläge

Det här skriptet skapar ett Azure Batch-konto i Batch-tjänstläget och visar hur du ställer frågor eller uppdaterar olika egenskaper för kontot. När du skapar ett Batch-konto i standardläget för Batch-tjänsten tilldelas dess beräkningsnoder internt av Batch-tjänsten. Tilldelade beräkningsnoder regleras av en separat vCPU-kvort (kärna) och kontot kan autentiseras antingen via autentiseringsuppgifter för delad nyckel eller en Azure Active Directory-token.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- I den här självstudien krävs version 2.0.20 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

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
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto. |
| [az batch account set](/cli/azure/batch/account#az-batch-account-set) | Uppdaterar egenskaperna för Batch-kontot.  |
| [az batch account show](/cli/azure/batch/account#az-batch-account-show) | Hämtar information om det angivna Batch-kontot.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az-batch-account-keys-list) | Hämtar åtkomstnycklar för det angivna Batch-kontot.  |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
