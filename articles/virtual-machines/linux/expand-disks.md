---
title: Expandera virtuella hård diskar på en virtuell Linux-dator
description: Lär dig hur du expanderar virtuella hård diskar på en virtuell Linux-dator med Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 72778c431c561f5345dde3d6803e814d6fdebfba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549132"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expandera virtuella hård diskar på en virtuell Linux-dator med Azure CLI

Den här artikeln beskriver hur du expanderar hanterade diskar för en virtuell Linux-dator (VM) med Azure CLI. Du kan [lägga till data diskar](add-disk.md) för att tillhandahålla ytterligare lagrings utrymme, och du kan också expandera en befintlig data disk. Standard storleken på virtuella hård diskar för operativ systemet (OS) är vanligt vis 30 GB på en virtuell Linux-dator i Azure. 

> [!WARNING]
> Se alltid till att fil systemet är i felfritt tillstånd, att tabell typen för diskpartitionen stöder den nya storleken och se till att dina data säkerhets kopie ras innan du utför åtgärder för storleks ändring av disken. Mer information finns i [snabb start för Azure Backup](../../backup/quick-backup-vm-portal.md). 

## <a name="expand-an-azure-managed-disk"></a>Expandera en Azure-hanterad disk
Kontrol lera att du har den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och är inloggad på ett Azure-konto genom att använda [AZ-inloggning](/cli/azure/reference-index#az-login).

Den här artikeln kräver en befintlig virtuell dator i Azure med minst en data disk ansluten och för beredd. Om du inte redan har en virtuell dator som du kan använda kan du läsa [skapa och förbereda en virtuell dator med data diskar](tutorial-manage-disks.md#create-and-attach-disks).

I följande exempel ersätter du parameter namn, till exempel *myResourceGroup* och *myVM* med dina egna värden.

1. Åtgärder på virtuella hård diskar kan inte utföras med den virtuella datorn som kör. Frigör den virtuella datorn med [AZ VM-frigörning](/cli/azure/vm#az-vm-deallocate). I följande exempel avallokeras den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Den virtuella datorn måste frigöras för att expandera den virtuella hård disken. Om den virtuella datorn stoppas `az vm stop` frigörs inte beräknings resurserna. Om du vill frigöra beräknings resurser använder du `az vm deallocate` .

1. Visa en lista med hanterade diskar i en resurs grupp med [AZ disk List](/cli/azure/disk#az-disk-list). I följande exempel visas en lista med hanterade diskar i resurs gruppen med namnet *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expandera den disk som krävs med [AZ disk Update](/cli/azure/disk#az-disk-update). I följande exempel expanderas den hanterade disken med namnet *myDataDisk* till *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > När du expanderar en hanterad disk avrundas den uppdaterade storleken upp till närmaste hanterade disk storlek. En tabell med tillgängliga storlekar och nivåer för hanterade diskar finns i [Översikt över Azure Managed disks – priser och fakturering](../managed-disks-overview.md).

1. Starta den virtuella datorn med [AZ VM start](/cli/azure/vm#az-vm-start). I följande exempel startas den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expandera en diskpartition och fil system
Om du vill använda en utökad disk expanderar du den underliggande partitionen och fil systemet.

1. SSH till den virtuella datorn med rätt autentiseringsuppgifter. Du kan se den offentliga IP-adressen för den virtuella datorn med [AZ VM show](/cli/azure/vm#az-vm-show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Expandera den underliggande partitionen och fil systemet.

    a. Om disken redan är monterad kan du Demontera den:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Använd `parted` för att Visa disk information och ändra storlek på partitionen:

    ```bash
    sudo parted /dev/sdc
    ```

    Visa information om den befintliga partitionen layout med `print` . Utdata liknar följande exempel som visar den underliggande disken är 215 GB:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. Expandera partitionen med `resizepart` . Ange partitionens nummer, *1* och en storlek för den nya partitionen:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Avsluta genom att ange `quit` .

1. Kontrol lera att partitionen har ändrat storlek genom att kontrol lera konsekvensen för partitionen med `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Ändra storlek på fil systemet med `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Montera partitionen till önskad plats, till exempel `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Om du vill kontrol lera att data disken har storleksändrats använder du `df -h` . Följande exempel på utdata visar att data enheten */dev/sdc1* är 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Nästa steg
* Om du behöver ytterligare lagring kan du också [lägga till data diskar till en virtuell Linux-dator](add-disk.md). 
* Mer information om disk kryptering finns i [Azure Disk Encryption för virtuella Linux-datorer](disk-encryption-overview.md).
