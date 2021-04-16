---
title: 'Snabbstart: Skapa en Azure Dedicated HSM med Azure PowerShell'
description: Skapa en Azure Dedicated HSM med Azure PowerShell
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/13/2020
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: d5b6d0399ceb98caf9bdd7bbd725e6d92af0eaa3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537797"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Snabbstart: Skapa en Azure Dedicated HSM med Azure PowerShell

Den här artikeln beskriver hur du kan skapa en Azure Dedicated HSM med hjälp av [PowerShell-modulen Az.DedicatedHsm.](/powershell/module/az.dedicatedhsm)

## <a name="requirements"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Även om **PowerShell-modulen Az.DedicatedHsm** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska debiteras. Välj en specifik prenumeration med hjälp [av cmdleten Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med [hjälp av cmdleten New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med det angivna namnet och på den angivna platsen.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Skapa en dedikerad HSM

Om du vill skapa en dedikerad HSM använder du cmdleten [New-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) I följande exempel skapas en dedikerad HSM i den angivna prenumerationen.

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Skaffa en dedikerad HSM

Om du vill hämta information om en befintlig dedikerad HSM använder du cmdleten [Get-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) I följande exempel hämtar den angivna dedikerade HSM:en.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Uppdatera en dedikerad HSM

Om du vill uppdatera en dedikerad HSM använder du cmdleten [Update-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) I följande exempel uppdateras en dedikerad HSM i den angivna prenumerationen.

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapades i den här artikeln inte behövs kan du ta bort dem genom att köra följande exempel.

### <a name="remove-a-dedicated-hsm"></a>Ta bort en dedikerad HSM

Om du vill ta bort en dedikerad HSM använder du cmdleten [Remove-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) I följande exempel tas den angivna dedikeradE HSM bort.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

> [!CAUTION]
> I följande exempel tas den angivna resursgruppen och alla resurser som ingår i den bort.
> Om det finns resurser utanför omfånget för den här artikeln i den angivna resursgruppen tas de också bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [att Azure Dedicated HSM](overview.md).
