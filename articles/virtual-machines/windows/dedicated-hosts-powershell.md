---
title: Distribuera Azure-dedikerade värdar med hjälp av Azure PowerShell
description: Distribuera virtuella datorer till dedikerade värdar med hjälp av Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/12/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 4517c939e14f3157db606f087ef042c1ba7eba3e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672265"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Distribuera virtuella datorer till dedikerade värdar med hjälp av Azure PowerShell

Den här artikeln vägleder dig genom hur du skapar en dedikerad Azure- [värd](../dedicated-hosts.md) som värd för dina virtuella datorer. 

Kontrol lera att du har installerat Azure PowerShell version 2.8.0 eller senare och att du är inloggad på ett Azure-konto i med `Connect-AzAccount` . 

## <a name="limitations"></a>Begränsningar

- Skalnings uppsättningar för virtuella datorer stöds för närvarande inte på dedikerade värdar.
- De storlekar och maskin varu typer som är tillgängliga för dedikerade värdar varierar beroende på region. Mer information hittar du på [prissättnings sidan](https://aka.ms/ADHPricing) för värden.

## <a name="create-a-host-group"></a>Skapa en värd grupp

En **värd grupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värd grupp i en region och en tillgänglighets zon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträck över flera tillgänglighets zoner. I så fall måste du ha en värd grupp i var och en av de zoner som du vill använda.
- Sträck över flera fel domäner som är mappade till fysiska rack. 
 
I båda fallen måste du ange antalet fel domäner för värd gruppen. Om du inte vill spänna över fel domäner i gruppen använder du antalet fel domäner 1. 

Du kan också välja att använda både tillgänglighets zoner och fel domäner. I det här exemplet skapas en värd grupp i zon 1, med 2 fel domäner. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Lägg till `-SupportAutomaticPlacement true` parametern om du vill att de virtuella datorerna och skalnings uppsättnings instanserna automatiskt ska placeras på värdar i en värd grupp. För ytterligare information, se [manuell respektive automatisk placering ](../dedicated-hosts.md#manual-vs-automatic-placement).


## <a name="create-a-host"></a>Skapa en värd

Nu ska vi skapa en dedikerad värd i värd gruppen. Förutom ett namn för värden måste du ange SKU för värden. Värd-SKU: n samlar in de VM-serier som stöds samt maskin varu generering för den dedikerade värden.

Mer information om värd-SKU: er och priser finns i [prissättning för Azure-dedikerad värd](https://aka.ms/ADHPricing).

Om du anger ett fel domän antal för värd gruppen uppmanas du att ange fel domänen för värden. I det här exemplet ställer vi in fel domänen för värden på 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator på den dedikerade värden. 

Om du har angett en tillgänglighets zon när du skapar värd gruppen måste du använda samma zon när du skapar den virtuella datorn. För det här exemplet måste vi skapa den virtuella datorn i zon 1, eftersom vår värd grupp finns i zon 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Om du skapar en virtuell dator på en värd som inte har tillräckligt med resurser, kommer den virtuella datorn att skapas i ett felaktigt tillstånd. 

## <a name="check-the-status-of-the-host"></a>Kontrol lera status för värden

Du kan kontrol lera värd hälso status och hur många virtuella datorer du kan distribuera till värden med [GetAzHost](/powershell/module/az.compute/get-azhost) med `-InstanceView` parametern.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Resultatet kommer att se ut ungefär så här:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning 

När du distribuerar en skalnings uppsättning anger du värd gruppen.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Om du manuellt vill välja vilken värd som ska användas för att distribuera skalnings uppsättningen till lägger du till `--host` och namnet på värden.



## <a name="add-an-existing-vm"></a>Lägg till en befintlig virtuell dator 

Du kan lägga till en befintlig virtuell dator till en dedikerad värd, men den virtuella datorn måste först vara Stop\Deallocated. Innan du flyttar en virtuell dator till en dedikerad värd kontrollerar du att VM-konfigurationen stöds:

- Storleken på den virtuella datorn måste vara i samma storleks familj som den dedikerade värden. Om din dedikerade värd till exempel är DSv3, kan storleken på den virtuella datorn vara Standard_D4s_v3, men det gick inte att Standard_A4_v2. 
- Den virtuella datorn måste finnas i samma region som den dedikerade värden.
- Den virtuella datorn kan inte ingå i en närhets placerings grupp. Ta bort den virtuella datorn från närhets placerings gruppen innan du flyttar den till en dedikerad värd. Mer information finns i [flytta en virtuell dator från en närhets placerings grupp](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- Den virtuella datorn får inte finnas i en tillgänglighets uppsättning.
- Om den virtuella datorn finns i en tillgänglighets zon måste den vara samma tillgänglighets zon som värd gruppen. Inställningarna för tillgänglighets zonen för den virtuella datorn och värd gruppen måste matcha.

Ersätt värdena för variablerna med din egen information.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Rensa

Du debiteras för dina dedikerade värdar även om inga virtuella datorer distribueras. Du bör ta bort alla värdar som du för närvarande inte använder för att spara kostnader.  

Du kan bara ta bort en värd när det inte finns några längre virtuella datorer som använder den. Ta bort de virtuella datorerna med [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

När du har tagit bort de virtuella datorerna kan du ta bort värden med hjälp av [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

När du har tagit bort alla värdar kan du ta bort värd gruppen med hjälp av [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Du kan också ta bort hela resurs gruppen i ett enda kommando med hjälp av [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Detta tar bort alla resurser som skapats i gruppen, inklusive alla virtuella datorer, värdar och värd grupper.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Nästa steg

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också distribuera dedikerade värdar med hjälp av [Azure Portal](../dedicated-hosts-portal.md).