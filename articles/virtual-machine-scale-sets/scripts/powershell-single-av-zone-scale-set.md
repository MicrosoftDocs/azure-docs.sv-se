---
title: Azure PowerShell exempel – skalnings uppsättning med en zon
description: Det här skriptet skapar en VM-skalningsuppsättning som kör Windows Server 2016 i en enda tillgänglighetszon.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: availability
ms.date: 04/05/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: dc76798ff97aa49d358b639557255b41872bab4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89070929"
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Skapa en VM-skalningsuppsättning med en zon med PowerShell
Det här skriptet skapar en VM-skalningsuppsättning som kör Windows Server 2016 i en enda tillgänglighetszon. När du kört skriptet har du åtkomst till den virtuella datorn över RDP.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Skapar VM-skalningsuppsättningen och alla stödresurser, inklusive virtuellt nätverk, lastbalansering och NAT-regler. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Hämtar information om en VM-skalningsuppsättning. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Lägger till ett virtuellt datortillägg för att det anpassade skriptet ska installera ett grundläggande webbprogram. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Uppdaterar modellen för VM-skalningsuppsättningen för att använda det virtuella datortillägget. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Hämtar information om den offentliga IP-adress som används av lastbalanseraren. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

