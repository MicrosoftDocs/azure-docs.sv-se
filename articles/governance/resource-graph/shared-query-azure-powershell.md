---
title: 'Snabb start: skapa en delad fråga med Azure PowerShell'
description: I den här snabb starten följer du stegen för att skapa en resurs diagram-delad fråga med hjälp av Azure PowerShell.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99981552"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Snabb start: skapa en resurs diagram delad fråga med hjälp av Azure PowerShell

Den här artikeln beskriver hur du kan skapa en delad Azure-resurs graf-fråga med PowerShell-modulen [AZ. ResourceGraph](/powershell/module/az.resourcegraph) .

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Även om **AZ. ResourceGraph** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Skapa en delad resurs diagrams fråga

Med PowerShell-modulen **AZ. ResourceGraph** som har lagts till i din miljö, är det dags att skapa en delad fråga för resurs diagram. Den delade frågan är ett Azure Resource Manager objekt som du kan ge behörighet till eller köra i Azure Resource Graph Explorer. Frågan sammanfattar antalet resurser grupperade efter _plats_.

1. Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) för att lagra den delade frågan i Azure Resource Graph. Den här resurs gruppen har namnet `resource-graph-queries` och platsen är `westus2` .

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Skapa en delad Azure Resource Graph-fråga med PowerShell-modulen **AZ. ResourceGraph** och cmdleten [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery) :

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

1. Visa en lista över delade frågor i den nya resurs gruppen. Cmdlet: en [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) returnerar en matris med värden.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Om du bara vill ha ett enda delat frågeresultat använder du `Get-AzResourceGraphQuery` `Name` parametern.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den delade frågan och resurs gruppen för resurs grafer från din Azure-miljö kan du göra det med hjälp av följande kommandon:

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

I den här snabb starten har du skapat en resurs graf-delad fråga med hjälp av Azure PowerShell. Om du vill veta mer om det här resurs språket fortsätter du till sidan information om frågespråk.

> [!div class="nextstepaction"]
> [Få mer information om frågespråket](./concepts/query-language.md)