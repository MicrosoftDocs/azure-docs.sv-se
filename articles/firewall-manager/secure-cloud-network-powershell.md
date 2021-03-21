---
title: 'Självstudie: skydda din virtuella hubb med Azure PowerShell'
description: Den här artikeln beskriver hur du skapar ett virtuellt Azure-nätverk i en region med Azure Firewall aktiverat i hubben.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 093b962bb9f42b660fb8cc5c0584ec67ebc87e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789169"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Självstudie: skydda din virtuella hubb med Azure PowerShell

I den här självstudien skapar du en virtuell WAN-instans med en virtuell hubb i en region och du distribuerar en Azure-brandvägg i den virtuella hubben för att skydda anslutningen. I det här exemplet visar vi säker anslutning mellan virtuella nätverk. Trafik mellan virtuella nätverk och plats-till-plats-, punkt-till-plats-eller ExpressRoute-grenar stöds också av virtuella säkra hubbar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Distribuera det virtuella WAN-nätverket
> * Distribuera Azure-brandväggen och konfigurera anpassad routning
> * Testa anslutning

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- PowerShell 7

   I den här självstudien krävs att du kör Azure PowerShell lokalt på PowerShell 7. Information om hur du installerar PowerShell 7 finns i [Migrera från Windows PowerShell 5,1 till PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7).
- AZ. Network-version 3.2.0

    Om du har AZ. Network version 3.4.0 eller senare måste du nedgradera för att kunna använda några av kommandona i den här självstudien. Du kan kontrol lera versionen av din AZ. Network-modul med kommandot `Get-InstalledModule -Name Az.Network` . Om du vill avinstallera AZ. Network-modulen kör du `Uninstall-Module -name az.network` . Om du vill installera modulen AZ. Network 3.2.0 kör du `Install-Module az.network -RequiredVersion 3.2.0 -force` .

## <a name="sign-in-to-azure"></a>Logga in på Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Första virtuella WAN-distribution

Som ett första steg måste du ange vissa variabler och skapa resurs gruppen, den virtuella WAN-instansen och den virtuella hubben:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Skapa två virtuella nätverk och Anslut dem till hubben som ekrar:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

Nu har du ett fullständigt fungerande virtuellt WAN-nätverk som tillhandahåller alla anslutningar. För att förbättra den med säkerhet måste du distribuera en Azure-brandvägg till varje virtuellt nav. Brand Väggs principer kan användas för att effektivt hantera den virtuella WAN-brand Väggs instansen. En brand Väggs princip skapas också i det här exemplet:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Att aktivera loggning från Azure-brandväggen till Azure Monitor är valfritt, men i det här exemplet använder du brand Väggs loggarna för att bevisa att trafiken passerar brand väggen:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Distribuera Azure-brandväggen och konfigurera anpassad routning

Nu har du en Azure-brandvägg i hubben, men du måste fortfarande ändra routning så att det virtuella WAN-nätverket skickar trafiken från de virtuella nätverken och från grenarna genom brand väggen. Du gör detta i två steg:

1. Konfigurera alla virtuella nätverks anslutningar (och förgrenings anslutningar om det fanns några) att sprida till `None` routningstabellen. Den här konfigurationen är att andra virtuella nätverk och grenar inte kommer att lära sig sina prefix, och därför har ingen routning för att komma åt dem.
1. Nu kan du infoga statiska vägar i `Default` routningstabellen (där alla virtuella nätverk och grenar är associerade som standard), så att all trafik skickas till Azure-brandväggen.

> [!NOTE]
> Detta är den konfiguration som distribueras när du skyddar anslutningen från Azure-portalen med Azure Firewall Manager

Börja med det första steget för att konfigurera dina virtuella nätverks anslutningar så att de sprids till `None` routningstabellen:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Nu kan du fortsätta med det andra steget för att lägga till statiska vägar i `Default` routningstabellen. I det här exemplet använder du standard konfigurationen som Azure Firewall Manager genererar vid anslutning i ett virtuellt WAN-nätverk, men du kan ändra listan över prefix i den statiska vägen så att den passar dina särskilda krav:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Testa anslutning

Nu har du en fullständigt fungerande säker hubb. Om du vill testa anslutningen behöver du en virtuell dator i varje eker virtuellt nätverk som är anslutet till hubben:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

Standard konfigurationen i brand Väggs principen är att ta bort allt. Du måste konfigurera vissa regler. Börja med DNAT-regler så att de virtuella test datorerna kan nås via brand väggens offentliga IP-adress:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Nu kan du konfigurera vissa exempel regler. Definiera en nätverks regel som tillåter SSH-trafik, plus en program regel som tillåter Internet åtkomst till det fullständigt kvalificerade domän namnet `ifconfig.co` . URL: en returnerar käll-IP-adressen som den ser i HTTP-begäran:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Innan du skickar någon trafik kan du kontrol lera de effektiva vägarna för de virtuella datorerna. De bör innehålla de prefix som har lärts från det virtuella WAN-nätverket ( `0.0.0.0/0` plus RFC1918), men inte prefixet för de andra ekrarna:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Generera nu trafik från en virtuell dator till en annan och kontrol lera att den släpps i Azure-brandväggen. I följande SSH-kommandon måste du acceptera finger avtryck för virtuella datorer och ange det lösen ord som du definierade när du skapade de virtuella datorerna. I det här exemplet ska du skicka fem ICMP Echo Request-paket från den virtuella datorn i spoke1 till spoke2, plus ett TCP-anslutningsfel på port 22 med Linux `nc` -verktyget (med `-vz` flaggorna skickar den bara en anslutningsbegäran och visar resultatet). Du bör se ping-åtgärden och TCP-anslutningsfel på port 22 lyckas, eftersom den tillåts av nätverks regeln som du konfigurerade tidigare:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Du kan också verifiera Internet trafik. HTTP-begäranden via verktyget `curl` till det FQDN som du tillåtit i brand Väggs principen ( `ifconfig.co` ) ska fungera, men http-förfrågningar till andra mål bör inte fungera (i det här exemplet testar du med `bing.com` ):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

Det enklaste sättet att kontrol lera att paketen ignoreras av brand väggen är att kontrol lera loggarna. Eftersom du konfigurerade Azure-brandväggen för att skicka loggar till Azure Monitor kan du hämta relevanta loggar från Azure Monitor med hjälp av Kusto-frågespråket:

> [!NOTE]
> Det kan ta cirka 1 minut innan loggarna visas för att skickas till Azure Monitor

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

I föregående kommando bör du se olika poster:

* SSH-anslutningen är DNAT'ed
* Ignorerade ICMP-paket mellan de virtuella datorerna i ekrarna (10.1.1.4 och 10.1.2.4)
* Tillåtna SSH-anslutningar mellan de virtuella datorerna i ekrarna

Här visas ett exempel på utdata som genereras av kommandot ovan:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Om du vill se loggarna för program reglerna (som beskriver tillåtna och nekade HTTP-anslutningar) eller ändra hur loggarna visas, kan du prova med andra KQL-frågor. Några exempel finns i [Azure Monitor loggar för Azure-brandväggen](../firewall/firewall-workbook.md).


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort test miljön kan du ta bort resurs gruppen med alla objekt som finns i listan:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om betrodda säkerhets partner](trusted-security-partners.md)