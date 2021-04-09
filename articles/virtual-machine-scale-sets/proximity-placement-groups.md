---
title: Förhands visning av närhets grupper för virtuella dator skalnings uppsättningar
description: Lär dig mer om att skapa och använda närhets placerings grupper för skalnings uppsättningar för virtuella Windows-datorer i Azure.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: proximity-placement-groups
ms.date: 07/01/2019
ms.reviewer: zivr
ms.custom: mimckitt
ms.openlocfilehash: eeb0dd6cd5ce838c0f3a68763c40612038ee82f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933499"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>För hands version: skapa och använda närhets placerings grupper med PowerShell

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera din skalnings uppsättning inom en [närhets placerings grupp](../virtual-machines/co-location.md#proximity-placement-groups).

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.

> [!IMPORTANT]
> Närhets placerings grupper finns för närvarande i offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Närhets placerings grupper är inte tillgängliga i dessa regioner under för hands versionen: **Östra Japan**, östra **Australien** och **Indien, centrala**.


## <a name="create-a-proximity-placement-group"></a>Skapa en närhetsplaceringsgrupp
Skapa en närhets placerings grupp med cmdleten [New-AzProximityPlacementGroup](/powershell/module/az.compute/new-azproximityplacementgroup) . 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>Lista närhets placerings grupper

Du kan visa alla närhets placerings grupper med hjälp av cmdleten [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) .

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning

Skapa en skala i närhets placerings gruppen med hjälp av `-ProximityPlacementGroup $ppg.Id` för att referera till närhets grupp-ID: t när du använder [New-AzVMSS](/powershell/module/az.compute/new-azvmss) för att skapa skalnings uppsättningen.

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

Du kan se instansen i placerings gruppen med hjälp av [Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup).

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>Nästa steg

Du kan också använda [Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) för att skapa närhets grupper.
