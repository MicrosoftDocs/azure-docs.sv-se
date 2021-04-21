---
title: Montera en filresurs till en Python-funktionsapp – Azure CLI
description: Skapa en serverlös Python-funktionsapp och montera en befintlig filresurs med hjälp av Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d0037cea24b1989c4f7a4d2ddd6bf3f8f7e812b3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762289"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Montera en filresurs till en Python-funktionsapp med Hjälp av Azure CLI

Den Azure Functions exempelskriptet skapar en funktionsapp och en resurs i Azure Files. De monterar resursen så att dina funktioner kan komma åt data.  

>[!NOTE]
>Funktionsappen som skapades körs i Python version 3.7. Azure Functions stöder [även Python-versionerna 3.6 och 3.8.](../functions-reference-python.md#python-version)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en funktionsapp Azure Functions med [förbrukningsplanen](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | Konfigurerar ett Azure Storage-konto. |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | Skapar en funktionsapp. |
| [az storage share create](/cli/azure/storage/share#az_storage_share_create) | Skapar en Azure Files resurs i lagringskontot. | 
| [az storage directory create](/cli/azure/storage/directory#az_storage_directory_create) | Skapar en katalog i resursen. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add) | Monterar resursen till funktionsappen. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_list) | Visar filresurser monterade i funktionsappen. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
