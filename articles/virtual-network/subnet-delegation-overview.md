---
title: Vad är under näts delegering i Azure Virtual Network?
description: Läs mer om delegering av undernät i Azure Virtual Network
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2020
ms.author: kumud
ms.openlocfilehash: 2801aa6b2e2b72df815b170200587c49ec0bae14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539000"
---
# <a name="what-is-subnet-delegation"></a>Vad är under näts delegering?

Med under näts delegering kan du ange ett speciellt undernät för en Azure PaaS-tjänst som du väljer och som måste matas in i det virtuella nätverket. Under näts delegering ger kunden fullständig kontroll över integreringen av Azure-tjänster i sina virtuella nätverk.

När du delegerar ett undernät till en Azure-tjänst tillåter du att tjänsten upprättar vissa grundläggande nätverks konfigurations regler för det under nätet, som hjälper Azure-tjänsten att använda sina instanser på ett stabilt sätt. Det innebär att Azure-tjänsten kan upprätta vissa för-eller-distributions villkor, till exempel:
- distribuera tjänsten i ett delat och dedikerat undernät.
- Lägg till i tjänsten en uppsättning principer för nätverks avsikt efter distribution som krävs för att tjänsten ska fungera korrekt.

##  <a name="advantages-of-subnet-delegation"></a>Fördelar med under näts delegering

Att delegera ett undernät till vissa tjänster ger följande fördelar:

- hjälper till att utse ett undernät för en eller flera Azure-tjänster och hantera instanserna i under nätet enligt krav. Till exempel kan den virtuella nätverks ägaren definiera följande för ett delegerat undernät för att hantera resurser och åtkomst på ett bättre sätt:
    - nätverks filtrerings trafik principer med nätverks säkerhets grupper.
    - principer för routning med användardefinierade vägar.
    - tjänst integrering med konfiguration av tjänst slut punkter.
- hjälper till att mata in tjänster för att bättre integrera med det virtuella nätverket genom att definiera deras för-villkor för distributioner i form av principer för nätverks avsikt. Detta säkerställer att alla åtgärder som kan påverka funktion i den inmatade tjänsten kan blockeras vid placering.


## <a name="who-can-delegate"></a>Vem kan delegera?
Under näts delegering är en övning som de virtuella nätverks ägarna måste utföra för att utse ett undernät för en enskild Azure-tjänst. Azure-tjänsten i sin tur distribuerar instanserna till det här under nätet för förbrukning av kundens arbets belastningar.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Påverkan av under näts delegering i under nätet
Varje Azure-tjänst definierar sin egen distributions modell där de kan definiera vilka egenskaper de gör eller inte stöder i ett delegerat undernät för injektions syfte, till exempel följande:
- delat undernät med andra Azure-tjänster eller skalnings uppsättningar för virtuella datorer/virtuella datorer i samma undernät, eller så stöder det bara ett dedikerat undernät med endast instanser av den här tjänsten.
- stöder NSG-Association med det delegerade under nätet.
- stöder NSG som är kopplade till det delegerade under nätet kan också associeras med andra undernät.
- tillåter koppling av tabell koppling med det delegerade under nätet.
- tillåter att routningstabellen som är associerad med det delegerade under nätet associeras med andra undernät.
- avgör det minsta antalet IP-adresser i det delegerade under nätet.
- avgör om IP-adressutrymmet i det delegerade under nätet ska vara från det privata IP-adressutrymmet (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- dikterar att den anpassade DNS-konfigurationen har en Azure DNS post.
- kräver att delegering tas bort innan under nätet eller det virtuella nätverket kan tas bort.
- kan inte användas med en privat slut punkt om under nätet är delegerat.

Inmatade tjänster kan också lägga till egna principer enligt följande:
- **Säkerhets principer**: en samling säkerhets regler som krävs för att en specifik tjänst ska fungera.
- **Väg principer**: en samling vägar som krävs för att en specifik tjänst ska fungera.

## <a name="what-subnet-delegation-does-not-do"></a>Vilken under näts delegering inte gör

De Azure-tjänster som matas in i ett delegerat undernät har fortfarande den grundläggande uppsättningen egenskaper som är tillgängliga för icke-delegerade undernät, till exempel:
-  Azure-tjänster kan mata in instanser i kund under nät, men de kan inte påverka befintliga arbets belastningar.
-  Principerna eller vägarna som tjänsterna gäller är flexibla och kan åsidosättas av kunden.

## <a name="next-steps"></a>Nästa steg

- [Delegera ett undernät](manage-subnet-delegation.md)
