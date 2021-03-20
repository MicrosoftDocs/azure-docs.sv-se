---
title: Använda S2S VPN som en säkerhets kopia för Azure ExpressRoute Private-peering | Microsoft Docs
description: Den här sidan innehåller arkitektur rekommendationer för att säkerhetskopiera Azure-ExpressRoute privata peering med S2S VPN.
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207928"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>Använda S2S VPN som säkerhets kopiering för privat ExpressRoute-peering

I artikeln [utformning för haveri beredskap med ExpressRoute privat peering][DR-PP], diskuterade vi behovet av en säkerhets kopierings lösning för en ExpressRoute privat peering-anslutning och hur du använder geo-redundanta ExpressRoute-kretsar för ändamålet. I den här artikeln ska vi fundera över hur du ska utnyttja och underhålla VPN för plats-till-plats (S2S) som en säkerhets kopia för privat ExpressRoute-peering. 

Till skillnad från geo-redundanta ExpressRoute-kretsar kan du bara använda ExpressRoute-VPN för haveri beredskap i aktivt-passivt läge. En stor utmaning med att använda en säkerhets kopierings nätverks anslutning i passivt läge är att den passiva anslutningen ofta kraschar vid den primära anslutningen. Den vanliga orsaken till att den passiva anslutningen Miss lyckas är inget aktivt underhåll. I den här artikeln ska vi därför fokusera på hur du verifierar och aktivt upprätthåller S2S VPN-anslutning som säkerhetskopierar en privat ExpressRoute-peering.

>[!NOTE] 
>När en specifik väg annonseras via både ExpressRoute och VPN skulle Azure föredra routning över ExpressRoute.  
>

I den här artikeln får du se hur du kan kontrol lera anslutningen både från Azure-perspektivet och nätverkets Edge-perspektiv på kund sidan. Möjligheten att validera från båda parter hjälper dig oavsett om du hanterar de nätverks enheter på kund sidan som peer-kopplas med Microsoft-nätverksmappar. 

## <a name="example-topology"></a>Exempel sto pol Ogin

I vår installation har vi ett lokalt nätverk som är anslutet till ett virtuellt nätverk för Azure Hub via både en ExpressRoute-krets och en S2S VPN-anslutning. Azure Hub VNet är i sin tur peer-kopplat till ett eker-VNet, som visas i diagrammet nedan:

![1][1]

I installationen avslutas ExpressRoute-kretsen på ett par "Customer Edge"-routrar (CE) på den lokala platsen. Det lokala LAN-nätverket är anslutet till CE-routrarna via ett par brand väggar som fungerar i ledare-följar läge. S2S VPN avslutas direkt på brand väggarna.

I följande tabell visas de viktigaste IP-prefixen för topologin:

| **Entitet** | **Prefix** |
| --- | --- |
| Lokalt lokalt nätverk | 10.1.11.0/25 |
| Azure Hub VNet | 10.17.11.0/25 |
| Azure eker VNet | 10.17.11.128/26 |
| Lokal test Server | 10.1.11.10 |
| Virtuellt nätverk för eker-test VM | 10.17.11.132 |
| ExpressRoute primär anslutning P2P-undernät | 192.168.11.16/30 |
| ExpressRoute sekundär anslutning P2P-undernät | 192.168.11.20/30 |
| Primär BGP peer-IP för VPN-gateway | 10.17.11.76 |
| IP-gateway sekundär IP för BGP-peer | 10.17.11.77 |
| Lokal brand vägg VPN BGP peer IP | 192.168.11.88 |
| Primär CE-router i/f mot brand Väggs-IP | 192.168.11.0/31 |
| Brand vägg i/f mot primär CE-routerns IP-adress | 192.168.11.1/31 |
| Sekundärt CE-router i/f mot brand Väggs-IP | 192.168.11.2/31 |
| Brand vägg i/f till sekundärt CE-router-IP | 192.168.11.3/31 |


I följande tabell visas ASN: er i topologin:

| **Autonomt system** | **ASN** |
| --- | --- |
| Lokalt | 65020 |
| Microsoft Enterprise Edge | 12076 |
| Virtual Network GW (ExR) | 65515 |
| Virtual Network GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>Hög tillgänglighet utan asymmetriskhet

### <a name="configuring-for-high-availability"></a>Konfigurera för hög tillgänglighet

[Konfigurera ExpressRoute-och plats-till-plats-sambefintliga anslutningar][Conf-CoExist] beskriver hur du konfigurerar den Sambefintliga ExpressRoute-kretsen och S2S VPN-anslutningar. Som vi har diskuterat i [design för hög tillgänglighet med ExpressRoute][HA], för att förbättra ExpressRoute hög tillgänglighet, upprätthålls nätverks redundans (förhindrar en enskild felpunkt) hela vägen upp till slut punkterna. Både de primära och sekundära anslutningarna i ExpressRoute-kretsarna är konfigurerade för att köras i aktivt läge genom att annonsera de lokala prefixen på samma sätt genom båda anslutningarna. 

Den lokala väg annonsen för den primära CE-routern via den primära anslutningen av ExpressRoute-kretsen visas nedan (Junos-kommandon):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

Den lokala väg annonsen för den sekundära CE-routern via den sekundära anslutningen av ExpressRoute-kretsen visas nedan (Junos-kommandon):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

För att förbättra den höga tillgängligheten för säkerhets kopierings anslutningen, konfigureras S2S VPN också i läget aktiv-aktiv. Konfigurationen av Azure VPN-gatewayen visas nedan. Observera som en del av VPN-konfigurationen VPN-IP-adresserna för BGP-peer för gatewayen--10.17.11.76 och 10.17.11.77 – visas också.

![2][2]

Den lokala vägen annonseras av brand väggarna till de primära och sekundära BGP-peer-datorerna i VPN-gatewayen. Väg annonserna visas nedan (Junos):

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>Att konfigurera S2S VPN i aktivt-aktivt läge ger inte bara hög tillgänglighet till din nätverks anslutning för haveri beredskap, men ger också högre genomflöde till säkerhets kopierings anslutningen. Med andra ord rekommenderar vi att du konfigurerar S2S VPN i aktivt-aktivt läge eftersom det tvingar fram att skapa flera underliggande tunnlar.
>

### <a name="configuring-for-symmetric-traffic-flow"></a>Konfigurera för symmetriskt trafikflöde

Vi noterade att när en specifik väg annonseras via både ExpressRoute och S2S VPN, skulle Azure föredra ExpressRoute-sökvägen. Om du vill tvinga Azure att föredra VPN-sökväg för S2S över befintliga ExpressRoute, måste du annonsera mer om mer information (längre prefix med större nätmask) via VPN-anslutningen. Syftet med detta är att endast använda VPN-anslutningar som säkerhets kopiering. Det innebär att standard beteendet för val av sökvägar i Azure är online med vårt mål. 

Det är vårt ansvar att se till att trafiken till Azure från lokala platser också föredrar ExpressRoute-sökväg via S2S VPN. Den lokala standard inställningen för CE-routrarna och brand väggarna i vår lokala installation är 100. Genom att konfigurera den lokala inställningen för de vägar som tas emot via ExpressRoute privata peering som är större än 100 (säg 150), kan vi göra trafiken till Azure föredra ExpressRoute-kretsen i stabilt tillstånd.

BGP-konfigurationen för den primära CE-router som avslutar den primära anslutningen av ExpressRoute-kretsen visas nedan. Observera att värdet för den lokala inställningen för de vägar som annonseras över iBGP-sessionen är konfigurerad att vara 150. På samma sätt måste vi se till att den lokala inställningen för den sekundära CE-routern som avslutar den sekundära anslutningen av ExpressRoute-kretsen också är 150.

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

Routningstabellen i de lokala brand väggarna bekräftar (visas nedan) för den lokala trafik som är avsedd för Azure den önskade sökvägen är över ExpressRoute i stabilt tillstånd.

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

I väg tabellen ovan för hubben och eker VNet-vägarna--10.17.11.0/25 och 10.17.11.128/26--ser vi att ExpressRoute-kretsen föredras över VPN-anslutningar. 192.168.11.0 och 192.168.11.2 är IP-adresser i brand Väggs gränssnittet mot CE-routrar.

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>Validering av Route Exchange över S2S VPN

Tidigare i den här artikeln verifierade vi lokal väg annonsering om brand väggarna till de primära och sekundära BGP-peer-datorerna i VPN-gatewayen. Vi ska också bekräfta Azure-vägar som tas emot av brand väggarna från de primära och sekundära BGP-peer-datorerna i VPN-gatewayen.

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

På samma sätt kontrollerar vi om det finns prefix för lokala nätverks vägar som tas emot av Azure VPN-gatewayen. 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

Som det setts ovan har VPN-gatewayen tagit emot båda av de primära och sekundära BGP-peer-datorerna i VPN-gatewayen. Den har också insyn i de vägar som tas emot via primära och sekundära ExpressRoute-anslutningar (de med anpassningsprefix som-Path-med 12076). För att bekräfta de vägar som tas emot via VPN-anslutningar måste vi känna till anslutningarnas lokala BGP-peer IP-adresser. I vår installation under överväganden är det 192.168.11.88 och vi ser de vägar som tas emot från den.

Nu ska vi kontrol lera de vägar som annonseras av Azure VPN-gatewayen till den lokala brand väggens BGP-peer (192.168.11.88).

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

Det gick inte att se Route-byten indikerar anslutnings problem. Se [fel sökning: en Azure plats-till-plats-VPN-anslutning kan inte anslutas och slutar fungera][VPN Troubleshoot] för hjälp med fel sökning av VPN-anslutningen.

## <a name="testing-failover"></a>Testa redundans

Nu när vi har bekräftat lyckade väg utbyten via VPN-anslutningen (kontroll planet) är vi inställda att byta trafik (data planet) från ExpressRoute-anslutningen till VPN-anslutningen. 

>[!NOTE] 
>I produktions miljöer måste testning av redundans utföras under schemalagt nätverks underhålls arbete – fönster som kan vara avbrott i tjänsten.
>

Innan du utför trafik växeln ska vi spåra den aktuella sökvägen i vår konfiguration från den lokala test servern till den virtuella test datorn i eker VNet.

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

De primära och sekundära ExpressRoute punkt-till-punkt-undernät i vår konfiguration är respektive 192.168.11.16/30 och 192.168.11.20/30. I steg 3 i ovanstående spårnings väg ser vi att vi håller på att träffa 192.168.11.18, som är gränssnitts-IP för den primära MSEE: N. Förekomst av MSEE: N-gränssnitt bekräftar att vår nuvarande sökväg är över ExpressRoute.

Som rapporteras i [ExpressRoute kretsar][RST]använder vi följande PowerShell-kommandon för att inaktivera både den primära och sekundära peeringen av ExpressRoute-kretsen.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Växlings tiden för växling vid fel beror på BGP-konvergens tiden. Växling vid fel i vår installation tar några sekunder (mindre än 10). Efter växeln visar upprepningen av traceroute följande sökväg:

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

Traceroute-resultatet bekräftar att säkerhets kopierings anslutningen via S2S VPN är aktiv och kan tillhandahålla tjänst kontinuitet om både den primära och sekundära ExpressRoute-anslutningarna har misslyckats. Om du vill slutföra testningen av redundans aktiverar du ExpressRoute-anslutningarna igen och normaliserar trafikflöde med följande kommandon.

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Om du vill bekräfta att trafiken växlas tillbaka till ExpressRoute upprepar du traceroute och kontrollerar att den går via ExpressRoute privata peering.

## <a name="next-steps"></a>Nästa steg

ExpressRoute har utformats för hög tillgänglighet utan någon enskild felpunkt i Microsoft-nätverket. En ExpressRoute-krets är fortfarande begränsad till en enda geografisk region och en tjänst leverantör. S2S VPN kan vara en felfri passiv säkerhets kopierings lösning för katastrof återställning till en ExpressRoute-krets. För en pålitlig passiv säkerhets kopierings lösning är det regelbundet underhåll av den passiva konfigurationen och den periodiska verifieringen som anslutningen är viktig. Det är viktigt att inte låta VPN-konfigurationen bli inaktuell och regelbundet (varje kvartal) Upprepa stegen för validering och redundans som beskrivs i den här artikeln under underhålls perioden.

Information om hur du aktiverar övervakning och aviseringar utifrån VPN Gateway-mått finns i [Konfigurera aviseringar på VPN Gateway mått][VPN-alerts].

[Konfigurera BFD över ExpressRoute][BFD]för att påskynda BGP-konvergensen efter ett ExpressRoute-haveri.

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "topologi som beaktas"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "Konfiguration av VPN GW"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md