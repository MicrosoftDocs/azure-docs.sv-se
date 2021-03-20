---
title: Skapa en server lös python-Function-app – Azure CLI
description: Skapa en server lös python-Function-app med hjälp av Azure CLI
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: 65ea6513046cd0372720e5a9438941dda2a74877
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934364"
---
# <a name="create-a-serverless-python-function-app-using-azure-cli"></a>Skapa en server lös python-Function-app med Azure CLI

Det här exempelskriptet för Azure Functions skapar en funktionsapp som blir container åt dina funktioner. 

>[!NOTE]
>Function-appen som skapats körs på python version 3,6. Python version 3,7 stöds också av Azure Functions.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure Function-app med hjälp av [förbruknings planen](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-consumption-python/create-function-app-consumption-python.sh "Create an Azure Function on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Konfigurerar ett Azure Storage-konto. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
