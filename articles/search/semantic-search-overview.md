---
title: Semantisk sökning
titleSuffix: Azure Cognitive Search
description: Lär dig hur Kognitiv sökning använder djup inlärnings semantiska Sök modeller från Bing för att göra Sök resultaten mer intuitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom: references_regions
ms.openlocfilehash: e0656c06f446ed6241b64040f063ed7ba419a942
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771319"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Semantisk sökning i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantisk sökning är i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick, under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)och är inte garanterade att ha samma implementering vid allmän tillgänglighet. Dessa funktioner är fakturerbara. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

Semantisk sökning är en samling fråge relaterade funktioner som lägger till semantisk relevans och språk förståelse för Sök resultat. *Semantisk rangordning* söker efter kontext och relateradhet bland termer och höjer matchningar som är mer begripliga för frågan. Med språk förståelse hittar du under *texter* och *svar* i ditt innehåll som sammanfattar det matchande dokumentet eller besvarar en fråga, som sedan kan återges på en Sök resultat sida för en mer produktiv Sök upplevelse.

Avancerade förtränade modeller används för sammanfattning och rankning. För att upprätthålla den snabba prestanda som användarna förväntar sig att söka, tillämpas semantisk Sammanfattning och rangordning enbart på de översta 50-resultaten, vilket visas i [standard algoritmen för likhets](index-similarity-and-scoring.md#similarity-ranking-algorithms)bedömning. Genom att använda dessa resultat som dokument sökkorpus, kommer semantisk rangordning att omvärdera dessa resultat baserat på matchningens semantiska styrka.

Den underliggande tekniken är från Bing och Microsoft Research och integreras i Kognitiv sökning-infrastrukturen som en tilläggs funktion. Mer information om forskning och AI-investeringar som backar semantisk sökning finns i [hur AI från Bing fungerar med Azure kognitiv sökning (Microsoft Research blogg)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/).

Följande videoklipp ger en översikt över funktionerna.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Komponenter och arbets flöde

Semantisk sökning förbättrar precisionen och återkallas genom att lägga till följande funktioner:

| Funktion | Beskrivning |
|---------|-------------|
| [Stavningskontroll](speller-how-to-add.md) | Korrigerar skrivfel innan sökorden når sökmotorn. |
| [Semantisk rangordning](semantic-ranking.md) | Använder kontexten eller semantiskt innebörd för att beräkna en ny relevans poäng. |
| [Semantiska beskrivningar och högdagrar](semantic-how-to-query-request.md) | Meningar och fraser från ett dokument som bäst sammanfattar innehållet, med högdagrar över viktiga passager för enkel sökning. Under texter som sammanfattar ett resultat är användbart när enskilda innehålls fält är för kompakta för resultat sidan. Markerad text förhöjer de mest relevanta termerna och fraserna så att användarna snabbt kan avgöra varför en matchning ansågs vara relevant. |
| [Semantiska svar](semantic-answers.md) | En valfri och ytterligare under struktur som returneras från en semantisk fråga. Det ger ett direkt svar på en fråga som ser ut som en fråga. |

### <a name="order-of-operations"></a>Åtgärds ordning

Komponenter för semantisk sökning utökar den befintliga pipeline för körning av frågor i båda riktningarna. Om du aktiverar stavnings korrigering korrigeras stavarna i början av [stavningen](speller-how-to-add.md) innan villkoren når sökmotorn.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Semantiska komponenter i frågekörningen" border="true":::

Frågekörningen fortsätter som vanligt, med termen tolkning, analys och skanningar över de inverterade indexen. Motorn hämtar dokument med hjälp av token-matchning och ger resultaten med hjälp av [standard bedömnings algoritmen för likhet](index-similarity-and-scoring.md#similarity-ranking-algorithms). Poängen beräknas baserat på den språkliga likheten mellan sökorden och matchnings villkoren i indexet. Om du har definierat dem tillämpas bedömnings profiler också i det här skedet. Resultaten skickas sedan till under systemet för semantisk sökning.

I förberedelse steget analyseras det dokument sökkorpus som returnerades från den första resultat uppsättningen på menings-och stycke nivå för att hitta resultat som sammanfattar varje dokument. I motsats till nyckelords sökning använder det här steget maskin läsning och förståelse för att utvärdera innehållet. Genom den här fasen av innehålls bearbetning returnerar en semantisk fråga under [texter](semantic-how-to-query-request.md) och [svar](semantic-answers.md). För att formulera dem använder semantisk sökning en språk representation för att extrahera och markera nyckel passager som bäst sammanfattar ett resultat. Om Sök frågan är en fråga – och svar begärs – svaret innehåller även en text passage som bäst svarar på frågan, som uttrycks av Sök frågan. 

För både bild texter och svar används befintlig text i formuleringen. De semantiska modellerna skapar inte nya meningar eller fraser från det tillgängliga innehållet, och använder inte heller logik för att komma till nya slut satser. I korthet returnerar systemet aldrig innehåll som inte redan finns.

Resultaten visas sedan på nytt baserat på det [konceptuella likhets](semantic-ranking.md) tecknet för sökord.

Om du vill använda semantiska funktioner i frågor måste du göra små ändringar i [sökbegäran](semantic-how-to-query-request.md), men ingen extra konfiguration eller Omindexering krävs.

## <a name="availability-and-pricing"></a>Tillgänglighet och priser

Semantiska funktioner är tillgängliga via [registrerings registrering](https://aka.ms/SemanticSearchPreviewSignup), på search Services som skapats på en standard nivå (S1, S2, S3), som finns i något av följande regioner: Norra centrala USA, västra USA, västra USA 2, östra USA 2, norra Europa, Västeuropa. 

Stavnings korrigering är tillgänglig i samma regioner, men saknar nivå begränsningar. Om du har en befintlig tjänst som uppfyller villkoret för nivån och regionen krävs bara registrering.

Mellan för hands versions lansering den 2 mars till och med den 1 april erbjuds stavnings korrigering och semantisk rangordning utan kostnad. Efter den 1 april blir beräknings kostnaden för att köra den här funktionen en fakturerbar händelse. Den förväntade kostnaden är cirka USD $500/månad för 250 000 frågor. Du hittar detaljerad kostnads information som beskrivs på [sidan kognitiv sökning prissättning](https://azure.microsoft.com/pricing/details/search/) och i [Beräkna och hantera kostnader](search-sku-manage-costs.md).

## <a name="next-steps"></a>Nästa steg

Med en ny frågetyp aktive ras rangordning och svars struktur för semantisk sökning.

Kom igång genom att [skapa en semantisk fråga](semantic-how-to-query-request.md) . Du kan också läsa följande artiklar om relaterad information.

+ [Lägg till stavnings kontroll i sökord](speller-how-to-add.md)
+ [Returnera ett semantiskt svar](semantic-answers.md)
+ [Semantisk rangordning](semantic-ranking.md)
+ [Introduktion till semantisk sökning (blogg inlägg)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Hitta meningsfulla insikter med semantiska funktioner (AI show video)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)