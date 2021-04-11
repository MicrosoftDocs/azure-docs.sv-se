---
title: Använd PowerShell för att distribuera Azure-Virtual Machines
description: Lär dig hur du använder Azure PowerShell för att distribuera Azure-Virtual Machines för att spara pengar.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802751"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Distribuera Azure-Virtual Machines med Azure PowerShell


Med hjälp av [Azure-Virtual Machines](../spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter då Azure behöver kapaciteten, tar Azure-infrastrukturen bort Azure-Virtual Machines. Därför är Azures Virtual Machines bra för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för Azure-Virtual Machines är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Mer information om hur du ställer in högsta pris finns i [Azure-Virtual Machines – prissättning](../spot-vms.md#pricing).

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en virtuell Azure-dator kan anges i kronor (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en spotVM med [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) för att skapa konfigurationen. Inkludera `-Priority Spot` och ange `-MaxPrice` antingen:
- `-1` Det innebär att den virtuella datorn inte avlägsnas utifrån priset.
- en dollar mängd, upp till 5 siffror. Till exempel `-MaxPrice .98765` innebär att den virtuella datorn kommer att frigöras när priset för en spotVM går ungefär $. 98765 per timme.


I det här exemplet skapas en spotVM som inte frigörs baserat på priser (endast när Azure behöver kapaciteten tillbaka). Borttagnings principen är inställd på att frigöra den virtuella datorn, så att den kan startas om vid ett senare tillfälle. Om du vill ta bort den virtuella datorn och den underliggande disken när den virtuella datorn avlägsnas, anger `-EvictionPolicy` du till `Delete` i `New-AzVMConfig` .


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

När den virtuella datorn har skapats kan du fråga om du vill se det högsta priset för alla virtuella datorer i resurs gruppen.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>Simulera en avtagning

Du kan simulera en avlägsnande av en virtuell Azure-dator med hjälp av REST, PowerShell eller CLI för att testa hur bra ditt program kommer att reagera på en plötslig avlägsning.

I de flesta fall ska du använda REST API [Virtual Machines-simulera avlägsnande](/rest/api/compute/virtualmachines/simulateeviction) för att hjälpa till med automatiserad testning av program. För REST `Response Code: 204` innebär det att den simulerade avtagningen lyckades. Du kan kombinera simulerade avvisningar med den [schemalagda händelse tjänsten](scheduled-events.md)för att automatisera hur appen kommer att svara när den virtuella datorn avlägsnas.

Om du vill se schemalagda händelser i praktiken kan du titta på [Azure fredag – använda azure schemalagda händelser för att förbereda för underhåll av virtuella datorer](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Kort guide

För ett snabb test för att visa hur en simulerad avlägsnande kommer att fungera, ska vi gå igenom den schemalagda händelse tjänsten och se hur den ser ut när du simulerar en avtagning med PowerShell.

Tjänsten schemalagd händelse tjänst är aktive rad för din tjänst första gången du gör en begäran om händelser. 

Fjärranslut till den virtuella datorn och öppna en kommando tolk. 

Från kommando tolken på den virtuella datorn skriver du:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Det här första svaret kan ta upp till 2 minuter. Från och med nu bör de visa utdata nästan omedelbart.

Från en dator där AZ PowerShell-modulen är installerad (till exempel din lokala dator) simulerar du en avtagning med [set-AzVM](https://docs.microsoft.com/powershell/module/az.compute/set-azvm). Ersätt resurs gruppens namn och namnet på den virtuella datorn med dina egna. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

Svarets utdata kommer att ha `Status: Succeeded` om begäran har gjorts.

Gå snabbt tillbaka till din fjärr anslutning till din virtuella dator och fråga Schemalagda händelser slut punkten igen. Upprepa följande kommando tills du får ett utdata som innehåller mer information:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

När den schemalagda händelse tjänsten får meddelandet om borttagning, får du ett svar som ser ut ungefär så här:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Du kan se att `"EventType":"Preempt"` och resursen är den virtuella dator resursen `"Resources":["myspotvm"]` . 

Du kan också se när den virtuella datorn kommer att avlägsnas genom att kontrol lera `"NotBefore"` värdet. Den virtuella datorn tas inte bort före den tid som anges i `NotBefore` , så det är ditt fönster för ditt program att stängas av korrekt.


## <a name="next-steps"></a>Nästa steg

Du kan också skapa en virtuell Azure-dator med hjälp av [Azure CLI](../linux/spot-cli.md), [portalen](../spot-portal.md) eller en [mall](../linux/spot-template.md).

Fråga aktuell pris information med hjälp av [Azures API för åter försäljning](/rest/api/cost-management/retail-prices/azure-retail-prices) för information om priser för Azure-priser för virtuella datorer. `meterName`Och `skuName` kommer båda att innehålla `Spot` .

Om du stöter på ett fel, se [felkoder](../error-codes-spot.md).
