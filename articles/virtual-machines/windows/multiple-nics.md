---
title: Skapa och hantera virtuella Windows-datorer i Azure som använder flera nätverkskort
description: Lär dig hur du skapar och hanterar en virtuell Windows-dator med flera nätverkskort som är kopplade till den med hjälp av Azure PowerShell-eller Resource Manager-mallar.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: cynthn
ms.openlocfilehash: 2664d175818a6e29dcd3f704c6938987bae050cd
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555184"
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>Skapa och hantera en virtuell Windows-dator som har flera nätverkskort
Virtuella datorer i Azure kan ha flera virtuella nätverkskort (NIC) anslutna till dem. Ett vanligt scenario är att ha olika undernät för klient dels-och backend-anslutningar. Du kan associera flera nätverkskort på en virtuell dator till flera undernät, men dessa undernät måste finnas i samma virtuella nätverk (vNet). Den här artikeln beskriver hur du skapar en virtuell dator som har flera nätverkskort kopplade till sig. Du lär dig också hur du lägger till eller tar bort nätverkskort från en befintlig virtuell dator. Olika [VM-storlekar](../sizes.md) har stöd för olika antal nätverkskort, så storleken på den virtuella datorn.

## <a name="prerequisites"></a>Förutsättningar

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn är *myResourceGroup*, *myVnet* och *myVM*.

 

## <a name="create-a-vm-with-multiple-nics"></a>Skapa en virtuell dator med flera nätverkskort
Skapa först en resurs grupp. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>Skapa virtuellt nätverk och undernät
Ett vanligt scenario är att ett virtuellt nätverk har två eller flera undernät. Ett undernät kan vara för klient dels trafik, det andra för backend-trafik. För att ansluta till båda under näten använder du sedan flera nätverkskort på den virtuella datorn.

1. Definiera två undernät för virtuella nätverk med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). I följande exempel definieras under näten för *mySubnetFrontEnd* och *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Skapa ditt virtuella nätverk och undernät med [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet*:

    ```powershell
    $myVnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>Skapa flera nätverkskort
Skapa två nätverkskort med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). Koppla ett nätverkskort till klient dels under nätet och ett nätverkskort till backend-undernätet. I följande exempel skapas nätverkskort med namnet *myNic1* och *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Vanligt vis skapar du också en [nätverks säkerhets grupp](../../virtual-network/network-security-groups-overview.md) för att filtrera nätverks trafik till den virtuella datorn och en [belastningsutjämnare](../../load-balancer/load-balancer-overview.md) för att distribuera trafik över flera virtuella datorer.

### <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn
Börja nu med att skapa din VM-konfiguration. Varje VM-storlek har en gräns för det totala antalet nätverkskort som du kan lägga till i en virtuell dator. Mer information finns i [storlekar för virtuella Windows-datorer](../sizes.md).

1. Ange dina autentiseringsuppgifter för virtuella datorer till `$cred` variabeln på följande sätt:

    ```powershell
    $cred = Get-Credential
    ```

2. Definiera din virtuella dator med [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Följande exempel definierar en virtuell dator med namnet *myVM* och använder en VM-storlek som har stöd för fler än två nätverkskort (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Skapa resten av din VM-konfiguration med [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) och [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). I följande exempel skapas en virtuell Windows Server 2016-dator:

    ```powershell
    $vmConfig = Set-AzVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Koppla de två nätverkskort som du tidigare skapade med [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Skapa din virtuella dator med [New-AzVM](/powershell/module/az.compute/new-azvm):

    ```powershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

6. Lägg till vägar för sekundära nätverkskort till operativ systemet genom att slutföra stegen i [Konfigurera operativ systemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="add-a-nic-to-an-existing-vm"></a>Lägga till ett nätverkskort i en befintlig virtuell dator
Om du vill lägga till ett virtuellt nätverkskort till en befintlig virtuell dator frigör du den virtuella datorn, lägger till det virtuella NÄTVERKSKORTet och startar sedan den virtuella datorn. Olika [VM-storlekar](../sizes.md) har stöd för olika antal nätverkskort, så storleken på den virtuella datorn. Om det behövs kan du [ändra storlek på en virtuell dator](resize-vm.md).

1. Frigör den virtuella datorn med [Stop-AzVM](/powershell/module/az.compute/stop-azvm). I följande exempel avallokeras den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzVm](/powershell/module/az.compute/get-azvm). I följande exempel hämtas information för den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. I följande exempel skapas ett virtuellt nätverkskort med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) med namnet *myNic3* som är kopplat till *mySubnetBackEnd*. Det virtuella NÄTVERKSKORTet kopplas sedan till den virtuella datorn med namnet *myVM* i *myResourceGroup* med [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzVMNetworkInterface -VM $vm -Id $nicId | Update-AzVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>Primära virtuella nätverkskort
    Ett av nätverkskorten på en virtuell dator med flera nätverkskort måste vara primärt. Om något av de befintliga virtuella nätverkskorten på den virtuella datorn redan har angetts som primär kan du hoppa över det här steget. I följande exempel förutsätts att två virtuella nätverkskort nu finns på en virtuell dator och du vill lägga till det första NÄTVERKSKORTet ( `[0]` ) som primärt:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Starta den virtuella datorn med [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

5. Lägg till vägar för sekundära nätverkskort till operativ systemet genom att slutföra stegen i [Konfigurera operativ systemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="remove-a-nic-from-an-existing-vm"></a>Ta bort ett nätverkskort från en befintlig virtuell dator
Om du vill ta bort ett virtuellt nätverkskort från en befintlig virtuell dator frigör du den virtuella datorn, tar bort det virtuella NÄTVERKSKORTet och startar sedan den virtuella datorn.

1. Frigör den virtuella datorn med [Stop-AzVM](/powershell/module/az.compute/stop-azvm). I följande exempel avallokeras den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    Stop-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Hämta den befintliga konfigurationen av den virtuella datorn med [Get-AzVm](/powershell/module/az.compute/get-azvm). I följande exempel hämtas information för den virtuella datorn med namnet *myVM* i *myResourceGroup*:

    ```powershell
    $vm = Get-AzVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Hämta information om NÄTVERKSKORTet ta bort med [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface). I följande exempel får du information om *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Ta bort NÄTVERKSKORTet med [Remove-AzVMNetworkInterface](/powershell/module/az.compute/remove-azvmnetworkinterface) och uppdatera den virtuella datorn med [Update-AzVm](/powershell/module/az.compute/update-azvm). I följande exempel tar vi bort *myNic3* som hämtades `$nicId` i föregående steg:

    ```powershell
    Remove-AzVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzVm -ResourceGroupName "myResourceGroup"
    ```   

5. Starta den virtuella datorn med [Start-AzVm](/powershell/module/az.compute/start-azvm):

    ```powershell
    Start-AzVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>Skapa flera nätverkskort med mallar
Azure Resource Manager mallar ger dig ett sätt att skapa flera instanser av en resurs under distributionen, till exempel skapa flera nätverkskort. Resource Manager-mallar använder deklarativ JSON-filer för att definiera din miljö. Mer information finns i [Översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md). Du kan använda *copy* för att ange antalet instanser som ska skapas:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

Mer information finns i [skapa flera instanser med hjälp av *Kopiera*](../../azure-resource-manager/templates/copy-resources.md). 

Du kan också använda `copyIndex()` för att lägga till ett nummer till ett resurs namn. Du kan sedan skapa *myNic1*, *MyNic2* och så vidare. Följande kod visar ett exempel på hur du lägger till indexvärdet:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Du kan läsa ett fullständigt exempel på hur du [skapar flera nätverkskort med hjälp av Resource Manager-mallar](../../virtual-network/template-samples.md).

Lägg till vägar för sekundära nätverkskort till operativ systemet genom att slutföra stegen i [Konfigurera operativ systemet för flera nätverkskort](#configure-guest-os-for-multiple-nics).

## <a name="configure-guest-os-for-multiple-nics"></a>Konfigurera gäst operativ system för flera nätverkskort

Azure tilldelar en standard-gateway till det första nätverks gränssnittet som är kopplat till den virtuella datorn. Azure tilldelar inte en standardgateway till ytterligare (sekundära) nätverksgränssnitt som är kopplade till en virtuell dator. Du kan därför som standard inte kommunicera med resurser utanför det undernät som är ett sekundärt nätverksgränssnitt befinner sig i. Sekundära nätverks gränssnitt kan dock kommunicera med resurser utanför deras undernät, även om stegen för att aktivera kommunikation är olika för olika operativ system.

1. Från en kommando tolk i Windows kör du `route print` kommandot som returnerar utdata som liknar följande utdata för en virtuell dator med två anslutna nätverks gränssnitt:

    ```
    ===========================================================================
    Interface List
    3...00 0d 3a 10 92 ce ......Microsoft Hyper-V Network Adapter #3
    7...00 0d 3a 10 9b 2a ......Microsoft Hyper-V Network Adapter #4
    ===========================================================================
    ```
 
    I det här exemplet är **Microsoft Hyper-V nätverkskort #4** (gränssnitt 7) det sekundära nätverks gränssnittet som inte har någon standard-gateway tilldelad.

2. Kör kommandot i en kommando tolk `ipconfig` för att se vilken IP-adress som är tilldelad det sekundära nätverks gränssnittet. I det här exemplet tilldelas 192.168.2.4 till gränssnitt 7. Ingen standardgateway-adress returneras för det sekundära nätverks gränssnittet.

3. Kör följande kommando för att dirigera all trafik som är avsedd för adresser utanför under nätet för det sekundära nätverks gränssnittet till gatewayen för under nätet:

    ```
    route add -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5015 IF 7
    ```

    Gateway-adressen för under nätet är den första IP-adressen (slutar i 1) i det adress intervall som definierats för under nätet. Om du inte vill dirigera all trafik utanför under nätet kan du i stället lägga till enskilda vägar till specifika mål. Om du till exempel bara ville dirigera trafik från det sekundära nätverks gränssnittet till 192.168.3.0-nätverket, anger du kommandot:

      ```
      route add -p 192.168.3.0 MASK 255.255.255.0 192.168.2.1 METRIC 5015 IF 7
      ```
  
4. För att bekräfta lyckad kommunikation med en resurs i 192.168.3.0-nätverket, kan du till exempel ange följande kommando för att pinga 192.168.3.4 med hjälp av gränssnitt 7 (192.168.2.4):

    ```
    ping 192.168.3.4 -S 192.168.2.4
    ```

    Du kan behöva öppna ICMP via Windows-brandväggen på enheten som du pingar med följande kommando:
  
      ```
      netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
      ```
  
5. För att bekräfta att den tillagda vägen är i routningstabellen, anger du `route print` kommandot, som returnerar utdata som liknar följande text:

    ```
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4     15
              0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.4   5015
    ```

    Den väg som anges med *192.168.1.1* under **Gateway** är den väg som finns som standard för det primära nätverks gränssnittet. Vägen med *192.168.2.1* under **gatewayen** är den väg som du har lagt till.

## <a name="next-steps"></a>Nästa steg
Granska [storleken på virtuella Windows-datorer](../sizes.md) när du försöker skapa en virtuell dator som har flera nätverkskort. Var uppmärksam på det maximala antalet nätverkskort som varje VM-storlek stöder.