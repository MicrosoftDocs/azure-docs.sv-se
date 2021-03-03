---
title: Lägg till stavnings kontroll
titleSuffix: Azure Cognitive Search
description: Koppla stavnings korrigering till frågans pipeline för att åtgärda skrivfel på sökord innan frågan körs.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: cdc5de8153e8b2e0ea8bb8ea372fe8610ccb895b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680358"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Lägg till stavnings kontroll i frågor i Kognitiv sökning

> [!IMPORTANT]
> Stavnings korrigering är i offentlig för hands version, endast tillgängligt via för hands versionen REST API. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vid den första förhands granskningen är det ingen avgift för stavnings kontroll. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

Du kan förbättra återkallning genom att stavnings kontrol lera villkoren för enskilda Sök frågor innan de når sökmotorn. **Stavnings** parametern stöds för alla frågetyper: [enkel](query-simple-syntax.md), [fullständig](query-lucene-syntax.md)och det nya [semantiska](semantic-how-to-query-request.md) alternativet för närvarande finns i en offentlig för hands version.

## <a name="prerequisites"></a>Förutsättningar

+ Ett befintligt Sök index som innehåller engelskt innehåll

+ En Sök klient för att skicka frågor

  Sök klienten måste ha stöd för för hands versioner av REST-API: er i förfrågan. Du kan använda [Postman](search-get-started-rest.md), [Visual Studio-kod](search-get-started-vs-code.md)eller kod som du har ändrat för att göra rest-anrop till för hands versions-API: erna.

+ [En fråge förfrågan](/rest/api/searchservice/preview-api/search-documents) som använder stavnings korrigering har "API-version = 2020-06 -30-Preview", "stavar = lexikon" och "queryLanguage = en-US".

  QueryLanguage krävs för stavnings kontroll och för närvarande är "en-US" det enda giltiga värdet.

> [!Note]
> Stavnings parametern är tillgänglig på alla nivåer, i samma regioner som innehåller semantisk sökning. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

## <a name="spell-correction-with-simple-search"></a>Stavnings korrigering med enkel sökning

I följande exempel används det inbyggda hotell-och exempel indexet för att demonstrera stavnings korrigering i en enkel text fråga med fri form. Utan stavnings korrigering returnerar frågan noll resultat. Vid korrigering returnerar frågan ett resultat för Johnson-kundorienterade utväg.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Stavnings korrigering med fullständig Lucene

Stavnings korrigeringen inträffar för enskilda sökord som genomgår analyser, vilket är anledningen till att du kan använda stavnings parametern med vissa Lucene-frågor, men inte andra.

+ Inkompatibla fråge formulär som kringgår text analys är: jokertecken, regex, fuzzy
+ Kompatibla fråge formulär innehåller: fält Sök, närhet, term förstärkning

I det här exemplet används sökning i fält i fältet kategori, med fullständig Lucene-syntax och en felstavad frågeterm. Genom att inkludera stavnings kontroll korrigeras skrivfel i "Suiite" och frågan lyckas.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Stavnings korrigering med semantisk sökning

Den här frågan, med skrivfel i varje term, förutom en, gör stavnings korrigeringar för att returnera relevanta resultat. Mer information finns i [skapa en semantisk fråga](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Språk överväganden

Den queryLanguage-parameter som krävs för stavnings kontroll måste vara konsekvent med alla [språk analys](index-add-language-analyzers.md) verktyg som har tilldelats fält definitionerna i index schemat. QueryLanguage anger vilka lexikon som används för stavnings kontroll och används även som inmatade i [algoritmen för semantisk rankning](semantic-how-to-query-response.md) om du använder den. Språk analys verktyg används vid indexering och vid hämtning av matchande dokument i Sök indexet. Om queryLanguage är "en-US" måste alla språk analys verktyg också vara en engelsk variant ("en. Microsoft" eller "en. Lucene"). 

> [!NOTE]
> Språk-oberoende analys verktyg (till exempel nyckelord, enkel, standard, stopp, blank steg eller `standardasciifolding.lucene` ) är inte i konflikt med queryLanguage-inställningar.

I en fråge förfrågan gäller den queryLanguage som du anger lika med stavning, svar och under texter. Det finns ingen åsidosättning för enskilda delar.

Även om innehåll i ett Sök index kan bestå av flera språk, är frågans Indatatyp mest troligt i ett. Sökmotorn kontrollerar inte kompatibiliteten för queryLanguage, språk analys och det språk som innehållet består av, så se till att omfattnings frågorna inte genererar felaktiga resultat.

## <a name="next-steps"></a>Nästa steg

+ [Skapa en semantisk fråga](semantic-how-to-query-request.md)
+ [Skapa en grundläggande fråga](search-query-create.md)
+ [Använd fullständig Lucene-frågesyntax](query-Lucene-syntax.md)
+ [Använd enkel frågesyntax](query-simple-syntax.md)
+ [Översikt över semantisk sökning](semantic-search-overview.md)