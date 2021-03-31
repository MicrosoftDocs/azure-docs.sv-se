---
title: Hantera Virtual Machine Scale Sets med Azure CLI
description: Vanliga Azure CLI-kommandon för att hantera Virtual Machine Scale Sets, till exempel hur du startar och stoppar en instans eller ändrar skalnings uppsättningens kapacitet.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d954f7cdda4cae65f822489828226e0364d0fc29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91570536"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Hantera en skalnings uppsättning för virtuella datorer med Azure CLI
Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. I den här artikeln beskrivs några av de vanliga Azure CLI-kommandon som du kan använda för att utföra dessa uppgifter.

Du behöver den senaste versionen av Azure CLI för att kunna slutföra dessa hanterings uppgifter. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli). Om du behöver skapa en skalnings uppsättning för virtuella datorer kan du [skapa en skalnings uppsättning med Azure CLI](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Visa information om en skalnings uppsättning
Om du vill visa den övergripande informationen om en skalnings uppsättning använder du [AZ VMSS show](/cli/azure/vmss). I följande exempel får du information om skalnings uppsättningen med namnet *myScaleSet* i resurs gruppen *myResourceGroup* . Ange dina egna namn enligt följande:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Om du vill visa en lista över virtuella dator instanser i en skalnings uppsättning använder du [AZ VMSS List-instances](/cli/azure/vmss). I följande exempel visas alla virtuella dator instanser i skalnings uppsättningen med namnet *myScaleSet* i resurs gruppen *myResourceGroup* . Ange dina egna värden för följande namn:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Om du vill visa ytterligare information om en speciell VM-instans lägger du till `--instance-id` parametern till [AZ VMSS get-instance-View](/cli/azure/vmss) och anger en instans att visa. Följande exempel visar information om den virtuella dator instansen *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna namn enligt följande:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Du kan också få detaljerad *instanceView* -information för alla instanser i ett API-anrop, vilket kan hjälpa till att undvika API-begränsning för stora installationer. Ange dina egna värden för `--resource-group` , `--subscription` och `--name` .

```azurecli
az vmss list-instances \
    --expand instanceView \
    --select instanceView \
    --resource-group <resourceGroupName> \
    --subscription <subID> \
    --name <vmssName>
```

```rest
GET "https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSSName>/virtualMachines?api-version=2019-03-01&%24expand=instanceView"
```

## <a name="list-connection-information-for-vms"></a>Lista anslutnings information för virtuella datorer
För att ansluta till de virtuella datorerna i en skalnings uppsättning, SSH eller RDP till en tilldelad offentlig IP-adress och port nummer. Som standard läggs regler för Network Address Translation (NAT) till i Azure Load Balancer som vidarebefordrar fjärr anslutnings trafiken till varje virtuell dator. Om du vill visa en lista över adressen och portarna för att ansluta till virtuella dator instanser i en skalnings uppsättning, använder du [AZ VMSS List-instance-Connection-info](/cli/azure/vmss). I följande exempel visas anslutnings information för virtuella dator instanser i skalnings uppsättningen med namnet *myScaleSet* och i resurs gruppen *myResourceGroup* . Ange dina egna värden för följande namn:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
Föregående kommandon visade information om din skalnings uppsättning och de virtuella dator instanserna. Om du vill öka eller minska antalet instanser i skalnings uppsättningen kan du ändra kapaciteten. Skalnings uppsättningen skapar eller tar bort det antal virtuella datorer som krävs, och konfigurerar sedan de virtuella datorerna för att ta emot program trafik.

Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [az vmss show](/cli/azure/vmss) och frågar efter *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [az vmss scale](/cli/azure/vmss). I följande exempel anges antalet virtuella datorer i din skalnings uppsättning till *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skalnings uppsättning tas de virtuella datorerna med de högsta instans-ID: na bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skalningsuppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalnings uppsättning använder du [AZ VMSS Stop](/cli/azure/vmss#az-vmss-stop). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Om du vill stoppa flera virtuella datorer avgränsar du varje instans-ID med ett blank steg.

I följande exempel stoppas instans *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna värden på följande sätt:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Stoppade virtuella datorer förblir allokerade och fortsätter att ådra sig beräknings kostnader. Om du i stället vill att de virtuella datorerna ska frigöras och bara debiteras lagrings avgifter använder du [AZ VMSS deallokera](/cli/azure/vmss). Om du vill frigöra flera virtuella datorer avgränsar du varje instans-ID med ett blank steg. I följande exempel stoppas och friallokeras instans *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna värden på följande sätt:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skalnings uppsättning
Om du vill starta en eller flera virtuella datorer i en skalnings uppsättning använder du [AZ VMSS start](/cli/azure/vmss). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Om du vill starta flera virtuella datorer avgränsar du varje instans-ID med ett blank steg.

I följande exempel startas instans *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna värden på följande sätt:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skalningsuppsättning
Om du vill starta om en eller flera virtuella datorer i en skalnings uppsättning använder du [AZ VMSS restart](/cli/azure/vmss). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Om du vill starta om flera virtuella datorer separerar du varje instans-ID med ett blank steg.

I följande exempel startas instans *0* om i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* . Ange dina egna värden på följande sätt:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skalnings uppsättning
Om du vill ta bort en eller flera virtuella datorer i en skalnings uppsättning använder du [AZ VMSS Delete-instances](/cli/azure/vmss). `--instance-ids`Parametern låter dig ange en eller flera virtuella datorer som ska tas bort. Om du anger * för instans-ID, tas alla virtuella datorer i skalnings uppsättningen bort. Om du vill ta bort flera virtuella datorer separerar du varje instans-ID med ett blank steg.

I följande exempel tas instans *0* i skalnings uppsättningen med namnet *myScaleSet* och resurs gruppen *myResourceGroup* bort. Ange dina egna värden på följande sätt:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalnings uppsättningar är hur du [distribuerar ett program](virtual-machine-scale-sets-deploy-app.md)och [uppgraderar VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure CLI för att [Konfigurera regler för automatisk skalning](virtual-machine-scale-sets-autoscale-overview.md).
