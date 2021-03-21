---
title: Azure frontend-belastnings utjämning med Azures program leverans Suite | Microsoft Docs
description: Den här artikeln hjälper dig att lära dig hur Azure rekommenderar belastnings utjämning med sin Application Delivery Suite
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 50e047325ad17710794b1640715ab1938373fe85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019471"
---
# <a name="load-balancing-with-azures-application-delivery-suite"></a>Belastningsutjämning med Azures paket för programleverans

## <a name="introduction"></a>Introduktion
Microsoft Azure tillhandahåller olika globala och regionala tjänster för att hantera hur nätverks trafiken distribueras och belastnings utjämning: 

* Application Gateway
* Front Door 
* Load Balancer  
* Traffic Manager

Tillsammans med Azures många regioner och zonindelade-arkitektur kan du med hjälp av dessa tjänster tillsammans skapa robusta, skalbara och högpresterande program.

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure1.png" alt-text="Application Delivery Suite":::
 
Dessa tjänster är uppdelade i två kategorier:
1. **Globala belastnings Utjämnings tjänster** som Traffic Manager och front dörr distribuerar trafik från dina slutanvändare över dina regionala Server delar, över moln och till och med dina hybrid lokala tjänster. Global belastnings utjämning dirigerar trafiken till närmaste tjänst Server del och reagerar på ändringar i tjänstens tillförlitlighet för att upprätthålla tillgänglighet och höga prestanda för dina användare. 
1. **Regionala belastnings Utjämnings tjänster** som belastningsutjämnare och programgatewayer ger möjlighet att distribuera trafik till virtuella datorer (VM) i ett virtuellt nätverk (virtuella nätverk) eller tjänst slut punkter inom en region.

När du kombinerar dessa globala och regionala tjänster drar programmet nytta av tillförlitlig och säker trafik från slut punkt till slut punkt som skickas från slutanvändarna till dina IaaS, PaaS eller lokala tjänster. I nästa avsnitt beskriver vi var och en av dessa tjänster.

## <a name="global-load-balancing"></a>Global belastnings utjämning
**Traffic Manager** tillhandahåller global belastnings utjämning för DNS. Den söker efter inkommande DNS-begäranden och svarar med en felfri Server del, efter den routningsprincip som kunden har valt. Alternativen för routningsmetoder är:
- * * Prestanda dirigering skickar begär anden till närmaste Server del med minsta latens.
- **Prioritera routning** dirigera all trafik till en server del med andra server delar som säkerhets kopiering.
- **Viktad** resursallokering distribuerar trafik baserat på den viktning som tilldelas varje server del.
- **Geografisk routning** garanterar att begär Anden som kommer från specifika geografiska regioner hanteras av Server delar som har mappats för dessa regioner. (Till exempel måste alla förfrågningar från Spanien dirigeras till den franska centrala Azure-regionen)
- Med hjälp av **under näts dirigering** kan du mappa IP-adressintervall till Server delar så att inkommande begär Anden för dessa IP-adresser skickas till den angivna Server delen. (Till exempel kan alla användare som ansluter från företagets HQ IP-adressintervall Hämta annat webb innehåll än allmänna användare)

Klienten ansluter direkt till Server delen. Azure Traffic Manager identifierar när en server del inte är felfri och omdirigerar sedan klienterna till en annan felfria instans. Mer information om tjänsten hittar du i [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) -dokumentationen.

**Azures front dörr** tillhandahåller en dynamisk webbplats acceleration (DSA), inklusive global http-belastnings utjämning.  Det ser till att inkommande HTTP-begäranden dirigeras till närmaste tjänst Server del/region för angivet värdnamn, URL-sökväg och konfigurerade regler.  
Frontend-dörren avslutar HTTP-begäranden på kanten av Microsofts nätverk och aktivt avsökningar för att identifiera program-eller infrastrukturens hälso tillstånd eller fördröjnings ändringar.  Front dörren dirigerar sedan alltid trafiken till Server delen snabbast och tillgänglig (felfri). Läs mer om tjänsten [routnings arkitektur](front-door-routing-architecture.md) och [trafikroutnings metoder](front-door-routing-methods.md) om du vill veta mer om tjänsten.

## <a name="regional-load-balancing"></a>Regional belastnings utjämning
Application Gateway tillhandahåller program leverans kontroll (ADC) som en tjänst som erbjuder olika Layer 7 belastnings Utjämnings funktioner för ditt program. Det gör att kunderna kan optimera webb server gruppens produktivitet genom att avlasta CPU-intensiva TLS-avslutning till programgatewayen. Andra ytterligare funktioner för Layer 7-routning omfattar även resursallokering-distribution av inkommande trafik, cookie-baserad sessionsgräns, URL-sökväg baserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda Programgateway. Application Gateway kan konfigureras som en Internetbaserad slut punkt, en intern slut punkt eller en kombination av båda. Application Gateway är helt Azure-hanterad, vilket ger dig skalbarhet och hög tillgänglighet. För att få en bättre hantering ingår en omfattande uppsättning diagnostik- och loggningsfunktioner.

Belastnings utjämning är en integrerad del av Azure SDN-stacken, som ger dig med högpresterande Layer 4-belastnings Utjämnings tjänster med låg latens för alla UDP-och TCP-protokoll. Du kan konfigurera offentliga eller interna belastningsutjämnade slut punkter genom att definiera regler som mappar inkommande anslutningar till Server dels pooler. Med hälso-/Bing-övervakning med TCP eller HTTPS kan det hjälpa dig att hantera tjänstens tillgänglighet.

## <a name="choosing-a-global-load-balancer"></a>Välja en global belastningsutjämnare
När du väljer en global belastningsutjämnare mellan Traffic Manager och Azure-frontend för global routning bör du fundera på vad som är detsamma och vad som är detsamma om de två tjänsterna.   Båda tjänsterna tillhandahåller
- **Multi-GEO-redundans:** Om en region går ur drift dirigerar trafiken sömlöst till den närmaste regionen utan att någon åtgärd från program ägaren.
- **Närmaste regions dirigering:** Trafiken dirigeras automatiskt till den närmaste regionen

</br>I följande tabell beskrivs skillnaderna mellan Traffic Manager och Azures frontend-dörr:</br>

| Traffic Manager | Azure Front Door |
| --------------- | ------------------------ |
|**Valfritt protokoll:** Eftersom Traffic Manager fungerar i DNS-skiktet, kan du dirigera vilken typ av nätverks trafik som helst. HTTP, TCP, UDP och så vidare. | **Http-acceleration:** Med front dörren är trafiken till proxy i Microsoft Networks kant. HTTP/S-begäranden ser förbättringar av svars tid och data flöde, vilket minskar svars tiden för TLS-förhandling.|
|**Lokal routning:** Med routning på ett DNS-lager går trafiken alltid från punkt till plats.  Routning från ditt avdelnings kontor till ditt lokala data Center kan ta en direkt sökväg. även på ditt eget nätverk med Traffic Manager. | **Oberoende skalbarhet:** Eftersom front dörren fungerar med HTTP-begäran kan begär anden till olika URL-sökvägar dirigeras till olika Server delar/regionala tjänst pooler (mikrotjänster) baserat på regler och hälsan för varje program mikrotjänst.|
|**Fakturerings format:** DNS-baserade fakturerings skalor med dina användare och för tjänster med fler användare, plateaus för att minska kostnaderna vid högre användning. |**Infogad säkerhet:** Front dörren gör det möjligt att använda regler som hastighets begränsning och IP ACL-ing för att skydda dina Server delar innan trafiken når ditt program. 

</br>Vi rekommenderar att kunderna använder front dörren för sin HTTP-arbets belastning på grund av de prestanda, Operability och säkerhets fördelar som HTTP fungerar med front dörren. Traffic Manager och front dörren kan användas parallellt för att hantera all trafik för ditt program. 

## <a name="building-with-azures-application-delivery-suite"></a>Skapa med Azures Application Delivery Suite 
Vi rekommenderar att alla webbplatser, API: er och tjänster är geografiskt redundanta så att de kan leverera trafik till användare från den närmaste platsen närhelst det är möjligt.  Genom att kombinera flera belastnings Utjämnings tjänster kan du bygga geografisk och zonindelade redundans för att maximera tillförlitlighet och prestanda.

I följande diagram beskriver vi en exempel arkitektur som använder en kombination av de här tjänsterna för att bygga en global webb tjänst. Arkitekten använde Traffic Manager för att dirigera trafik till globala Server delar för fil-och objekt leverans. När du använder en front dörr, för att globalt dirigera URL-sökvägar som matchar mönstret/Store/* till den tjänst som de har migrerat till App Service. Till sist dirigerar alla andra förfrågningar till regionala programgatewayer.

I varje region av IaaS-tjänsten har programutvecklaren beslutat att alla URL: er som matchar mönstret/images/* erhålls från en dedikerad pool av virtuella datorer. Den här poolen av virtuella datorer skiljer sig från resten av webb server gruppen.

Dessutom måste standard-VM-poolen som hanterar det dynamiska innehållet kommunicera med en backend-databas som finns i ett kluster med hög tillgänglighet. Hela distributionen konfigureras via Azure Resource Manager.

Följande diagram visar arkitekturen för det här scenariot:

:::image type="content" source="./media/front-door-lb-with-azure-app-delivery-suite/application-delivery-figure2.png" alt-text="Detaljerad arkitektur för Application Delivery Suite":::

> [!NOTE]
> Det här exemplet är bara en av många möjliga konfigurationer av de belastnings Utjämnings tjänster som Azure erbjuder. Traffic Manager, front dörren, Application Gateway och Load Balancer kan blandas och matchas mot bästa möjliga belastnings Utjämnings behov. Om t. ex. TLS/SSL-avlastning eller Layer 7-bearbetning inte behövs, kan Load Balancer användas i stället för Application Gateway.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
