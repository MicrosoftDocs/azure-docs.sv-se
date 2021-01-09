---
title: Distribuera och konfigurera Azure-brandväggen med Azure PowerShell
description: I den här artikeln får du lära dig hur du distribuerar och konfigurerar Azure-brandväggen med hjälp av Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 12/03/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: dc36d45e226cffafb51cf7aa09ea6f0d528ee016
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051385"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-powershell"></a>Distribuera och konfigurera Azure-brandväggen med Azure PowerShell

En viktig del av en övergripande säkerhetsplan för nätverket är att kontrollera utgående nätverksåtkomst. Du kanske till exempel vill begränsa åtkomsten till webbplatser. Eller så kanske du vill begränsa de utgående IP-adresserna och portarna som kan nås.

Med Azure Firewall kan du kontrollera åtkomsten till utgående nätverk från ett Azure-undernät. Med Azure Firewall kan du konfigurera:

* Programreglerna som definierar fullständigt kvalificerade domännamn (FQDN) kan nås från ett undernät.
* Nätverksregler som definierar källadress, protokoll, målport och måladress.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

I den här artikeln skapar du ett förenklat enda VNet med tre undernät för enkel distribution. För produktions distributioner rekommenderas en [nav-och eker-modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) , där brand väggen finns i ett eget VNet. Arbets belastnings servrarna finns i peer-virtuella nätverk i samma region med ett eller flera undernät.

* **AzureFirewallSubnet** – brandväggen ligger i det här undernätet.
* **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.
* **AzureBastionSubnet** – det undernät som används för Azure skydds, som används för att ansluta till arbets belastnings servern. Mer information om Azure skydds finns i [Vad är Azure skydds?](../bastion/bastion-overview.md)

![Självstudie om nätverksinfrastruktur](media/deploy-ps/tutorial-network.png)

I den här artikeln kan du se hur du:


* konfigurera en testnätverksmiljö
* distribuera en brandvägg
* Skapa en standardväg
* Konfigurera en program regel för att tillåta åtkomst till www.google.com
* Konfigurera en nätverksregel för att tillåta åtkomst till externa DNS-servrar
* testa brandväggen.

Om du vill kan du slutföra den här proceduren med hjälp av [Azure Portal](tutorial-firewall-deploy-portal.md).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver att du kör PowerShell lokalt. Du måste ha installerat Azure PowerShell-modulen. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). När du har verifierat PowerShell-versionen kör du `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="set-up-the-network"></a>Konfigurera nätverket

Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Sedan skapa ett virtuellt nätverk, undernät och testservrar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resurs gruppen innehåller alla resurser för distributionen.

```azurepowershell
New-AzResourceGroup -Name Test-FW-RG -Location "East US"
```

### <a name="create-a-virtual-network-and-azure-bastion-host"></a>Skapa ett virtuellt nätverk och en Azure skydds-värd

Det här virtuella nätverket har tre undernät:

> [!NOTE]
> Storleken på AzureFirewallSubnet-undernätet är/26. Mer information om under näts storleken finns i [vanliga frågor och svar om Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

```azurepowershell
$Bastionsub = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.0.0/27
$FWsub = New-AzVirtualNetworkSubnetConfig -Name AzureFirewallSubnet -AddressPrefix 10.0.1.0/26
$Worksub = New-AzVirtualNetworkSubnetConfig -Name Workload-SN -AddressPrefix 10.0.2.0/24
```
Skapa det virtuella nätverket nu:

```azurepowershell
$testVnet = New-AzVirtualNetwork -Name Test-FW-VN -ResourceGroupName Test-FW-RG `
-Location "East US" -AddressPrefix 10.0.0.0/16 -Subnet $Bastionsub, $FWsub, $Worksub
```
### <a name="create-public-ip-address-for-azure-bastion-host"></a>Skapa en offentlig IP-adress för Azure skydds-värden

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName Test-FW-RG -Location "East US" `
   -Name Bastion-pip -AllocationMethod static -Sku standard
```

### <a name="create-azure-bastion-host"></a>Skapa en Azure skydds-värd

```azurepowershell
New-AzBastion -ResourceGroupName Test-FW-RG -Name Bastion-01 -PublicIpAddress $publicip -VirtualNetwork $testVnet
```
### <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Skapa nu den virtuella arbets belastnings datorn och placera den i lämpligt undernät.
När du uppmanas anger du användarnamn och lösenord för den virtuella datorn.


Skapa en virtuell arbets belastnings dator.
När du uppmanas anger du användarnamn och lösenord för den virtuella datorn.

```azurepowershell
#Create the NIC
$wsn = Get-AzVirtualNetworkSubnetConfig -Name  Workload-SN -VirtualNetwork $testvnet
$NIC01 = New-AzNetworkInterface -Name Srv-Work -ResourceGroupName Test-FW-RG -Location "East us" -Subnet $wsn

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName Srv-Work -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Srv-Work -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC01.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2019-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName Test-FW-RG -Location "East US" -VM $VirtualMachine -Verbose
```

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Distribuera nu brand väggen till det virtuella nätverket.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName Test-FW-RG `
  -Location "East US" -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name Test-FW01 -ResourceGroupName Test-FW-RG -Location "East US" -VirtualNetwork $testVnet -PublicIpAddress $FWpip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP
```

Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>Skapa en standardväg

Skapa en tabell med avaktiverad BGP Route-spridning

```azurepowershell
$routeTableDG = New-AzRouteTable `
  -Name Firewall-rt-table `
  -ResourceGroupName Test-FW-RG `
  -location "East US" `
  -DisableBgpRoutePropagation

#Create a route
 Add-AzRouteConfig `
  -Name "DG-Route" `
  -RouteTable $routeTableDG `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $testVnet `
  -Name Workload-SN `
  -AddressPrefix 10.0.2.0/24 `
  -RouteTable $routeTableDG | Set-AzVirtualNetwork
```

## <a name="configure-an-application-rule"></a>Konfigurera en programregel

Med program regeln tillåts utgående åtkomst till www.google.com.

```azurepowershell
$AppRule1 = New-AzFirewallApplicationRule -Name Allow-Google -SourceAddress 10.0.2.0/24 `
  -Protocol http, https -TargetFqdn www.google.com

$AppRuleCollection = New-AzFirewallApplicationRuleCollection -Name App-Coll01 `
  -Priority 200 -ActionType Allow -Rule $AppRule1

$Azfw.ApplicationRuleCollections.Add($AppRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Mer information finns i [Infrastruktur-FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

Nätverks regeln tillåter utgående åtkomst till två IP-adresser i port 53 (DNS).

```azurepowershell
$NetRule1 = New-AzFirewallNetworkRule -Name "Allow-DNS" -Protocol UDP -SourceAddress 10.0.2.0/24 `
   -DestinationAddress 209.244.0.3,209.244.0.4 -DestinationPort 53

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 200 `
   -Rule $NetRule1 -ActionType "Allow"

$Azfw.NetworkRuleCollections.Add($NetRuleCollection)

Set-AzFirewall -AzureFirewall $Azfw
```

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändra den primära och sekundära DNS-adressen för nätverksgränssnittet **Srv-Work**

Konfigurera serverns primära och sekundära DNS-adresser i test syfte i den här proceduren. Detta är inte ett allmänt krav för Azure-brandvägg.

```azurepowershell
$NIC01.DnsSettings.DnsServers.Add("209.244.0.3")
$NIC01.DnsSettings.DnsServers.Add("209.244.0.4")
$NIC01 | Set-AzNetworkInterface
```

## <a name="test-the-firewall"></a>testa brandväggen.

Testa nu brand väggen för att bekräfta att den fungerar som förväntat.

1. Anslut till den virtuella datorn för **SRV-arbete** med skydds och logga in. 

   :::image type="content" source="media/deploy-ps/bastion.png" alt-text="Anslut med skydds.":::

3. Öppna ett PowerShell-fönster i **SRV-arbete** och kör följande kommandon:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Båda kommandona ska returnera svar, vilket visar att dina DNS-frågor går igenom brand väggen.

1. Kör följande kommandon:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   `www.google.com`Begär Anden ska lyckas och `www.microsoft.com` begär Anden ska Miss lyckas. Detta visar att brand Väggs reglerna fungerar som förväntat.

Nu har du verifierat att brand Väggs reglerna fungerar:

* Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.
* Du kan bläddra till en tillåten FQDN, men inte till andra.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brand Väggs resurser för nästa självstudie eller, om du inte längre behöver, ta bort resurs gruppen **test-VB-RG** för att ta bort alla brand Väggs resurser:

```azurepowershell
Remove-AzResourceGroup -Name Test-FW-RG
```

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Övervaka Azure Firewall-loggar](./firewall-diagnostics.md)
