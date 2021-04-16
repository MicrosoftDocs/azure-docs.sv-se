---
title: 'Snabbstart: Skapa en delad fråga med Azure PowerShell'
description: I den här snabbstarten följer du stegen för att skapa en Resource Graph delad fråga med Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9015b6df99bdd6f153194e8f4cbbe7658cf1d6dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535812"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Snabbstart: Skapa en Resource Graph delad fråga med Azure PowerShell

Den här artikeln beskriver hur du kan skapa en Azure Resource Graph delad fråga med hjälp av [PowerShell-modulen Az.ResourceGraph.](/powershell/module/az.resourcegraph)

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > När **PowerShell-modulen Az.ResourceGraph** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten .

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska debiteras. Välj en specifik prenumeration med hjälp [av cmdleten Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Skapa en Resource Graph delad fråga

Nu när **PowerShell-modulen Az.ResourceGraph** har lagts till i din miljö är det dags att skapa en Resource Graph delad fråga. Den delade frågan är ett Azure Resource Manager objekt som du kan bevilja behörighet till eller köra i Azure Resource Graph Explorer. Frågan sammanfattar antalet resurser grupperade efter _plats._

1. Skapa en resursgrupp med [New-AzResourceGroup för](/powershell/module/az.resources/new-azresourcegroup) att lagra Azure Resource Graph delad fråga. Den här resursgruppen heter `resource-graph-queries` och platsen är `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Skapa den Azure Resource Graph delade frågan med hjälp av PowerShell-modulen **Az.ResourceGraph** och [cmdleten New-AzResourceGraphQuery:](/powershell/module/az.resourcegraph/new-azresourcegraphquery)

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Visa en lista över delade frågor i den nya resursgruppen. Cmdleten [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) returnerar en matris med värden.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Om du bara vill få ett enda delat frågeresultat använder du `Get-AzResourceGraphQuery` med dess `Name` parameter.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Resource Graph delad fråga och resursgrupp från Azure-miljön kan du göra det med hjälp av följande kommandon:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en Resource Graph delad fråga med hjälp av Azure PowerShell. Om du vill veta mer Resource Graph språk fortsätter du till sidan med information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)
