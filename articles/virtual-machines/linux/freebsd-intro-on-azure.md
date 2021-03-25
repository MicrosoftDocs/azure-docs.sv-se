---
title: Introduktion till FreeBSD på Azure
description: Lär dig mer om att använda virtuella FreeBSD-datorer i Azure
author: thomas1206
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: da51fabaaa3c02137770f0b2d9a851b1f6702980
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044057"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introduktion till FreeBSD på Azure
Den här artikeln innehåller en översikt över att köra en virtuell FreeBSD-dator i Azure.

## <a name="overview"></a>Översikt
FreeBSD för Microsoft Azure är ett avancerat operativ system för datorer som används för att driva moderna servrar, skriv bord och inbäddade plattformar.

Microsoft Corporation gör avbildningar av FreeBSD tillgängliga i Azure med [Azures gästa Gent](https://github.com/Azure/WALinuxAgent/) förkonfigurerad. För närvarande erbjuds följande FreeBSD-versioner som avbildningar av Microsoft:

- FreeBSD 10,4 på Azure Marketplace
- FreeBSD 11,2 på Azure Marketplace
- FreeBSD 12,0 på Azure Marketplace

Agenten ansvarar för kommunikation mellan den virtuella datorn FreeBSD och Azure-infrastrukturen för åtgärder, till exempel etablering av den virtuella datorn vid första användningen (användar namn, lösen ord eller SSH-nyckel, värdnamn osv.) och aktivering av funktioner för selektiva VM-tillägg.

Som för framtida versioner av FreeBSD är strategin att vara aktuell och göra de senaste versionerna tillgängliga strax efter att de publicerats av FreeBSD release Engineering-teamet.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Skapa en virtuell FreeBSD-dator via Azure CLI på FreeBSD
Först måste du installera [Azure CLI](/cli/azure/get-started-with-azure-cli) genom att följa kommando på en FreeBSD dator.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Om bash inte är installerat på FreeBSD-datorn kör du följande kommando före installationen. 

```bash
sudo pkg install bash
```

Om python inte är installerat på FreeBSD-datorn kör du följande kommandon före installationen. 

```bash
sudo pkg install python38
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.8 /usr/local/bin/python
```

Under installationen uppmanas du att `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` . Om du svarar `y` och anger `/etc/rc.conf` som kan `a path to an rc file to update` du möta problemet `ERROR: [Errno 13] Permission denied` . För att lösa det här problemet bör du ge den aktuella användaren Skriv behörighet till den aktuella användaren `etc/rc.conf` .

Nu kan du logga in på Azure och skapa din virtuella FreeBSD-dator. Nedan visas ett exempel på hur du skapar en virtuell FreeBSD 11,0-dator. Du kan också lägga till parametern `--public-ip-address-dns-name` med ett globalt unikt DNS-namn för en nyligen skapad offentlig IP-adress. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Sedan kan du logga in på din virtuella FreeBSD-dator via IP-adressen som skrevs i resultatet ovan. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>VM-tillägg för FreeBSD
Följande virtuella dator tillägg stöds i FreeBSD.

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) -tillägget kan:

* Återställ lösen ordet för den ursprungliga sudo-användaren.
* Skapa en ny sudo-användare med det angivna lösen ordet.
* Ange den offentliga värd nyckeln med den angivna nyckeln.
* Återställ den offentliga värd nyckeln som tillhandahölls under etablering av virtuell dator om värd nyckeln inte har angetts.
* Öppna SSH-porten (22) och Återställ sshd_config om reset_ssh har värdet sant.
* Ta bort den befintliga användaren.
* Kontrol lera diskar.
* Reparera en disk som lagts till.

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) -tillägget kan:

* Om det här alternativet har angetts laddar du ned de anpassade skripten från Azure Storage eller extern offentlig lagring (till exempel GitHub).
* Kör Start punkts skriptet.
* Stöd för infogade kommandon.
* Konvertera Windows-formatmallens rad matning i Shell-och Python-skript automatiskt.
* Ta bort BOM i Shell och Python-skript automatiskt.
* Skydda känsliga data i CommandToExecute.

> [!NOTE]
> FreeBSD VM stöder bara CustomScript version 1. x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autentisering: användar namn, lösen ord och SSH-nycklar
När du skapar en virtuell FreeBSD-dator med hjälp av Azure Portal måste du ange ett användar namn, lösen ord eller en offentlig SSH-nyckel.
Användar namn för att distribuera en virtuell FreeBSD-dator på Azure får inte matcha namn på system konton (UID <100) som redan finns på den virtuella datorn ("rot", t. ex.).
För närvarande stöds endast RSA SSH-nyckeln. En SSH-nyckel för flera rader måste börja med `---- BEGIN SSH2 PUBLIC KEY ----` och sluta med `---- END SSH2 PUBLIC KEY ----` .

## <a name="obtaining-superuser-privileges"></a>Hämta behörigheter för superanvändare
Det användar konto som anges under distributionen av virtuella dator instanser på Azure är ett konto med privilegier. Paketet med sudo installerades i den publicerade FreeBSD-avbildningen.
När du har loggat in via det här användar kontot kan du köra kommandon som rot med hjälp av kommandosyntaxen.

```
$ sudo <COMMAND>
```

Du kan också hämta ett rot gränssnitt genom att använda `sudo -s` .

## <a name="known-issues"></a>Kända problem
[Azure VM-gästens agent](https://github.com/Azure/WALinuxAgent/) version 2.2.2 har ett [känt problem](https://github.com/Azure/WALinuxAgent/pull/517) som orsakar etablerings fel för FreeBSD VM på Azure. Korrigeringen samlades in av [Azure VM-gäst Gent](https://github.com/Azure/WALinuxAgent/) version 2.2.3 och senare versioner. 

## <a name="next-steps"></a>Nästa steg
* Gå till [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/thefreebsdfoundation.freebsd-12_2?tab=Overview) för att skapa en virtuell FreeBSD-dator.
