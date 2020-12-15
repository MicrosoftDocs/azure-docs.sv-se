---
title: Microsofts peering-princip
titleSuffix: Azure
description: Microsofts peering-princip
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 20f25e0add5d05bb2dcf7f3ebdc86ccd5ae889d0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510795"
---
# <a name="peering-policy"></a>Peering-policy
Microsoft har en selektiv peering-policy som är utformad för att säkerställa bästa möjliga kund upplevelse som backas upp av bransch standarder och bästa praxis, skalning för framtida behov och strategisk placering av peering. Microsoft förbehåller sig rätten att göra undantag till principen som bedömt nödvändigt. Microsofts allmänna krav från nätverket beskrivs i avsnitten nedan. Dessa gäller både direkt peering-och Exchange peering-begäranden. 

## <a name="technical-requirements"></a>Tekniska krav

* Ett fullständigt redundant nätverk med tillräcklig kapacitet för att utväxla trafik utan överbelastning.
* Peer kommer att ha ett offentligt dirigerbart autonomt system nummer (ASN).
* Både IPv4 och IPv6 stöds och Microsoft förväntar sig att upprätta sessioner av båda typerna på varje peering-plats.
* MD5 stöds inte.
* **ASN-information:**

    * Microsoft hanterar AS8075 tillsammans med följande ASN: er: AS8068, AS8069, AS12076. En fullständig lista över ASN: er med AS8075-peering, referens som-SET.
    * Alla parter som är peering med Microsoft accepterar att inte acceptera vägar från AS12076 (Express Route) under några omständigheter och bör filtrera ut AS12076 på alla peer-datorer.

* **Princip för Routning:**
    * Motparten kommer att ha minst ett offentligt dirigerbart/24.
    * Microsoft kommer att skriva över mottagna Diskriminatorer för flera avslutningar (med).
    * Microsoft föredrar att ta emot BGP community-taggar från peer-datorer för att indikera väg ursprung.
    * Vi rekommenderar att peer-datorer ställer in ett max-prefix på 2000 (IPv4) och 500 (IPv6) vägar på peering-sessioner med Microsoft.
    * Om det inte uttryckligen överenskommits på förhand förväntas motparter att tillkännage konsekventa vägar på alla platser där de är peer-kopplade med Microsoft.
    * I allmänhet kommer peering-sessioner med AS8075 att annonsera alla som-MICROSOFT-vägar. Microsoft kan tillkännage vissa regionala information.
    * Ingen av parterna kommer att upprätta en statisk väg, en väg till den sista utväg eller på annat sätt skicka trafik till den andra parten för en väg som inte annonseras via BGP.
    * Peer krävs för att registrera sina vägar i en IR-databas (Public Internet routing Registry), i syfte att filtrera och se till att den här informationen är aktuell.      
    * Peer-datorer följer MANRS bransch standarder för väg säkerhet.  I det enda fallet kan Microsoft välja: 1.) att inte upprätta peering med företag som inte har några vägar signerade och registrerade. 2.) för att ta bort ogiltiga RPKI vägar. 3.) för att inte acceptera vägar från etablerade peer-datorer som inte är registrerade och signerade. 

## <a name="operational-requirements"></a>Drift krav
* En fullständigt bemannad nätverks åtgärds Center (NOC), som kan hjälpa till med lösning på alla tekniska och prestanda problem, säkerhets överträdelser, Denial of Service-attacker eller andra missbruk med ursprung i motparten eller deras kunder.
* Peer-datorer förväntas ha en fullständig och uppdaterad profil på [PeeringDB](https://www.peeringdb.com) , till exempel ett e-postmeddelande dygnet runt från företags domänen och telefonnumret. Vi använder den här informationen för att verifiera motpartens information, till exempel NOC information, teknisk kontakt information och deras närvaro vid peering-anläggningarna osv. Personliga Yahoo-, Gmail-och Hotmail-konton godkänns inte.

## <a name="physical-connection-requirements"></a>Krav för fysisk anslutning
* De platser där du kan ansluta till Microsoft för direkt peering eller Exchange-peering finns i [PeeringDB](https://www.peeringdb.com/net/694)

* **Exchange-peering:**
    * Peer-datorer förväntas ha minst 10 GB anslutning till Exchange.
    * Peer-datorer förväntas uppgradera sina portar när högsta användning överskrider 50%.
    * Microsoft uppmuntrar peer-datorer att upprätthålla olika anslutningar till Exchange för att stödja failover-scenarier.

* **Direkt peering:**
    * Samtrafik måste vara över en fiber fiber med 100 Gbit/s optik.
    * Microsoft upprättar endast direkt peering med ISP-eller nätverks tjänst leverantörer.
    * Peer-datorer förväntas uppgradera sina portar när den högsta belastningen överskrider 50% och har olika kapacitet i varje tunnelbane linje, antingen inom en enda plats eller på flera platser i en tunnelbane linje.
    * Varje direkt peering består av två anslutningar till två Microsoft Edge-routrar från peer-routrar som finns i peer-gränsen. Microsoft kräver dubbla BGP-sessioner över dessa anslutningar. Motparten kan välja att inte distribuera redundanta enheter i slutet.


## <a name="traffic-requirements"></a>Trafik krav

* Peer-datorer över Exchange-peering måste ha minst 500 MB trafik och mindre än 2 GB. För trafik som överstiger 2 GB direkt peering bör upprättas.
* Microsoft kräver minst 2 GB för direkt peering. Var och en som helst avtalas för peering-platsen måste ha stöd för redundans som säkerställer att peering förblir lokaliserad under ett failover-scenario. 

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om hur du konfigurerar direkt peering med Microsoft följer du [genom gången av direkt peering](walkthrough-direct-all.md).
* Om du vill veta mer om hur du konfigurerar Exchange-peering med Microsoft följer du [genom gången av Exchange-peering](walkthrough-exchange-all.md).
