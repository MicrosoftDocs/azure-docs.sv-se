---
title: Koppla en datadisk till en virtuell Linux-dator
description: Använd portalen för att koppla en ny eller befintlig datadisk till en virtuell Linux-dator.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: ca8584d6cce3f5487d84f6647c42274879d610dd
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98198075"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Använd portalen för att koppla en datadisk till en virtuell Linux-dator 
Den här artikeln visar hur du ansluter både nya och befintliga diskar till en virtuell Linux-dator via Azure Portal. Du kan också [ansluta en datadisk till en virtuell Windows-dator i Azure Portal](../windows/attach-managed-disk-portal.md). 

Innan du kopplar diskar till din virtuella dator kan du läsa följande tips:

* Storleken på den virtuella datorn styr hur många data diskar du kan koppla. Mer information finns i [storlekar för virtuella datorer](../sizes.md).
* Diskar som är anslutna till virtuella datorer är faktiskt VHD-filer som lagras i Azure. Mer information finns i [Introduktion till Managed disks](../managed-disks-overview.md).
* När du har anslutit disken måste du [ansluta till den virtuella Linux-datorn för att montera den nya disken](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Hitta den virtuella datorn
1. Gå till [Azure Portal](https://portal.azure.com/) för att hitta den virtuella datorn. Sök efter och välj **virtuella datorer**.
2. Välj den virtuella datorn i listan.
3. På sidan **virtuella datorer** väljer du **diskar** under **Inställningar**.


## <a name="attach-a-new-disk"></a>Koppla en ny disk

1. I fönstret **diskar** under **data diskar** väljer du **skapa och koppla en ny disk**.

1. Ange ett namn för den hanterade disken. Granska standardinställningarna och uppdatera **lagrings typ**, **storlek (GIB)**, **kryptering** och **cachelagring av värden** vid behov.
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Granska disk inställningar.":::


1. När du är färdig väljer du **Spara** längst upp på sidan för att skapa den hanterade disken och uppdatera konfigurationen för den virtuella datorn.


## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
1. I fönstret **diskar** under **data diskar** väljer du  **Anslut befintliga diskar**.
1. Klicka på den nedrullningsbara menyn för **disk namn** och välj en disk i listan över tillgängliga hanterade diskar. 

1. Klicka på **Spara** för att koppla den befintliga hanterade disken och uppdatera VM-konfigurationen:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ansluta till den virtuella Linux-datorn för att montera den nya disken
För att partitionera, formatera och montera den nya disken så att din virtuella Linux-dator kan använda den, SSH i den virtuella datorn. Mer information finns i [Så här använder du SSH med Linux på Azure](mac-create-ssh-keys.md). Följande exempel ansluter till en virtuell dator med den offentliga IP-adressen för *10.123.123.25* med användar namnet *azureuser*: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>Hitta disken

När du har anslutit till den virtuella datorn måste du hitta disken. I det här exemplet använder vi `lsblk` för att lista diskarna. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Utdata ser ut ungefär så här:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
```

I det här exemplet är den disk som jag har lagt till `sdc` . Det är en LUN 0 och är 4 GB.

Ett mer avancerat exempel är hur flera data diskar ser ut i portalen:

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Granska disk inställningar.":::

I avbildningen kan du se att det finns tre data diskar: 4 GB på LUN 0, 16 GB vid LUN 1 och 32G vid LUN 2.

Så här kan det se ut som om du använder `lsblk` :

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

Från utdata från `lsblk` kan du se att den 4 GB disk på LUN 0 är `sdc` , 16 GB-disken på LUN 1 `sdd` och 32G-disken på LUN 2 är `sde` .

### <a name="partition-a-new-disk"></a>Partitionera en ny disk

Om du använder en befintlig disk som innehåller data kan du gå vidare till montera disken. Om du ansluter till en ny disk måste du partitionera disken.

`parted`Verktyget kan användas för att partitionera och formatera en datadisk.

> [!NOTE]
> Vi rekommenderar att du använder den senaste versionen `parted` som är tillgänglig för din distribution.
> Om disk storleken är 2 tebibyte (TiB) eller större måste du använda GPT-partitionering. Om disk storleken är under 2 TiB kan du antingen använda MBR-eller GPT-partitionering.  


I följande exempel används `parted` på `/dev/sdc` , som är den första data disken som normalt kommer på de flesta virtuella datorer. Ersätt `sdc` med rätt alternativ för disken. Vi formaterar också det med [xfs](https://xfs.wiki.kernel.org/) -fil systemet.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Använd [`partprobe`](https://linux.die.net/man/8/partprobe) verktyget för att se till att kärnan är medveten om den nya partitionen och fil systemet. Om det inte `partprobe` går att använda kan blkid-eller lslbk-kommandona inte returnera UUID för det nya fil systemet omedelbart.

### <a name="mount-the-disk"></a>Montera disken

Skapa en katalog för att montera fil systemet med hjälp av `mkdir` . I följande exempel skapas en katalog på `/datadrive` :

```bash
sudo mkdir /datadrive
```

Använd `mount` för att montera fil systemet. I följande exempel monteras */dev/sdc1* -partitionen till `/datadrive` monterings punkten:

```bash
sudo mount /dev/sdc1 /datadrive
```

För att säkerställa att enheten monteras om automatiskt efter en omstart måste den läggas till i */etc/fstab* -filen. Det rekommenderas också starkt att UUID (Universal Unique Identifier) används i */etc/fstab* för att referera till enheten i stället för bara enhets namnet (t. ex. */dev/sdc1*). Om operativsystemet upptäcker ett diskfel vid start och använder UUID undviker du att den felaktiga disken monteras på en viss plats. Återstående datadiskar tilldelas sedan samma enhets-ID:n. Du kan hitta UUID för den nya enheten med verktyget `blkid`:

```bash
sudo blkid
```

Utdata ser ut ungefär som i följande exempel:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> Felaktig redigering av **/etc/fstab** -filen kan leda till ett system som inte kan startas. Om du är osäker läser du distributionens dokumentation för att få information om hur du redigerar filen på rätt sätt. Vi rekommenderar också att du skapar en säkerhets kopia av/etc/fstab-filen innan du redigerar.

Öppna sedan */etc/fstab* -filen i en text redigerare enligt följande:

```bash
sudo nano /etc/fstab
```

I det här exemplet använder du UUID-värdet för `/dev/sdc1` enheten som skapades i föregående steg och monterings punkt för `/datadrive` . Lägg till följande rad i slutet av `/etc/fstab` filen:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

Vi använde nano-redigeraren, så när du är klar med att redigera filen använder `Ctrl+O` du för att skriva filen och `Ctrl+X` Avsluta redigeraren.

> [!NOTE]
> Om du senare tar bort en datadisk utan att redigera fstab kan det hända att den virtuella datorn inte kan starta. De flesta distributioner ger antingen alternativen *nomisslyckande* och/eller *nobootwait* fstab. De här alternativen gör det möjligt för ett system att starta även om disken inte kan monteras vid start. Mer information om dessa parametrar finns i distributionens dokumentation.
> 
> Alternativet *nomisslyckande* ser till att den virtuella datorn startar även om fil systemet är skadat eller om disken inte finns vid start. Utan det här alternativet kan du stöta på det sätt som beskrivs i [kan inte användas med SSH till Linux på grund av FSTAB-fel](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)


## <a name="verify-the-disk"></a>Verifiera disken

Nu kan du använda `lsblk` igen för att se disk-och monterings punkt.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Utdata ser ut ungefär så här:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

Du kan se att `sdc` är nu monterad på `/datadrive` .

### <a name="trimunmap-support-for-linux-in-azure"></a>Stöd för trimning/MAPPNING för Linux i Azure

Vissa Linux-Kernels stöder TRIMNINGs-/MAPPNINGs åtgärder för att ta bort oanvända block på disken. Den här funktionen är främst användbar i standard lagring för att informera Azure om att borttagna sidor inte längre är giltiga och kan tas bort och du kan spara pengar om du skapar stora filer och sedan tar bort dem.

Det finns två sätt att aktivera TRIMNINGs stöd i din virtuella Linux-dator. Som vanligt kan du kontakta din distribution för den rekommenderade metoden:

* Använd `discard` alternativet Mount i */etc/fstab*, till exempel:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* I vissa fall `discard` kan alternativet ha prestanda konsekvenser. Du kan också köra `fstrim` kommandot manuellt från kommando raden eller lägga till det i crontab för att köra regelbundet:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>Nästa steg
Du kan också [ansluta en datadisk](add-disk.md) med hjälp av Azure CLI.