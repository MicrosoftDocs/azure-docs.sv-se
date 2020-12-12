---
title: Guide för att använda Azure PowerShell för att etablera SQL Server på virtuell Azure-dator
description: Innehåller steg-och PowerShell-kommandon för att skapa en virtuell Azure-dator med SQL Server Galleri avbildningar för virtuella datorer.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a3f51a07b274320d1cd9f12b33703d8ec7f21f49
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359667"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Använda Azure PowerShell för att etablera SQL Server på Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här guiden beskriver alternativ för att använda PowerShell för att etablera SQL Server på Azure Virtual Machines (VM). Ett strömlinjeformat Azure PowerShell exempel som förlitar sig på standardvärden finns i snabb starten för [SQL VM Azure PowerShell](sql-vm-create-powershell-quickstart.md).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

1. Öppna PowerShell och upprätta åtkomst till ditt Azure-konto genom att köra kommandot **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Ange dina autentiseringsuppgifter när du uppmanas att göra det. Använd samma e-postadress och lösenord som du använder för att logga in på Azure Portal.

## <a name="define-image-variables"></a>Definiera bildvariabler

Börja med att definiera ett antal variabler för att återanvända värden och förenkla skript skapandet. Ändra de parameter värden som du vill ha, men var medveten om namngivnings begränsningar som är relaterade till namn längd och specialtecken när du ändrar värdena som anges.

### <a name="location-and-resource-group"></a>Plats och resurs grupp

Definiera data området och resurs gruppen där du vill skapa andra VM-resurser.

Ändra som du vill och kör sedan dessa cmdlets för att initiera dessa variabler.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Lagrings egenskaper

Definiera lagrings kontot och vilken typ av lagring som ska användas av den virtuella datorn.

Ändra som du vill och kör sedan följande cmdlet för att initiera dessa variabler. Vi rekommenderar att du använder [Premium-SSD](../../../virtual-machines/disks-types.md#premium-ssd) för produktions arbets belastningar.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Nätverks egenskaper

Definiera de egenskaper som ska användas av nätverket på den virtuella datorn. 

- Nätverksgränssnitt
- Tilldelnings metod för TCP/IP
- Virtuellt nätverksnamn
- Namn på virtuellt undernät
- Intervall med IP-adresser för det virtuella nätverket
- Intervall med IP-adresser för under nätet
- Namn etikett för offentlig domän

Ändra som du vill och kör sedan denna cmdlet för att initiera dessa variabler.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Egenskaper för virtuell dator

Definiera följande egenskaper:

- Namn på virtuell dator
- Datornamn
- Storlek för virtuell dator
- Operativ system diskens namn för den virtuella datorn

Ändra som du vill och kör sedan denna cmdlet för att initiera dessa variabler.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Välj en SQL Server bild

Använd följande variabler för att definiera SQL Server avbildningen som ska användas för den virtuella datorn. 

1. Först visar en lista över alla SQL Server avbildnings erbjudanden med `Get-AzVMImageOffer` kommandot. Det här kommandot visar de aktuella avbildningarna som är tillgängliga i Azure Portal och även äldre avbildningar som bara kan installeras med PowerShell:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. I den här självstudien använder du följande variabler för att ange SQL Server 2017 på Windows Server 2016.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Ange sedan de tillgängliga versionerna för ditt erbjudande.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. I den här självstudien använder du SQL Server 2017 Developer Edition (**SQLDEV**). Developer Edition licensieras fritt för testning och utveckling och du betalar bara för kostnaden för att köra den virtuella datorn.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Med distributions modellen Resource Manager är det första objekt som du skapar resurs gruppen. Använd cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) för att skapa en Azure-resurs grupp och dess resurser. Ange de variabler som du tidigare har initierat för resurs gruppens namn och plats.

Kör denna cmdlet för att skapa din nya resurs grupp.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Den virtuella datorn kräver lagrings resurser för operativ system disken och för SQL Server data-och loggfiler. För enkelhetens skull skapar du en enskild disk för båda. Du kan lägga till ytterligare diskar senare med hjälp av cmdleten [Add-Azure disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) för att placera SQL Server data och loggfiler på dedikerade diskar. Använd cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) för att skapa ett standard lagrings konto i din nya resurs grupp. Ange de variabler som du tidigare har initierat för lagrings konto namnet, lagrings-SKU-namnet och platsen.

Kör denna cmdlet för att skapa ditt nya lagrings konto.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Det kan ta några minuter att skapa lagrings kontot.

## <a name="create-network-resources"></a>Skapa nätverksresurser

Den virtuella datorn kräver ett antal nätverks resurser för nätverks anslutningen.

* Varje virtuell dator kräver ett virtuellt nätverk.
* Ett virtuellt nätverk måste ha minst ett definierat undernät.
* Ett nätverks gränssnitt måste definieras med antingen en offentlig eller en privat IP-adress.

### <a name="create-a-virtual-network-subnet-configuration"></a>Skapa en under näts konfiguration för virtuellt nätverk

Börja med att skapa en under näts konfiguration för ditt virtuella nätverk. I den här självstudien skapar du ett standard-undernät med cmdleten [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) . Ange de variabler som du tidigare har initierat för under nätets namn och adressprefix.

> [!NOTE]
> Du kan definiera ytterligare egenskaper för det virtuella nätverkets under näts konfiguration med hjälp av den här cmdleten, men det ligger utanför den här självstudiens omfattning.

Kör denna cmdlet för att skapa din virtuella under näts konfiguration.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa sedan ditt virtuella nätverk i din nya resurs grupp med hjälp av cmdleten [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) . Ange de variabler som du tidigare har initierat för namn, plats och adressprefix. Använd under näts konfigurationen som du definierade i föregående steg.

Kör denna cmdlet för att skapa ditt virtuella nätverk.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Skapa den offentliga IP-adressen

Nu när du har definierat ditt virtuella nätverk måste du konfigurera en IP-adress för anslutning till den virtuella datorn. I den här självstudien skapar du en offentlig IP-adress med dynamisk IP-adressering som stöd för Internet anslutning. Använd cmdleten [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) för att skapa den offentliga IP-adressen i din nya resurs grupp. Ange de variabler som du tidigare har initierat för etiketten namn, plats, allokeringsmetod och DNS-domännamn.

> [!NOTE]
> Du kan definiera ytterligare egenskaper för den offentliga IP-adressen med hjälp av den här cmdleten, men det ligger utanför den inledande självstudiens omfång. Du kan också skapa en privat adress eller en adress med en statisk adress, men det är även utanför den här självstudiens omfång.

Kör denna cmdlet för att skapa din offentliga IP-adress.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>Skapa nätverks säkerhets gruppen

Skapa en nätverks säkerhets grupp för att skydda den virtuella datorn och SQL Server trafik.

1. Skapa först en regel för nätverks säkerhets grupp för fjärr skrivbord (RDP) för att tillåta RDP-anslutningar.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Konfigurera en regel för nätverks säkerhets grupp som tillåter trafik på TCP-port 1433. Om du gör det aktive RAS anslutningar till SQL Server via Internet.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Skapa nätverks säkerhets gruppen.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>Skapa nätverks gränssnittet

Nu är du redo att skapa nätverks gränssnittet för den virtuella datorn. Använd cmdleten [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) för att skapa nätverks gränssnittet i din nya resurs grupp. Ange namn, plats, undernät och offentlig IP-adress som definierats tidigare.

Kör denna cmdlet för att skapa ett nätverks gränssnitt.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Konfigurera ett VM-objekt

Nu när lagrings-och nätverks resurser har definierats är du redo att definiera beräknings resurser för den virtuella datorn.

- Ange storleken på den virtuella datorn och olika egenskaper för operativ systemet.
- Ange det nätverks gränssnitt som du skapade tidigare.
- Definiera Blob Storage.
- Ange operativ system disken.

### <a name="create-the-vm-object"></a>Skapa VM-objektet

Börja med att ange den virtuella datorns storlek. I den här självstudien anger du en DS13. Använd cmdleten [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) för att skapa ett konfigurerbart virtuellt dator objekt. Ange de variabler som du tidigare har initierat för namn och storlek.

Kör denna cmdlet för att skapa objektet för den virtuella datorn.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Skapa ett Credential-objekt för att lagra namn och lösen ord för autentiseringsuppgifter för lokal administratör

Innan du kan ange egenskaper för operativ systemet för den virtuella datorn måste du ange autentiseringsuppgifterna för det lokala administratörs kontot som en säker sträng. Använd cmdleten [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) för att åstadkomma detta.

Kör följande cmdlet. Du måste ange den virtuella datorns lokala administratörs namn och lösen ord i fönstret för begäran om PowerShell-autentiseringsuppgifter.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Ange egenskaper för operativ systemet för den virtuella datorn

Nu är du redo att ange den virtuella datorns egenskaper för operativ systemet med cmdleten [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) .

- Ange typ av operativ system som Windows.
- Kräv att den [virtuella dator agenten](../../../virtual-machines/extensions/agent-windows.md) ska installeras.
- Ange att cmdleten aktiverar automatisk uppdatering.
- Ange de variabler som du tidigare har initierat för namnet på den virtuella datorn, dator namnet och autentiseringsuppgiften.

Kör denna cmdlet för att ange egenskaper för operativ systemet för den virtuella datorn.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Lägg till nätverks gränssnittet på den virtuella datorn

Använd sedan cmdleten [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) för att lägga till nätverks gränssnittet med den variabel som du definierade tidigare.

Kör denna cmdlet för att ange nätverks gränssnittet för den virtuella datorn.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Ange Blob Storage-platsen för den disk som ska användas av den virtuella datorn

Ange sedan Blob Storage-platsen för den virtuella datorns disk med de variabler som du definierade tidigare.

Kör denna cmdlet för att ställa in Blob Storage-platsen.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Ange egenskaper för operativ system disken för den virtuella datorn

Ange sedan operativ systemets disk egenskaper för den virtuella datorn med cmdleten [set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) . 

- Ange att operativ systemet för den virtuella datorn kommer från en avbildning.
- Ange att cachelagring ska vara skrivskyddad (eftersom SQL Server installeras på samma disk).
- Ange de variabler som du tidigare har initierat för VM-namnet och operativ system disken.

Kör denna cmdlet för att ange egenskaper för operativ system disken för den virtuella datorn.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Ange plattforms avbildningen för den virtuella datorn

Det sista konfigurations steget är att ange plattforms avbildningen för den virtuella datorn. I den här självstudien använder du den senaste SQL Server 2016 CTP-avbildningen. Använd cmdleten [set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) för att använda den här avbildningen med de variabler som du definierade tidigare.

Kör denna cmdlet för att ange plattforms avbildningen för den virtuella datorn.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Skapa den virtuella SQL-datorn

Nu när du har slutfört konfigurations stegen är du redo att skapa den virtuella datorn. Använd cmdleten [New-AzVM](/powershell/module/az.compute/new-azvm) för att skapa den virtuella datorn med de variabler som du har definierat.

> [!TIP]
> Det kan ta några minuter att skapa den virtuella datorn.

Kör denna cmdlet för att skapa den virtuella datorn.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

Den virtuella datorn skapas.

> [!NOTE]
> Om du får ett fel meddelande om startdiagnostik kan du ignorera det. Ett standard lagrings konto skapas för startdiagnostik eftersom det angivna lagrings kontot för den virtuella datorns disk är ett Premium Storage-konto.

## <a name="install-the-sql-iaas-agent"></a>Installera SQL Iaas-agenten

SQL Server virtuella datorer stöder automatiserade hanterings funktioner med [SQL Server IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md). För att registrera SQL Server med tillägget kör kommandot [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) när den virtuella datorn har skapats. Ange licens typ för din SQL Server VM, välja mellan antingen betala per användning eller hämta egen licens via [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). Mer information om licensiering finns i [licensierings modell](licensing-model-azure-hybrid-benefit-ahb-change.md). 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```

Det finns tre sätt att registrera med tillägget: 
- [Automatiskt för alla aktuella och framtida virtuella datorer i en prenumeration](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuellt för en enskild virtuell dator](sql-agent-extension-manually-register-single-vm.md)
- [Manuellt för flera virtuella datorer i grupp](sql-agent-extension-manually-register-vms-bulk.md)


## <a name="stop-or-remove-a-vm"></a>Stoppa eller ta bort en virtuell dator

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Följande kommando stoppar den virtuella datorn men lämnar den tillgänglig för framtida bruk.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn med kommandot **Remove-AzResourceGroup**. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet.

## <a name="example-script"></a>Exempelskript

Följande skript innehåller det fullständiga PowerShell-skriptet för den här självstudien. Det förutsätter att du redan har konfigurerat Azure-prenumerationen att använda med kommandona **Connect-AzAccount** och **Select-AzSubscription** .

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Nästa steg

När du har skapat den virtuella datorn kan du:

- Ansluta till den virtuella datorn med RDP
- Konfigurera SQL Server inställningar i portalen för din virtuella dator, inklusive:
   - [Lagrings inställningar](storage-configuration.md) 
   - [Automatiserade hanterings uppgifter](sql-server-iaas-agent-extension-automate-management.md)
- [Konfigurera anslutning](ways-to-connect-to-sql.md)
- Ansluta klienter och program till den nya SQL Server-instansen