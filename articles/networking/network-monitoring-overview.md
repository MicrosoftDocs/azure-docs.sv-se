---
title: Om nätverks övervakning i Azure Monitor loggar | Microsoft Docs
description: Översikt över lösningar för nätverks övervakning, inklusive NPM, för att hantera nätverk i molnet, lokalt och i hybrid miljöer.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: a92c6789723dc42ac8f3a9c471e494079dc19328
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233448"
---
# <a name="network-monitoring-solutions"></a>Lösningar för nätverks övervakning 

Azure erbjuder en värd med lösningar för att övervaka dina nätverks resurser. Azure har lösningar och verktyg för att övervaka nätverks anslutningen, hälsan för ExpressRoute-kretsar och analysera nätverks trafik i molnet.

## <a name="network-performance-monitor-npm"></a>Övervakare av nätverksprestanda (NPM)

Övervakare av nätverksprestanda (NPM) är en uppsättning funktioner, som var och en är inriktad på att övervaka nätverkets hälso tillstånd, nätverks anslutning till dina program och ger insikter om nätverkets prestanda. NPM är molnbaserad och tillhandahåller en hybrid nätverks övervaknings lösning som övervakar anslutningar mellan:
 
* Moln distributioner och lokala platser
* Flera data Center och avdelnings kontor
* Verksamhets kritiska program på flera nivåer/Micro-tjänster
* Användar platser och webbaserade program (HTTP/HTTPs) 

Prestanda övervakaren, ExpressRoute-övervakaren och tjänst anslutnings övervakaren övervakar funktioner i NPM och beskrivs nedan.

## <a name="performance-monitor"></a>Prestandaövervakaren

Prestanda övervakaren är en del av NPM och är nätverks övervakning för moln-, hybrid-och lokala miljöer. Du kan övervaka nätverks anslutningar över fjärranslutna förgreningar och fält kontor, lagrings platser, data Center och moln. Du kan identifiera nätverks problem innan användarna klagar. Viktiga fördelar är:

* Övervaka förlust och svars tid i olika undernät och Ställ in aviseringar
* Övervaka alla sökvägar (inklusive redundanta sökvägar) i nätverket
* Felsök tillfälliga och tidpunkts nätverks problem som är svåra att replikera
* Fastställ det speciella segmentet i nätverket, som ansvarar för försämrade prestanda
* Övervaka nätverkets hälso tillstånd utan behov av SNMP

![Karta över NPM-topologi](./media/network-monitoring-overview/npm-topology-map.png) 

Mer information finns i följande artiklar:

* [Konfigurera en Övervakare av nätverksprestanda-lösning i Azure Monitor loggar](../azure-monitor/insights/network-performance-monitor.md) 
* [Användningsfall](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)
* Produkt uppdateringar:
  * [Februari 2017](/archive/blogs/msoms/oms-network-performance-monitor-is-now-generally-available)
  * [Augusti 2017](/archive/blogs/msoms/improvements-to-oms-network-performance-monitor)

## <a name="expressroute-monitor"></a>Övervakaren ExpressRoute

NPM för ExpressRoute erbjuder omfattande ExpressRoute övervakning för Azures privata peering och Microsoft-peering-anslutningar. Du kan övervaka E2E-anslutningar och prestanda mellan dina avdelnings kontor och Azure över ExpressRoute. Huvud funktionerna är:

* Automatisk identifiering av ER-kretsar som är associerade med din prenumeration
* Identifiering av nätverkstopologi från lokal plats till dina moln program
* Kapacitets planering, analys av bandbredds användning
* Övervakning och aviseringar på både primära och sekundära sökvägar
* Övervaka anslutning till Azure-tjänster som Microsoft 365, Dynamics 365,... över ExpressRoute
* Identifiera försämring av anslutningen till virtuella nätverk

![Geo-karta som visar trafik över regioner](./media/network-monitoring-overview/expressroute-topology-map.png) 

Mer information finns i följande artiklar:

* [Konfigurera övervakare av nätverksprestanda för ExpressRoute](../expressroute/how-to-npm.md)
* [Blogg inlägg](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Övervakare av tjänstanslutning

Med övervakning av tjänst anslutning kan du nu testa nåbarhet för program och identifiera prestanda Flask halsar i lokala, transport företags nätverk och moln/privata data Center.

* Övervaka nätverks anslutningar från slut punkt till slut punkt för program
* Korrelera program leverans med nätverks prestanda, identifiera exakt plats för försämring längs sökvägen mellan användaren och programmet
* Testa program tillgänglighet från flera användar platser över hela världen
* Bestäm nätverks fördröjning och paket förlust för dina affärs-och SaaS-program
* Fastställa aktiva punkter i nätverket, som kan orsaka dåliga program prestanda
* Övervaka tillgängligheten till Microsoft 365 program med hjälp av inbyggda tester för Microsoft 365, Dynamics 365, Skype för företag och andra Microsoft-tjänster

Mer information finns i följande artiklar:

* [Konfigurera Övervakare av nätverksprestanda för övervakning av tjänst slut punkter](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Blogg inlägg](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Trafikanalys
Trafikanalys är en molnbaserad lösning som ger insyn i användar-och program aktivitet i moln nätverk. NSG flödes loggar analyseras för att ge insikter om:

* Trafik flödar över nätverk mellan Azure och Internet, offentliga moln regioner, virtuella nätverk och undernät
* Program och protokoll i nätverket, utan att behöva Sniffer eller dedikerade insamlings utrustning
* Främsta pratare, chattprogram, virtuella dator konversationer i molnet, trafik hotspots
* Källor och mål för trafik över virtuella nätverk, mellan relationer mellan viktiga företags tjänster och program
* Säkerhet – skadlig trafik, portar öppna för Internet, program eller virtuella datorer som försöker ansluta till Internet...
* Kapacitets användning – hjälper dig att undvika problem med överetablering eller under användning genom att övervaka användnings trender för VPN-gatewayer och andra tjänster

Trafikanalys förser dig med åtgärds bara information som hjälper dig att granska organisationens nätverks aktivitet, säkra program och data, optimera arbets Belastningens prestanda och hålla dig uppdaterad.

![Geo-karta som visar trafik i regioner 2](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Relaterade länkar:
* [Blogg inlägg](https://aka.ms/trafficanalytics), [dokumentation](../network-watcher/traffic-analytics.md), [vanliga frågor och svar](../network-watcher/traffic-analytics-faq.md)

## <a name="dns-analytics"></a>DNS-analys
Den här lösningen är byggd för DNS-administratörer och samlar in, analyserar och korrelerar DNS-loggar för att tillhandahålla säkerhet, åtgärder och prestanda relaterade insikter.  Några av funktionerna är:

* Identifiering av klienter som försöker matcha till skadliga domäner
* Identifiering av inaktuella resurs poster
* Insyn i domän namn som efter frågas och pratsam DNS-klienter
* Insyn i begär ande belastningen på DNS-servrar
* Övervakning av dynamiska DNS-registrerings fel

![DNS-analys instrument panel](./media/network-monitoring-overview/dns-analytics-overview.png) 

Relaterade länkar:
* [Blogginlägg](/archive/blogs/msoms/introducing-oms-dns-analytics), [Dokumentation](../azure-monitor/insights/dns-analytics.md)

## <a name="miscellaneous"></a>Övriga farliga ämnen

* [Ny prissättning](../azure-monitor/insights/network-performance-monitor-pricing-faq.md)