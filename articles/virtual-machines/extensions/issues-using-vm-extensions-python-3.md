---
title: Problem med att använda VM-tillägg i python 3 – aktiverade Linux Azure Virtual Machines system
description: Lär dig mer om att använda VM-tillägg i python 3-aktiverade Linux-system
services: virtual-machines
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: d38b41bc47c01b01fcbb1b05238396dca6d8eb4e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558652"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problem med att använda VM-tillägg i python 3 – aktiverade Linux Azure Virtual Machines system

> [!NOTE]
> Microsoft uppmuntrar användare att använda **python 3. x** i sina system om inte arbets belastningen kräver **python 2. x** -support. Exempel på detta krav kan vara äldre administrations skript eller tillägg som **Azure Disk Encryption** och **Azure Monitor**.
>
> Innan du installerar **python 2. x** i produktion bör du överväga frågan om långsiktig support för python 2. x, särskilt deras förmåga att ta emot säkerhets uppdateringar. Som produkter, inklusive vissa av de tillägg som nämns, uppdaterar du med **python 3,8** -stöd, men du bör inte använda python 2. x.

Vissa Linux-distributioner har gått över till python 3,8 och har tagit bort den äldre `/usr/bin/python` Start punkten för python helt. Den här över gången påverkar den automatiserade distributionen av vissa tillägg för virtuella datorer (VM) med följande två villkor:

- Tillägg som fortfarande övergår till python 3. x-stöd
- Tillägg som använder den äldre `/usr/bin/python` Start punkten

Linux-distributions användare som har gått över till **python 3. x** måste se till att den äldre `/usr/bin/python` Start punkten finns innan du försöker distribuera dessa tillägg till sina virtuella datorer. Annars kan tilläggets distribution Miss Miss Missing. 

- Godkända Linux-distributioner som påverkas är **Ubuntu Server 20,04 LTS** och **Ubuntu Pro 20,04 LTS**.

- Berörda VM-tillägg omfattar **Azure Disk Encryption**, **Log Analytics**, **VM-åtkomst** (används för lösen ords återställning) och **gäst diagnostik** (används för ytterligare prestanda räknare).

Uppgraderingar på plats, till exempel uppgradering från **Ubuntu 18,04 LTS** till **Ubuntu 20,04 LTS**, bör behålla `/usr/bin/python` symlink och förbli opåverkade.

## <a name="resolution"></a>Lösning

Överväg de här allmänna rekommendationerna innan du distribuerar tillägg i de kända scenarier som beskrivs tidigare i sammanfattningen:

1. Innan du distribuerar tillägget återställer du `/usr/bin/python` symlink med hjälp av den leverantörs metod som tillhandahålls av Linux-distribution.

   - För **Python 2,7** använder du till exempel: `sudo apt update && sudo apt install python-is-python2`

1. Den här rekommendationen är för Azure-kunder och stöds inte i Azure Stack:

   - Om du redan har distribuerat en instans som uppvisar det här problemet använder du funktionen Kör kommando på bladet VM för att köra kommandona ovan. Själva kommando tillägget för körning påverkas inte av över gången till python 3,8.

1. Om du distribuerar en ny instans och behöver ange ett tillägg vid etablerings tiden använder du användar data i **Cloud-Init** för att installera de paket som anges ovan.

   Till exempel för python 2,7:

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. Om din organisations princip administratörer bestämmer att tillägg inte ska distribueras på virtuella datorer kan du inaktivera tilläggs stöd vid etablerings tiden:

   - REST-API

     Så här inaktiverar du och aktiverar tillägg när du kan distribuera en virtuell dator med den här egenskapen:

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>Nästa steg

Se [andra ändringar i bas systemet sedan 18,04 LTS – python 3 som standard](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) för ytterligare information.
