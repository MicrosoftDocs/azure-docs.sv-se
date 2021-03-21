---
title: Använd Cloud-Init för att ange värdnamn för en virtuell Linux-dator
description: Använda Cloud-Init för att anpassa en virtuell Linux-dator när den skapas med Azure CLI
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 5c3195fd338d17e2dae3ff2aadfb90790519715b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559400"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Använd Cloud-Init för att ange värdnamn för en virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [Cloud-Init](https://cloudinit.readthedocs.io) för att konfigurera ett särskilt värdnamn på en virtuell dator (VM) eller Virtual Machine Scale set (VMSS) vid etablerings tiden i Azure. Dessa Cloud-Init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur Cloud-Init fungerar internt i Azure och vilka Linux-distributioner som stöds finns i [Översikt över Cloud-Init](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Ange värd namnet med Cloud-Init
Som standard är värd namnet detsamma som namnet på den virtuella datorn när du skapar en ny virtuell dator i Azure.  Om du vill köra ett Cloud-Init-skript för att ändra det här standard värd namnet när du skapar en virtuell dator i Azure med [AZ VM Create](/cli/azure/vm), anger du Cloud-Init-filen med `--custom-data` växeln.  

Om du vill se uppgraderings processen skapar du en fil i det aktuella gränssnittet med namnet *cloud_init_hostname.txt* och klistrar in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_hostname.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 om du vill använda **nano** -redigeraren. Kontrol lera att hela Cloud-Init-filen har kopierats korrekt, särskilt den första raden.  

```yaml
#cloud-config
hostname: myhostname
```

Innan du distribuerar den här avbildningen måste du skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [AZ VM Create](/cli/azure/vm) och anger Cloud-Init-filen med följande `--custom-data cloud_init_hostname.txt` :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

När du har skapat Azure CLI visas information om den virtuella datorn. Använd `publicIpAddress` till SSH för den virtuella datorn. Ange din egen adress enligt följande:

```bash
ssh <publicIpAddress>
```

Om du vill se namnet på den virtuella datorn använder du `hostname` kommandot på följande sätt:

```bash
hostname
```

Den virtuella datorn bör rapportera värd namnet som det värdet som anges i filen Cloud-Init, som visas i följande exempel på utdata:

```bash
myhostname
```

## <a name="next-steps"></a>Nästa steg
Ytterligare Cloud-Init-exempel på konfigurations ändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
