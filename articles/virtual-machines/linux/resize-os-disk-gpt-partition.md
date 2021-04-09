---
title: Ändra storlek på en OS-disk som har en GPT-partition
description: Den här artikeln innehåller anvisningar om hur du ändrar storlek på en OS-disk som har en GPT-partition (GUID Partition Table) i Linux.
services: virtual-machines
ms.topic: article
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 46b6ceff74dd3a296d26cc018b380c1c3f76664c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552957"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Ändra storlek på en OS-disk som har en GPT-partition

> [!NOTE]
> Den här artikeln gäller bara för OS-diskar som har en GPT-partition (GUID Partition Table).

Den här artikeln beskriver hur du ökar storleken på en OS-disk som har en GPT-partition i Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Identifiera om OS-disken har en MBR-eller GPT-partition

Använd `parted` kommandot för att identifiera om diskpartitionen har skapats med antingen en Master Boot Record-partition (MBR) eller en GPT-partition.

### <a name="mbr-partition"></a>MBR-partition

I följande utdata visar **partitionstabellen** värdet **MSDOS**. Det här värdet identifierar en MBR-partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT-partition

I följande utdata visar **partitionstabellen** ett **GPT**-värde. Det här värdet identifierar en GPT-partition.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Om din virtuella dator har en GPT-partition på din OS-disk ökar du storleken på operativ system disken.

## <a name="increase-the-size-of-the-os-disk"></a>Öka storleken på operativ system disken

Följande instruktioner gäller för Linux-godkända distributioner.

> [!NOTE]
> Innan du fortsätter kan du göra en säkerhets kopia av den virtuella datorn eller ta en ögonblicks bild av din OS-disk.

### <a name="ubuntu"></a>Ubuntu

För att öka storleken på operativ system disken i Ubuntu 16. *x* och 18. *x*:

1. Stoppa den virtuella datorn.
1. Öka storleken på operativ system disken från portalen.
1. Starta om den virtuella datorn och logga sedan in på den virtuella datorn som en **rot** användare.
1. Kontrol lera att operativ system disken nu visar en större fil system storlek.

I följande exempel har OS-disken ändrat storlek från portalen till 100 GB. **/Dev/sda1** -filsystemet som är monterat **/** nu visar 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

För att öka storleken på operativ system disken i SUSE 12 SP4, SUSE SLES 12 för SAP, SUSE SLES 15 och SUSE SLES 15 för SAP:

1. Stoppa den virtuella datorn.
1. Öka storleken på operativ system disken från portalen.
1. Starta om den virtuella datorn.

När den virtuella datorn har startats om utför du följande steg:

1. Få åtkomst till den virtuella datorn som en **rot** användare med hjälp av det här kommandot:

   ```
   # sudo -i
   ```

1. Använd följande kommando för att installera **growpart** -paketet som du ska använda för att ändra storlek på partitionen:

   ```
   # zypper install growpart
   ```

1. Använd `lsblk` kommandot för att hitta partitionen som är monterad i roten för fil systemet ( **/** ). I det här fallet ser vi att partition 4 av enhets **SDA** är monterad på **/** :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Ändra storlek på den nödvändiga partitionen genom att använda `growpart` kommandot och partitionsnumret som fastställs i föregående steg:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Kör `lsblk` kommandot igen för att kontrol lera om partitionen har ökat.

   Följande utdata visar att **/dev/sda4** -partitionen har storleksändrats till 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifiera typ av fil system på OS-disken genom att använda `lsblk` kommandot med `-f` flaggan:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Använd lämpliga kommandon för att ändra storlek på fil systemet baserat på typ av fil system.
   
   För **xfs** använder du följande kommando:
   
   ```
   #xfs_growfs /
   ```
   
   Exempel på utdata:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   För **ext4** använder du följande kommando:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Verifiera den ökade fil system storleken för **DF** genom att använda det här kommandot:
   
   ```
   #df -Thl
   ```
   
   Exempel på utdata:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   I föregående exempel kan vi se att fil system storleken för operativ system disken har ökat.

### <a name="rhel-with-lvm"></a>RHEL med LVM

1. Få åtkomst till den virtuella datorn som en **rot** användare med hjälp av det här kommandot:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Använd `lsblk` kommandot för att avgöra vilken logisk volym (LV) som är monterad i roten för fil systemet ( **/** ). I det här fallet ser vi att **rootvg-rootlv** är monterad på **/** . Om du vill ha ett annat fil system ersätter du LV och monterings punkt i den här artikeln.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Kontrol lera om det finns ledigt utrymme i den LVM volym grupp (VG) som innehåller rot partitionen. Om det finns ledigt utrymme går du vidare till steg 12.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   I det här exemplet visar den **kostnads fria PE/storleken** att det finns 38,02 GB ledigt utrymme i volym gruppen. Du behöver inte ändra storlek på disken innan du lägger till utrymme i volym gruppen.

1. För att öka storleken på operativ system disken i RHEL 7. *x* med LVM:

   1. Stoppa den virtuella datorn.
   1. Öka storleken på operativ system disken från portalen.
   1. Starta den virtuella datorn.

1. När den virtuella datorn har startats om utför du följande steg:

   - Installera paketet **Cloud-utils-growpart** för att tillhandahålla kommandot **growpart** , vilket krävs för att öka storleken på operativ system disken och GDISK-hanteraren för GPT-diskens layouter. Dessa paket är förinstallerade på de flesta Marketplace-avbildningar.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Bestäm vilken disk och partition som innehåller LVM fysiska volymer eller volymer (PV) i volym gruppen med namnet **rootvg** med hjälp av `pvscan` kommandot. Observera storlek och ledigt utrymme som anges mellan hakparenteserna (**[** och **]**).

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Kontrol lera partitionens storlek med hjälp av `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Expandera partitionen som innehåller detta PV genom att använda `growpart` , enhets namnet och partitionens nummer. Om du gör det expanderas den angivna partitionen så att allt ledigt utrymme på enheten används.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Kontrol lera att partitionen har ändrat storlek till den förväntade storleken genom att använda `lsblk` kommandot igen. Observera att i exemplet **sda4** har ändrats från 63 GB till 95 GB.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Expandera NUVÄRDEt för att använda resten av den nya expanderade partitionen:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Kontrol lera att den nya storleken på PV är den förväntade storleken, och jämför det med de ursprungliga **[storlek/lediga]** värdena:

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Expandera önskad logisk volym (LV) med den mängd som du vill ha. Mängden behöver inte vara allt ledigt utrymme i volym gruppen. I följande exempel ändrar **/dev/mapper/rootvg-rootlv** storlek från 2 GB till 12 GB (en ökning på 10 GB). Det här kommandot ändrar även storlek på fil systemet.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Exempel på utdata:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. `lvresize`Kommandot anropar automatiskt rätt storleks ändrings kommando för fil systemet på LV. Kontrol lera om **/dev/mapper/rootvg-rootlv**, som är monterat på **/** , har en större fil system storlek med hjälp av det här kommandot:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Exempel på utdata:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Om du vill använda samma procedur för att ändra storlek på en annan logisk volym ändrar du namnet på LV i steg 12.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE] 
>Ta alltid en ögonblicks bild av den virtuella datorn innan du ökar storleken på operativ system disken.

För att öka storleken på operativ system disken i en RHEL RAW-partition:

1. Stoppa den virtuella datorn.
1. Öka storleken på operativ system disken från portalen.
1. Starta den virtuella datorn.

Utför följande steg när den virtuella datorn har startats om:

1. Få åtkomst till den virtuella datorn som en **rot** användare med hjälp av följande kommando:
 
   ```
   sudo su
   ```

1. Installera **gptfdisk** -paketet, vilket krävs för att öka storleken på operativ system disken.

   ```
   yum install gdisk -y
   ```

1.  Kör följande kommando för att se alla sektorer som är tillgängliga på disken:
    ```
    gdisk -l /dev/sda
    ```

1. Du kommer att se informationen som informerar partitionstypen. Se till att det är GPT. Identifiera rotnoden. Ändra inte eller ta bort startpartitionen (BIOS-startpartitionen) och systempartitionen (EFI system partition)

1. Använd följande kommando för att starta partitionering för första gången. 
    ```
    gdisk /dev/sda
    ```

1. Nu ska du se ett meddelande som frågar efter nästa kommando ("kommando:? för hjälp "). 

   ```
   w
   ```

1. Du får en varning om att "varning! Sekundärt huvud har placerats för tidigt på disken! Vill du korrigera det här problemet? (J/N): ". Du måste trycka på "Y"

   ```
   Y
   ```

1. Du bör se ett meddelande som talar om att de slutliga kontrollerna har slutförts och att du uppmanas att bekräfta. Tryck på "Y"

   ```
   Y
   ```

1. Kontrol lera om allt hände korrekt med kommandot partprobe

   ```
   partprobe
   ```

1. Ovanstående steg har säkerställt att det sekundära GPT-huvudet placeras i slutet. Nästa steg är att starta processen med att ändra storlek genom att använda verktyget gdisk igen. Använd följande kommando.

   ```
   gdisk /dev/sda
   ```
1. I menyn kommando trycker du på "p" för att se en lista över partitioner. Identifiera rotnoden (i stegen är sda2 betraktas som rotnod) och startpartitionen (i stegen är sda3 betraktas som startpartition) 

   ```
   p
   ```
    ![Rotnod och startpartition](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Tryck på "d&quot; för att ta bort partitionen och välj det partitionsnummer som är tilldelat till Start (i det här exemplet är det &quot;3")
   ```
   d
   3
   ```
1. Tryck på "d" för att ta bort partitionen och välj det partitionsnummer som är tilldelat till Start (i det här exemplet är det ' 2 ')
   ```
   d
   2
   ```
    ![Ta bort rotnod och startpartition](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Om du vill skapa en ny rotnod med större storlek trycker du på "n", anger det partitionsnummer som du tog bort tidigare för roten ("2" i det här exemplet) och väljer den första sektorn som standardvärde, sista sektorn som sista sektor värde – start storleks sektor (' 4096 i det här fallet motsvarar 2 MB start) och hex-kod som "8300"
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Om du vill återskapa startpartitionen trycker du på "n", anger det partitionsnummer som du tog bort tidigare för start ("3" i det här exemplet) och väljer den första sektorn som standardvärdet, den sista sektorn som standardvärde och hex-kod som "EF02"
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Skriv ändringarna med kommandot "w" och tryck på "Y" för att bekräfta
   ```
   w
   Y
   ```
1. Kör kommandot ' partprobe ' för att kontrol lera disk stabiliteten
   ```
   partprobe
   ```
1. Starta om den virtuella datorn och rot partitionens storlek skulle ha ökat
   ```
   reboot
   ```

   ![Ny rotnod och startpartition](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Kör kommandot xfs_growfs på partitionen för att ändra storlek på det
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS-tillväxt FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)


1. Kontrol lera att den nya storleken visas med kommandot **DF** :

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
