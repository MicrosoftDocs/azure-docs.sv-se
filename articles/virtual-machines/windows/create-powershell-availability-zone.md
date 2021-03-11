---
title: Skapa en virtuell dator i zonen med Azure PowerShell
description: Skapa en virtuell dator i en tillgänglighets zon med Azure PowerShell
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 7167c5806fde883a84878d4947dba9fd8c095f69
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558839"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-azure-powershell"></a>Skapa en virtuell dator i en tillgänglighets zon med hjälp av Azure PowerShell

Den här artikeln innehåller information om att använda Azure PowerShell för att skapa en virtuell Azure-dator som kör Windows Server 2016 i en Azure-tillgänglighetszon. En [tillgänglighetszon](../../availability-zones/az-overview.md) är en fysiskt separat zon i en Azure-region. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

Om du vill använda en tillgänglighetszon skapar du din virtuella dator i en [Azure-region som stöds](../../availability-zones/az-region.md).

 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="check-vm-sku-availability"></a>Kontrollera tillgänglighet för SKU för virtuell dator
Tillgängligheten för VM-storlek eller SKU: er kan variera beroende på region och zon. När du planerar för användningen av tillgänglighetszoner kan du visa tillgängliga VM SKU:er via Azure-region och zon. Den möjligheten säkerställer att du väljer en lämplig VM-storlek och hämtar önskad elasticitet i flera zoner. Mer information om olika VM-typer och -storlekar finns i [Översikt över VM-storlekar](../sizes.md).

Du kan se tillgängliga VM SKU:er med kommandot [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Följande exempel visar tillgängliga VM SKU:er i regionen *usaöstra2*:

```powershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("eastus2")};
```

Utdata liknar följande komprimerade exempel som visar tillgänglighetszoner som varje VM-storlek är tillgänglig i:

```powershell
ResourceType                Name  Location      Zones   [...]
------------                ----  --------      -----
virtualMachines  Standard_DS1_v2   eastus2  {1, 2, 3}
virtualMachines  Standard_DS2_v2   eastus2  {1, 2, 3}
[...]
virtualMachines     Standard_F1s   eastus2  {1, 2, 3}
virtualMachines     Standard_F2s   eastus2  {1, 2, 3}
[...]
virtualMachines  Standard_D2s_v3   eastus2  {1, 2, 3}
virtualMachines  Standard_D4s_v3   eastus2  {1, 2, 3}
[...]
virtualMachines   Standard_E2_v3   eastus2  {1, 2, 3}
virtualMachines   Standard_E4_v3   eastus2  {1, 2, 3}
```


## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I det här exemplet ska vi skapa en resursgrupp med namnet *myResourceGroup* i regionen *eastus2*. 

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS2
```

## <a name="create-networking-resources"></a>Skapa nätverksresurser

### <a name="create-a-virtual-network-subnet-and-a-public-ip-address"></a>Skapa ett virtuellt nätverk, undernät och offentlig IP-adress 
Dessa resurser används för att skapa nätverksanslutning till den virtuella datorn och ansluta den till internet. Skapa IP-adressen i en tillgänglighetszon, *2* i det här exemplet. I ett senare steg skapar du den virtuella datorn i samma zon som du använde när du skapade IP-adressen.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myVNet -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address in an availability zone and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName myResourceGroup -Location eastus2 -Zone 2 `
    -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)" -Sku Standard
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Skapa en nätverkssäkerhetsgrupp och en regel för nätverkssäkerhetsgrupp 
Nätverkssäkerhetsgruppen skyddar den virtuella datorn med hjälp av regler för inkommande och utgående trafik. I detta fall skapas en regel för inkommande trafik för port 3389, som tillåter inkommande anslutningar till fjärrskrivbord. Vi vill också skapa en regel för inkommande trafik för port 80, som tillåter inkommande webbtrafik.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 3389 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
    -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
    -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location eastus2 `
    -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Skapa ett nätverkskort för den virtuella datorn 
Skapa ett nätverkskort med [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) för den virtuella datorn. Nätverkskortet ansluter den virtuella datorn till ett undernät, en nätverkssäkerhetsgrupp och offentlig IP-adress.

```powershell
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location eastus2 `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell datorkonfiguration. Den här konfigurationen innehåller de inställningar som används vid distribution av den virtuella datorn, t.ex. den virtuella datorns avbildning, storlek och konfiguration för verifiering. Storleken *Standard_DS1_v2* i det här exemplet stöds i tillgänglighetszoner. Konfigurationen anger också den tillgänglighetszon du ställer in när du skapar IP-adressen. När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

```powershell
# Define a credential object
$cred = Get-Credential

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS1_v2 -Zone 2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
```

Skapa den virtuella datorn med [New-AzVM](/powershell/module/az.compute/new-azvm).

```powershell
New-AzVM -ResourceGroupName myResourceGroup -Location eastus2 -VM $vmConfig
```

## <a name="confirm-zone-for-managed-disk"></a>Bekräfta zon för hanterad disk

Du skapade den virtuella datorns IP-adressresurs i samma tillgänglighetszon som den virtuella datorn. Den hanterade diskresursen för den virtuella datorn skapas i samma tillgänglighetszon. Du kan kontrollera detta med [Get-AzDisk](/powershell/module/az.compute/get-azdisk):

```powershell
Get-AzDisk -ResourceGroupName myResourceGroup
```

Utdata visar att de hanterade diskarna är i samma tillgänglighetszon som den virtuella datorn:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx//resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en virtuell dator i en tillgänglighetszon. Lär dig mer om [tillgänglighet](../availability.md) för virtuella Azure-datorer.