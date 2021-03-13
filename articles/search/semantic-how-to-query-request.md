---
title: Skapa en semantisk fråga
titleSuffix: Azure Cognitive Search
description: Ange en semantisk frågetyp för att koppla de djup inlärnings modellerna till att fråga bearbetning, härleda avsikter och kontext som en del av Sök rangordning och relevans.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9ff98a2613143474afd6041ccf52d4eb509d646b
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418886"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Skapa en semantisk fråga i Kognitiv sökning

> [!IMPORTANT]
> Typen av semantisk fråga finns i en offentlig för hands version, som är tillgänglig via REST API och Azure Portal för för hands versionen. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

I den här artikeln får du lära dig att formulera en sökbegäran som använder semantisk rangordning. Begäran kommer att returnera semantiska beskrivningar och eventuellt [semantiska svar](semantic-answers.md), med högdagrar över de mest relevanta termerna och fraserna.

Både bild texter och svar extraheras orda Grant från text i Sök dokumentet. Det semantiska under systemet avgör vilket innehåll som har egenskaperna för en under text eller ett svar, men det skapar inte nya meningar eller fraser. Av den anledningen fungerar innehåll som innehåller förklaringar eller definitioner bäst för semantisk sökning.

## <a name="prerequisites"></a>Förutsättningar

+ En Sök tjänst på standard nivån (S1, S2, S3), som finns i någon av följande regioner: Norra centrala USA, västra USA, västra USA 2, östra USA 2, norra Europa, Västeuropa. Om du har en befintlig S1 eller större tjänst i någon av dessa regioner kan du begära åtkomst utan att behöva skapa en ny tjänst.

+ Åtkomst till för hands version av semantisk sökning: [Registrera dig](https://aka.ms/SemanticSearchPreviewSignup)

+ Ett befintligt Sök index som innehåller engelskt innehåll

+ En Sök klient för att skicka frågor

  Sök klienten måste ha stöd för för hands versioner av REST-API: er i förfrågan. Du kan använda [Postman](search-get-started-rest.md), [Visual Studio-kod](search-get-started-vs-code.md)eller kod som du har ändrat för att göra rest-anrop till för hands versions-API: erna. Du kan också använda [Sök Utforskaren](search-explorer.md) i Azure Portal för att skicka en semantisk fråga.

+ En [fråge förfrågan](/rest/api/searchservice/preview-api/search-documents) måste innehålla semantiskt alternativ och andra parametrar som beskrivs i den här artikeln.

## <a name="whats-a-semantic-query"></a>Vad är en semantisk fråga?

I Kognitiv sökning är en fråga en parametriserad begäran som avgör bearbetning av frågor och svars form. En *semantisk fråga* lägger till parametrar som anropar den semantiska ranknings modellen som kan bedöma kontexten och betydelsen av matchnings resultat, höja mer relevanta matchningar till toppen och returnera semantiska svar och bild texter.

Följande begäran är representativ för en minimal semantisk fråga (utan svar).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Precis som med alla frågor i Kognitiv sökning riktar begäran dokument samlingen för ett enskilt index. Dessutom har en semantisk fråga samma sekvens med parsning, analys, skanning och bedömning som en icke-semantisk fråga. 

Skillnaden är relevans och poäng. Som definieras i den här för hands versionen är en semantisk fråga en vars *resultat* rangordnas med hjälp av en semantisk språk modell, vilket ger ett sätt att presentera de matchningar som bedöms mest relevant av semantisk rangation i stället för poängen som tilldelas av standardalgoritmen för algoritmen för likheter.

Endast de översta 50-matchningarna från de första resultaten kan semantiskt rangordnas och alla inkludera under texter i svaret. Alternativt kan du ange en **`answer`** parameter på begäran för att extrahera ett möjligt svar. Mer information finns i [semantiska svar](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Fråga med Sökutforskaren

[Sök Utforskaren](search-explorer.md) har uppdaterats för att inkludera alternativ för semantiska frågor. De här alternativen blir synliga i portalen när du får åtkomst till förhands granskningen. Frågealternativen kan aktivera semantiska frågor, searchFields och stavnings korrigering.

Du kan också klistra in de obligatoriska frågeparametrarna i frågesträngen.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Frågealternativ i Sök Utforskaren" border="true":::

## <a name="query-using-rest"></a>Fråga med REST

Använd [Sök dokumenten (rest Preview)](/rest/api/searchservice/preview-api/search-documents) för att formulera begäran program mässigt.

Ett svar innehåller under texter och markeringar automatiskt. Om du vill att svaret ska innehålla stavnings korrigering eller-svar lägger du till en valfri **`speller`** **`answers`** parameter eller parameter på begäran.

I följande exempel används hotell-Sample-indexet för att skapa en semantisk förfrågan med semantiska svar och bild texter:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

I följande tabell sammanfattas frågeparametrar som används i en semantisk fråga så att du kan se dem holistiskt. En lista över alla parametrar finns i [Sök efter dokument (rest-förhands granskning)](/rest/api/searchservice/preview-api/search-documents)

| Parameter | Typ | Beskrivning |
|-----------|-------|-------------|
| queryType | Sträng | Giltiga värden är enkel, fullständig och semantisk. Värdet "semantisk" krävs för semantiska frågor. |
| queryLanguage | Sträng | Krävs för semantiska frågor. För närvarande är endast "en-US" implementerad. |
| searchFields | Sträng | En kommaavgränsad lista över sökbara fält. Valfritt men rekommenderas. Anger de fält över vilka semantisk rangordning inträffar. </br></br>I motsats till enkla och fullständiga frågetyper, bestämmer ordningen i vilka fält som är prioritet. Mer information om användning finns i [steg 2: Ange searchFields](#searchfields). |
| stavningskontroll | Sträng | Valfri parameter, inte bara för semantiska frågor, som korrigerar felstavade villkor innan de når sökmotorn. Mer information finns i [lägga till stavnings korrigering i frågor](speller-how-to-add.md). |
| svar |Sträng | Valfria parametrar som anger om semantiska svar ingår i resultatet. För närvarande implementeras endast "extraktion". Svar kan konfigureras för att returnera högst fem. Standardvärdet är ett. Det här exemplet visar antalet tre svar: "extraherings \| count3". Mer information finns i [returnera semantiska svar](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formulera begäran

I det här avsnittet beskrivs de frågeparametrar som krävs för semantisk sökning.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Steg 1: Ange frågetyp och queryLanguage

Lägg till följande parametrar i resten. Båda parametrarna måste anges.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

QueryLanguage måste vara konsekvent med alla [språk analys](index-add-language-analyzers.md) verktyg som har tilldelats fält definitioner i index schemat. Om queryLanguage är "en-US" måste alla språk analyser också vara en engelsk variant ("en. Microsoft" eller "en. Lucene"). Alla språk oberoende analyser, till exempel nyckelord eller enkla, har ingen konflikt med queryLanguage-värden.

Om du också använder [stavnings korrigering](speller-how-to-add.md)i en förfrågan, gäller den queryLanguage som du anger lika med stavning, svar och bild texter. Det finns ingen åsidosättning för enskilda delar. 

Även om innehåll i ett Sök index kan bestå av flera språk, är frågans Indatatyp mest troligt i ett. Sökmotorn kontrollerar inte kompatibiliteten för queryLanguage, språk analys och det språk som innehållet består av, så se till att omfattnings frågorna inte genererar felaktiga resultat.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Steg 2: Ange searchFields

Den här parametern är valfri i att det inte finns något fel om du lämnar den, men om du anger en ordnad lista med fält rekommenderar vi att både bild texter och svar används.

Parametern searchFields används för att identifiera passager som ska utvärderas för "semantisk likhet" i frågan. För för hands versionen rekommenderar vi inte att du lämnar searchFields tomt eftersom modellen kräver ett tips för vilka fält som är viktigast att bearbeta.

SearchFields-ordningen är kritisk. Om du redan använder searchFields i befintliga enkla eller fullständiga Lucene-frågor måste du gå tillbaka till den här parametern för att kontrol lera om det finns fält ordning när du växlar till en semantisk frågetyp.

Följ dessa rikt linjer för att säkerställa optimala resultat när två eller fler searchFields har angetts:

+ Inkludera endast sträng fält och toppnivå sträng fält i samlingar. Om du råkar ta med fält som inte är strängar eller fält på lägre nivåer i en samling, finns det inga fel, men dessa fält används inte i semantisk rangordning.

+ Det första fältet måste alltid vara koncist (till exempel titel eller namn), helst under 25 ord.

+ Om indexet har ett URL-fält som är text (som kan läsas av människa, t. ex `www.domain.com/name-of-the-document-and-other-details` ., och inte maskin fokuserat som `www.domain.com/?id=23463&param=eis` ), placerar du det andra i listan (eller första om det inte finns något koncist rubrik fält).

+ Följ dessa fält genom beskrivande fält där svaret på semantiska frågor kan hittas, till exempel huvud innehållet i ett dokument.

Om bara ett fält har angetts använder du ett beskrivande fält där svaret på semantiska frågor kan hittas, till exempel huvud innehållet i ett dokument. Välj ett fält som tillhandahåller tillräckligt med innehåll. För att säkerställa bearbetnings tiden kan endast cirka 8 000 tokens av det samlade innehållet i searchFields genomgå semantisk utvärdering och rangordning.

#### <a name="step-3-remove-orderby-clauses"></a>Steg 3: ta bort orderBy-satser

Ta bort eventuella orderBy-satser, om de finns i en befintlig begäran. Det semantiska resultatet används för att beställa resultat, och om du inkluderar explicit sorterings logik returneras ett HTTP 400-fel.

#### <a name="step-4-add-answers"></a>Steg 4: Lägg till svar

Du kan också lägga till "svar" om du vill inkludera ytterligare bearbetning som ger ett svar. Svar (och under texter) extraheras från passager som finns i fält som anges i searchFields. Se till att inkludera innehålls rika fält i searchFields för att få bästa svar i ett svar. Mer information finns i [så här returnerar du semantiska svar](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>Steg 5: Lägg till andra parametrar

Ange andra parametrar som du vill ha i begäran. Parametrar som [stavfel](speller-how-to-add.md), [Select](search-query-odata-select.md)och Count förbättrar kvaliteten på begäran och läsningen av svaret.

Du kan också anpassa markerings formatet som används för under texter. Under texter används Markera formatering framför nyckel passager i det dokument som sammanfattar svaret. Standardvärdet är `<em>`. Om du vill ange typ av formatering (till exempel gul bakgrund) kan du ange highlightPreTag och highlightPostTag.

## <a name="evaluate-the-response"></a>Utvärdera svaret

Som med alla frågor består ett svar av alla fält som har marker ATS som hämtnings bara eller bara de fält som anges i SELECT-parametern. Den innehåller den ursprungliga relevansen och kan också innehålla ett antal, eller batchade resultat, beroende på hur du formulerat begäran.

I en semantisk fråga har svaret ytterligare element: en ny semantiskt rangordnad resultat text, under texter i klartext och med högdagrar och eventuellt ett svar.

I en klient app kan du strukturera Sök sidan så att den innehåller en beskrivning av matchningen, i stället för hela innehållet i ett särskilt fält. Detta är användbart när enskilda fält är för kompakta för sidan Sök resultat.

Svaret för exempel frågan ovan returnerar följande matchning som den översta plockningen. Under texter returneras automatiskt med oformaterad text och markerade versioner. Svar utelämnas från exemplet eftersom ett inte kunde fastställas för den aktuella fråge-och sökkorpus.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Nästa steg

Kom ihåg att semantisk rangordning och svar skapas över en första resultat uppsättning. All logik som förbättrar kvaliteten på de första resultaten kommer att överföras till semantisk sökning. I nästa steg ska du gå igenom de funktioner som bidrar till de första resultaten, inklusive analys verktyg som påverkar hur strängarna är tokens, bedömnings profiler som kan justera resultat och standardalgoritmen för relevans.

+ [Analys verktyg för text bearbetning](search-analyzers.md)
+ [Likhet och poängsättning i Kognitiv sökning](index-similarity-and-scoring.md)
+ [Lägga till rankningsprofiler](index-add-scoring-profiles.md)
+ [Översikt över semantisk sökning](semantic-search-overview.md)
+ [Lägg till stavnings kontroll i sökord](speller-how-to-add.md)
