---
title: Förbered en virtuell Debian Linux-hårddisk
description: Lär dig hur du skapar Debian VHD-avbildningar för distributioner av virtuella datorer i Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d822aa30926c473d08bc5d785211c82d038ef652
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203192"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Förbered en virtuell Debian-VHD för Azure
## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet förutsätter att du redan har installerat ett Debian Linux-operativsystem från en. ISO-fil som hämtats från [Debian-webbplatsen](https://www.debian.org/distrib/) till en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer; Hyper-V är bara ett exempel. Instruktioner för hur du använder Hyper-V finns i [Installera Hyper-v-rollen och konfigurera en virtuell dator](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Installations information
* Se även [allmänna Linux-Installationsinstruktioner](create-upload-generic.md#general-linux-installation-notes) för mer information om hur du förbereder Linux för Azure.
* Det nya VHDX-formatet stöds inte i Azure. Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten **Convert-VHD** .
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner snarare än LVM (vanligt vis som standard för många installationer). På så sätt undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för fel sökning. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) eller [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) kan användas på data diskar om det är lämpligt.
* Konfigurera inte en swap-partition på OS-disken. Azure Linux-agenten kan konfigureras för att skapa en växlings fil på den tillfälliga resurs disken. Mer information hittar du i stegen nedan.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till en virtuell hård disk måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information finns i [installations information för Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Använd Azure-Manage för att skapa Debian-VHD: er
Det finns verktyg som är tillgängliga för att skapa Debian-VHD: er för Azure, till exempel [Azure-hantera](https://github.com/credativ/azure-manage) skript från [credativ](https://www.credativ.com/). Detta är den rekommenderade metoden jämfört med att skapa en avbildning från grunden. Om du till exempel vill skapa en Debian 8-VHD kör du följande kommandon för att ladda ned `azure-manage` verktyget (och beroenden) och köra `azure_build_image` skriptet:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Förbered en virtuell Debian-VHD manuellt
1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Anslut** för att öppna ett konsol fönster för den virtuella datorn.
3. Om du har installerat operativ systemet med hjälp av ISO kan du kommentera ut vilken rad som helst som relaterar till " `deb cdrom` " i `/etc/apt/source.list` .

4. Redigera `/etc/default/grub` filen och ändra **GRUB_CMDLINE_LINUX** -parametern enligt följande om du vill inkludera ytterligare kernel-parametrar för Azure.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Återskapa grub och kör:

    ```console
    # sudo update-grub
    ```

6. Lägg till Debian Azure-databaser till/etc/apt/sources.list för antingen Debian 8, 9 eller 10:

    **Debian 8. x "Jessie"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9. x "sträckning"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```
    
    **Debian 10. x "Buster"**
    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian buster main
    deb-src http://debian-archive.trafficmanager.net/debian buster main
    deb http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb http://debian-archive.trafficmanager.net/debian buster-updates main
    deb-src http://debian-archive.trafficmanager.net/debian buster-updates main
    deb http://debian-archive.trafficmanager.net/debian buster-backports main
    deb-src http://debian-archive.trafficmanager.net/debian buster-backports main
    ```

7. Installera Azure Linux-agenten:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. För Debian 9 + rekommenderar vi att du använder den nya Debian Cloud-kärnan för användning med virtuella datorer i Azure. Om du vill installera den nya kerneln måste du först skapa en fil med namnet/etc/apt/preferences.d/Linux.PREF med följande innehåll:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Kör sedan "sudo apt-get install linux-image-Cloud-amd64" för att installera den nya Debian Cloud-kärnan.

9. Avetablera den virtuella datorn och Förbered den för etablering på Azure och kör:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Klicka på **åtgärd** -> stänga av i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din virtuella Debian-hård disk för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).