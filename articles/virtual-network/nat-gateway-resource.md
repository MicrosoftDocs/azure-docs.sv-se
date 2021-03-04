---
title: Utforma virtuella nätverk med NAT-gateway-resurser
titleSuffix: Azure Virtual Network NAT
description: Lär dig hur du utformar virtuella nätverk med NAT gateway-resurser.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 5c70c575464d82f714022291406418cdd1ca0f8d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102094974"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Utforma virtuella nätverk med NAT-gateway-resurser

NAT-gateway-resurser ingår i [Virtual Network NAT](nat-overview.md) och tillhandahålla utgående Internet anslutning för ett eller flera undernät i ett virtuellt nätverk. Under nätet för de virtuella nätverks tillstånd som NAT-gatewayen ska användas för. NAT tillhandahåller käll Network Address Translation (SNAT) för ett undernät.  NAT-gateway-resurser anger vilka statiska IP-adresser som virtuella datorer använder när de skapar utgående flöden. Statiska IP-adresser kommer från offentliga IP-adressresurser (PIP), offentliga IP-prefix resurser eller både och. Om en resurs för offentliga IP-prefix används används alla IP-adresser för hela den offentliga IP-prefixet av en NAT-gateway-resurs. En NAT-gateway-resurs kan använda totalt upp till 16 statiska IP-adresser från båda.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network NAT för utgående till Internet">
</p>

*Bild: Virtual Network NAT för utgående till Internet*

## <a name="how-to-deploy-nat"></a>Så här distribuerar du NAT

Att konfigurera och använda NAT-gateway har avsiktligt gjort enkla:  

NAT-gateway-resurs:
- Skapa en regional eller zonindelade (zon isolerad) NAT-gateway-resurs,
- Tilldela IP-adresser,
- Om det behövs ändrar du timeout för TCP-inaktivitet (valfritt).  Granska [timers](#timers) <ins>innan</ins> du ändrar standardvärdet.

Virtuellt nätverk:
- Konfigurera undernät för virtuellt nätverk för att använda en NAT-gateway.

Användardefinierade vägar är inte nödvändiga.

## <a name="resource"></a>Resurs

Resursen är utformad för att vara enkel som du kan se från följande Azure Resource Manager exempel i ett mall-liknande format.  Det här mall-liknande-formatet visas här för att illustrera koncepten och strukturen.  Ändra exemplet för dina behov.  Det här dokumentet är inte avsett som en själv studie kurs.

I följande diagram visas skrivbara referenser mellan de olika Azure Resource Manager resurserna.  Pilen anger riktningen för referensen, från vilken den är skrivbar. Genomgång 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" alt="Figure depicts a NAT receiving traffic from internal subnets and directing it to a public IP and an IP prefix." width="256" title="Virtual Network NAT-objektmodellen">
</p>

*Bild: Virtual Network NAT-objektmodellen*

NAT rekommenderas för de flesta arbets belastningar om du inte har ett speciellt beroende på [poolbaserade Load Balancer utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md).  

Du kan migrera från vanliga scenarier för belastnings utjämning, inklusive [utgående regler](../load-balancer/load-balancer-outbound-connections.md#outboundrules), till NAT-gateway. För att migrera, flytta resurserna för offentliga IP-adresser och offentliga IP-adresser från belastningsutjämnare till NAT-gateway. Nya IP-adresser för NAT gateway krävs inte. Standard resurser för offentliga IP-adresser och resurser för offentliga IP-prefix kan återanvändas så länge som summan inte överstiger 16 IP-adresser. Planera för migrering med avbrott i tjänsten i åtanke under över gången.  Du kan minimera avbrottet genom att automatisera processen. Testa migreringen i en mellanlagrings miljö först.  Under över gången påverkas inte inkommande ursprungliga flöden.


Följande exempel är ett kodfragment från en Azure Resource Manager-mall.  Den här mallen distribuerar flera resurser, inklusive en NAT-gateway.  Mallen har följande parametrar i det här exemplet:

- **natgatewayname** -namnet på NAT-gatewayen.
- **plats** – Azure-region där resursen finns.
- **publicipname** – namnet på den utgående offentliga IP-adress som är ASSOCIERAD med NAT-gatewayen.
- **vnetname** – namnet på det virtuella nätverket.
- **subnetname** – namnet på det undernät som är associerat med NAT-gatewayen.

Det totala antalet IP-adresser som anges av alla IP-adresser och prefix får inte överskrida 16 IP-adresser totalt. Valfritt antal IP-adresser mellan 1 och 16 tillåts.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

När NAT-gateway-resursen har skapats kan den användas i ett eller flera undernät i ett virtuellt nätverk. Ange vilka undernät som använder denna NAT gateway-resurs. En NAT-gateway kan inte omfatta mer än ett virtuellt nätverk. Det är inte nödvändigt att tilldela samma NAT-gateway till alla undernät i ett virtuellt nätverk. Enskilda undernät kan konfigureras med olika NAT-gateway-resurser.

Scenarier som inte använder tillgänglighets zoner är regionala (ingen zon har angetts). Om du använder tillgänglighets zoner kan du ange en zon för att isolera NAT till en speciell zon. Zon-redundans stöds inte. Granska NAT- [tillgänglighets zoner](#availability-zones).

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

NAT-gatewayer definieras med en egenskap i ett undernät i ett virtuellt nätverk. Flöden som skapats av virtuella datorer i under nätet **subnetname** för virtuella nätverk **vnetname** använder NAT-gatewayen. Alla utgående anslutningar använder de IP-adresser som är kopplade till **natgatewayname** som käll-IP-adress.

Mer information om Azure Resource Manager-mallen som används i det här exemplet finns i:

- [Snabb start: skapa en NAT-gateway – Resource Manager-mall](quickstart-create-nat-gateway-template.md)
- [Virtual Network NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Design vägledning

Läs det här avsnittet för att bekanta dig med att tänka på när du utformar virtuella nätverk med NAT.  

1. [Kostnadsoptimering](#cost-optimization)
1. [Samexistens av inkommande och utgående](#coexistence-of-inbound-and-outbound)
2. [Hantera grundläggande resurser](#managing-basic-resources)
3. [Tillgänglighetszoner](#availability-zones)

### <a name="cost-optimization"></a>Kostnadsoptimering

[Tjänst slut punkter](virtual-network-service-endpoints-overview.md) och [privat länk](../private-link/private-link-overview.md) är alternativ för att överväga att optimera kostnader. NAT behövs inte för de här tjänsterna. Trafik som riktas mot tjänst slut punkter eller privat länk bearbetas inte av det virtuella nätverkets NAT.  

Tjänst slut punkter binder Azure-tjänsteresurser till ditt virtuella nätverk och styr åtkomsten till dina Azure-tjänst resurser. När du till exempel använder Azure Storage ska du använda en tjänst slut punkt för lagring för att undvika att data som bearbetas NAT debiteras. Tjänstens slut punkter är kostnads fria.

Privat länk exponerar Azure PaaS service (eller andra tjänster som är värdbaserade med privat länk) som en privat slut punkt i ett virtuellt nätverk.  Privat länk faktureras baserat på varaktighet och bearbetade data.

Utvärdera om någon av eller båda dessa metoder passar bra för ditt scenario och använd vid behov.

### <a name="coexistence-of-inbound-and-outbound"></a>Samexistens av inkommande och utgående

NAT-gatewayen är kompatibel med:

 - Standard Load Balancer
 - Offentlig standard-IP
 - Allmänt standard-IP-prefix

Börja med standard-SKU: er när du utvecklar en ny distribution.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network." width="256" title="Virtual Network NAT för utgående till Internet">
</p>

*Bild: Virtual Network NAT för utgående till Internet*

Det enda Internet-utgående scenariot som tillhandahålls av NAT-gateway kan utökas med inkommande från Internet-funktioner. Varje resurs är medveten om i vilken riktning ett flöde har sitt ursprung. I ett undernät med NAT-gateway ersätts alla utgående till Internet-scenarier av NAT-gatewayen. Inkommande från Internet scenarier tillhandahålls av respektive resurs.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT och virtuell dator med offentlig IP på instans nivå

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Virtual Network NAT och virtuell dator med offentlig IP på instans nivå">
</p>

*Bild: Virtual Network NAT och virtuell dator med offentlig IP på instans nivå*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | Virtuell dator med offentlig IP på instans nivå |
| Utgående | NAT Gateway |

Den virtuella datorn kommer att använda NAT-gateway för utgående trafik.  Inkommande ursprungligt kommer inte att påverkas.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT och virtuell dator med offentlig Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Virtual Network NAT och virtuell dator med offentlig Load Balancer">
</p>

*Bild: Virtual Network NAT och virtuell dator med offentlig Load Balancer*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | offentlig Load Balancer |
| Utgående | NAT Gateway |

Eventuell utgående konfiguration från en belastnings Utjämnings regel eller utgående regler ersätts av NAT-gatewayen.  Inkommande ursprungligt kommer inte att påverkas.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT och virtuell dator med offentlig IP på instans nivå och offentliga Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Virtual Network NAT och virtuell dator med offentlig IP på instans nivå och offentliga Load Balancer">
</p>

*Bild: Virtual Network NAT och virtuell dator med offentlig IP på instans nivå och offentliga Load Balancer*

| Riktning | Resurs |
|:---:|:---:|
| Inkommande | Virtuell dator med offentlig IP på instans nivå och offentlig Load Balancer |
| Utgående | NAT Gateway |

Eventuell utgående konfiguration från en belastnings Utjämnings regel eller utgående regler ersätts av NAT-gatewayen.  Den virtuella datorn kommer också att använda NAT-gateway för utgående trafik.  Inkommande ursprungligt kommer inte att påverkas.

### <a name="managing-basic-resources"></a>Hantera grundläggande resurser

Standard Load Balancer, offentlig IP och offentliga IP-prefix är kompatibla med NAT-gateway. NAT-gatewayer körs i omfånget för ett undernät. Den grundläggande SKU: n för dessa tjänster måste distribueras i ett undernät utan NAT-gateway. Denna separation gör att båda SKU-varianterna kan finnas i samma virtuella nätverk.

NAT-gatewayer har företräde framför utgående scenarier i under nätet. En grundläggande belastningsutjämnare eller offentlig IP-adress (och en hanterad tjänst som skapats med dem) kan inte justeras med rätt översättningar. NAT-gatewayen tar kontroll över utgående trafik till Internet trafik på ett undernät. Inkommande trafik till grundläggande belastningsutjämnare och offentlig IP-adress är inte tillgänglig. Inkommande trafik till en grundläggande belastningsutjämnare och en offentlig IP-adress som kon figurer ATS på en virtuell dator är inte tillgänglig.

### <a name="availability-zones"></a>Tillgänglighetszoner

#### <a name="zone-isolation-with-zonal-stacks"></a>Zon isolering med zonindelade Stacks

<p align="center">
  <img src="media/nat-overview/az-directions.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet." width="425" title="Virtual Network NAT med zon isolering, skapa flera "zonal stacks"">
</p>

*Bild: Virtual Network NAT med zon isolering, skapa flera "zonindelade Stacks"*

Även om du inte har tillgänglighets zoner är NAT elastiskt och kan överleva flera infrastruktur komponents problem.  Tillgänglighets zoner bygger på denna återhämtning med zon isolerings scenarier för NAT.

Virtuella nätverk och deras undernät är regionala konstruktioner.  Undernät är inte begränsade till en zon.

Ett zonindelade löfte om zon isolering finns när en virtuell dator instans med en NAT-gateway-resurs finns i samma zon som NAT-gateway-resursen och dess offentliga IP-adresser. Det mönster som du vill använda för zon isolering är att skapa en "zonindelade stack" per tillgänglighets zon.  Denna "zonindelade stack" består av virtuella dator instanser, NAT-gateway-resurser, offentliga IP-adresser och/eller prefix resurser i ett undernät som antas betjäna endast samma zon.   Kontroll Plans åtgärderna och data planet justeras sedan med och begränsas till den angivna zonen. 

Ett annat haveri i en zon än där ditt scenario finns förväntas vara utan påverkan på NAT. Utgående trafik från virtuella datorer i samma zon kan inte utföras på grund av zon isolering.  

#### <a name="integrating-inbound-endpoints"></a>Integrera inkommande slut punkter

Om ditt scenario kräver inkommande slut punkter har du två alternativ:

| Alternativ | Mönster | Exempel | Pro | CON |
|---|---|---|---|---|
| 81.1 | **Justera** inkommande slut punkter med respektive zonindelade- **stack** som du skapar för utgående trafik. | Skapa en standard Load Balancer med zonindelade-frontend. | Samma hälso modell och fel läge för inkommande och utgående. Enklare att hantera. | Enskilda IP-adresser per zon kan behöva maskeras med ett gemensamt DNS-namn. |
| (2) | **Överlappa** zonindelade-stackarna med en inkommande slut punkt i en **zon** . | Skapa en standard Load Balancer med Zone-redundant klient del. | Enskild IP-adress för inkommande slut punkt. | Varierande hälso modell och fel lägen för inkommande och utgående.  Mer komplext att hantera. |

>[!NOTE]
> En zon isolerad NAT gateway kräver att IP-adresser matchar zonen i NAT-gatewayen. NAT gateway-resurser med IP-adresser från en annan zon eller utan en zon är inte tillåtna.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>Utgående scenarier mellan zoner stöds inte

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" alt="Figure depicts three zonal stacks, each of which contains a NAT gateway and a subnet, with the connections between to of the gateways and their subnets broken." width="425" title="Virtual Network NAT är inte kompatibelt med zon Spanning Subnet">
</p>

*Bild: Virtual Network NAT är inte kompatibelt med zon Spanning Subnet*

Du kan inte uppnå ett zonindelade löfte med NAT-gatewayens resurser när de virtuella dator instanserna distribueras i flera zoner i samma undernät.   Även om det fanns flera zonindelade NAT-gatewayer anslutna till ett undernät vet inte den virtuella dator instansen vilken NAT-gateway-resurs som ska väljas.

Ett zonindelade Promise-does't finns när a) zonen i en virtuell dator instans och zonen i en zonindelade NAT-gateway inte är justerade eller b) en regional NAT-gateway-resurs används med zonindelade virtuella dator instanser.

Även om scenariot verkar fungera, är dess hälso modell och felläge odefinierat från en tillgänglighets zon i vyn. Överväg att gå med zonindelade-stackar eller alla regionala i stället.

>[!NOTE]
>Egenskapen zoner för en NAT-gateway-resurs är inte föränderligt.  Distribuera om NAT-gateway-resursen med den avsedda regionala eller zon inställningen.

>[!NOTE] 
>IP-adresser som själva inte är zoner – redundanta om ingen zon anges.  Klient delen för en [standard Load Balancer är zon-redundant](../load-balancer/load-balancer-standard-availability-zones.md) om en IP-adress inte har skapats i en speciell zon.  Detta gäller inte för NAT.  Endast regional eller zon isolering stöds.

## <a name="performance"></a>Prestanda

Varje NAT-gateway kan ge upp till 50 Gbit/s genom strömning. Du kan dela upp dina distributioner i flera undernät och tilldela varje undernät eller grupper med undernät en NAT-gateway för att skala ut.

Varje NAT-gateway har stöd för 64 000-flöden för TCP-respektive UDP per tilldelad utgående IP-adress.  Läs följande avsnitt om översättning av käll nätverks adresser (SNAT) för information och [fel söknings artikel](./troubleshoot-nat.md) för en detaljerad vägledning om problem lösning.

## <a name="source-network-address-translation"></a>Käll nätverks adress Översättning

Med käll Network Address Translation (SNAT) skrivs källan för ett flöde om till härstamma från en annan IP-adress.  Resurser för NAT-gateway använder en variant av SNAT som vanligt vis kallas port adress översättning (PAT). PAT skriver om käll-och käll porten. Med SNAT finns det ingen fast relation mellan antalet privata adresser och deras översatta offentliga adresser.  

### <a name="fundamentals"></a>Grunder

Nu ska vi titta på ett exempel på fyra flöden för att förklara det grundläggande konceptet.  NAT-gatewayen använder offentliga IP-65.52.1.1 och den virtuella datorn ansluter till 65.52.0.1.

| Flöden | Käll tupel | Mål tupel |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Dessa flöden kan se ut så här när PAT har ägt rum:

| Flöden | Käll tupel | SNAT'ed-käll tupel | Mål tupel | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

Målet kommer att se källan till flödet som 65.52.0.1 (SNAT-källans tupel) med den tilldelade porten som visas.  PAT som visas i tabellen ovan kallas även för port maskerad SNAT.  Flera privata källor är maskerade bakom en IP-adress och port.  

#### <a name="source-snat-port-reuse"></a>åter användning av källa (SNAT)

NAT-gatewayer autentiseringsuppsättningarna åter användnings käll port (SNAT).  Följande illustrerar det här konceptet som ett ytterligare flöde för den föregående uppsättningen flöden.  Den virtuella datorn i exemplet är ett flöde till 65.52.0.2.

| Flöden | Käll tupel | Mål tupel |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

En NAT-gateway kommer troligen att översätta flöde 4 till en port som även kan användas för andra mål.  Se [skalning](#scaling) för ytterligare en diskussion om hur du ändrar din IP-adress.

| Flöden | Käll tupel | SNAT'ed-käll tupel | Mål tupel | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Ta inte ett beroende på det speciella sätt som käll portarna tilldelas i exemplet ovan.  Föregående är en illustration av det grundläggande konceptet.

SNAT som tillhandahålls av NAT skiljer sig från [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) i flera olika delar.

### <a name="on-demand"></a>På begäran

NAT tillhandahåller SNAT-portar på begäran för nya utgående trafik flöden. Alla tillgängliga SNAT-portar i lagret används av en virtuell dator på undernät som kon figurer ATS med NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Virtual Network NAT på begäran för utgående SNAT">
</p>

*Bild: Virtual Network NAT på begäran för utgående SNAT*

Alla IP-konfigurationer för en virtuell dator kan skapa utgående flöden vid behov.  I förväg allokeras planeringen per instans, inklusive överetablering per instans, vilket inte krävs.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Skillnader i utfalls scenarier">
</p>

*Bild: skillnader i utfalls scenarier*

När en SNAT-port släpps, är den tillgänglig för användning av alla virtuella datorer på undernät som kon figurer ATS med NAT.  Med allokering på begäran kan dynamiska och Divergent arbets belastningar på undernät använda SNAT-portar som de behöver.  Så länge som det finns SNAT-port inventeringen kommer SNAT-flöden att lyckas. SNAT-portens frekventa fläckar drar nytta av den större inventeringen i stället. SNAT-portar är inte kvar för virtuella datorer som inte aktivt behöver dem.

### <a name="scaling"></a>Skalning

Skalning av NAT är i första hand en funktion i hanteringen av den delade, tillgängliga SNAT-port inventeringen. NAT behöver tillräckligt med SNAT-port inventering för förväntade högsta utgående flöden för alla undernät som är anslutna till en NAT-gateway-resurs.  Du kan använda offentliga IP-adressresurser, resurser för offentliga IP-prefix eller både och för att skapa SNAT-port inventering.  

>[!NOTE]
>Om du tilldelar en offentlig IP-prefixlängd används hela det offentliga IP-prefixet.  Du kan inte tilldela en offentlig IP-prefix resurs och sedan dela upp enskilda IP-adresser för att tilldela till andra resurser.  Om du vill tilldela enskilda IP-adresser från ett offentligt IP-prefix till flera resurser måste du skapa enskilda offentliga IP-adresser från resursen för offentliga IP-prefix och tilldela dem efter behov i stället för själva resursen för offentliga IP-prefix.

SNAT mappar privata adresser till en eller flera offentliga IP-adresser, och skriver om käll-och käll porten i processerna. En NAT-gateway-resurs använder 64 000 portar (SNAT portar) per konfigurerad offentlig IP-adress för den här översättningen. NAT-gatewayens resurser kan skala upp till 16 IP-adresser och 1 miljon SNAT-portar. Om en resurs för offentliga IP-prefix anges tillhandahåller varje IP-adress i prefixet SNAT-port inventering. Och om du lägger till fler offentliga IP-adresser ökar de tillgängliga Inventory SNAT-portarna. TCP och UDP är separata SNAT-port inventeringar och orelaterade.

NAT gateway-resurser autentiseringsuppsättningarna åter användnings källa (SNAT)-portar. Som design vägledning för skalnings syfte bör du förutsätta att varje flöde kräver en ny SNAT-port och skala det totala antalet tillgängliga IP-adresser för utgående trafik.  Du bör noggrant överväga vilken skala du utformar för och etablera IP-adresser i enlighet med detta.

SNAT-portar till olika destinationer kommer förmodligen att återanvändas när det är möjligt. Det går inte att utföra flöden och som metoder för att fastställa SNAT-portar.  

Se [viktigare för SNAT](#source-network-address-translation) till exempel.


### <a name="protocols"></a>Protokoll

NAT-gatewayens resurser interagerar med IP-och IP-transportnivån för UDP-och TCP-flöden och är oberoende till program nivå nytto laster.  Andra IP-protokoll stöds inte.

### <a name="timers"></a>Timers

>[!IMPORTANT]
>Lång inaktiv timer kan i onödan öka sannolikheten för att det går att frigöra SNAT. Längre än en timer som du anger, kommer den längre NAT att hållas på till SNAT-portar tills tids gränsen för inaktivitet är inaktiv. Om dina flöden är inaktiva, kommer de inte längre att kunna användas i onödan.  Flöden som Miss lyckas vid 2 timmar skulle ha misslyckats med standard 4 minuter. Att öka tids gränsen för inaktivitet är ett sista utväg-alternativ som bör användas sparsamt. Om ett flöde aldrig är inaktivt påverkas det inte av timern för inaktivitet.

Timeout för TCP-inaktivitet kan justeras från 4 minuter (standard) till 120 minuter (2 timmar) för alla flöden.  Dessutom kan du återställa inaktiv timer med trafik i flödet.  Ett rekommenderat mönster för att uppdatera långa inaktiva anslutningar och slut punkts Live-identifiering är TCP keepalive.  TCP keepalive-poster visas som dubbla ack-filer till slut punkterna, är låga kostnader och är osynliga för program lagret.

Följande timers används för Port versionen av SNAT:

| Timer | Värde |
|---|---|
| TCP RÄNTETRANS | 60 sekunder |
| TCP-VARJE | 10 sekunder |
| TCP halv öppen | 30 sekunder |

En SNAT-port är tillgänglig för åter användning till samma mål-IP-adress och mål Port efter 5 sekunder.

>[!NOTE] 
>Dessa timer-inställningar kan komma att ändras. Värdena används för att under lätta fel sökningen och du bör inte ta ett beroende på vissa timers just nu.

## <a name="limitations"></a>Begränsningar

- NAT är kompatibelt med standard-SKU offentlig IP, offentliga IP-prefix och belastnings Utjämnings resurser.   Grundläggande resurser (till exempel grundläggande belastningsutjämnare) och alla produkter som härletts från dem är inte kompatibla med NAT.  Grundläggande resurser måste placeras i ett undernät som inte har kon figurer ATS med NAT.
- IPv4-adress familjen stöds.  NAT interagerar inte med IPv6-adress familjen.  Det går inte att distribuera NAT i ett undernät med ett IPv6-prefix.
- NAT kan inte omfatta flera virtuella nätverk.
- IP-fragmentering stöds inte.

## <a name="suggestions"></a>Förslag

Vi vill veta hur vi kan förbättra tjänsten. Saknas en funktion? Gör ditt ärende för det vi ska skapa nästa på [UserVoice för NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [NAT för virtuella nätverk](nat-overview.md).
* Lär dig mer om [mått och aviseringar för NAT gateway-resurser](nat-metrics.md).
* Lär dig mer om att [Felsöka resurser för NAT-gateway](troubleshoot-nat.md).
