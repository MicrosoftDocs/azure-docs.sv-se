---
title: Flera klient delar – Azure Load Balancer
description: Med den här utbildnings vägen kan du komma igång med en översikt över flera klient delar på Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5c2072d13cab9839a276c0437747d7075918e78a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696888"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Flera klient delar för Azure Load Balancer

Med Azure Load Balancer kan du belastningsutjämna tjänster på flera portar, flera IP-adresser eller både och. Du kan använda offentliga och interna definitioner för belastnings utjämning för att belastningsutjämna flöden över en uppsättning virtuella datorer.

Den här artikeln beskriver grunderna för den här funktionen, viktiga begrepp och begränsningar. Om du bara planerar att exponera tjänster på en IP-adress, kan du hitta förenklade instruktioner för [offentliga](./quickstart-load-balancer-standard-public-portal.md) eller [interna](./quickstart-load-balancer-standard-internal-portal.md) konfigurationer för belastnings utjämning. Att lägga till flera klient delar ökar stegvis till en enda klient dels konfiguration. Med hjälp av begreppen i den här artikeln kan du när som helst expandera en förenklad konfiguration.

När du definierar en Azure Load Balancer är en klient del och en konfiguration av en backend-pool ansluten med regler. Hälso avsökningen som regeln hänvisar till används för att avgöra hur nya flöden skickas till en nod i backend-poolen. Klient delen (aka VIP) definieras av en 3-tupel som består av en IP-adress (offentlig eller intern), ett transport protokoll (UDP eller TCP) och ett port nummer från belastnings Utjämnings regeln. Backend-poolen är en samling virtuella dator-IP-konfigurationer (en del av NIC-resursen) som refererar till Load Balancer backend-poolen.

Följande tabell innehåller några exempel på konfiguration av frontend:

| Klientdel | IP-adress | protokollhanterare | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

I tabellen visas fyra olika klient delar. Frontend-#1, #2 och #3 är en enda klient del med flera regler. Samma IP-adress används men porten eller protokollet är olika för varje klient del. Frontend-#1 och #4 är ett exempel på flera klient delar där samma klient dels protokoll och port återanvänds i flera klient delar.

Azure Load Balancer ger flexibilitet i att definiera belastnings Utjämnings regler. En regel förklarar hur en adress och port på klient delen mappas till mål adressen och porten på Server delen. Huruvida Server dels portar återanvänds över regler beror på regelns typ. Varje typ av regel har särskilda krav som kan påverka utformningen av värd konfiguration och sökning. Det finns två typer av regler:

1. Standard regeln utan åter användning av Server dels portar
2. Den flytande IP-regel där backend-portar återanvänds

Med Azure Load Balancer kan du blanda båda regel typerna på samma belastnings Utjämnings konfiguration. Belastningsutjämnaren kan använda dem samtidigt för en virtuell dator, eller en kombination, så länge du följer begränsningarna i regeln. Vilken regeltyp du väljer beror på kraven för ditt program och hur du hanterar konfigurationen. Du bör utvärdera vilka regel typer som passar bäst för ditt scenario.

Vi utforskar dessa scenarier ytterligare genom att börja med standard beteendet.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regel typ #1: ingen server dels port åter användning

![Bild av flera klient delar med grön och lila frontend](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

I det här scenariot konfigureras klient dels inställningarna på följande sätt:

| Klientdel | IP-adress | protokollhanterare | port |
| --- | --- | --- | --- |
| ![grön frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila klient del](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP är målet för det inkommande flödet. I backend-poolen exponerar varje virtuell dator den önskade tjänsten på en unik port på en DIP. Den här tjänsten är associerad med klient delen via en regel definition.

Vi definierar två regler:

| Regel | Mappa klient del | Till backend-pool |
| --- | --- | --- |
| 1 |![grön frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![grön Server del](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![grön Server del](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![lila Server del](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![lila Server del](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Den fullständiga mappningen i Azure Load Balancer är nu enligt följande:

| Regel | IP-adress för klient del | protokollhanterare | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP-IP-adress |80 |
| ![lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP-IP-adress |81 |

Varje regel måste skapa ett flöde med en unik kombination av målets IP-adress och mål Port. Genom att variera mål porten för flödet kan flera regler leverera flöden till samma DIP på olika portar.

Hälso avsökningar dirigeras alltid till DIP för en virtuell dator. Du måste se till att avsökningen visar hälso tillståndet för den virtuella datorn.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regeltyp #2: åter användning av backend-portar med hjälp av flytande IP

Azure Load Balancer ger flexibiliteten att återanvända klient dels porten över flera klient delar oavsett vilken regeltyp som används. Dessutom föredrar vissa program scenarier eller kräver att samma port används av flera program instanser på en enskild virtuell dator i backend-poolen. Vanliga exempel på port åter användning är klustring för hög tillgänglighet, virtuella nätverks installationer och exponerar flera TLS-slutpunkter utan omkryptering.

Om du vill återanvända backend-porten över flera regler måste du aktivera flytande IP i regel definitionen.

"Flytande IP" är Azures terminologi för en del av vad som kallas för direkt Server retur (DSR). DSR består av två delar: en flödes sto pol Ogin och ett schema för IP-adress mappning. På en plattforms nivå körs Azure Load Balancer alltid i en hög DSR-flödes topologi oavsett om flytande IP är aktiverat eller inte. Det innebär att den utgående delen av ett flöde alltid skrivs om korrekt för att flöda direkt tillbaka till ursprunget.

Med standard regel typen exponerar Azure ett traditionellt mappnings schema för IP-adresser för belastnings utjämning för enkel användning. När du aktiverar flytande IP-adresser ändras schemat för IP-adresser så att du får ytterligare flexibilitet enligt beskrivningen nedan.

Följande diagram visar den här konfigurationen:

![Bild av flera klient delar med grön och lila frontend med DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

För det här scenariot har varje virtuell dator i backend-poolen tre nätverks gränssnitt:

* DIP: ett virtuellt nätverkskort som är kopplat till den virtuella datorn (IP-konfiguration för Azures NIC-resurs)
* Klient del 1: ett loopback-gränssnitt i gäst operativ system som är konfigurerat med IP-adressen för klient delen 1
* Frontend 2: ett loopback-gränssnitt i gäst operativ system som är konfigurerat med IP-adressen för klient delen 2

För varje virtuell dator i backend-poolen kör du följande kommandon i kommando tolken i Windows.

Om du vill hämta en lista över gränssnitts namn som du har på den virtuella datorn skriver du följande kommando:

```console
netsh interface show interface 
```

För det virtuella dator NÄTVERKSKORTet (Azure Managed) skriver du följande kommando:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(Ersätt gränssnitt med namnet på det här gränssnittet)

Upprepa följande kommandon för varje loopback-gränssnitt som du har lagt till:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(Ersätt interfacename med namnet på detta loopback-gränssnitt)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(Ersätt interfacename med namnet på detta loopback-gränssnitt)

> [!IMPORTANT]
> Konfigurationen av loopback-gränssnitten utförs i gäst operativ systemet. Den här konfigurationen utförs eller hanteras inte av Azure. Utan den här konfigurationen fungerar inte reglerna. Hälso avsöknings definitioner använder DIP för den virtuella datorn i stället för loopback-gränssnittet som representerar DSR-frontend. Därför måste tjänsten tillhandahålla avsöknings svar på en DIP-port som visar statusen för den tjänst som erbjuds i loopback-gränssnittet som representerar DSR-frontend.


Vi antar samma konfiguration för klient delen som i föregående scenario:

| Klientdel | IP-adress | protokollhanterare | port |
| --- | --- | --- | --- |
| ![grön frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila klient del](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Vi definierar två regler:

| Regel | Klientdel | Mappa till backend-pool |
| --- | --- | --- |
| 1 |![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![grön Server del](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (i VM1 och VM2) |
| 2 |![lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![lila Server del](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (i VM1 och VM2) |

I följande tabell visas en fullständig mappning i belastningsutjämnaren:

| Regel | IP-adress för klient del | protokollhanterare | port | Mål | port |
| --- | --- | --- | --- | --- | --- |
| ![grön regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |samma som klient del (65.52.0.1) |samma som klient del (80) |
| ![lila regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |samma som klient del (65.52.0.2) |samma som klient del (80) |

Målet för det inkommande flödet är klient delens IP-adress på loopback-gränssnittet på den virtuella datorn. Varje regel måste skapa ett flöde med en unik kombination av målets IP-adress och mål Port. Genom att variera målets IP-adress kan åter användning av portar vara möjlig på samma virtuella dator. Tjänsten exponeras för belastningsutjämnaren genom att binda den till klient delens IP-adress och port för respektive loopback-gränssnitt.

Observera att det här exemplet inte ändrar mål porten. Även om detta är ett flytande IP-scenario stöder Azure Load Balancer också att definiera en regel för att skriva om Server dels mål porten och att den skiljer sig från klient delens målport.

Typ av flytande IP-regel är grunden för flera konfigurations mönster för belastnings utjämning. Ett exempel som för närvarande är tillgängligt är [SQL AlwaysOn med flera Listener](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) -konfigurationer. Med tiden kommer vi att dokumentera fler av dessa scenarier.

## <a name="limitations"></a>Begränsningar

* Flera klient dels konfigurationer stöds endast med virtuella IaaS-datorer.
* Med den flytande IP-regeln måste programmet använda den primära IP-konfigurationen för utgående SNAT-flöden. Om programmet binder till klient delens IP-adress som kon figurer ATS i loopback-gränssnittet i gäst operativ systemet, är Azures utgående SNAT inte tillgängligt för att skriva om det utgående flödet och flödet Miss lyckas.  Granska [utgående scenarier](load-balancer-outbound-connections.md).
* Flytande IP stöds för närvarande inte på sekundära IP-konfigurationer för interna belastnings Utjämnings scenarier.
* Offentliga IP-adresser har en inverkan på faktureringen. Mer information finns i pris information för [IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Prenumerations begränsningar gäller. Mer information finns i [tjänst begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) för mer information.

## <a name="next-steps"></a>Nästa steg

- Granska [utgående anslutningar](load-balancer-outbound-connections.md) för att förstå effekten av flera klient delar vid utgående anslutning.