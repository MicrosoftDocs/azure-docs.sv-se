---
title: Skapa en anpassad Azure-resursprovider med Azure PowerShell
description: Beskriver hur du skapar en anpassad Azure-resursprovider med Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c19eb41210b6fba1935a0d158c8240375f4f8f5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533918"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Snabbstart: Skapa en anpassad Azure-resursprovider med Azure PowerShell

I den här snabbstarten lär du dig hur du skapar en egen anpassad Azure-resursprovider med hjälp av PowerShell-modulen [Az.CustomProviders.](/powershell/module/az.customproviders)

> [!CAUTION]
> Anpassade Azure-providers finns för närvarande i offentlig förhandsversion. Förhandsversionen tillhandahålls utan serviceavtal. Den rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar Az PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Mer information om hur du installerar Az PowerShell-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). Om du väljer att använda Cloud Shell finns [mer information i Översikt Azure Cloud Shell](../../cloud-shell/overview.md) information.

> [!IMPORTANT]
> När **PowerShell-modulen Az.CustomProviders** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När den här PowerShell-modulen blir allmänt tillgänglig blir den en del av framtida versioner av Az PowerShell-modulen och blir tillgänglig inbyggt Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska debiteras. Välj en specifik prenumeration med hjälp [av cmdleten Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md) med [hjälp av cmdleten New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resursgrupp med det angivna namnet och på den angivna platsen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Skapa en anpassad resursprovider

Om du vill skapa eller uppdatera en anpassad resursprovider använder du cmdleten [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) enligt följande exempel.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Hämta manifestet för den anpassade resursprovidern

Om du vill hämta information om manifestet för den anpassade resursprovidern använder du cmdleten [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) enligt följande exempel.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Skapa en association

Om du vill skapa eller uppdatera en association använder du cmdleten [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) enligt följande exempel.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Hämta en association

Om du vill hämta information om en association använder du cmdleten [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) enligt följande exempel.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Rensa resurser

Om de resurser som skapades i den här artikeln inte behövs kan du ta bort dem genom att köra följande exempel.

### <a name="delete-an-association"></a>Ta bort en association

Om du vill ta bort en association använder du cmdleten [Remove-AzCustomProviderAssociation.](/powershell/module/az.customproviders/remove-azcustomproviderassociation) I följande exempel tas en association bort.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Ta bort en anpassad resursprovider

Om du vill ta bort en anpassad resursprovider använder du cmdleten [Remove-AzCustomProvider.](/powershell/module/az.customproviders/remove-azcustomprovider) I följande exempel tas en anpassad resursprovider bort.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

> [!CAUTION]
> I följande exempel tas den angivna resursgruppen och alla resurser som ingår i den bort.
> Om det finns resurser utanför omfånget för den här artikeln i den angivna resursgruppen tas de också bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [anpassade Resursproviders i Azure.](overview.md)
