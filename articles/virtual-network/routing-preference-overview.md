---
title: Flödes inställningar i Azure
description: Läs om hur du kan välja hur trafiken dirigeras mellan Azure och Internet med cirkulations inställningar.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 56fd9898065dd55b9a56547a775f7f2aa4a56156
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693738"
---
# <a name="what-is-routing-preference"></a>Vad är routningsprioritet?

Med Azure routing-inställningen kan du välja hur trafiken ska dirigeras mellan Azure och Internet. Du kan välja att dirigera trafik antingen via Microsoft-nätverket eller via Internet leverantörs nätverket (offentligt Internet). De här alternativen kallas även för *kall källroutning* och routning av *aktiva potatis* . Priset för utgående data överföring varierar beroende på valet av routning. Du kan välja alternativet routning när du skapar en offentlig IP-adress. Den offentliga IP-adressen kan associeras med resurser, till exempel virtuell dator, skalnings uppsättningar för virtuella datorer, belastnings utjämning på Internet, osv. Du kan också ange cirkulations inställningar för Azure Storage-resurser som blobbar, filer, webb och Azure-DataLake. Trafiken dirigeras som standard via Microsofts globala nätverk för alla Azure-tjänster.

## <a name="routing-via-microsoft-global-network"></a>Routning via Microsoft globalt nätverk

När du dirigerar din trafik via *Microsofts globala nätverk*, levereras trafiken över ett av de största nätverken i världen som sträcker sig över 160 000 mil fibrer med över 165 Edge Point of Presence (pop). Nätverket är väl etablerad med flera redundanta fiber vägar för att säkerställa hög tillförlitlighet och tillgänglighet. Trafik teknikerna hanteras av en programdefinierad WAN-styrenhet som garanterar en låg latens Sök vägs val för din trafik och ger förstklassig nätverks prestanda.

![Routning via Microsoft globalt nätverk](media/routing-preference-overview/route-via-microsoft-global-network.png)

**Ingress trafik:** Det globala BGP anycast-meddelandet säkerställer att inkommande trafik går in i det Microsoft-nätverk som är närmast användaren. Till exempel, om en användare från Singapore får åtkomst till Azure-resurser som finns i Chicago, kommer USA att ingå i Microsoft globalt nätverk i Singapore Edge POP och reser i Microsoft Network till den tjänst som finns i Chicago.

**Utgående trafik:** Den utgående trafiken följer samma princip. Trafiken reser merparten av sin resa på Microsofts globala nätverk och avslutas närmast användaren. Om till exempel trafik från Azure Chicago är avsedd för en användare från Singapore, överförs trafiken i Microsoft Network från Chicago till Singapore, och Microsoft-nätverket i Singapore Edge-POP avslutas.

Både ingångs-och utgångs trafik förblir Mass av resan i Microsofts globala nätverk. Detta kallas även för *kall potatis-routning*.


## <a name="routing-over-public-internet-isp-network"></a>Routning över offentligt Internet (Internet leverantörs nätverk)

Det nya Dirigerings alternativet *Internet routning* minimerar resor i Microsofts globala nätverk, och använder ett ÖVERFÖRINGs leverantörs nätverk för att dirigera trafiken. Det här alternativet för kostnads optimerad routning ger nätverks prestanda som är jämförbar med andra moln leverantörer.

![Routning över offentligt Internet](media/routing-preference-overview/route-via-isp-network.png)

**Ingress trafik:** Sökvägen för ingångs punkt använder frekvent *källroutning* , vilket innebär att trafiken går in i Microsoft-nätverket som är närmast den värdbaserade tjänst regionen. Till exempel, om en användare från Singapore får åtkomst till Azure-resurser som finns i Chicago, överförs trafiken via det offentliga Internet och går in i Microsofts globala nätverk i Chicago.

**Utgående trafik:** Den utgående trafiken följer samma princip. Trafiken avslutar Microsoft Network i samma region som tjänsten finns i. Om till exempel trafik från din tjänst i Azure Chicago är avsedd för en användare från Singapore, avslutar trafiken Microsoft Network i Chicago och reser via det offentliga Internet till användaren i Singapore.

## <a name="supported-services"></a>Tjänster som stöds

Alternativet för offentlig IP-adress med Routning val "Microsoft globalt nätverk" kan associeras med alla Azure-tjänster. Offentliga IP-adresser med inställnings alternativ för **routning kan dock** vara kopplade till följande Azure-resurser:

* Virtuell dator
* Skaluppsättning för virtuella datorer
* Azure Kubernetes Service (AKS)
* Belastningsutjämnare mot Internet
* Application Gateway
* Azure Firewall

För lagring använder primära slut punkter alltid **Microsofts globala nätverk**. Du kan aktivera sekundära slut punkter med **Internet** som du väljer för trafik dirigering. De lagrings tjänster som stöds är:

* Blobar
* Filer
* Webb
* Azure DataLake

## <a name="pricing"></a>Prissättning
Pris skillnaden mellan båda alternativen återspeglas i priset för utgående data överföring i Internet. Routning via **Microsofts globala nätverks** data överförings pris är detsamma som aktuellt pris för utgående Internet. Besök [pris sidan för Azure bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/) för den senaste pris informationen.

## <a name="limitations"></a>Begränsningar


* Dirigerings inställningar är bara kompatibla med zon-redundant standard-SKU för offentlig IP-adress. Grundläggande SKU för offentlig IP-adress stöds inte.
* Inställningen routning stöder för närvarande endast IPv4 offentliga IP-adresser. Offentliga IPv6-IP-adresser stöds inte.


## <a name="next-steps"></a>Nästa steg

* [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure CLI](configure-routing-preference-virtual-machine-cli.md)
