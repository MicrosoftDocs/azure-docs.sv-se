---
title: Skapa en ögonblicksbild av en virtuell hårddisk med hjälp av Azure CLI
description: Lär dig hur du skapar en kopia av en virtuell hårddisk i Azure som en säkerhetskopia eller för felsökning av problem.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ab19bb1c6cc43334a3d0d427b6aff6ced2d6cc69
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789671"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>Skapa en ögonblicksbild med hjälp av portalen eller Azure CLI

Ta en ögonblicksbild av ett operativsystem eller en datadisk för säkerhetskopiering eller felsök problem med virtuella datorer. En ögonblicksbild är en fullständig, skrivskyddad kopia av en virtuell hårddisk. 

## <a name="use-azure-cli"></a>Använda Azure CLI 

I följande exempel måste du använda [Cloud Shell](https://shell.azure.com/bash) eller ha Azure CLI installerat.

Följande steg visar hur du tar en ögonblicksbild med kommandot **az snapshot create** med parametern **--source-disk.** I följande exempel förutsätts att det finns en virtuell dator med *namnet myVM* i *resursgruppen myResourceGroup.*

Hämta disk-ID:t [med az vm show](/cli/azure/vm#az_vm_show).

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Ta en ögonblicksbild med namnet *osDisk-backup med* [az snapshot create](/cli/azure/snapshot#az_snapshot_create).

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> Om du vill lagra ögonblicksbilden i zon elastisk lagring måste du skapa [](../../availability-zones/az-overview.md) den i en region som stöder tillgänglighetszoner och inkludera parametern **--sku Standard_ZRS.**

Du kan se en lista över ögonblicksbilderna med [az snapshot list](/cli/azure/snapshot#az_snapshot_list).

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>Använda Azure-portalen 

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Från och med det övre vänstra hörnet klickar du **på Skapa en resurs** och söker efter **ögonblicksbild.** Välj **Ögonblicksbild** i sökresultatet.
3. På **bladet Ögonblicksbild** klickar du på **Skapa**.
4. Ange ett **namn** för ögonblicksbilden.
5. Välj en befintlig resursgrupp eller skriv namnet på en ny. 
7. För **Källdisk** väljer du den hanterade disk som ska ögonblicksbilden.
8. Välj den **kontotyp som** ska användas för att lagra ögonblicksbilden. Använd **Standard HDD** om du inte behöver den lagrad på en HÖGPRESTERAnde SSD.
9. Klicka på **Skapa**.


## <a name="next-steps"></a>Nästa steg

 Skapa en virtuell dator från en ögonblicksbild genom att skapa en hanterad disk från ögonblicksbilden och sedan koppla den nya hanterade disken som OS-disk. Mer information finns i Skapa en [virtuell dator från ett ögonblicksbildsskript.](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json)