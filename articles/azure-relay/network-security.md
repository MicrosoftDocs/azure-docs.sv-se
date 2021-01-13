---
title: Nätverks säkerhet för Azure Relay
description: I den här artikeln beskrivs hur du använder IP-brandväggs regler och privata slut punkter med Azure Relay.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 608224f42fac8cd2d8ff06ab84989b1f675d418c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134474"
---
# <a name="network-security-for-azure-relay"></a>Nätverks säkerhet för Azure Relay 
I den här artikeln beskrivs hur du använder följande säkerhetsfunktioner med Azure Relay: 

- IP-brandväggs regler (för hands version)
- Privata slutpunkter 

> [!NOTE]
> Azure Relay stöder inte nätverks tjänstens slut punkter. 


## <a name="ip-firewall"></a>IP-brandvägg 
Som standard är relä namn rymder tillgängliga från Internet så länge förfrågan levereras med giltig autentisering och auktorisering. Med IP-brandvägg kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adress intervall i [CIDR-notation (classless Inter-Domain routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Den här funktionen är användbar i scenarier där Azure Relay bör endast vara tillgängliga från vissa välkända webbplatser. Med brand Väggs regler kan du konfigurera regler för att acceptera trafik som kommer från vissa IPv4-adresser. Om du till exempel använder relä med [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), kan du skapa en **brand Väggs regel** som tillåter trafik från enbart lokala infrastruktur-IP-adresser. 

IP-brandväggens regler tillämpas på relä namn områdes nivå. Reglerna gäller därför för alla anslutningar från klienter som använder ett protokoll som stöds. Alla anslutnings försök från en IP-adress som inte matchar en tillåten IP-regel i relä namn området avvisas som obehörig. Svaret innehåller ingen IP-regel. IP filter regler tillämpas i ordning och den första regeln som matchar IP-adressen avgör vilken åtgärd som godkänns eller nekas.

Mer information finns i [så här konfigurerar du IP-brandvägg för ett relä namn område](ip-firewall-virtual-networks.md)

> [!NOTE]
> Den här funktionen är för närvarande en för **hands version**. 

## <a name="private-endpoints"></a>Privata slutpunkter

Azure **Private Link service** ger dig åtkomst till Azure-tjänster (till exempel Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage och Azure Cosmos dB) och Azure-värdbaserade kund-/partner tjänster via en privat slut punkt i det virtuella nätverket. Mer information finns i [Vad är en privat Azure-länk?](../private-link/private-link-overview.md)

En **privat slut punkt** är ett nätverks gränssnitt som gör att dina arbets belastningar som körs i ett virtuellt nätverk kan ansluta privat och säkert till en tjänst som har en **privat länk resurs** (till exempel ett relä namn område). Den privata slut punkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt ansluter tjänsten till ditt VNet. All trafik till tjänsten kan dirigeras via den privata slut punkten, så inga gatewayer, NAT-enheter, ExpressRoute, VPN-anslutningar eller offentliga IP-adresser krävs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsoft stamnätet-nätverket som eliminerar exponering från det offentliga Internet. Du kan ange en nivå av granularitet i åtkomst kontroll genom att tillåta anslutningar till vissa Azure Relay namn områden.

Mer information finns i [så här konfigurerar du privata slut punkter](private-link-service.md)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera IP-brandvägg](ip-firewall-virtual-networks.md)
- [Konfigurera privata slut punkter](private-link-service.md)
