---
title: Anslut virtuella nätverk med VNet-peering – Azure PowerShell
description: I den här artikeln får du lära dig hur du ansluter virtuella nätverk med peering av virtuella nätverk med hjälp av Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5214dbe692584c8b1df2007019c8108fd56dfa38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059351"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Ansluta virtuella nätverk med peering för virtuella nätverk med hjälp av PowerShell

Du kan ansluta virtuella nätverk till varandra med peerkoppling. När virtuella nätverk har peerkopplats kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna fanns i samma virtuella nätverk. I den här artikeln kan du se hur du:

* Skapa två virtuella nätverk
* Koppla samman två virtuella nätverk med en peerkoppling
* Distribuera en virtuell dator (VM) till varje virtuellt nätverk
* Kommunicera mellan virtuella datorer

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

Innan du skapar ett virtuellt nätverk måste du skapa en resurs grupp för det virtuella nätverket och alla andra resurser som skapas i den här artikeln. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork1* med adressprefixet *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Skapa en under näts konfiguration med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). I följande exempel skapas en under näts konfiguration med adressprefixet 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Skriv under näts konfigurationen till det virtuella nätverket med [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), som skapar under nätet:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Skapa ett virtuellt nätverk med ett 10.1.0.0/16-adressprefix och ett undernät:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Peerkoppla virtuella nätverk

Skapa en peering med [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). I följande exempel peer- *myVirtualNetwork1* till *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

I utdata som returneras efter att föregående kommando körts, ser du att **PeeringState** har *initierats*. Peer-kopplingen förblir i det *initierade* läget tills du skapar peer-kopplingen från *myVirtualNetwork2* till *myVirtualNetwork1*. Skapa en peering från *myVirtualNetwork2* till *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

I utdata som returneras efter att föregående kommando körts, ser du att **PeeringState** är *ansluten*. Azure ändrade också peering-statusen för *myVirtualNetwork1-myVirtualNetwork2-* peering till *ansluten*. Bekräfta att peering-statusen för *myVirtualNetwork1-myVirtualNetwork2-* peering har ändrats till *ansluten* med [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering).

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Resurser i ett virtuellt nätverk kan inte kommunicera med resurser i det andra virtuella nätverket förrän **PeeringState** för peering i båda de virtuella nätverken är *anslutna*.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa en virtuell dator med [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator med namnet *myVm1* i det virtuella *myVirtualNetwork1* -nätverket. `-AsJob`Alternativet skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg. När du uppmanas till det anger du det användar namn och lösen ord som du vill logga in på den virtuella datorn med.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med senare steg förrän Azure skapar den virtuella datorn och returnerar utdata till PowerShell.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

Du kan ansluta till en offentlig IP-adress för en virtuell dator från Internet. Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den virtuella datorn *myVm1*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbordssession med den virtuella datorn *myVm1* från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returnerades från föregående kommando.

```
mstsc /v:<publicIpAddress>
```

En Remote Desktop Protocol-fil (. RDP) skapas, hämtas till datorn och öppnas. Ange användar namn och lösen ord (du kan behöva välja **fler alternativ** och sedan **använda ett annat konto** för att ange de autentiseringsuppgifter som du angav när du skapade den virtuella datorn) och klicka sedan på **OK**. Du kan få en certifikatvarning under inloggningen. Klicka på **Ja** eller **Fortsätt** för att fortsätta med anslutningen.

På den virtuella datorn *myVm1* aktiverar du Internet Control Message Protocol (ICMP) via Windows-brandväggen så att du kan pinga den här virtuella datorn från *myVm2* i ett senare steg med PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Även om ping används för att kommunicera mellan virtuella datorer i den här artikeln, rekommenderas inte att tillåta ICMP via Windows-brandväggen för produktions distributioner.

Du ansluter till den virtuella datorn *myVm2* genom att ange följande kommando från en kommandotolk på den virtuella datorn *myVm1*:

```
mstsc /v:10.1.0.4
```

Eftersom du har aktiverat ping på *myVm1* kan du nu pinga det via IP-adress från en kommando tolk på den virtuella datorn *myVm2* :

```
ping 10.0.0.4
```

Du får fyra svar. Koppla från RDP-sessionerna till både *myVm1* och *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser den innehåller.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du ansluter två nätverk i samma Azure-region, med peering av virtuella nätverk. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region) och i [olika Azure-prenumerationer](create-peering-different-subscriptions.md#powershell), samt skapa [nav- och ekernätverksdesigner](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) med peerkoppling. Mer information om peerkoppling av virtuella nätverk finns i [Översikt över peerkoppling av virtuella nätverk](virtual-network-peering-overview.md) och [Hantera peerkopplingar av virtuella nätverk](virtual-network-manage-peering.md).

Du kan [ansluta din egen dator till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via en VPN-anslutning och interagera med resurser i ett virtuellt nätverk eller i peer-kopplade virtuella nätverk. För återanvändbara skript för att utföra många av de uppgifter som beskrivs i de virtuella nätverks artiklarna, se [skript exempel](powershell-samples.md).
