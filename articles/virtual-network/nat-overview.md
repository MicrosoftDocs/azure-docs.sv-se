---
title: Vad är Azure Virtual Network NAT?
titlesuffix: Azure Virtual Network
description: Översikt över Virtual Network NAT-funktioner, resurser, arkitektur och implementering. Lär dig hur Virtual Network NAT fungerar och hur du använder resurser för NAT-gateway i molnet.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: allensu
ms.openlocfilehash: 75a2bb187b2ed7a234e99d8cd293cb30148bcb1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91667205"
---
# <a name="what-is-virtual-network-nat"></a>Vad är Virtual Network NAT?

Virtual Network NAT (Network Address Translation) fören klar endast utgående Internet-anslutning för virtuella nätverk. När den konfigureras i ett undernät använder all utgående anslutning dina angivna statiska offentliga IP-adresser.  Utgående anslutningar är möjlig utan belastningsutjämnare eller offentliga IP-adresser som är direkt anslutna till virtuella datorer. NAT är fullständigt hanterat och mycket flexibelt.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP (PIP) and an IP prefix." width="256" title="Virtual Network NAT">
</p>



*Bild: Virtual Network NAT*

## <a name="static-ip-addresses-for-outbound-only"></a>Statiska IP-adresser för utgående

Utgående anslutningar kan definieras för varje undernät med NAT.  Flera undernät i samma virtuella nätverk kan ha olika NAT. Ett undernät konfigureras genom att ange vilken NAT-gateway-resurs som ska användas. Alla utgående UDP-och TCP-flöden från en virtuell dator instans använder NAT. 

NAT är kompatibelt med standard-SKU offentliga IP-adressresurser eller offentliga IP-adressprefix eller en kombination av båda.  Du kan använda ett offentligt IP-prefix direkt eller distribuera de offentliga IP-adresserna för prefixet över flera NAT gateway-resurser. NAT rensar all trafik till prefixets IP-adress intervall.  Det är nu enkelt att använda IP-filtrering för dina distributioner.

All utgående trafik för under nätet bearbetas av NAT automatiskt utan någon kund konfiguration.  Användardefinierade vägar är inte nödvändiga. NAT prioriteras jämfört med andra utgående scenarier och ersätter ett undernäts standard mål.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>SNAT på begäran med flera IP-adresser för skalning

NAT använder "Port Network Address Translation" (PNAT eller PAT) och rekommenderas för de flesta arbets belastningar. Dynamiska eller Divergent arbets belastningar kan enkelt anpassas med utgående flödes tilldelning på begäran. En omfattande för planering, Förallokering och i slut ändan av utgående resurser undviks. SNAT-port resurser delas och är tillgängliga i alla undernät med en angiven NAT-gateway-resurs och tillhandahålls när det behövs.

En offentlig IP-adress som är ansluten till NAT innehåller upp till 64 000 samtidiga flöden för UDP respektive TCP. Du kan börja med en enda IP-adress och skala upp till 16 IP-adresser med hjälp av offentliga IP-adresser eller offentliga IP-prefix eller både och.  En NAT-gateway-resurs kommer att använda alla IP-adresser som är kopplade till resursen för utgående anslutningar från alla undernät som kon figurer ATS med samma NAT-gateway-resurs.

NAT gör det möjligt att skapa flöden från det virtuella nätverket till Internet. Retur trafik från Internet tillåts bara som svar på ett aktivt flöde.

Till skillnad från utgående SNAT för belastningsutjämnare har NAT inga begränsningar för vilka privata IP-adresser för en virtuell dator instans kan göra utgående anslutningar.  Primära och sekundära IP-konfigurationer kan skapa utgående Internet anslutning med NAT.

## <a name="coexistence-of-inbound-and-outbound"></a>Samexistens av inkommande och utgående

NAT är kompatibelt med följande standard-SKU-resurser:

- Lastbalanserare
- Offentlig IP-adress
- Offentligt IP-prefix

När de används tillsammans med NAT tillhandahåller de här resurserna inkommande Internet anslutning till dina undernät. NAT tillhandahåller all utgående Internet anslutning från dina undernät.

NAT-och kompatibla standard-SKU-funktioner är medvetna om i vilken riktning som flödet startades. Inkommande och utgående scenarier kan finnas. De här scenarierna tar emot rätt översättning av nätverks adresser eftersom dessa funktioner är medvetna om flödes riktningen. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="512" title="Virtual Network NAT-flödets riktning">
</p>

*Bild: Virtual Network NAT-flödets riktning*

## <a name="fully-managed-highly-resilient"></a>Fullständigt hanterad, mycket elastisk

NAT har skalats ut från början. Det krävs ingen RAMPs-eller skalnings åtgärd.  Azure hanterar NAT-funktionen åt dig.  NAT har alltid flera fel domäner och kan hantera flera fel utan avbrott i tjänsten.

## <a name="tcp-reset-for-unrecognized-flows"></a>TCP-återställning för okända flöden

Den privata sidan av NAT skickar TCP-återställnings paket för försök att kommunicera med en TCP-anslutning som inte finns. Ett exempel är anslutningar som har nått tids gränsen för inaktivitet. Nästa paket som tas emot returnerar en TCP-återställning till den privata IP-adressen för att signalera och tvinga anslutningen att avslutas.

Den offentliga sidan av NAT genererar inte TCP-återställnings paket eller någon annan trafik.  Endast trafik som genereras av kundens virtuella nätverk genereras.

## <a name="configurable-tcp-idle-timeout"></a>Konfigurerbar timeout för TCP-inaktivitet

En förinställd timeout för TCP-inaktivitet på 4 minuter används och kan ökas till upp till 120 minuter. Alla aktiviteter i ett flöde kan också återställa inaktiva timer, inklusive TCP keepalive-tillägg.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Regional eller zon isolering med tillgänglighets zoner

NAT är regional som standard. När du skapar scenarier för [tillgänglighets zoner](../availability-zones/az-overview.md) kan NAT isoleras i en speciell zon (zonindelade-distribution).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="512" title="Virtual Network NAT med tillgänglighets zoner">
</p>

*Bild: Virtual Network NAT med tillgänglighets zoner*

## <a name="multi-dimensional-metrics-for-observability"></a>Flerdimensionella mått för att kunna iakttas

Du kan övervaka hur din NAT fungerar genom flerdimensionella mått som visas i Azure Monitor. Dessa mått kan användas för att observera användningen och för fel sökning.  NAT-gateway-resurser visar följande mått:
- Byte
- Skickas
- Ignorerade paket
- Totalt antal SNAT-anslutningar
- SNAT-anslutnings tillstånds över gångar per intervall.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Vid allmän tillgänglighet är NAT-datasökvägen minst 99,9% tillgänglig.

## <a name="pricing"></a>Priser

Pris information finns i [Virtual Network prissättning](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="availability"></a>Tillgänglighet

Virtual Network NAT och NAT-gateway-resursen är tillgänglig i alla regioner i alla Azure-moln [regioner](https://azure.microsoft.com/global-infrastructure/regions/).

## <a name="suggestions"></a>Förslag

Vi vill veta hur vi kan förbättra tjänsten. Föreslå och rösta på det vi ska bygga nästa på [UserVoice för NAT](https://aka.ms/natuservoice).

## <a name="limitations"></a>Begränsningar

* NAT är kompatibelt med standard-SKU offentlig IP, offentliga IP-prefix och belastnings Utjämnings resurser. Grundläggande resurser, t. ex. Basic Load Balancer, och alla produkter som härletts från dem är inte kompatibla med NAT.  Grundläggande resurser måste placeras i ett undernät som inte har kon figurer ATS med NAT.
* IPv4-adress familjen stöds.  NAT interagerar inte med IPv6-adress familjen.  Det går inte att distribuera NAT i ett undernät med ett IPv6-prefix.
* NAT kan inte omfatta flera virtuella nätverk.

## <a name="next-steps"></a>Nästa steg

* Lär dig om [NAT gateway-resurs](./nat-gateway-resource.md).
* [Berätta för oss vad du ska bygga härnäst för Virtual Network NAT i UserVoice](https://aka.ms/natuservoice).
