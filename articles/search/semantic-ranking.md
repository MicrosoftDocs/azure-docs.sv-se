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
ms.openlocfilehash: a008551ac6f149617feedd01e256b637f83e975d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235060"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantisk rangordning i Azure Kognitiv sökning

> [!IMPORTANT]
> Semantiska Sök funktioner finns i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick, under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)och är inte garanterade att ha samma implementering vid allmän tillgänglighet. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

Semantisk rangordning är ett tillägg till pipelinen för frågekörning som förbättrar precisionen och återkallas genom att rangordna de främsta matchningarna i en första resultat uppsättning. Semantisk rangordning backas upp av den avancerade djupgående datorn som läser förståelse modeller, tränade för frågor som uttrycks på naturligt språk i stället för språkmatchade mot nyckelord. Till skillnad från [standardalgoritmen för likhet med likhet](index-ranking-similarity.md)använder den semantiska rangordningen kontexten och innebörden av ord för att avgöra relevansen.

## <a name="how-semantic-ranking-works"></a>Så här fungerar semantisk rangordning

Den semantiska rangordningen är både resurs-och tids intensiv. För att kunna slutföra bearbetningen inom den förväntade svars tiden för en fråga, tar modellen som indata som bara de översta 50-dokumenten som returneras från [standardalgoritmen för likhets sortering](index-ranking-similarity.md). Resultat från den inledande rangordningen kan omfatta mer än 50 matchningar, men endast den första 50 kommer att omrankas semantiskt. 

För semantisk rangordning använder modellen både maskin läsnings förståelse och överföring av inlärning för att omvärdera dokumenten baserat på hur väl var och en matchar frågans avsikt.

1. För varje dokument utvärderar semantisk rangordning fälten i searchFields-parametern i ordning, och konsoliderar innehållet till en stor sträng.

1. Strängen trimmas sedan för att säkerställa att den totala längden är högst 20 000 tokens. Om du har mycket stora dokument, med ett innehålls fält eller merged_content fält som har många sidor med innehåll, kommer endast de första 20 000 tokens att användas.

1. Var och en av de 50 dokumenten representeras nu av en enda lång sträng som är upp till 20 000 tokens. Den här strängen skickas till sammanfattnings modellen. Sammanfattnings modellen skapar under texter (och svar) med hjälp av maskin läsnings förståelse för att identifiera de passager som visas för att sammanfatta innehållet eller besvara frågan. Utdatan från sammanfattnings modellen är en ytterligare reducerad sträng, som är högst 128 tokens.

1. Den mindre strängen blir dokumentets rubrik och representerar de mest relevanta träffarna i den större strängen. Uppsättningen med 50 (eller färre) under texter rangordnas sedan i prioritetsordning. 

Konceptuell och semantisk relevans upprättas genom vektor representation och term kluster. En algoritm för nyckelords likhet kan ge samma vikt till valfri term i frågan, den semantiska modellen har tränats för att identifiera interdependency och relationer mellan ord som annars inte är relaterade till ytan. Det innebär att om en frågesträng innehåller termer från samma kluster rangordnas ett dokument som innehåller båda med högre än ett.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vektor representation för kontext" border="true":::

## <a name="next-steps"></a>Nästa steg

Semantisk rangordning erbjuds på standard-nivåer, i vissa regioner. Mer information och registrera dig finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

Med en ny frågetyp aktive ras rangordning och svars struktur för semantisk sökning. Kom igång genom att [skapa en semantisk fråga](semantic-how-to-query-request.md) .

Du kan också läsa någon av följande artiklar om relaterad information.

+ [Lägg till stavnings kontroll i sökord](speller-how-to-add.md)
+ [Returnera ett semantiskt svar](semantic-answers.md)