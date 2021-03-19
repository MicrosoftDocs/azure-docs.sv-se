---
title: Hantering av Server dels pooler
titleSuffix: Azure Load Balancer
description: Kom igång och lär dig hur du konfigurerar och hanterar backend-poolen för en Azure Load Balancer
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: c49a721a4db758965c9cf8d71f5d73b5754b6088
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654483"
---
# <a name="backend-pool-management"></a>Hantering av Server dels pooler
Backend-poolen är en kritisk komponent i belastningsutjämnaren. Backend-poolen definierar den grupp av resurser som kommer att betjäna trafik för en specifik belastnings Utjämnings regel.

Det finns två sätt att konfigurera en backend-pool:
* Nätverks gränssnitts kort (NIC)
* Kombination av resurs-ID för IP-adress och Virtual Network (VNET)

Konfigurera din backend-pool med NIC när du använder befintliga virtuella datorer och skalnings uppsättningar för virtuella datorer. Den här metoden skapar den mest direkta länken mellan resursen och backend-poolen. 

När du förallokerar din backend-pool med ett IP-adressintervall som du planerar att senare skapa virtuella datorer och skalnings uppsättningar för virtuella datorer, konfigurerar du din backend-pool med en kombination av IP-adress och VNET-ID.

Du kan konfigurera IP-baserade och NIC-baserade backend-pooler för samma belastningsutjämnare men du kan inte skapa en enda server dels uppsättning som blandar de adresser som är riktade mot NIC-och IP-adresser inom samma pool.

Konfigurations avsnitten i den här artikeln kommer att fokuseras på:

* Azure PowerShell
* Azure CLI
* REST-API
* Azure Resource Manager-mallar 

Dessa avsnitt ger insikter om hur backend-pooler är strukturerade för varje konfigurations alternativ.

## <a name="configuring-backend-pool-by-nic"></a>Konfigurera backend-poolen via NIC
Backend-poolen skapas som en del av belastnings Utjämnings åtgärden. NÄTVERKSKORTets IP-konfigurationsfil används för att lägga till medlemmar i Server delen.

Följande exempel fokuserar på åtgärder för att skapa och fylla i backend-poolen för att markera det här arbets flödet och relationen.

  >[!NOTE] 
  >Det är viktigt att Observera att backend-pooler som kon figurer ATS via nätverks gränssnittet inte kan uppdateras som en del av en åtgärd på backend-poolen. Eventuella tillägg eller borttagningar av Server dels resurser måste ske i resursens nätverks gränssnitt.

### <a name="powershell"></a>PowerShell
Skapa en ny backend-pool:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Skapa ett nytt nätverks gränssnitt och Lägg till det i backend-poolen:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

Hämta information om Server delens pool för belastningsutjämnaren för att bekräfta att nätverks gränssnittet har lagts till i backend-poolen:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Skapa en ny virtuell dator och bifoga nätverks gränssnittet för att placera det i backend-poolen:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Skapa backend-poolen:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Skapa ett nytt nätverks gränssnitt och Lägg till det i backend-poolen:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

Hämta backend-poolen för att bekräfta att IP-adressen har lagts till korrekt:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Skapa en ny virtuell dator och bifoga nätverks gränssnittet för att placera det i backend-poolen:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="resource-manager-template"></a>Resource Manager-mall

Följ den här [snabb starts guiden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) för att distribuera en belastningsutjämnare och virtuella datorer och lägga till de virtuella datorerna i backend-poolen via nätverks gränssnittet.

Följ den här [snabb starts guiden](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-ip-configured-backend-pool) för att distribuera en belastningsutjämnare och virtuella datorer och lägga till de virtuella datorerna i backend-poolen via IP-adress.


## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>Konfigurera backend-poolen med IP-adress och virtuellt nätverk
Använd IP och Virtual Network i scenarier med förifyllda backend-pooler.

All hantering av backend-pooler görs direkt på objektet för Server delen som marker ATS i exemplen nedan.

### <a name="powershell"></a>PowerShell
Skapa ny backend-pool:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Uppdatera backend-poolen med en ny IP-adress från det befintliga virtuella nätverket:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

Hämta information om Server delens pool för belastningsutjämnaren för att bekräfta att backend-adresserna har lagts till i backend-poolen:

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Skapa ett nätverks gränssnitt och Lägg till det i backend-poolen. Ange IP-adressen till en av Server dels adresserna:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Skapa en virtuell dator och koppla NÄTVERKSKORTet till en IP-adress i backend-poolen:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Med CLI kan du antingen fylla i backend-poolen via kommando rads parametrar eller via en JSON-konfigurationsfil. 

Skapa och fyll i backend-poolen via kommando rads parametrarna:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

Skapa och fyll i backend-poolen via JSON-konfigurations filen:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

JSON-konfigurations fil:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Hämta information om Server delens pool för belastningsutjämnaren för att bekräfta att backend-adresserna har lagts till i backend-poolen:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Skapa ett nätverks gränssnitt och Lägg till det i backend-poolen. Ange IP-adressen till en av Server dels adresserna:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Skapa en virtuell dator och koppla NÄTVERKSKORTet till en IP-adress i backend-poolen:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
 
### <a name="limitations"></a>Begränsningar
En backend-pool som kon figurer ATS av IP-adressen har följande begränsningar:
  * Kan endast användas för standard belastnings utjämning
  * Begränsning på 100 IP-adresser i backend-poolen
  * Server dels resurserna måste finnas i samma virtuella nätverk som belastningsutjämnaren
  * En Load Balancer med IP-baserad backend-pool kan inte fungera som en privat länk tjänst
  * Den här funktionen stöds inte för närvarande i Azure Portal
  * ACI-behållare stöds för närvarande inte av den här funktionen
  * Belastnings utjämning eller tjänster som Application Gateway kan inte placeras i backend-poolen för belastningsutjämnaren
  * Inkommande NAT-regler kan inte anges via IP-adress

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig om Azure Load Balancer hantering av Server dels pooler och hur du konfigurerar en backend-pool med IP-adress och virtuellt nätverk.

Läs mer om [Azure Load Balancer](load-balancer-overview.md).

Granska [REST API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerbackendaddresspools/createorupdate) för IP-baserad backendpool-hantering.
