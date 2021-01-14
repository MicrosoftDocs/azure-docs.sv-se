---
title: Använda en Linux felsöka virtuell dator med Azure CLI | Microsoft Docs
description: Lär dig hur du felsöker problem med virtuella Linux-datorer genom att ansluta OS-disken till en virtuell återställnings dator med Azure CLI
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 70855f7956e42d815e0b8135ccfe6e374853d8aa
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197531"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell dator för återställning med Azure CLI
Om din virtuella Linux-dator (VM) påträffar ett start-eller diskfel kan du behöva utföra fel söknings stegen på den virtuella hård disken. Ett vanligt exempel är en ogiltig post i `/etc/fstab` som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder Azure CLI för att ansluta din virtuella hård disk till en annan virtuell Linux-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn. 

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Stoppa den virtuella datorn som påverkas.
1. Ta en ögonblicks bild från den virtuella datorns OS-disk.
1. Skapa en disk från ögonblicks bilden av OS-disken.
1. Anslut och montera den nya OS-disken till en annan virtuell Linux-dator i fel söknings syfte.
1. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör eventuella verktyg för att åtgärda problem på den nya OS-disken.
1. Demontera och koppla från den nya OS-disken från den virtuella fel söknings datorn.
1. Ändra OS-disken för den berörda virtuella datorn.

Om du vill utföra de här fel söknings stegen måste du ha det senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggat på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Du kan automatisera steg 1, 2, 3, 4, 6 och 7 genom att använda kommandona för reparation av virtuella datorer. Mer dokumentation och instruktioner finns i [Reparera en virtuell Linux-dator med hjälp av reparations kommandona för virtuella Azure-datorer](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

> [!Important]
> Skripten i den här artikeln gäller bara för de virtuella datorer som använder [hanterad disk](../managed-disks-overview.md). 

I följande exempel ersätter du parameter namn med dina egna värden, till exempel `myResourceGroup` och `myVM` .

## <a name="determine-boot-issues"></a>Fastställa start problem
Granska de seriella utdata för att avgöra varför den virtuella datorn inte kan starta korrekt. Ett vanligt exempel är en ogiltig post i `/etc/fstab` , eller den underliggande virtuella hård disken som tas bort eller flyttas.

Hämta start loggarna med [AZ VM Boot-Diagnostics get-Boot-log](/cli/azure/vm/boot-diagnostics). I följande exempel hämtas serie resultatet från den virtuella datorn som heter `myVM` i resurs gruppen med namnet `myResourceGroup` :

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Granska de seriella utdata för att avgöra varför den virtuella datorn inte kan starta. Om den seriella utmatningen inte ger någon indikation kan du behöva granska loggfilerna i `/var/log` när du har en virtuell hård disk ansluten till en felsöknings-VM.

## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

I följande exempel stoppas den virtuella datorn med namnet `myVM` från resurs gruppen med namnet `myResourceGroup` :

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Ta en ögonblicks bild från OS-disken för den berörda virtuella datorn

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk. Den kan inte kopplas till en virtuell dator. I nästa steg ska vi skapa en disk från den här ögonblicks bilden. I följande exempel skapas en ögonblicks bild med namnet `mySnapshot` från operativ system disken för den virtuella datorn med namnet "myVM". 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicks bilden

Det här skriptet skapar en hanterad disk med namnet `myOSDisk` från ögonblicks bilden med namnet `mySnapshot` .  

```azurecli
#Provide the name of your resource group
$resourceGroup="myResourceGroup"

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot="mySnapshot"

#Provide the name of the Managed Disk
$osDisk="myNewOSDisk"

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType="Premium_LRS"

#Provide the OS type
$osType="linux"

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query id -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Om resurs gruppen och käll ögonblicks bilden inte finns i samma region visas fel meddelandet "Det gick inte att hitta resursen" när du kör `az disk create` . I så fall måste du ange `--location <region>` att disken ska skapas i samma region som ögonblicks bilden av källan.

Nu har du en kopia av den ursprungliga OS-disken. Du kan montera den här nya disken till en annan virtuell Windows-dator i fel söknings syfte.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Koppla den nya virtuella hård disken till en annan virtuell dator
För kommande steg använder du en annan virtuell dator i fel söknings syfte. Du ansluter disken till den här fel söknings datorn för att bläddra och redigera diskens innehåll. Med den här processen kan du korrigera eventuella konfigurations fel eller granska ytterligare program-eller systemloggfiler.

Det här skriptet ansluter disken `myNewOSDisk` till den virtuella datorn `MyTroubleshootVM` :

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az disk show -g $resourceGroup -n $osDisk --query id -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $myNewOSDiskid --resource-group $resourceGroup --size-gb $diskSize --sku $storageType --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>Montera den anslutna data disken

> [!NOTE]
> Följande exempel beskriver stegen som krävs på en virtuell Ubuntu-dator. Om du använder en annan Linux-distribution, till exempel Red Hat Enterprise Linux eller SUSE, kan logg filens platser och `mount` kommandon vara lite annorlunda. Se dokumentationen för din speciella distribution för lämpliga ändringar i kommandon.

1. SSH till din felsöka virtuella dator med hjälp av lämpliga autentiseringsuppgifter. Om den här disken är den första datadisk som är ansluten till den virtuella fel söknings datorn är disken antagligen ansluten till `/dev/sdc` . Använd `dmesg` för att Visa anslutna diskar:

    ```bash
    dmesg | grep SCSI
    ```

    Utdata ser ut ungefär så här:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    I föregående exempel är OS-disken på `/dev/sda` och den temporära disken som anges för varje virtuell dator finns på `/dev/sdb` . Om du hade flera data diskar bör de vara på `/dev/sdd` , `/dev/sde` och så vidare.

2. Skapa en katalog för att montera din befintliga virtuella hård disk. I följande exempel skapas en katalog med namnet `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Om du har flera partitioner på din befintliga virtuella hård disk monterar du den nödvändiga partitionen. I följande exempel monteras den första primära partitionen på `/dev/sdc1` :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Bästa praxis är att montera data diskar på virtuella datorer i Azure med hjälp av den virtuella hård diskens UUID (Universal Unique Identifier). För det här korta fel söknings scenariot är det inte nödvändigt att montera den virtuella hård disken med UUID. Men under normal användning kan redigering av `/etc/fstab` virtuella hård diskar med enhets namn i stället för UUID leda till att den virtuella datorn inte kan starta.


## <a name="fix-issues-on-the-new-os-disk"></a>Åtgärda problem på den nya OS-disken
Med den befintliga virtuella hård disken monterad kan du nu utföra eventuella underhålls-och fel söknings steg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.


## <a name="unmount-and-detach-the-new-os-disk"></a>Demontera och koppla från den nya OS-disken
När dina fel har åtgärd ATS demonterar du och kopplar från den befintliga virtuella hård disken från den virtuella fel söknings datorn. Du kan inte använda den virtuella hård disken med någon annan virtuell dator förrän lånet som ansluter till den virtuella hård disken till den virtuella fel söknings datorn har släppts.

1. Från SSH-sessionen till din felsöka virtuella hård disk avmonterar du den befintliga virtuella hård disken. Ändra från den överordnade katalogen för monterings punkten först:

    ```bash
    cd /
    ```

    Demontera nu den befintliga virtuella hård disken. I följande exempel demonterar du enheten på `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Koppla nu från den virtuella hård disken från den virtuella datorn. Avsluta SSH-sessionen till din felsöka VM:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Ändra OS-disken för den berörda virtuella datorn

Du kan använda Azure CLI för att växla OS-diskarna. Du behöver inte ta bort och återskapa den virtuella datorn.

Det här exemplet stoppar den virtuella datorn med namnet `myVM` och tilldelar disken namnet `myNewOSDisk` som den nya OS-disken.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g $resourceGroup -n $osDisk --query id -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖK SSH-anslutningar till en virtuell Azure-dator](troubleshoot-ssh-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Linux-dator](troubleshoot-app-connection.md).

