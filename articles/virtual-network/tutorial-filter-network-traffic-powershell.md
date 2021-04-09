---
title: Filtrera nätverks trafik – Azure PowerShell | Microsoft Docs
description: I den här artikeln får du lära dig hur du filtrerar nätverks trafik till ett undernät med en nätverks säkerhets grupp med hjälp av PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8ab22e7960e233d6ae934fb52989aa73a494b33a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791518"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtrera nätverks trafik med en nätverks säkerhets grupp med hjälp av PowerShell

Du kan filtrera inkommande och utgående nätverkstrafik till och från ett undernät i ett virtuellt nätverk med en nätverkssäkerhetsgrupp. Nätverkssäkerhetsgrupper innehåller säkerhetsregler som filtrerar nätverkstrafik efter IP-adress, port och protokoll. Säkerhetsregler tillämpas på resurser som har distribuerats i ett undernät. I den här artikeln kan du se hur du:

* Skapa en nätverkssäkerhetsgrupp och säkerhetsregler
* Skapa ett virtuellt nätverk och associera en nätverkssäkerhetsgrupp med ett undernät
* Distribuera virtuella datorer (VM) i ett undernät
* Testa trafikfilter

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

En nätverkssäkerhetsgrupp innehåller säkerhetsregler. Säkerhetsregler anger en källa och ett mål. Källor och mål kan vara programsäkerhetsgrupper.

### <a name="create-application-security-groups"></a>Skapa programsäkerhetsgrupper

Skapa först en resurs grupp för alla resurser som skapats i den här artikeln med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resursgrupp på platsen *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Skapa en program säkerhets grupp med [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup). En programsäkerhetsgrupp gör att du kan gruppera servrar med liknande portfiltreringskrav. I följande exempel skapas två programsäkerhetsgrupper.

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Skapa säkerhetsregler

Skapa en säkerhets regel med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). I följande exempel skapas en regel som tillåter inkommande trafik från Internet till programsäkerhetsgruppen *myWebServers* via port 80 och 443:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

I den här artikeln visas RDP (port 3389) för Internet för den virtuella datorn *myAsgMgmtServers* . För produktions miljöer, i stället för att exponera port 3389 på Internet, rekommenderar vi att du ansluter till Azure-resurser som du vill hantera med hjälp av en [VPN-](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) eller [privat](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nätverks anslutning.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNsg*:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Skapa en under näts konfiguration med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)och skriv sedan under näts konfigurationen till det virtuella nätverket med [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork). I följande exempel läggs ett undernät med namnet *mySubnet* till i det virtuella nätverket och nätverkssäkerhetsgruppen *myNsg* associeras med det:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Innan du skapar de virtuella datorerna hämtar du det virtuella nätverks objekt med under nätet med [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Skapa en offentlig IP-adress för varje virtuell dator med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress):

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Skapa två nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)och tilldela en offentlig IP-adress till nätverks gränssnittet. I följande exempel skapas ett nätverksgränssnitt, den offentliga IP-adressen för *myVmWeb* associeras med det och det görs till medlem i programsäkerhetsgruppen *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

I följande exempel skapas ett nätverksgränssnitt, den offentliga IP-adressen för *myVmMgmt* associeras med det och det görs till medlem i programsäkerhetsgruppen *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera trafikfiltrering i ett senare steg.

Skapa en VM-konfiguration med [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)och skapa sedan den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas en virtuell dator som fungerar som en webbserver. Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden, så att du kan fortsätta till nästa steg:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Skapa en virtuell dator som fungerar som en hanteringsserver:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med nästa steg förrän Azure har skapat den virtuella datorn.

## <a name="test-traffic-filters"></a>Testa trafikfilter

Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) för att returnera den offentliga IP-adressen för en virtuell dator. I följande exempel returneras den offentliga IP-adressen för den virtuella datorn *myVmMgmt*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Använd följande kommando för att skapa en fjärrskrivbordssession med den virtuella datorn *myVmMgmt* från den lokala datorn. Ersätt `<publicIpAddress>` med IP-adressen som returnerades från föregående kommando.

```
mstsc /v:<publicIpAddress>
```

Öppna den nedladdade RDP-filen. Välj **Anslut** om du uppmanas att göra det.

Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.

Anslutningen lyckas, eftersom port 3389 tillåts inkommande från Internet till programsäkerhetsgruppen *myAsgMgmtServers* där nätverksgränssnittet som är kopplat till den virtuella datorn *myVmMgmt* ingår.

Använd följande kommando för att skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVmWeb*, från den virtuella datorn *myVmMgmt*, med följande kommando, från PowerShell:

``` 
mstsc /v:myvmWeb
```

Anslutningen lyckas eftersom en standardsäkerhetsregel i varje nätverkssäkerhetsgrupp tillåter trafik via alla portar mellan alla IP-adresser i ett virtuellt nätverk. Det går inte att skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVmWeb* från Internet eftersom säkerhetsregeln för *myAsgWebServers* inte tillåter port 3389 inkommande från Internet.

Använd följande kommando för att installera Microsoft IIS på den virtuella datorn *myVmWeb* från PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

När IIS-installationen är klar kopplar du från den virtuella datorn *myVmWeb*, vilket lämnar dig i fjärrskrivbordsanslutningen med den virtuella datorn *myVmMgmt*. Visa välkomst skärmen i IIS genom att öppna en webbläsare och gå till http: \/ /myVmWeb.

Koppla från den virtuella datorn *myVmMgmt*.

På datorn anger du följande kommando från PowerShell för att hämta den offentliga IP-adressen för servern *myVmWeb*:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Bekräfta att du har åtkomst till webbservern *myVmWeb* utanför Azure genom att öppna en webbläsare på datorn och gå till `http://<public-ip-address-from-previous-step>`. Anslutningen lyckas, eftersom port 80 tillåts inkommande från Internet till programsäkerhetsgruppen *myAsgWebServers* där nätverksgränssnittet som är kopplat till den virtuella datorn *myVmWeb* ingår.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser som den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en nätverks säkerhets grupp och kopplat den till ett virtuellt nätverks under nät. Mer information om nätverkssäkerhetsgrupper finns i [Översikt över nätverkssäkerhetsgrupper](./network-security-groups-overview.md) och [Hantera en nätverkssäkerhetsgrupp](manage-network-security-group.md).

Azure dirigerar som standard trafik mellan undernät. Du kan i stället välja att exempelvis dirigera trafik mellan undernät via en virtuell dator, som fungerar som en brandvägg. Mer information finns i [skapa en routningstabell](tutorial-create-route-table-powershell.md).