---
title: Snabb start – skapa en privat Azure-slutpunkt med Azure PowerShell
description: Använd den här snabb starten för att lära dig hur du skapar en privat slut punkt med hjälp av Azure PowerShell.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: afd1f8b6c80cfd7aa8d5142841458d76511a9e96
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562919"
---
# <a name="quickstart-create-an-azure-private-endpoint-using-azure-powershell"></a>Snabb start: skapa en privat Azure-slutpunkt med Azure PowerShell

Kom igång med en privat Azure-länk genom att använda en privat slut punkt för att ansluta säkert till en Azure-webbapp.

I den här snabb starten skapar du en privat slut punkt för en Azure-webbapp och distribuerar en virtuell dator för att testa den privata anslutningen.  

Privata slut punkter kan skapas för olika typer av Azure-tjänster, till exempel Azure SQL och Azure Storage.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* En Azure-webbapp med en **PremiumV2-** eller högre App Service-plan distribuerad i din Azure-prenumeration.  
    * Mer information och ett exempel finns i [snabb start: skapa en ASP.net Core webbapp i Azure](../app-service/quickstart-dotnetcore.md). 
    * En detaljerad själv studie kurs om hur du skapar en webbapp och en slut punkt finns i [Självstudier: Anslut till en webbapp med en privat Azure-slutpunkt](tutorial-private-endpoint-webapp-portal.md).

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln version 5.4.1 eller senare av Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
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
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>Skapa privat slut punkt

I det här avsnittet ska du skapa en privat slut punkt och anslutning med:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
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

3. Välj **CreatePrivateEndpointQS-RG**.

4. Välj **myVM**.

5. På sidan Översikt för **myVM** väljer du **Anslut** sedan **skydds**.

6. Välj knappen blå **användnings skydds** .

7. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

8. Öppna Windows PowerShell på servern när du har anslutit.

9. Ange `nslookup <your-webapp-name>.azurewebsites.net`. Ersätt **\<your-webapp-name>** med namnet på den webbapp som du skapade i föregående steg.  Du får ett meddelande som liknar det som visas nedan:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    En privat IP-adress för **10.0.0.5** returneras för namnet på webb programmet.  Adressen finns i under nätet för det virtuella nätverk som du skapade tidigare.

10. Öppna Internet Explorer i skydds-anslutningen till **myVM**.

11. Ange URL: en för din webbapp, **https:// \<your-webapp-name> . azurewebsites.net**.

12. Du får standard sidan webbapp om ditt program inte har distribuerats:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Standard webb program sida." border="true":::

13. Stäng anslutningen till **myVM**.

## <a name="clean-up-resources"></a>Rensa resurser 
När du är klar med den privata slut punkten och den virtuella datorn använder du [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla resurser den har:

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en:

* Virtuellt nätverk och skydds-värd.
* Virtuell dator.
* Privat slut punkt för en Azure-webbapp.

Du använde den virtuella datorn för att testa anslutningen på ett säkert sätt till webbappen över den privata slut punkten.

Mer information om de tjänster som stöder en privat slut punkt finns i:
> [!div class="nextstepaction"]
> [Tillgänglighet för privat länk](private-link-overview.md#availability)
