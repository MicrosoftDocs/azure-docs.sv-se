---
title: Tagga en virtuell dator med hjälp av PowerShell
description: Lär dig mer om att tagga en virtuell dator med hjälp av PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: bf13d5c0caeb0bf31a383cd23155a6856c81c53b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897399"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Tagga en virtuell dator i Azure med hjälp av PowerShell

Den här artikeln beskriver hur du taggar en virtuell dator i Azure med hjälp av PowerShell. Taggar är användardefinierade nyckel/värde-par som kan placeras direkt på en resurs eller en resurs grupp. Azure har för närvarande stöd för upp till 50 Taggar per resurs och resurs grupp. Taggar kan placeras på en resurs vid tidpunkten för skapandet eller läggs till i en befintlig resurs. Om du vill tagga en virtuell dator med hjälp av Azure CLI kan du läsa om [att tagga en virtuell dator i Azure med hjälp av Azure CLI](tag-cli.md).

Använd `Get-AzVM` cmdleten för att visa den aktuella listan över taggar för den virtuella datorn.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Om den virtuella datorn redan innehåller taggar visas alla Taggar i list format.

Använd kommandot för att lägga till taggar `Set-AzResource` . När du uppdaterar Taggar via PowerShell uppdateras taggarna som helhet. Om du lägger till en tagg till en resurs som redan har taggar, måste du ta med alla Taggar som du vill placera i resursen. Nedan visas ett exempel på hur du lägger till ytterligare taggar till en resurs via PowerShell-cmdletar.

Tilldela alla aktuella taggar för den virtuella datorn till `$tags` variabeln med hjälp av `Get-AzResource` egenskapen och `Tags` .

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Om du vill se de aktuella taggarna skriver du variabeln.

```azurepowershell-interactive
$tags
```

Så här kan utdata se ut:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

I följande exempel lägger vi till en tagg som kallas `Location` med värdet `myLocation` . Använd `+=` för att lägga till det nya nyckel/värde-paret i `$tags` listan.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Används `Set-AzResource` för att ställa in alla Taggar som definierats i variabeln *$Tags* på den virtuella datorn.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Används `Get-AzResource` för att visa alla Taggar på resursen.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

Utdata bör se ut ungefär så här, som nu innehåller den nya taggen:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```

### <a name="next-steps"></a>Nästa steg

- Läs mer om att tagga dina Azure-resurser i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md) och [använda taggar för att ordna dina Azure-resurser](../azure-resource-manager/management/tag-resources.md).
- Information om hur taggar kan hjälpa dig att hantera din användning av Azure-resurser finns i [förstå din Azure-faktura](../cost-management-billing/understand/review-individual-bill.md).
