---
title: Skapa en närhetsplaceringsgrupp med hjälp av Azure CLI
description: Lär dig mer om att skapa och använda närhetsplaceringsgrupper för virtuella datorer i Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: e4f91afa86a0d99b4ce42e96295bf2ae1f9fcd9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771451"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Distribuera virtuella datorer till närhetsplaceringsgrupper med Azure CLI

För att få virtuella datorer så nära som möjligt, för att uppnå lägsta möjliga svarstid, bör du distribuera dem i en [närhetsplaceringsgrupp](../co-location.md#proximity-placement-groups).

En närhetsplaceringsgrupp är en logisk gruppering som används för att se till att Azure-beräkningsresurser finns fysiskt nära varandra. Närhetsplaceringsgrupper är användbara för arbetsbelastningar där låg latens är ett krav.


## <a name="create-the-proximity-placement-group"></a>Skapa närhetsplaceringsgruppen
Skapa en närhetsplaceringsgrupp med hjälp av [`az ppg create`](/cli/azure/ppg#az_ppg_create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Lista närhetsplaceringsgrupper

Du kan lista alla dina närhetsplaceringsgrupper med [az ppg list](/cli/azure/ppg#az_ppg_list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator i närhetsplaceringsgruppen med hjälp [av den nya az vm](/cli/azure/vm#az_vm_create).

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Du kan se den virtuella datorn i närhetsplaceringsgruppen med [az ppg show](/cli/azure/ppg#az_ppg_show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Du kan också skapa en tillgänglighetsuppsättning i din närhetsplaceringsgrupp. Använd samma parameter med az vm availability-set create för att skapa en tillgänglighetsuppsättning och alla virtuella datorer i tillgänglighetsuppsättningen skapas också i samma `--ppg` närhetsplaceringsgrupp. [](/cli/azure/vm/availability-set#az_vm_availability_set_create)

## <a name="scale-sets"></a>Skalningsuppsättningar

Du kan också skapa en skalningsuppsättning i din närhetsplaceringsgrupp. Använd samma parameter med az vmss create för att skapa en skalningsuppsättning och alla instanser skapas i `--ppg` samma närhetsplaceringsgrupp. [](/cli/azure/vmss#az_vmss_create)

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure CLI-kommandon](/cli/azure/ppg) för närhetsplaceringsgrupper.