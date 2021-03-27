---
title: Nätverks överväganden – Azure Dedicated HSM | Microsoft Docs
description: Översikt över nätverks överväganden som gäller för Azure dedikerade HSM-distributioner
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 3370389027805cfb5a68b5b0551d14dc31154804
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611845"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure Dedicated HSM-nätverk

Azure Dedicated HSM kräver en mycket säker nätverks miljö. Detta är sant om det är från Azure-molnet tillbaka till kundens IT-miljö (lokalt), med hjälp av distribuerade program eller för scenarier med hög tillgänglighet. Azure-nätverk ger detta och det finns fyra distinkta områden som måste åtgärdas.

- Skapa HSM-enheter i din Virtual Network (VNet) i Azure
- Ansluta lokalt till molnbaserade resurser för konfiguration och hantering av HSM-enheter
- Skapa och ansluta virtuella nätverk för program resurser mellan anslutning och HSM-enheter
- Ansluta virtuella nätverk mellan regioner för kommunikation mellan olika regioner och även för att aktivera scenarier med hög tillgänglighet

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtuellt nätverk för din dedikerade HSM: er

Dedikerade HSM: er är integrerade i en Virtual Network och placeras i kundernas egna privata nätverk i Azure. Detta ger åtkomst till enheterna från virtuella datorer eller beräknings resurser i det virtuella nätverket.  
Mer information om hur du integrerar Azure-tjänster i det virtuella nätverket och de funktioner som den tillhandahåller finns i dokumentationen [för virtuella nätverk för Azure-tjänster](../virtual-network/virtual-network-for-azure-services.md) .

### <a name="virtual-networks"></a>Virtuella nätverk

Innan du konfigurerar en dedikerad HSM-enhet måste kunderna först skapa en Virtual Network i Azure eller använda en som redan finns i kund prenumerationen. Det virtuella nätverket definierar säkerhets omkretsen för den dedikerade HSM-enheten. Mer information om hur du skapar virtuella nätverk finns i dokumentationen för det [virtuella nätverket](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Undernät

Undernät segmenterar det virtuella nätverket i separata adress utrymmen som kan användas av de Azure-resurser som du placerar i dem. Dedikerade HSM: er distribueras till ett undernät i det virtuella nätverket. Varje dedikerad HSM-enhet som distribueras i kundens undernät kommer att få en privat IP-adress från det här under nätet. Under nätet där HSM-enheten distribueras måste vara uttryckligen delegerad till tjänsten: Microsoft. HardwareSecurityModules/dedicatedHSMs. Detta ger vissa behörigheter till HSM-tjänsten för distribution till under nätet. Delegering till dedikerade HSM: er tillämpar vissa princip begränsningar på under nätet. Nätverks säkerhets grupper (NSG: er) och User-Defined vägar (UDR) stöds för närvarande inte i delegerade undernät. När ett undernät delegeras till dedikerade HSM: er, kan det därför endast användas för att distribuera HSM-resurser. Det går inte att distribuera andra kund resurser till under nätet.


### <a name="expressroute-gateway"></a>ExpressRoute-Gateway

Ett krav för den aktuella arkitekturen är konfiguration av en återställnings-gateway i under nätet för kunder där en HSM-enhet måste placeras för att möjliggöra integrering av HSM-enheten i Azure. Den här ER-gatewayen kan inte användas för att ansluta lokala platser till kundernas HSM-enheter i Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Ansluta ditt lokala nätverk till Azure

När du skapar molnbaserade resurser är det ett typiskt krav för en privat anslutning tillbaka till lokala IT-resurser. I händelse av dedikerad HSM är detta huvudsakligen för HSM-klientprogramvaran att konfigurera HSM-enheter och även för aktiviteter som säkerhets kopiering och hämtning av loggar från HSM: er för analys. En viktig besluts punkt här är den typ av anslutning som finns i alternativ.  Det mest flexibla alternativet är plats-till-plats-VPN eftersom det kan finnas flera lokala resurser som kräver säker kommunikation med resurser (inklusive HSM: er) i Azure-molnet. Detta kräver att en kund organisation har en VPN-enhet för att under lätta anslutningen. En punkt-till-plats-VPN-anslutning kan användas om det bara finns en enda slut punkt lokalt, till exempel en enda administrations arbets Station.
Mer information om anslutnings alternativ finns i [VPN gateway planerings alternativ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> För närvarande är ExpressRoute inte ett alternativ för anslutning till lokala resurser. Det bör även noteras att ExpressRoute-gatewayen som används enligt beskrivningen ovan inte är för anslutningar till en lokal infrastruktur.

### <a name="point-to-site-vpn"></a>Punkt-till-plats-VPN

Ett virtuellt privat nätverk från punkt till plats är den enklaste formen av säker anslutning till en enda slut punkt lokalt. Detta kan vara relevant om du endast planerar att ha en enda administrations arbets station för Azure-baserade dedikerade HSM: er.

### <a name="site-to-site-vpn"></a>Plats-till-plats-VPN

Ett plats-till-plats-virtuellt privat nätverk möjliggör säker kommunikation mellan Azure-baserade dedikerade HSM: er och din lokala IT. En anledning till att göra detta är att ha en säkerhets kopierings funktion för HSM: s lokala och behöver en anslutning mellan de två för att köra säkerhets kopieringen.

## <a name="connecting-virtual-networks"></a>Ansluta virtuella nätverk

En typisk distributions arkitektur för dedikerad HSM kommer att starta med ett enda virtuellt nätverk och motsvarande undernät där HSM-enheterna skapas och tillhandahålls. Inom samma region kan det vara bra att ha ytterligare virtuella nätverk och undernät för program komponenter som använder dedikerad HSM. För att aktivera kommunikation mellan dessa nätverk använder vi Virtual Network-peering.

### <a name="virtual-network-peering"></a>Virtuell nätverkspeering

Om det finns flera virtuella nätverk i en region som behöver åtkomst till var and ras resurser, kan Virtual Network peering användas för att skapa säkra kommunikations kanaler mellan dem.  Peering av virtuella nätverk ger inte bara säker kommunikation utan garanterar också en låg latens och anslutningar med hög bandbredd mellan resurserna i Azure.

![nätverks-peering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Ansluta i Azure-regioner

HSM-enheterna har möjlighet att via program bibliotek omdirigera trafik till en alternativ HSM. Fördirigering av trafik är användbart om enheter inte fungerar eller om åtkomst till en enhet går förlorad. Scenarier på regional nivå kan undvikas genom att distribuera HSM: er i andra regioner och aktivera kommunikation mellan virtuella nätverk över flera regioner.

### <a name="cross-region-ha-using-vpn-gateway"></a>Mellan region HA med VPN-gateway

För globalt distribuerade program eller för regionala failover-scenarier med hög tillgänglighet måste du ansluta virtuella nätverk över flera regioner. Med Azure Dedicated HSM kan hög tillgänglighet uppnås med hjälp av en VPN Gateway som tillhandahåller en säker tunnel mellan de två virtuella nätverken. Mer information om VNet-till-VNet-anslutningar med hjälp av VPN Gateway finns i artikeln med rubriken [Vad är VPN gateway?](../vpn-gateway/design.md#V2V)

> [!NOTE]
> Global VNet-peering är inte tillgänglig i scenarier mellan olika regioner med dedikerade HSM: er just nu och VPN-gatewayen bör användas i stället. 

![Diagrammet visar två regioner anslutna med två V P N-gatewayer. Varje region innehåller peer-kopplat virtuella nätverk.](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>Nätverks begränsningar
> [!NOTE]
> En begränsning av den dedikerade HSM-tjänsten med hjälp av under näts delegering införs begränsningar som bör övervägas när du utformar mål nätverks arkitekturen för en HSM-distribution. Användning av under näts delegering innebär att NSG: er, UDR och global VNet-peering inte stöds för dedikerad HSM. I avsnitten nedan får du hjälp med alternativa metoder för att uppnå samma eller liknande resultat för dessa funktioner. 

HSM-NÄTVERKSKORTet som finns i det dedikerade HSM VNet kan inte använda nätverks säkerhets grupper eller användardefinierade vägar. Det innebär att det inte går att ange standard-Deny-principer från den dedikerade HSM-VNet och att andra nätverks segment måste vara allowlisted för att få åtkomst till den dedikerade HSM-tjänsten. 

Genom att lägga till NVA-proxyn (Network Virtual reservers) kan en NVA-brandvägg i överförings-och DMZ-navet placeras logiskt framför HSM-NÄTVERKSKORTet, vilket ger det nödvändiga alternativet till NSG: er och UDR.

### <a name="solution-architecture"></a>Lösningsarkitektur
Nätverks designen kräver följande element:
1.  En överförings-eller DMZ Hub-VNet med en NVA-proxyadress. Det finns två eller fler NVA i idealet. 
2.  En ExpressRoute-krets med en privat peering aktive rad och en anslutning till transport Hub VNet.
3.  En VNet-peering mellan transport hubbens VNet och det dedikerade HSM-VNet.
4.  En NVA-brandvägg eller Azure-brandvägg kan distribueras DMZ-tjänster i hubben som ett alternativ. 
5.  Ytterligare virtuella nätverk för arbets belastnings ekrar kan peer-kopplas till hubb-VNet. Gemalto-klienten har åtkomst till den dedikerade HSM-tjänsten via hubbens VNet.

![Diagram visar ett DMZ Hub-VNet med en NVA-proxyvärd för NSG och UDR-lösning](media/networking/network-architecture.png)

Eftersom du lägger till NVA proxy-lösningen kan en NVA-brandvägg i överförings-och DMZ-navet placeras logiskt framför HSM-NÄTVERKSKORTet, vilket ger nödvändiga standard principer för neka. I vårt exempel använder vi Azure-brandväggen för detta ändamål och behöver följande element på plats:
1. En Azure-brandvägg distribuerad till under nätet "AzureFirewallSubnet" i DMZ Hub VNet
2. En routningstabell med en UDR som dirigerar trafik till den privata Azure ILB-slutpunkten i Azure-brandväggen. Den här routningstabellen används för den GatewaySubnet där den virtuella ExpressRoute för kunden finns
3. Nätverks säkerhets regler inom AzureFirewall för att tillåta vidarebefordran mellan ett betrott käll intervall och den privata Azure IBL-slutpunkten som lyssnar på TCP-port 1792. Den här säkerhets logiken lägger till den nödvändiga principen "standard neka" mot den dedikerade HSM-tjänsten. Det innebär att endast betrodda käll-IP-intervall tillåts i den dedikerade HSM-tjänsten. Alla andra intervall kommer att tas bort.  
4. En routningstabell med en UDR som dirigerar trafik till lokal i Azure-brandväggen. Den här routningstabellen används i NVA-proxy-undernätet. 
5. En NSG som används för proxy NVA-undernätet för att endast lita på under näts intervallet för Azure-brandväggen som källa och för att endast tillåta vidarebefordran till HSM NIC IP-adress via TCP-port 1792. 

> [!NOTE]
> Eftersom NVA-UDR kommer att kräva att klientens IP-adress överförs till HSM-NÄTVERKSKORTet krävs inga mellan HSM VNet och DMZ Hub VNet.  

### <a name="alternative-to-udrs"></a>Alternativ till UDR
Den NVA nivå lösning som nämns ovan fungerar som ett alternativ till UDR. Det finns några viktiga saker att tänka på.
1.  Översättning av nätverks adresser ska konfigureras på NVA för att tillåta att RETUR trafik dirigeras korrekt.
2. Kunderna bör inaktivera klientens IP-check i Luna HSM-konfigurationen för att använda VNA för NAT. Följande kommandon servce som exempel.
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  Distribuera UDR för ingress trafik till NVA-nivån. 
4. Som enligt design kommer HSM-undernät inte att initiera en utgående anslutningsbegäran till plattforms nivån.

### <a name="alternative-to-using-global-vnet-peering"></a>Alternativ till att använda globala VNET-peering
Det finns ett par arkitekturer som du kan använda som ett alternativ till global VNet-peering.
1.  Använda [VNet-till-Vnet VPN gateway-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) 
2.  Anslut HSM VNET med ett annat VNET med en ER-krets. Detta fungerar bäst när en direkt lokal sökväg krävs eller VPN VNET. 

#### <a name="hsm-with-direct-express-route-connectivity"></a>HSM med Direct Express Route-anslutning
![Diagrammet visar HSM med direkt Express Route-anslutning](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga frågor och svar](faq.md)
- [Support](supportability.md)
- [Hög tillgänglighet](high-availability.md)
- [Fysisk säkerhet](physical-security.md)
- [Övervakning](monitoring.md)
- [Distributions arkitektur](deployment-architecture.md)
