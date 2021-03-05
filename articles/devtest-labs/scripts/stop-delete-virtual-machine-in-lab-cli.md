---
title: Azure CLI – stoppa och ta bort en virtuell dator i ett labb
description: Den här artikeln innehåller ett Azure CLI-skript som stoppar och tar bort en virtuell dator i ett labb i Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198226"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Använd Azure CLI för att stoppa och ta bort en virtuell dator i ett labb i Azure DevTest Labs

Det här Azure CLI-skriptet stoppar och tar bort en virtuell dator i ett labb. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | Stoppar en virtuell dator i ett labb. Den här åtgärden tar ett tag att slutföra. |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | Tar bort en virtuell dator i ett labb. Den här åtgärden tar ett tag att slutföra. |


## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure Lab Services CLI-skriptexempel finns i [Azure Lab Services CLI-exempel](../samples-cli.md).
