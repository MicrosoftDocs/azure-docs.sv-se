---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6d6c4a019e9b0b9bd4ed52990fa8b59e939026f1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "106072850"
---
I den här snabb starten används ett i förväg skapade Azure Key Vault. Du kan skapa ett nyckel valv genom att följa stegen i snabb starten för [Azure CLI](../articles/key-vault/general/quick-create-cli.md), [Azure PowerShell snabb start](../articles/key-vault/general/quick-create-powershell.md)eller [Azure Portal snabb start](../articles/key-vault/general/quick-create-portal.md). 

Alternativt kan du bara köra kommandona för Azure CLI eller Azure PowerShell nedan.

> [!Important]
> Varje nyckel valv måste ha ett unikt namn. Ersätt <ditt-unika-nyckel-valv> med namnet på nyckel valvet i följande exempel.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---