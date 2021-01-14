---
title: Använd CLI för att distribuera virtuella Azure-datorer
description: Lär dig hur du använder CLI för att distribuera virtuella Azure-datorer för att spara kostnader.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 71e0f2e87fc71deef0bdc4dd48425cdc9dd99dc8
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200778"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Distribuera virtuella datorer med hjälp av Azure CLI

Med hjälp av [virtuella Azure-datorer](../spot-vms.md) kan du dra nytta av vår outnyttjade kapacitet till betydande kostnads besparingar. Vid alla tidpunkter när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort virtuella datorer. De virtuella datorerna är därför fantastiska för arbets belastningar som kan hantera avbrott som bearbetnings jobb, utvecklings-/test miljöer, stora beräknings arbets belastningar med mera.

Priser för virtuella datorer i virtuella datorer är varierande, baserat på region och SKU. Mer information finns i prissättning för virtuella datorer för [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) och [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Du har möjlighet att ange ett högsta pris som du är villig att betala per timme för den virtuella datorn. Det maximala priset för en VM-VM kan anges i USD (USD) med upp till 5 decimaler. Värdet skulle till exempel `0.98765` vara ett max pris på $0,98765 USD per timme. Om du anger det högsta priset så `-1` kommer den virtuella datorn inte att avlägsnas baserat på priset. Priset för den virtuella datorn är det aktuella priset för dekor pris eller priset för en standard-VM, som någonsin är mindre, så länge det finns kapacitet och tillgänglig kvot. Mer information om hur du ställer in högsta pris finns i [VM-priser för virtuella datorer](../spot-vms.md#pricing).

Processen för att skapa en virtuell dator med dekor med hjälp av Azure CLI är samma som beskrivs i [snabb starts artikeln](./quick-create-cli.md). Lägg bara till parametern "--priority", ange `--eviction-policy` antingen för att frigöra (detta är standard) eller `Delete` , och ange ett högsta pris eller `-1` . 


## <a name="install-azure-cli"></a>Installera Azure CLI

För att skapa virtuella datorer måste du köra Azure CLI-versionen 2.0.74 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 

Logga in på Azure med [AZ-inloggning](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Skapa en virtuell dator

Det här exemplet visar hur du distribuerar en virtuell Linux-dator som inte kommer att avlägsnas utifrån pris. Borttagnings principen är inställd på att frigöra den virtuella datorn, så att den kan startas om vid ett senare tillfälle. Om du vill ta bort den virtuella datorn och den underliggande disken när den virtuella datorn avlägsnas, anger `--eviction-policy` du till `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



När den virtuella datorn har skapats kan du fråga om du vill se det högsta fakturerings priset för alla virtuella datorer i resurs gruppen.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Simulera en avtagning

Du kan [simulera en avlägsnande](/rest/api/compute/virtualmachines/simulateeviction) av en virtuell dator för att testa hur bra ditt program kommer att återdamma till en plötslig avlägsning. 

Ersätt följande med din information: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Nästa steg**

Du kan också skapa en virtuell dator med hjälp av [Azure PowerShell](../windows/spot-powershell.md), [portalen](../spot-portal.md)eller en [mall](spot-template.md).

Fråga aktuell pris information med hjälp av [Azures API för åter försäljning](/rest/api/cost-management/retail-prices/azure-retail-prices) för information om prissättning av priser. `meterName`Och `skuName` kommer båda att innehålla `Spot` .

Om du stöter på ett fel, se [felkoder](../error-codes-spot.md).