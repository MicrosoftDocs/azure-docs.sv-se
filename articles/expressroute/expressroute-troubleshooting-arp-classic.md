---
title: 'Azure-ExpressRoute: ARP-tabeller – fel sökning: klassisk'
description: Den här sidan innehåller instruktioner för att hämta ARP-tabeller för en ExpressRoute-krets – klassisk distributions modell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/30/2017
ms.author: duau
ms.openlocfilehash: 7c83b6d25936e184c70d4bf0a51636a3986bf4b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89394847"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>Hämta ARP-tabeller i den klassiska distributions modellen
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell – Klassisk](expressroute-troubleshooting-arp-classic.md)
> 
> 

Den här artikeln vägleder dig igenom stegen för att hämta ARP-tabellerna (Address Resolution Protocol) för din Azure ExpressRoute-krets.

> [!IMPORTANT]
> Det här dokumentet är avsett för att hjälpa dig att diagnostisera och åtgärda enkla problem. Den är inte avsedd att ersätta Microsoft-supporten. Om du inte kan lösa problemet med hjälp av följande vägledning öppnar du en supportbegäran med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>ARP-protokoll (Address Resolution Protocol) och ARP-tabeller
ARP är ett nivå 2-protokoll som definieras i [RFC 826](https://tools.ietf.org/html/rfc826). ARP används för att mappa en Ethernet-adress (MAC-adress) till en IP-adress.

En ARP-tabell innehåller en mappning av IPv4-adressen och MAC-adressen för en viss peering. ARP-tabellen för en ExpressRoute-krets-peering innehåller följande information för varje gränssnitt (primär och sekundär):

1. Mappning av en lokal routers gränssnitts-IP-adress till en MAC-adress
2. Mappning av en IP-adress för ExpressRoute router-router till en MAC-adress
3. Ålder på mappningen

ARP-tabeller kan hjälpa dig med validering av Layer 2-konfiguration och med fel sökning av problem med grundläggande Layer 2-anslutningar.

Följande är ett exempel på en ARP-tabell:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


I följande avsnitt finns information om hur du visar ARP-tabellerna som visas av ExpressRoute Edge-routrar.

## <a name="prerequisites-for-using-arp-tables"></a>Krav för att använda ARP-tabeller
Se till att du har följande innan du fortsätter:

* En giltig ExpressRoute-krets som är konfigurerad med minst en peering. Kretsen måste konfigureras fullständigt av anslutnings leverantören. Du (eller din anslutnings leverantör) måste konfigurera minst en av peering (Azure Private, Azure Public eller Microsoft) på den här kretsen.
* IP-adressintervall som används för att konfigurera peering (Azure Private, Azure Public och Microsoft). Granska exemplen för IP-adresstilldelning på [sidan ExpressRoute-krav för routning](expressroute-routing.md) för att få en förståelse för hur IP-adresser mappas till gränssnitt på din sida och på ExpressRoute-sidan. Du kan få information om peering-konfigurationen genom att granska [konfigurations sidan för ExpressRoute-peering](expressroute-howto-routing-classic.md).
* Information från nätverks teamet eller anslutnings leverantören om MAC-adresserna för de gränssnitt som används med de här IP-adresserna.
* Den senaste Windows PowerShell-modulen för Azure (version 1,50 eller senare).

## <a name="arp-tables-for-your-expressroute-circuit"></a>ARP-tabeller för din ExpressRoute-krets
Det här avsnittet innehåller anvisningar om hur du visar ARP-tabeller för varje typ av peering med hjälp av PowerShell. Innan du fortsätter måste du eller din anslutnings leverantör konfigurera peer kopplingen. Varje krets har två sökvägar (primär och sekundär). Du kan kontrol lera ARP-tabellen för varje sökväg oberoende av varandra.

### <a name="arp-tables-for-azure-private-peering"></a>ARP-tabeller för privat Azure-peering
Följande cmdlet tillhandahåller ARP-tabeller för Azures privata peering:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure private peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

# ARP table for Azure private peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary
```

Följande är exempel på utdata för en av Sök vägarna:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-azure-public-peering"></a>ARP-tabeller för offentlig Azure-peering:
Följande cmdlet tillhandahåller ARP-tabeller för Azures offentliga peering:

```azurepowershell
# Required variables
$ckt = "<your Service Key here>

# ARP table for Azure public peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

# ARP table for Azure public peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary
```

Följande är exempel på utdata för en av Sök vägarna:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           10.0.0.1   ffff.eeee.dddd
  0 Microsoft         10.0.0.2   aaaa.bbbb.cccc
```


Följande är exempel på utdata för en av Sök vägarna:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           64.0.0.1   ffff.eeee.dddd
  0 Microsoft         64.0.0.2   aaaa.bbbb.cccc
```


### <a name="arp-tables-for-microsoft-peering"></a>ARP-tabeller för Microsoft-peering
Följande cmdlet tillhandahåller ARP-tabeller för Microsoft-peering:

```azurepowershell
# ARP table for Microsoft peering--primary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

# ARP table for Microsoft peering--secondary path
Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary
```


Exempel på utdata visas nedan för en av Sök vägarna:

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```


## <a name="how-to-use-this-information"></a>Så här använder du den här informationen
ARP-tabellen för en peering kan användas för att verifiera skikt 2-konfiguration och-anslutning. Det här avsnittet innehåller en översikt över hur ARP-tabeller ser ut i olika scenarier.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>ARP-tabell när en krets är i ett drifts tillstånd (förväntat)
* ARP-tabellen har en post för den lokala sidan med en giltig IP-adress och MAC-adress och en liknande post för Microsoft-sidan.
* Den sista oktetten i den lokala IP-adressen är alltid ett udda nummer.
* Den sista oktetten i Microsofts IP-adress är alltid ett jämnt tal.
* Samma MAC-adress visas på Microsoft-sidan för alla tre peer-datorer (primär/sekundär).

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
 10 On-Prem           65.0.0.1   ffff.eeee.dddd
  0 Microsoft         65.0.0.2   aaaa.bbbb.cccc
```

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>ARP-tabell när den är lokalt eller när anslutningen på leverantörs sidan har problem
 Endast en post visas i ARP-tabellen. Den visar mappningen mellan MAC-adressen och den IP-adress som används på Microsoft-sidan.

```output
Age InterfaceProperty IpAddress  MacAddress    
--- ----------------- ---------  ----------    
   0 Microsoft         65.0.0.2  aaaa.bbbb.cccc
```

> [!NOTE]
> Om det uppstår ett problem som detta kan du öppna en supportbegäran med anslutnings leverantören för att lösa problemet.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>ARP-tabell när Microsoft-sidan har problem
* Du ser ingen ARP-tabell för en peering om det finns problem på Microsoft-sidan.
* Öppna en support förfrågan med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Ange att du har problem med Layer 2-anslutning.

## <a name="next-steps"></a>Nästa steg
* Verifiera Layer 3-konfigurationer för din ExpressRoute-krets:
  * Hämta en väg Sammanfattning för att fastställa statusen för BGP-sessioner.
  * Hämta en routningstabell för att avgöra vilka prefix som annonseras via ExpressRoute.
* Verifiera data överföring genom att granska byte in och ut.
* Öppna en support förfrågan med [Microsoft Azure hjälp + support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du fortfarande har problem.

