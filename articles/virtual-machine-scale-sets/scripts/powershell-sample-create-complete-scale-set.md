---
title: Azure PowerShell exempel – skapa en fullständig skalnings uppsättning för virtuella datorer
description: Det här skriptet skapar en skalnings uppsättning för virtuella datorer som kör Windows Server 2016, där enskilda resurser konfigureras och skapas.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: d37f5f624459db6bc336884987a16c60503492a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078511"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Skapa en fullständig VM-skalningsuppsättning med PowerShell

Det här skriptet skapar en VM-skalningsuppsättning som kör Windows Server 2016. Enskilda resurser konfigureras och skapas i stället för att använda de [inbyggda alternativen för att skapa resurser som finns här i New-AzVmss](powershell-sample-create-simple-scale-set.md). När skriptet har körts får du åtkomst till de virtuella datorinstanserna via RDP.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Rensa distribution
Kör följande kommando för att ta bort resursgruppen, skalningsuppsättningen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt nätverk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Skapar en klientdels-IP-konfiguration för en lastbalanserare. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Skapar en konfiguration för serverdelsadresspool för en lastbalanserare. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Skapar en inkommande NAT-regelkonfiguration för en lastbalanserare. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Skapar en lastbalanserare. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Skapar en avsökningskonfiguration för en lastbalanserare. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Skapar en regelkonfiguration för en lastbalanserare. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | Uppdatera lastbalanseraren med den angivna informationen. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | Skapa en IP-konfiguration för skalningsuppsättningens VM-instanser. VM-instanser är anslutna till lastbalanserarens serverdelspool, NAT-pool och undernät för virtuellt nätverk. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | Skapar en skalningsuppsättningskonfiguration. Den här konfigurationen innehåller information såsom antal VM-instanser att skapa, SKU för VM (storlek) och uppgraderingsläge för principer. Ytterligare cmdleter läggs till konfigurationen och används vid skapande av skalningsuppsättning. |
| [Set-AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definiera avbildningen som ska användas för VM-instanser och lägg till det i skalningsuppsättningskonfig. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | Definiera autentiseringsuppgifterna för administrativa användarnamn och lösenord samt VM-namnprefix. Lägg till dessa värden i skalningsuppsättningskonfig. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | Lägg till ett virtuellt nätverkssgränssnitt i VM-instansen baserat på IP-konfigurationen. Lägg till dessa värden i skalningsuppsättningskonfig. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | Skapa skaluppsättningen baserat på informationen i konfigurationen för skalningsuppsättning. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |

## <a name="next-steps"></a>Nästa steg
Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).
