---
title: Tjänst administration i portalen
titleSuffix: Azure Cognitive Search
description: Hantera en Azure Kognitiv sökning-tjänst, en värd för moln Sök tjänst på Microsoft Azure, med hjälp av Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 814a5afbde548891a30d941365cdd71d227b4767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674404"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Tjänst administration för Azure Kognitiv sökning i Azure Portal

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST-API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure Kognitiv sökning är en fullständigt hanterad, molnbaserad Sök tjänst som används för att skapa en omfattande Sök upplevelse i anpassade appar. Den här artikeln beskriver de tjänst administrations uppgifter som du kan utföra i [Azure Portal](https://portal.azure.com) för en Sök tjänst som du redan har etablerad. Tjänst administration är lätt att utforma, begränsad till följande uppgifter:

* Kontrol lera lagringen med hjälp av länken mellan sidans **användning** .
* Kontrol lera fråga volymer och svars tider med hjälp av länken för **övervakning** på Mittens sidan och om begär Anden har begränsats.
* Hantera åtkomst med hjälp av sidan **nycklar** till vänster.
* Justera kapaciteten med hjälp av **skalnings** sidan till vänster.

Samma uppgifter som utförs i portalen kan också hanteras via programmering via [API: er för hantering](/rest/api/searchmanagement/) och [AZ. Sök PowerShell-modulen](search-manage-powershell.md). Administrativa uppgifter visas fullständigt i portalen och program mässiga gränssnitt. Det finns ingen speciell administrativ uppgift som bara är tillgänglig i en enda spärr.

Azure Kognitiv sökning utnyttjar andra Azure-tjänster för djupare övervakning och hantering. Av sig själva är de enda data som lagras med en Sök tjänst innehåll (index, indexerare och data käll definitioner och andra objekt). Mått som rapporteras ut till Portal sidor hämtas från interna loggar på en rullande 30-dagars cykel. För kvarhållning av användar kontrollerad logg och ytterligare händelser behöver du [Azure Monitor](../azure-monitor/index.yml). 

## <a name="fixed-service-properties"></a>Egenskaper för fast tjänst

Flera aspekter av en Sök tjänst bestäms när tjänsten är etablerad och kan inte ändras senare:

* Tjänst namn (du kan inte byta namn på en tjänst)
* Tjänst plats (du kan för närvarande inte flytta en intakt tjänst till en annan region)
* Maximalt antal repliker och partitioner (bestäms av nivån, Basic eller standard)

Om du började med Basic med maximalt en partition, och du nu behöver fler partitioner, måste du [skapa en ny tjänst](search-create-service-portal.md) på en högre nivå och återskapa innehållet på den nya tjänsten. 

## <a name="administrator-rights"></a>Administratörs behörighet

Etablering eller inaktive ring av tjänsten kan utföras av en administratör för Azure-prenumeration eller tillsammans med en administratör.

När det gäller åtkomst till slut punkten har alla som har åtkomst till tjänst-URL: en och en API-nyckel åtkomst till innehåll. Mer information om nycklar finns i [Hantera API-nycklar](search-security-api-keys.md).

* Skrivskyddad åtkomst till tjänsten är frågeinställningar, som vanligt vis beviljas till ett klient program genom att ge den URL: en och en fråge-API-nyckel.
* Läs-och skriv åtkomst ger möjlighet att lägga till, ta bort eller ändra Server objekt, inklusive API-nycklar, index, indexerare, data källor och scheman. Läs-och Skriv behörighet beviljas genom att ge URL: en en administratörs-API-nyckel.

Rättigheter till tjänst etablerings apparaten beviljas genom roll tilldelningar. [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) är ett auktoriserings system som bygger på [Azure Resource Manager](../azure-resource-manager/management/overview.md) för etablering av Azure-resurser. 

I Azure-Kognitiv sökning avgör Azure- [rollerna](search-security-rbac.md) vem som kan utföra uppgifter, oavsett om de använder [portalen](search-manage.md), [PowerShell](search-manage-powershell.md)eller [hantering REST-API: er](/rest/api/searchmanagement/search-howto-management-rest-api):

* Skapa eller ta bort en tjänst
* Skala tjänsten
* Ta bort eller återskapa API-nycklar
* Aktivera diagnostisk loggning (skapa tjänster)
* Aktivera trafik analys (skapa tjänster)

> [!TIP]
> Med hjälp av Azure-omfattande mekanismer kan du låsa en prenumeration eller resurs för att förhindra oavsiktlig eller obehörig borttagning av Sök tjänsten av användare med administratörs behörighet. Mer information finns i [låsa resurser för att förhindra oväntad borttagning](../azure-resource-manager/management/lock-resources.md).

## <a name="logging-and-system-information"></a>Loggning och system information

På Basic-nivån och över, övervakar Microsoft alla Azure Kognitiv sökning Services för 99,9% tillgänglighet per service nivå avtal (SLA). Om tjänsten är långsam eller begär data flöde hamnar under SLA-tröskelvärden granskar support team de loggfiler som är tillgängliga för dem och löser problemet.

Azure Kognitiv sökning utnyttjar [Azure Monitor](../azure-monitor/index.yml) för att samla in och lagra indexerings-och fråga-aktiviteter. En Sök tjänst lagrar själva innehållet (index, Indexer definitioner, definitioner av data källor, färdigheter-definitioner, synonym Maps). Cachelagring och loggad information lagras ofta i ett Azure Storage konto. Mer information om att logga indexering och fråga arbets belastningar finns i [samla in och analysera loggdata](search-monitor-logs.md).

När det gäller allmän information om din tjänst, med enbart de funktioner som är inbyggda i Azure Kognitiv sökning, kan du hämta information på följande sätt:

* På sidan tjänst **Översikt** , via aviseringar, egenskaper och status meddelanden.
* Använd [PowerShell](search-manage-powershell.md) eller [hanterings REST API](/rest/api/searchmanagement/) för att [Hämta tjänst egenskaper](/rest/api/searchmanagement/services). Det finns ingen ny information eller några åtgärder som tillhandahålls i programmerings lagret. Gränssnitten finns så att du kan skriva skript.

## <a name="monitor-resource-usage"></a>Övervaka resursanvändning

I instrument panelen är resurs övervakning begränsad till den information som visas i instrument panelen för tjänsten och några mått som du kan hämta genom att fråga tjänsten. På instrument panelen för tjänsten i användnings avsnittet kan du snabbt avgöra om partitionernas resurs nivåer är lämpliga för ditt program. Du kan etablera externa resurser, till exempel Azure-övervakning, om du vill samla in och spara loggade händelser. Mer information finns i [övervakning av Azure-kognitiv sökning](search-monitor-usage.md).

Med hjälp av Sök tjänsten REST API kan du få ett antal dokument och index program mässigt: 

* [Hämta index statistik](/rest/api/searchservice/Get-Index-Statistics)
* [Räkna dokument](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Haveri beredskap och drift avbrott

Även om vi kan återanvända dina data ger Azure Kognitiv sökning inte omedelbar redundansväxling av tjänsten om det uppstår ett avbrott på klustret eller data Center nivån. Om ett kluster Miss lyckas i data centret kommer drift teamet att identifiera och arbeta med återställnings tjänsten. Du får stillestånds tid under tjänst återställningen, men du kan begära service krediter för att kompensera för att tjänsten inte är tillgänglig per [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Om kontinuerlig tjänst krävs vid oåterkalleliga haverier utanför Microsofts kontroll kan du [etablera en ytterligare tjänst](search-create-service-portal.md) i en annan region och implementera en strategi för geo-replikering för att säkerställa att index är helt redundanta för alla tjänster.

Kunder som använder [indexerare](search-indexer-overview.md) för att fylla i och uppdatera index kan hantera haveri beredskap via geo-/regionsspecifika indexerare som använder samma data källa. Två tjänster i olika regioner, som kör en indexerare, kan indexera samma data källa för att uppnå GEO-redundans. Om du indexerar från data källor som också är geo-redundanta bör du vara medveten om att Azure Kognitiv sökning indexerare endast kan utföra stegvis indexering (sammanfoga uppdateringar från nya, ändrade eller borttagna dokument) från primära repliker. I en redundansväxling, se till att du pekar på indexeraren igen till den nya primära repliken. 

Om du inte använder indexerare använder du program koden för att skicka objekt och data till olika Sök tjänster parallellt. Mer information finns i [prestanda och optimering i Azure kognitiv sökning](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Eftersom Azure Kognitiv sökning inte är en primär data lagrings lösning ger vi inte en formell mekanism för säkerhets kopiering och återställning av självbetjäning. Du kan dock använda exempel koden **index-Backup-Restore** i den här [Azure kognitiv sökning .net-exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-dotnet-samples) för att säkerhetskopiera index definitionen och ögonblicks bilden till en serie JSON-filer och sedan använda filerna för att återställa indexet, om det behövs. Det här verktyget kan också flytta index mellan tjänst nivåer.

Annars är din program kod som används för att skapa och fylla i ett index det här alternativet för återställning om du tar bort ett index av misstag. Om du vill återskapa ett index tar du bort det (förutsatt att det finns), återskapar indexet i tjänsten och läser in igen genom att hämta data från det primära data lagret.

## <a name="scale-up-or-down"></a>Skala upp eller ned

Varje Sök tjänst börjar med minst en replik och en partition. Om du har registrerat dig för en [nivå som har stöd för mer kapacitet](search-limits-quotas-capacity.md)klickar du på **skala** i det vänstra navigerings fönstret för att justera resursanvändningen.

När du lägger till kapacitet via någon av resurserna använder tjänsten dem automatiskt. Ingen ytterligare åtgärd krävs för din del, men det finns en liten fördröjning innan den nya resursens påverkan realiseras. Det kan ta 15 minuter eller mer att etablera ytterligare resurser.

### <a name="add-replicas"></a>Lägg till repliker

Att öka antalet frågor per sekund (frågor per sekund) eller att uppnå hög tillgänglighet görs genom att lägga till repliker. Varje replik har en kopia av ett index, så du kan lägga till en eller flera repliker översätts till ett mer index som är tillgängligt för att hantera förfrågningar om tjänst frågor. Det krävs minst 3 repliker för hög tillgänglighet (se [Justera kapaciteten](search-capacity-planning.md) för mer information).

En Sök tjänst som har fler repliker kan belastningsutjämna förfrågningar om begär anden över ett större antal index. Med tanke på en nivå av frågans volym kommer frågans data flöde att bli snabbare när det finns fler kopior av det index som är tillgängligt för att betjäna begäran. Om du upplever svars tid för frågor kan du förvänta en positiv inverkan på prestanda när de ytterligare replikerna är online.

Även om frågans data flöde går upp när du lägger till repliker, så är det inte exakt dubbelt eller tredubbla när du lägger till repliker till din tjänst. Alla Sök program omfattas av externa faktorer som kan impinge på frågans prestanda. Komplexa frågor och nätverks fördröjning är två faktorer som bidrar till variationer i svars tider för frågor.

### <a name="add-partitions"></a>Lägg till partitioner

Det är vanligare att lägga till repliker, men när lagringen är begränsad kan du lägga till partitioner för att få mer kapacitet. Den nivå där du etablerade tjänsten avgör om partitioner kan läggas till. Basic-nivån är låst på en partition. Standard-nivåer och högre stöder ytterligare partitioner.

Partitioner läggs till i divisor av 12 (särskilt, 1, 2, 3, 4, 6 eller 12). Detta är en artefakt av horisontell partitionering. Ett index skapas i 12 Shards, som kan lagras på en partition eller vara jämnt indelat i 2, 3, 4, 6 eller 12 partitioner (en Shard per partition).

### <a name="remove-replicas"></a>Ta bort repliker

Efter perioder med hög fråga-volymer kan du använda skjutreglaget för att minska antalet repliker när Sök frågan läses in har normaliserats (till exempel efter att försäljningen är över). Det finns inga ytterligare steg som krävs för din del. Om du sänker antalet repliker överlämnas virtuella datorer i data centret. Dina frågor och data inmatnings åtgärder kommer nu att köras på färre virtuella datorer än tidigare. Minimi kravet är en replik.

### <a name="remove-partitions"></a>Ta bort partitioner

I motsats till att ta bort repliker, som inte kräver extra ansträngning på din sida, kan du ha lite arbete om du använder mer lagrings utrymme än vad som kan minskas. Om din lösning exempelvis använder tre partitioner genererar downsizing till en eller två partitioner ett fel om det nya lagrings utrymmet är mindre än vad som krävs för att vara värd för ditt index. Som du kan förväntar dig kan du välja att ta bort index eller dokument i ett associerat index för att frigöra utrymme, eller behålla den aktuella konfigurationen.

Det finns ingen identifierings metod som anger vilka index-Shards som lagras på vissa partitioner. Varje partition ger cirka 25 GB lagring, så du måste minska lagrings utrymmet till en storlek som kan hanteras av antalet partitioner som du har. Om du vill återgå till en partition måste alla 12-Shards anpassas.

Om du vill ha hjälp med framtida planeringen kanske du vill kontrol lera lagringen (med hjälp av [Hämta index statistik](/rest/api/searchservice/Get-Index-Statistics)) för att se hur mycket du faktiskt har använt. 

## <a name="next-steps"></a>Nästa steg

* Automatisera med [PowerShell](search-manage-powershell.md) eller [Azure CLI](search-manage-azure-cli.md)

* Granska [prestanda-och optimerings](search-performance-optimization.md) tekniker

* Granska [säkerhetsfunktioner](search-security-overview.md) för att skydda innehåll och åtgärder

* Aktivera [diagnostisk loggning](search-monitor-logs.md) för att övervaka arbets belastningar för frågor och indexering