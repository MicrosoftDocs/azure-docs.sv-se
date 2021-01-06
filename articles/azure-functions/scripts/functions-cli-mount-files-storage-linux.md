---
title: Montera en fil resurs i en python Function-app – Azure CLI
description: Skapa en server lös python Function-app och montera en befintlig fil resurs med hjälp av Azure CLI.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 89cf1422c70a49497a1bc43e42248612e0c8dd83
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97930913"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Montera en fil resurs i en python Function-app med hjälp av Azure CLI

Den här Azure Functions exempel skriptet skapar en Function-app och skapar en resurs i Azure Files. Den monterar resursen så att dina funktioner kan komma åt data.  

>[!NOTE]
>Function-appen som skapats körs på python version 3,7. Azure Functions [stöder också python-versionerna 3,6 och 3,8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure Function-app med hjälp av [förbruknings planen](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Konfigurerar ett Azure Storage-konto. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Skapar en Azure Files resurs i lagrings kontot. | 
| [Skapa AZ Storage Directory Create](/cli/azure/storage/directory#az-storage-directory-create) | Skapar en katalog i resursen. |
| [AZ webapp config Storage – konto Add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Monterar resursen till Function-appen. |
| [AZ webapp config Storage – konto lista](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Visar fil resurser som är monterade på Function-appen. | 

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
