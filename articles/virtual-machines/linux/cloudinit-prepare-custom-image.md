---
title: Förbered Azure VM-avbildning för användning med Cloud-Init
description: Förbereda en befintlig Azure VM-avbildning för distribution med Cloud-Init
author: danis
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: 3aed65b0319f9a80c5ebc45428ff0c380c33fc3d
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883273"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Förbered en befintlig Linux Azure VM-avbildning för användning med Cloud-Init
Den här artikeln visar hur du tar en befintlig virtuell Azure-dator och förbereder den för distribution och redo att använda Cloud-init. Den resulterande avbildningen kan användas för att distribuera en ny virtuell dator eller skalnings uppsättningar för virtuella datorer – någon av dessa kan sedan anpassas ytterligare genom Cloud-Init vid distributions tillfället.  Dessa Cloud-Init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur Cloud-Init fungerar internt i Azure och vilka Linux-distributioner som stöds finns i [Översikt över Cloud-Init](using-cloud-init.md)

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du redan har en virtuell Azure-dator som kör en version av Linux-operativsystemet som stöds. Du har redan konfigurerat datorn så att den passar dina behov, installerat alla nödvändiga moduler, bearbetade alla nödvändiga uppdateringar och har testat den för att säkerställa att den uppfyller dina krav. 

## <a name="preparing-rhel-76--centos-76"></a>Förbereder RHEL 7,6/CentOS 7,6
Du måste använda SSH i din virtuella Linux-dator och köra följande kommandon för att kunna installera Cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install -y cloud-init 
```

Uppdatera `cloud_init_modules` avsnittet i `/etc/cloud/cloud.cfg` om du vill inkludera följande moduler:

```bash
- disk_setup
- mounts
```

Här är ett exempel på ett allmänt-syfte- `cloud_init_modules` avsnitt som ser ut.

```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```

Ett antal uppgifter som rör etablering och hantering av tillfälliga diskar måste uppdateras i `/etc/waagent.conf` . Kör följande kommandon för att uppdatera lämpliga inställningar.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Tillåt endast Azure som en data källa för Azure Linux-agenten genom att skapa en ny fil `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` med valfritt redigerings program med följande rad:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Om din befintliga Azure-avbildning har en växlings fil konfigurerad och du vill ändra växlings filens konfiguration för nya avbildningar med Cloud-Init måste du ta bort den befintliga växlings filen.

För Red Hat-baserade bilder – Följ instruktionerna i följande Red Hat-dokument som förklarar hur du [tar bort växlings filen](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

För CentOS-avbildningar med swapfile aktiverat kan du köra följande kommando för att inaktivera swapfile:

```bash
sudo swapoff /mnt/resource/swapfile
```

Se till att swapfile-referensen tas bort från `/etc/fstab` – den bör se ut ungefär så här:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Om du vill spara utrymme och ta bort växlings filen kan du köra följande kommando:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Extra steg för för beredda Cloud-Init-avbildning
> [!NOTE]
> Om avbildningen tidigare var en **moln-init-** förberedad och konfigurerad avbildning måste du utföra följande steg.

Följande tre kommandon används bara om den virtuella datorn som du anpassar till ska vara en ny specialiserad käll avbildning som tidigare har tillhandahållits av Cloud-init.  Du behöver inte köra dessa om avbildningen har kon figurer ATS med Azure Linux-agenten.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Slutför inställningen för Linux-agenten 
Alla Azure Platform-avbildningar har Azure Linux-agenten installerad, oavsett om den har kon figurer ATS av Cloud-Init eller inte.  Kör följande kommando för att slutföra avetableringen av användaren från Linux-datorn. 

```bash
sudo waagent -deprovision+user -force
```

Mer information om kommandon för att avetablera Azure Linux-agenten finns i [Azure Linux-agenten](../extensions/agent-linux.md) för mer information.

Avsluta SSH-sessionen och kör sedan följande AzureCLI-kommandon från bash-gränssnittet för att frigöra, generalisera och skapa en ny avbildning av en virtuell Azure-dator.  Ersätt `myResourceGroup` och `sourceVmName` med lämplig information som motsvarar din sourceVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Nästa steg
Ytterligare Cloud-Init-exempel på konfigurations ändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
