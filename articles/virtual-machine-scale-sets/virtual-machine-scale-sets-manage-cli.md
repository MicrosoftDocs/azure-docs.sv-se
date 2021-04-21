---
title: Hantera Virtual Machine Scale Sets med Azure CLI
description: Vanliga Azure CLI-kommandon för att hantera Virtual Machine Scale Sets, till exempel hur du startar och stoppar en instans eller ändrar skalningsuppsättningens kapacitet.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/29/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9c2b2217fc6b32e5191bb67ffdaa10b796adf84b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762775"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Hantera en VM-skalningsuppsättning med Azure CLI
Under livscykeln för en VM-skalningsuppsättning kan du behöva köra en eller flera hanteringsuppgifter. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Den här artikeln beskriver några av de vanliga Azure CLI-kommandon som gör att du kan utföra dessa uppgifter.

För att kunna utföra de här hanteringsuppgifterna behöver du den senaste versionen av Azure CLI. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli) Om du behöver skapa en VM-skalningsuppsättning kan du [skapa en skalningsuppsättning med Azure CLI.](quick-create-cli.md)


## <a name="view-information-about-a-scale-set"></a>Visa information om en skalningsuppsättning
Om du vill visa den övergripande informationen om en skalningsuppsättning använder [du az vmss show](/cli/azure/vmss). I följande exempel hämtar du information om skalningsuppsättningen *med namnet myScaleSet* i *resursgruppen myResourceGroup.* Ange dina egna namn på följande sätt:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Om du vill visa en lista över virtuella datorinstanser i en skalningsuppsättning använder [du az vmss list-instances](/cli/azure/vmss). I följande exempel visas alla VM-instanser i skalningsuppsättningen med *namnet myScaleSet* i *resursgruppen myResourceGroup.* Ange dina egna värden för dessa namn:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Om du vill visa ytterligare information om en specifik VM-instans lägger du till parametern i `--instance-id` [az vmss get-instance-view](/cli/azure/vmss) och anger en instans som ska visas. I följande exempel visas information om VM-instans *0* i skalningsuppsättningen med namnet *myScaleSet* och *resursgruppen myResourceGroup.* Ange dina egna namn på följande sätt:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```

Du kan också få detaljerad *instansVyinformation* för alla instanser i ett API-anrop, vilket kan hjälpa dig att undvika API-begränsning för stora installationer. Ange dina egna värden för `--resource-group` `--subscription` , och `--name` .

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

## <a name="list-connection-information-for-vms"></a>Lista anslutningsinformation för virtuella datorer
Om du vill ansluta till de virtuella datorerna i en skalningsuppsättning, SSH eller RDP till en tilldelad offentlig IP-adress och portnummer. Som standard läggs NAT-regler (Network Address Translation) till i Azure Load Balancer som vidarebefordrar fjärranslutningstrafik till varje virtuell dator. Använd [az vmss list-instance-connection-info](/cli/azure/vmss)för att visa adressen och portarna för att ansluta till VM-instanser i en skalningsuppsättning. I följande exempel visas anslutningsinformation för VIRTUELLA datorinstanser i skalningsuppsättningen med namnet *myScaleSet* och i *resursgruppen myResourceGroup.* Ange egna värden för dessa namn:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändra kapaciteten för en skalningsuppsättning
Föregående kommandon visade information om din skalningsuppsättning och de virtuella datorinstanserna. Om du vill öka eller minska antalet instanser i skalningsuppsättningen kan du ändra kapaciteten. Skalningsuppsättningen skapar eller tar bort det antal virtuella datorer som krävs och konfigurerar sedan de virtuella datorerna för att ta emot programtrafik.

Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [az vmss show](/cli/azure/vmss) och frågar efter *sku.capacity*:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [az vmss scale](/cli/azure/vmss). I följande exempel anges antalet virtuella datorer i din skalningsuppsättning till *5*:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Det tar några minuter att uppdatera kapaciteten för din skalningsuppsättning. Om du minskar kapaciteten för en skalningsuppsättning tas de virtuella datorerna med högst instans-ID bort först.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Stoppa och starta virtuella datorer i en skalningsuppsättning
Om du vill stoppa en eller flera virtuella datorer i en skalningsuppsättning använder [du az vmss stop](/cli/azure/vmss#az_vmss_stop). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att stoppa. Om du inte anger ett instans-ID, stoppas alla virtuella datorer i skalningsuppsättningen. Om du vill stoppa flera virtuella datorer avgränsar du varje instans-ID med ett blanksteg.

I följande exempel stoppas instans *0* i skalningsuppsättningen med *namnet myScaleSet* och *resursgruppen myResourceGroup.* Ange dina egna värden på följande sätt:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Stoppade virtuella datorer förblir allokerade och fortsätter att medföra beräkningsavgifter. Om du i stället vill att de virtuella datorerna ska friplaceras och endast medföra lagringsavgifter använder [du az vmss deallocate](/cli/azure/vmss). Om du vill avallokera flera virtuella datorer avgränsar du varje instans-ID med ett blanksteg. I följande exempel stoppas och allokeras instans *0 i* skalningsuppsättningen med namnet *myScaleSet* och *resursgruppen myResourceGroup.* Ange dina egna värden på följande sätt:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Starta virtuella datorer i en skalningsuppsättning
Om du vill starta en eller flera virtuella datorer i en skalningsuppsättning använder [du az vmss start](/cli/azure/vmss). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen. Om du vill starta flera virtuella datorer separerar du varje instans-ID med ett blanksteg.

I följande exempel startar instans *0* i skalningsuppsättningen med namnet *myScaleSet* och *resursgruppen myResourceGroup.* Ange dina egna värden på följande sätt:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Starta om virtuella datorer i en skalningsuppsättning
Om du vill starta om en eller flera virtuella datorer i en skalningsuppsättning använder [du az vmss restart](/cli/azure/vmss). Parametern `--instance-ids` låter dig ange en eller flera virtuella datorer att starta om. Om du inte anger ett instans-ID, startas alla virtuella datorer i skalningsuppsättningen om. Om du vill starta om flera virtuella datorer separerar du varje instans-ID med ett blanksteg.

I följande exempel startas *instansen 0 om* i skalningsuppsättningen med namnet *myScaleSet* och *resursgruppen myResourceGroup.* Ange dina egna värden på följande sätt:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Ta bort virtuella datorer från en skalningsuppsättning
Om du vill ta bort en eller flera virtuella datorer i en skalningsuppsättning använder du [az vmss delete-instances](/cli/azure/vmss). Med `--instance-ids` parametern kan du ange en eller flera virtuella datorer som ska tas bort. Om du anger * för instans-ID tas alla virtuella datorer i skalningsuppsättningen bort. Om du vill ta bort flera virtuella datorer avgränsar du varje instans-ID med ett blanksteg.

I följande exempel tas instans *0 bort* i skalningsuppsättningen med namnet *myScaleSet* och *resursgruppen myResourceGroup.* Ange dina egna värden på följande sätt:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Nästa steg
Andra vanliga uppgifter för skalningsuppsättningar är hur du [distribuerar ett program](virtual-machine-scale-sets-deploy-app.md)och [uppgraderar VM-instanser](virtual-machine-scale-sets-upgrade-scale-set.md). Du kan också använda Azure CLI för [att konfigurera regler för automatisk skalning.](virtual-machine-scale-sets-autoscale-overview.md)
