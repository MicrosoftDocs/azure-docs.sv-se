---
title: Använd Cloud-Init för att konfigurera en swap-partition på en virtuell Linux-dator
description: Använda Cloud-Init för att konfigurera en swap-partition i en virtuell Linux-dator när den skapas med Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: b9f4adc4e1e980db2af4fcc20b3a4492309c89f3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559383"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Använd Cloud-Init för att konfigurera en swap-partition på en virtuell Linux-dator
Den här artikeln visar hur du använder [Cloud-Init](https://cloudinit.readthedocs.io) för att konfigurera växlings partition på olika Linux-distributioner. Byt partition konfigurerades traditionellt av Linux-agenten (WALA) baserat på vilka distributioner som krävs.  Det här dokumentet beskriver processen för att bygga swap-partitionen på begäran under etablerings tiden med Cloud-init.  Mer information om hur Cloud-Init fungerar internt i Azure och vilka Linux-distributioner som stöds finns i [Översikt över Cloud-Init](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Skapa swap-partition för Ubuntu-baserade avbildningar
Som standard i Azure skapar Ubuntu Gallery-avbildningar inte växlingsfiler-partitioner. Om du vill aktivera konfiguration av växlings partition under etablerings tiden för virtuella datorer med Cloud-Init, se [AzureSwapPartitions-dokumentet](https://wiki.ubuntu.com/AzureSwapPartitions) på Ubuntu-wikin.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Skapa swap-partition för Red Hat-och CentOS-baserade bilder

Skapa en fil i det aktuella gränssnittet med namnet *cloud_init_swappart.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_swappart.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 om du vill använda **nano** -redigeraren. Kontrol lera att hela Cloud-Init-filen har kopierats korrekt, särskilt den första raden.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Innan du distribuerar den här avbildningen måste du skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [AZ VM Create](/cli/azure/vm) och anger Cloud-Init-filen med följande `--custom-data cloud_init_swappart.txt` :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verifiera att växlings partition skapades
SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

När du har SSH'ed till den virtuella datorn kontrollerar du om Byt partition har skapats

```bash
swapon -s
```

Utdata från det här kommandot bör se ut så här:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Om du har en befintlig Azure-avbildning som har en konfigurerad swap-partition och du vill ändra konfigurationen för växlings partition för nya avbildningar bör du ta bort den befintliga växlings partitionen. Mer information finns i avsnittet Anpassa avbildningar för att etablera efter Cloud-Init-dokument.

## <a name="next-steps"></a>Nästa steg
Ytterligare Cloud-Init-exempel på konfigurations ändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
