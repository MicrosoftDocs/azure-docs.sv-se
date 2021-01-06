---
title: Skapa och ladda upp en CentOS Linux-baserad virtuell dator
description: Lär dig att skapa och överföra en virtuell Azure-hårddisk (VHD) som innehåller ett CentOS-baserat Linux-operativsystem.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 74883e04170165c66fa389051a94cf349c3395cf
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916413"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Förbereda en CentOS-baserad virtuell dator för Azure

Lär dig att skapa och överföra en virtuell Azure-hårddisk (VHD) som innehåller ett CentOS-baserat Linux-operativsystem.

* [Förbered en virtuell CentOS 6. x-dator för Azure](#centos-6x)
* [Förbered en CentOS 7.0 + virtuell dator för Azure](#centos-70)


## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har installerat ett Linux-operativsystem med CentOS (eller liknande derivat) till en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Anvisningar finns i [Installera Hyper-V-rollen och konfigurera en virtuell dator](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Installations information för CentOS**

* Se även [allmänna Linux-Installationsinstruktioner](create-upload-generic.md#general-linux-installation-notes) för mer information om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hård disk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten Convert-VHD. Om du använder VirtualBox innebär det att du väljer **fast storlek** i stället för standardvärdet som tilldelas dynamiskt när disken skapas.
* När du installerar Linux-systemet *rekommenderar* vi att du använder standardpartitioner snarare än LVM (vanligt vis som standard för många installationer). På så sätt undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan identisk virtuell dator för fel sökning. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kan användas på data diskar.
* **Kernel-stöd för att montera UDF-filsystem krävs.** Vid första starten av Azure skickas etablerings konfigurationen till den virtuella Linux-datorn via UDF-formaterade medier som är kopplade till gästen. Azure Linux-agenten måste kunna montera UDF-filsystemet för att läsa konfigurationen och etablera den virtuella datorn.
* Linux kernel-versioner nedan 2.6.37 stöder inte NUMA på Hyper-V med större VM-storlekar. Det här problemet påverkar främst äldre distributioner med den överordnade Red Hat 2.6.32-kärnan och har åtgärd ATS i RHEL 6,6 (kernel-2.6.32-504). System som kör anpassade kernels som är äldre än 2.6.37, eller RHEL-baserade kernels som är äldre än 2.6.32-504, måste ange start parametern `numa=off` på kernel-kommandoraden i grub. conf. Mer information finns i Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Konfigurera inte en swap-partition på OS-disken. Mer information om detta finns i stegen nedan.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information finns i [installations information för Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="centos-6x"></a>CentOS 6. x

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Anslut** för att öppna ett konsol fönster för den virtuella datorn.

3. I CentOS 6 kan NetworkManager störa Azure Linux-agenten. Avinstallera det här paketet genom att köra följande kommando:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Skapa eller redigera filen `/etc/sysconfig/network` och Lägg till följande text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Skapa eller redigera filen `/etc/sysconfig/network-scripts/ifcfg-eth0` och Lägg till följande text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Ändra udev-regler för att undvika att skapa statiska regler för Ethernet-gränssnitten. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Se till att nätverks tjänsten kommer att starta vid start genom att köra följande kommando:

    ```bash
    sudo chkconfig network on
    ```

8. Om du vill använda de OpenLogic-speglar som finns i Azure-datacentren ersätter du `/etc/yum.repos.d/CentOS-Base.repo` filen med följande databaser.  Detta lägger också till **[OpenLogic]** -databasen som innehåller ytterligare paket som Azure Linux-agenten:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > Resten av den här guiden förutsätter att du använder minst `[openlogic]` lagrings platsen, som kommer att användas för att installera Azure Linux-agenten nedan.

9. Lägg till följande rad i/etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Kör följande kommando för att rensa aktuella yum-metadata och uppdatera systemet med de senaste paketen:

    ```bash
    yum clean all
    ```

    Om du inte skapar en avbildning för en äldre version av CentOS, rekommenderar vi att du uppdaterar alla paket till de senaste:

    ```bash
    sudo yum -y update
    ```

    Det kan krävas en omstart efter att det här kommandot har körts.

11. Valfritt Installera driv rutinerna för Linux Integration Services (LIS).

    > [!IMPORTANT]
    > Steget **krävs** för CentOS 6,3 och tidigare, och valfritt för senare versioner.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Alternativt kan du följa anvisningarna för manuell installation på sidan för att [Hämta](https://www.microsoft.com/download/details.aspx?id=55106) RPM till den virtuella datorn.

12. Installera Azure Linux-agenten och-beroenden. Starta och aktivera waagent-tjänsten:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    WALinuxAgent-paketet tar bort NetworkManager-och NetworkManager-GNOME-paketen om de inte redan har tagits bort enligt beskrivningen i steg 3.

13. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna `/boot/grub/menu.lst` i en text redigerare och se till att standard kärnan innehåller följande parametrar:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem.

    Förutom ovanstående, rekommenderar vi att du *tar bort* följande parametrar:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten.  `crashkernel`Alternativet kan vara rätt konfigurerat om det behövs, men Observera att den här parametern kommer att minska mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre VM-storlekarna.

    > [!Important]
    > CentOS 6,5 och tidigare måste också ange en kernel-parameter `numa=off` . Se Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.

15. Skapa inte växlings utrymme på OS-disken.

    Azure Linux-agenten kan automatiskt konfigurera växlings utrymme med hjälp av den lokala resurs disk som är kopplad till den virtuella datorn efter etableringen på Azure. Observera att den lokala resurs disken är en *temporär* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg) ändrar du följande parametrar på `/etc/waagent.conf` lämpligt sätt:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.



## <a name="centos-70"></a>CentOS 7.0 +

**Ändringar i CentOS 7 (och liknande derivat)**

Att förbereda en virtuell CentOS 7-dator för Azure liknar CentOS 6, men det finns flera viktiga skillnader att notera:

* NetworkManager-paketet är inte längre i konflikt med Azure Linux-agenten. Det här paketet installeras som standard och vi rekommenderar att det inte tas bort.
* GRUB2 används nu som standard Start programmet, så proceduren för att redigera kernel-parametrar har ändrats (se nedan).
* XFS är nu standard fil systemet. Fil systemet ext4 kan fortfarande användas om du vill.

**Konfigurations steg**

1. Välj den virtuella datorn i Hyper-V Manager.

2. Klicka på **Anslut** för att öppna ett konsol fönster för den virtuella datorn.

3. Skapa eller redigera filen `/etc/sysconfig/network` och Lägg till följande text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Skapa eller redigera filen `/etc/sysconfig/network-scripts/ifcfg-eth0` och Lägg till följande text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Ändra udev-regler för att undvika att skapa statiska regler för Ethernet-gränssnitten. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Om du vill använda de OpenLogic-speglar som finns i Azure-datacentren ersätter du `/etc/yum.repos.d/CentOS-Base.repo` filen med följande databaser.  Detta lägger också till **[OpenLogic]** -databasen som innehåller paket för Azure Linux-agenten:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > Resten av den här guiden förutsätter att du använder minst `[openlogic]` lagrings platsen, som kommer att användas för att installera Azure Linux-agenten nedan.

7. Kör följande kommando för att rensa aktuella yum-metadata och installera eventuella uppdateringar:

    ```bash
    sudo yum clean all
    ```

    Om du inte skapar en avbildning för en äldre version av CentOS, rekommenderar vi att du uppdaterar alla paket till de senaste:

    ```bash
    sudo yum -y update
    ```

    En omstart kanske krävs efter att det här kommandot har körts.

8. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna `/etc/default/grub` i en text redigerare och redigera `GRUB_CMDLINE_LINUX` parametern, till exempel:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Detta säkerställer också att alla konsol meddelanden skickas till den första seriella porten, vilket kan hjälpa Azure-support med fel söknings problem. Den stänger också av de nya CentOS 7-namngivnings konventionerna för nätverkskort. Förutom ovanstående, rekommenderar vi att du *tar bort* följande parametrar:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafisk och tyst start är inte användbart i en moln miljö där vi vill att alla loggar ska skickas till den seriella porten. `crashkernel`Alternativet kan vara rätt konfigurerat om det behövs, men Observera att den här parametern kommer att minska mängden tillgängligt minne på den virtuella datorn med 128 MB eller mer, vilket kan vara problematiskt på de mindre VM-storlekarna.

9. När du är klar med redigeringen av `/etc/default/grub` ovan kör du följande kommando för att återskapa grub-konfigurationen:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Om du skapar avbildningen från **VMware, VirtualBox eller kvm:** se till att Hyper-V-drivrutinerna ingår i initramfs:

    Redigera `/etc/dracut.conf` , Lägg till innehåll:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Återskapa initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Installera Azure Linux-agenten och beroenden för Azure VM-tillägg:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Installera Cloud-Init för att hantera etableringen

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


13. Växlings konfiguration skapar inte växlings utrymme på operativ system disken.

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

14. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

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

15. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att använda din virtuella CentOS Linux-hård disk för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).
