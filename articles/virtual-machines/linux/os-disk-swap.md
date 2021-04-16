---
title: Växla mellan OS-diskar med hjälp av Azure CLI
description: Ändra operativsystemdisken som används av en virtuell Azure-dator med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c9b0c1948dc4ecef74cd78ec1736803a0c0b4bc
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497378"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-azure-cli"></a>Ändra os-disken som används av en virtuell Azure-dator med hjälp av Azure CLI


Om du har en befintlig virtuell dator, men vill byta disken mot en säkerhetskopia eller en annan OS-disk, kan du använda Azure CLI för att växla OS-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn. Du kan till och med använda en hanterad disk i en annan resursgrupp, så länge den inte redan används.

Den virtuella datorn måste stoppas\frisökas. Sedan kan resurs-ID:t för den hanterade disken ersättas med resurs-ID:t för en annan hanterad disk. 

Kontrollera att VM-storleken och lagringstypen är kompatibla med den disk som du vill ansluta. Om den disk som du vill använda till exempel finns i Premium Storage måste den virtuella datorn kunna Premium Storage (t.ex. en DS-seriestorlek).

Den här artikeln kräver Azure CLI version 2.0.25 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 


Använd [az disk list](/cli/azure/disk) för att hämta en lista över diskarna i resursgruppen.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Använd [az vm stop](/cli/azure/vm) för att stoppa\frisöka den virtuella datorn innan du växlar diskarna.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Använd [az vm update](/cli/azure/vm#az-vm-update) med det fullständiga resurs-ID:t för den nya disken för `--osdisk` parametern 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Starta om den virtuella datorn med [az vm start](/cli/azure/vm).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Nästa steg**

Information om hur du skapar en kopia av en disk finns [i Ögonblicksbild av en disk.](snapshot-copy-managed-disk.md)
