---
title: Skapa och ladda upp en Linux-VHD
description: Lär dig att skapa och ladda upp en virtuell Azure-hård disk (VHD) som innehåller ett Linux-operativsystem.
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: 5a62bae8881f20eb7778f179cbecce77f102f6e8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554640"
---
# <a name="information-for-non-endorsed-distributions"></a>Information om icke-godkända distributioner

Service avtalet för Azure-plattformen gäller enbart virtuella datorer som kör Linux OS när en av de godkända [distributionerna](endorsed-distros.md) används. För dessa godkända distributioner finns förkonfigurerade Linux-avbildningar på Azure Marketplace.

* [Linux på Azure-godkända distributioner](endorsed-distros.md)
* [Stöd för Linux-avbildningar i Microsoft Azure](https://support.microsoft.com/kb/2941892)

Alla distributioner som körs på Azure har ett antal krav. Den här artikeln kan inte vara omfattande eftersom varje distribution är annorlunda. Även om du uppfyller alla kriterier nedan kan du behöva justera Linux-systemet avsevärt för att det ska fungera korrekt.

Vi rekommenderar att du börjar med en av [Linux på Azure-godkända distributioner](endorsed-distros.md). I följande artiklar visas hur du förbereder de olika godkända Linux-distributioner som stöds i Azure:

- [CentOS-baserade distributioner](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES och openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

Den här artikeln fokuserar på allmän vägledning för att köra din Linux-distribution på Azure.

## <a name="general-linux-installation-notes"></a>Allmän Linux-installation anmärkningar
* Formatet för Hyper-V virtuell hård disk (VHDX) stöds inte i Azure, endast *fast virtuell* hård disk.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten [Convert-VHD](/powershell/module/hyper-v/convert-vhd) . Om du använder VirtualBox väljer du **fast storlek** i stället för standard (dynamiskt allokerat) när du skapar disken.
* Azure stöder gen1 (BIOS boot) & Gen2 (UEFI boot) virtuella datorer.
* Den maximala storlek som tillåts för den virtuella hård disken är 1 023 GB.
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner istället för LVM (Logical Volume Manager) som är standard för många installationer. Genom att använda standardpartitioner undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin är ansluten till en annan identisk virtuell dator för fel sökning. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) eller [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) kan användas på data diskar.
* Kernel-stöd för att montera UDF-filsystem är nödvändigt. Vid första starten av Azure skickas etablerings konfigurationen till den virtuella Linux-datorn med hjälp av UDF-formaterade medier som är kopplade till gästen. Azure Linux-agenten måste montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Tidigare versioner av Linux-kärnan än 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar främst äldre distributioner med den överordnade Red Hat 2.6.32-kärnan och har åtgärd ATS i Red Hat Enterprise Linux (RHEL) 6,6 (kernel-2.6.32-504). System som kör anpassade kernels som är äldre än 2.6.37, eller RHEL-baserade kernels som är äldre än 2.6.32-504, måste ange start parametern `numa=off` på kernel-kommandoraden i grub. conf. Mer information finns i [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte en swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlings fil på den tillfälliga resurs disken, enligt beskrivningen i följande steg.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att storleken på den råa disken är en multipel av 1 MB före konverteringen, enligt beskrivningen i följande steg.

### <a name="installing-kernel-modules-without-hyper-v"></a>Installera kernel-moduler utan Hyper-V
Azure körs på Hyper-V-hypervisorn så att Linux kräver vissa kernel-moduler för att köras i Azure. Om du har en virtuell dator som har skapats utanför Hyper-V kan Linux-installations programmet inte inkludera driv rutinerna för Hyper-V i den första RAMDISK-enheten (initrd eller initramfs), om inte den virtuella datorn känner av att den körs i en Hyper-V-miljö. När du använder ett annat Virtualization-system (t. ex. VirtualBox, KVM och så vidare) för att förbereda Linux-avbildningen kan du behöva återskapa initrd så att minst hv_vmbus och hv_storvsc kernel-moduler är tillgängliga på den första RAMDISK-datorn.  Det här kända problemet är för system baserade på den överordnade Red Hat-distributionen och eventuellt andra.

Mekanismen för att återskapa initrd-eller initramfs-avbildningen kan variera beroende på distributionen. Läs igenom distributionens dokumentation eller support om du vill ha en korrekt procedur.  Här är ett exempel på hur du återskapar initrd med hjälp av `mkinitrd` verktyget:

1. Säkerhetskopiera den befintliga initrd-avbildningen:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Återskapa initrd med hv_vmbus och hv_storvsc kernel-moduler:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Ändra storlek på virtuella hård diskar
VHD-avbildningar på Azure måste ha en virtuell storlek som är justerad till 1 MB.  Vanligt vis justeras virtuella hård diskar som skapats med Hyper-V korrekt.  Om den virtuella hård disken inte justeras korrekt kan du få ett fel meddelande som liknar följande när du försöker skapa en avbildning från din virtuella hård disk.

* VHD http: \/ / \<mystorageaccount> . blob.Core.Windows.net/VHDs/MyLinuxVM.VHD har en virtuell storlek på 21475270656 byte som inte stöds. Storleken måste vara ett heltal (i MB).

I det här fallet ändrar du storlek på den virtuella datorn med hjälp av Hyper-V Manager-konsolen eller [ändra storlek på VHD PowerShell-](/powershell/module/hyper-v/resize-vhd) cmdleten.  Om du inte kör i en Windows-miljö rekommenderar vi att du använder `qemu-img` för att konvertera (om det behövs) och ändra storlek på den virtuella hård disken.

> [!NOTE]
> Det finns en [känd bugg i qemu-img-](https://bugs.launchpad.net/qemu/+bug/1490611) versionerna >= 2.2.1 som resulterar i en felaktigt formaterad virtuell hård disk. Problemet har åtgärd ATS i QEMU 2,6. Vi rekommenderar att du använder antingen `qemu-img` 2.2.0 eller lägre eller 2,6 eller högre.
> 

1. Att ändra storlek på den virtuella hård disken direkt med hjälp av verktyg som `qemu-img` eller `vbox-manage` kan leda till en virtuell hård disk som inte kan startas.  Vi rekommenderar att först konvertera den virtuella hård disken till en RAW disk-avbildning.  Om den virtuella dator avbildningen skapades som en RAW disk avbildning (standardinställningen för vissa hypervisorer som KVM) kan du hoppa över det här steget.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Beräkna den nödvändiga storleken på disk avbildningen så att den virtuella storleken är justerad till 1 MB.  Följande bash shell-skript använder `qemu-img info` för att fastställa disk avbildningens virtuella storlek och beräknar sedan storleken till nästa 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Ändra storlek på den råa disken med den `$rounded_size` som anges ovan.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Konvertera nu den råa disken till en virtuell hård disk med fast storlek.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Eller, med qemu version 2.6 +, inkluderar du `force_size` alternativet.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Krav för linux-kernel

LIS-drivrutinerna (Linux Integration Services) för Hyper-V och Azure bidrogs direkt till den överordnade Linux-kärnan. Många distributioner som innehåller en nyare Linux-kernel-version (t. ex. 3. x) har dessa driv rutiner som redan är tillgängliga, eller som på annat sätt förser de driv rutiner som har fått nya versioner med deras kärnor.  De här driv rutinerna uppdateras ständigt i den överordnade kerneln med nya korrigeringar och funktioner, så när det är möjligt rekommenderar vi att du kör en [godkänd distribution](endorsed-distros.md) som innehåller dessa korrigeringar och uppdateringar.

Om du använder en variant av Red Hat Enterprise Linux version 6,0 till 6,3 måste du installera de [senaste Lis-drivrutinerna för Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Från och med RHEL 6.4 + (och derivat) är LIS-drivrutinerna redan inkluderade i kerneln och det behövs inga ytterligare installations paket.

Om en anpassad kernel krävs rekommenderar vi en ny kernel-version (till exempel 3.8 +). För distributioner eller leverantörer som underhåller sin egen kernel måste du regelbundet Backport LIS-drivrutinerna från den överordnade kerneln till din anpassade kernel.  Även om du redan kör en relativt ny kernel-version rekommenderar vi starkt att du håller koll på eventuella överordnade korrigeringar i LIS-drivrutinerna och Backport dem efter behov. Platserna för LIS-drivrutinens källfiler anges i [underhållar](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) filen i käll trädet i Linux-kernel:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
Följande korrigeringar måste ingå i kerneln. Den här listan kan inte slutföras för alla distributioner.

* [ata_piix: skjuta upp diskar till Hyper-V-drivrutinerna som standard](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: konto för paket som överförs i återställnings Sök vägen](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: Undvik användning av WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: inaktivera skrivning samma för RAID och virtuella värd kort driv rutiner](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL-referens för pekare](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: Ring-buffertöverskridningsfel kan resultera i i/O-frysning](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: skydda mot dubbel körning av __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux-agenten
[Azure Linux-agenten](../extensions/agent-linux.md) `waagent` tillhandahåller en virtuell Linux-dator i Azure. Du kan hämta den senaste versionen, fil problemen eller skicka pull-begäranden på [Linux-agentens GitHub-lagrings platsen](https://github.com/Azure/WALinuxAgent).

* Linux-agenten lanseras i Apache 2,0-licensen. Många distributioner innehåller redan RPM-eller deb-paket för agenten och de här paketen kan enkelt installeras och uppdateras.
* Azure Linux-agenten kräver python v 2.6 +.
* Agenten kräver även en python-pyasn1-modul. De flesta distributioner ger den här modulen ett separat paket som ska installeras.
* I vissa fall kanske Azure Linux-agenten inte är kompatibel med NetworkManager. Många av de RPM/deb-paket som tillhandahålls av distributioner konfigurera NetworkManager som en konflikt med waagent-paketet. I dessa fall kommer den att avinstallera NetworkManager när du installerar Linux-agenten.
* Azure Linux-agenten måste vara minst eller högre än den [lägsta version som stöds](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Allmänna Linux system krav

1. Ändra start raden för kernel i GRUB eller GRUB2 för att inkludera följande parametrar, så att alla konsol meddelanden skickas till den första serie porten. Dessa meddelanden kan hjälpa Azure support med fel sökning av problem.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Vi rekommenderar också att du *tar bort* följande parametrar om de finns.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar skickas till den seriella porten. `crashkernel`Alternativet kan lämnas om det behövs, men Observera att den här parametern minskar mängden tillgängligt minne på den virtuella datorn med minst 128 MB, vilket kan vara problematiskt för mindre VM-storlekar.

1. Installera Azure Linux-agenten.
  
    Azure Linux-agenten krävs för att kunna tillhandahålla en Linux-avbildning på Azure.  Många distributioner tillhandahåller agenten som ett RPM-eller deb-paket (paketet kallas vanligt vis WALinuxAgent eller WALinuxAgent).  Agenten kan också installeras manuellt genom att följa stegen i guiden för [Linux-agenten](../extensions/agent-linux.md).

1. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Den här konfigurationen är vanligt vis standardvärdet.

1. Skapa inte växlings utrymme på OS-disken.
  
    Azure Linux-agenten kan automatiskt konfigurera växlings utrymme med hjälp av den lokala resurs disk som är kopplad till den virtuella datorn efter etableringen på Azure. Den lokala resurs disken är en *temporär* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (steg 2 ovan) ändrar du följande parametrar i/etc/waagent.conf efter behov.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Kör följande kommandon för att avetablera den virtuella datorn.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > På VirtualBox kan du se följande fel när du har kört `waagent -force -deprovision` det som säger `[Errno 5] Input/output error` . Det här fel meddelandet är inte kritiskt och kan ignoreras.

* Stäng av den virtuella datorn och överför den virtuella hård disken till Azure.