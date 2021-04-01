---
title: PowerShell – skapa en anpassad roll i ett labb i Azure DevTest Labs
description: Den här artikeln innehåller ett Azure PowerShell-skript som skapar en anpassad roll i ett labb i Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3757d6c16a762a0f76f4718f117da93322af074c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021205"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Använd PowerShell för att skapa en anpassad roll i ett labb i Azure DevTest Labs

Det här exemplet på PowerShell-skriptet skapar en anpassad roll som ska användas i ett labb i Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Ett labb**. Skriptet kräver att du har ett befintligt labb. 

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Kommentarer |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Hämtar åtgärder för en Azure-resurs-Provider som kan skyddas med Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Visar en lista över alla Azure-roller som är tillgängliga för tilldelning. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Skapar en anpassad roll. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare Azure Lab Services PowerShell-skript exempel finns i [Azure Lab Services PowerShell-exempel](../samples-powershell.md).
