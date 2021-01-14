---
title: Skapa och ladda upp en Ubuntu Linux VHD i Azure
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Ubuntu Linux operativ system.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: ca1aae3e819d58ea32453f2549d162bbfc9c91ae
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203226"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Förbereda en virtuell Ubuntu-dator för Azure


Ubuntu publicerar nu officiella Azure-VHD: er för nedladdning på [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Om du behöver skapa en egen specialiserad Ubuntu-avbildning för Azure, i stället för att använda den manuella proceduren nedan, rekommenderar vi att du börjar med de kända virtuella hård diskarna och anpassar efter behov. De senaste avbildnings versionerna kan alltid hittas på följande platser:

* Ubuntu 16.04/xenial: [Ubuntu-16,04-Server-cloudimg-amd64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du redan har installerat ett Ubuntu Linux operativ system på en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Anvisningar finns i [Installera Hyper-V-rollen och konfigurera en virtuell dator](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Installations information för Ubuntu**

* Se även [allmänna Linux-Installationsinstruktioner](create-upload-generic.md#general-linux-installation-notes) för mer information om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hård disk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller `Convert-VHD` cmdleten.
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner snarare än LVM (vanligt vis som standard för många installationer). På så sätt undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för fel sökning. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) eller [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) kan användas på data diskar om det är lämpligt.
* Konfigurera inte en swap-partition eller swapfile på OS-disken. Konfigurations agenten för Cloud-Init kan konfigureras för att skapa en växlings fil eller en swap-partition på den tillfälliga resurs disken. Mer information om detta finns i stegen nedan.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information finns i [installations information för Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Manuella steg
> [!NOTE]
> Innan du försöker skapa en egen anpassad Ubuntu-avbildning för Azure bör du överväga att använda de förbyggda och testade avbildningarna i [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) stället.
> 
> 

1. I mittenfönstret i Hyper-V Manager väljer du den virtuella datorn.

2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.

3. Ersätt de aktuella databaserna i avbildningen så att de använder Ubuntu Azure-lagringsplatsen.

    Innan du redigerar bör du `/etc/apt/sources.list` göra en säkerhets kopia:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16,04 och Ubuntu 18,04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Ubuntu Azure-avbildningarna använder nu [Azure-skräddarsydda kernel](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel). Uppdatera operativ systemet till den senaste Azure-anpassade kärnan och installera Azure Linux-verktyg (inklusive Hyper-V-beroenden) genom att köra följande kommandon:

    Ubuntu 16,04 och Ubuntu 18,04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Ändra start raden för kernel för grub för att inkludera ytterligare kernel-parametrar för Azure. Öppna `/etc/default/grub` en text redigerare genom att leta upp variabeln `GRUB_CMDLINE_LINUX_DEFAULT` (eller lägga till den vid behov) och redigera den för att inkludera följande parametrar:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Spara och Stäng filen och kör sedan `sudo update-grub` . Detta säkerställer att alla konsol meddelanden skickas till den första serie porten, vilket kan hjälpa Azure Technical Support med fel söknings problem.

6. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.

7. Installera Cloud-Init (etablerings agenten) och Azure Linux-agenten (gäst tilläggs hanteraren). Cloud-Init använder netplan för att konfigurera systemets nätverks konfiguration under etableringen och varje efterföljande omstart.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  `walinuxagent`Paketet kan ta bort `NetworkManager` -och `NetworkManager-gnome` -paketen om de är installerade.

8. Ta bort standard konfigurationerna för Cloud-Init och överblivna netplan-artefakter som kan vara i konflikt med Cloud-Init-etablering på Azure:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Konfigurera Cloud-Init för att etablera systemet med hjälp av Azure DataSource:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Konfigurera Azure Linux-agenten så att den förlitar sig på Cloud-Init för att utföra etableringen. Titta på [WALinuxAgent-projektet](https://github.com/Azure/WALinuxAgent) om du vill ha mer information om de här alternativen.

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Rensa Cloud-Init-och Azure Linux-agentens körnings artefakter och loggar:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

    > [!NOTE]
    > `sudo waagent -force -deprovision+user`Kommandot försöker rensa systemet och göra det lämpligt för ometablering. `+user`Alternativet tar bort det senast etablerade användar kontot och associerade data.

    > [!WARNING]
    > Avetablering med kommandot ovan garanterar inte att avbildningen är klar med all känslig information och är lämplig för omdistribution.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Klicka på **åtgärd-> stänga av** i Hyper-V Manager.

14. Azure accepterar bara virtuella hård diskar med fast storlek. Om den virtuella datorns OS-disk inte är en virtuell hård disk med fast storlek använder du `Convert-VHD` PowerShell-cmdleten och anger `-VHDType Fixed` alternativet. Ta en titt på dokumenten `Convert-VHD` här: [Convert-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Ubuntu Linux virtuella hård disk för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).