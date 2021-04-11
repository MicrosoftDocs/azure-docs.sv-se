---
title: Load Balancer TCP-återställning och tids gräns för inaktivitet i Azure
titleSuffix: Azure Load Balancer
description: I den här artikeln får du lära dig mer om Azure Load Balancer med dubbelriktade TCP-paket för inaktivitet vid inaktivitet.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 9b555d7972e88df85e08dbcb1737231755c21361
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448856"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer TCP-återställning och tids gräns för inaktivitet

Du kan använda [standard Load Balancer](./load-balancer-overview.md) för att skapa ett mer förutsägbart program beteende för dina scenarier genom att aktivera TCP-återställning vid inaktivitet för en viss regel. Load Balancerens standard beteende är att tyst släppa flöden när tids gränsen för inaktivitet för ett flöde uppnås.  Om du aktiverar den här funktionen kommer Load Balancer att skicka dubbelriktade TCP-återställningar (TCP-paket) vid inaktivitet.  Detta kommer att informera dina program slut punkter om att anslutningen har nått sin tids gräns och inte längre kan användas.  Slut punkter kan omedelbart upprätta en ny anslutning om det behövs.

![Load Balancer TCP-återställning](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP-återställning

Du ändrar det här standard beteendet och aktiverar sändning av TCP-återställning vid inaktivitet på inkommande NAT-regler, belastnings Utjämnings regler och [utgående regler](./load-balancer-outbound-connections.md#outboundrules).  När den aktive ras per regel kommer Load Balancer att skicka dubbelriktad TCP-återställning (TCP-paket) till både klient-och Server slut punkter vid tidpunkten för tids gränsen för inaktivitet för alla matchande flöden.

Slut punkter som tar emot TCP-och-paket stänger motsvarande socket direkt. Detta ger en omedelbar avisering till slut punkterna som lanseringen av anslutningen har inträffat och eventuell framtida kommunikation på samma TCP-anslutning.  Program kan rensa anslutningar när socketen stängs och återupprätta anslutningar vid behov utan att vänta på att TCP-anslutningen ska ta slut på timeout.

I många fall kan detta minska behovet av att skicka TCP (eller program lagret) keepalive för att uppdatera tids gränsen för inaktivitet i ett flöde. 

Om dina inaktiva varaktigheter överstiger de som tillåts av konfigurationen eller om programmet visar att TCP-återställningar har Aktiver ATS, kan du fortfarande behöva använda TCP keepalive (eller program lagrets keepalive) för att övervaka Live-anslutningarna för TCP-anslutningarna.  Dessutom kan keepalive-objekt även vara användbara för när anslutningen är via proxy någonstans i sökvägen, särskilt program lagrets keepalive.  

Undersök noggrant hela slut punkt till slut punkt för att avgöra om du har nytta av att aktivera TCP-återställningar, justera tids gränsen för inaktivitet och om ytterligare steg kan krävas för att säkerställa att det önskade programmet fungerar.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurerbar timeout för TCP-inaktivitet

Azure Load Balancer har följande intervall för inaktivitet:
-  4 minuter till 100 minuter för utgående regler
-  4 minuter till 30 minuter för Load Balancer regler och inkommande NAT-regler

Som standard är den inställd på 4 minuter. Om en period av inaktivitet är längre än timeout-värdet finns det ingen garanti för att TCP-eller HTTP-sessionen upprätthålls mellan klienten och moln tjänsten.

När anslutningen är stängd kan klient programmet få följande fel meddelande: "den underliggande anslutningen stängdes: en anslutning som förväntades vara aktiv stängdes av servern."

En vanlig metod är att använda en TCP Keep-Alive. Den här metoden håller anslutningen aktiv under en längre period. Mer information finns i dessa [.net-exempel](/dotnet/api/system.net.servicepoint.settcpkeepalive). När Keep-Alive är aktiverat skickas paketen under perioder av inaktivitet på anslutningen. Keep-Alive-paket se till att timeout-värdet för inaktivitet inte uppnås och att anslutningen upprätthålls under en längre period.

Inställningen fungerar endast för inkommande anslutningar. Undvik att förlora anslutningen genom att konfigurera TCP Keep-Alive med ett intervall som är lägre än tids gränsen för inaktivitet eller öka timeout-värdet för inaktivitet. För att stödja dessa scenarier har stöd för en konfigurerbar tids gräns för inaktivitet lagts till.

TCP Keep-Alive fungerar för scenarier där batteri tiden inte är en begränsning. Det rekommenderas inte för mobila program. Genom att använda en TCP Keep-Alive i ett mobilt program kan du tömma enhetens batteri snabbare.


## <a name="limitations"></a>Begränsningar

- TCP-återställning skickas endast under TCP-anslutning i upprättat läge.
- Timeout för TCP-inaktivitet påverkar inte belastnings Utjämnings regler på UDP-protokoll.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [standard Load Balancer](./load-balancer-overview.md).
- Läs mer om [utgående regler](./load-balancer-outbound-connections.md#outboundrules).
- [Konfigurera TCP-tidsgräns vid inaktivitet](load-balancer-tcp-idle-timeout.md)
