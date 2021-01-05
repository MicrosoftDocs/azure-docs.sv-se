---
title: Skapa och ladda upp en Oracle Linux VHD
description: Lär dig att skapa och ladda upp en virtuell Azure-hårddisk (VHD) som innehåller ett Oracle Linux operativ system.
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: b63591db7f72e655839ee6f575e49bbf873abc5b
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895469"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Förbereda en Oracle Linux virtuell dator för Azure

Den här artikeln förutsätter att du redan har installerat ett Oracle Linux operativ system på en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Anvisningar finns i [Installera Hyper-V-rollen och konfigurera en virtuell dator](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Oracle Linux installations anmärkningar
* Se även [allmänna Linux-Installationsinstruktioner](create-upload-generic.md#general-linux-installation-notes) för mer information om hur du förbereder Linux för Azure.
* Stöd för Hyper-V och Azure Oracle Linux med antingen den avbrotts bara Enterprise-kernel (UEK) eller Red Hat-kompatibla kernel.
* Oracle-UEK2 stöds inte på Hyper-V och Azure eftersom den inte omfattar de nödvändiga driv rutinerna.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hård disk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten Convert-VHD.
* **Kernel-stöd för att montera UDF-filsystem krävs.** Vid första starten av Azure skickas etablerings konfigurationen till den virtuella Linux-datorn via UDF-formaterade medier som är kopplade till gästen. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner snarare än LVM (vanligt vis som standard för många installationer). På så sätt undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för fel sökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på data diskar om det är lämpligt.
* Tidigare versioner av Linux-kärnan än 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar främst äldre distributioner med den överordnade Red Hat 2.6.32-kärnan och har åtgärd ATS i Oracle Linux 6,6 och senare
* Konfigurera inte en swap-partition på OS-disken. Mer information om detta finns i stegen nedan.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information finns i [installations information för Linux](create-upload-generic.md#general-linux-installation-notes) .
* Kontrol lera att `Addons` lagrings platsen är aktive rad. Redigera filen `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) eller `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7) och ändra raden `enabled=0` till `enabled=1` under **[ol6_addons]** eller **[ol7_addons]** i den här filen.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 och senare
Du måste slutföra vissa konfigurations steg i operativ systemet för att den virtuella datorn ska kunna köras i Azure.

1. I mittenfönstret i Hyper-V Manager väljer du den virtuella datorn.
2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.
3. Avinstallera NetworkManager genom att köra följande kommando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Obs:** Om paketet inte redan är installerat kommer det här kommandot inte att fungera med ett fel meddelande. Detta är förväntat.
4. Skapa en fil med namnet **nätverk** i `/etc/sysconfig/` katalogen som innehåller följande text:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Skapa en fil med namnet **ifcfg-eth0** i `/etc/sysconfig/network-scripts/` katalogen som innehåller följande text:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Ändra udev-regler för att undvika att skapa statiska regler för Ethernet-gränssnitten. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Se till att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

    ```console
    # chkconfig network on
    ```

8. Installera python-pyasn1 genom att köra följande kommando:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna "/boot/grub/menu.lst" i en text redigerare och se till att kerneln innehåller följande parametrar:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Detta säkerställer att alla konsol meddelanden skickas till den första serie porten, vilket kan hjälpa Azure-support med fel söknings problem.
   
   Förutom ovanstående, rekommenderar vi att du *tar bort* följande parametrar:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten.
   
   `crashkernel`Alternativet kan vara rätt konfigurerat om det behövs, men Observera att den här parametern kommer att minska mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre VM-storlekarna.
10. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.
11. Installera Azure Linux-agenten genom att köra följande kommando. Den senaste versionen är 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Observera att om du installerar WALinuxAgent-paketet tas NetworkManager-och NetworkManager-GNOME-paketen bort, om de inte redan har tagits bort enligt beskrivningen i steg 2.
12. Skapa inte växlings utrymme på OS-disken.
    
    Azure Linux-agenten kan automatiskt konfigurera växlings utrymme med hjälp av den lokala resurs disk som är kopplad till den virtuella datorn efter etableringen på Azure. Observera att den lokala resurs disken är en *temporär* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg) ändrar du följande parametrar i/etc/waagent.conf på lämpligt sätt:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 och senare
**Ändringar i Oracle Linux 7**

Att förbereda en virtuell dator med Oracle Linux 7 för Azure liknar Oracle Linux 6, men det finns flera viktiga skillnader att notera:

* Azure har stöd för Oracle Linux med antingen den avbrotts bara Enterprise kernel (UEK) eller Red Hat-kompatibla kernel. Oracle Linux med UEK rekommenderas.
* NetworkManager-paketet är inte längre i konflikt med Azure Linux-agenten. Det här paketet installeras som standard och vi rekommenderar att det inte tas bort.
* GRUB2 används nu som standard Start programmet, så proceduren för att redigera kernel-parametrar har ändrats (se nedan).
* XFS är nu standard fil systemet. Fil systemet ext4 kan fortfarande användas om du vill.

**Konfigurationssteg**

1. Välj den virtuella datorn i Hyper-V Manager.
2. Klicka på **Anslut** för att öppna ett konsol fönster för den virtuella datorn.
3. Skapa en fil med namnet **nätverk** i `/etc/sysconfig/` katalogen som innehåller följande text:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Skapa en fil med namnet **ifcfg-eth0** i `/etc/sysconfig/network-scripts/` katalogen som innehåller följande text:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Ändra udev-regler för att undvika att skapa statiska regler för Ethernet-gränssnitten. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Se till att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

    ```console
    # sudo chkconfig network on
    ```

7. Installera python-pyasn1-paketet genom att köra följande kommando:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Kör följande kommando för att rensa aktuella yum-metadata och installera eventuella uppdateringar:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna "/etc/default/grub" i en text redigerare och redigera `GRUB_CMDLINE_LINUX` parametern, till exempel:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem. Det stänger också av namngivnings konventionerna för nätverkskort i Oracle Linux 7 med den hårda företags kärnan. Förutom ovanstående, rekommenderar vi att du *tar bort* följande parametrar:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten.
   
   `crashkernel`Alternativet kan vara rätt konfigurerat om det behövs, men Observera att den här parametern kommer att minska mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre VM-storlekarna.
10. När du är klar med att redigera "/etc/default/grub" per ovan kör du följande kommando för att återskapa grub-konfigurationen:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.
12. Installera Azure Linux-agenten och beroenden:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Installera Cloud-Init för att hantera etableringen

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. Växlings konfiguration skapar inte växlings utrymme på operativ system disken.

    Tidigare användes Azure Linux-agenten automatiskt för att konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har distribuerats på Azure. Detta hanteras dock inte av Cloud-init. du **får inte** använda Linux-agenten för att formatera resurs disken skapa växlings filen, ändra följande parametrar på `/etc/waagent.conf` lämpligt sätt:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Om du vill montera, formatera och skapa växling kan du antingen:
    * Skicka detta i som en Cloud-Init-konfiguration varje gång du skapar en virtuell dator
    * Använd ett bakade-direktiv för Cloud-Init i avbildningen som ska göras varje gång den virtuella datorn skapas:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```


15. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

16. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din Oracle Linux. VHD för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).
