---
title: Lägg till en datadisk till en virtuell Linux-dator med hjälp av Azure CLI
description: Lär dig att lägga till en beständig datadisk till din virtuella Linux-dator med Azure CLI
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: adf6198cf12011c77fcf3f93d4b595ea433ddefd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580393"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Lägg till en disk till en virtuell Linux-dator

Den här artikeln visar hur du ansluter en beständig disk till den virtuella datorn så att du kan bevara dina data, även om din virtuella dator har reserveras på grund av underhåll eller storleks ändring.


## <a name="attach-a-new-disk-to-a-vm"></a>Koppla en ny disk till en virtuell dator

Om du vill lägga till en ny tom datadisk på den virtuella datorn använder du kommandot [AZ VM disk Attach](/cli/azure/vm/disk) med `--new` parametern. Om den virtuella datorn finns i en tillgänglighets zon skapas disken automatiskt i samma zon som den virtuella datorn. Mer information finns i [Översikt över Tillgänglighetszoner](../../availability-zones/az-overview.md). I följande exempel skapas en disk med namnet *myDataDisk* som är 50 GB i storlek:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk

Om du vill koppla en befintlig disk letar du reda på disk-ID och skickar ID: t till kommandot [AZ VM disk Attach](/cli/azure/vm/disk) . Följande exempel frågar efter en disk med namnet *myDataDisk* i *myResourceGroup* och kopplar den sedan till den virtuella datorn med namnet *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Formatera och montera disken

För att partitionera, formatera och montera den nya disken så att din virtuella Linux-dator kan använda den, SSH i den virtuella datorn. Mer information finns i [Så här använder du SSH med Linux på Azure](mac-create-ssh-keys.md). Följande exempel ansluter till en virtuell dator med den offentliga IP-adressen för *10.123.123.25* med användar namnet *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Hitta disken

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
sdc     3:0:0:0      50G
```

Här `sdc` är den disk som vi vill ha, eftersom den är 50G. Om du inte är säker på vilken disk som den endast baseras på en storlek kan du gå till sidan för virtuella datorer i portalen, välja **diskar** och kontrol lera LUN-numret för disken under **data diskar**. 


### <a name="format-the-disk"></a>Formatera disken

Formatera disken med `parted` , om disk storleken är 2 tebibyte (TIB) eller större, måste du använda GPT-partitionering, om den finns under 2TiB, så kan du antingen använda MBR eller GPT-partitionering. 

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

Skapa nu en katalog för att montera fil systemet med hjälp av `mkdir` . I följande exempel skapas en katalog på `/datadrive` :

```bash
sudo mkdir /datadrive
```

Använd `mount` för att montera fil systemet. I följande exempel monterar `/dev/sdc1` partitionen till `/datadrive` monterings punkten:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Behåll monteringen

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

I det här exemplet använder vi nano-redigeraren, så när du är klar med att redigera filen använder `Ctrl+O` du för att skriva filen och `Ctrl+X` Avsluta redigeraren.

> [!NOTE]
> Om du senare tar bort en datadisk utan att redigera fstab kan det hända att den virtuella datorn inte kan starta. De flesta distributioner ger antingen alternativen *nomisslyckande* och/eller *nobootwait* fstab. De här alternativen gör det möjligt för ett system att starta även om disken inte kan monteras vid start. Mer information om dessa parametrar finns i distributionens dokumentation.
>
> Alternativet *nomisslyckande* ser till att den virtuella datorn startar även om fil systemet är skadat eller om disken inte finns vid start. Utan det här alternativet kan du stöta på det sätt som beskrivs i [kan inte användas med SSH till Linux på grund av FSTAB-fel](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)
>
> Den seriella Azure VM-konsolen kan användas för konsol åtkomst till den virtuella datorn om en ändring av fstab har resulterat i ett startfel. Mer information finns i dokumentationen för den [seriella konsolen](/troubleshoot/azure/virtual-machines/serial-console-linux).

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

## <a name="troubleshooting"></a>Felsökning

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Nästa steg

* För att säkerställa att din virtuella Linux-dator har kon figurer ATS korrekt kan du granska prestanda rekommendationerna [optimera dina Linux-datorer](/previous-versions/azure/virtual-machines/linux/optimization) .
* Utöka lagrings kapaciteten genom att lägga till ytterligare diskar och [Konfigurera RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) för ytterligare prestanda.