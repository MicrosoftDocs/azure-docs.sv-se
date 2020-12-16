---
title: 'Azure-ExpressRoute: ARP-tabeller – fel sökning'
description: Den här sidan innehåller instruktioner för hur du hämtar ARP-tabeller (Address Resolution Protocol) för en ExpressRoute-krets
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 7d8ae2c58979c66ebbbab366d172179bdeee4253
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561587"
---
# <a name="getting-arp-tables-in-the-resource-manager-deployment-model"></a>Hämta ARP-tabeller i distributions modellen för Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

Den här artikeln vägleder dig igenom stegen för att lära dig ARP-tabeller för din ExpressRoute-krets.

> [!IMPORTANT]
> Det här dokumentet är avsett för att hjälpa dig att diagnostisera och åtgärda enkla problem. Den är inte avsedd att ersätta Microsoft-supporten. Du måste öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du inte kan lösa problemet genom att följa anvisningarna nedan.
> 
> 

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP-protokoll (Address Resolution Protocol) och ARP-tabeller
ARP (Address Resolution Protocol) är ett Layer 2-protokoll som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa Ethernet-adressen (MAC-adress) med en IP-adress.

ARP-tabellen innehåller följande information för både de primära och sekundära gränssnitten för varje peering-typ:

1. Mappning av IP-adress för lokalt routergränssnitt till MAC-adressen
2. Mappning av IP-adressen för ExpressRoute router Interface till MAC-adressen
3. Mappningens ålder

ARP-tabeller kan hjälpa dig att validera Layer 2-konfiguration och fel sökning av problem med Layer 2-anslutningar. 

Exempel på ARP-tabell: 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


I följande avsnitt finns information om hur du kan visa ARP-tabeller som visas av ExpressRoute Edge-routrar. 

## <a name="prerequisites-for-learning-arp-tables"></a>Krav för Learning ARP-tabeller
Se till att informationen nedan stämmer innan du fortsätter:

* En giltig ExpressRoute-krets som kon figurer ATS med minst en peering. Kretsen måste konfigureras fullständigt av anslutnings leverantören. Du eller anslutnings leverantören måste ha konfigurerat minst Azure Private, Azure offentlig eller Microsoft-peering på denna krets.
* IP-adressintervall som används för att konfigurera peer kopplingarna. Granska exemplen för IP-adresstilldelning på [sidan ExpressRoute-krav för routning](expressroute-routing.md) för att se hur IP-adresser mappas till gränssnitt. Du kan få information om peering-konfigurationen genom att granska [konfigurations sidan för ExpressRoute-peering](expressroute-howto-routing-arm.md).
* Information från din nätverks team/anslutnings leverantör på MAC-adresserna för gränssnitt som används med dessa IP-adresser.
* Du måste ha den senaste PowerShell-modulen för Azure (version 1,50 eller senare).

> [!NOTE]
> Om Layer 3 tillhandahålls av tjänst leverantören och ARP-tabellerna är tomma i portalen/utdata nedan, uppdaterar du krets konfigurationen med hjälp av uppdatera-knappen på portalen. Den här åtgärden kommer att tillämpa konfigurationen för rätt routning på din krets. 
>
>

## <a name="getting-the-arp-tables-for-your-expressroute-circuit"></a>Hämta ARP-tabeller för din ExpressRoute-krets
Det här avsnittet innehåller anvisningar om hur du kan visa ARP-tabeller per peering med hjälp av PowerShell. Du eller anslutnings leverantören måste ha konfigurerat peering innan du fortsätter. Varje krets har två sökvägar (primär och sekundär). Du kan kontrol lera ARP-tabellen för varje sökväg oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för privat Azure-peering
Följande cmdlet tillhandahåller ARP-tabellerna för Azures privata peering

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure private peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary

# ARP table for Azure private peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 
```

Exempel på utdata visas nedan för en av Sök vägarna

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för offentlig Azure-peering
Följande cmdlet tillhandahåller ARP-tabellerna för Azures offentliga peering

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Azure public peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary

# ARP table for Azure public peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 
```


Exempel på utdata visas nedan för en av Sök vägarna

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabeller för Microsoft-peering
Följande cmdlet tillhandahåller ARP-tabeller för Microsoft-peering

```azurepowershell
# Required Variables
$RG = "<Your Resource Group Name Here>"
$Name = "<Your ExpressRoute Circuit Name Here>"

# ARP table for Microsoft peering - Primary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary

# ARP table for Microsoft peering - Secondary path
Get-AzExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 
```


Exempel på utdata visas nedan för en av Sök vägarna

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Så här använder du den här informationen
ARP-tabellen för en peering kan användas för att fastställa validering av Layer 2-konfiguration och-anslutning. Det här avsnittet innehåller en översikt över hur ARP-tabeller kommer att se ut under olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-operational-state-expected-state"></a>ARP-tabell när en krets är i drifts tillstånd (förväntat tillstånd)
* ARP-tabellen kommer att ha en post för den lokala sidan med en giltig IP-adress och MAC-adress. Samma kan ses för Microsoft-sidan. 
* Den sista oktetten i den lokala IP-adressen är alltid ett udda nummer.
* Den sista oktetten i Microsofts IP-adress är alltid ett jämnt tal.
* Samma MAC-adress visas på Microsoft-sidan för alla tre peer-datorer (primär/sekundär). 

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-on-premises--connectivity-provider-side-has-problems"></a>ARP-tabell när den lokala/anslutna leverantörs sidan har problem
Om det uppstår ett problem med den lokala providern eller anslutnings leverantören, Visar ARP-tabellen en av två saker. Du kan antingen se att den lokala MAC-adressen är ofullständig eller bara visa Microsoft-posten i ARP-tabellen.
  
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------   
  0 On-Prem           65.0.0.1   Incomplete
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```
eller
   
```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```  

> [!NOTE]
> Öppna en supportbegäran med din anslutnings leverantör för att felsöka problemen. Om ARP-tabellen inte har IP-adresser till gränssnitten som är mappade till MAC-adresser, granskar du följande information:
> 
> 1. Om den första IP-adressen för det/30-undernät som har tilldelats länken mellan MSEE: N-PR och MSEE: N används i gränssnittet för MSEE: N-PR. Azure använder alltid den andra IP-adressen för msee.
> 2. Kontrol lera att VLAN-taggar för Customer (C-tag) och service (S-tag) matchar både på MSEE: N-PR-och MSEE: N-par.
> 

### <a name="arp-table-when-microsoft-side-has-problems"></a>ARP-tabell när problem med Microsoft har problem
* Du ser inte en ARP-tabell som visas för en peering om det finns problem på Microsoft-sidan. 
* Öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ange att du har problem med Layer 2-anslutning. 

## <a name="next-steps"></a>Nästa steg
* Verifiera Layer 3-konfigurationer för din ExpressRoute-krets.
  * Hämta väg Sammanfattning för att fastställa tillstånd för BGP-sessioner.
  * Hämta routningstabellen för att avgöra vilka prefix som annonseras via ExpressRoute.
* Verifiera data överföring genom att granska byte in/ut.
* Öppna ett support ärende med [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du fortfarande har problem.

