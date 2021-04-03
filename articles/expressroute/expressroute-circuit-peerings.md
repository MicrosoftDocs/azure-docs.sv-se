---
title: 'Azure-ExpressRoute: kretsar och peering'
description: Den här sidan innehåller en översikt över ExpressRoute-kretsar och routningsdomäner/peering.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: 87fed1d2ac4f5fa85c01d7af10bec10c1412744f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202352"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute-kretsar och peering

ExpressRoute-kretsar ansluter din lokala infrastruktur till Microsoft via en anslutnings leverantör. Den här artikeln hjälper dig att förstå ExpressRoute-kretsar och routningsdomäner/peering. Följande bild visar en logisk representation av anslutningen mellan WAN och Microsoft.

![Diagram över hur ExpressRoute-kretsar ansluter din lokala infrastruktur till Microsoft via en anslutnings leverantör.](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azures offentliga peering är inaktuell och är inte tillgänglig för nya ExpressRoute-kretsar. Nya kretsar stöder Microsoft-peering och privat peering.  
>

## <a name="expressroute-circuits"></a><a name="circuits"></a>ExpressRoute-kretsar

En ExpressRoute-krets representerar en logisk anslutning mellan din lokala infrastruktur och Microsofts moln tjänster via en anslutnings leverantör. Du kan beställa flera ExpressRoute-kretsar. Varje krets kan finnas i samma eller olika regioner och kan anslutas till dina lokaler via olika anslutnings leverantörer.

ExpressRoute-kretsar mappar inte till några fysiska entiteter. En krets identifieras unikt av ett standard-GUID som kallas för en tjänst nyckel (s-Key). Tjänst nyckeln är den enda informations enhet som utbyts mellan Microsoft, anslutnings leverantören och dig. S-nyckeln är inte en hemlighet av säkerhets synpunkt. Det finns en 1:1-mappning mellan en ExpressRoute-krets och s-nyckeln.

Nya ExpressRoute-kretsar kan omfatta två oberoende peering: privat peering och Microsoft-peering. Befintliga ExpressRoute-kretsar kan innehålla tre peer-datorer: Azure offentlig, Azure Private och Microsoft. Varje peering är ett par oberoende BGP-sessioner, som var och en har kon figurer ATS redundant för hög tillgänglighet. Det finns en 1: N (1 <= N <= 3) mappning mellan en ExpressRoute-krets och routningsdomäner. En ExpressRoute-krets kan ha en, två eller alla tre peering-aktiverade per ExpressRoute-krets.

Varje krets har en fast bandbredd (50 Mbit/s, 100 Mbit/s, 200 Mbit/s, 500 Mbps, 1 Gbit/s, 10 Gbit/s) och mappas till en anslutnings leverantör och en peering-plats. Bandbredden som du väljer delas mellan alla krets-peering

### <a name="quotas-limits-and-limitations"></a><a name="quotas"></a>Kvoter, gränser och begränsningar

Standard kvoter och-gränser gäller för varje ExpressRoute-krets. Se sidan för [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) för uppdaterad information om kvoter.

## <a name="expressroute-peering"></a><a name="routingdomains"></a>ExpressRoute-peering

En ExpressRoute-krets har flera associerade routningsdomäner/peering-kopplingar: Azure offentlig, Azure Private och Microsoft. Varje peering konfigureras identiskt på ett par routrar (i aktiv-aktiv eller belastnings delnings konfiguration) för hög tillgänglighet. Azure-tjänster kategoriseras som *offentliga Azure* -tjänster och *Azure Private* för att representera IP-adressering-scheman.

![Diagram över hur Azures offentliga, Azure Private-och Microsoft-peering konfigureras i en ExpressRoute-krets.](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="azure-private-peering"></a><a name="privatepeering"></a>Privat peering i Azure

Azure Compute Services, nämligen Virtual Machines (IaaS) och Cloud Services (PaaS), som distribueras i ett virtuellt nätverk kan anslutas via den privata peering-domänen. Den privata peering-domänen anses vara ett betrott tillägg till kärn nätverket i Microsoft Azure. Du kan konfigurera dubbelriktad anslutning mellan kärn nätverket och virtuella Azure-nätverk (virtuella nätverk). Med denna peering kan du ansluta till virtuella datorer och moln tjänster direkt på deras privata IP-adresser.  

Du kan ansluta fler än ett virtuellt nätverk till den privata peering-domänen. På [sidan med vanliga frågor och svar](expressroute-faqs.md) hittar du information om begränsningar och begränsningar. Du kan gå till sidan för [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md) för uppdaterad information om begränsningar.  På sidan [routning](expressroute-routing.md) hittar du detaljerad information om konfiguration av routning.

### <a name="microsoft-peering"></a><a name="microsoftpeering"></a>Microsoft-peering

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Anslutning till Microsoft onlinetjänster (Microsoft 365 och Azure PaaS Services) sker via Microsoft-peering. Vi aktiverar dubbelriktad anslutning mellan dina WAN-och Microsofts moln tjänster via Microsoft-peering-routningsdomänen. Du måste bara ansluta till Microsofts moln tjänster över offentliga IP-adresser som ägs av dig eller anslutnings leverantören och du måste följa alla definierade regler. Mer information finns på sidan med [ExpressRoute-krav](expressroute-prerequisites.md) .

På [sidan med vanliga frågor och svar](expressroute-faqs.md) finns mer information om tjänster som stöds, kostnader och konfigurations information. På sidan [ExpressRoute locations](expressroute-locations.md) hittar du information om listan över anslutnings leverantörer som erbjuder stöd för Microsoft-peering.

## <a name="peering-comparison"></a><a name="peeringcompare"></a>Jämförelse av peering

I följande tabell jämförs de tre peer kopplingarna:

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

Du kan aktivera en eller flera av routningsdomänen som en del av din ExpressRoute-krets. Du kan välja att låta alla routningsdomäner placeras på samma VPN om du vill kombinera dem i en enda routningsdomän. Du kan också ställa in dem på olika routningsdomäner, ungefär som i diagrammet. Den rekommenderade konfigurationen är att privat peering är anslutet direkt till kärn nätverket och att länkarna för offentliga och Microsoft-peering är anslutna till din DMZ.

Varje peering kräver separata BGP-sessioner (ett par för varje peering-typ). BGP-session par innehåller en länk med hög tillgänglighet. Om du ansluter via Layer 2 anslutnings leverantörer ansvarar du för att konfigurera och hantera routning. Du kan lära dig mer genom att granska [arbets flöden](expressroute-workflows.md) för att konfigurera ExpressRoute.

## <a name="expressroute-health"></a><a name="health"></a>ExpressRoute-hälsa

ExpressRoute-kretsar kan övervakas för tillgänglighet, anslutning till virtuella nätverk och bandbredds användning med [övervakare av nätverksprestanda](../networking/network-monitoring-overview.md) (NPM).

NPM övervakar hälsan för Azures privata peering och Microsoft-peering. Se vår [post](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) för mer information.

## <a name="next-steps"></a>Nästa steg

* Hitta en tjänstleverantör. Se [ExpressRoute-tjänst leverantörer och platser](expressroute-locations.md).
* Kontrollera att alla krav är uppfyllda. Se [ExpressRoute-krav](expressroute-prerequisites.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa och hantera ExpressRoute-kretsar](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurera routning (peering) för ExpressRoute-kretsar](expressroute-howto-routing-portal-resource-manager.md)