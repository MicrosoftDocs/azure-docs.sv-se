---
title: 'Interoperabilitet i Azure: analys av data planet'
description: Den här artikeln innehåller data planet analys av test konfigurationen som du kan använda för att analysera interoperabilitet mellan ExpressRoute, en plats-till-plats-VPN och virtuell nätverks-peering i Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4797fca5b4a756ca502147172e6f1590b9eacbe4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234179"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Interoperabilitet i Azure: analys av data planet

I den här artikeln beskrivs analys av [test inställningarna][Setup]för data planet. Du kan också granska [konfigurationen av test konfigurationen][Configuration] och [kontroll Plans analysen][Control-Analysis] av test konfigurationen.

Data Plans analys undersöker sökvägen som tas av paket som passerar från ett lokalt nätverk (LAN eller virtuellt nätverk) till ett annat inom en topologi. Data Sök vägen mellan två lokala nätverk är inte nödvändigt vis symmetrisk. I den här artikeln analyserar vi därför en vidarebefordrande sökväg från ett lokalt nätverk till ett annat nätverk som är skilt från den omvända sökvägen.

## <a name="data-path-from-the-hub-vnet"></a>Data Sök väg från hubbens VNet

### <a name="path-to-the-spoke-vnet"></a>Sökväg till eker VNet

Peering för virtuella nätverk (VNet) emulerar funktionen för nätverks bryggor mellan de två virtuella nätverk som är peer-kopplat. Traceroute-utdata från ett hubb-VNet till en virtuell dator i det virtuella eker-nätverket visas här:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

Följande bild visar vyn grafisk anslutning för hubbens VNet och eker VNet från perspektivet Azure Network Watcher:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Sökväg till grenens VNet

Traceroute-utdata från ett hubb-VNet till en virtuell dator i grenen VNet visas här:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

I den här traceroute är det första hoppet VPN-gatewayen i Azure VPN Gateway av hubbens VNet. Det andra hoppet är VPN-gatewayen för grenens VNet. IP-adressen för VPN-gatewayen för grenens VNet annonseras inte i hubbens VNet. Det tredje hoppet är den virtuella datorn i grenens VNet.

Följande bild visar vyn grafisk anslutning för hubbens VNet och grenens VNet från Network Watcher perspektiv:

![2][2]

För samma anslutning visar följande figur rutnätsvy i Network Watcher:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från ett hubb-VNet till en virtuell dator på den lokala platsen 1 visas här:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

I den här traceroute är det första hoppet Azure ExpressRoute Gateway-tunnelns slut punkt till en Microsoft Enterprise Edge-router (MSEE: N). De andra och tredje hoppen är kundens (CE) router och lokal plats 1 LAN-IP. Dessa IP-adresser annonseras inte i hubbens VNet. Det fjärde hoppet är den virtuella datorn på den lokala platsen 1.


### <a name="path-to-on-premises-location-2"></a>Sökväg till lokal plats 2

Traceroute-utdata från ett hubb-VNet till en virtuell dator på den lokala platsen 2 visas här:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

I den här traceroute är det första hoppet ExpressRoute Gateway-tunnelns slut punkt till en MSEE: N. Den andra och tredje hoppen är CE-routern och den lokala platsen 2 LAN IP-adresser. Dessa IP-adresser annonseras inte i hubbens VNet. Det fjärde hoppet är den virtuella datorn på den lokala platsen 2.

### <a name="path-to-the-remote-vnet"></a>Sökväg till fjärrnätverket

Traceroute-utdata från ett hubb-VNet till en virtuell dator i det virtuella fjärrnätverket visas här:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

I den här traceroute är det första hoppet ExpressRoute Gateway-tunnelns slut punkt till en MSEE: N. Det andra hoppet är Gateway-IP för fjärrnätverket. Det andra hoppets IP-intervall annonseras inte i hubbens VNet. Det tredje hoppet är den virtuella datorn på fjärrnätverket.

## <a name="data-path-from-the-spoke-vnet"></a>Data Sök väg från eker VNet

Det eker-VNet som delar nätverks visningen av hubbens VNet. Med hjälp av VNet-peering använder det virtuella eker-nätverket fjärran sluten Gateway-anslutningen till hubben för hubben som om den är direktansluten till eker VNet.

### <a name="path-to-the-hub-vnet"></a>Sökväg till hubbens VNet

Traceroute-utdata från det virtuella eker-nätverket till en virtuell dator i hubbens VNet visas här:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Sökväg till grenens VNet

Traceroute-utdata från det virtuella eker-nätverket till en virtuell dator i grenen VNet visas här:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

I den här traceroute är det första hoppet VPN-gatewayen för hubbens VNet. Det andra hoppet är VPN-gatewayen för grenens VNet. IP-adressen för VPN-gatewayen för grenens VNet annonseras inte inom hubben/ekraret VNet. Det tredje hoppet är den virtuella datorn i grenens VNet.

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från det virtuella eker-nätverket till en virtuell dator på den lokala platsen 1 visas här:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

I den här traceroute är det första hoppet Hub VNet: s ExpressRoute Gateway-tunnel slut punkt till en MSEE: N. Den andra och tredje hoppen är CE-routern och den lokala platsen 1 LAN-IP. Dessa IP-adresser annonseras inte i hubben/ekraret VNet. Det fjärde hoppet är den virtuella datorn på den lokala platsen 1.

### <a name="path-to-on-premises-location-2"></a>Sökväg till lokal plats 2

Traceroute-utdata från det virtuella eker-nätverket till en virtuell dator på den lokala platsen 2 visas här:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

I den här traceroute är det första hoppet Hub VNet: s ExpressRoute Gateway-tunnel slut punkt till en MSEE: N. Den andra och tredje hoppen är CE-routern och den lokala platsen 2 LAN IP-adresser. Dessa IP-adresser annonseras inte i hubben/eker-virtuella nätverk. Det fjärde hoppet är den virtuella datorn på den lokala platsen 2.

### <a name="path-to-the-remote-vnet"></a>Sökväg till fjärrnätverket

Traceroute-utdata från det virtuella eker-nätverket till en virtuell dator i det virtuella fjärrnätverket visas här:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

I den här traceroute är det första hoppet Hub VNet: s ExpressRoute Gateway-tunnel slut punkt till en MSEE: N. Det andra hoppet är Gateway-IP för fjärrnätverket. Det andra hoppets IP-intervall annonseras inte i hubben/ekraret VNet. Det tredje hoppet är den virtuella datorn på fjärrnätverket.

## <a name="data-path-from-the-branch-vnet"></a>Data Sök väg från gren VNet

### <a name="path-to-the-hub-vnet"></a>Sökväg till hubbens VNet

Traceroute-utdata från gren-VNet till en virtuell dator i hubbens VNet visas här:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

I den här traceroute är det första hoppet VPN-gatewayen för grenens VNet. Det andra hoppet är VPN-gatewayen för hubbens VNet. IP-adressen för VPN-gatewayen för hubbens VNet annonseras inte i fjärrnätverket. Det tredje hoppet är den virtuella datorn i hubbens VNet.

### <a name="path-to-the-spoke-vnet"></a>Sökväg till eker VNet

Traceroute-utdata från gren-VNet till en virtuell dator i eker VNet visas här:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

I den här traceroute är det första hoppet VPN-gatewayen för grenens VNet. Det andra hoppet är VPN-gatewayen för hubbens VNet. IP-adressen för VPN-gatewayen för hubbens VNet annonseras inte i fjärrnätverket. Det tredje hoppet är den virtuella datorn i eker VNet.

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från gren-VNet till en virtuell dator på den lokala platsen 1 visas här:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

I den här traceroute är det första hoppet VPN-gatewayen för grenens VNet. Det andra hoppet är VPN-gatewayen för hubbens VNet. IP-adressen för VPN-gatewayen för hubbens VNet annonseras inte i fjärrnätverket. Det tredje hoppet är VPN-tunnelns avslutnings plats på den primära CE-routern. Det fjärde hoppet är en intern IP-adress för den lokala platsen 1. Den här nätverks-IP-adressen annonseras inte utanför CE-routern. Det femte hoppet är den virtuella mål datorn på den lokala platsen 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Sökväg till lokal plats 2 och fjärr-VNet

Som vi diskuterat i kontroll Plans analysen har grenen VNet ingen synlighet för antingen lokal plats 2 eller fjärr-VNet enligt nätverks konfigurationen. Följande ping-resultat bekräftar: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\rb>ping 10.17.30.4

Pinging 10.17.30.4 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.17.30.4:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

## <a name="data-path-from-on-premises-location-1"></a>Data Sök väg från lokal plats 1

### <a name="path-to-the-hub-vnet"></a>Sökväg till hubbens VNet

Traceroute-utdata från den lokala platsen 1 till en virtuell dator i hubbens VNet visas här:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

I den här traceroute är de första två hoppen en del av det lokala nätverket. Det tredje hoppet är det primära MSEE: N-gränssnittet som passar för CE-routern. Det fjärde hoppet är ExpressRoute-gatewayen för hubbens VNet. IP-intervallet för ExpressRoute-gatewayen för hubbens VNet annonseras inte till det lokala nätverket. Det femte hoppet är den virtuella mål datorn.

Network Watcher tillhandahåller endast en Azure-inriktad vy. För ett lokalt perspektiv använder vi Azure Övervakare av nätverksprestanda. Övervakare av nätverksprestanda tillhandahåller agenter som du kan installera på servrar i nätverk utanför Azure för data Sök vägs analys.

Följande bild visar topologin för den lokala plats 1 VM-anslutningen till den virtuella datorn i hubbens VNet via ExpressRoute:

![4][4]

Som tidigare nämnts använder test installationen en plats-till-plats-VPN som säkerhets kopierings anslutning för ExpressRoute mellan den lokala platsen 1 och hubbens VNet. För att testa säkerhets kopian av säkerhets kopierings data, ska vi orsaka ett ExpressRoute länkfel mellan den lokala primära och den lokala platsen 1 primära CE-router och motsvarande MSEE: N. Om du vill inducera ett ExpressRoute-länkfel stänger du av det CE-gränssnitt som är MSEE: N:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

Följande bild visar topologin för den lokala plats 1 VM-anslutningen till den virtuella datorn i hubbens VNet via VPN-anslutning från plats till plats när ExpressRoute-anslutningen är avstängd:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Sökväg till eker VNet

Traceroute-utdata från den lokala platsen 1 till en virtuell dator i det virtuella eker-nätverket visas här:

Nu ska vi gå tillbaka till den primära ExpressRoute-anslutningen för att utföra data Sök vägs analysen mot eker VNet:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Ta upp den primära ExpressRoute 1-anslutningen för resten av data Sök vägs analysen.

### <a name="path-to-the-branch-vnet"></a>Sökväg till grenens VNet

Traceroute-utdata från lokal plats 1 till en virtuell dator i grenen VNet visas här:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>Sökväg till lokal plats 2

När vi diskuterar i [kontroll Plans analysen][Control-Analysis]har den lokala platsen 1 ingen synlighet för lokal plats 2 per nätverks konfigurationen. Följande ping-resultat bekräftar: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>Sökväg till fjärrnätverket

Traceroute-utdata från lokal plats 1 till en virtuell dator i det virtuella fjärrnätverket visas här:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Data Sök väg från lokal plats 2

### <a name="path-to-the-hub-vnet"></a>Sökväg till hubbens VNet

Traceroute-utdata från den lokala platsen 2 till en virtuell dator i hubbens VNet visas här:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Sökväg till eker VNet

Traceroute-utdata från den lokala platsen 2 till en virtuell dator i det virtuella eker-nätverket visas här:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Sökväg till gren-VNet, lokal plats 1 och fjärrnätverket

När vi diskuterar i [kontroll Plans analysen][Control-Analysis]har den lokala platsen 1 ingen insyn i grenens VNet, till lokal plats 1 eller till fjärrnätverket enligt nätverks konfigurationen. 

## <a name="data-path-from-the-remote-vnet"></a>Data Sök väg från fjärrnätverket

### <a name="path-to-the-hub-vnet"></a>Sökväg till hubbens VNet

Traceroute-utdata från fjärrnätverket till en virtuell dator i hubbens VNet visas här:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Sökväg till eker VNet

Traceroute-utdata från fjärrnätverket till en virtuell dator i det virtuella eker-nätverket visas här:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Sökväg till gren-VNet och lokal plats 2

När vi diskuterar i [kontroll Plans analysen][Control-Analysis]har fjärrnätverket ingen insyn i grenens VNet eller till lokal plats 2 per nätverks konfigurationen. 

### <a name="path-to-on-premises-location-1"></a>Sökväg till lokal plats 1

Traceroute-utdata från fjärrnätverket till en virtuell dator på den lokala platsen 1 visas här:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute och plats-till-plats VPN-anslutning i tandem

###  <a name="site-to-site-vpn-over-expressroute"></a>Plats-till-plats-VPN via ExpressRoute

Du kan konfigurera en plats-till-plats-VPN genom att använda ExpressRoute Microsoft-peering för att utbyta data mellan ditt lokala nätverk och ditt Azure-virtuella nätverk. Med den här konfigurationen kan du utbyta data med konfidentialitet, äkthet och integritet. Data utbytet är också en anti-omuppspelning. Mer information om hur du konfigurerar en plats-till-plats IPsec-VPN i tunnel läge med ExpressRoute Microsoft-peering finns i [plats-till-plats-VPN över ExpressRoute Microsoft-peering][S2S-Over-ExR]. 

Den primära begränsningen för att konfigurera en plats-till-plats-VPN som använder Microsoft-peering är data flöde. Data flödet över IPsec-tunneln begränsas av VPN-gatewayens kapacitet. Data flödet för VPN-gatewayen är lägre än ExpressRoute-dataflödet. I det här scenariot kan du använda IPsec-tunneln för hög säker trafik och använda privat peering för all annan trafik för att optimera ExpressRoute bandbredds användning.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Plats-till-plats-VPN som en säker redundans för ExpressRoute

ExpressRoute fungerar som ett redundant krets par för att säkerställa hög tillgänglighet. Du kan konfigurera Geo-redundant ExpressRoute-anslutning i olika Azure-regioner. Som du ser i vår test konfiguration, i en Azure-region, kan du också använda en plats-till-plats-VPN för att skapa en sökväg för redundans för ExpressRoute-anslutningen. När samma prefix annonseras över både ExpressRoute och en plats-till-plats-VPN prioriterar Azure ExpressRoute. För att undvika asymmetrisk routning mellan ExpressRoute och plats-till-plats-VPN, bör den lokala nätverks konfigurationen också vara kolv med ExpressRoute-anslutning innan den använder VPN-anslutning från plats till plats.

Mer information om hur du konfigurerar sambefintliga anslutningar för ExpressRoute och en plats-till-plats-VPN finns i [ExpressRoute och plats-till-plats-samexistens][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Utöka backend-anslutningen till ekrar virtuella nätverk och filial platser

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Eker VNet-anslutning med hjälp av VNet-peering

NAV-och eker VNet-arkitekturen används ofta. Hubben är ett VNet i Azure som fungerar som en central punkt för anslutningen mellan ekrarnas virtuella nätverk och det lokala nätverket. Ekrarna är virtuella nätverk som peer-kopplas med hubben och som du kan använda för att isolera arbets belastningar. Trafik flöden mellan det lokala data centret och hubben via en ExpressRoute-eller VPN-anslutning. Mer information om arkitekturen finns i [implementera en nätverkstopologi för NAV-ekrar i Azure][Hub-n-Spoke].

I VNet-peering inom en region kan eker-virtuella nätverk använda hubb-gatewayer (både VPN-och ExpressRoute-gatewayer) för att kommunicera med fjärrnätverk.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Gren-VNet-anslutning med plats-till-plats-VPN

Du kanske vill använda Branch virtuella nätverk, som finns i olika regioner och lokala nätverk för att kommunicera med varandra via ett nav-VNet. Den interna Azure-lösningen för den här konfigurationen är plats-till-plats-VPN-anslutning med hjälp av en VPN. Ett alternativ är att använda en virtuell nätverks installation (NVA) för routning i hubben.

Mer information finns i [Vad är VPN gateway?][VPN] och [distribuera en hög tillgänglig NVA][Deploy-NVA].


## <a name="next-steps"></a>Nästa steg

Se [vanliga frågor och svar om ExpressRoute][ExR-FAQ] för att:
-   Lär dig hur många ExpressRoute-kretsar du kan ansluta till en ExpressRoute-Gateway.
-   Lär dig hur många ExpressRoute-gatewayer du kan ansluta till en ExpressRoute-krets.
-   Lär dig mer om andra skalnings gränser för ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Network Watcher visning av anslutningar från ett hubb-VNet till ett eker VNet"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Network Watcher visning av anslutningar från ett hubb-VNet till ett förgrenings-VNet"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Network Watcher rutnätsvy över anslutningar från ett hubb-VNet till ett förgrenings-VNet"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Övervakare av nätverksprestanda visning av anslutningen från plats 1 VM till hubbens VNet via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Övervakare av nätverksprestanda visning av anslutningen från plats 1 VM till hubbens VNet via en VPN-anslutning från plats till plats"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md