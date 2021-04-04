---
title: Dirigera trafik via NVA-Azure PowerShell skript exempel
description: Azure PowerShell skript exempel – väg trafik via en brand vägg NVA.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0bc7ddde7964ecdd003b951a8562a840b8b88e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223218"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>Dirigera trafik via en virtuell nätverksinstallation – skriptexempel

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Det skapar också en virtuell dator med aktiverad IP-vidarebefordran för att dirigera trafik mellan de två undernäten. När du kört skriptet kan du distribuera nätverksprogramvara, till exempel ett brandväggsprogram, till den virtuella datorn.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/powershell), eller från en lokal installation av PowerShell. Om du använder PowerShell lokalt kräver det här skriptet version 5.4.1 eller senare av Az PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar serverdels- och DMZ-undernät. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress för åtkomst till den virtuella datorn från Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar ett virtuellt nätverksgränssnitt och aktiverar IP-vidarebefordran för det. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp (NSG). |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Skapar NSG-regler som tillåter inkommande HTTP- och HTTPS-portar till den virtuella datorn. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Associerar NSG:erna och routningstabellerna med undernät. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Skapar en routningstabell för alla vägar. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Skapar vägar för att dirigera trafik mellan undernät och Internet via den virtuella datorn. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapar en virtuell dator och kopplar nätverkskortet till den. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för virtuella nätverk finns i [PowerShell-exempel för virtuella nätverk](../powershell-samples.md).