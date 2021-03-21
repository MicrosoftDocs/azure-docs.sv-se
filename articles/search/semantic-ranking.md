---
title: Semantisk rangordning
titleSuffix: Azure Cognitive Search
description: Lär dig hur algoritmen för semantisk rankning fungerar i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bb65a53f1ba6e97a39bd0c0170c5c41da38aee8b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720516"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantisk rangordning i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantiska Sök funktioner finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick, under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)och är inte garanterade att ha samma implementering vid allmän tillgänglighet. Dessa funktioner är fakturerbara. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

Semantisk rangordning är ett tillägg till pipelinen för frågekörning som förbättrar precisionen och återkallas genom att rangordna de främsta matchningarna i en första resultat uppsättning. Semantisk rangordning backas upp av den avancerade maskin varan som läser förståelse modeller, tränas för frågor som uttrycks på naturligt språk i stället för språk matchning på nyckelord. Till skillnad från [standardalgoritmen för likhet med likhet](index-ranking-similarity.md)använder den semantiska rangordningen kontexten och innebörden av ord för att avgöra relevansen.

Semantisk rangordning är både resurs-och tids intensiv. För att slutföra bearbetningen inom den förväntade svars tiden för en åtgärd, konsol IDE ras och förenklas indata så att Sammanfattning och analys kan slutföras så snabbt som möjligt.

## <a name="preparation-for-semantic-ranking"></a>Förberedelse för semantisk rangordning

Innan du ansöker om relevans måste innehållet minskas till en mängd parametrar som kan hanteras effektivt av semantisk rangation. Innehålls minskning innehåller följande steg.

1. Innehålls minskningen börjar med att använda de första resultaten som returneras av standard [rangordningen för likheter](index-ranking-similarity.md) som används för nyckelords sökning. Sök resultaten kan innehålla upp till 1 000 träffar, men semantisk rangordning bearbetar bara den översta 50. 

   Med tanke på frågan kan de första resultaten vara mycket mindre än 50, beroende på hur många matchningar som hittades. Oavsett antalet dokument är den första resultat uppsättningen sökkorpus för semantisk rangordning.

1. I dokumentet sökkorpus extraheras innehållet i varje fält i "searchFields" och kombineras till en lång sträng.

1. Alla strängar som är alltför långa rensas för att se till att den totala längden uppfyller ingångs kraven för sammanfattnings modellen. Den här trimnings övningen är varför det är viktigt att placera koncisa fält först i "searchFields" för att se till att de ingår i strängen. Om du har mycket stora dokument med text – tungt fält, kommer allt efter den maximala gränsen att ignoreras.

Varje dokument representeras nu av en enda lång sträng.

> [!NOTE]
> Parameter indata till modeller är token som inte är tecken eller ord. Tokenisering bestäms delvis av Analyzer-tilldelningen i sökbara fält. Om du vill veta mer om hur strängar är token kan du granska utdata från en analys med hjälp av [test analys REST API](/rest/api/searchservice/test-analyzer).
>
> För närvarande kan långa strängar vara högst 8 000 tokens i den här för hands versionen. Om sökningen Miss lyckas med att leverera ett förväntat svar från djupet i ett dokument kan du läsa mer om hur du kan ta reda på varför. 

## <a name="summarization"></a>Sammanfattning

Efter sträng minskning är det nu möjligt att skicka parametrarna genom maskin läsnings förståelse och språk representation för att avgöra vilka meningar och fraser som bäst sammanfattar modellen, i förhållande till frågan.

Indata till sammanfattning är den långa strängen från förberedelse fasen. Från det inmatade resultatet utvärderar sammanfattnings modellen innehållet för att hitta de som är mest representativa.

Output är en [semantisk text Beskrivning](semantic-how-to-query-request.md), i klartext och med högdagrar. Texten är mindre än den långa strängen, vanligt vis färre än 200 ord per dokument, och det anses vara det mest representativa dokumentet. 

Ett [semantiskt svar](semantic-answers.md) kommer också att returneras om du har angett parametern "svar", om frågan befann sig som en fråga och om en passage kan hittas i den långa strängen som ser ut som ett plausible svar på frågan.

## <a name="scoring-and-ranking"></a>Poängsättning och rangordning

Nu har du nu under texter för varje dokument. Under texter utvärderas för frågan.

1. Poängen bestäms genom utvärdering av varje rubrik för konceptuell och semantisk relevans i förhållande till den angivna frågan.

   Följande diagram visar en illustration av vad "semantisk relevans" innebär. Betrakta termen "kapital", som kan användas inom ramen för ekonomi, lag, geografi eller grammatik. Om en fråga innehåller termer från samma vektor utrymme (t. ex. "kapital" och "investering"), kommer ett dokument som även innehåller tokens i samma kluster att bli högre än ett som inte är det.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektor representation för kontext" border="true":::

1. Utdata från den här fasen är en @search.rerankerScore tilldelad till varje dokument. När alla dokument har räknats upp visas de i fallande ordning och ingår i svars nytto lasten för frågan. Nytto lasten innehåller svar, oformaterad text och markerade rubriker och fält som du har markerat som hämtnings bara eller angivna i en SELECT-sats.

## <a name="next-steps"></a>Nästa steg

Semantisk rangordning erbjuds på standard-nivåer, i vissa regioner. Mer information om tillgänglig och registrering finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing). Med en ny frågetyp aktive ras rangordning och svars struktur för semantisk sökning. Kom igång genom att [skapa en semantisk fråga](semantic-how-to-query-request.md).

Du kan också läsa följande artiklar om standard rangordning. Semantisk rangordning beror på likhets graderna för att returnera de första resultaten. Att veta mer om frågekörning och rankning ger dig en bred förståelse för hur hela processen fungerar.

+ [Full texts ökning i Azure Kognitiv sökning](search-lucene-query-architecture.md)
+ [Likhet och bedömning i Azure Kognitiv sökning](index-similarity-and-scoring.md)
+ [Analys verktyg för text bearbetning i Azure Kognitiv sökning](search-analyzers.md)