---
title: Rikt linjer för Azure NetApp Files nätverks planering | Microsoft Docs
description: Beskriver rikt linjer som kan hjälpa dig att utforma en effektiv nätverks arkitektur med hjälp av Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: 96d8ba058a33d408ec2ee2a1adfba9011f393da9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184492"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Riktlinjer för Azure NetApp Files-nätverksplanering

Planering av nätverks arkitektur är ett viktigt element i att utforma alla program infrastrukturer. Den här artikeln hjälper dig att utforma en effektiv nätverks arkitektur för dina arbets belastningar för att dra nytta av de omfattande funktionerna i Azure NetApp Files.

Azure NetApp Files volymer är utformade för att ingå i ett särskilt syfte under nät som kallas för ett [delegerat undernät](../virtual-network/virtual-network-manage-subnet.md) i Azure Virtual Network. Därför kan du komma åt volymerna direkt från ditt VNet, från peer-virtuella nätverk i samma region eller från lokala platser över en Virtual Network Gateway (ExpressRoute eller VPN Gateway) om det behövs. Under nätet är dedikerat för Azure NetApp Files och det finns ingen anslutning till andra Azure-tjänster eller Internet.

## <a name="considerations"></a>Överväganden  

Du bör förstå några saker som du bör tänka på när du planerar för Azure NetApp Files nätverk.

### <a name="constraints"></a>Villkor

Funktionerna nedan stöds för närvarande inte för Azure NetApp Files: 

* Nätverks säkerhets grupper (NSG: er) som tillämpas på det delegerade under nätet
* Användardefinierade vägar (UDR) som tillämpas på det delegerade under nätet
* Azure-principer (till exempel anpassade namngivnings principer) i Azure NetApp Files-gränssnittet
* Belastnings utjämning för Azure NetApp Files trafik
* Azure Virtual WAN 
* Zone-redundanta Virtual Network gatewayer (Gateway-SKU: er med AZ) 
* Aktiva/aktiva Virtual Network GWs 
* VNet med dubbla stackar (IPv4 och IPv6)

Följande nätverks begränsningar gäller för Azure NetApp Files:

* Antalet IP-adresser som används i ett VNet med Azure NetApp Files (inklusive peer-virtuella nätverk) får inte överskrida 1000. Vi arbetar för att öka den här gränsen för att uppfylla behoven för kund skalning. 
* Endast ett undernät kan delegeras till Azure NetApp Files i varje Azure Virtual Network (VNet).


### <a name="supported-network-topologies"></a>Nätverkstopologier som stöds

I följande tabell beskrivs de nätverkstopologier som stöds av Azure NetApp Files.  Den beskriver också lösningarna för topologier som inte stöds. 

|    Topologier    |    Stöds    |     Lösning    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Anslutning till volym i ett lokalt virtuellt nätverk    |    Ja    |         |
|    Anslutning till volym i ett peer-kopplat VNet (samma region)    |    Ja    |         |
|    Anslutning till volym i ett peer-kopplat VNet (mellan regioner eller global peering)    |    Inga    |    Inget    |
|    Anslutning till en volym över ExpressRoute-Gateway    |    Ja    |         |
|    Anslutning från lokal plats till en volym i ett eker VNet över ExpressRoute-gateway och VNet-peering med Gateway-överföring    |    Ja    |        |
|    Anslutning från lokal plats till en volym i en eker VNet över VPN-gateway    |    Ja    |         |
|    Anslutning från lokal plats till en volym i ett eker VNet via VPN-gateway och VNet-peering med Gateway-överföring    |    Ja    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Virtuellt nätverk för Azure NetApp Files volymer

Det här avsnittet beskriver begrepp som hjälper dig med planeringen av virtuella nätverk.

### <a name="azure-virtual-networks"></a>Virtuella Azure-nätverk

Innan du konfigurerar en Azure NetApp Files volym måste du skapa ett virtuellt Azure-nätverk (VNet) eller använda ett som redan finns i din prenumeration. VNet definierar volymens nätverks gränser.  Mer information om hur du skapar virtuella nätverk finns i [Azure Virtual Network-dokumentationen](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Undernät

Undernät segmenterar det virtuella nätverket i separata adress utrymmen som kan användas av Azure-resurserna i dem.  Azure NetApp Files volymer finns i ett särskilt syfte undernät som kallas ett [delegerat undernät](../virtual-network/virtual-network-manage-subnet.md). 

Under näts delegering ger explicita behörigheter till tjänsten Azure NetApp Files för att skapa tjänstespecifika resurser i under nätet.  En unik identifierare används i distributionen av tjänsten. I det här fallet skapas ett nätverks gränssnitt för att möjliggöra anslutning till Azure NetApp Files.

Om du använder ett nytt virtuellt nätverk kan du skapa ett undernät och delegera under nätet till Azure NetApp Files genom att följa instruktionerna i [delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md). Du kan också delegera ett befintligt tomt undernät som inte redan har delegerats till andra tjänster.

Om VNet är peer-kopplat med ett annat VNet kan du inte expandera det virtuella nätverkets adress utrymme. Därför måste det nya delegerade under nätet skapas i VNet-adressutrymmet. Om du behöver utöka adress utrymmet måste du ta bort VNet-peering innan du expanderar adress utrymmet.

### <a name="udrs-and-nsgs"></a>UDR och NSG: er

Användardefinierade vägar (UDR) och nätverks säkerhets grupper (NSG: er) stöds inte i delegerade undernät för Azure NetApp Files. Du kan dock använda UDR och NSG: er till andra undernät, även inom samma VNet som under nätet delegerat till Azure NetApp Files.

* UDR definiera sedan trafik flöden från de andra under näten till det Azure NetApp Files delegerade under nätet. Detta hjälper till att säkerställa att detta justeras mot trafikflöde från Azure NetApp Files till de andra under näten med hjälp av system vägar.  
* NSG: er antingen tillåta eller neka trafiken till och från det Azure NetApp Files delegerade under nätet. 

## <a name="azure-native-environments"></a>Inbyggda Azure-miljöer

Följande diagram illustrerar en Azure-enhetlig miljö:

![Azure-inbyggd nätverks miljö](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Lokalt virtuellt nätverk

Ett grundläggande scenario är att skapa eller ansluta till en Azure NetApp Files volym från en virtuell dator (VM) i samma VNet. För VNet 2 i diagrammet ovan skapas volym 1 i ett delegerat undernät och kan monteras på VM 1 i standard under nätet.

### <a name="vnet-peering"></a>VNet-peering

Om du har ytterligare virtuella nätverk i samma region som behöver åtkomst till var and ras resurser, kan virtuella nätverk anslutas med VNet- [peering](../virtual-network/virtual-network-peering-overview.md) för att möjliggöra säker anslutning via Azure-infrastrukturen. 

Betrakta VNet 2 och VNet 3 i diagrammet ovan. Om VM 1 behöver ansluta till den virtuella datorn 2 eller volym 2, eller om VM 2 måste ansluta till den virtuella datorn 1 eller volym 1, måste du aktivera VNet-peering mellan VNet 2 och VNet 3. 

Tänk också på ett scenario där VNet 1 peer-kopplas med VNet 2 och VNet 2 peer-kopplas med VNet 3 i samma region. Resurserna från VNet 1 kan ansluta till resurser i VNet 2, men det går inte att ansluta till resurser i VNet 3, om inte VNet 1 och VNet 3 är peer-kopplade. 

I diagrammet ovan, även om VM 3 kan ansluta till volym 1, kan inte VM 4 ansluta till volym 2.  Orsaken till detta är att eker-virtuella nätverk inte är peer-kopplat och att _transit routning inte stöds över VNet-peering_.

## <a name="hybrid-environments"></a>Hybrid miljöer

Följande diagram illustrerar en hybrid miljö: 

![Hybrid nätverks miljö](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

I hybrid scenariot behöver program från lokala data Center åtkomst till resurserna i Azure.  Detta är fallet om du vill utöka ditt data Center till Azure, eller om du vill använda Azure Native Services eller för haveri beredskap. Se [VPN gateway planerings alternativ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) för information om hur du ansluter flera resurser lokalt till resurser i Azure via en plats-till-plats-VPN eller en ExpressRoute.

I en topologi med hybrid nav – ekrar fungerar Hub VNet i Azure som en central punkt för anslutningen till ditt lokala nätverk. Ekrarna är virtuella nätverk som peer-kopplas med hubben och de kan användas för att isolera arbets belastningar.

Beroende på konfigurationen kan du ansluta lokala resurser till resurser i hubben och ekrarna.

I topologin som illustreras ovan är det lokala nätverket anslutet till ett hubb-VNet i Azure och det finns 2 ekrar virtuella nätverk i samma region som är peer-kopplad med hubbens VNet.  I det här scenariot är anslutnings alternativen som stöds för Azure NetApp Files volymer följande:

* Lokala resurser VM 1 och VM 2 kan ansluta till volym 1 i hubben över en plats-till-plats-VPN-eller ExpressRoute-krets. 
* Lokala resurser VM 1 och VM 2 kan ansluta till volym 2 eller volym 3 över en plats-till-plats-VPN och regional VNet-peering.
* VM 3 i hubbens VNet kan ansluta till volym 2 i eker VNet 1 och volym 3 i eker VNet 2.
* VM 4 från eker VNet 1 och VM 5 från eker VNet 2 kan ansluta till volym 1 i hubbens VNet.
* VM 4 i eker VNet 1 kan inte ansluta till volym 3 i eker VNet 2. VM 5 i eker-VNet2 kan heller inte ansluta till volym 2 i eker VNet 1. Detta beror på att eker-virtuella nätverk inte är peer-och _överförings vägar inte stöds över VNet-peering_.
* I ovanstående arkitektur går det inte att förlora anslutningen till ANF-volymen från lokal som ansluter via gatewayen i hubben, om det också finns en gateway i det virtuella nätverket i ekrar. Enligt design skulle prioriteten ges till gatewayen i eker VNet och så att endast datorer som ansluter via den gatewayen kan ansluta till ANF-volymen.

## <a name="next-steps"></a>Nästa steg

[Delegera ett undernät till Azure NetApp Files](azure-netapp-files-delegate-subnet.md)