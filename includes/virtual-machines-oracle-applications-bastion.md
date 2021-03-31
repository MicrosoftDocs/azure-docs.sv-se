---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "68361549"
---
### <a name="bastion-tier"></a>Skydds-nivå

Skydds-värden är en valfri komponent som du kan använda som en hopp Server för att komma åt programmet och databas instanserna. Den virtuella skydds-värddatorn kan ha en offentlig IP-adress som tilldelats, även om rekommendationen är att konfigurera en ExpressRoute-anslutning eller VPN för plats till plats med ditt lokala nätverk för säker åtkomst. Dessutom bör endast SSH (port 22, Linux) eller RDP (port 3389, Windows Server) öppnas för inkommande trafik. För hög tillgänglighet kan du distribuera en skydds-värd i två tillgänglighets zoner eller i en enda tillgänglighets uppsättning.

Du kan också aktivera vidarebefordran av SSH-agenten på dina virtuella datorer, vilket gör att du kan komma åt andra virtuella datorer i det virtuella nätverket genom att vidarebefordra autentiseringsuppgifterna från din skydds-värd. Du kan också använda SSH-tunnlar för att få åtkomst till andra instanser.

Här är ett exempel på agent vidarebefordring:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Det här kommandot ansluter till skydds och körs omedelbart `ssh` igen, så du får en terminal på mål instansen. Du kan behöva ange en annan användare än roten på mål instansen om klustret har kon figurer ATS på ett annat sätt. `-A`Argumentet vidarebefordrar agent anslutningen så att din privata nyckel på den lokala datorn används automatiskt. Observera att agent vidarebefordring är en kedja, så det andra `ssh` kommandot inkluderar även `-A` så att alla efterföljande SSH-anslutningar som initieras från mål instansen också använder din lokala privata nyckel.