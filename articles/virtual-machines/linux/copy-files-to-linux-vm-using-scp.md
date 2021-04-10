---
title: Flytta filer till och från virtuella Azure Linux-datorer med SCP
description: Flytta filer säkert till och från en virtuell Linux-dator i Azure med hjälp av SCP och ett SSH-nyckelpar.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 83b57055ee7a3fedab014abeab96520c3877b843
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558448"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Flytta filer till och från en virtuell Linux-dator med SCP

Den här artikeln visar hur du flyttar filer från din arbets station till en virtuell Azure Linux-dator eller från en virtuell Azure Linux-dator till din arbets station med hjälp av en säker kopia (SCP). Det är enkelt och säkert att flytta filer mellan din arbets Station och en virtuell Linux-dator, och det är viktigt att du hanterar din Azure-infrastruktur. 

I den här artikeln behöver du en virtuell Linux-dator som distribuerats i Azure med [offentliga SSH-och privata nyckelfiler](mac-create-ssh-keys.md). Du behöver också en SCP-klient för den lokala datorn. Den är byggd ovanpå SSH och ingår i standard bash-gränssnittet för de flesta Linux-och Mac-datorer och vissa Windows-gränssnitt.

## <a name="quick-commands"></a>Snabbkommandon

Kopiera en fil upp till den virtuella Linux-datorn

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopiera en fil ned från den virtuella Linux-datorn

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

Som exempel flyttar vi en Azure-konfigurationsfil upp till en virtuell Linux-dator och hämtar en logg fils katalog, både med SCP och SSH-nycklar.   

## <a name="ssh-key-pair-authentication"></a>Autentisering av SSH-nyckelpar

SCP använder SSH för transport lagret. SSH hanterar autentiseringen på mål värden och flyttar filen i en krypterad tunnel som tillhandahålls som standard med SSH. För SSH-autentisering kan du använda användar namn och lösen ord. Autentisering av offentliga och privata SSH-nycklar rekommenderas dock som en säkerhets rutin. När SSH har autentiserat anslutningen börjar SCP att kopiera filen. Med en korrekt konfigurerad `~/.ssh/config` och offentliga och privata offentliga och privata nycklar kan SCP-anslutningen upprättas genom att du bara använder ett server namn (eller en IP-adress). Om du bara har en SSH-nyckel söker SCP efter den i `~/.ssh/` katalogen och använder den som standard för att logga in på den virtuella datorn.

Mer information om hur du konfigurerar dina `~/.ssh/config` offentliga och privata nycklar och privata nycklar finns i [skapa SSH-nycklar](mac-create-ssh-keys.md).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP a-fil till en virtuell Linux-dator

I det första exemplet kopierar vi en Azure-konfigurationsfil upp till en virtuell Linux-dator som används för att distribuera Automation. Eftersom den här filen innehåller autentiseringsuppgifter för Azure API, som innehåller hemligheter, är säkerheten viktigt. Den krypterade tunneln som tillhandahålls av SSH skyddar filens innehåll.

Följande kommando kopierar den lokala *Azure/config-* filen till en virtuell Azure-dator med FQDN- *myserver.eastus.cloudapp.Azure.com*. Administratörs användar namnet på den virtuella Azure-datorn är *azureuser*. Filen är riktad till katalogen */Home/azureuser/* . Ersätt dina egna värden i det här kommandot.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP en katalog från en virtuell Linux-dator

I det här exemplet kopierar vi en katalog med loggfiler från den virtuella Linux-datorn till din arbets Station. En loggfil kan innehålla känsliga eller hemliga data. Att använda SCP säkerställer dock att innehållet i loggfilerna är krypterade. Att använda SCP för att överföra filerna är det enklaste sättet att hämta logg katalogen och filer till din arbets Station samtidigt som de är säkra.

Följande kommando kopierar filer i katalogen */Home/azureuser/logs/* på den virtuella Azure-datorn till den lokala katalogen/tmp-katalogen:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r`Flaggan instruerar SCP att rekursivt kopiera filer och kataloger från den plats i katalogen som anges i kommandot.  Observera också att kommando rads koden liknar kommandot `cp` Kopiera.

## <a name="next-steps"></a>Nästa steg

* [Hantera användare, SSH och kontrol lera eller reparera diskar på virtuella Azure Linux-datorer med VMAccess-tillägget](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
