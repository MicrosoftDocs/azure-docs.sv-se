---
title: Använd Cloud-Init för att köra ett bash-skript i en virtuell Linux-dator på Azure
description: Använda Cloud-Init för att köra ett bash-skript i en virtuell Linux-dator när den skapas med Azure CLI
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1dabd7db85d4222ca2e9f8387b716af291ee6a69
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559434"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Använd Cloud-Init för att köra ett bash-skript i en virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [Cloud-Init](https://cloudinit.readthedocs.io) för att köra ett befintligt bash-skript på en virtuell Linux-dator (VM) eller Virtual Machine Scale set (VMSS) vid etablerings tiden i Azure. Dessa Cloud-Init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur Cloud-Init fungerar internt i Azure och vilka Linux-distributioner som stöds finns i [Översikt över Cloud-Init](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Kör ett bash-skript med Cloud-Init
Med Cloud-init behöver du inte konvertera dina befintliga skript till en molnbaserad-config. Cloud-Init accepterar flera indatatyper, varav ett är ett bash-skript.

Om du har använt det anpassade Linux-skriptet Azure-tillägget för att köra skripten kan du migrera dem så att de använder Cloud-init. Azure-tillägg har dock integrerat rapporter för att varna till skript fel, en distribution av moln-init-avbildningen Miss lyckas inte om skriptet Miss lyckas.

Om du vill se hur den här funktionen fungerar skapar du ett enkelt bash-skript för testning. Precis som Cloud-Init- `#cloud-config` filen måste det här skriptet vara lokalt där du kommer att köra AzureCLI-kommandona för att etablera den virtuella datorn.  I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor simple_bash.sh` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 om du vill använda **nano** -redigeraren. Kontrol lera att hela Cloud-Init-filen har kopierats korrekt, särskilt den första raden.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Innan du distribuerar den här avbildningen måste du skapa en resurs grupp med kommandot [AZ Group Create](/cli/azure/group) . En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu ska du skapa en virtuell dator med [AZ VM Create](/cli/azure/vm) och ange bash-skript filen med följande `--custom-data simple_bash.sh` :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verifiera att bash-skriptet har körts
SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

Ändra till **katalogen/tmp** -katalogen och kontrol lera att myScript.txt filen finns och att den innehåller rätt text inuti den.  Om den inte gör det kan du kontrol lera **/var/log/Cloud-init.log** för mer information.  Sök efter följande post:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Nästa steg
Ytterligare Cloud-Init-exempel på konfigurations ändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Köra en paket hanterare för att uppdatera befintliga paket vid första starten](cloudinit-update-vm.md)
- [Ändra lokalt värdnamn för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
