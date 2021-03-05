---
title: Azure CLI-skriptexempel – Starta en virtuell dator i ett labb | Microsoft Docs
description: Azure CLI-skriptet startar en virtuell dator i ett labb i Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 8a3308a4e13b82cd90e00b6c25edadf4cc8aa4ee
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198174"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Använd Azure CLI för att starta en virtuell dator i ett labb i Azure DevTest Labs

Det här Azure CLI-skriptet startar en virtuell dator i ett labb. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az lab vm start ](/cli/azure/lab/vm#az-lab-vm-start) | Startar en virtuell dator i ett labb. Den här åtgärden tar ett tag att slutföra. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure Lab Services CLI-skriptexempel finns i [Azure Lab Services CLI-exempel](../samples-cli.md).
