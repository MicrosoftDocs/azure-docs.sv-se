---
title: 'Självstudie: skapa en NAT-gateway – PowerShell'
titlesuffix: Azure Virtual Network NAT
description: Kom igång med att skapa en NAT-gateway med Azure PowerShell.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 884697cee84c05916fe19fb8f9435de86bda291e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620240"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Självstudie: skapa en NAT-gateway med hjälp av Azure PowerShell

Den här självstudien visar hur du använder Azure Virtual Network NAT-tjänsten. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk.
> * Skapa en virtuell dator.
> * Skapa en NAT-gateway och koppla den till det virtuella nätverket.
> * Anslut till den virtuella datorn och kontrol lera IP-adressen för NAT.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installerat lokalt eller Azure Cloud Shell

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resurs grupp med namnet **myResourceGroupNAT** på **eastus2** -platsen:

```azurepowershell-interactive
$rsg = @{
    Name = 'myResourceGroupNAT'
    Location = 'eastus2'
}
New-AzResourceGroup @rsg
```
## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

I det här avsnittet skapar vi NAT-gateway och stöd resurser.

* För att få åtkomst till Internet behöver du en eller flera offentliga IP-adresser för NAT-gatewayen. Använd [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa en offentlig IP-adressresurs med namnet **myPublicIP** i **myResourceGroupNAT**. 

* Skapa en global Azure NAT-gateway med [New-AzNatGateway](/powershell/module/az.network/new-aznatgateway). Resultatet av det här kommandot skapar en gateway-resurs med namnet **myNATgateway** som använder den offentliga IP- **myPublicIP**. Tids gränsen för inaktivitet har angetts till 10 minuter.  

* Skapa ett virtuellt nätverk med namnet **myVnet** med ett undernät med namnet mina **undernät** med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) i **myResourceGroup** med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). IP-adressutrymmet för det virtuella nätverket är **10.1.0.0/16**. Under nätet i det virtuella nätverket är **10.1.0.0/24**.

* Skapa en Azure skydds-värd med namnet **myBastionHost** för att få åtkomst till den virtuella datorn. Använd [New-AzBastion](/powershell/module/az.network/new-azbastion) för att skapa skydds-värden. Skapa en offentlig IP-adress för skydds-värden med [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress).

```azurepowershell-interactive
## Create public IP address for NAT gateway ##
$ip = @{
    Name = 'myPublicIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicIP = New-AzPublicIpAddress @ip

## Create NAT gateway resource ##
$nat = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myNATgateway'
    IdleTimeoutInMinutes = '10'
    Sku = 'Standard'
    Location = 'eastus2'
    PublicIpAddress = $publicIP
}
$natGateway = New-AzNatGateway @nat

## Create subnet config and associate NAT gateway to subnet##
$subnet = @{
    Name = 'mySubnet'
    AddressPrefix = '10.1.0.0/24'
    NatGateway = $natGateway
}
$subnetConfig = New-AzVirtualNetworkSubnetConfig @subnet 

## Create Azure Bastion subnet. ##
$bastsubnet = @{
    Name = 'AzureBastionSubnet' 
    AddressPrefix = '10.1.1.0/24'
}
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig @bastsubnet

## Create the virtual network ##
$net = @{
    Name = 'myVNet'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    AddressPrefix = '10.1.0.0/16'
    Subnet = $subnetConfig,$bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @net

## Create public IP address for bastion host. ##
$ip = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @ip

## Create bastion host ##
$bastion = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @bastion -AsJob

```

## <a name="virtual-machine"></a>Virtuell dator

I det här avsnittet ska du skapa en virtuell dator för att testa NAT-gatewayen och kontrol lera den offentliga IP-adressen för den utgående anslutningen.

* Skapa ett nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface).

* Ange ett administratörs användar namn och lösen ord för den virtuella datorn med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

* Skapa den virtuella datorn med:
    * [New-AzVM](/powershell/module/az.compute/new-azvm)
    * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
    * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
    * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
    * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)
    
```azurepowershell-interactive
# Set the administrator and password for the VMs. ##
$cred = Get-Credential

## Place the virtual network into a variable. ##
$vnet = Get-AzVirtualNetwork -Name 'myVNet' -ResourceGroupName 'myResourceGroupNAT'

## Create network interface for virtual machine. ##
$nic = @{
    Name = "myNicVM"
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @nic

## Create a virtual machine configuration for VMs ##
$vmsz = @{
    VMName = "myVM"
    VMSize = 'Standard_DS1_v2'  
}
$vmos = @{
    ComputerName = "myVM"
    Credential = $cred
}
$vmimage = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'    
}
$vmConfig = New-AzVMConfig @vmsz `
    | Set-AzVMOperatingSystem @vmos -Windows `
    | Set-AzVMSourceImage @vmimage `
    | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine for VMs ##
$vm = @{
    ResourceGroupName = 'myResourceGroupNAT'
    Location = 'eastus2'
    VM = $vmConfig
}
New-AzVM @vm

```

Vänta tills den virtuella datorn har skapats innan du går vidare till nästa avsnitt.

## <a name="test-nat-gateway"></a>Testa NAT-gateway

I det här avsnittet ska vi testa NAT-gatewayen. Vi identifierar först den offentliga IP-adressen för NAT-gatewayen. Sedan ansluter vi till den virtuella test datorn och verifierar den utgående anslutningen via NAT-gatewayen.
    
1. Logga in på [Azure-portalen](https://portal.azure.com)

1. Hitta den offentliga IP-adressen för NAT-gatewayen på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicIP**.

2. Anteckna den offentliga IP-adressen:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Identifiera offentlig IP-adress för NAT-gateway" border="true":::

3. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myVM** i resurs gruppen **myResourceGroupNAT** i resurs gruppen.

4. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

5. Välj knappen blå **användnings skydds** .

6. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange **https://whatsmyip.com** i adress fältet.

9. Kontrol lera att den IP-adress som visas matchar NAT-gateway-adressen som du antecknade i föregående steg:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer som visar extern utgående IP" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort det virtuella nätverket, den virtuella datorn och NAT-gatewayen med följande steg:

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'myResourceGroupNAT' -Force
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Virtual Network NAT finns i:
> [!div class="nextstepaction"]
> [Översikt över Virtual Network NAT](nat-overview.md)
