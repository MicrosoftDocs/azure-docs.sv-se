---
title: Ändra storlek på en virtuell Linux-dator med Azure CLI
description: Skala upp eller skala ned en virtuell Linux-dator genom att ändra storleken på den virtuella datorn.
author: DavidCBerry13
ms.service: virtual-machines
ms.topic: how-to
ms.date: 02/10/2017
ms.author: daberry
ms.collection: linux
ms.openlocfilehash: 290c42cadd840e5a292201247b1555059b5b4381
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556646"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>Ändra storlek på en virtuell Linux-dator med Azure CLI 

När du har etablerat en virtuell dator (VM) kan du skala upp eller ned den virtuella datorn genom att ändra storleken på den [virtuella][vm-sizes]datorn. I vissa fall måste du frigöra den virtuella datorn först. Du måste frigöra den virtuella datorn om den önskade storleken inte är tillgänglig på det maskin varu kluster som är värd för den virtuella datorn. Den här artikeln beskriver hur du ändrar storlek på en virtuell Linux-dator med Azure CLI. 

## <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator
Om du vill ändra storlek på en virtuell dator måste du ha det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

1. Visa listan över tillgängliga VM-storlekar i maskin varu klustret där den virtuella datorn är värd för [AZ VM List-VM-size-Options](/cli/azure/vm). I följande exempel visas VM-storlekar för den virtuella datorn med namnet `myVM` i resurs grupps `myResourceGroup` regionen:
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. Om önskad VM-storlek visas ändrar du storlek på den virtuella datorn med [AZ storlek på virtuell dator](/cli/azure/vm). I följande exempel ändras storleken på den virtuella datorn med namn `myVM` till `Standard_DS3_v2` storleken:
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    Den virtuella datorn startas om under den här processen. Efter omstarten mappas dina befintliga operativ system och data diskar om. Allt på den temporära disken förloras.

3. Om önskad VM-storlek inte visas i listan måste du först frigöra den virtuella datorn med [AZ VM deallokering](/cli/azure/vm). Den här processen gör det möjligt för den virtuella datorn att ändra storlek till vilken storlek som helst som är tillgänglig som regionen har stöd för och sedan startas. Följande steg frigör, ändrar storlek och startar sedan den virtuella datorn `myVM` som heter i resurs gruppen med namnet `myResourceGroup` :
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > Om du frigör den virtuella datorn frigörs också alla dynamiska IP-adresser som tilldelats den virtuella datorn. Operativ system-och data diskar påverkas inte.

## <a name="next-steps"></a>Nästa steg
Om du vill ha ytterligare skalbarhet kan du köra flera virtuella dator instanser och skala ut. Mer information finns i skala [Linux-datorer automatiskt i en skalnings uppsättning för virtuella datorer][scale-set]. 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
