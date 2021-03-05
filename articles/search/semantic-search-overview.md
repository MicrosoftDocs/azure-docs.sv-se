---
title: Semantisk sökning
titleSuffix: Azure Cognitive Search
description: Lär dig hur Kognitiv sökning använder djup inlärnings semantiska Sök modeller från Bing för att göra Sök resultaten mer intuitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203240"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Semantisk sökning i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantiska Sök funktioner finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Semantisk sökning är en samling frågor som är relaterade till funktioner som stöder en bättre, mer naturlig fråge upplevelse. Funktionerna omfattar semantisk rerangering av Sök resultat, samt under texter och svars generering med semantisk markering. De översta 50 resultaten som returnerades från [motorn för full texts ökning](search-lucene-query-architecture.md) rangordnas för att hitta de mest relevanta matchningarna.

Den underliggande tekniken utnyttjar investeringarna från Bing och Microsoft Research och integreras i Kognitiv sökning-infrastrukturen. Om du vill använda det behöver du små ändringar i sökbegäran, men ingen extra konfiguration eller Omindexering krävs.

Offentliga för hands versions funktioner är:

+ Semantisk rangordnings modell som utvärderar resultat baserat på kontext eller semantisk innebörd av Sök frågornas villkor
+ Semantiska bild texter som markerar relevanta passager
+ Semantiska svar på frågan, som också formuleras från resultat
+ Stavnings kontroll som korrigerar skrivfel innan villkoren når sökmotorn

## <a name="semantic-search-architecture"></a>Arkitektur för semantisk sökning

Komponenter i semantisk sökning ligger ovanpå den befintliga pipeline för frågekörning. Stavnings korrigering (visas inte i diagrammet) förbättrar återställningen genom att korrigera skrivfel i enskilda sökord. När all parsning och analys har slutförts hämtar sökmotorn dokumenten som matchade frågan och poängen med [standardalgoritmen](index-similarity-and-scoring.md#similarity-ranking-algorithms)för bedömning, antingen BM25 eller klassisk, beroende på när tjänsten skapades. Bedömnings profiler tillämpas också i det här skedet. 

När de översta 50-matchningarna har tagits emot utvärderar [algoritmen för semantisk rankning](semantic-how-to-query-response.md) åter dokument sökkorpus. Resultaten kan innehålla fler än 50 matchningar, men endast den första 50 kommer att rangordnas. Algoritmen använder sig av både Machine Learning och överförings utbildning för att omvärdera dokumenten baserat på hur väl var och en matchar frågans avsikt.

För att skapa under texter och svar använder den språk representations modeller. I en beskrivning använder algoritmen en språk modell som utvecklats av Bing för att extrahera under texter med högdagrar som bäst sammanfattar frågeresultat. Om Sök frågan är en fråga och svaren begärs, identifierar en liknande språk modell text passager som bäst svarar på frågan som uttrycks av Sök frågan.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Semantiska komponenter i en frågans pipeline" border="true":::

## <a name="availability-and-pricing"></a>Tillgänglighet och priser

Semantisk rangordning är tillgängligt via [registrerings registrering](https://aka.ms/SemanticSearchPreviewSignup), på search Services som skapats på en standard nivå (S1, S2, S3), som finns i någon av dessa regioner: Norra centrala USA, västra USA, västra USA 2, östra USA 2, norra Europa, västra Europa. En befintlig Sök tjänst på S1 eller högre i de angivna regionerna uppfyller kraven för för hands versionen (inget behov av att skapa en ny tjänst).

Stavnings korrigering är tillgänglig i samma regioner, men har inga nivå begränsningar och inget krav på registrering. 

Mellan för hands versions lansering den 2 mars till och med den 1 april erbjuds stavnings korrigering och semantisk rangordning utan kostnad. Efter den 1 april blir beräknings kostnaden för att köra den här funktionen en fakturerbar händelse. Den förväntade kostnaden är cirka USD $500/månad för 250 000 frågor. Du hittar detaljerad kostnads information som beskrivs på [sidan kognitiv sökning prissättning](https://azure.microsoft.com/pricing/details/search/) och i [Beräkna och hantera kostnader](search-sku-manage-costs.md).

## <a name="next-steps"></a>Nästa steg

Med en ny frågetyp aktive ras rangordning och svars struktur för semantisk sökning. Kom igång genom att [skapa en semantisk fråga](semantic-how-to-query-request.md) . Du kan också läsa någon av följande artiklar om relaterad information.

+ [Lägg till stavnings kontroll i sökord](speller-how-to-add.md)
+ [Semantisk rangordning och svar](semantic-how-to-query-response.md)