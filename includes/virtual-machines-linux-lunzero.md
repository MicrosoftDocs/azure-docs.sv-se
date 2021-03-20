---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09fa612e7e5c681da16bf19e94c626ae14a3b8a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77590720"
---
När du lägger till data diskar till en virtuell Linux-dator kan du stöta på fel om en disk inte finns på LUN 0. Om du lägger till en disk manuellt med `az vm disk attach -new` kommandot och du anger ett LUN ( `--lun` ) i stället för att tillåta att Azure-plattformen fastställer lämplig LUN, bör du tänka på att en disk redan finns/kommer att finnas på LUN 0. 

Tänk på följande exempel som visar ett kodfragment av utdata från `lsscsi` :

```bash
[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc 
[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd 
```

De två data diskarna finns på LUN 0 och LUN 1 (den första kolumnen i `lsscsi` informationen om utdata `[host:channel:target:lun]` ). Båda diskarna bör vara tillgängliga från den virtuella datorn. Om du har angett att den första disken manuellt ska läggas till på LUN 1 och den andra disken vid LUN 2, kanske du inte ser diskarna på rätt sätt i den virtuella datorn.

> [!NOTE]
> Azure `host` -värdet är 5 i de här exemplen, men det kan variera beroende på vilken typ av lagring du väljer.
> 
> 

Det här disk beteendet är inte ett Azure-problem, men det sätt på vilket Linux-kernel följer SCSI-specifikationerna. När Linux-kärnan genomsöker SCSI-bussen efter anslutna enheter måste en enhet hittas på LUN 0 för att systemet ska kunna fortsätta att söka efter ytterligare enheter. Så här:

* Granska utdata från `lsscsi` när du har lagt till en datadisk för att kontrol lera att du har en disk på LUN 0.
* Om disken inte visas korrekt i den virtuella datorn kontrollerar du att det finns en disk på LUN 0.

