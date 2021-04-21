---
title: Använda SCP för att flytta filer till och från en virtuell dator
description: Flytta filer på ett säkert sätt till och från en virtuell Linux-dator i Azure med hjälp av SCP och ett SSH-nyckelpar.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/20/2021
ms.author: cynthn
ms.subservice: ''
ms.openlocfilehash: edfc44f79cff25486fde6326ac954fe5b575d846
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816448"
---
# <a name="use-scp-to-move-files-to-and-from-a-linux-vm"></a>Använda SCP för att flytta filer till och från en virtuell Linux-dator 

Den här artikeln visar hur du flyttar filer från din arbetsstation upp till en virtuell Linux-dator i Azure eller från en virtuell Linux-dator till din arbetsstation med hjälp av SCP (Secure Copy). Det är viktigt att flytta filer mellan din arbetsstation och en virtuell Linux-dator snabbt och säkert för att hantera din Azure-infrastruktur. 

För den här artikeln behöver du en virtuell Linux-dator som distribueras i Azure med [hjälp av offentliga och privata SSH-nyckelfiler.](mac-create-ssh-keys.md) Du behöver också en SCP-klient för den lokala datorn. Den bygger på SSH och ingår i Bash-standardgränssnittet på de flesta Linux- och Mac-datorer och PowerShell.


## <a name="quick-commands"></a>Snabbkommandon

Kopiera en fil upp till den virtuella Linux-datorn

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopiera en fil från den virtuella Linux-datorn

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

Som exempel flyttar vi en Azure-konfigurationsfil upp till en virtuell Linux-dator och hämtar en loggfilskatalog, både med hjälp av SCP- och SSH-nycklar.   

## <a name="ssh-key-pair-authentication"></a>Autentisering med SSH-nyckelpar

SCP använder SSH för transportskiktet. SSH hanterar autentiseringen på målvärden och flyttar filen i en krypterad tunnel som tillhandahålls som standard med SSH. För SSH-autentisering kan användarnamn och lösenord användas. Offentlig och privat SSH-nyckelautentisering rekommenderas dock som bästa säkerhetsmetod. När SSH har autentiserat anslutningen börjar SCP att kopiera filen. Med en korrekt konfigurerad och offentliga och privata SSH-nycklar kan SCP-anslutningen upprättas med ett `~/.ssh/config` servernamn (eller en IP-adress). Om du bara har en SSH-nyckel letar SCP efter den i katalogen och använder den som `~/.ssh/` standard för att logga in på den virtuella datorn.

Mer information om hur du konfigurerar `~/.ssh/config` offentliga och privata SSH-nycklar finns i [Skapa SSH-nycklar.](mac-create-ssh-keys.md)

## <a name="scp-a-file-to-a-linux-vm"></a>SCP en fil till en virtuell Linux-dator

I det första exemplet kopierar vi en Azure-konfigurationsfil till en virtuell Linux-dator som används för att distribuera automatisering. Eftersom den här filen innehåller autentiseringsuppgifter för Azure API, som innehåller hemligheter, är säkerheten viktig. Den krypterade tunneln som tillhandahålls av SSH skyddar innehållet i filen.

Följande kommando kopierar den lokala *.azure/config-filen* till en virtuell Azure-dator med FQDN *myserver.eastus.cloudapp.azure.com*. Om du inte har ett [FQDN inställt](../create-fqdn.md)kan du också använda IP-adressen för den virtuella datorn. Administratörsanvändarnamnet på den virtuella Azure-datorn *är azureuser*. Filen är riktad till katalogen */home/azureuser/.* Ersätt dina egna värden i det här kommandot.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP en katalog från en virtuell Linux-dator

I det här exemplet kopierar vi en katalog med loggfiler från den virtuella Linux-datorn till din arbetsstation. En loggfil kan innehålla känsliga eller hemliga data. Men genom att använda SCP ser du till att innehållet i loggfilerna krypteras. Att använda SCP för att överföra filerna är det enklaste sättet att hämta loggkatalogen och filerna till din arbetsstation samtidigt som det är säkert.

Följande kommando kopierar filer i katalogen */home/azureuser/logs/* på den virtuella Azure-datorn till den lokala /tmp-katalogen:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Flaggan `-r` instruerar SCP att rekursivt kopiera filerna och katalogerna från den punkt i katalogen som anges i kommandot.  Observera också att kommandoradssyntaxen liknar ett `cp` kopieringskommando.

## <a name="next-steps"></a>Nästa steg

* [Hantera användare, SSH och kontrollera eller reparera diskar på virtuella Linux-datorer i Azure med VMAccess-tillägget](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
