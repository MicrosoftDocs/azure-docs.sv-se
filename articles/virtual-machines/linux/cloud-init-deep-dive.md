---
title: Förstå Cloud-Init
description: Djupgående för att förstå etablering av en virtuell Azure-dator med Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 94dd57310375febb4bc9a55efa704a5fbf4e80e8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559451"
---
# <a name="diving-deeper-into-cloud-init"></a>Simhopp djupare in i Cloud-Init
Om du vill veta mer om [Cloud-Init](https://cloudinit.readthedocs.io/en/latest/index.html) eller felsöka det på en djupare nivå måste du förstå hur det fungerar. I det här dokumentet beskrivs viktiga delar och det beskriver Azure-information.

När Cloud-Init ingår i en generaliserad avbildning och en virtuell dator skapas från avbildningen kommer den att bearbeta konfigurationer och köra genom 5 steg under den första starten. De här faserna, så som det visar dig vid vilken punkt Cloud-Init, kommer att tillämpa konfigurationer. 


## <a name="understand-cloud-init-configuration"></a>Förstå Cloud-Init konfiguration
Konfigurera en virtuell dator som ska köras på en plattform, innebär att Cloud-Init måste tillämpa flera konfigurationer, som en avbildnings konsument, de viktigaste konfigurationerna som du kommer att interagera med är `User data` (customData), som har stöd för flera format, dokumenteras [här](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats). Du kan också lägga till och köra skript (/var/lib/Cloud/scripts) för ytterligare konfiguration. nedan beskrivs mer information.

Vissa konfigurationer är redan bakade i Azure Marketplace-avbildningar som medföljer Cloud-Init, till exempel:

1. **Moln data källa** – Cloud-Init innehåller kod som kan interagera med moln plattformar, dessa kallas för "DataSources". När en virtuell dator skapas från en Cloud-Init-avbildning i [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure)läser Cloud-Init in Azure-datakällan, som kommer att interagera med Azure metadata-slutpunkter för att hämta den VM-speciella konfigurationen.
2. **Körnings konfiguration** (/Run/Cloud-Init)
3. **Avbildnings konfiguration** (/etc/Cloud), `/etc/cloud/cloud.cfg` t `/etc/cloud/cloud.cfg.d/*.cfg` . ex.. Ett exempel på var det används i Azure är det vanligt för Linux OS-avbildningar med Cloud-Init att ha ett Azure DataSource-direktiv, som talar om för Cloud-Init vilken data källa den ska använda, så att moln initierings tiden sparas:

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>Start steg för Cloud-Init (bearbetnings konfiguration)

Vid etablering med Cloud-Init finns det fem stadier av Start, som bearbetar konfigurationen och visas i loggarna.

1. [Generator steg](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator): den moln-init-systemgenererade generatorn startar och avgör att Cloud-Init ska ingå i Start målen, och i så fall aktiverar Cloud-init. 

2. [Cloud-Init Local Stage](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local): här kommer Cloud-Init att leta efter den lokala "Azure"-DataSource, som aktiverar Cloud-Init till gränssnitt med Azure och tillämpar en nätverks konfiguration, inklusive reserv.

3. [Initierings fas för Cloud-Init (nätverk)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network): nätverk ska vara online och information om NIC-och routningstabell-tabellen ska genereras. I det här skedet kommer modulerna som anges i `cloud_init_modules` i/etc/Cloud/Cloud.cfg att köras. Den virtuella datorn i Azure kommer att monteras, den tillfälliga disken formateras, värd namnet anges tillsammans med andra uppgifter.

   Detta är några av `cloud_init_modules` följande:
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   Efter det här steget kommer Cloud-Init att signalera till den Azure-plattform som den virtuella datorn har etablerat. Vissa moduler kan ha misslyckats, inte alla misslyckade moduler leder till ett etablerings fel.

4. [Konfigurations steg för Cloud-Init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config): i det här skedet kommer modulerna i `cloud_config_modules` definierade och som anges i/etc/Cloud/Cloud.cfg att köras.


5. [Slut skede för Cloud-Init](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final): i det här slutliga skedet kommer modulerna i `cloud_final_modules` , som anges i/etc/Cloud/Cloud.cfg, att köras. Här är moduler som måste köras sent i Start processen, till exempel paket installationer och körning av skript osv. 

   -   Under det här steget kan du köra skript genom att placera dem i katalogerna under `/var/lib/cloud/scripts` :
   - `per-boot` – skript i den här katalogen, körs vid varje omstart
   - `per-instance` – skript i den här katalogen körs när en ny instans startas om första gången
   - `per-once` – skript i den här katalogen körs bara en gång

## <a name="next-steps"></a>Nästa steg

[Felsöka Cloud-Init](cloud-init-troubleshooting.md).
