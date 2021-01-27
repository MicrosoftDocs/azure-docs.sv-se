---
title: Skapa en virtuell Windows-dator med accelererat nätverk – Azure PowerShell
description: Skapa en virtuell Windows-dator (VM) med accelererat nätverk för att avsevärt förbättra nätverkets prestanda.
services: virtual-network
documentationcenter: ''
author: gsilva5
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/15/2020
ms.author: gsilva
ms.openlocfilehash: b0ebb75530858a589c3166e21261e2f737fff50d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919977"
---
# <a name="create-a-windows-vm-with-accelerated-networking-using-azure-powershell"></a>Skapa en virtuell Windows-dator med accelererat nätverk med Azure PowerShell

I den här självstudien får du lära dig hur du skapar en virtuell Windows-dator (VM) med accelererat nätverk.

> [!NOTE]
> Information om hur du använder accelererat nätverk med en virtuell Linux-dator finns i [skapa en virtuell Linux-dator med accelererat nätverk](create-vm-accelerated-networking-cli.md).

Accelererat nätverk möjliggör SR-IOV (Single root I/O Virtualization) till en virtuell dator, vilket avsevärt förbättrar nätverkets prestanda. Med den här högpresterande sökvägen kringgås värden från data Sök vägen, vilket minskar svars tiden, skakningarna och processor användningen för de mest krävande nätverks belastningarna på VM-typer som stöds. Följande diagram illustrerar hur två virtuella datorer kommunicerar med och utan accelererat nätverk:

![Kommunikation mellan virtuella Azure-datorer med och utan accelererat nätverk](./media/create-vm-accelerated-networking/accelerated-networking.png)

Utan accelererat nätverk måste all nätverks trafik i och från den virtuella datorn passera värden och den virtuella växeln. Den virtuella växeln tillhandahåller all princip tillämpning, till exempel nätverks säkerhets grupper, åtkomst kontrol listor, isolering och andra virtualiserade nätverks tjänster till nätverks trafik.

> [!NOTE]
> Mer information om virtuella växlar finns i [Hyper-V virtuell växel](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch).

Med accelererat nätverk anländer nätverks trafiken till den virtuella datorns nätverks gränssnitt (NIC) och vidarebefordras sedan till den virtuella datorn. Alla nätverks principer som den virtuella växeln gäller avlastas nu och används i maskin vara. Eftersom principen tillämpas i maskin vara kan NÄTVERKSKORTet vidarebefordra nätverks trafik direkt till den virtuella datorn. NÄTVERKSKORTet kringgår värden och den virtuella växeln, medan den upprätthåller all princip som tillämpas på värden.

Fördelarna med accelererat nätverk gäller endast den virtuella dator som den är aktive rad på. För bästa resultat bör du aktivera den här funktionen på minst två virtuella datorer som är anslutna till samma virtuella Azure-nätverk. När du kommunicerar via virtuella nätverk eller ansluter lokalt har den här funktionen minimal påverkan på den totala svars tiden.

## <a name="benefits"></a>Fördelar

- **Lägre latens/högre paket per sekund (PPS)**: att ta bort den virtuella växeln från data Sök vägen tar bort de tid paket som ägnas åt värden för princip bearbetning. Det ökar också antalet paket som kan bearbetas inuti den virtuella datorn.

- **Reducerade Darr**: bearbetning av virtuell växel beror på den mängd princip som måste tillämpas. Det beror också på arbets belastningen för den processor som utför bearbetningen. Genom att avlasta den tvingande principen till maskin varan tas den bort om du levererar paket direkt till den virtuella datorn. Avlastning tar också bort värd-till-VM-kommunikation, alla program avbrott och alla kontext växlar.

- **Minskad processor användning**: om du kringgår den virtuella växeln på värden går det snabbare att bearbeta nätverks trafiken.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande distributioner stöds direkt från Azure-galleriet:

- **Windows Server 2019 Datacenter**
- **Windows Server 2016 Datacenter** 
- **Windows Server 2012 R2 Datacenter**

## <a name="limitations-and-constraints"></a>Begränsningar och begränsningar

### <a name="supported-vm-instances"></a>Virtuella dator instanser som stöds

Accelererat nätverk stöds i de flesta generella syftes-och beräknings optimerade instans storlekar med 2 eller fler virtuella processorer. På instanser som stöder hyperthreading stöds accelererat nätverk på VM-instanser med 4 eller fler virtuella processorer. 

Stöd för accelererat nätverk finns i den enskilda dokumentationen för [virtuella dator storlekar](../virtual-machines/sizes.md) . 

### <a name="custom-images"></a>Anpassade avbildningar

Om du använder en anpassad avbildning och din avbildning har stöd för accelererat nätverk måste du se till att du har de driv rutiner som krävs för Mellanox ConnectX-3-och ConnectX-4 LX-nätverkskort på Azure.

### <a name="regions"></a>Regioner

Accelererat nätverk är tillgängligt i alla globala Azure-regioner och Azure Government molnet.

### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Aktivera accelererat nätverk på en virtuell dator som körs

En virtuell dator storlek som stöds utan accelererat nätverk kan bara aktivera funktionen när den stoppas och frigörs.

### <a name="deployment-through-azure-resource-manager"></a>Distribution via Azure Resource Manager

Virtuella datorer (klassiska) kan inte distribueras med accelererat nätverk.

## <a name="vm-creation-using-the-portal"></a>Skapa virtuell dator med portalen

Även om den här artikeln innehåller steg för att skapa en virtuell dator med accelererat nätverk med Azure PowerShell, kan du också [använda Azure Portal för att skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) som möjliggör accelererat nätverk. När du skapar en virtuell dator i portalen väljer du fliken **nätverk** på sidan **skapa en virtuell dator** . På den här fliken finns ett alternativ för **accelererat nätverk**. Om du har valt ett [operativ system som stöds](#supported-operating-systems) och storleken på den [virtuella datorn](#supported-vm-instances)är det här alternativet automatiskt inställt **på**. Annars är alternativet inställt på **av** och Azure visar anledningen till varför det inte går att aktivera.

> [!NOTE]
> Endast operativ system som stöds kan aktive ras via portalen. Om du använder en anpassad avbildning och din avbildning har stöd för accelererat nätverk skapar du en virtuell dator med CLI eller PowerShell. 

När du har skapat den virtuella datorn kan du kontrol lera om det accelererade nätverket är aktiverat. Följ dessa anvisningar:

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina virtuella datorer. Sök efter och välj **virtuella datorer**.

2. I listan virtuell dator väljer du den nya virtuella datorn.

3. Välj **nätverk** i meny raden VM.

I nätverks gränssnitts information, bredvid den **accelererade nätverks** etiketten, visar portalen antingen **inaktive rad** eller **aktive rad** för status för accelererat nätverk.

## <a name="vm-creation-using-powershell"></a>Skapa virtuell dator med PowerShell

Innan du fortsätter installerar du [Azure PowerShell](/powershell/azure/install-az-ps) version 1.0.0 eller senare. Kör för att hitta den version som är installerad `Get-Module -ListAvailable Az` . Om du behöver installera eller uppgradera installerar du den senaste versionen av AZ-modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az). Logga in på ett Azure-konto med [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)i en PowerShell-session.

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn som ingår *myResourceGroup*, *myNic* och *myVM*.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Följande kommando skapar en resurs grupp med namnet *myResourceGroup* på platsen för den *centrala* platsen:

    ```azurepowershell
    New-AzResourceGroup -Name "myResourceGroup" -Location "centralus"
    ```

2. Skapa en under näts konfiguration med [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/New-azVirtualNetworkSubnetConfig). Följande kommando skapar ett undernät med namnet *undernät*:

    ```azurepowershell
    $subnet = New-AzVirtualNetworkSubnetConfig `
        -Name "mySubnet" `
        -AddressPrefix "192.168.1.0/24"
    ```

3. Skapa ett virtuellt nätverk med [New-AzVirtualNetwork](/powershell/module/az.Network/New-azVirtualNetwork), *med under nätet undernät.*

    ```azurepowershell
    $vnet = New-AzVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "centralus" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $Subnet
    ```

### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Skapa en regel för nätverks säkerhets grupp med [New-AzNetworkSecurityRuleConfig](/powershell/module/az.Network/New-azNetworkSecurityRuleConfig).

    ```azurepowershell
    $rdp = New-AzNetworkSecurityRuleConfig `
        -Name 'Allow-RDP-All' `
        -Description 'Allow RDP' `
        -Access Allow `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 100 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389
    ```

2. Skapa en nätverks säkerhets grupp med [New-AzNetworkSecurityGroup](/powershell/module/az.Network/New-azNetworkSecurityGroup) och tilldela den *tillåtelse-RDP-all* säkerhets regeln. Förutom den *tillåtna-RDP-all-* regeln innehåller nätverks säkerhets gruppen flera standard regler. En standard regel inaktiverar all inkommande åtkomst från Internet. När den har skapats tilldelas nätverks säkerhets gruppen den *tillåtna-RDP-all-* regeln, så att du kan fjärrans luta till den virtuella datorn.

    ```azurepowershell
    $nsg = New-AzNetworkSecurityGroup `
        -ResourceGroupName myResourceGroup `
        -Location centralus `
        -Name "myNsg" `
        -SecurityRules $rdp
    ```

3. Koppla nätverks säkerhets gruppen *till under nätet under nät* med [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.Network/Set-azVirtualNetworkSubnetConfig). Regeln i nätverks säkerhets gruppen är effektiv för alla resurser som distribueras i under nätet.

    ```azurepowershell
    Set-AzVirtualNetworkSubnetConfig `
        -VirtualNetwork $vnet `
        -Name 'mySubnet' `
        -AddressPrefix "192.168.1.0/24" `
        -NetworkSecurityGroup $nsg
    ```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Skapa ett nätverks gränssnitt med accelererat nätverk

1. Skapa en offentlig IP-adress med hjälp av [New-AzPublicIpAddress](/powershell/module/az.Network/New-azPublicIpAddress). En offentlig IP-adress är onödig om du inte planerar att komma åt den virtuella datorn från Internet. Men det krävs för att slutföra stegen i den här artikeln.

    ```azurepowershell
    $publicIp = New-AzPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name 'myPublicIp' `
        -location centralus `
        -AllocationMethod Dynamic
    ```

2. Skapa ett nätverks gränssnitt med [New-AzNetworkInterface](/powershell/module/az.Network/New-azNetworkInterface) med accelererat nätverk aktiverat och tilldela den offentliga IP-adressen till nätverks gränssnittet. I följande exempel skapas ett nätverks gränssnitt med namnet *myNic* i *under nätet undernät för* det virtuella *myVnet* -nätverket, och tilldelar den offentliga *myPublicIp* -IP-adressen:

    ```azurepowershell
    $nic = New-AzNetworkInterface `
        -ResourceGroupName "myResourceGroup" `
        -Name "myNic" `
        -Location "centralus" `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $publicIp.Id `
        -EnableAcceleratedNetworking
    ```

### <a name="create-a-vm-and-attach-the-network-interface"></a>Skapa en virtuell dator och bifoga nätverks gränssnittet

1. Ange autentiseringsuppgifterna för din virtuella dator till `$cred` variabeln med [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential), som gör att du kan logga in:

    ```azurepowershell
    $cred = Get-Credential
    ```

2. Definiera din virtuella dator med [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). Följande kommando definierar en virtuell dator med namnet *myVM* med en VM-storlek som har stöd för accelererat nätverk (*Standard_DS4_v2*):

    ```azurepowershell
    $vmConfig = New-AzVMConfig -VMName "myVm" -VMSize "Standard_DS4_v2"
    ```

    En lista över alla storlekar och egenskaper för virtuella datorer finns i [storlekar för virtuella Windows-datorer](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

3. Skapa resten av din VM-konfiguration med [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) och [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage). Följande kommando skapar en virtuell Windows Server 2016-dator:

    ```azurepowershell
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

4. Bifoga nätverks gränssnittet som du skapade tidigare med [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface):

    ```azurepowershell
    $vmConfig = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

5. Skapa din virtuella dator med [New-AzVM](/powershell/module/az.compute/new-azvm).

    ```azurepowershell
    New-AzVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "centralus"
    ```

### <a name="confirm-the-ethernet-controller-is-installed-in-the-windows-vm"></a>Bekräfta att Ethernet-styrenheten är installerad på den virtuella Windows-datorn

När du har skapat den virtuella datorn i Azure ansluter du till den virtuella datorn och kontrollerar att Ethernet-styrenheten är installerad i Windows.

1. Gå till [Azure Portal](https://portal.azure.com) för att hantera dina virtuella datorer. Sök efter och välj **virtuella datorer**.

2. I listan virtuell dator väljer du den nya virtuella datorn.

3. På sidan VM-översikt, om **status** för den virtuella datorn visas som **skapa**, väntar du tills Azure har skapat den virtuella datorn. **Status** kommer att ändras till att **köras** när den virtuella datorn har skapats.

4. I verktygsfältet översikt över virtuell dator väljer du **Anslut**  >  **RDP**  >  **Download RDP-fil**.

5. Öppna. RDP-filen och logga sedan in på den virtuella datorn med de autentiseringsuppgifter som du angav i avsnittet [skapa en virtuell dator och bifoga nätverks gränssnittet](#create-a-vm-and-attach-the-network-interface) . Om du aldrig har anslutit till en virtuell Windows-dator i Azure, se [Anslut till virtuell dator](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-virtual-machine).

6. När fjärrskrivbordssessionen för den virtuella datorn visas högerklickar du på Start-knappen i Windows och väljer **Enhetshanteraren**.

7. I fönstret **Enhetshanteraren** expanderar **du noden nätverkskort** .

8. Bekräfta att **Ethernet-adaptern för den virtuella funktionen Mellanox ConnectX-3** visas, som du ser i följande bild:

    ![Mellanox ConnectX – 3 virtuell funktion Ethernet-kort, nytt nätverkskort för accelererat nätverk, Enhetshanteraren](./media/create-vm-accelerated-networking/device-manager.png)

Accelererat nätverk har nu Aktiver ATS för den virtuella datorn.

> [!NOTE]
> Om Mellanox-adaptern inte kan starta öppnar du en administratörs kommando tolk i fjärrskrivbordssessionen och anger följande kommando:
>
> `netsh int tcp set global rss = enabled`

## <a name="enable-accelerated-networking-on-existing-vms"></a>Aktivera accelererat nätverk på befintliga virtuella datorer

Om du har skapat en virtuell dator utan accelererat nätverk kan du aktivera den här funktionen på en befintlig virtuell dator. Den virtuella datorn måste ha stöd för accelererat nätverk genom att uppfylla följande krav, som också beskrivs ovan:

* Den virtuella datorn måste ha en storlek som stöds för accelererat nätverk.
* Den virtuella datorn måste vara en Azure Gallery-avbildning som stöds (och kernel-version för Linux).
* Alla virtuella datorer i en tillgänglighets uppsättning eller en virtuell dators skalnings uppsättning måste stoppas eller frigöras innan du aktiverar accelererat nätverk på något nätverkskort.

### <a name="individual-vms-and-vms-in-an-availability-set"></a>Enskilda virtuella datorer och virtuella datorer i en tillgänglighets uppsättning

1. Stoppa eller frigör den virtuella datorn eller, om det finns en tillgänglighets uppsättning, alla virtuella datorer i uppsättningen:

    ```azurepowershell
    Stop-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

    > [!NOTE]
    > När du skapar en virtuell dator individuellt, utan en tillgänglighets uppsättning, behöver du bara stoppa eller frigöra den enskilda virtuella datorn för att aktivera accelererat nätverk. Om den virtuella datorn skapades med en tillgänglighets uppsättning måste du stoppa eller frigöra alla virtuella datorer som finns i tillgänglighets uppsättningen innan du aktiverar accelererat nätverk på något av nätverkskorten, så att de virtuella datorerna slutar på ett kluster som har stöd för accelererat nätverk. Kravet på att stoppa eller frigöra är onödigt om du inaktiverar accelererat nätverk, eftersom kluster som har stöd för accelererat nätverk också fungerar bra med nätverkskort som inte använder accelererat nätverk.

2. Aktivera accelererat nätverk på den virtuella datorns nätverkskort:

    ```azurepowershell
    $nic = Get-AzNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic"
    
    $nic.EnableAcceleratedNetworking = $true
    
    $nic | Set-AzNetworkInterface
    ```

3. Starta om den virtuella datorn eller, om den finns i en tillgänglighets uppsättning, alla virtuella datorer i uppsättningen och bekräfta att accelererat nätverk är aktiverat:

    ```azurepowershell
    Start-AzVM -ResourceGroup "myResourceGroup" `
        -Name "myVM"
    ```

### <a name="virtual-machine-scale-set"></a>Skaluppsättning för virtuella datorer

En skalnings uppsättning för en virtuell dator är något annorlunda, men den följer samma arbets flöde.

1. Stoppa de virtuella datorerna:

    ```azurepowershell
    Stop-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

2. Uppdatera egenskapen för accelererat nätverk under nätverks gränssnittet:

    ```azurepowershell
    $vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].EnableAcceleratedNetworking = $true
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

3. Ställ in de uppdateringar som tillämpas automatiskt så att ändringarna omedelbart hämtas:

    ```azurepowershell
    $vmss.UpgradePolicy.Mode = "Automatic"
    
    Update-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -VirtualMachineScaleSet $vmss
    ```

    > [!NOTE]
    > En skalnings uppsättning har VM-uppgraderingar som tillämpar uppdateringar med tre olika inställningar: automatisk, rullande och manuell. I dessa instruktioner är principen inställd på automatisk, så skalnings uppsättningen hämtar ändringarna direkt efter att den startats om.

4. Starta om skalnings uppsättningen:

    ```azurepowershell
    Start-AzVmss -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet"
    ```

Vänta tills uppgraderingarna har startats om. När uppgraderingen är färdig visas den virtuella funktionen (VF) i den virtuella datorn. Kontrol lera att du använder ett operativ system och en virtuell dator storlek som stöds.

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Ändra storlek på befintliga virtuella datorer med accelererat nätverk

Om en virtuell dator har accelererat nätverk kan du bara ändra storlek på den till en virtuell dator som har stöd för accelererat nätverk.  

Det går inte att ändra storlek på en virtuell dator med accelererat nätverk till en VM-instans som inte stöder accelererat nätverk med åtgärden ändra storlek. I stället kan du ändra storlek på en av de virtuella datorerna:

1. Stoppa eller frigör den virtuella datorn. Stoppa eller frigör alla virtuella datorer i tillgänglighets uppsättningen eller skalnings uppsättningen för en tillgänglighets uppsättning eller skalnings uppsättning.

2. Inaktivera accelererat nätverk på NÄTVERKSKORTet för den virtuella datorn. För en tillgänglighets uppsättning eller skalnings uppsättning inaktiverar du accelererat nätverk på nätverkskorten för alla virtuella datorer i tillgänglighets uppsättningen eller skalnings uppsättningen.

3. När du har inaktiverat accelererat nätverk flyttar du den virtuella datorn, tillgänglighets uppsättningen eller skalnings uppsättningen till en ny storlek som inte stöder accelererat nätverk och startar sedan om dem.