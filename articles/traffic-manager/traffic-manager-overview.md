---
title: Azure Traffic Manager | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Traffic Manager. Ta reda på om det är rätt val för belastnings utjämning av användar trafik för ditt program.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 8c8897218b153c8584c89abab98934268ccd555d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182170"
---
# <a name="what-is-traffic-manager"></a>Vad är Traffic Manager?
Azure Traffic Manager är en DNS-baserad trafikbelastnings utjämning. Med den här tjänsten kan du distribuera trafik till dina offentliga program i globala Azure-regioner. Traffic Manager också tillhandahålla dina offentliga slut punkter med hög tillgänglighet och snabb svars tid.

Traffic Manager använder DNS för att dirigera klient begär anden till lämplig tjänst slut punkt baserat på en metod för trafik dirigering. Traffic Manager tillhandahåller också hälso övervakning för varje slut punkt. Slut punkten kan vara valfri Internet-riktad tjänst som finns i eller utanför Azure. Traffic Manager tillhandahåller en uppsättning [trafikdirigeringsmetoder](traffic-manager-routing-methods.md) och [alternativ för slutpunktsövervakning](traffic-manager-monitoring.md) som passar olika programbehov och modeller för automatisk redundansväxling. Traffic Manager har bra återhämtningsförmåga i händelse av fel, inklusive fel som påverkar en hel Azure-region.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. 
> * Om du vill belastningsutjämna mellan servrarna i en region i program lagret granskar du [Application Gateway](../application-gateway/overview.md).
> * Om du behöver optimera en global routning av din webb trafik och optimera prestanda och tillförlitlighet på toppnivå med hjälp av snabb global redundans, se [front dörren](../frontdoor/front-door-overview.md).
> * Om du vill använda belastnings utjämning för nätverks lager granskar du [Load Balancer](../load-balancer/load-balancer-overview.md). 
> 
> Dina scenarier från slut punkt till slut punkt kan dra nytta av att kombinera de här lösningarna efter behov.
> En alternativ jämförelse för Azure-belastnings utjämning finns i [Översikt över belastnings Utjämnings alternativ i Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

Traffic Manager erbjuder följande funktioner:

## <a name="increase-application-availability"></a>Öka tillgängligheten för program

Traffic Manager tillhandahåller hög tillgänglighet för dina verksamhetskritiska program med slutpunktsövervakning och automatisk redundansväxling om en slutpunkt kraschar.
    
## <a name="improve-application-performance"></a>Förbättra programprestanda

Med Azure kan du köra moln tjänster och webbplatser i Data Center som ligger runt om i världen. Traffic Manager kan förbättra svars tiden för din webbplats genom att dirigera trafiken till slut punkten med den lägsta svars tiden.

## <a name="service-maintenance-without-downtime"></a>Service underhåll utan drift avbrott

Du kan ha planerat underhåll som gjorts i dina program utan drift stopp. Traffic Manager kan dirigera trafiken till alternativa slutpunkter under underhållsperioden.

## <a name="combine-hybrid-applications"></a>Kombinera hybridprogram

Traffic Manager stöder externa slutpunkter utanför Azure, vilket gör att lösningen kan användas i distributioner med hybridmoln och med lokala distributioner, inklusive ”[burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”-, ”migrate-to-cloud”- och ”failover-to-cloud”-scenarier.

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuera trafik för komplexa distributioner

Genom att använda [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md) kan du kombinera flera trafikdirigeringsmetoder för att skapa avancerade och flexibla regler som uppfyller behoven i större och mer komplexa distributioner.

## <a name="pricing"></a>Prissättning

Prisinformation finns på sidan med [prisinformation för Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en Traffic Manager-profil](./quickstart-create-traffic-manager-profile.md).
- Lär dig [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).
- Läs [vanliga frågor och svar](traffic-manager-FAQs.md) om Traffic Manager.