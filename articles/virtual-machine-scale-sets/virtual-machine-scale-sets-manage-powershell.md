---
title: Hantera Virtual Machine Scale Sets med Azure PowerShell
description: Vanliga Azure PowerShell-cmdletar för att hantera Virtual Machine Scale Sets, till exempel hur du startar och stoppar en instans eller ändrar skalnings uppsättningens kapacitet.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: eee4dd7fae872f6b3ddd01f60aba732edc170766
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570583"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Hantera en skalnings uppsättning för virtuella datorer med Azure PowerShell

Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. I den här artikeln beskrivs några vanliga Azure PowerShell-cmdletar som låter dig utföra dessa uppgifter.

Om du behöver skapa en skalnings uppsättning för virtuella datorer kan du [skapa en skalnings uppsättning med Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Visa information om en skalnings uppsättning
Om du vill visa den övergripande informationen om en skalnings uppsättning använder du [Get-AzVmss](/powershell/module/az.compute/get-azvmss). I följande exempel får du information om skalnings uppsättningen med namnet *myScaleSet* i resurs gruppen *myResourceGroup* . Ange dina egna namn enligt följande:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Om du vill visa en lista över virtuella dator instanser i en skalnings uppsättning, använder du [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). I följande exempel visas alla virtuella dator instanser i skalnings uppsättningen med namnet *myScaleSet* och i resurs gruppen *myResourceGroup* . Ange dina egna värden för följande namn:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Om du vill visa ytterligare information om en speciell VM-instans lägger du till `-InstanceId` parametern till [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) och anger en instans att visa. Följande exempel visar information om den virtuella dator instansen *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna namn enligt följande:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Du kan också få detaljerad *instanceView* -information för alla instanser i ett API-anrop, vilket kan hjälpa till att undvika API-begränsning för stora installationer.

```powershell
Get-AzVmssVM -InstanceView -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
Föregående kommandon visade information om din skalnings uppsättning och de virtuella dator instanserna. Om du vill öka eller minska antalet instanser i skalnings uppsättningen kan du ändra kapaciteten. Skalnings uppsättningen skapar eller tar bort det antal virtuella datorer som krävs, och konfigurerar sedan de virtuella datorerna för att ta emot program trafik.

Skapa först ett skalningsuppsättningsobjekt med [Get-AzVmss](/powershell/module/az.compute/get-azvmss) och ange sedan ett nytt värde för `sku.capacity`. Om du vill tillämpa kapacitetsändringen, använder du [Update-AzVmss](/powershell/module/az.compute/update-azvmss). I följande exempel uppdateras *myScaleSet* i resurs gruppen *myResourceGroup* till en kapacitet på *5* instanser. Ange dina egna värden på följande sätt:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skalnings uppsättning tas de virtuella datorerna med de högsta instans-ID: na bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skalningsuppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalningsuppsättning, använder du [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Om du vill stoppa flera virtuella datorer avgränsar du varje instans-ID med ett kommatecken.

I följande exempel stoppas instans *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna värden på följande sätt:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Som standard frigörs stoppade virtuella datorer och uppbär inga beräkningskostnader. Om du vill att den virtuella datorn är kvar i etablerat tillstånd när den stoppats, lägger du till parametern `-StayProvisioned` till det föregående kommandot. Stoppade virtuella datorer som fortsätter att vara etablerade, kostar vanliga beräkningsavgifter.


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skalnings uppsättning
Om du vill starta en eller flera virtuella datorer i en skalningsuppsättning, använder du [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Om du vill starta flera virtuella datorer avgränsar du varje instans-ID med ett kommatecken.

I följande exempel startas instans *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna värden på följande sätt:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skalningsuppsättning
Om du vill starta om en eller flera virtuella datorer i en skalnings uppsättning använder du [restart-AzVmss](/powershell/module/az.compute/restart-azvmss). Parametern `-InstanceId` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Om du vill starta om flera virtuella datorer måste du avgränsa varje instans-ID med ett kommatecken.

I följande exempel startas instans *0* om i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna värden på följande sätt:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skalnings uppsättning
Om du vill ta bort en eller flera virtuella datorer i en skalnings uppsättning använder du [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). `-InstanceId`Parametern låter dig ange en eller flera virtuella datorer som ska tas bort. Om du inte anger ett instans-ID tas alla virtuella datorer i skalnings uppsättningen bort. Om du vill ta bort flera virtuella datorer avgränsar du varje instans-ID med kommatecken.

I följande exempel tas instans *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* bort. Ange dina egna värden på följande sätt:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalnings uppsättningar är hur du [distribuerar ett program](virtual-machine-scale-sets-deploy-app.md)och [uppgraderar VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure PowerShell för att [Konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
