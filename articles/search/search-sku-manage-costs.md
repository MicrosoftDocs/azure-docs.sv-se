---
title: Uppskatta kostnader
titleSuffix: Azure Cognitive Search
description: Lär dig mer om fakturerbara händelser, pris sättnings modellen och tips för att hantera kostnaden för att köra en Kognitiv sökning-tjänst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539546"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>Beräkna och hantera kostnader för en Azure Kognitiv sökning-tjänst

I den här artikeln lär du dig om pris modellen, fakturerbara händelser och tips för att hantera kostnaden för att köra en Azure Kognitiv sökning-tjänst.

## <a name="pricing-model"></a>Prismodell

Skalbarhets arkitekturen i Azure Kognitiv sökning baseras på flexibla kombinationer av repliker och partitioner så att du kan variera kapaciteten beroende på om du behöver fler frågor eller indexerings kraft och bara betala för det du behöver.

Hur mycket resurser som används av Sök tjänsten, multiplicerat med den fakturerings taxa som fastställs av tjänst nivån, bestämmer kostnaden för att köra tjänsten. Kostnaderna och kapaciteten är nära kopplade. När du uppskattar kostnaderna får du en bra uppfattning om vilka kostnader som krävs för att köra din indexering och fråga arbets belastningar.

I fakturerings syfte finns det två enkla formler att tänka på:

| Formel | Beskrivning |
|---------|-------------|
| **R x P = SU** | Antalet repliker som används, multiplicerat med antalet partitioner som används, motsvarar antalet *Sök enheter* (SU) som används av en tjänst. En SU är en resurs enhet och kan vara antingen en partition eller en replik. |
| **SU * fakturerings taxa = månads utgifter** | Antalet SUs multiplicerat med fakturerings taxan för den nivå där du etablerade tjänsten är den främsta faktorn för den totala månads fakturan. Vissa funktioner eller arbets belastningar har beroenden för andra Azure-tjänster, vilket kan öka kostnaden för din lösning på prenumerations nivå. Avsnittet fakturerbara händelser nedan identifierar funktioner som kan läggas till i din faktura. |

Varje tjänst börjar med en SU (en replik multiplicerad med en partition) som minimum. Det maximala antalet för en tjänst är 36 SUs. Det här max värdet kan nås på flera sätt: 6 partitioner x 6 repliker eller 3 partitioner x 12 repliker, till exempel. Det är vanligt att använda mindre än den totala kapaciteten (t. ex. en 3-replikering, en 3-partitions tjänst som faktureras som 9 SUs). Se diagrammet [partition och replik kombinations](search-capacity-planning.md#chart) diagram för giltiga kombinationer.

Fakturerings takten är per timme per SU. Varje nivå har en progressivt högre hastighet. Högre nivåer levereras med större och speedier partitioner, och detta bidrar till en högre pris per timme för den nivån. Du kan visa priserna för varje nivå på [pris informations](https://azure.microsoft.com/pricing/details/search/) sidan.

De flesta kunder tar bara en del av den totala kapaciteten online, där resten av reserven behålls. För fakturering bestämmer antalet partitioner och repliker som du ansluter online, vilket avgör vad du betalar per timme. 

## <a name="billable-events"></a>Fakturerbara händelser

En lösning som bygger på Azure Kognitiv sökning kan ådra sig kostnaderna på följande sätt:

+ [Kostnaden för själva tjänsten](#service-costs) , som körs dygnet runt, med lägsta konfiguration (en partition och replik), enligt bas priset. Du kan tänka på detta som den fasta kostnaden för att köra tjänsten.

+ Lägga till kapacitet (repliker eller partitioner), där kostnaderna ökar vid ökningar av fakturerings takten. Om hög tillgänglighet är ett affärs krav behöver du 3 repliker.

+ Bandbredds avgifter (utgående data överföring)

+ Tilläggs tjänster som krävs för vissa funktioner eller funktioner:

  + AI-anrikning (kräver [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + kunskaps lager (kräver [Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + stegvis anrikning (kräver [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), gäller AI-anrikning)
  + Kundhanterade nycklar och Double Encryption (kräver [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + privata slut punkter för en modell utan Internet åtkomst (kräver [Azures privata länk](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Tjänstkostnader

Till skillnad från virtuella datorer eller andra resurser som kan vara "pausade" för att undvika avgifter är en Azure Kognitiv sökning-tjänst alltid tillgänglig på maskin vara som är dedikerad för exklusiv användning. Därför är att skapa en tjänst en fakturerbar händelse som startar när du skapar tjänsten och slutar när du tar bort tjänsten. 

Den lägsta avgiften är den första Sök enheten (en partition med en enda replik x) till fakturerings takten. Detta minimum är fast för tjänstens livs längd eftersom tjänsten inte kan köras på något som är mindre än den här konfigurationen. 

Utöver det lägsta antalet kan du lägga till repliker och partitioner oberoende av varandra. Stegvisa ökningar i kapaciteten via repliker och partitioner ökar din faktura utifrån följande formel: **(repliker x partitioner x fakturerings takt)**, där den hastighet du debiteras beror på vilken pris nivå du väljer.

När du uppskattar kostnaden för en Sök lösning bör du tänka på att priserna och kapaciteten inte är linjära (dubblerad kapacitet mer än att dubblera kostnaden). Ett exempel på hur formeln fungerar finns i [så här allokerar du repliker och partitioner](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Bandbredds avgifter

Användningen av [indexerare](search-indexer-overview.md) kan påverka faktureringen om Azure-datakällan är i en annan region än Azure kognitiv sökning. I det här scenariot kan det finnas en kostnad för att flytta utgående data från Azure Data Source till Azure Kognitiv sökning. Mer information finns på sidan med pris information för den aktuella Azure-dataplattformen.

Du kan ta bort avgifter för utgående data helt och hållet om du skapar Azure Kognitiv sökning-tjänsten i samma region som dina data. Här är lite information från [prissättnings sidan för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Inkommande data: Microsoft debiterar inga inkommande data till någon tjänst i Azure. 

+ Utgående data: utgående data refererar till frågeresultat. Kognitiv sökning debiteras inte för utgående data, men utgående avgifter från Azure är möjliga om tjänsterna är i olika regioner.

  Dessa avgifter ingår inte i din Azure Kognitiv sökning-faktura. De anges här eftersom om du skickar resultat till andra regioner eller appar som inte är Azure-appar kan du se att dessa kostnader avspeglas i din totala faktura.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-anrikning med Cognitive Services

För [AI-anrikning](cognitive-search-concept-intro.md)bör du planera att [koppla en fakturerbar Azure Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md), i samma region som Azure kognitiv sökning, på pris nivån S0 för att betala per användning. Det finns ingen fast kostnad för att bifoga Cognitive Services. Du betalar bara för den bearbetning du behöver.

| Åtgärd | Fakturerings påverkan |
|-----------|----------------|
| Dokument sprickor, text extrahering | Kostnadsfri |
| Dokument sprickor, avbildnings extrahering | Faktureras enligt antalet bilder som har extraherats från dina dokument. I en [indexerare-konfiguration](/rest/api/searchservice/create-indexer#indexer-parameters)är **imageAction** den parameter som utlöser avbildnings extrahering. Om **imageAction** är inställt på "ingen" (standard) debiteras du inte för avbildnings extrahering. Hastigheten för avbildnings extrahering dokumenteras på sidan med [pris information](https://azure.microsoft.com/pricing/details/search/) för Azure kognitiv sökning.|
| [Inbyggda kognitiva kompetenser](cognitive-search-predefined-skills.md) | Faktureras enligt samma takt som om du har utfört uppgiften genom att använda Cognitive Services direkt. |
| Anpassade färdigheter | En anpassad färdighet är funktioner som du anger. Kostnaden för att använda en anpassad färdighet beror helt på om anpassad kod anropar andra avgiftsbelagda tjänster. |

Med funktionen för [förhands granskning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) kan du tillhandahålla ett cacheminne som gör det möjligt för indexeraren att vara mer effektivt när du bara kör de kognitiva färdigheter som behövs om du ändrar din färdigheter i framtiden, vilket sparar tid och pengar.

## <a name="tips-for-managing-costs"></a>Tips för att hantera kostnader

Följande rikt linjer hjälper dig att sänka kostnaderna eller hantera kostnader mer effektivt:

+ Skapa alla resurser i samma region, eller i så få regioner som möjligt, för att minimera eller eliminera bandbredds kostnader.

+ Konsolidera alla tjänster till en resurs grupp, till exempel Azure Kognitiv sökning, Cognitive Services och andra Azure-tjänster som används i din lösning. I Azure Portal letar du reda på resurs gruppen och använder **Cost Management** kommandon för att få inblick i faktiska och planerade utgifter.

+ Överväg att använda Azure Web App för ditt klient program så att förfrågningar och svar stannar inom data centrets gränser.

+ Skala upp för resurs krävande åtgärder som indexering och justera sedan nedåt för vanliga frågor och arbets belastningar. Börja med den lägsta konfigurationen för Azure Kognitiv sökning (en SU som består av en partition och en replik) och övervaka sedan användar aktivitet för att identifiera användnings mönster som indikerar ett behov av mer kapacitet. Om det finns ett förutsägbart mönster kanske du kan synkronisera skalning med aktivitet (du måste skriva kod för att automatisera detta).

+ Kostnads hantering är inbyggt i Azure-infrastrukturen. Granska [fakturerings-och kostnads hantering](../cost-management-billing/cost-management-billing-overview.md) för mer information om spårning av kostnader, verktyg och API: er.

Det går inte att stänga av en Sök tjänst temporärt. Dedikerade resurser är alltid operativa, allokeras för exklusiv användning för din tjänsts livs längd. Att ta bort en tjänst är permanent och tar också bort tillhör ande data.

Vad gäller själva tjänsten är det enda sättet att sänka din faktura till att minska antalet repliker och partitioner till en nivå som fortfarande ger acceptabel prestanda och [SLA-efterlevnad](https://azure.microsoft.com/support/legal/sla/search/v1_0/), eller skapa en tjänst på en lägre nivå (S1 Tim pris är lägre än S2 eller S3-priser). Förutsatt att du etablerar tjänsten i det nedre slutet av dina belastnings prognoser, kan du skapa en andra större tjänst, återskapa dina index på den andra tjänsten och sedan ta bort den första.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du övervakar och hanterar kostnader i din Azure-prenumeration.

> [!div class="nextstepaction"]
> [Dokumentation om Azure Cost Management och fakturering](../cost-management-billing/cost-management-billing-overview.md)