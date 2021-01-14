---
title: Skapa och ladda upp en SUSE Linux-hårddisk i Azure
description: Lär dig att skapa och ladda upp en virtuell Azure-hård disk (VHD) som innehåller ett SUSE Linux-operativsystem.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 3d6a981db93cd84f0dbe5ab229ba1e90ee0bd1e7
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200744"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Förbereda en virtuell SLES- eller openSUSE-dator för Azure


Den här artikeln förutsätter att du redan har installerat ett SUSEt eller openSUSE Linux-operativsystem till en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer, till exempel en virtualiseringslösning som Hyper-V. Anvisningar finns i [Installera Hyper-V-rollen och konfigurera en virtuell dator](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>Installations information för SLES/openSUSE
* Se även [allmänna Linux-Installationsinstruktioner](create-upload-generic.md#general-linux-installation-notes) för mer information om hur du förbereder Linux för Azure.
* VHDX-formatet stöds inte i Azure, endast **fast virtuell hård disk**.  Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdleten Convert-VHD.
* När du installerar Linux-systemet rekommenderar vi att du använder standardpartitioner snarare än LVM (vanligt vis som standard för många installationer). På så sätt undviker du LVM namn konflikter med klonade virtuella datorer, särskilt om en OS-disk någonsin måste kopplas till en annan virtuell dator för fel sökning. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) eller [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) kan användas på data diskar om det är lämpligt.
* Konfigurera inte en swap-partition på OS-disken. Linux-agenten kan konfigureras för att skapa en växlings fil på den tillfälliga resurs disken.  Mer information om detta finns i stegen nedan.
* Alla virtuella hård diskar på Azure måste ha en virtuell storlek som är justerad till 1 MB. När du konverterar från en RAW-disk till VHD måste du se till att den råa disk storleken är en multipel av 1 MB före konverteringen. Mer information finns i [installations information för Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>Använda SUSE Studio
[SUSE Studio](https://studioexpress.opensuse.org/) kan enkelt skapa och hantera dina SLES-och openSUSE-avbildningar för Azure och Hyper-V. Detta är den rekommenderade metoden för att anpassa dina egna SLES-och openSUSE-avbildningar.

Som ett alternativ till att skapa en egen virtuell hård disk publicerar SUSE också BYOS-avbildningar (ta med din egen prenumeration) för SLES i [VM-depå](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Förbered SUSE Linux Enterprise Server för Azure
1. I mittenfönstret i Hyper-V Manager väljer du den virtuella datorn.
2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.
3. Registrera ditt SUSE Linux Enterprise-system så att det kan ladda ned uppdateringar och installera paket.
4. Uppdatera systemet med de senaste korrigeringarna:

    ```console
    # sudo zypper update
    ```
    
5. Installera Azure Linux-agenten och Cloud-Init

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Aktivera waagent & Cloud-Init för att starta vid start

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Uppdatera waagent och Cloud-Init-konfiguration

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Redigera/etc/default/grub-filen för att se till att konsol loggar skickas till en seriell port och uppdatera sedan huvud konfigurations filen med grub2-mkconfig-o/Boot/grub2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Detta säkerställer att alla konsol meddelanden skickas till den första serie porten, vilket kan hjälpa Azure-support med fel söknings problem.
    
9. Se till att/etc/fstab-filen refererar till disken med dess UUID (med-UUID)
         
10. Ändra udev-regler för att undvika att skapa statiska regler för Ethernet-gränssnitten. Dessa regler kan orsaka problem när du klonar en virtuell dator i Microsoft Azure eller Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. Vi rekommenderar att du redigerar filen "/etc/sysconfig/Network/DHCP" och ändrar `DHCLIENT_SET_HOSTNAME` parametern till följande:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. I "/etc/sudoers", kommentera ut eller ta bort följande rader om de finns:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Se till att SSH-servern är installerad och konfigurerad för start vid start. Detta är vanligt vis standardvärdet.

14. Växlings konfiguration
 
    Skapa inte växlings utrymme på operativ system disken.

    Tidigare användes Azure Linux-agenten automatiskt för att konfigurera växlings utrymmet genom att använda den lokala resurs disk som är kopplad till den virtuella datorn efter att den virtuella datorn har distribuerats på Azure. Detta hanteras dock inte av Cloud-init. du **får inte** använda Linux-agenten för att formatera resurs disken skapa växlings filen, ändra följande parametrar på `/etc/waagent.conf` lämpligt sätt:

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Om du vill montera, formatera och skapa växling kan du antingen:
    * Skicka detta i som en Cloud-Init-konfiguration varje gång du skapar en virtuell dator.
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
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

---
## <a name="prepare-opensuse-131"></a>Förbered openSUSE 13.1 +
1. I mittenfönstret i Hyper-V Manager väljer du den virtuella datorn.
2. Klicka på **Anslut** för att öppna fönstret för den virtuella datorn.
3. Kör kommandot på gränssnittet `zypper lr` . Om det här kommandot returnerar utdata som liknar följande, konfigureras databaserna som förväntat – inga justeringar krävs (Observera att versions numren kan variera):

   | # | Alias                 | Name                  | Enabled | Uppdatera
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Moln: Tools_13.1      | Moln: Tools_13.1      | Ja     | Ja
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | Ja     | Ja
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | Ja     | Ja

    Om kommandot returnerar "inga databaser har definierats..." Använd sedan följande kommandon för att lägga till dessa databaser:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Du kan sedan kontrol lera att databaserna har lagts till genom att köra kommandot `zypper lr` igen. Om en av relevanta uppdaterings databaser inte är aktive rad aktiverar du den med följande kommando:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Uppdatera kerneln till den senaste tillgängliga versionen:

    ```console
    # sudo zypper up kernel-default
    ```

    Eller för att uppdatera systemet med alla de senaste korrigeringarna:

    ```console
    # sudo zypper update
    ```

5. Installera Azure Linux-agenten.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Ändra start raden för kernel i grub-konfigurationen för att inkludera ytterligare kernel-parametrar för Azure. Det gör du genom att öppna "/boot/grub/menu.lst" i en text redigerare och se till att standard kärnan innehåller följande parametrar:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Detta säkerställer att alla konsol meddelanden skickas till den första serie porten, vilket kan hjälpa Azure-support med fel söknings problem. Ta dessutom bort följande parametrar från kernelns start rad om de finns:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Vi rekommenderar att du redigerar filen "/etc/sysconfig/Network/DHCP" och ändrar `DHCLIENT_SET_HOSTNAME` parametern till följande:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Viktigt:** I "/etc/sudoers", kommentera ut eller ta bort följande rader om de finns:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Se till att SSH-servern är installerad och konfigurerad för start vid start.  Detta är vanligt vis standardvärdet.
10. Skapa inte växlings utrymme på OS-disken.

    Azure Linux-agenten kan automatiskt konfigurera växlings utrymme med hjälp av den lokala resurs disk som är kopplad till den virtuella datorn efter etableringen på Azure. Observera att den lokala resurs disken är en *temporär* disk och kan tömmas när den virtuella datorn avetableras. När du har installerat Azure Linux-agenten (se föregående steg) ändrar du följande parametrar i/etc/waagent.conf på lämpligt sätt:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Kör följande kommandon för att avetablera den virtuella datorn och förbereda den för etablering på Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Se till att Azure Linux-agenten körs vid start:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Klicka på **åtgärd-> stänga av** i Hyper-V Manager. Din Linux-VHD är nu redo att laddas upp till Azure.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att använda din virtuella SUSE Linux-hårddisk för att skapa nya virtuella datorer i Azure. Om det är första gången du laddar upp VHD-filen till Azure, se [skapa en virtuell Linux-dator från en anpassad disk](upload-vhd.md#option-1-upload-a-vhd).