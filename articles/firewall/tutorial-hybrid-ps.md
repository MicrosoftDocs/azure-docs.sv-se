---
title: Distribuera & konfigurera Azure-brandväggen i hybrid nätverk med PowerShell
description: I den här artikeln får du lära dig hur du distribuerar och konfigurerar Azure-brandväggen med hjälp av Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 08/28/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: e60c829831bde3b454ab180d1a39ec46cb346963
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94658663"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Distribuera och konfigurera Azure Firewall i ett hybridnätverk med hjälp av Azure PowerShell

När du ansluter ditt lokala nätverk till ett virtuellt Azure-nätverk för att skapa ett hybridnätverk är förmågan att styra åtkomst till dina Azure-nätverksresurser en viktig del av den övergripande säkerhetsplanen.

Du kan använda Azure Firewall för att styra nätverksåtkomst i ett hybridnätverk med hjälp av regler som definierar tillåten respektive nekad nätverkstrafik.

I den här artikeln skapar du tre virtuella nätverk:

- **VNet-Hub** – brandväggen finns i det här virtuella nätverket.
- **VNet-Spoke** – det virtuella ekernätverket representerar den arbetsbelastning som finns på Azure.
- **VNet-Onprem** – det lokala virtuella nätverket representerar ett lokalt nätverk. I en verklig distribution kan den anslutas antingen via en VPN-eller ExpressRoute-anslutning. För enkelhetens skull använder den här artikeln en VPN gateway-anslutning och ett Azure-placerat virtuellt nätverk används för att representera ett lokalt nätverk.

![Brandvägg i ett hybridnätverk](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

I den här artikeln kan du se hur du:

* Deklarera variablerna
* Skapa brandväggens virtuella hubbnätverk
* Skapa det virtuella ekernätverket
* Skapa det lokala virtuella nätverket
* Konfigurera och distribuera brandväggen
* Skapa och ansluta VPN-gatewayer
* Peera de virtuella hubb- och ekernätverken
* Skapa vägarna
* Skapa de virtuella datorerna
* testa brandväggen.

Om du vill använda Azure Portal i stället för att slutföra den här kursen, se [Självstudier: Distribuera och konfigurera Azure-brandväggen i ett hybrid nätverk med hjälp av Azure Portal](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln kräver att du kör PowerShell lokalt. Du måste ha installerat Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Login-AzAccount` för att skapa en anslutning till Azure.

Det finns tre viktiga krav för att det här scenariot ska fungera korrekt:

- En användardefinierad väg (UDR, User-Defined Route) i ekerundernätet som pekar på IP-adressen för Azure Firewall som standardgateway. Väg spridning för virtuell nätverksgateway måste **inaktive ras** i den här routningstabellen.
- En UDR i hubbgatewayens undernät måste peka på brandväggens IP-adress som nästa hopp till ekernätverken.

   Det krävs ingen UDR i Azure Firewall-undernätet eftersom det lär sig vägarna från BGP.
- Se till att ange **AllowGatewayTransit** vid peering av VNet-Hub till VNet-Spoke och **UseRemoteGateways** vid peering av VNet-Spoke till VNet-Hub.

Se avsnittet [skapa vägar](#create-the-routes) i den här artikeln för att se hur dessa vägar skapas.

>[!NOTE]
>Azure Firewall måste ha direktanslutning till internet. Om din AzureFirewallSubnet lär sig en standard väg till ditt lokala nätverk via BGP måste du åsidosätta detta med en 0.0.0.0/0-UDR med **NextHopType** -värdet som **Internet** för att upprätthålla direkt Internet anslutning.
>
>Azure-brandväggen kan konfigureras för att stödja Tvingad tunnel trafik. Mer information finns i [Tvingad tunnel trafik i Azure Firewall](forced-tunneling.md).

>[!NOTE]
>Trafiken mellan direkt peerkopplade virtuella nätverk dirigeras direkt även om en UDR pekar på Azure Firewall som standardgateway. För att undernät till undernät-trafik ska kunna skickas till brandväggen i det här scenariot måste en UDR uttryckligen innehålla nätverksprefixet för målundernätverket på båda undernäten.

Om du vill läsa relaterad referensdokumentation för Azure PowerShell går du till [Azure PowerShell-referensen](/powershell/module/az.network/new-azfirewall).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="declare-the-variables"></a>Deklarera variablerna

I följande exempel deklaras variablerna med hjälp av värdena för den här artikeln. I vissa fall kan du behöva ersätta vissa värden med dina egna för att de ska fungera i din prenumeration. Ändra variablerna om det behövs och kopiera och klistra in dem i PowerShell-konsolen.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Skapa brandväggens virtuella hubbnätverk

Skapa först en resurs grupp som innehåller resurserna för den här artikeln:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Definiera de undernät som ska ingå i det virtuella nätverket:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Skapa nu brandväggens virtuella hubbnätverk:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Begär en offentlig IP-adress som ska allokeras till den VPN-gateway som du skapar för det virtuella nätverket. Observera att *AllocationMethod* är **Dynamic**. Du kan inte ange den IP-adress som du vill använda. Den allokeras dynamiskt till VPN-gatewayen.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Skapa det virtuella ekernätverket

Definiera de undernät som ska ingå i det virtuella ekernätverket:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Skapa det virtuella ekernätverket:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Skapa det lokala virtuella nätverket

Definiera de undernät som ska ingå i det virtuella nätverket:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Skapa nu det lokala virtuella nätverket:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Begär en offentlig IP-adress som ska allokeras till den gateway som du skapar för det virtuella nätverket. Observera att *AllocationMethod* är **Dynamic**. Du kan inte ange den IP-adress som du vill använda. Den allokeras dynamiskt till gatewayen.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Konfigurera och distribuera brandväggen

Distribuera nu brandväggen till det virtuella hubbnätverket.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Konfigurera nätverksregler

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Skapa och ansluta VPN-gatewayer

De virtuella hubbnätverken och de lokala virtuella nätverken ansluts via VPN-gatewayer.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Skapa en VPN-gateway för det virtuella hubbnätverket

Skapa VPN-gateway-konfigurationen. VPN-gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Skapa nu VPN-gatewayen för det virtuella hubbnätverket. Nätverk-till-nätverk-konfigurationer kräver VpnType RouteBased. Att skapa en VPN-gateway kan ofta ta 45 minuter eller mer, beroende på vald VPN-gateway-SKU.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Skapa en VPN-gateway för det lokala virtuella nätverket

Skapa VPN-gateway-konfigurationen. VPN-gateway-konfigurationen definierar undernätet och den offentliga IP-adress som ska användas.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Skapa nu VPN-gatewayen för det lokala virtuella nätverket. Nätverk-till-nätverk-konfigurationer kräver VpnType RouteBased. Att skapa en VPN-gateway kan ofta ta 45 minuter eller mer, beroende på vald VPN-gateway-SKU.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Skapa VPN-anslutningarna

Nu kan du skapa VPN-anslutningarna mellan hubb-gatewayerna och de lokala gatewayerna

#### <a name="get-the-vpn-gateways"></a>Hämta VPN-gatewayerna

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Skapa anslutningarna

I det här steget skapar du anslutningen från det virtuella hubbnätverket till det lokala virtuella nätverket. Du ser en delad nyckel som refereras i exemplen. Du kan använda egna värden för den delade nyckeln. Det är viktigt att den delade nyckeln matchar båda anslutningarna. Att skapa en anslutning kan ta en stund att slutföra.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Skapa anslutningen mellan det lokala virtuella nätverket och det virtuella hubbnätverket. Det här steget liknar det föregående steget förutom att du skapar anslutningen från Vnet-Onprem till VNet-hub. Kontrollera att de delade nycklarna matchar. Anslutningen upprättas efter några minuter.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Verifiera anslutningen

Du kan verifiera en lyckad anslutning med hjälp av cmdleten *Get-AzVirtualNetworkGatewayConnection* , med eller utan *-Debug*. Använd följande cmdlet-exempel genom att konfigurera värdena för att matcha dina egna. Vid uppmaning väljer du **A** för att köra **Alla**. I exemplet avser *-Name* namnet på den anslutning som du vill testa.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Visa värdena när cmdleten är klar. I följande exempel visas anslutningsstatusen som *Ansluten* och du kan se ingående och utgående byte.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Peera de virtuella hubb- och ekernätverken

Peera nu de virtuella hubb- och ekernätverken.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Skapa vägarna

Därefter skapar du några vägar:

- En väg från hubbgateway-undernätet till ekerundernätet via brandväggens IP-adress
- En standardväg från ekerundernätet via brandväggens IP-adress

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu ekerarbetsbelastningen och de lokala virtuella datorerna, och placera dem i respektive undernät.

### <a name="create-the-workload-virtual-machine"></a>Skapa den virtuella arbetsbelastningsdatorn

Skapa en virtuell dator i det virtuella ekernätverket som kör IIS, saknar offentlig IP-adress och tillåter ingående pingar.
När du uppmanas anger du användarnamn och lösenord för den virtuella datorn.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Skapa den lokala virtuella datorn

Det här är en enkel virtuell dator som du använder för att ansluta med hjälp av Fjärrskrivbord till den offentliga IP-adressen. Därifrån kan du sedan ansluta till den lokala servern via brandväggen. När du uppmanas anger du användarnamn och lösenord för den virtuella datorn.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>testa brandväggen.

Först hämtar du och sedan antecknar den privata IP-adressen för den virtuella datorn **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Från Azure-portalen ansluter du till den virtuella datorn **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Öppna en webbläsare på **VM-OnPrem** och gå till http:// \<VM-spoke-01 private IP\> .

Du bör se standardsidan för Internet Information Services.

Från **VM-Onprem** öppnar du ett fjärrskrivbord till **VM-spoke-01** på den privata IP-adressen.

Anslutningen bör upprättas, och du bör kunna logga in med ditt valda användarnamn och lösenord.

Nu har du verifierat att brand Väggs reglerna fungerar:

<!---- You can ping the server on the spoke VNet.--->
- Du kan bläddra i webbservern på det virtuella ekernätverket.
- Du kan ansluta till servern på det virtuella ekernätverket med hjälp av RDP.

Ändra sedan brandväggsnätverksregelns insamlingsåtgärd till **Neka** för att verifiera att brandväggsreglerna fungerar som förväntat. Kör följande skript för att ändra regelns insamlingsåtgärd till **Neka**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Kör nu testerna igen. De bör alla misslyckas den här gången. Stäng alla befintliga fjärrskrivbord innan du testar de ändrade reglerna.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **FW-Hybrid-Test** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

Därefter kan du övervaka Azure Firewall-loggarna.

[Självstudie: Övervaka Azure Firewall-loggar](./firewall-diagnostics.md)