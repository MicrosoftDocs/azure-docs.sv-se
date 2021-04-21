---
title: Azure CLI – Skapa och verifiera en virtuell dator i ett labb
description: Det här Azure CLI-skriptet skapar en virtuell dator i ett labb och verifierar att den är tillgänglig.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 779ddee7da4248ce11b906c2a4736fe7851603cf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786755"
---
# <a name="use-azure-cli-to-create-and-verify-availability-of-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Använd Azure CLI för att skapa och verifiera tillgängligheten för en virtuell dator i ett labb i Azure DevTest Labs

Det här Azure CLI-skriptet skapar en virtuell dator i ett labb. Den virtuella datorn skapas baserat på en marknadsplatsbild med ssh-autentisering. Skriptet kontrollerar sedan att den virtuella datorn är tillgänglig för användning. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/create-verify-virtual-machine-in-lab/create-verify-virtual-machine-in-lab.sh "Create and verify availability of a VM")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az lab vm create](/cli/azure/lab/vm#az_lab_vm_create) | Skapar en virtuell dator i ett labb. |
| [az lab vm show](/cli/azure/lab/vm#az_lab_vm_show) | Visar status för den virtuella datorn i ett labb. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare Azure Lab Services CLI-skriptexempel finns i [Azure Lab Services CLI-exempel](../samples-cli.md).
