---
title: 'Azure ExpressRoute: Azure för Network-tekniker'
description: På den här sidan förklaras vanliga nät verks tekniker hur nätverk fungerar i Azure.
documentationcenter: na
services: networking
author: osamaz
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 06/25/2020
ms.author: osamaz
ms.openlocfilehash: 9a22e58e4407897fb9418cae0ba9f32408cda8e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98234213"
---
# <a name="azure-for-network-engineers"></a>Azure för nätverkstekniker
Som en konventionell nätverks tekniker har du hanterat fysiska till gångar, till exempel routrar, växlar, kablar och brand väggar för att bygga infrastruktur. I ett logiskt lager har du konfigurerat virtuellt LAN (VLAN), spanning tree Protocol (STP), routningsprotokoll (RIP, OSPF, BGP). Du har hanterat ditt nätverk med hjälp av hanterings verktyg och CLI. Nätverk i molnet är annorlunda där nätverks slut punkter är logiska och användning av routningsprotokoll är minst. Du kommer att arbeta med Azure Resource Manager API, Azure CLI och PowerShell för att konfigurera och hantera till gångar i Azure. Du kommer att starta din nätverks resa i molnet genom att förstå grundläggande klienter i Azure-nätverk. 
## <a name="virtual-network"></a>Virtuellt nätverk
När du utformar ett nätverk från nedre delen samlar du in lite grundläggande information. Den här informationen kan vara antalet värdar, nätverks enheter, antal undernät, routning mellan undernät, isolerade domäner som till exempel VLAN. Den här informationen hjälper till att ändra storlek på Nätverks-och säkerhets enheter och skapa arkitekturen för att stödja program och tjänster.

När du planerar att distribuera dina program och tjänster i Azure börjar du med att skapa en logisk gränser i Azure, vilket kallas för ett virtuellt nätverk. Det här virtuella nätverket är via till en fysisk nätverks gränser. Eftersom det är ett virtuellt nätverk behöver du inte fysiskt kugg hjul, men ändå måste du planera för de logiska enheterna, till exempel IP-adresser, IP-undernät, Routning och principer.

När du skapar ett virtuellt nätverk i Azure är det förkonfigurerat med ett IP-intervall (10.0.0.0/16). Intervallet är inte fast, du kan definiera ett eget IP-intervall. Du kan definiera både IPv4-och IPv6-adressintervall. IP-intervall som har definierats för det virtuella nätverket annonseras inte till Internet. Du kan skapa flera undernät från ditt IP-intervall. Dessa undernät kommer att användas för att tilldela IP-adresser till virtuella nätverks gränssnitt (virtuella nätverkskort). De första fyra IP-adresserna från varje undernät är reserverade och kan inte användas för IP-allokering. Det finns inget begrepp för VLAN i ett offentligt moln. Du kan dock skapa isolering i ett virtuellt nätverk utifrån dina definierade undernät.

Du kan skapa ett stort undernät som omfattar det virtuella nätverkets adress utrymme eller välja att skapa flera undernät. Men om du använder en virtuell nätverksgateway måste du skapa ett undernät med namnet "Gateway-undernät" i Azure. Azure kommer att använda det här under nätet för att tilldela IP-adresser till virtuella nätverksgateway. 

## <a name="ip-allocation"></a>IP-allokering

När du tilldelar en IP-adress till en värd tilldelar du faktiskt en IP-adress till ett nätverkskort (NIC). Du kan tilldela två typer av IP-adresser till ett nätverkskort i Azure:

- Offentliga IP-adresser – används för att kommunicera inkommande och utgående (utan Network Address Translation (NAT)) med Internet och andra Azure-resurser som inte är anslutna till ett virtuellt nätverk. Tilldelning av en offentlig IP-adress till ett NIC är valfritt. Offentliga IP-adresser tillhör Microsoft IP-adressutrymmet.
- Privata IP-adresser – används för kommunikation inom ett virtuellt nätverk, ditt lokala nätverk och Internet (med NAT). IP-adressutrymmet som du definierar i det virtuella nätverket betraktas som privat även om du konfigurerar det offentliga IP-adressutrymmet. Microsoft meddelar inte det här utrymmet till Internet. Du måste tilldela minst en privat IP-adress till en virtuell Dator.

Precis som med fysiska värdar eller enheter finns det två sätt att allokera en IP-adress till en resurs-dynamisk eller statisk. I Azure är standard metoden för allokering dynamisk, där en IP-adress tilldelas när du skapar en virtuell dator (VM) eller startar en stoppad virtuell dator. IP-adressen frisläpps när du stoppar eller tar bort den virtuella datorn. Om du inte vill att IP-adressen för den virtuella datorn ska ändras kan du uttryckligen ange allokeringsmetoden till statisk. I så fall tilldelas en IP-adress direkt. Den frisläpps bara om du ta bort den virtuella datorn eller om du ändrar dess allokeringsmetod till dynamisk. 

Privata IP-adresser tilldelas från de undernät som du har definierat i ett virtuellt nätverk. För en virtuell dator väljer du ett undernät för IP-allokeringen. Om en virtuell dator innehåller flera nätverkskort kan du välja ett annat undernät för varje nätverkskort.

## <a name="routing"></a>Routning
När du skapar ett virtuellt nätverk skapar Azure en routningstabell för nätverket. Routningstabellen innehåller följande typer av vägar.
- Systemvägar
- Standard vägar för undernät
- Vägar från andra virtuella nätverk
- BGP-vägar
- Tjänst slut punkts vägar
- Användardefinierade vägar (UDR)

När du skapar ett virtuellt nätverk för första gången utan att definiera några undernät, skapar Azure vägvals poster i routningstabellen. Dessa vägar kallas system vägar. System vägar definieras på den här platsen. Du kan inte ändra dessa vägar. Du kan dock åsidosätta system vägar genom att konfigurera UDR.

När du skapar ett eller flera undernät i ett virtuellt nätverk, skapar Azure standard poster i routningstabellen för att möjliggöra kommunikation mellan dessa undernät i ett virtuellt nätverk. Dessa vägar kan ändras med hjälp av statiska vägar, som är användardefinierade vägar (UDR) i Azure.

När du skapar en virtuell nätverkspeering mellan två virtuella nätverk läggs en väg till för varje adressintervall i adressutrymmet för varje virtuellt nätverk som en peering har skapats för.

Om din lokala nätverksgateway utbyter BGP-vägar (Border Gateway Protocol) med en virtuell Azure-nätverksgateway, läggs en väg till för varje väg som sprids från den lokala Nätverksgatewayen. Dessa vägar visas i routningstabellen som BGP-vägar.

De offentliga IP-adresserna för vissa tjänster läggs till i routningstabellen av Azure när du aktiverar en tjänstslutpunkt för tjänsten. Tjänst slut punkter är aktiverade för enskilda undernät i ett virtuellt nätverk. När du aktiverar en tjänst slut punkt läggs vägen bara till i routningstabellen för under nätet som tillhör den här tjänsten. Azure hanterar adresserna i routningstabellen automatiskt när adresserna ändras.

Användardefinierade vägar kallas även anpassade vägar. Du skapar UDR i Azure för att åsidosätta Azures standard system vägar eller lägga till ytterligare vägar till ett undernäts routningstabell. I Azure skapar du en routningstabell och kopplar sedan routningstabellen till undernät för virtuella nätverk.

När du har konkurrerande poster i en routningstabell väljer Azure nästa hopp baserat på det längsta prefix som motsvarar traditionella routrar. Men om det finns flera nästa hopp-poster med samma adressprefix väljer Azure vägarna i följande ordning.
1. Användardefinierade vägar (UDR)
1. BGP-vägar
1. Systemvägar

## <a name="security"></a>Säkerhet

Du kan filtrera nätverks trafik till och från resurser i ett virtuellt nätverk med hjälp av nätverks säkerhets grupper. Du kan också använda virtuella nätverks installationer (NVA) som Azure-brandvägg eller brand väggar från andra leverantörer. Du kan styra hur Azure dirigerar trafik från undernät. Du kan också begränsa vem i din organisation som kan arbeta med resurser i virtuella nätverk.

En Nätverkssäkerhetsgrupp (NSG) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till undernät, NICs eller både och. NSG:er kan antingen associeras med undernät eller individuella NICs anslutna till ett undernät. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla virtuella datorer i det undernätet. Dessutom kan trafik till en enskild NIC begränsas genom att koppla en NSG direkt till en NIC.

NSG:er innehåller två regeluppsättningar: inkommande och utgående. En regels prioritet måste vara unik inom varje uppsättning. Varje regel har egenskaperna för protokoll, källa och målportintervall, adressprefix, trafikriktning, prioritet och behörighetstyp. Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar.

## <a name="verification"></a>Verifiering
### <a name="routes-in-virtual-network"></a>Vägar i virtuellt nätverk
Kombinationen av vägar som du skapar, Azures standard vägar och alla vägar som sprids från ditt lokala nätverk via en Azure VPN-gateway (om ditt virtuella nätverk är anslutet till ditt lokala nätverk) via BGP (Border Gateway Protocol) är de effektiva vägarna för alla nätverks gränssnitt i ett undernät. Du kan se dessa effektiva vägar genom att gå till NIC via portal, PowerShell eller CLI.
### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
De effektiva säkerhets regler som tillämpas på ett nätverks gränssnitt är en agg regering av de regler som finns i NSG som är kopplade till ett nätverks gränssnitt och under nätet som nätverks gränssnittet finns i. Du kan visa alla gällande säkerhets regler från NSG: er som tillämpas på din virtuella dators nätverks gränssnitt genom att gå till NÄTVERKSKORTet via portal, PowerShell eller CLI.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Virtual Network][VNet].

Läs mer om [routning av virtuella nätverk][vnet-routing].

Lär dig mer om [nätverks säkerhets grupper][network-security].

<!--Link References-->
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[vnet-routing]: ../virtual-network/virtual-networks-udr-overview.md
[network-security]: ../virtual-network/network-security-groups-overview.md