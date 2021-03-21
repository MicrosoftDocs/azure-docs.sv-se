---
title: Azure Load Balancer och Tillgänglighetszoner
titleSuffix: Azure Load Balancer
description: Med den här utbildnings vägen kommer du igång med Azure Standard Load Balancer och Tillgänglighetszoner.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 3c18b6d8dc44762649a9c07b88af348a18888fb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699121"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer och Tillgänglighetszoner

Azure Load Balancer stöder scenarier med tillgänglighets zoner. Du kan använda Standard Load Balancer för att öka tillgängligheten i hela scenariot genom att justera resurser med och distribution mellan zoner.  Granska det här dokumentet för att förstå dessa begrepp och design rikt linjer för grundläggande scenarier

En Load Balancer kan antingen vara **zon redundant, zonindelade** eller **icke-zonindelade**. Om du vill konfigurera relaterade egenskaper för zonen (som nämns ovan) för belastningsutjämnaren väljer du lämplig typ av klient.

## <a name="zone-redundant"></a>Zonen är redundant

I en region med Tillgänglighetszoner kan en Standard Load Balancer vara zon-redundant. Den här trafiken hanteras av en enda IP-adress.

En IP-adress för en klient del kommer att överleva zon haveriet. Klient delens IP-adress kan användas för att få åtkomst till alla (icke-förbrukade) Server dels medlemmar oavsett zon. En eller flera tillgänglighets zoner kan inte köras och data Sök vägen finns kvar så länge en zon i regionen är felfri.

Klient delens IP-adress hanteras samtidigt av flera oberoende infrastruktur distributioner i flera tillgänglighets zoner. Eventuella återförsök eller återupprättade kommer att lyckas i andra zoner som inte påverkas av zon felen.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Bild: zon redundant belastningsutjämnare*

## <a name="zonal"></a>Zonindelade

Du kan välja att en klient del garanteras för en enda zon, som kallas en *zonindelade*.  Det här scenariot innebär att alla inkommande eller utgående flöden betjänas av en enda zon i en region.  Din klient del delar i nedbrytningen med zonens hälsa.  Data Sök vägen påverkas inte av andra problem i andra zoner än de som har garanterats. Du kan använda zonindelade-frontend-klienter för att exponera en IP-adress per tillgänglighets zon.  

Dessutom stöds användningen av zonindelade-frontend direkt för belastningsutjämnade slut punkter i varje zon. Du kan använda den här konfigurationen för att exponera belastnings Utjämnings slut punkter per zon för att övervaka varje zon individuellt. För offentliga slut punkter kan du integrera dem med en DNS-belastnings Utjämnings produkt som [Traffic Manager](../traffic-manager/traffic-manager-overview.md) och använda ett enda DNS-namn.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Bild: zonindelade Load Balancer*

För en offentlig belastningsutjämnare-frontend lägger du till en **zon** parameter till den offentliga IP-adressen. Den här offentliga IP-adressen refereras till av klient delens IP-konfiguration som används av respektive regel.

För en intern belastningsutjämnare-frontend lägger du till en **zon** parameter till den interna belastnings Utjämnings-IP-konfigurationen. En zonindelade-frontend garanterar en IP-adress i ett undernät till en speciell zon.

## <a name="design-considerations"></a><a name="design"></a> Design överväganden

Nu när du förstår zonens relaterade egenskaper för Standard Load Balancer kan följande design aspekter hjälpa dig när du utformar för hög tillgänglighet.

### <a name="tolerance-to-zone-failure"></a>Tolerans för zon haveriering

- En **redundant zon** Load Balancer kan hantera en zonindelade-resurs i valfri zon med en IP-adress.  IP-adressen kan överleva en eller flera zon fel förutsatt att minst en zon fortfarande är felfri i regionen.
- En **zonindelade** -frontend är en minskning av tjänsten till en enda zon och delas med respektive zon. Om den zon som distributionen finns i slutar fungera, kommer distributionen inte att överleva det här felet.

Vi rekommenderar att du använder zonens redundanta Load Balancer för dina produktions arbets belastningar.

### <a name="control-vs-data-plane-implications"></a>Effekter av kontroll vs data plan

Zon-redundans innebär inte hitless data plan eller kontroll plan. Zoner – redundanta flöden kan använda valfri zon och dina flöden använder alla felfria zoner i en region. I ett zon fel påverkas inte trafik flöden som använder felfria zoner.

Trafik flöden som använder en zon vid tidpunkten för zon fel kan påverkas, men program kan återställas. Trafiken fortsätter i de friska zonerna i regionen när den återsänds när Azure har konvergerat runt zon felet.

Gå igenom [design mönster för molnet i molnet](/azure/architecture/patterns/) för att förbättra återhämtningen hos ditt program till haveri situationer.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Tillgänglighetszoner](../availability-zones/az-overview.md)
- Läs mer om [standard Load Balancer](./load-balancer-overview.md)
- Lär dig att [belastningsutjämna virtuella datorer inom en zon med hjälp av ett zonindelade-standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md)
- Lär dig att [belastningsutjämna virtuella datorer över zoner med hjälp av en zon redundant standard Load Balancer](./quickstart-load-balancer-standard-public-cli.md)
- Lär dig mer om [moln design mönster i Azure](/azure/architecture/patterns/) för att förbättra programmets återhämtnings förmåga till haveri situationer.
