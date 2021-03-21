---
title: Skapa ett virtuellt nätverk – snabbstart – Azure PowerShell
titlesuffix: Azure Virtual Network
description: I den här snabb starten skapar du ett virtuellt nätverk med hjälp av Azure Portal. Ett virtuellt nätverk gör att Azure-resurser kan kommunicera med varandra och med Internet.
author: KumudD
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.topic: quickstart
ms.date: 03/06/2021
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b27f050d3d37daab05e8c5125d6b75a6bb4dea50
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199041"
---
# <a name="quickstart-create-a-virtual-network-using-powershell"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av PowerShell

Med ett virtuellt nätverk kan Azure-resurser, exempelvis virtuella datorer, kommunicera privat med varandra och med Internet. 

I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till de virtuella datorerna från Internet och kommunicerar privat i det virtuella nätverket.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell installerat lokalt eller Azure Cloud Shell

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group-and-a-virtual-network"></a>Skapa en resursgrupp och ett virtuellt nätverk

Det finns en handfull steg du måste gå igenom för att konfigurera din resursgrupp och virtuella nätverk.

### <a name="create-the-resource-group"></a>Skapa resursgruppen

Innan du kan skapa ett virtuellt nätverk måste du skapa en resursgrupp som ska vara värd för det virtuella nätverket. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I det här exemplet skapas en resurs grupp med namnet **CreateVNetQS-RG** på platsen för **öster** :

```azurepowershell-interactive
$rg = @{
    Name = 'CreateVNetQS-rg'
    Location = 'EastUS'
}
New-AzResourceGroup @rg
```

### <a name="create-the-virtual-network"></a>Skapa det virtuella nätverket

Skapa ett virtuellt nätverk med hjälp av [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I det här exemplet skapas ett virtuellt standard nätverk med namnet **myVNet** på platsen för **öster** :

```azurepowershell-interactive
$vnet = @{
    Name = 'myVNet'
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    AddressPrefix = '10.0.0.0/16'    
}
$virtualNetwork = New-AzVirtualNetwork @vnet
```

### <a name="add-a-subnet"></a>Lägga till ett undernät

Azure distribuerar resurser till ett undernät i ett virtuellt nätverk, så du måste skapa ett undernät. Skapa en undernätskonfiguration med namnet **standard** med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig):

```azurepowershell-interactive
$subnet = @{
    Name = 'default'
    VirtualNetwork = $virtualNetwork
    AddressPrefix = '10.0.0.0/24'
}
$subnetConfig = Add-AzVirtualNetworkSubnetConfig @subnet
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Associera undernätet med det virtuella nätverket

Du kan skriva undernätskonfigurationen till det virtuella nätverket med [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork). Det här kommandot skapar undernätet:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

Skapa den första virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm). När du kör följande kommando måste du ange autentiseringsuppgifter. Ange ett användarnamn och lösenord för den virtuella datorn:

```azurepowershell-interactive
$vm1 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM1'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm1 -AsJob
```

Alternativet `-AsJob` skapar den virtuella datorn i bakgrunden. Du kan fortsätta till nästa steg.

När Azure börjar skapa den virtuella datorn i bakgrunden får du tillbaka något liknande detta:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Skapa den andra virtuella datorn med det här kommandot:

```azurepowershell-interactive
$vm2 = @{
    ResourceGroupName = 'CreateVNetQS-rg'
    Location = 'EastUS'
    Name = 'myVM2'
    VirtualNetworkName = 'myVNet'
    SubnetName = 'default'
}
New-AzVM @vm2
```

Du måste skapa en annan användare och lösenord. Det tar några minuter för Azure att skapa den virtuella datorn.

> [!IMPORTANT]
> Fortsätt inte med nästa steg förrän Azure har avslutat uppgiften.  Det hela är klart när Azure returnerar utdata till PowerShell.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

Använd [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)för att hämta den offentliga IP-adressen för den virtuella datorn.

I det här exemplet returneras den offentliga IP-adressen för den virtuella datorn **myVm1**:

```azurepowershell-interactive
$ip = @{
    Name = 'myVM1'
    ResourceGroupName = 'CreateVNetQS-rg'
}
Get-AzPublicIpAddress @ip | select IpAddress
```

Öppna en kommandotolk på den lokala datorn. Kör kommandot `mstsc`. Ersätt `<publicIpAddress>` med den offentliga IP-adress som returnerades från det senaste steget:

> [!NOTE]
> Om du har kört dessa kommandon från en PowerShell-kommandotolk på din lokala dator, och du använder version 1.0 av Az PowerShell-modulen eller senare, kan du fortsätta i det gränssnittet.

```cmd
mstsc /v:<publicIpAddress>
```
1. Välj **Anslut** om du uppmanas att göra det.

1. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn.

    > [!NOTE]
    > Du kan behöva välja **fler alternativ**  >  **Använd ett annat konto** för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatsvarning. Om så är fallet väljer du **Ja** eller **Fortsätt**.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell på fjärrskrivbordet på **myVm1**.

1. Ange `ping myVm2`.

    Du får tillbaka något liknande detta:

    ```powershell
    PS C:\Users\myVm1> ping myVm2

    Pinging myVm2.ovvzzdcazhbu5iczfvonhg2zrb.bx.internal.cloudapp.net
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.0.0.5:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Pinget misslyckas eftersom det använder Internet Control Message Protocol (ICMP). ICMP släpps som standard inte genom Windows-brandväggen.

1. Ange det efterföljande kommandot för att tillåta **myVm2** att pinga **myVm1** i ett senare skede:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Kommandot tillåter ICMP inkommande genom Windows-brandväggen.

1. Stäng fjärrskrivbordsanslutningen till **myVm1**.

1. Upprepa stegen i [Ansluta till en virtuell dator från Internet](#connect-to-a-vm-from-the-internet). Den här gången ansluter du till **myVm2**.

1. Från en kommandotolk på den virtuella datorn **myVm2** anger du `ping myvm1`.

    Du får tillbaka något liknande detta:

    ```cmd
    C:\windows\system32>ping myVm1

    Pinging myVm1.e5p2dibbrqtejhq04lqrusvd4g.bx.internal.cloudapp.net [10.0.0.4] with 32 bytes of data:
    Reply from 10.0.0.4: bytes=32 time=2ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.0.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.0.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 2ms, Average = 0ms
    ```

    Du får svar från **myVm1**, eftersom du har tillåtit ICMP via Windows-brandväggen på den virtuella datorn **myVm1** i ett tidigare steg.

1. Stäng fjärrskrivbordsanslutningen till **myVm2**.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resursgruppen och alla resurser den innehåller med [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'CreateVNetQS-rg' -Force
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten: 

* Du har skapat ett virtuellt standard nätverk och två virtuella datorer. 
* Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan de två virtuella datorerna.

Privat kommunikation mellan virtuella datorer är obegränsad i ett virtuellt nätverk. 

Fortsätt till nästa artikel om du vill lära dig mer om att konfigurera olika typer av nätverkskommunikation för virtuella datorer:
> [!div class="nextstepaction"]
> [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md)
