---
title: 'Azure-ExpressRoute: kontrol lera anslutningen – fel söknings guide'
description: Den här sidan innehåller instruktioner om fel sökning och validering av slut punkt till slut punkt för en ExpressRoute-krets.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 10/31/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 6213e17f1232bd505ba1edf185a851bf669b9809
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553438"
---
# <a name="verifying-expressroute-connectivity"></a>Verifiera ExpressRoute-anslutning
Den här artikeln hjälper dig att kontrol lera och felsöka ExpressRoute-anslutningen. ExpressRoute utökar ett lokalt nätverk till Microsoft-molnet via en privat anslutning som ofta fören klar av en anslutnings leverantör. ExpressRoute-anslutningen omfattar vanligt vis tre olika nätverks zoner, enligt följande:

-   Kundnätverk
-   Leverantörs nätverk
-   Microsoft-datacenter

> [!NOTE]
> I ExpressRoute Direct Connectivity-modellen (erbjuds till 10/100 Gbit/s bandbredd) kan kunderna ansluta direkt till porten Microsoft Enterprise Edge (MSEE: N) routers. Därför finns det bara kund-och Microsofts nätverks zoner i den direkta anslutnings modellen.
>


Syftet med det här dokumentet är att hjälpa användarna att identifiera om och var det finns ett anslutnings problem. För att hjälpa till att söka efter support från rätt team för att lösa ett problem. Om Microsoft Support krävs för att lösa ett problem öppnar du ett support ärende med [Microsoft Support][Support].

> [!IMPORTANT]
> Det här dokumentet är avsett för att hjälpa till att diagnostisera och åtgärda enkla problem. Den är inte avsedd att ersätta Microsoft-supporten. Öppna ett support ärende med [Microsoft Support][Support] om du inte kan lösa problemet med hjälp av anvisningarna.
>
>

## <a name="overview"></a>Översikt
Följande diagram visar den logiska anslutningen för ett kund nätverk till Microsoft-nätverk med ExpressRoute.
[![81.1]][1]

I föregående diagram indikerar talen viktiga nätverks punkter. Dessa nätverks platser refereras i den här artikeln vid tidpunkten med deras associerade nummer. Beroende på ExpressRoute-anslutnings modell – molnets Exchange-samplacering, punkt-till-punkt-Ethernet-anslutning eller alla-till-alla (IPVPN)--nätverks punkterna 3 och 4 kan vara växlar (skikt 2 enheter) eller routrar (Layer 3-enheter). Det finns inga nätverks punkter 3 och 4 i den direkta anslutnings modellen. i stället är CEs (2) direkt ansluten till msee via mörk fiber. De viktiga nätverks punkterna illustreras på följande sätt:

1.  Kund beräknings enhet (till exempel en server eller dator)
2.  CEs: kund gräns routrar 
3.  Parameterentiteter (CE Facing): providers yttre routrar/växlar som riktar sig mot kund gräns routrar. Kallas PE-CEs i det här dokumentet.
4.  Parameterentiteter (MSEE: N Facing): providerns gräns routrar/växlar som är riktade till msee. Kallas PE-MSEEs i det här dokumentet.
5.  Msee: Microsoft Enterprise Edge (MSEE: N) ExpressRoute-routrar
6.  Virtual Network-Gateway (VNet)
7.  Compute-enhet på Azure VNet

Om Cloud Exchange-samplaceringen, punkt-till-punkt-Ethernet eller Direct anslutnings modeller används, upprättar CEs (2) BGP-peering med msee (5). 

Om någon-till-valfri (IPVPN)-anslutnings modell används, upprättar PE-MSEEs (4) BGP-peering med msee (5). PE-MSEEs sprider de vägar som tas emot från Microsoft till kund nätverket via IPVPN Service Provider Network.

> [!NOTE]
>För hög tillgänglighet upprättar Microsoft en fullständigt redundant parallell anslutning mellan msee (5) och PE-MSEEs (4) par. En fullständigt redundant parallell nätverks Sök väg rekommenderas också mellan kundens nätverk och PE-CEs paret. Mer information om hög tillgänglighet finns i artikeln [design för hög tillgänglighet med ExpressRoute][HA]
>
>

Följande är de logiska stegen i Troubleshooting ExpressRoute-kretsen:

* [Verifiera krets etablering och tillstånd](#verify-circuit-provisioning-and-state)
  
* [Verifiera peering-konfiguration](#validate-peering-configuration)
  
* [Verifiera ARP](#validate-arp)
  
* [Verifiera BGP och vägar på MSEE: N](#validate-bgp-and-routes-on-the-msee)
  
* [Bekräfta trafikflöde](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Verifiera krets etablering och tillstånd
Genom att etablera en ExpressRoute-krets upprättas redundant Layer 2-anslutningar mellan CEs/PE-msee (2)/(4) och msee (5). Mer information om hur du skapar, ändrar, etablerar och verifierar en ExpressRoute-krets finns i artikeln [skapa och ändra en ExpressRoute-krets][CreateCircuit].

>[!TIP]
>En tjänst nyckel identifierar unikt en ExpressRoute-krets. Om du behöver hjälp från Microsoft eller en ExpressRoute-partner för att felsöka ett ExpressRoute-problem kan du ange tjänst nyckeln för att enkelt identifiera kretsen.
>
>

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure Portal
Öppna ExpressRoute krets-bladet i Azure Portal. I avsnittet ![3][3] på bladet visas ExpressRoute Essentials som visas på följande skärm bild:

![4][4]    

I ExpressRoute Essentials anger *kretsen* status för kretsen på Microsoft-sidan. *Leverantörs status* anger om kretsen har *etablerats/inte etablerats* på tjänstens leverantörs sida. 

För att en ExpressRoute-krets ska fungera måste *kretsens status* vara *aktive rad* och *providerns status* måste vara *etablerad*.

> [!NOTE]
> När du har konfigurerat en ExpressRoute-krets, om *kretsens status* är fastnat i statusen inte aktive rad, kontaktar du [Microsoft Support][Support]. Å andra sidan, om *providerns status* är fastnat i statusen inte etablerad, kontaktar du leverantören.
>
>

### <a name="verification-via-powershell"></a>Verifiering via PowerShell
Om du vill visa en lista över alla ExpressRoute-kretsar i en resurs grupp använder du följande kommando:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"
```

>[!TIP]
>Om du letar efter namnet på en resurs grupp kan du hämta det genom att lista alla resurs grupper i din prenumeration med kommandot *Get-AzResourceGroup*
>


Använd följande kommando för att välja en viss ExpressRoute-krets i en resurs grupp:

```azurepowershell
Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
```

Ett exempel svar är:

```output
Name                             : Test-ER-Ckt
ResourceGroupName                : Test-ER-RG
Location                         : westus2
Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_UnlimitedData",
                                    "Tier": "Standard",
                                    "Family": "UnlimitedData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : {
                                    "ServiceProviderName": "****",
                                    "PeeringLocation": "******",
                                    "BandwidthInMbps": 100
                                    }
ServiceKey                       : **************************************
Peerings                         : []
Authorizations                   : []
```

För att bekräfta om en ExpressRoute-krets fungerar, bör du särskilt tänka på följande fält:

```output
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
```

> [!NOTE]
> När du har konfigurerat en ExpressRoute-krets, om *kretsens status* är fastnat i statusen inte aktive rad, kontaktar du [Microsoft Support][Support]. Å andra sidan, om *providerns status* är fastnat i statusen inte etablerad, kontaktar du leverantören.
>
>

## <a name="validate-peering-configuration"></a>Verifiera peering-konfiguration
När tjänste leverantören har slutfört etableringen av ExpressRoute-kretsen kan flera eBGP-baserade konfigurationer för routning skapas över ExpressRoute-kretsen mellan CEs/MSEE: N-Parameterentiteter (2)/(4) och msee (5). Varje ExpressRoute-krets kan ha: Azures privata peering (trafik till privata virtuella nätverk i Azure) och/eller Microsoft-peering (trafik till offentliga slut punkter för PaaS och SaaS). Mer information om hur du skapar och ändrar konfiguration av routning finns i artikeln [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Verifiering via Azure Portal

> [!NOTE]
> I IPVPN anslutnings modell hanterar tjänst leverantörer ansvaret för att konfigurera peering (Layer 3-tjänster). När tjänst leverantören har konfigurerat en peering i en sådan modell och om peering är tom i portalen, kan du prova att uppdatera krets konfigurationen med hjälp av uppdaterings knappen på portalen. Den här åtgärden hämtar den aktuella konfigurationen för routning från kretsen. 
>

I Azure Portal kan status för en peering för en ExpressRoute-krets kontrol leras under bladet ExpressRoute-krets. I bladet ![3][3] i bladet skulle ExpressRoute-peering visas som på följande skärm bild:

![5][5]

I föregående exempel, som noterade att Azures privata peering har tillhandahållits, medan Azures offentliga och Microsoft-peering inte har tillhandahållits. En etablerad peering-kontext har även de primära och sekundära punkt-till-punkt-undernät som anges. /30-undernät används för IP-adressen för gränssnittet msee och CEs/PE-msee. För peering som har tillhandahållits, Visar listan även vem som senast ändrade konfigurationen. 

> [!NOTE]
> Om det inte går att aktivera en peering, kontrollerar du om de primära och sekundära undernät som har tilldelats matchar konfigurationen på den länkade CE/PE-MSEE: N. Kontrol lera också om rätt *VlanId*, *AzureASN* och *PeerASN* används på msee och om dessa värden mappar till de som används i den länkade CE/PE-msee: n. Om du väljer MD5-hash ska den delade nyckeln vara densamma på MSEE: N och PE-MSEE: N/CE-paret. Den tidigare konfigurerade delade nyckeln visas inte av säkerhets skäl. Om du behöver ändra någon av dessa konfigurationer på en MSEE: N-router, se [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].  
>

> [!NOTE]
> På ett/30-undernät som är tilldelat för gränssnittet väljer Microsoft den andra användbara IP-adressen för under nätet för MSEE: N-gränssnittet. Se därför till att den första användbara IP-adressen för under nätet har tilldelats till peered CE/PE-MSEE: N.
>


### <a name="verification-via-powershell"></a>Verifiering via PowerShell
Använd följande kommandon för att hämta konfigurations information för Azure privat peering:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
```

Ett exempel svar, för en korrekt konfigurerad privat peering, är:

```output
Name                       : AzurePrivatePeering
Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
Etag                       : W/"################################"
PeeringType                : AzurePrivatePeering
AzureASN                   : 12076
PeerASN                    : 123##
PrimaryPeerAddressPrefix   : 172.16.0.0/30
SecondaryPeerAddressPrefix : 172.16.0.4/30
PrimaryAzurePort           : 
SecondaryAzurePort         : 
SharedKey                  : 
VlanId                     : 200
MicrosoftPeeringConfig     : null
ProvisioningState          : Succeeded
```

 En aktive rad peering-kontext skulle ha de primära och sekundära adress prefixen i listan. /30-undernät används för IP-adressen för gränssnittet msee och CEs/PE-msee.

Använd följande kommandon för att hämta konfigurations information för Azures offentliga peering:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
```

Använd följande kommandon för att hämta konfigurations information för Microsoft-peering:

```azurepowershell
$ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

Om en peering inte har kon figurer ATS visas ett fel meddelande. Ett exempel svar när den angivna peering (offentlig Azure-peering i det här exemplet) inte har kon figurer ATS inom kretsen:

```azurepowershell
Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
At line:1 char:1
    + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
        + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand
```

> [!NOTE]
> Om det inte går att aktivera en peering, kontrollerar du om de primära och sekundära undernät som har tilldelats matchar konfigurationen på den länkade CE/PE-MSEE: N. Kontrol lera också om rätt *VlanId*, *AzureASN* och *PeerASN* används på msee och om dessa värden mappar till de som används i den länkade CE/PE-msee: n. Om du väljer MD5-hash ska den delade nyckeln vara densamma på MSEE: N och PE-MSEE: N/CE-paret. Den tidigare konfigurerade delade nyckeln visas inte av säkerhets skäl. Om du behöver ändra någon av dessa konfigurationer på en MSEE: N-router, se [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].  
>
>

> [!NOTE]
> På ett/30-undernät som är tilldelat för gränssnittet väljer Microsoft den andra användbara IP-adressen för under nätet för MSEE: N-gränssnittet. Se därför till att den första användbara IP-adressen för under nätet har tilldelats till peered CE/PE-MSEE: N.
>

## <a name="validate-arp"></a>Verifiera ARP

ARP-tabellen innehåller en mappning av IP-adressen och MAC-adressen för en viss peering. ARP-tabellen för en ExpressRoute-krets-peering innehåller följande information för varje gränssnitt (primär och sekundär):
* Mappning av IP-adress för lokalt routergränssnitt till MAC-adressen
* Mappning av IP-adressen för ExpressRoute router Interface till MAC-adressen
* Ålder på mappnings-ARP-tabeller kan hjälpa dig att validera nivå 2-konfiguration och fel sökning av problem med Layer 2-anslutningar.


Se [Hämta ARP-tabeller i distributions modell dokumentet i Resource Manager][ARP] , för hur du visar ARP-tabellen för en ExpressRoute-peering och hur du använder informationen för att felsöka problem med Layer 2-anslutning.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Verifiera BGP och vägar på MSEE: N

Använd följande kommando för att hämta routningstabellen från MSEE: N på den *primära* sökvägen för kontexten för *privat* Routning:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****
```

Ett exempel svar är:

```output
Network : 10.1.0.0/16
NextHop : 10.17.17.141
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.1.0.0/16
NextHop : 10.17.17.140*
LocPrf  : 
Weight  : 0
Path    : 65515

Network : 10.2.20.0/25
NextHop : 172.16.0.1
LocPrf  : 
Weight  : 0
Path    : 123##
```

> [!NOTE]
> Om tillståndet för en eBGP-peering mellan en MSEE: N och en CE/PE-MSEE: N är aktiv eller inaktiv, kontrollerar du om de primära och sekundära peer-undernät som har tilldelats matchar konfigurationen på den länkade CE/PE-MSEE: N. Kontrol lera också om rätt *VlanId*, *AzureAsn* och *PeerAsn* används på msee och om dessa värden mappar till dem som används i den länkade PE-msee: n/CE. Om du väljer MD5-hash ska den delade nyckeln vara densamma på MSEE: N-och CE/PE-MSEE: N-paret. Om du behöver ändra någon av dessa konfigurationer på en MSEE: N-router, se [skapa och ändra routning för en ExpressRoute-krets][CreatePeering].
>


> [!NOTE]
> Om vissa mål inte kan kommas åt via en peering, kontrollerar du routningstabellen för msee för motsvarande peering-kontext. Om ett matchande prefix (kan vara NATed IP) finns i routningstabellen, kontrollerar du om det finns brand väggar/NSG/ACL: er på den sökväg som blockerar trafiken.
>


I följande exempel visas svaret på kommandot för peering som inte finns:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="confirm-the-traffic-flow"></a>Bekräfta trafikflöde
Använd följande kommando för att få en kombinerad primär och sekundär väg för trafik statistik – byte in och ut--av en peering-kontext:

```azurepowershell
Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'
```

Ett exempel på utdata från kommandot är:

```output
PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
-------------- --------------- ---------------- -----------------
     240780020       239863857        240565035         239628474
```

Ett exempel på utdata från kommandot för en icke-befintlig peering är:

```azurepowershell
Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
StatusCode: 400
```

## <a name="next-steps"></a>Nästa steg
Mer information eller hjälp finns i följande länkar:

- [Microsoft Support][Support]
- [Skapa och ändra en ExpressRoute-krets][CreateCircuit]
- [Skapa och ändra routning för en ExpressRoute-krets][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "logisk Express vägs anslutning"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikonen alla resurser"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Översikts ikon"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Skärm bild av ExpressRoute Essentials-exempel"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Skärm bild av ExpressRoute Essentials-exempel"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ARP]: ./expressroute-troubleshooting-arp-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[DR-Pvt]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
