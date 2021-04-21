---
title: Exempel på Azure CLI-skript – Lägga till ett program i Batch
description: Det här exempelskriptet visar hur du lägger till ett program för användning med Azure Batch en pool eller en uppgift.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 06afb59a76e763c25e943c3be1531372a6bd2aa1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765295"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI-exempel: Lägga till ett program i ett Azure Batch-konto

Det här skriptet visar hur du lägger till ett program som ska användas med en pool eller uppgift i Azure Batch. Konfigurera ett program som ska läggas till i Batch-kontot genom att paketera den körbara filen tillsammans med eventuella beroenden i en zip-fil. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0.20 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

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
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Skapar ett lagringskonto. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Skapar Batch-kontot. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentiserar mot det angivna Batch-kontot för ytterligare CLI-interaktion.  |
| [az batch application create](/cli/azure/batch/application#az_batch-application-create) | Skapar ett program.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch-application-package-create) | Lägger till ett programpaket i det angivna programmet.  |
| [az batch application set](/cli/azure/batch/application#az_batch-application-set) | Uppdaterar egenskaperna för ett program.  |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
