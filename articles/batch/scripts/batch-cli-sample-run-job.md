---
title: Exempel på Azure CLI-skript – Köra ett Batch-jobb
description: Det här skriptet skapar ett Batch-jobb och lägger till en rad uppgifter i jobbet. Det visar även hur du övervakar ett jobb och dess uppgifter.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768177"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI-exempel: Köra ett jobb och uppgifter med Azure Batch

Det här skriptet skapar ett Batch-jobb och lägger till en rad uppgifter i jobbet. Det visar även hur du övervakar ett jobb och dess uppgifter. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här självstudien kräver version 2.0.20 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="example-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Skapar en pool med beräkningsnoder.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Skapar ett Batch-jobb.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Lägger till en aktivitet i angivet Batch-jobb.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Uppdaterar egenskaperna för ett Batch-jobb.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Hämtar information om ett angivet Batch-jobb.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Hämtar information om en uppgift från det angivna Batch-jobbet.  |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).
