---
title: Skala för prestanda
titleSuffix: Azure Cognitive Search
description: Lär dig mer om teknik och bästa praxis för att justera Azure Kognitiv sökning prestanda och konfigurera den optimala skalningen.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.custom: references_regions
ms.openlocfilehash: 5e8b873e0e4e89979769183cfbf0ad473069e897
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096427"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Skalbarhet för prestanda på Azure Kognitiv sökning

Den här artikeln beskriver metod tips för avancerade scenarier med avancerade krav på skalbarhet och tillgänglighet.

## <a name="start-with-baseline-numbers"></a>Börja med bas linje nummer

Innan du försäkrar en större distributions ansträngning bör du se till att du vet vad en typisk fråga ska se ut. Följande rikt linjer kan hjälpa dig att komma till bas linje frågor.

1. Välj en mål svars tid (eller den längsta tid) som en typisk sökbegäran ska ta att slutföra.

1. Skapa och testa en verklig arbets belastning mot din Sök tjänst med en realistisk data uppsättning för att mäta de här latens priserna.

1. Börja med ett lågt antal frågor per sekund (frågor per sekund) och öka sedan gradvis antalet som körs i testet tills svars tiden för frågan sjunker under det fördefinierade målet. Detta är ett viktigt mått för att hjälpa dig att planera för skalning när ditt program växer i användning.

1. Återanvända HTTP-anslutningar närhelst det är möjligt. Om du använder Azure Kognitiv sökning .NET SDK innebär det att du bör återanvända en instans eller [SearchClient](/dotnet/api/azure.search.documents.searchclient) -instans och om du använder REST API bör du återanvända en enda httpclient.

1. Variera ämnet för förfrågningar så att sökningen sker över olika delar av ditt index. Variationen är viktig eftersom om du kontinuerligt kör samma Sök begär Anden börjar cachelagring av data att öka prestandan bättre än den kan ha en mer detaljerad frågegrupp.

1. Variera strukturen för fråge förfrågningar så att du får olika typer av frågor. Alla Sök frågor utförs inte på samma nivå. Till exempel är ett dokuments öknings-eller Sök förslag vanligt vis snabbare än en fråga med ett stort antal ansikte och filter. Test kompositionen bör innehålla olika frågor, i ungefär samma förhållande som du förväntar sig i produktionen.  

När du skapar de här test arbets belastningarna finns det vissa egenskaper för Azure Kognitiv sökning att tänka på:

+ Det är möjligt att du överbelastar din tjänst genom att skicka för många Sök frågor vid ett tillfälle. När detta inträffar visas HTTP 503-svars koder. Om du vill undvika en 503 under testningen börjar du med olika intervall av Sök begär Anden för att se skillnaderna mellan svars tider när du lägger till fler Sök begär Anden.

+ Azure Kognitiv sökning kör inte indexerings aktiviteter i bakgrunden. Om tjänsten hanterar fråge-och indexerings arbets belastningar samtidigt tar du hänsyn till detta genom att antingen introducera indexerings jobb i dina testtester eller genom att undersöka alternativen för att köra indexerings jobb under låg belastnings tid.

> [!Tip]
> Du kan simulera en realistisk läsar fråga med hjälp av belastnings test verktyg. Prova att [läsa in tester med Azure DevOps](/azure/devops/test/load-test/get-started-simple-cloud-load-test) eller Använd något av dessa [alternativ](/azure/devops/test/load-test/overview#alternatives).

## <a name="scale-for-high-query-volume"></a>Skala för hög fråga-volym

En tjänst överbelastas när frågor tar för lång tid eller när tjänsten börjar släppa förfrågningar. Om detta inträffar kan du lösa problemet på något av följande sätt:

+ **Lägg till repliker**  

  Varje replik är en kopia av dina data, så att tjänsten kan belastningsutjämna begär Anden mot flera kopior.  All belastnings utjämning och replikering av data hanteras av Azure Kognitiv sökning och du kan ändra antalet repliker som har allokerats för din tjänst när du vill. Du kan allokera upp till 12 repliker i en standard Sök tjänst och tre repliker i en grundläggande Sök tjänst. Repliker kan justeras antingen från [Azure Portal](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).

+ **Skapa en ny tjänst på en högre nivå**  

  Azure Kognitiv sökning finns på ett [antal nivåer](https://azure.microsoft.com/pricing/details/search/) och var och en har olika prestanda nivåer. I vissa fall kan det hända att du har så många frågor att nivån som du är på inte kan ge tillräckligt med gång, även om repliker är överutnyttjade. I det här fallet bör du överväga att flytta till en högre nivå, till exempel standard nivån S3, som är utformad för scenarier som har ett stort antal dokument och mycket hög fråge arbets belastning.

## <a name="scale-for-slow-individual-queries"></a>Skala för långsamma enskilda frågor

En annan orsak till hög latens frekvenser är att en fråga tar för lång tid att slutföra. I det här fallet kommer du inte att kunna lägga till repliker. Två möjliga alternativ som kan vara till hjälp kan vara följande:

+ **Öka partitioner**

  En partition delar upp data mellan extra data behandlings resurser. Två partitioner dela data på hälften, en tredje partition delar den i tredje, och så vidare. En positiv sido effekt är att långsammare frågor ibland presterar snabbare på grund av parallell dator användning. Vi har noterat parallellisering för frågor med låg selektivitet, till exempel frågor som stämmer överens med många dokument, eller till att det finns ett stort antal dokument. Eftersom det krävs en betydande beräkning för att räkna med relevanta i dokumenten, eller för att räkna antalet dokument, så kan du lägga till extra partitioner snabbare.  
   
  Det får finnas högst 12 partitioner i standard Sök tjänsten och 1 partition i den grundläggande Sök tjänsten. Partitioner kan justeras antingen från [Azure Portal](search-create-service-portal.md) eller [PowerShell](search-manage-powershell.md).

+ **Begränsa fält för högsta kardinalitet**

  Ett högt kardinalitet-fält består av ett fasettable-eller filter bara fält som har ett stort antal unika värden och som därför förbrukar betydande resurser när du beräknar resultat. Om du till exempel anger ett produkt-ID eller ett beskrivnings fält som ett fasettable/filterbar, räknas det som hög kardinalitet eftersom de flesta av värdena från dokument till dokument är unika. När det är möjligt begränsar du antalet fält för hög kardinalitet.

+ **Öka Sök nivån**  

  Att flytta upp till en högre Azure Kognitiv sökning-nivå kan vara ett annat sätt att förbättra prestandan för långsamma frågor. Varje högre nivå ger snabbare processorer och mer minne, som båda har en positiv inverkan på frågans prestanda.

## <a name="scale-for-availability"></a>Skala för tillgänglighet

Repliker bidrar inte bara till att minska svars tiden, men kan även tillåta hög tillgänglighet. Med en enda replik bör du förvänta dig periodiska avbrott på grund av omstart av servern efter program uppdateringar eller för andra underhålls händelser som kommer att inträffa. Därför är det viktigt att tänka på om ditt program kräver hög tillgänglighet för sökningar (frågor) och skrivningar (indexerings händelser). Azure Kognitiv sökning erbjuder SLA-alternativ för alla betalda Sök erbjudanden med följande attribut:

+ Två repliker för hög tillgänglighet för skrivskyddade arbets belastningar (frågor)

+ Tre eller flera repliker för hög tillgänglighet av Läs-och skriv arbets belastningar (frågor och indexering)

Mer information finns på [Azure Kognitiv sökning serviceavtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Eftersom repliker är kopior av dina data kan du med flera repliker tillåta Azure Kognitiv sökning att starta om datorn och underhåll mot en replik, medan frågekörningen fortsätter på andra repliker. Om du däremot tar bort repliker tar du en fråga om prestanda försämring, förutsatt att dessa repliker var en underutnyttjad resurs.

<a name="availability-zones"></a>

### <a name="availability-zones"></a>Tillgänglighetszoner

[Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview) dela upp en regions Data Center i distinkta fysiska plats grupper för att tillhandahålla hög tillgänglighet inom samma region. För Kognitiv sökning är enskilda repliker enheterna för zon tilldelning. En Sök tjänst körs inom en region. dess repliker körs i olika zoner.

Du kan använda Tillgänglighetszoner med Azure Kognitiv sökning genom att lägga till två eller fler repliker till din Sök tjänst. Varje replik kommer att placeras i en annan tillgänglighets zon inom regionen. Om du har fler repliker än Tillgänglighetszoner kommer replikerna att fördelas på Tillgänglighetszoner så jämnt som möjligt.

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

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Skala för geo-distribuerade arbets belastningar och GEO-redundans

För geo-distribuerade arbets belastningar kommer användare som ligger långt från värd data centret att ha högre latens taxa. En minskning är att etablera flera Sök tjänster i regioner med närmare närhet till dessa användare.

Azure Kognitiv sökning tillhandahåller för närvarande inte en automatiserad metod för geo-replikering av Azure Kognitiv sökning-index över flera regioner, men det finns vissa tekniker som kan användas för att göra den här processen enkel att implementera och hantera. Dessa beskrivs i följande avsnitt.

Målet för en geo-distribuerad uppsättning Sök tjänster är att två eller fler index är tillgängliga i två eller flera regioner, där en användare dirigeras till den Azure Kognitiv sökning-tjänst som ger den lägsta svars tiden som visas i det här exemplet:

   ![Globala flikar för tjänster efter region][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Hålla data synkroniserade över flera tjänster

Det finns två alternativ för att hålla dina distribuerade Sök tjänster synkroniserade, som består av antingen med hjälp av [azure kognitiv sökning-indexeraren](search-indexer-overview.md) eller push-API: et (kallas även [azure-kognitiv sökning REST API](/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Använd indexerare för att uppdatera innehåll på flera tjänster

Om du redan använder indexerare på en tjänst kan du konfigurera en andra indexerare på en andra tjänst för att använda samma data käll objekt, hämta data från samma plats. Varje tjänst i varje region har sin egen indexerare och ett mål index (ditt Sök index är inte delat, vilket innebär att data är duplicerade), men varje indexerare refererar samma data källa.

Här är ett visuellt visuellt objekt av vad arkitekturen skulle se ut.

   ![Enkel data källa med distribuerade indexerare och tjänst kombinationer][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Använd REST-API: er för att överföra innehålls uppdateringar på flera tjänster

Om du använder Azure-Kognitiv sökning REST API för att [skicka innehåll i ditt Azure kognitiv sökning-index](/rest/api/searchservice/update-index)kan du behålla dina olika Sök tjänster synkroniserade genom att skicka ändringar till alla Sök tjänster när en uppdatering krävs. I din kod ser du till att hantera fall där en uppdatering av en Sök tjänst Miss lyckas men fungerar för andra Sök tjänster.

## <a name="leverage-azure-traffic-manager"></a>Utnyttja Azure Traffic Manager

Med [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) kan du dirigera begär anden till flera geo-placerade webbplatser som sedan backas upp av flera Sök tjänster. En fördel med Traffic Manager är att den kan avsöka Azure-Kognitiv sökning för att säkerställa att den är tillgänglig och dirigera användare till alternativa Sök tjänster i händelse av drift stopp. Om du dirigerar Sök förfrågningar via Azure Web Sites kan du dessutom använda Azure-Traffic Manager för att läsa in balans fall där webbplatsen är igång men inte Azure Kognitiv sökning. Här är ett exempel på vad arkitekturen som utnyttjar Traffic Manager.

   ![Över-fliken av tjänster efter region, med central Traffic Manager][3]

## <a name="next-steps"></a>Nästa steg

Mer information om pris nivåer och tjänst begränsningar för var och en finns i [tjänst begränsningar](search-limits-quotas-capacity.md). Mer information om partition och replik kombinationer finns i [Planera för kapacitet](search-capacity-planning.md) .

En diskussion om prestanda och demonstrationer av de tekniker som beskrivs i den här artikeln finns på följande video:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png