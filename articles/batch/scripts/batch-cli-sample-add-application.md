---
title: Exempel på Azure CLI-skript – Lägga till ett program i Batch
description: Det här exempel skriptet visar hur du lägger till ett program som ska användas med en Azure Batch-pool eller en aktivitet.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 414a401168b1b378ed33f4904607243de0267d22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94565814"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI-exempel: Lägga till ett program i ett Azure Batch-konto

Det här skriptet visar hur du lägger till ett program som ska användas med en pool eller uppgift i Azure Batch. Konfigurera ett program som ska läggas till i Batch-kontot genom att paketera den körbara filen tillsammans med eventuella beroenden i en zip-fil. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2.0.20 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon.
Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Skapar ett lagringskonto. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Skapar ett program.  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Lägger till ett programpaket i det angivna programmet.  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Uppdaterar egenskaperna för ett program.  |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
