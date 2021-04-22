---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 2c85e26d5a9115b00621c4099e3ed36afb224e3f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880054"
---
## <a name="create-a-spatial-anchors-resource"></a>Skapa en Spatial Anchors-resurs

### <a name="portal"></a>[Portal](#tab/azure-portal)

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.

Välj Skapa en resurs **i den vänstra rutan.**

Använd sökrutan för att söka efter **Spatial Anchors**.

![Skärmbild som visar resultatet av en sökning efter Spatial Anchors.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Välj **Spatial Anchors** och välj sedan **Skapa.**

I **fönstret Spatial Anchors konto** gör du följande:

* Ange ett unikt resursnamn med hjälp av vanliga alfanumeriska tecken.
* Välj den prenumeration som du vill koppla resursen till.
* Skapa en resursgrupp genom att välja **Skapa ny**. Ge den **namnet myResourceGroup** och välj **sedan OK.**

  [!INCLUDE [resource group intro text](resource-group.md)]

* Välj en plats (region) där du vill placera resursen.
* Välj **Ny** för att börja skapa resursen.

![Skärmbild av fönstret Spatial Anchors för att skapa en resurs.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

När resursen har skapats visar Azure Portal att distributionen är klar.

![Skärmbild som visar att resursdistributionen är klar.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Välj **Gå till resurs**. Nu kan du visa resursegenskaperna.

Kopiera resursens **konto-ID-värde** till en textredigerare för senare användning.

![Skärmbild av fönstret resursegenskaper.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Kopiera också resursens **kontodomänvärde till** en textredigerare för senare användning.

![Skärmbild som visar resursens kontodomänvärde.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Under **Inställningar** väljer du **Nyckel.** Kopiera värdet **för Primärnyckel,** **Kontonyckel,** till en textredigerare för senare användning.

![Skärmbild av fönstret Nycklar för kontot.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Börja med att förbereda din miljö för Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. När du har loggat in använder du [kommandot az account set](/cli/azure/account#az_account_set) för att välja den prenumeration där du vill konfigurera kontot för spatiala fästpunkter:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Kör kommandot [az group create](/cli/azure/group#az_group_create) för att skapa en resursgrupp eller använd en befintlig resursgrupp:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Du kan visa dina aktuella spatiala fästpunkter för en resursgrupp med kommandot [az spatial-anchors-account](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_list) list:

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Du kan också visa kontona för spatiala fästpunkter för din prenumeration:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Kör kommandot [az spatial-anchors-account create för](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_create) att skapa ditt spatiala fästpunktskonto:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Visa resursegenskaperna med kommandot [az spatial-anchors-account show:](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_show)

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Kopiera värdet för **resurskonto-ID** och **resurskontots domänvärde** till en textredigerare för senare användning.

1. Kör kommandot [az spatial-anchors-account key show för](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_show) att hämta dina primära och sekundära nycklar:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Kopiera nyckelvärdena till en textredigerare för senare användning.

   Om du behöver återskapa nycklar använder du kommandot [az spatial-anchors-account key renew:](/cli/azure/spatial-anchors-account/key#az_spatial_anchors_account_key_renew)

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

Du kan ta bort ett konto med kommandot [az spatial-anchors-account delete:](/cli/azure/spatial-anchors-account#az_spatial_anchors_account_delete)

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Börja med att förbereda din miljö för Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> När **PowerShell-modulen Az.MixedReality** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. När du har loggat in använder du cmdleten [Set-AzContext](/powershell/module/az.accounts/set-azcontext) för att välja den prenumeration där du vill konfigurera kontot för spatiala fästpunkter:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Kör [cmdleten New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) för att skapa en resursgrupp eller använd en befintlig resursgrupp:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Du kan visa dina aktuella spatiala fästpunkter-konton för en resursgrupp med hjälp av cmdleten [Get-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/get-azspatialanchorsaccount)

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Du kan också visa kontona för spatiala fästpunkter för din prenumeration:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Kör [cmdleten New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) för att skapa ditt spatiala fästpunktskonto:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Visa resursegenskaperna med hjälp av cmdleten [Get-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/get-azspatialanchorsaccount)

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Kopiera värdet **accountId för** egenskapen och värdet för **egenskapskontotDomän** till en textredigerare för senare användning.

1. Kör [cmdleten Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) för att hämta dina primära och sekundära nycklar:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Kopiera nyckelvärdena till en textredigerare för senare användning.

   Om du behöver återskapa nycklar använder du cmdleten [New-AzSpatialAnchorsAccountKey:](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey)

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

Du kan ta bort ett konto med cmdleten [Remove-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount)

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
