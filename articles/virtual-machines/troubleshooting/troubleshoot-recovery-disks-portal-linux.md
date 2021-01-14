---
title: Använd en Linux-felsökning av virtuella datorer i Azure Portal | Microsoft Docs
description: Lär dig hur du felsöker problem med virtuella Linux-datorer genom att ansluta OS-disken till en virtuell återställnings dator med hjälp av Azure Portal
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: affdee6871649102ef7881fb0f540eba6ab450ca
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200965"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Felsöka en virtuell Linux-dator genom att koppla OS-disken till en virtuell återställnings dator med hjälp av Azure Portal
Om din virtuella Linux-dator (VM) påträffar ett start-eller diskfel kan du behöva utföra fel söknings stegen på den virtuella hård disken. Ett vanligt exempel är en ogiltig post i `/etc/fstab` som förhindrar att den virtuella datorn kan starta. Den här artikeln beskriver hur du använder Azure Portal för att ansluta den virtuella hård disken till en annan virtuell Linux-dator för att åtgärda eventuella fel och sedan återskapa den ursprungliga virtuella datorn.

## <a name="recovery-process-overview"></a>Översikt över återställningsprocessen
Så här ser felsökningsprocessen ut:

1. Stoppa den virtuella datorn som påverkas.
1. Ta en ögonblicks bild för den virtuella datorns OS-disk.
1. Skapa en virtuell hård disk från ögonblicks bilden.
1. Anslut och montera den virtuella hård disken till en annan virtuell Linux-dator i fel söknings syfte.
1. Anslut till den virtuella felsökningsdatorn. Redigera filer eller kör eventuella verktyg för att åtgärda problem på den ursprungliga virtuella hård disken.
1. Demontera och koppla från den virtuella hårddisken från den virtuella felsökningsdatorn.
1. Byt OS-disk för den virtuella datorn.

> [!NOTE]
> Den här artikeln gäller inte för den virtuella datorn med en ohanterad disk.

## <a name="determine-boot-issues"></a>Fastställa start problem
Granska skärm bilden startdiagnostik och VM för att avgöra varför den virtuella datorn inte kan starta korrekt. Ett vanligt exempel är en ogiltig post i `/etc/fstab` eller en underliggande virtuell hård disk som tas bort eller flyttas.

Välj din virtuella dator i portalen och rulla ned till avsnittet **support och fel sökning** . Klicka på **Starta diagnostik** för att Visa konsol meddelanden som strömmas från din virtuella dator. Granska konsol loggarna för att se om du kan avgöra varför den virtuella datorn påträffar ett problem. I följande exempel visas en virtuell dator som fastnat i underhålls läge och som kräver manuell interaktion:

![Visa loggfiler för VM Boot Diagnostic-konsolen](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Du kan också klicka på **skärm bild** överst i Start-diagnostikloggar för att ladda ned en bild av skärm bilden för den virtuella datorn.

## <a name="take-a-snapshot-of-the-os-disk"></a>Ta en ögonblicks bild av OS-disken
En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk (VHD). Vi rekommenderar att du stänger den virtuella datorn i klartext innan du tar en ögonblicks bild, för att ta bort alla processer som pågår. Följ dessa steg om du vill ta en ögonblicks bild av en OS-disk:

1. Gå till [Azure Portal](https://portal.azure.com). Välj **virtuella datorer** på sid panelen och välj sedan den virtuella dator som har problem.
1. Välj **diskar** i den vänstra rutan och välj sedan namnet på operativ system disken.
    ![Bild om namnet på OS-disken](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. På sidan **Översikt** på OS-disken och välj sedan **skapa ögonblicks bild**.
1. Skapa en ögonblicks bild på samma plats som OS-disken.

## <a name="create-a-disk-from-the-snapshot"></a>Skapa en disk från ögonblicks bilden
Följ dessa steg om du vill skapa en disk från ögonblicks bilden:

1. Välj **Cloud Shell** från Azure Portal.

    ![Bild om öppen Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Kör följande PowerShell-kommandon för att skapa en hanterad disk från ögonblicks bilden. Du bör ersätta dessa exempel namn med lämpliga namn.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Om kommandona körs utan problem visas den nya disken i den resurs grupp som du har angett.

## <a name="attach-disk-to-another-vm"></a>Koppla disk till en annan virtuell dator
För kommande steg använder du en annan virtuell dator i fel söknings syfte. När du har kopplat disken till fel söknings datorn kan du bläddra och redigera diskens innehåll. Med den här processen kan du korrigera eventuella konfigurations fel eller granska ytterligare program-eller systemloggfiler. Följ dessa steg om du vill koppla disken till en annan virtuell dator:

1. Välj din resurs grupp i portalen och välj sedan din fel söknings dator. Välj **diskar**, Välj **Redigera** och klicka sedan på **Lägg till data disk**:

    ![Bifoga befintlig disk i portalen](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. I listan **data diskar** väljer du OS-disken för den virtuella dator som du har identifierat. Om du inte ser OS-disken måste du kontrol lera att den virtuella datorn felsöks och att operativ system disken finns i samma region (plats). 
3. Välj **Spara** för att tillämpa ändringarna.

## <a name="mount-the-attached-data-disk"></a>Montera den anslutna data disken

> [!NOTE]
> Följande exempel beskriver stegen som krävs på en virtuell Ubuntu-dator. Om du använder en annan Linux-distribution, till exempel Red Hat Enterprise Linux eller SUSE, kan logg filens platser och `mount` kommandon vara lite annorlunda. Se dokumentationen för din speciella distribution för lämpliga ändringar i kommandon.

1. SSH till din felsöka virtuella dator med hjälp av lämpliga autentiseringsuppgifter. Om den här disken är den första datadisk som är ansluten till den virtuella fel söknings datorn är den förmodligen ansluten till `/dev/sdc` . Använd `dmseg` för att lista anslutna diskar:

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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Åtgärda problem på den ursprungliga virtuella hård disken
Med den befintliga virtuella hård disken monterad kan du nu utföra eventuella underhålls-och fel söknings steg efter behov. När du har åtgärdat problemen fortsätter du med följande steg.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Demontera och koppla från den ursprungliga virtuella hård disken
När dina fel har åtgärd ATS kopplar du från den befintliga virtuella hård disken från den virtuella fel söknings datorn. Du kan inte använda den virtuella hård disken med någon annan virtuell dator förrän lånet som ansluter till den virtuella hård disken till den virtuella fel söknings datorn har släppts.

1. Från SSH-sessionen till din felsöka virtuella hård disk avmonterar du den befintliga virtuella hård disken. Ändra från den överordnade katalogen för monterings punkten först:

    ```bash
    cd /
    ```

    Demontera nu den befintliga virtuella hård disken. I följande exempel demonterar du enheten på `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Koppla nu från den virtuella hård disken från den virtuella datorn. Välj den virtuella datorn i portalen och klicka på **diskar**. Välj din befintliga virtuella hård disk och klicka sedan på **Koppla från**:

    ![Koppla från befintlig virtuell hård disk](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Vänta tills den virtuella datorn har kopplats från data disken innan du fortsätter.

## <a name="swap-the-os-disk-for-the-vm"></a>Byt OS-disk för den virtuella datorn

Azure Portal har nu stöd för att ändra den virtuella datorns OS-disk. Det gör du genom att följa dessa steg:

1. Gå till [Azure Portal](https://portal.azure.com). Välj **virtuella datorer** på sid panelen och välj sedan den virtuella dator som har problem.
1. I det vänstra fönstret väljer du **diskar** och sedan **Växla OS-disk**.
        ![Avbildningen om växling av OS-disk i Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Välj den nya disken som du reparerat och skriv sedan namnet på den virtuella datorn för att bekräfta ändringen. Om du inte ser disken i listan väntar du 10 ~ 15 minuter efter att du kopplar bort disken från den virtuella fel söknings datorn. Kontrol lera också att disken finns på samma plats som den virtuella datorn.
1. Välj OK.

## <a name="next-steps"></a>Nästa steg
Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖK SSH-anslutningar till en virtuell Azure-dator](troubleshoot-ssh-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Linux-dator](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).

Mer information om hur du använder Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
