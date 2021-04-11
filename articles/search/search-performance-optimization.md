---
title: Tillgänglighet och kontinuitet
titleSuffix: Azure Cognitive Search
description: Lär dig hur du gör en Sök tjänst hög tillgänglig och elastisk mot period avbrott eller till och med oåterkalleliga fel.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: references_regions
ms.openlocfilehash: 493f6759f63f023572f38647076e04425acf9d6a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581531"
---
# <a name="availability-and-business-continuity-in-azure-cognitive-search"></a>Tillgänglighet och affärs kontinuitet i Azure Kognitiv sökning

I Kognitiv sökning uppnås tillgänglighet genom flera repliker, medan verksamhets kontinuitet (och haveri beredskap) uppnås via flera Sök tjänster. Den här artikeln innehåller rikt linjer som du kan använda som utgångs punkt för att utveckla en strategi som uppfyller dina affärs krav för både tillgänglighet och kontinuerlig drift.

<a name="scale-for-availability"></a>

## <a name="high-availability"></a>Hög tillgänglighet

I Kognitiv sökning är repliker kopior av ditt index. Med flera repliker kan Azure Kognitiv sökning starta om datorn och underhåll mot en replik, medan frågekörningen fortsätter på andra repliker. Mer information om hur du lägger till repliker finns i [lägga till eller minska repliker och partitioner](search-capacity-planning.md#adjust-capacity).

För varje enskild Sök tjänst garanterar Microsoft minst 99,9% tillgänglighet för konfigurationer som uppfyller följande kriterier: 

+ Två repliker för hög tillgänglighet för skrivskyddade arbets belastningar (frågor)

+ Tre eller flera repliker för hög tillgänglighet av Läs-och skriv arbets belastningar (frågor och indexering) 

Det finns inget service avtal för den kostnads fria nivån. Mer information finns i [SLA för Azure kognitiv sökning](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

<a name="availability-zones"></a>

## <a name="availability-zones"></a>Tillgänglighetszoner

[Tillgänglighetszoner](../availability-zones/az-overview.md) är en Azure-plattform som delar upp en regions Data Center i distinkta fysiska plats grupper för att tillhandahålla hög tillgänglighet inom samma region. Om du använder Tillgänglighetszoner för Kognitiv sökning, är enskilda repliker enheterna för zon tilldelning. En Sök tjänst körs inom en region. dess repliker körs i olika zoner.

Du kan använda Tillgänglighetszoner med Azure Kognitiv sökning genom att lägga till två eller fler repliker till din Sök tjänst. Varje replik kommer att placeras i en annan tillgänglighets zon inom regionen. Om du har fler repliker än Tillgänglighetszoner kommer replikerna att fördelas på Tillgänglighetszoner så jämnt som möjligt. Det finns ingen speciell åtgärd för din del, förutom att [skapa en Sök tjänst](search-create-service-portal.md) i en region som tillhandahåller Tillgänglighetszoner och sedan konfigurera tjänsten att [använda flera repliker](search-capacity-planning.md#adjust-capacity).

Azure Kognitiv sökning stöder för närvarande Tillgänglighetszoner för standard nivå eller högre Sök tjänster som har skapats i någon av följande regioner:

+ Östra Australien (skapad 30 januari 2021 eller senare)
+ Kanada, centrala (skapad 30 januari 2021 eller senare)
+ USA, centrala (skapad av 4 december 2020 eller senare)
+ USA, östra (skapad 27 januari 2021 eller senare)
+ USA, östra 2 (skapad 30 januari 2021 eller senare)
+ Centrala Frankrike (skapad den 23 oktober 2020 eller senare)
+ Japan, östra (skapad 30 januari 2021 eller senare)
+ Nord Europa (skapad 28 januari 2021 eller senare)
+ Asien, sydöstra (skapad 31 januari 2021 eller senare)
+ Storbritannien, södra (skapad 30 januari 2021 eller senare)
+ Europa, västra (skapad den 29 januari 2021 eller senare)
+ USA, västra 2 (skapad 30 januari 2021 eller senare)

Tillgänglighetszoner påverkar inte [Azure kognitiv sökning-serviceavtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Du behöver fortfarande tre eller fler repliker för att fråga hög tillgänglighet.

## <a name="multiple-services-in-separate-geographic-regions"></a>Flera tjänster i separata geografiska regioner

Även om de flesta kunder bara använder en tjänst kan det vara nödvändigt med tjänstredundans om de operativa kraven omfattar följande:

+ [Verksamhets kontinuitet och haveri beredskap (BCDR)](../best-practices-availability-paired-regions.md) (kognitiv sökning ger inte omedelbar redundansväxling i händelse av ett avbrott).
+ Globalt distribuerade program. Om frågor och indexerings begär Anden kommer från alla över hela världen, kommer användare som är närmast värd data centret att ha snabbare prestanda. Att skapa ytterligare tjänster i regioner med nära närhet till dessa användare kan utjämna prestanda för alla användare.
+ [Arkitekturer för flera innehavare](search-modeling-multitenant-saas-applications.md) anropar ibland för två eller flera tjänster.

Om du behöver två fler Sök tjänster kan du skapa dem i olika regioner för att uppfylla program kraven för kontinuitet och återställning, samt snabbare svars tider för en global användar bas.

Azure Kognitiv sökning tillhandahåller för närvarande inte en automatiserad metod för geo-replikering av Sök index över flera regioner, men det finns vissa tekniker som kan användas för att göra den här processen enkel att implementera och hantera. Dessa beskrivs i följande avsnitt.

Målet för en geo-distribuerad uppsättning Sök tjänster är att två eller fler index är tillgängliga i två eller flera regioner, där en användare dirigeras till den Azure Kognitiv sökning-tjänst som tillhandahåller den lägsta svars tiden:

   ![Globala flikar för tjänster efter region][1]

Du kan implementera den här arkitekturen genom att skapa flera tjänster och utforma en strategi för datasynkronisering. Du kan också ta med en resurs som Azure Traffic Manager for routing-begäranden. Mer information finns i [skapa en Sök tjänst](search-create-service-portal.md).

<a name="data-sync"></a>

### <a name="keep-data-synchronized-across-multiple-services"></a>Hålla data synkroniserade över flera tjänster

Det finns två alternativ för att hålla två eller fler distribuerade Sök tjänster synkroniserade, som består av antingen med hjälp av [azure kognitiv sökning-indexeraren](search-indexer-overview.md) eller push-API: et (kallas även [azure-kognitiv sökning REST API](/rest/api/searchservice/)). 

#### <a name="option-1-use-indexers-for-updating-content-on-multiple-services"></a>Alternativ 1: Använd indexerare för att uppdatera innehåll på flera tjänster

Om du redan använder indexerare på en tjänst kan du konfigurera en andra indexerare på en andra tjänst för att använda samma data käll objekt, hämta data från samma plats. Varje tjänst i varje region har sin egen indexerare och ett mål index (ditt Sök index är inte delat, vilket innebär att data är duplicerade), men varje indexerare refererar samma data källa.

Här är ett visuellt visuellt objekt av vad arkitekturen skulle se ut.

   ![Enkel data källa med distribuerade indexerare och tjänst kombinationer][2]

#### <a name="option-2-use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Alternativ 2: Använd REST-API: er för att överföra innehålls uppdateringar på flera tjänster

Om du använder Azure Kognitiv sökning REST API för att [skicka innehåll till ditt sökindex](tutorial-optimize-indexing-push-api.md), kan du behålla dina olika Sök tjänster synkroniserade genom att skicka ändringar till alla Sök tjänster när en uppdatering krävs. I din kod ser du till att hantera fall där en uppdatering av en Sök tjänst Miss lyckas men fungerar för andra Sök tjänster.

### <a name="use-azure-traffic-manager-to-coordinate-requests"></a>Använd Azure-Traffic Manager för att koordinera förfrågningar

Med [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kan du dirigera begär anden till flera geo-placerade webbplatser som sedan backas upp av flera Sök tjänster. En fördel med Traffic Manager är att den kan avsöka Azure-Kognitiv sökning för att säkerställa att den är tillgänglig och dirigera användare till alternativa Sök tjänster i händelse av drift stopp. Om du dirigerar Sök förfrågningar via Azure Web Sites kan du dessutom använda Azure-Traffic Manager för att läsa in balans fall där webbplatsen är igång men inte Azure Kognitiv sökning. Här är ett exempel på vad arkitekturen som utnyttjar Traffic Manager.

   ![Över-fliken av tjänster efter region, med central Traffic Manager][3]

## <a name="disaster-recovery-and-service-outages"></a>Haveri beredskap och drift avbrott

Även om vi kan återanvända dina data ger Azure Kognitiv sökning inte omedelbar redundansväxling av tjänsten om det uppstår ett avbrott på klustret eller data Center nivån. Om ett kluster Miss lyckas i data centret kommer drift teamet att identifiera och arbeta med återställnings tjänsten. Du får stillestånds tid under tjänst återställningen, men du kan begära service krediter för att kompensera för att tjänsten inte är tillgänglig per [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Om kontinuerlig tjänst krävs vid oåterkalleliga haverier utanför Microsofts kontroll kan du [etablera en ytterligare tjänst](search-create-service-portal.md) i en annan region och implementera en strategi för geo-replikering för att säkerställa att index är helt redundanta för alla tjänster.

Kunder som använder [indexerare](search-indexer-overview.md) för att fylla i och uppdatera index kan hantera haveri beredskap via geo-/regionsspecifika indexerare som använder samma data källa. Två tjänster i olika regioner, som kör en indexerare, kan indexera samma data källa för att uppnå GEO-redundans. Om du indexerar från data källor som också är geo-redundanta bör du vara medveten om att Azure Kognitiv sökning indexerare endast kan utföra stegvis indexering (sammanfoga uppdateringar från nya, ändrade eller borttagna dokument) från primära repliker. I en redundansväxling, se till att du pekar på indexeraren igen till den nya primära repliken. 

Om du inte använder indexerare använder du program koden för att skicka objekt och data till olika Sök tjänster parallellt. Mer information finns i [hålla data synkroniserade över flera tjänster](#data-sync).

## <a name="back-up-and-restore-alternatives"></a>Säkerhetskopiera och återställa alternativ

Eftersom Azure Kognitiv sökning inte är en primär data lagrings lösning, ger Microsoft ingen formell mekanism för att säkerhetskopiera och återställa självbetjäning. Du kan dock använda exempel koden **index-Backup-Restore** i den här [Azure kognitiv sökning .net-exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-dotnet-samples) för att säkerhetskopiera index definitionen och ögonblicks bilden till en serie JSON-filer och sedan använda filerna för att återställa indexet, om det behövs. Det här verktyget kan också flytta index mellan tjänst nivåer.

Annars är din program kod som används för att skapa och fylla i ett index det här alternativet för återställning om du tar bort ett index av misstag. Om du vill återskapa ett index tar du bort det (förutsatt att det finns), återskapar indexet i tjänsten och läser in igen genom att hämta data från det primära data lagret.

## <a name="next-steps"></a>Nästa steg

Mer information om pris nivåer och tjänst begränsningar för var och en finns i [tjänst begränsningar](search-limits-quotas-capacity.md). Mer information om partition och replik kombinationer finns i [Planera för kapacitet](search-capacity-planning.md) .

En diskussion om prestanda och demonstrationer av de tekniker som beskrivs i den här artikeln finns på följande video:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png