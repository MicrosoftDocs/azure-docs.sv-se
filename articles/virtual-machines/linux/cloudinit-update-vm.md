---
title: Använda Cloud-Init i en virtuell Linux-dator på Azure
description: Så här använder du Cloud-Init för att uppdatera och installera paket i en virtuell Linux-dator när du skapar med Azure CLI
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: bbd3c30cb00dae25afeea356cefb86a9c860cde5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559349"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Använd Cloud-Init för att uppdatera och installera paket i en virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [Cloud-Init](https://cloudinit.readthedocs.io) för att uppdatera paket på en virtuell Linux-dator (VM) eller skalnings uppsättningar för virtuella datorer vid etablerings tiden i Azure. Dessa Cloud-Init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur Cloud-Init fungerar internt i Azure och vilka Linux-distributioner som stöds finns i [Översikt över Cloud-Init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Uppdatera en virtuell dator med Cloud-Init
Av säkerhets synpunkt kanske du vill konfigurera en virtuell dator för att tillämpa de senaste uppdateringarna vid första starten. Eftersom Cloud-Init fungerar över olika Linux-distributioner behöver du inte ange `apt` eller `yum` för paket hanteraren. I stället definierar du `package_upgrade` och låter Cloud-Init-processen fastställa lämplig mekanism för den distribution som används. Med det här arbets flödet kan du använda samma Cloud-Init-skript i distributioner.

Om du vill se uppgraderings processen skapar du en fil i det aktuella gränssnittet med namnet *cloud_init_upgrade.txt* och klistrar in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_upgrade.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 om du vill använda **nano** -redigeraren. Kontrol lera att hela Cloud-Init-filen har kopierats korrekt, särskilt den första raden.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Innan du distribuerar den här avbildningen måste du skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [AZ VM Create](/cli/azure/vm) och anger Cloud-Init-filen med följande `--custom-data cloud_init_upgrade.txt` :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

Kör paket hanterings verktyget och Sök efter uppdateringar.

```bash
sudo yum update
```

När Cloud-Init har kontroller ATS för och installerade uppdateringar vid start bör inga ytterligare uppdateringar tillämpas.  Du ser uppdaterings processen, antalet ändrade paket och installationen av `httpd` genom att köra `yum history` och granska utdata som liknar dem nedan.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Nästa steg
Ytterligare Cloud-Init-exempel på konfigurations ändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
