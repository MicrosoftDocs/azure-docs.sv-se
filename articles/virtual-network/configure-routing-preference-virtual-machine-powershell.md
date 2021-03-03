---
title: Konfigurera Dirigerings inställningar för en virtuell dator – Azure PowerShell
description: Lär dig hur du skapar en virtuell dator med en offentlig IP-adress med inställnings alternativ för routning med hjälp av Azure PowerShell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 8ffa7ef7b3539e3cefc4e2f8621d7c67cd3b56b0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672963"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure PowerShell

Den här artikeln visar hur du konfigurerar cirkulations inställningar för en virtuell dator. Internet bindnings trafik från den virtuella datorn kommer att dirigeras via Internet leverantörens nätverk när du väljer **Internet** som alternativ för routning av inställningar. Standardroutningen är via Microsofts globala nätverk.

Den här artikeln visar hur du skapar en virtuell dator med en offentlig IP-adress som är inställd på att dirigera trafik via Internet leverantörs nätverket med hjälp av Azure PowerShell.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Om du använder Cloud Shell går du vidare till steg 2. Öppna en kommando-session och logga in på Azure med `Connect-AzAccount` .
2. Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). I följande exempel skapas en resurs grupp i Azure-regionen USA, östra:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

För att få åtkomst till dina virtuella datorer från Internet behöver du en offentlig IP-adress. Skapa offentliga IP-adresser med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). I följande exempel skapas en offentlig IPv4-IP-adress med namnet *MyPublicIP* routning Preference Type *Internet* i resurs gruppen *MyResourceGroup* i regionen *USA, östra* :

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Innan du distribuerar en virtuell dator måste du skapa stöd för nätverks resurser – nätverks säkerhets grupp, virtuellt nätverk och virtuellt nätverkskort.

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp med [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup). I följande exempel skapas en NSG med namnet *myNSG*

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVNET* med *under nätet*:

### <a name="create-a-subnet"></a>Skapa ett undernät

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Skapa ett nätverkskort

Skapa virtuella nätverkskort med [New-AzNetworkInterface] (/PowerShell/module/AZ.Network/New-aznetworkinterface. I följande exempel skapas ett virtuellt nätverkskort.

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Ange ett administratörsanvändarnamn och lösenord för de virtuella datorerna med [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Nu kan du skapa den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm). I följande exempel skapas två virtuella datorer och de virtuella nätverkskomponenter som krävs, om de inte redan finns.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Tillåt nätverks trafik till den virtuella datorn

Innan du kan ansluta till den offentliga IP-adressen från Internet måste du se till att de nödvändiga portarna är öppna i alla nätverks säkerhets grupper som du kan ha kopplat till nätverks gränssnittet, under nätet som nätverks gränssnittet finns i eller båda. Du kan visa de effektiva säkerhets reglerna för ett nätverks gränssnitt och dess undernät med hjälp av [portalen](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)eller [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [cirkulations inställningar i offentliga IP-adresser](routing-preference-overview.md).
* Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
* Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).