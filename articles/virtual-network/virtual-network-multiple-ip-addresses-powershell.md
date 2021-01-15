---
title: Flera IP-adresser för virtuella Azure-datorer – PowerShell | Microsoft Docs
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med hjälp av PowerShell. | Resource Manager
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/24/2017
ms.author: allensu
ms.openlocfilehash: d86d4248b449ad3961a7798fd36a320eb6a74009
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217081"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Tilldela flera IP-adresser till virtuella datorer med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Den här artikeln beskriver hur du skapar en virtuell dator (VM) via Azure Resource Manager distributions modellen med hjälp av PowerShell. Det går inte att tilldela flera IP-adresser till resurser som skapats via den klassiska distributions modellen. Läs mer om Azures distributions modeller i artikeln [förstå distributions modeller](../azure-resource-manager/management/deployment-models.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Stegen nedan beskriver hur du skapar ett exempel på en virtuell dator med flera IP-adresser, enligt beskrivningen i scenariot. Ändra de variabel värden som krävs för din implementering.

1. Öppna en PowerShell-kommandotolk och slutför de återstående stegen i det här avsnittet i en enkel PowerShell-session. Om du inte redan har PowerShell installerat och konfigurerat slutför du stegen i artikeln [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/) .
2. Logga in på ditt konto med `Connect-AzAccount` kommandot.
3. Ersätt *myResourceGroup* och *väst* med ett namn och en plats där du väljer. Skapa en resursgrupp. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

   ```powershell
   $RgName   = "MyResourceGroup"
   $Location = "westus"

   New-AzResourceGroup `
   -Name $RgName `
   -Location $Location
   ```

4. Skapa ett virtuellt nätverk (VNet) och undernät på samma plats som resurs gruppen:

   ```powershell

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name MySubnet `
   -AddressPrefix 10.0.0.0/24

   # Create a virtual network
   $VNet = New-AzVirtualNetwork `
   -ResourceGroupName $RgName `
   -Location $Location `
   -Name MyVNet `
   -AddressPrefix 10.0.0.0/16 `
   -Subnet $subnetConfig

   # Get the subnet object
   $Subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetConfig.Name -VirtualNetwork $VNet
   ```

5. Skapa en nätverks säkerhets grupp (NSG) och en regel. NSG säkrar den virtuella datorn med regler för inkommande och utgående trafik. I detta fall skapas en regel för inkommande trafik för port 3389, som tillåter inkommande anslutningar till fjärrskrivbord.

    ```powershell
    
    # Create an inbound network security group rule for port 3389

    $NSGRule = New-AzNetworkSecurityRuleConfig `
    -Name MyNsgRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $NSG = New-AzNetworkSecurityGroup `
    -ResourceGroupName $RgName `
    -Location $Location `
    -Name MyNetworkSecurityGroup `
    -SecurityRules $NSGRule
    ```

6. Definiera NÄTVERKSKORTets primära IP-konfiguration. Ändra 10.0.0.4 till en giltig adress i under nätet som du skapade, om du inte använde värdet som definierades tidigare. Innan du tilldelar en statisk IP-adress rekommenderar vi att du först bekräftar att den inte redan används. Ange kommandot `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.4 -VirtualNetwork $VNet` . Om adressen är tillgänglig returnerar utdata *värdet True*. Om den inte är tillgänglig returnerar utdata *falskt* och en lista med adresser som är tillgängliga. 

    I följande kommandon **ersätter \<replace-with-your-unique-name> du med det unika DNS-namn som ska användas.** Namnet måste vara unikt för alla offentliga IP-adresser inom en Azure-region. Detta är en valfri parameter. Den kan tas bort om du bara vill ansluta till den virtuella datorn med hjälp av den offentliga IP-adressen.

    ```powershell
    
    # Create a public IP address
    $PublicIP1 = New-AzPublicIpAddress `
    -Name "MyPublicIP1" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -DomainNameLabel <replace-with-your-unique-name> `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName1 = "IPConfig-1"
    $IpConfig1     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName1 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.4 `
    -PublicIpAddress $PublicIP1 `
    -Primary
    ```

    När du tilldelar flera IP-konfigurationer till ett nätverkskort måste en konfiguration tilldelas som *-primär*.

    > [!NOTE]
    > Offentliga IP-adresser har en nominell avgift. Läs mer om pris information om IP-adresser i [pris](https://azure.microsoft.com/pricing/details/ip-addresses) sidan för IP-adresser. Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

7. Definiera de sekundära IP-konfigurationerna för NÄTVERKSKORTet. Du kan lägga till eller ta bort konfigurationer vid behov. Varje IP-konfiguration måste tilldelas en privat IP-adress. Varje konfiguration kan också ha en tilldelad offentlig IP-adress.

    ```powershell
    
    # Create a public IP address
    $PublicIP2 = New-AzPublicIpAddress `
    -Name "MyPublicIP2" `
    -ResourceGroupName $RgName `
    -Location $Location `
    -AllocationMethod Static
        
    #Create an IP configuration with a static private IP address and assign the public IP address to it
    $IpConfigName2 = "IPConfig-2"
    $IpConfig2     = New-AzNetworkInterfaceIpConfig `
    -Name $IpConfigName2 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.5 `
    -PublicIpAddress $PublicIP2
        
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzNetworkInterfaceIpConfig `
    -Name $IPConfigName3 `
    -Subnet $Subnet `
    -PrivateIpAddress 10.0.0.6
    ```

8. Skapa NÄTVERKSKORTet och associera de tre IP-konfigurationerna med den:

   ```powershell
   $NIC = New-AzNetworkInterface `
   -Name MyNIC `
   -ResourceGroupName $RgName `
   -Location $Location `
   -NetworkSecurityGroupId $NSG.Id `
   -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
   ```

   >[!NOTE]
   >Även om alla konfigurationer tilldelas till ett nätverkskort i den här artikeln kan du tilldela flera IP-konfigurationer till varje nätverkskort som är kopplat till den virtuella datorn. Information om hur du skapar en virtuell dator med flera nätverkskort finns i artikeln [skapa en virtuell dator med flera nätverkskort](../virtual-machines/windows/multiple-nics.md) .

9. Skapa den virtuella datorn genom att ange följande kommandon:

    ```powershell
    
    # Define a credential object. When you run these commands, you're prompted to enter a username and password for the VM you're creating.
    $cred = Get-Credential
    
    # Create a virtual machine configuration
    $VmConfig = New-AzVMConfig `
    -VMName MyVM `
    -VMSize Standard_DS1_v2 | `
    Set-AzVMOperatingSystem -Windows `
    -ComputerName MyVM `
    -Credential $cred | `
    Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
    Add-AzVMNetworkInterface `
    -Id $NIC.Id
    
    # Create the VM
    New-AzVM `
    -ResourceGroupName $RgName `
    -Location $Location `
    -VM $VmConfig
    ```

10. Lägg till de privata IP-adresserna i operativ systemet för den virtuella datorn genom att följa stegen för ditt operativ system i avsnittet [Lägg till IP-adresser till ett operativ system för virtuella datorer](#os-config) i den här artikeln. Lägg inte till offentliga IP-adresser i operativ systemet.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Lägg till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser i Azure-nätverks gränssnittet genom att följa anvisningarna nedan. I exemplen i följande avsnitt förutsätts att du redan har en virtuell dator med de tre IP-konfigurationerna som beskrivs i [scenariot](#scenario) i den här artikeln, men du behöver inte göra det.

1. Öppna en PowerShell-kommandotolk och slutför de återstående stegen i det här avsnittet i en enkel PowerShell-session. Om du inte redan har PowerShell installerat och konfigurerat slutför du stegen i artikeln [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/) .
2. Ändra "värden" för följande $Variables till namnet på det nätverkskort som du vill lägga till IP-adressen till och resurs gruppen och platsen som NÄTVERKSKORTet finns i:

   ```powershell
   $NicName  = "MyNIC"
   $RgName   = "MyResourceGroup"
   $Location = "westus"
   ```

   Om du inte vet namnet på det nätverkskort som du vill ändra anger du följande kommandon och ändrar sedan värdena för föregående variabler:

   ```powershell
   Get-AzNetworkInterface | Format-Table Name, ResourceGroupName, Location
   ```

3. Skapa en variabel och Ställ in den på det befintliga NÄTVERKSKORTet genom att skriva följande kommando:

   ```powershell
   $MyNIC = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $RgName
   ```

4. I följande kommandon ändrar du *MyVNet* och *mitt undernät* till namnen på det VNet och undernät som nätverkskortet är anslutet till. Ange kommandon för att hämta VNet-och under näts objekt som NÄTVERKSKORTet är anslutet till:

   ```powershell
   $MyVNet = Get-AzVirtualnetwork -Name MyVNet -ResourceGroupName $RgName
   $Subnet = $MyVnet.Subnets | Where-Object { $_.Name -eq "MySubnet" }
   ```

   Om du inte känner till det VNet-eller under näts namn som NÄTVERKSKORTet är anslutet till, anger du följande kommando:

   ```powershell
   $MyNIC.IpConfigurations
   ```

   I utdata söker du efter text som liknar följande exempel på utdata:

   ```
   "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet/subnets/MySubnet"
   ```

    I dessa utdata är *MyVnet* det *VNet och det undernät som* nätverkskortet är anslutet till.

5. Slutför stegen i något av följande avsnitt, baserat på dina krav:

   **Lägg till en privat IP-adress**

   Om du vill lägga till en privat IP-adress till ett nätverkskort måste du skapa en IP-konfiguration. Följande kommando skapar en konfiguration med en statisk IP-adress för 10.0.0.7. När du anger en statisk IP-adress måste den vara en oanvänd adress för under nätet. Vi rekommenderar att du först testar adressen för att se till att den är tillgänglig genom att ange `Test-AzPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` kommandot. Om IP-adressen är tillgänglig returnerar utdata *värdet True*. Om den inte är tillgänglig returnerar utdata *falskt*, och en lista över adresser som är tillgängliga.

   ```powershell
   Add-AzNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
   $MyNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
   ```

   Skapa så många konfigurationer som du behöver, med hjälp av unika konfigurations namn och privata IP-adresser (för konfigurationer med statiska IP-adresser).

   Lägg till den privata IP-adressen till den virtuella datorns operativ system genom att slutföra stegen för ditt operativ system i avsnittet [Lägg till IP-adresser till ett operativ system för virtuella datorer](#os-config) i den här artikeln.

   **Lägg till en offentlig IP-adress**

   En offentlig IP-adress läggs till genom att associera en offentlig IP-adressresurs till antingen en ny IP-konfiguration eller en befintlig IP-konfiguration. Slutför stegen i något av de avsnitt som följer, efter behov.

   > [!NOTE]
   > Offentliga IP-adresser har en nominell avgift. Läs mer om pris information om IP-adresser i [pris](https://azure.microsoft.com/pricing/details/ip-addresses) sidan för IP-adresser. Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
   >

   **Associera den offentliga IP-adressresursen till en ny IP-konfiguration**

   När du lägger till en offentlig IP-adress i en ny IP-konfiguration måste du också lägga till en privat IP-adress, eftersom alla IP-konfigurationer måste ha en privat IP-adress. Du kan antingen lägga till en befintlig offentlig IP-adressresurs eller skapa en ny. Ange följande kommando för att skapa en ny:

   ```powershell
   $myPublicIp3 = New-AzPublicIpAddress `
   -Name "myPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location `
   -AllocationMethod Static
   ```

   Om du vill skapa en ny IP-konfiguration med en statisk privat IP-adress och den tillhör ande *myPublicIp3* offentliga IP-adressresursen, anger du följande kommando:

   ```powershell
   Add-AzNetworkInterfaceIpConfig `
   -Name IPConfig-4 `
   -NetworkInterface $myNIC `
   -Subnet $Subnet `
   -PrivateIpAddress 10.0.0.7 `
   -PublicIpAddress $myPublicIp3
   ```

   **Associera den offentliga IP-adressresursen till en befintlig IP-konfiguration**

   En offentlig IP-adressresurs kan bara kopplas till en IP-konfiguration som inte redan har en associerad. Du kan avgöra om en IP-konfiguration har en associerad offentlig IP-adress genom att ange följande kommando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

   Du ser utdata som liknar följande:

   ```
   Name       PrivateIpAddress PublicIpAddress                                           Primary

   IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
   IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
   IpConfig-3 10.0.0.6                                                                     False
   ```

   Eftersom kolumnen **PublicIpAddress** för *IpConfig-3* är tom är ingen offentlig IP-adressresurs kopplad till den. Du kan lägga till en befintlig offentlig IP-adressresurs i IpConfig-3 eller ange följande kommando för att skapa en:

   ```powershell
   $MyPublicIp3 = New-AzPublicIpAddress `
   -Name "MyPublicIp3" `
   -ResourceGroupName $RgName `
   -Location $Location -AllocationMethod Static
   ```

   Ange följande kommando för att associera den offentliga IP-adressresursen till den befintliga IP-konfigurationen med namnet *IpConfig-3*:

   ```powershell
   Set-AzNetworkInterfaceIpConfig `
   -Name IpConfig-3 `
   -NetworkInterface $mynic `
   -Subnet $Subnet `
   -PublicIpAddress $myPublicIp3
   ```

6. Ange NÄTVERKSKORTet med den nya IP-konfigurationen genom att ange följande kommando:

   ```powershell
   Set-AzNetworkInterface -NetworkInterface $MyNIC
   ```

7. Visa de privata IP-adresserna och de offentliga IP-adressresurser som tilldelats NÄTVERKSKORTet genom att ange följande kommando:

   ```powershell
   $MyNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
   ```

8. Lägg till den privata IP-adressen till den virtuella datorns operativ system genom att slutföra stegen för ditt operativ system i avsnittet [Lägg till IP-adresser till ett operativ system för virtuella datorer](#os-config) i den här artikeln. Lägg inte till den offentliga IP-adressen i operativ systemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]