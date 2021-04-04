---
title: Skapa en Azure-anpassad resurs leverantör med Azure PowerShell
description: Beskriver hur du skapar en Azure-anpassad resurs leverantör med Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91951850"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Snabb start: skapa en Azure-anpassad resurs leverantör med Azure PowerShell

I den här snabb starten får du lära dig hur du skapar en egen Azure-anpassad resurs leverantör med hjälp av PowerShell-modulen [AZ. CustomProviders](/powershell/module/az.customproviders) .

> [!CAUTION]
> Azure-anpassade leverantörer är för närvarande en offentlig för hands version. Förhandsversionen tillhandahålls utan serviceavtal. Den rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps). Om du väljer att använda Cloud Shell, se [Översikt över Azure Cloud Shell](../../cloud-shell/overview.md) för mer information.

> [!IMPORTANT]
> Även om **AZ. CustomProviders** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När den här PowerShell-modulen blir allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../../azure-resource-manager/management/overview.md) med cmdlet: en [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resurs grupp med det angivna namnet och på den angivna platsen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Skapa en anpassad resursprovider

Om du vill skapa eller uppdatera en anpassad resurs leverantör använder du cmdleten [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) som visas i följande exempel.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Hämta manifestet för den anpassade resurs leverantören

Om du vill hämta information om det anpassade resurs leverantörs manifestet använder du cmdleten [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) som visas i följande exempel.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Skapa en Association

Om du vill skapa eller uppdatera en Association använder du cmdleten [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) som visas i följande exempel.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Hämta en Association

Om du vill hämta information om en Association använder du cmdleten [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) som visas i följande exempel.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapas i den här artikeln inte behövs kan du ta bort dem genom att köra följande exempel.

### <a name="delete-an-association"></a>Ta bort en Association

Om du vill ta bort en Association använder du cmdleten [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) . I följande exempel tar vi bort en Association.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Ta bort en anpassad resurs leverantör

Om du vill ta bort en anpassad resurs leverantör använder du cmdleten [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) . I följande exempel tar bort en anpassad resurs leverantör.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Ta bort resursgruppen

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här artikeln finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azures anpassade resurs leverantörer](overview.md).