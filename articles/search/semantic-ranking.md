---
title: Semantisk rangordning
titleSuffix: Azure Cognitive Search
description: Beskriver algoritmen för semantisk rankning i Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 01c4d6475ec23b8a55d91e18f49cab27760aa907
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604295"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantisk rangordning i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantiska Sök funktioner finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick, under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)och är inte garanterade att ha samma implementering vid allmän tillgänglighet. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

Semantisk rangordning är ett tillägg till pipelinen för frågekörning som förbättrar precisionen och återkallas genom att rangordna de främsta matchningarna i en första resultat uppsättning. Semantisk rangordning backas upp av den avancerade djupgående datorn som läser förståelse modeller, tränade för frågor som uttrycks på naturligt språk i stället för språkmatchade mot nyckelord. Till skillnad från [standardalgoritmen för likhet med likhet](index-ranking-similarity.md)använder den semantiska rangordningen kontexten och innebörden av ord för att avgöra relevansen.

## <a name="how-semantic-ranking-works"></a>Så här fungerar semantisk rangordning

Den semantiska rangordningen är både resurs-och tids intensiv. För att kunna slutföra bearbetningen inom den förväntade svars tiden för en fråga, tar modellen som indata som bara de översta 50-dokumenten som returneras från [standardalgoritmen för likhets sortering](index-ranking-similarity.md). Resultat från den inledande rangordningen kan omfatta mer än 50 matchningar, men endast den första 50 kommer att omrankas semantiskt. 

För semantisk rangordning använder modellen både maskin läsnings förståelse och överföring av inlärning för att omvärdera dokumenten baserat på hur väl var och en matchar frågans avsikt.

### <a name="preparation-passage-extraction-phase"></a>Förberedelse fas (passage extrahering)

För varje dokument i de första resultaten finns det en passage extrahering som identifierar nyckel passager. Det här är en downsizing-övning som minskar innehållet till en mängd som kan bearbetas snabbt.

1. För var och en av 50-dokumenten utvärderas varje fält i searchFields-parametern i löpande ordning. Innehållet från varje fält samlas in i en lång sträng. 

1. Den långa strängen trimmas sedan för att säkerställa att den totala längden är högst 8 000 tokens. Därför rekommenderar vi att du placerar koncisa fält först så att de ingår i strängen. Om du har mycket stora dokument med text – tungt fält, kommer allt efter token-gränsen att ignoreras.

1. Varje dokument representeras nu av en enda lång sträng som är upp till 8 000 tokens. De här strängarna skickas till sammanfattnings modellen, vilket kommer att minska strängen ytterligare. Sammanfattnings modellen utvärderar den långa strängen för viktiga meningar eller omlopp som bäst sammanfattar dokumentet eller besvarar frågan.

1. Utdata från den här fasen är en beskrivning (och eventuellt ett svar). Texten är högst 128 tokens per dokument och betraktas som det mest representativa dokumentet.

### <a name="scoring-and-ranking-phases"></a>Bedömnings-och rangordnings faser

I den här fasen utvärderas alla 50-beskrivningar för att utvärdera relevans.

1. Poängen bestäms genom utvärdering av varje rubrik för konceptuell och semantisk relevans i förhållande till den angivna frågan.

   Följande diagram visar en illustration av vad "semantisk relevans" innebär. Betrakta termen "kapital", som kan användas inom ramen för ekonomi, lag, geografi eller grammatik. Om en fråga innehåller termer från samma vektor utrymme (t. ex. "kapital" och "investering"), kommer ett dokument som även innehåller tokens i samma kluster att bli högre än ett som inte är det.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektor representation för kontext" border="true":::

1. Utdata från den här fasen är en @search.rerankerScore tilldelad till varje dokument. När alla dokument har räknats upp visas de i fallande ordning och ingår i svars nytto lasten för frågan.

## <a name="next-steps"></a>Nästa steg

Semantisk rangordning erbjuds på standard-nivåer, i vissa regioner. Mer information och registrera dig finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing). Med en ny frågetyp aktive ras rangordning och svars struktur för semantisk sökning. Kom igång genom att [skapa en semantisk fråga](semantic-how-to-query-request.md).

Du kan också läsa någon av följande artiklar om relaterad information.

+ [Översikt över semantisk sökning](semantic-search-overview.md)
+ [Returnera ett semantiskt svar](semantic-answers.md)