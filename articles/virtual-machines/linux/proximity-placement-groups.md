---
title: Använda närhetsplaceringsgrupper
description: Lär dig mer om att skapa och använda närhets placerings grupper för virtuella datorer i Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: ea50d0fdc5a772cae449951901af36eed4e93771
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737633"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Distribuera virtuella datorer till närhetsplaceringsgrupper med Azure CLI

För att få virtuella datorer så nära som möjligt, vilket ger lägsta möjliga fördröjning, bör du distribuera dem i en [närhets placerings grupp](co-location.md#proximity-placement-groups).

En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Placerings grupper för närhet är användbara för arbets belastningar där låg latens är ett krav.


## <a name="create-the-proximity-placement-group"></a>Skapa placerings gruppen närhet
Skapa en närhets placerings grupp med [`az ppg create`](/cli/azure/ppg#az-ppg-create) . 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Lista närhets placerings grupper

Du kan visa alla närhets placerings grupper med [AZ PPG List](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator i närhets placerings gruppen med [den nya virtuella AZ-datorn](/cli/azure/vm#az-vm-create).

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

Du kan se den virtuella datorn i närhets placerings gruppen med [AZ PPG show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
Du kan också skapa en tillgänglighets uppsättning i närheten av placerings gruppen. Använd samma `--ppg` parameter med [AZ VM Availability-set Create](/cli/azure/vm/availability-set#az-vm-availability-set-create) för att skapa en tillgänglighets uppsättning och alla virtuella datorer i tillgänglighets uppsättningen kommer också att skapas i samma närhets placerings grupp.

## <a name="scale-sets"></a>Skalningsuppsättningar

Du kan också skapa en skalnings uppsättning i närheten av placerings gruppen. Använd samma `--ppg` parameter med [AZ VMSS Create](/cli/azure/vmss#az_vmss_create) för att skapa en skalnings uppsättning och alla instanser kommer att skapas i samma närhets placerings grupp.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure CLI](/cli/azure/ppg) -kommandon för närhets placerings grupper.
