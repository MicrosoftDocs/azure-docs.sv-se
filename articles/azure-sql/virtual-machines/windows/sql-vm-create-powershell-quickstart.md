---
title: Skapa SQL Server på en virtuell Windows-dator med Azure PowerShell | Microsoft Docs
description: Den här självstudien visar hur du använder Azure PowerShell för att skapa en virtuell Windows-dator som kör SQL Server 2017.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97356845"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Snabb start: skapa SQL Server på en virtuell Windows-dator med Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här snabb starten beskriver hur du skapar en SQL Server virtuell dator (VM) med Azure PowerShell.

> [!TIP]
> - I snabbstarten finns en sökväg för snabb etablering och anslutning till en SQL-VM. Mer information om andra Azure PowerShell-alternativ för att skapa virtuella SQL-datorer finns i [Etableringsguide för virtuella SQL Server-datorer med Azure PowerShell](create-sql-vm-powershell.md).
> - Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Skaffa en Azure-prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Skaffa Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Konfigurera PowerShell

1. Öppna PowerShell och upprätta åtkomst till ditt Azure-konto genom att köra kommandot **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. När du ser inloggnings fönstret anger du dina autentiseringsuppgifter. Använd samma e-postadress och lösenord som du använder för att logga in på Azure Portal.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Definiera en variabel med ett unikt resursgruppsnamn. För att förenkla resten av snabbstarten används i återstående kommandon det här namnet som grund för andra resursnamn.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Definiera en plats för en Azure-region som är mål för alla VM-resurser.

   ```powershell
   $Location = "East US"
   ```

1. Skapa resursgruppen.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Konfigurera nätverksinställningar

1. Skapa ett virtuellt nätverk, undernät och offentlig IP-adress. Dessa resurser används för att skapa nätverksanslutning till den virtuella datorn och ansluta den till internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Skapa en nätverkssäkerhetsgrupp. Konfigurera regler för att tillåta anslutning till fjärrskrivbord (RDP) och SQL Server-anslutningar.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Skapa nätverksgränssnittet.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Skapa den virtuella SQL-datorn

1. Definiera dina autentiseringsuppgifter för att logga in på den virtuella datorn. Användarnamnet är ”azureadmin”. Se till att du ändrar \<password> innan du kör kommandot.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Skapa ett konfigurationsobjekt för den virtuella datorn och sedan skapa den virtuella datorn. Följande kommando skapar en SQL Server 2017 Developer Edition VM på Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Det tar flera minuter att skapa den virtuella datorn.

## <a name="register-with-sql-vm-rp"></a>Registrera med SQL VM RP 

Om du vill hämta funktioner för Portal integrering och SQL-VM måste du registrera dig för [SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md).

För att få full funktionalitet måste du registrera dig med tillägget i fullständigt läge. Det innebär dock att du startar om den SQL Server tjänsten, så den rekommenderade metoden är att registrera i Lightweight-läge och sedan uppgradera till fullständig under underhålls perioden. 

Registrera först SQL Server VM i Lightweight-läge: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Uppgradera till fullständigt läge under ett underhålls fönster: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>Fjärrskrivbord på den virtuella datorn

1. Använd följande kommando för att hämta den offentliga IP-adressen för den nya virtuella datorn.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Skicka sedan den returnerade IP-adressen som kommandoradsparameter till **mstsc** för att starta en fjärrskrivbordssession på den nya virtuella datorn.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Ange autentiseringsuppgifter för ett annat konto när du tillfrågas om autentiseringsuppgifter. Ange användarnamnet med ett föregående omvänt snedstreck (till exempel `\azureadmin`), och det lösenord som du angav tidigare i den här snabbstarten.

## <a name="connect-to-sql-server"></a>Anslut till SQL Server

1. När du har loggat in i fjärrskrivbordssessionen kan du starta **SQL Server Management Studio 2017** från Start-menyn.

1. Behåll standardvärden i dialogrutan **Anslut till server**. Servernamnet är namnet på den virtuella datorn. Autentisering har angetts till **Windows-autentisering**. Välj **Anslut**.

Du är nu ansluten till SQL Server lokalt. Om du vill fjärrans luta måste du [Konfigurera anslutningen](ways-to-connect-to-sql.md) från Azure Portal eller manuellt.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte behöver köra den virtuella SQL-datorn kontinuerligt kan du undvika onödiga kostnader genom att stoppa den när den inte används. Följande kommando stoppar den virtuella datorn men lämnar den tillgänglig för framtida bruk.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Du kan även permanent ta bort alla resurser som är kopplade till den virtuella datorn med kommandot **Remove-AzResourceGroup**. Det här tar även permanent bort den virtuella datorn, så använd det här kommandot med försiktighet.

## <a name="next-steps"></a>Nästa steg

Med den här snabbstarten skapade du en virtuell SQL Server 2017-dator med Azure PowerShell. Mer information om hur du migrerar data till den nya SQL-servern finns i följande artikel.

> [!div class="nextstepaction"]
> [Migrera en databas till en virtuell SQL-dator](migrate-to-vm-from-sql-server.md)
