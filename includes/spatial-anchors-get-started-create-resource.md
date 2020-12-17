---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 596b73f8fb205b6a5681fecf3d00fd2a67c1f59f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628759"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öppna <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

I den vänstra rutan väljer du **skapa en resurs**.

Använd sökrutan för att söka efter **avstånds ankare**.

![Skärm bild som visar resultatet av en sökning efter avstånds ankare.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Välj **spatiala ankare** och välj sedan **skapa**.

Gör följande i **konto fönstret spatiala ankare** :

* Ange ett unikt resurs namn genom att använda vanliga alfanumeriska tecken.
* Välj den prenumeration som du vill koppla resursen till.
* Skapa en resurs grupp genom att välja **Skapa ny**. Ge den namnet **myResourceGroup** och välj sedan **OK**.

  [!INCLUDE [resource group intro text](resource-group.md)]

* Välj en plats (region) där resursen ska placeras.
* Välj **Ny** för att börja skapa resursen.

![Skärm bild av fönstret spatiala ankare för att skapa en resurs.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

När resursen har skapats visar Azure Portal att distributionen är slutförd.

![Skärm bild som visar att resurs distributionen är klar.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Välj **Gå till resurs**. Nu kan du Visa resurs egenskaperna.

Kopiera resursens **konto-ID-** värde till en text redigerare för senare användning.

![Skärm bild av fönstret resurs egenskaper.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Kopiera också resursens **konto** värde till en text redigerare för senare användning.

![Skärm bild som visar resursens konto värde.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Under **Inställningar** väljer du **nyckel**. Kopiera värdet för **primär nyckel** , **konto nyckel**, till en text redigerare för senare användning.

![Skärm bild av fönstret nycklar för kontot.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja med att förbereda din miljö för Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. När du har loggat in använder du kommandot [AZ Account set](/cli/azure/account#az_account_set) för att välja den prenumeration där du vill ställa in kontot för spatiala ankare:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Kör kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp eller Använd en befintlig resurs grupp:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Du kan visa dina aktuella ankare för spatialdata för en resurs grupp med hjälp av kommandot [AZ spatial-ankare – konto lista](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list) :

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Du kan också Visa kontona för spatiala ankare för din prenumeration:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Kör kommandot [AZ spatial-ankare – konto Create](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) för att skapa ditt konto för spatiala ankare:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Visa resurs egenskaperna genom att använda kommandot [AZ spatial-ankare – konto show](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show) :

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Kopiera resurs **kontots ID-** värde och resurs **kontots domän** värde till en text redigerare för senare användning.

1. Kör kommandot [AZ spatial-ankare – konto nyckel Visa](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) för att hämta dina primära och sekundära nycklar:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Kopiera nyckel värden till en text redigerare för senare användning.

   Om du behöver återskapa nycklar använder du kommandot [AZ spatial-ankare-konto nyckel förnya](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew) :

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

Du kan ta bort ett konto med hjälp av kommandot [AZ spatial-ankare-Account Delete](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete) :

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Börja med att förbereda din miljö för Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Även om **AZ. MixedReality** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. När du har loggat in använder du cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) för att välja den prenumeration där du vill ställa in kontot för spatiala ankare:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Kör cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) för att skapa en resurs grupp eller Använd en befintlig resurs grupp:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Du kan visa dina aktuella ankare för spatialdata för en resurs grupp med hjälp av cmdleten [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) :

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Du kan också Visa kontona för spatiala ankare för din prenumeration:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Kör cmdleten [New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) för att skapa ditt konto för spatial ankare:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Visa resurs egenskaperna med hjälp av cmdleten [Get-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/get-azspatialanchorsaccount) :

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Kopiera egenskap svärdet **accountId** och egenskapen **accountDomain** till en text redigerare för senare användning.

1. Kör cmdleten [Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) för att hämta dina primära och sekundära nycklar:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Kopiera nyckel värden till en text redigerare för senare användning.

   Om du behöver återskapa nycklar använder du cmdleten [New-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey) :

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

Du kan ta bort ett konto med hjälp av cmdleten [Remove-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount) :

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
