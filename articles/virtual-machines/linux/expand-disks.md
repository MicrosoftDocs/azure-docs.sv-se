---
title: Expandera virtuella hårddiskar på en virtuell Linux-dator
description: Lär dig hur du expanderar virtuella hårddiskar på en virtuell Linux-dator med Azure CLI.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c27b042b78931fd58e43e4bbb06699abe510f385
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762559"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Expandera virtuella hårddiskar på en virtuell Linux-dator med Azure CLI

Den här artikeln beskriver hur du expanderar hanterade diskar för en virtuell Linux-dator (VM) med Azure CLI. Du kan [lägga till datadiskar](add-disk.md) för ytterligare lagringsutrymme och du kan också expandera en befintlig datadisk. Standardstorleken för virtuell hårddisk för operativsystemet (OS) är vanligtvis 30 GB på en virtuell Linux-dator i Azure. 

> [!WARNING]
> Se alltid till att filsystemet är i felfritt tillstånd, att diskpartitionstabelltypen har stöd för den nya storleken och att dina data säkerhetskopieras innan du utför åtgärder för diskstorleksändring. Mer information finns i Azure Backup [snabbstart.](../../backup/quick-backup-vm-portal.md) 

## <a name="expand-an-azure-managed-disk"></a>Expandera en Azure Managed Disk
Kontrollera att du har den senaste [versionen av Azure CLI](/cli/azure/install-az-cli2) installerad och är inloggad på ett Azure-konto med hjälp av az [login](/cli/azure/reference-index#az_login).

Den här artikeln kräver en befintlig virtuell dator i Azure med minst en ansluten och förberedd datadisk. Om du inte redan har en virtuell dator som du kan använda kan du se [Skapa och förbereda en virtuell dator med datadiskar.](tutorial-manage-disks.md#create-and-attach-disks)

I följande exempel ersätter du exempelparameternamn som *myResourceGroup* och *myVM* med dina egna värden.

1. Åtgärder på virtuella hårddiskar kan inte utföras när den virtuella datorn körs. Frisplacera den virtuella datorn [med az vm deallocate](/cli/azure/vm#az_vm_deallocate). I följande exempel frisänds den virtuella datorn *med namnet myVM* i resursgruppen med namnet *myResourceGroup*:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Den virtuella datorn måste friplaceras för att expandera den virtuella hårddisken. Om du stoppar den virtuella `az vm stop` datorn med frigörs inte beräkningsresurserna. Om du vill frigöra beräkningsresurser använder du `az vm deallocate` .

1. Visa en lista över hanterade diskar i en resursgrupp med [az disk list](/cli/azure/disk#az_disk_list). I följande exempel visas en lista över hanterade diskar i resursgruppen med namnet *myResourceGroup*:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Expandera den nödvändiga disken med [az disk update](/cli/azure/disk#az_disk_update). I följande exempel expanderas den hanterade disken *med namnet myDataDisk* till *200* GB:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > När du expanderar en hanterad disk avrundas den uppdaterade storleken uppåt till närmaste hanterade diskstorlek. En tabell över tillgängliga storlekar och nivåer för hanterade diskar finns i [Azure Managed Disks Översikt – priser och fakturering.](../managed-disks-overview.md)

1. Starta den virtuella datorn med [az vm start](/cli/azure/vm#az_vm_start). I följande exempel startas den virtuella datorn med *namnet myVM* i resursgruppen med namnet *myResourceGroup*:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Expandera en diskpartition och ett filsystem
Om du vill använda en expanderad disk expanderar du den underliggande partitionen och filsystemet.

1. SSH till den virtuella datorn med rätt autentiseringsuppgifter. Du kan se den offentliga IP-adressen för den virtuella datorn med [az vm show](/cli/azure/vm#az_vm_show):

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --output tsv
    ```

1. Expandera den underliggande partitionen och filsystemet.

    a. Om disken redan är monterad demonterar du den:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Använd `parted` för att visa diskinformation och ändra storlek på partitionen:

    ```bash
    sudo parted /dev/sdc
    ```

    Visa information om den befintliga partitionslayouten med `print` . Utdata liknar följande exempel, som visar att den underliggande disken är 215 GB:

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

    c. Expandera partitionen med `resizepart` . Ange partitionsnumret, *1* och en storlek för den nya partitionen:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Om du vill avsluta anger du `quit` .

1. När partitionen har ändrats kontrollerar du partitionens konsekvens med `e2fsck` :

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Ändra storlek på filsystemet med `resize2fs` :

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Montera partitionen på önskad plats, till exempel `/datadrive` :

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Kontrollera att datadisken har storleksändrats med hjälp av `df -h` . Följande exempelutdata visar att dataenheten */dev/sdc1* nu är 200 GB:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Nästa steg
* Om du behöver ytterligare lagringsutrymme kan du också lägga till [datadiskar till en virtuell Linux-dator.](add-disk.md) 
* Mer information om diskkryptering finns i [Azure Disk Encryption för virtuella Linux-datorer.](disk-encryption-overview.md)
