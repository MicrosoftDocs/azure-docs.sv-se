---
title: Belastningsutjämnare för flera regioner (för hands version)
titleSuffix: Azure Load Balancer
description: Översikt över belastnings Utjämnings nivån på flera regioner för Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: allensu
ms.custom: references_regions
ms.openlocfilehash: 8a2ad6cea4ab97742261b1cfd84975b243a46e87
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938723"
---
# <a name="cross-region-load-balancer-preview"></a>Belastningsutjämnare för flera regioner (för hands version)

Azure Load Balancer distribuerar inkommande trafik som anländer till klient delen för belastningsutjämnare till instanser av backend-poolen.

Azure Standard Load Balancer stöder belastnings utjämning mellan regioner som aktiverar geo-redundanta behovs scenarier som:

* Inkommande trafik som härstammar från flera regioner.
* [Omedelbar global redundans](#regional-redundancy) till nästa optimala regionala distribution.
* Belastnings fördelning mellan regioner till närmaste Azure-region med [Ultra latens](#ultra-low-latency).
* Möjlighet att [skala upp/ned](#ability-to-scale-updown-behind-a-single-endpoint) efter en enda slut punkt.
* [Statisk IP](#static-ip)
* [IP-konservering för klienter](#client-ip-preservation)
* [Bygg en befintlig lösning för belastnings utjämning](#build-cross-region-solution-on-existing-azure-load-balancer) utan inlärnings kurva

> [!IMPORTANT]
> Belastningsutjämnare för flera regioner är för närvarande en för hands version och dold i för hands versionen av portalen. Logga in för **https://preview.portal.azure.com** att visa och distribuera funktionen.. </br> </br>
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Belastnings utjämning mellan regioner ger samma fördelar med hög prestanda och låg latens som regional standard Load Balancer. 

Klient delens IP-konfiguration för belastningsutjämnaren i flera regioner är statisk och annonserad i [de flesta Azure-regioner](#participating-regions).

:::image type="content" source="./media/cross-region-overview/cross-region-load-balancer.png" alt-text="Diagram över belastnings utjämning i flera regioner." border="true":::

> [!NOTE]
> Server dels porten i belastnings Utjämnings regeln i en belastningsutjämnare för flera regioner bör matcha frontend-porten för belastnings Utjämnings regeln/inkommande NAT-regel i regional standard Load Balancer. 

### <a name="regional-redundancy"></a>Regional redundans

Konfigurera regional redundans genom att lägga till en offentlig IP-adress för en global klient del i dina befintliga belastningsutjämnare. 

Om en region Miss lyckas dirigeras trafiken till nästa närmaste felfria regionala belastnings utjämning.  

Hälso avsökningen av belastningsutjämnaren i flera regioner samlar in information om tillgänglighet var 20: e sekund. Om en regional belastningsutjämnare släpper sin tillgänglighet till 0, kommer belastnings utjämning mellan regioner att identifiera problemet. Den regionala belastningsutjämnaren tas sedan bort från rotationen. 

:::image type="content" source="./media/cross-region-overview/global-region-view.png" alt-text="Diagram över vyn trafik för global region." border="true":::

### <a name="ultra-low-latency"></a>Extremt låg latens

Den geo-närhet algoritmen för belastnings utjämning baseras på användarens geografiska plats och dina regionala distributioner. 

Trafik som startas från en klient kommer att hamna närmast deltagande region och resa genom Microsofts globala nätverkets stamnät för att komma till den närmaste regionala distributionen. 

Till exempel har du en belastningsutjämnare för flera regioner med standard belastnings utjämning i Azure-regioner:

* USA, västra
* Norra Europa

Om ett flöde startas från Seattle, kommer trafik att komma in västra USA. Den här regionen är den närmast deltagande regionen från Seattle. Trafiken dirigeras till den närmaste regionens belastningsutjämnare, som är västra USA.

Belastnings utjämning i Azure i flera regioner använder geo-närhet för att dirigera beslutet. 

Det konfigurerade belastnings distributions läget för regionala belastningsutjämnare används för att fatta det slutliga beslutet om routning när flera regionala belastnings utjämning används för geo-närhet.

Mer information finns i [Konfigurera distributions läge för Azure Load Balancer](./load-balancer-distribution-mode.md).


### <a name="ability-to-scale-updown-behind-a-single-endpoint"></a>Möjlighet att skala upp/ned efter en enda slut punkt

När du exponerar en global slut punkt för en belastningsutjämnare i flera regioner till kunder kan du lägga till eller ta bort regionala distributioner bakom den globala slut punkten utan kund påverkan. 

<!---To learn about how to add or remove a regional deployment from the backend, read more [here](TODO: Insert CLI doc here).--->

### <a name="static-ip"></a>Statisk IP
Belastningsutjämnare för flera regioner levereras med en statisk offentlig IP-adress, som ser till att IP-adressen förblir densamma. Läs mer om statisk IP [här](../virtual-network/public-ip-addresses.md#allocation-method)

### <a name="client-ip-preservation"></a>IP-konservering för klienter
Belastnings utjämning mellan regioner är en Layer-4-vidarekoppling av nätverks belastning. Det här direkt bevarar paketets ursprungliga IP-adress.  Den ursprungliga IP-adressen är tillgänglig för den kod som körs på den virtuella datorn. Med det här skyddet kan du tillämpa logik som är speciell för en IP-adress.

## <a name="build-cross-region-solution-on-existing-azure-load-balancer"></a>Bygg lösningen för flera regioner på befintliga Azure Load Balancer
Backend-poolen för belastningsutjämnare i flera regioner innehåller en eller flera regionala belastnings utjämning. 

Lägg till dina befintliga belastnings Utjämnings distributioner i en belastningsutjämnare för flera regioner för en hög tillgänglig distribution över flera regioner.

**Hem regionen** är den plats där belastningsutjämnaren för flera regioner har distribuerats. Den här regionen påverkar inte hur trafiken kommer att dirigeras. Om en hem region kraschar, påverkar det inte trafikflödet.

### <a name="home-regions"></a>Hem regioner
* USA, östra 2
* USA, västra
* Europa, västra
* Sydostasien
* Central US
* Norra Europa
* Asien, östra

> [!NOTE]
> Du kan bara distribuera en belastningsutjämnare för flera regioner i någon av de åtta regionerna ovan.

En **deltagande region** är den plats där den globala offentliga IP-adressen för belastningsutjämnaren är tillgänglig. 

Trafik som startas av användaren överförs till den närmast deltagande regionen via Microsoft Core-nätverket. 

Belastnings utjämning mellan regioner dirigerar trafiken till lämplig regional belastningsutjämnare.

### <a name="participating-regions"></a>Deltagande regioner
* East US 
* Europa, västra 
* USA, centrala 
* USA, östra 2 
* USA, västra 
* Norra Europa 
* USA, södra centrala 
* USA, västra 2 
* Storbritannien, södra 
* Sydostasien 
* USA, norra centrala 
* Japan, östra 
* Asien, östra 
* USA, västra centrala 
* Australien, sydöstra 
* Australien, östra 
* Indien, centrala 

## <a name="limitations"></a>Begränsningar

* IP-konfigurationer för klient delen för flera regioner är endast offentliga. Det finns för närvarande inte stöd för en intern klient del.

* Det går inte att lägga till en privat eller intern belastningsutjämnare i backend-poolen för Cross-regional belastningsutjämnare 

* IP-konfigurationer för IPv6-frontend i flera regioner stöds inte. 

* Det går inte att konfigurera en hälso avsökning för närvarande. En standard hälso avsökning samlar automatiskt in tillgänglighets information om den regionala belastningsutjämnaren var 20: e sekund. 

## <a name="pricing-and-sla"></a>Priser och service nivå avtal
Belastnings utjämning för flera regioner delar [service avtalet](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/ ) för standard Load Balancer.

 
## <a name="next-steps"></a>Nästa steg

- Se [Självstudier: skapa en belastningsutjämnare för flera regioner med hjälp av Azure Portal](tutorial-cross-region-portal.md) för att skapa en belastningsutjämnare för flera regioner.
- Se [skapa en offentlig standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) för att skapa en standard regional belastningsutjämnare.
- Läs mer om [Azure Load Balancer](load-balancer-overview.md).
