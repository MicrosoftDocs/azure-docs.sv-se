---
title: 'Självstudie: ansluta till en Azure SQL-Server med en privat Azure-slutpunkt – PowerShell'
description: Använd den här självstudien för att lära dig hur du skapar en Azure SQL Server med en privat slut punkt med hjälp av Azure PowerShell
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8668bdb9dc391582234bf5741c0dd287d026defd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554946"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-powershell"></a>Självstudie: ansluta till en Azure SQL-Server med hjälp av en privat Azure-slutpunkt – Azure PowerShell

Den privata Azure-slutpunkten är det grundläggande Bygg blocket för privat länk i Azure. Den gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera med privata länk resurser privat.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett virtuellt nätverk och en skydds-värd.
> * Skapa en virtuell dator.
> * Skapa en Azure SQL-Server och en privat slut punkt.
> * Testa anslutningen till den privata SQL Server-slutpunkten.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateSQLEndpointTutorial-rg' -Location 'eastus'
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Skapa ett virtuellt nätverk och en skydds-värd

I det här avsnittet ska du skapa ett virtuellt nätverk, ett undernät och en skydds-värd. 

Skydds-värden kommer att användas för att ansluta säkert till den virtuella datorn för att testa den privata slut punkten.

Skapa ett virtuellt nätverk och skydds-värd med:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)
* [New-AzBastion](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
```

Det kan ta några minuter innan Azure skydds-värden kan distribueras.

## <a name="create-test-virtual-machine"></a>Skapa virtuell test dator

I det här avsnittet ska du skapa en virtuell dator som ska användas för att testa den privata slut punkten.

Skapa den virtuella datorn med:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreateSQLEndpointTutorial-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-an-azure-sql-server"></a>Skapa en Azure SQL-Server

I det här avsnittet ska du skapa en SQL-Server och-databas med hjälp av:

* [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)
* [New-AzSQlDatabase](/powershell/module/az.sql/new-azsqldatabase)

Skapa SQL Server och databas. Ersätt **\<sql-server-name>** med ditt unika Server Namn:

```azurepowershell-interactive
## Set and administrator and password for the SQL server. ##
$cred = Get-Credential

## Create SQL server ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    SqlAdministratorCredentials = $cred
    Location = 'eastus'
}
New-AzSqlServer @parameters1

## Create database. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    DatabaseName = 'mysqldatabase'
    RequestedServiceObjectiveName = 'S0'
    SampleName = 'AdventureWorksLT'
}
New-AzSqlDatabase @parameters2
```

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet ska du skapa en privat slut punkt och anslutning med:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place SQL server into variable. Replace <sql-server-name> with your server name ##
$server = Get-AzSqlServer -ResourceGroupName CreateSQLEndpointTutorial-rg -ServerName <sql-server-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $server.ResourceID
    GroupID = 'sqlserver'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>Konfigurera den privata DNS-zonen

I det här avsnittet ska du skapa och konfigurera den privata DNS-zonen med:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'privatelink.database.windows.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ZoneName = 'privatelink.database.windows.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.database.windows.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>Testa anslutningen till privat slut punkt

I det här avsnittet ska du använda den virtuella datorn som du skapade i föregående steg för att ansluta till SQL Server över den privata slut punkten.

1. Logga in på [Azure-portalen](https://portal.azure.com) 
 
2. Välj **resurs grupper** i det vänstra navigerings fönstret.

3. Välj **CreateSQLEndpointTutorial-RG**.

4. Välj **myVM**.

5. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

6. Välj knappen blå **användnings skydds** .

7. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

8. Öppna Windows PowerShell på servern när du har anslutit.

9. Ange `nslookup <sqlserver-name>.database.windows.net`. Ersätt **\<sqlserver-name>** med namnet på den SQL Server som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    En privat IP-adress för **10.0.0.5** returneras för SQL Server-namnet.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.


10. Installera [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) på **myVM**.

11. Öppna **SQL Server Management Studio**.

12. I **Anslut till Server** anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | Servertyp | Välj **Databasmotor**.|
    | Servernamn | Ange **\<sql-server-name> . Database.Windows.net** |
    | Autentisering | Välj **SQL Server-autentisering**. |
    | Användarnamn | Ange det användar namn som du angav när servern skapades |
    | Lösenord | Ange lösen ordet som du angav när servern skapades |
    | Kom ihåg lösenordet | Välj **Ja**. |

13. Välj **Anslut**.

14. Bläddra bland databaser på den vänstra menyn.

15. Du kan också Skapa eller fråga efter information från **mysqldatabase**.

16. Stäng skydds-anslutningen till **myVM**. 

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten, SQL Server och den virtuella datorn tar du bort resurs gruppen och alla resurser den innehåller: 

1. Ange **CreateSQLEndpointTutorial-RG** i rutan **Sök** högst upp i portalen och välj **CreateSQLEndpointTutorial-RG** från Sök resultaten. 

2. Välj **Ta bort resursgrupp**. 

3. Ange **CreateSQLEndpointTutorial-RG** för **Skriv resurs gruppens namn** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en:

* Virtuellt nätverk och skydds-värd.
* Virtuell dator.
* Azure SQL Server med privat slut punkt.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till SQL Server över den privata slut punkten.

Som nästa steg kanske du också är intresse rad av arkitektur scenariot för **webbappen med privat anslutning till Azure SQL Database** , som ansluter ett webb program utanför det virtuella nätverket till en Databass privata slut punkt.
> [!div class="nextstepaction"]
> [Webbapp med privat anslutning till Azure SQL Database](/azure/architecture/example-scenario/private-web-app/private-web-app)