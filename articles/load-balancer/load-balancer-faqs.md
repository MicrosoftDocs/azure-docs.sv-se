---
title: Vanliga frågor och svar – Azure Load Balancer
description: Svar på vanliga frågor om Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3752a36d22f879b95b02bd49436be78212fe56a2
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576049"
---
# <a name="load-balancer-frequently-asked-questions"></a>Vanliga frågor och svar om Load Balancer

## <a name="what-types-of-load-balancer-exist"></a>Vilka typer av Load Balancer finns?
Interna belastningsutjämnare som balanserar trafik i ett VNET och externa belastningsutjämnare som balanserar trafik till och från en ansluten Internet-slutpunkt. Mer information finns i [Load Balancer typer](components.md#frontend-ip-configurations). 

För båda dessa typer erbjuder Azure en grundläggande SKU och standard-SKU som har olika funktioner för funktioner, prestanda, säkerhet och hälso tillstånd. Dessa skillnader beskrivs i vår artikel om [SKU-jämförelse](skus.md) .

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Hur kan jag uppgradera från en Basic till en Standard Load Balancer?
Se artikeln [Uppgradera från Basic till standard](upgrade-basic-standard.md) för ett automatiserat skript och vägledning om hur du uppgraderar en Load Balancer SKU.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Vilka är de olika alternativen för belastnings utjämning i Azure?
Se [teknik guiden för belastningsutjämnare](/azure/architecture/guide/technology-choices/load-balancing-overview)  för de tillgängliga belastnings Utjämnings tjänsterna och rekommenderade användnings områden för var och en.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Var hittar jag Load Balancer ARM-mallar?
Se [listan över Azure Load Balancer snabb starts mallar](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) för ARM-mallar för vanliga distributioner.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>Hur skiljer sig inkommande NAT-regler från regler för belastnings utjämning?
NAT-regler används för att ange en server dels resurs som trafik ska skickas till. Du kan till exempel konfigurera en speciell belastnings Utjämnings port för att skicka RDP-trafik till en angiven virtuell dator. Regler för belastnings utjämning används för att ange en pool med Server dels resurser för att dirigera trafik till, vilket balanserar belastningen över varje instans. En belastnings Utjämnings regel kan till exempel dirigera TCP-paket på port 80 för belastningsutjämnaren i en pool av webb servrar.

## <a name="what-is-ip-1686312916"></a>Vad är IP-168.63.129.16?
Den virtuella IP-adressen för värden som taggats som Azure-infrastruktur Load Balancer där Azures hälso avsökningen kommer. När du konfigurerar Server dels instanser måste de tillåta trafik från den här IP-adressen att kunna svara på hälso avsökningar. Den här regeln samverkar inte med åtkomst till din Load Balancer-frontend. Om du inte använder Azure Load Balancer kan du åsidosätta den här regeln. Du kan lära dig mer om service märken [här](../virtual-network/service-tags-overview.md#available-service-tags).

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>Kan jag använda global VNet-peering med Basic Load Balancer?
Nej. Basic Load Balancer stöder inte global VNET-peering. Du kan använda en Standard Load Balancer i stället. Se artikeln [Uppgradera från Basic till standard](upgrade-basic-standard.md) för sömlös uppgradering.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>Hur kan jag identifiera den offentliga IP-adress som används av en virtuell Azure-dator?

Det finns många sätt att fastställa IP-adressen för den offentliga källan för en utgående anslutning. OpenDNS tillhandahåller en tjänst som kan visa den offentliga IP-adressen för den virtuella datorn.
Genom att använda nslookup-kommandot kan du skicka en DNS-fråga för namnet myip.opendns.com till OpenDNS-matcharen. Tjänsten returnerar käll-IP-adressen som användes för att skicka frågan. När du kör följande fråga från din virtuella dator, är svaret den offentliga IP-adress som används för den virtuella datorn:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>Kan jag lägga till en virtuell dator från samma tillgänglighets uppsättning till olika backend-pooler för en Load Balancer?
Nej, det är inte möjligt.

## <a name="what-is-the-maximum-data-throughput-that-can-be-achieved-via-an-azure-load-balancer"></a>Vad är det maximala data flödet som kan uppnås via en Azure Load Balancer?
Eftersom Azure LB är en direkt utjämning av nätverks belastning, bestäms data flödes begränsningar av den typ av virtuell dator som används i backend-poolen. Om du vill veta mer om annan information om nätverks data flöde, se [data flöde för virtuella datorer](../virtual-network/virtual-machine-network-throughput.md).

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>Hur fungerar anslutningar till Azure Storage i samma region?
Att ha utgående anslutningar via scenarierna ovan är inte nödvändigt för att ansluta till lagring i samma region som den virtuella datorn. Om du inte vill det använder du nätverks säkerhets grupper (NSG: er) enligt beskrivningen ovan. För anslutning till lagring i andra regioner krävs utgående anslutning. Observera att när du ansluter till lagring från en virtuell dator i samma region, kommer käll-IP-adressen i lagrings diagnostikloggar att vara en intern provideradress och inte den offentliga IP-adressen för den virtuella datorn. Om du vill begränsa åtkomsten till ditt lagrings konto till virtuella datorer i ett eller flera Virtual Network undernät i samma region, använder du [Virtual Network tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) och inte din offentliga IP-adress när du konfigurerar brand väggen för ditt lagrings konto. När tjänstens slut punkter har kon figurer ATS visas din Virtual Network privata IP-adress i dina lagrings diagnostikloggar och inte på den interna provideradress.

## <a name="does-azure-load-balancer-support-tlsssl-termination"></a>Stöder Azure Load Balancer TLS/SSL-terminering?
Nej, Azure Load Balancer har för närvarande inte stöd för uppsägning eftersom det är ett pass via en utjämning av nätverks belastning. Application Gateway kan vara en potentiell lösning om ditt program kräver detta.

## <a name="what-are-best-practises-with-respect-to-outbound-connectivity"></a>Vad är bäst vad gäller utgående anslutningar?
Standard Load Balancer och allmän standard-IP-adress ger funktioner och olika beteenden för utgående anslutning. De är inte samma som för grundläggande SKU: er. Om du vill använda utgående anslutningar när du arbetar med standard-SKU: er måste du uttryckligen definiera det antingen med offentliga IP-adresser eller offentliga standard Load Balancer. Detta inkluderar att skapa utgående anslutningar när du använder en intern Standard Load Balancer. Vi rekommenderar att du alltid använder utgående regler på en offentlig standard Load Balancer. Det innebär att när en intern Standard Load Balancer används, måste du vidta åtgärder för att skapa utgående anslutningar för de virtuella datorerna i backend-poolen om den utgående anslutningen önskas. I kontexten för utgående anslutning, en enda fristående virtuell dator, alla virtuella datorer i en tillgänglighets uppsättning, fungerar alla instanser i en VMSS som en grupp. Det innebär att om en enskild virtuell dator i en tillgänglighets uppsättning är associerad med en standard-SKU fungerar alla VM-instanser i den här tillgänglighets uppsättningen med samma regler som om de är associerade med standard-SKU: n, även om en enskild instans inte är direkt kopplad till den. Det här beteendet observeras även när det gäller en fristående virtuell dator med flera nätverkskort kopplade till en belastningsutjämnare. Om ett nätverkskort läggs till som fristående har det samma beteende. Läs noggrant igenom hela dokumentet för att förstå de övergripande begreppen, granska [standard Load Balancer](./load-balancer-overview.md) för skillnader mellan SKU: er och granska [utgående regler](load-balancer-outbound-connections.md#outboundrules).
Med hjälp av utgående regler kan du få en detaljerad kontroll över alla aspekter av utgående anslutningar.
 
## <a name="next-steps"></a>Nästa steg
Om din fråga inte visas ovan kan du skicka feedback om den här sidan med din fråga. Detta skapar ett GitHub-problem för produkt teamet för att se till att alla våra värdefulla kund frågor besvaras.
