---
title: Semantisk rangordning
titleSuffix: Azure Cognitive Search
description: Lär dig hur algoritmen för semantisk rankning fungerar i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562043"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantisk rangordning i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantiska Sök funktioner finns i offentlig för hands version, som är tillgängliga via förhands granskningen REST API och portalen. För hands versions funktionerna erbjuds i befintligt skick, under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)och är inte garanterade att ha samma implementering vid allmän tillgänglighet. Dessa funktioner är fakturerbara. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

Semantisk rangordning är ett tillägg till pipeline för frågekörningen som förbättrar precisionen genom att rangordna de främsta matchningarna i en första resultat uppsättning. Semantisk rangordning backas upp av stora transformatorbaserade nätverk, tränas för att samla in den semantiska innebörden av sökord, till skillnad från språkliga matchning för nyckelord. Till skillnad från [standardalgoritmen för likhet med likhet](index-ranking-similarity.md)använder den semantiska rangordningen kontexten och innebörden av ord för att avgöra relevansen.

Semantisk rangordning är både resurs-och tids intensiv. För att kunna slutföra bearbetningen inom den förväntade svars tiden för en fråga konsol IDE ras och minskas indata till semantisk rangordning så att den underliggande sammanfattningen och omrangering av stegen kan slutföras så snabbt som möjligt.

## <a name="pre-processing"></a>För bearbetning

Före en bedömning av relevans måste innehållet minskas till ett hanterbart antal indata som kan hanteras effektivt av semantisk rangation.

1. För det första börjar innehålls minskningen med den första resultat uppsättningen som returnerades av standard [rangordningen för likheter](index-ranking-similarity.md) som används för nyckelords sökning. För en specifik fråga kan resultaten vara en fåtal av dokument, upp till max gränsen på 1 000. Eftersom bearbetning av ett stort antal matchningar skulle ta för lång tid tar det bara det översta 50-förloppet till semantisk rangordning.

   Oavsett dokument antal, anger om en eller 50 den första iterationen av dokumentet sökkorpus för semantisk rangordning.

1. Därefter extraheras innehållet i varje fält i "searchFields" och kombineras till en lång sträng i sökkorpus.

1. Efter sträng konsolideringen rensas alla strängar som är alltför långa för att säkerställa att den totala längden uppfyller de krav som infaller i sammanfattnings steget.

   Den här trimnings övningen är varför det är viktigt att placera koncisa fält först i "searchFields" för att se till att de ingår i strängen. Om du har mycket stora dokument med text – tungt fält, kommer allt efter den maximala gränsen att ignoreras.

Varje dokument representeras nu av en enda lång sträng.

> [!NOTE]
> Strängen består av tokens, inte tecken eller ord. Tokenisering bestäms delvis av Analyzer-tilldelningen i sökbara fält. Om du använder specialiserad analys, till exempel nGram eller EdgeNGram, kanske du vill utesluta fältet från searchFields. Om du vill veta mer om hur strängar är token kan du granska utdata från en analys med hjälp av [test analys REST API](/rest/api/searchservice/test-analyzer).

## <a name="extraction"></a>Extrahering

Efter en sträng minskning är det nu möjligt att klara de reducerade indatana genom maskin läsnings förståelse och språk representations modeller för att fastställa vilka meningar och fraser som bäst sammanfattar dokumentet, i förhållande till frågan. Den här fasen extraherar innehåll från den sträng som flyttas framåt till den semantiska rangordningen.

Indata till sammanfattning är de långa strängarna som hämtas för varje dokument i förberedelse fasen. Från varje sträng hittar sammanfattnings modellen en passage som är mest representativ. Den här passageen utgör även en [semantisk Beskrivning](semantic-how-to-query-request.md) av dokumentet. Varje beskrivning är tillgänglig i en oformaterad text version och en markerings version och är ofta mindre än 200 ord per dokument.

Ett [semantiskt svar](semantic-answers.md) kommer också att returneras om du har angett parametern "svar", om frågan befann sig som en fråga, och om en passage finns i den långa strängen som kan ge ett svar på frågan.

## <a name="semantic-ranking"></a>Semantisk rangordning

1. Under texter utvärderas för konceptuell och semantisk relevans i förhållande till den angivna frågan.

   Följande diagram visar en illustration av vad "semantisk relevans" innebär. Betrakta termen "kapital", som kan användas inom ramen för ekonomi, lag, geografi eller grammatik. Om en fråga innehåller termer från samma vektor utrymme (t. ex. "kapital" och "investering"), kommer ett dokument som även innehåller tokens i samma kluster att bli högre än ett som inte är det.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektor representation för kontext" border="true":::

1. @search.rerankerScoreTilldelas varje dokument baserat på rubrikens semantiska relevans.

1. När alla dokument har räknats upp visas de i fallande ordning efter poäng och ingår i svars nytto lasten för frågan. Nytto lasten innehåller svar, oformaterad text och markerade rubriker och fält som du har markerat som hämtnings bara eller angivna i en SELECT-sats.

## <a name="next-steps"></a>Nästa steg

Semantisk rangordning erbjuds på standard-nivåer, i vissa regioner. Mer information om tillgänglighet och registrering finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing). En ny frågetyp aktiverar ranknings-och svars strukturerna för semantisk sökning. Kom igång genom att [skapa en semantisk fråga](semantic-how-to-query-request.md).

Du kan också läsa följande artiklar om standard rangordning. Semantisk rangordning beror på likhets graderna för att returnera de första resultaten. Att veta mer om frågekörning och rankning ger dig en bred förståelse för hur hela processen fungerar.

+ [Full texts ökning i Azure Kognitiv sökning](search-lucene-query-architecture.md)
+ [Likhet och bedömning i Azure Kognitiv sökning](index-similarity-and-scoring.md)
+ [Analys verktyg för text bearbetning i Azure Kognitiv sökning](search-analyzers.md)