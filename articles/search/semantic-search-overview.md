---
title: Semantisk sökning
titleSuffix: Azure Cognitive Search
description: Lär dig hur Kognitiv sökning använder djup inlärnings semantiska Sök modeller från Bing för att göra Sök resultaten mer intuitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432987"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Semantisk sökning i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantiska Sök funktioner finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Semantisk sökning är en samling frågor som är relaterade till funktioner som stöder en bättre, mer naturlig fråge upplevelse. Funktionerna omfattar semantisk rerangering av Sök resultat, samt under texter och svars generering med semantisk markering. De översta 50 resultaten som returnerades från [motorn för full texts ökning](search-lucene-query-architecture.md) rangordnas för att hitta de mest relevanta matchningarna.

Den underliggande tekniken är från Bing och Microsoft Research och integreras i Kognitiv sökning-infrastrukturen. Mer information om forskning och AI-investeringar som backar semantisk sökning finns i [hur AI från Bing fungerar med Azure kognitiv sökning (Microsoft Research blogg)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Om du vill använda semantisk sökning i frågor måste du göra små ändringar i sökbegäran, men ingen extra konfiguration eller Omindexering krävs.

Offentliga för hands versions funktioner är:

+ Semantisk rangordnings modell som använder kontexten eller semantiskt syfte att beräkna en relevans Poäng
+ Semantiska beskrivningar som sammanfattar viktiga resultat för enkel skanning
+ Semantiska svar på frågan, om frågan är en fråga
+ Semantiska högdagrar som fokuserar på nyckel fraser och termer
+ Stavnings kontroll som korrigerar skrivfel innan villkoren når sökmotorn

## <a name="availability-and-pricing"></a>Tillgänglighet och priser

Semantisk rangordning är tillgängligt via [registrerings registrering](https://aka.ms/SemanticSearchPreviewSignup), på search Services som skapats på en standard nivå (S1, S2, S3), som finns i någon av dessa regioner: Norra centrala USA, västra USA, västra USA 2, östra USA 2, norra Europa, västra Europa. Stavnings korrigering är tillgänglig i samma regioner, men saknar nivå begränsningar. Om du har en befintlig tjänst som uppfyller villkoret för nivån och regionen krävs bara registrering.

Mellan för hands versions lansering den 2 mars till och med den 1 april erbjuds stavnings korrigering och semantisk rangordning utan kostnad. Efter den 1 april blir beräknings kostnaden för att köra den här funktionen en fakturerbar händelse. Den förväntade kostnaden är cirka USD $500/månad för 250 000 frågor. Du hittar detaljerad kostnads information som beskrivs på [sidan kognitiv sökning prissättning](https://azure.microsoft.com/pricing/details/search/) och i [Beräkna och hantera kostnader](search-sku-manage-costs.md).

## <a name="semantic-search-architecture"></a>Arkitektur för semantisk sökning

Komponenter i semantisk sökning ligger ovanpå den befintliga pipeline för frågekörning. Stavnings korrigering (visas inte i diagrammet) förbättrar återställningen genom att korrigera skrivfel i enskilda sökord. När parsningen och analysen har slutförts hämtar sökmotorn dokumenten som matchade frågan och returnerar dem med hjälp av [standard bedömnings algoritmen](index-similarity-and-scoring.md#similarity-ranking-algorithms), antingen BM25 eller klassisk, beroende på när tjänsten skapades. Bedömnings profiler tillämpas också i det här skedet.

När de översta 50-matchningarna har tagits emot utvärderar den [semantiska ranknings modellen](semantic-how-to-query-response.md) dokumentet sökkorpus. Resultaten kan innehålla fler än 50 matchningar, men endast den första 50 kommer att rangordnas. För rankning använder modellen både Machine Learning och överförings utbildning för att omvärdera dokumenten baserat på hur väl var och en matchar frågans avsikt.

För att skapa under texter och svar, använder semantisk sökning en språk representation för att extrahera och markera nyckel passager som bäst sammanfattar ett resultat. Om Sök frågan är en fråga och svaren begärs, kommer svaret att innehålla en text passage som bäst svarar på frågan, som uttrycks av Sök frågan.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Semantiska komponenter i en frågans pipeline" border="true":::

## <a name="next-steps"></a>Nästa steg

Med en ny frågetyp aktive ras rangordning och svars struktur för semantisk sökning.

Kom igång genom att [skapa en semantisk fråga](semantic-how-to-query-request.md) . Du kan också läsa någon av följande artiklar om relaterad information.

+ [Lägg till stavnings kontroll i sökord](speller-how-to-add.md)
+ [Semantisk rangordning och svar (svar och under texter)](semantic-how-to-query-response.md)