---
title: Azure PowerShell skript exempel – peer två virtuella nätverk | Microsoft Docs
description: Skapa och Anslut två virtuella nätverk i samma region. Använd Azure-skriptet för två peer-virtuella nätverk för att ansluta nätverken till Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63fab50f991a15a38d18dead53d268abca79c811
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2021
ms.locfileid: "98231900"
---
# <a name="peer-two-virtual-networks"></a>Peerkoppla två virtuella nätverk

Det här skriptet skapar och ansluter två virtuella nätverk i samma region via Azure-nätverket. När du har kört skriptet har du en peering mellan två virtuella nätverk.

Om det behövs installerar du Azure PowerShell med hjälp av den instruktion som finns i [Azure PowerShell-guiden](/powershell/azure/)och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, en virtuell dator och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. | 
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)| Skapar ett virtuellt Azure-nätverk och undernät. |
| [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) | Skapar en peering mellan två virtuella nätverk.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).