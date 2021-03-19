---
title: Ändra storlek på diskar som är krypterade med Azure Disk Encryption
description: Den här artikeln innehåller instruktioner för att ändra storlek på ADE-krypterade diskar med hantering av logiska volymer.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 92e3be9111abe5712549ba85ef4550a9ac1473f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596815"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Ändra storlek på logiska enheter för volym hantering som använder Azure Disk Encryption

I den här artikeln får du lära dig hur du ändrar storlek på data diskar som använder Azure Disk Encryption. Om du vill ändra storlek på diskarna använder du LVM (Logical Volume Management) i Linux. Stegen gäller för flera scenarier.

Du kan använda den här processen för att ändra storlek i följande miljöer:

- Linux-distributioner:
    - Red Hat Enterprise Linux (RHEL) 7 eller senare
    - Ubuntu 16 eller senare
    - SUSE 12 eller senare
- Azure Disk Encryption versioner: 
    - Tillägg för ett enda pass
    - Dubbla pass-tillägg

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har:

- En befintlig LVM-konfiguration. Mer information finns i [Konfigurera LVM på en virtuell Linux-dator](/previous-versions/azure/virtual-machines/linux/configure-lvm).

- Diskar som redan är krypterade med Azure Disk Encryption. Mer information finns i [Konfigurera LVM och RAID på krypterade enheter](how-to-configure-lvm-raid-on-crypt.md).

- Användning av Linux och LVM.

- Användning av */dev/disk/SCSI1/* sökvägar för data diskar i Azure. Mer information finns i [Felsöka problem med enhets namn för Linux-datorer](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems). 

## <a name="scenarios"></a>Scenarier

Procedurerna i den här artikeln gäller för följande scenarier:

- Traditionella LVM-och LVM-krypterade konfigurationer
- Traditionell LVM-kryptering 
- LVM-on-Encryption 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Traditionella LVM-och LVM-krypterade konfigurationer

Traditionella LVM-och LVM-crypt-konfigurationer utökar en logisk volym (LV) när volym gruppen (VG) har tillgängligt utrymme.

### <a name="traditional-lvm-encryption"></a>Traditionell LVM-kryptering 

I traditionell LVM-kryptering krypteras LVs. Hela disken är inte krypterad.

Genom att använda traditionell LVM-kryptering kan du:

- Utöka LV när du lägger till en ny fysisk volym (PV).
- Förläng LV när du ändrar storlek på ett befintligt PV.

### <a name="lvm-on-crypt"></a>LVM-on-Encryption 

Den rekommenderade metoden för disk kryptering är LVM-on-Encryption. Med den här metoden krypteras hela disken, inte bara LV.

Genom att använda LVM-on-Encryption kan du: 

- Utöka LV när du lägger till ett nytt PV.
- Förläng LV när du ändrar storlek på ett befintligt PV.

> [!NOTE]
> Vi rekommenderar inte att du blandar traditionell LVM-kryptering och LVM-on-Encryption på samma virtuella dator.

I följande avsnitt finns exempel på hur du använder LVM och LVM-on-Encryption. I exemplen används befintliga värden för diskar, PVs, VGs, LVs, fil system, universellt unika identifierare (UUID) och monterings punkter. Ersätt värdena med dina egna värden så att de passar din miljö.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Utöka en LV när VG har tillgängligt utrymme

Det traditionella sättet att ändra storlek på LVs är att förlänga en LV när VG har tillgängligt utrymme. Du kan använda den här metoden för diskar som inte är krypterade, traditionella LVM-krypterade volymer och LVM-in-crypt-konfigurationer.

1. Kontrol lera den aktuella storleken på det fil system som du vill öka:

    ``` bash
    df -h /mountpoint
    ```

    ![Skärm bild som visar kod som kontrollerar storleken på fil systemet. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Kontrol lera att VG har tillräckligt med utrymme för att öka LV:

    ``` bash
    vgs
    ```

    ![Skärm bild som visar koden som söker efter utrymme på VG. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Du kan också använda `vgdisplay` :

    ``` bash
    vgdisplay vgname
    ```

    ![Skärm bild som visar den V G-bildskärms kod som söker efter utrymme på VG. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identifiera vilka LV som måste ändras:

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar resultatet av kommandot l s b l k. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    För LVM-on-Encryption är skillnaden att det här resultatet visar att det krypterade lagret är på disk nivå.

    ![Skärm bild som visar resultatet av kommandot l s b l k. Utdatan är markerad. Det visar det krypterade lagret.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Kontrol lera LV-storleken:

    ``` bash
    lvdisplay lvname
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på den logiska volymen. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Öka storleken på LV genom `-r` att använda för att ändra storlek på fil systemet online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Skärm bild som visar den kod som ökar storleken på den logiska volymen. Kommandot och resultaten är markerade.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Kontrol lera de nya storlekarna för LV och fil systemet:

    ``` bash
    df -h /mountpoint
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på LV och fil systemet. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Storleken på utdata indikerar att storleken på LV och fil systemet har ändrats.

Du kan kontrol lera LV-informationen igen för att bekräfta ändringarna på nivån LV:

``` bash
lvdisplay lvname
```

![Skärm bild som visar den kod som bekräftar de nya storlekarna. Storlekarna är markerade.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Utöka en traditionell LVM volym genom att lägga till ett nytt PV

När du behöver lägga till en ny disk för att öka VG storlek kan du utöka den traditionella LVM-volymen genom att lägga till ett nytt PV.

1. Kontrol lera den aktuella storleken på det fil system som du vill öka:

    ``` bash
    df -h /mountpoint
    ```

    ![Skärm bild som visar den kod som kontrollerar den aktuella storleken på ett fil system. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verifiera den aktuella PV-konfigurationen:

    ``` bash
    pvs
    ```

    ![Skärm bild som visar den kod som kontrollerar den aktuella PV-konfigurationen. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Kontrol lera den aktuella VG-informationen:

    ``` bash
    vgs
    ```

    ![Skärm bild som visar den kod som kontrollerar aktuell volym grupps information. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Kontrol lera listan över aktuella diskar. Identifiera data diskar genom att kontrol lera enheterna i */dev/disk/Azure/SCSI1/*.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Skärm bild som visar den kod som kontrollerar den aktuella disk listan. Kommandot och resultaten är markerade.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Kontrol lera utdata från `lsblk` : 

    ``` bash
    lsbk
    ```

    ![Skärm bild som visar den kod som kontrollerar utdata från l s b l k. Kommandot och resultaten är markerade.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Koppla den nya disken till den virtuella datorn genom att följa anvisningarna i [koppla en datadisk till en virtuell Linux-dator](attach-disk-portal.md).

7. Kontrol lera disk listan och Lägg märke till den nya disken.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Skärm bild som visar den kod som kontrollerar disk listan. Resultaten är markerade.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Skärm bild som visar den kod som kontrollerar disk listan med hjälp av l s b l k. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Skapa ett nytt PV ovanpå den nya data disken:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Skärm bild som visar koden som skapar ett nytt PV. Resultatet är markerat.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Den här metoden använder hela disken som ett PV utan partition. Du kan också använda `fdisk` för att skapa en partition och sedan använda den partitionen för `pvcreate` .

9. Kontrol lera att PV har lagts till i listan PV:

    ``` bash
    pvs
    ```

    ![Skärm bild som visar koden som visar listan över fysiska volymer. Resultatet är markerat.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Utöka VG genom att lägga till det nya PV:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Skärm bild som visar koden som utökar volym gruppen. Resultatet är markerat.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Kontrol lera den nya storleken för VG:

    ``` bash
    vgs
    ```

    ![Skärm bild som visar den kod som kontrollerar volym gruppens storlek. Resultaten är markerade.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Används `lsblk` för att identifiera den LV som behöver storleks änd ras:

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar den kod som identifierar den lokala volym som måste ändras. Resultaten är markerade.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Utöka LV-storleken genom `-r` att använda för att öka fil systemet online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Skärm bild som visar kod som ökar storleken på fil systemet online. Resultaten är markerade.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verifiera de nya storlekarna på LV och fil systemet:

    ``` bash
    df -h /mountpoint
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på den lokala volymen och fil systemet. Kommandot och resultatet är markerat.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >När Azure Data Encryption används på traditionella LVM-konfigurationer skapas det krypterade lagret på LV-nivå, inte på disk nivå.
    >
    >I det här läget expanderas det krypterade lagret till den nya disken. Den faktiska datadisken har inga krypterings inställningar på plattforms nivån, så dess krypterings status är inte uppdaterad.
    >
    >Detta är några av orsakerna till varför LVM-on-Encryption är den rekommenderade metoden. 

15. Kontrol lera krypterings informationen från portalen:

    ![Skärm bild som visar krypterings information i portalen. Disk namnet och krypteringen markeras.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Om du vill uppdatera krypterings inställningarna på disken lägger du till en ny LV och aktiverar tillägget på den virtuella datorn.
    
16. Lägg till ett nytt LV, skapa ett fil system på det och Lägg till det i `/etc/fstab` .

17. Ange krypterings tillägget igen. Nu ska du stämpla krypterings inställningarna på den nya data disken på plattforms nivå. Här är ett CLI-exempel:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Kontrol lera krypterings informationen från portalen:

    ![Skärm bild som visar krypterings information i portalen. Disk namnet och krypterings informationen är markerad.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

När krypterings inställningarna har uppdaterats kan du ta bort det nya LV. Ta även bort posten från `/etc/fstab` och `/etc/crypttab` som du skapade.

![Skärm bild som visar den kod som tar bort den nya logiska volymen. Fliken borttagna F S och fliken crypt är markerad.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Slutför rensningen genom att följa dessa steg:

1. Demontera LV:

    ``` bash
    umount /mountpoint
    ```

1. Stäng volymens krypterade skikt:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Ta bort LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Utöka en traditionell LVM-volym genom att ändra storlek på ett befintligt PV

Snabb meddelanden vissa scenarier, dina begränsningar kan kräva att du ändrar storlek på en befintlig disk. Gör så här:

1. Identifiera dina krypterade diskar:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Skärm bild som visar koden som identifierar krypterade diskar. Resultaten är markerade.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Skärm bild som visar alternativa koder som identifierar krypterade diskar. Resultaten är markerade.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Kontrol lera PV-informationen:

    ``` bash
    pvs
    ```

    ![Skärm bild som visar den kod som kontrollerar information om den fysiska volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Resultaten i bilden visar att allt utrymme på alla PVs används för närvarande.

3. Kontrol lera VG-informationen:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Skärm bild som visar den kod som kontrollerar information om volym gruppen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Kontrol lera disk storlekarna. Du kan använda `fdisk` eller `lsblk` för att Visa enhets storlekarna.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Skärm bild som visar den kod som kontrollerar disk storlekarna. Resultaten är markerade.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Här identifierade vi vilka PVs som är kopplade till vilka LVs med hjälp av `lsblk -fs` . Du kan identifiera kopplingarna genom att köra `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Skärm bild som visar ett alternativt sätt att identifiera fysiska volym associationer med lokala volymer. Resultaten är markerade.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    I det här fallet är alla fyra data enheter en del av samma VG och en enda LV. Konfigurationen kan skilja sig.

5. Kontrol lera den aktuella fil system användningen:

    ``` bash
    df -h /datalvm*
    ```

    ![Skärm bild som visar koden som kontrollerar användningen av fil systemet. Kommandot och resultaten är markerade.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Ändra storlek på data diskarna genom att följa anvisningarna i [expandera en Azure-hanterad disk](expand-disks.md#expand-an-azure-managed-disk). Du kan använda portalen, CLI eller PowerShell.

    >[!IMPORTANT]
    >Du kan inte ändra storlek på virtuella diskar när den virtuella datorn körs. Frigör den virtuella datorn för det här steget.

7. Starta den virtuella datorn och kontrol lera de nya storlekarna genom att använda `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Skärm bild som visar den kod som kontrollerar disk storleken. Resultatet är markerat.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    I det här fallet `/dev/sdd` ändrades storleken från 5 g till 20 g.

8. Kontrol lera aktuell PV-storlek:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på P V. Resultatet är markerat.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Även om storleken på disken har ändrats har PV fortfarande den tidigare storleken.

9. Ändra storlek på PV:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Skärm bild som visar den kod som ändrar storlek på den fysiska volymen. Resultatet är markerat.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Kontrol lera PV-storleken:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på den fysiska volymen. Resultatet är markerat.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Använd samma procedur för alla diskar som du vill ändra storlek på.

11. Kontrol lera VG-informationen.

    ``` bash
    vgdisplay vgname
    ```

    ![Skärm bild som visar den kod som kontrollerar information för volym gruppen. Resultatet är markerat.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG har nu tillräckligt med utrymme för att allokeras till LVs.

12. Ändra storlek på LV:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Skärm bild som visar den kod som ändrar storlek på L V. Resultaten är markerade.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Kontrol lera fil systemets storlek:

    ``` bash
    df -h /datalvm2
    ```

    ![Skärm bild som visar den kod som kontrollerar fil systemets storlek. Resultatet är markerat.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Utöka en LVM-on-Encryption-volym genom att lägga till ett nytt PV

Du kan också utöka en LVM-on-Encryption-volym genom att lägga till ett nytt PV. Den här metoden följer stegen i [Konfigurera LVM och RAID på krypterade enheter](how-to-configure-lvm-raid-on-crypt.md#general-steps). Se de avsnitt som förklarar hur du lägger till en ny disk och konfigurerar den i en LVM-on-Encryption-konfiguration.

Du kan använda den här metoden för att lägga till utrymme i ett befintligt LV. Eller så kan du skapa nya VGs eller LVs.

1. Verifiera den aktuella storleken på din VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Skärm bild som visar den kod som kontrollerar volym gruppens storlek. Resultaten är markerade.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Kontrol lera storleken på det fil system och den LV som du vill expandera:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på den lokala volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Skärm bild som visar den kod som kontrollerar fil systemets storlek. Resultatet är markerat.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Lägg till en ny datadisk till den virtuella datorn och identifiera den.

    Innan du lägger till den nya disken kontrollerar du diskarna:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på diskarna. Resultatet är markerat.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Här är ett annat sätt att kontrol lera diskarna innan du lägger till den nya disken:

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar en alternativ kod som kontrollerar storleken på diskarna. Resultaten är markerade.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Om du vill lägga till den nya disken kan du använda PowerShell, Azure CLI eller Azure Portal. Mer information finns i [koppla en datadisk till en virtuell Linux-dator](attach-disk-portal.md).

    Kernel Name-schemat gäller för den nyligen tillagda enheten. En ny enhet tilldelas normalt nästa tillgängliga bokstav. I det här fallet är den tillagda disken `sdd` .

4. Kontrol lera diskarna för att se till att den nya disken har lagts till:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Skärm bild som visar den kod som visar diskarna. Resultaten är markerade.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar den nyligen tillagda disken i utdata.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Skapa ett fil system ovanpå den nyligen tillagda disken. Matcha disken med de länkade enheterna på `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Skärm bild som visar koden som skapar ett fil system. Resultaten är markerade.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Skärm bild som visar ytterligare kod som skapar ett fil system och matchar disken med de länkade enheterna. Resultaten är markerade.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Skapa en tillfällig monterings punkt för den nya disk som har lagts till:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Lägg till det senast skapade fil systemet i `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Montera det nyligen skapade fil systemet:

    ``` bash
    mount -a
    ```

9. Kontrol lera att det nya fil systemet är monterat:

    ``` bash
    df -h
    ```

    ![Skärm bild som visar den kod som kontrollerar att fil systemet är monterat. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar ytterligare kod som kontrollerar att fil systemet är monterat. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Starta om krypteringen som du tidigare har startat för data enheter.

    >[!TIP]
    >Vi rekommenderar att du använder för LVM-on-Encryption `EncryptFormatAll` . Annars kan du se en dubbel kryptering medan du anger ytterligare diskar.
    >
    >Mer information finns i [Konfigurera LVM och RAID på krypterade enheter](how-to-configure-lvm-raid-on-crypt.md).

    Här är ett exempel:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    När krypteringen är klar visas ett krypterings skikt på den nyligen tillagda disken:

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar den kod som kontrollerar krypterings skiktet. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Demontera det krypterade lagret på den nya disken:

    ``` bash
    umount ${newmount}
    ```

12. Kontrol lera den aktuella PV-informationen:

    ``` bash
    pvs
    ```

    ![Skärm bild som visar den kod som kontrollerar information om den fysiska volymen. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Skapa ett PV ovanpå det krypterade lagret på disken. Ta enhets namnet från föregående `lsblk` kommando. Lägg till en `/dev/` mapp framför enhets namnet för att skapa PV:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Skärm bild som visar koden som skapar en fysisk volym på det krypterade lagret. Resultaten är markerade.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Du ser en varning om att rensa den aktuella `ext4 fs` signaturen. Den här varningen förväntas. Besvara den här frågan med `y` .

14. Kontrol lera att det nya PV har lagts till i LVM-konfigurationen:

    ``` bash
    pvs
    ```

    ![Skärm bild som visar den kod som kontrollerar att den fysiska volymen har lagts till i LVM-konfigurationen. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Lägg till det nya PV-VG som du behöver öka.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Skärm bild som visar koden som lägger till en fysisk volym i en volym grupp. Resultaten är markerade.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Verifiera den nya storleken och det fria utrymmet på VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Skärm bild som visar den kod som kontrollerar storlek och ledigt utrymme för volym gruppen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Observera ökningen av `Total PE` antalet och `Free PE / Size` .

17. Öka storleken på LV och fil systemet. Använd `-r` alternativet på `lvextend` . I det här exemplet lägger vi till det totala tillgängliga utrymmet i VG till angivet LV.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Skärm bild som visar den kod som ökar storleken på den lokala volymen och fil systemet. Resultaten är markerade.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Följ nästa steg för att verifiera ändringarna.

1. Verifiera storleken på LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Skärm bild som visar den kod som verifierar den nya storleken på den lokala volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Kontrol lera den nya storleken på fil systemet:

    ``` bash
    df -h mountpoint
    ```

    ![Skärm bild som visar den kod som verifierar fil systemets nya storlek. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Kontrol lera att LVM-lagret är ovanpå det krypterade lagret:

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar den kod som kontrollerar att LVM-lagret är ovanpå det krypterade lagret. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Om du använder `lsblk` utan alternativ visas monterings punkterna flera gånger. Kommandot sorterar efter enhets-och LVs. 

    Du kanske vill använda `lsblk -fs` . I det här kommandot `-fs` vänder sig sorterings ordningen så att monterings punkterna visas en gång. Diskarna visas flera gånger.

    ``` bash
    lsblk -fs
    ```

    ![Skärm bild som visar en alternativ kod som verifierar att LVM-lagret är ovanpå det krypterade lagret. Resultatet är markerat.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Utöka en LVM på en krypterings volym genom att ändra storlek på ett befintligt PV

1. Identifiera dina krypterade diskar:

    ``` bash
    lsblk
    ```

    ![Skärm bild som visar den kod som identifierar de krypterade diskarna. Resultaten är markerade.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Skärm bild som visar alternativa koder som identifierar de krypterade diskarna. Resultaten är markerade.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Kontrol lera din PV-information:

    ``` bash
    pvs
    ```

    ![Skärm bild som visar den kod som kontrollerar information om fysiska volymer. Resultaten är markerade.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Kontrol lera din VG-information:

    ``` bash
    vgs
    ```

    ![Skärm bild som visar den kod som kontrollerar information för volym grupper. Resultaten är markerade.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Kontrol lera din LV-information:

    ``` bash
    lvs
    ```

    ![Skärm bild som visar den kod som kontrollerar information för den lokala volymen. Resultatet är markerat.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Kontrol lera fil system användningen:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Skärm bild som visar den kod som kontrollerar hur mycket av fil systemet som används. Resultaten är markerade.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Kontrol lera disk storlekarna:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på diskarna. Resultaten är markerade.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Ändra storlek på data disken. Du kan använda portalen, CLI eller PowerShell. Mer information finns i avsnittet disk-ändra storlek i [expandera virtuella hård diskar på en virtuell Linux-dator](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >Du kan inte ändra storlek på virtuella diskar när den virtuella datorn körs. Frigör den virtuella datorn för det här steget.

8. Kontrol lera disk storlekarna:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Skärm bild som visar kod som kontrollerar disk storlekar. Resultaten är markerade.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    I det här fallet ändrades storleken på båda diskarna från 2 GB till 4 GB. Men storleken på fil systemet, LV och PV är oförändrad.

9. Kontrol lera den aktuella PV-storleken. Kom ihåg att vid LVM-on-Encryption är PV `/dev/mapper/` enheten, inte `/dev/sd*` enheten.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på den aktuella fysiska volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Ändra storlek på PV:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Skärm bild som visar den kod som ändrar storlek på den fysiska volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Kontrol lera den nya PV-storleken:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Skärm bild som visar den kod som kontrollerar storleken på den fysiska volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Ändra storlek på det krypterade lagret på PV:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Använd samma procedur för alla diskar som du vill ändra storlek på.

13. Kontrol lera din VG-information:

    ``` bash
    vgdisplay vgname
    ```

    ![Skärm bild som visar den kod som kontrollerar information för volym gruppen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG har nu tillräckligt med utrymme för att allokeras till LVs.

14. Kontrol lera LV-informationen:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Skärm bild som visar den kod som kontrollerar information för den lokala volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Kontrol lera fil system användningen:

    ``` bash
    df -h /mountpoint
    ```

    ![Skärm bild som visar den kod som kontrollerar användningen av fil systemet. Resultaten är markerade.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Ändra storlek på LV:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Skärm bild som visar den kod som ändrar storlek på den lokala volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Här använder vi `-r` alternativet för att även ändra storlek på fil systemet.

17. Kontrol lera LV-informationen:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Skärm bild som visar koden som hämtar information om den lokala volymen. Resultaten är markerade.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Kontrol lera fil system användningen:

    ``` bash
    df -h /mountpoint
    ```

    ![Skärm bild som visar koden som kontrollerar användningen av fil systemet. Resultaten är markerade.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Tillämpa samma storlek på ändrings proceduren på alla andra LV som kräver det.

## <a name="next-steps"></a>Nästa steg

[Felsöka Azure Disk Encryption](disk-encryption-troubleshooting.md)