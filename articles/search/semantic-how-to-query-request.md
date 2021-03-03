---
title: Skapa en semantisk fråga
titleSuffix: Azure Cognitive Search
description: Ange en semantisk frågetyp för att koppla de djup inlärnings modellerna till att fråga bearbetning, härleda avsikter och kontext som en del av Sök rangordning och relevans.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 7551ef88c2251b64cf6f6db1de4fed22db2c69e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693653"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Skapa en semantisk fråga i Kognitiv sökning

> [!IMPORTANT]
> Typen av semantisk fråga finns i en offentlig för hands version, som är tillgänglig via REST API och Azure Portal för för hands versionen. För hands versions funktionerna erbjuds i befintligt skick under [kompletterande användnings villkor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vid den första förhands granskningen är det ingen kostnad för semantisk sökning. Mer information finns i [tillgänglighet och priser](semantic-search-overview.md#availability-and-pricing).

I den här artikeln får du lära dig att formulera en sökbegäran som använder semantisk rangordning och ger semantiska beskrivningar och svar.

## <a name="prerequisites"></a>Förutsättningar

+ En Sök tjänst på standard nivån (S1, S2, S3), som finns i någon av följande regioner: Norra centrala USA, västra USA, västra USA 2, östra USA 2, norra Europa, Västeuropa. Om du har en befintlig S1 eller större tjänst i någon av dessa regioner kan du begära åtkomst utan att behöva skapa en ny tjänst.

+ Åtkomst till för hands version av semantisk sökning: [Registrera dig](https://aka.ms/SemanticSearchPreviewSignup)

+ Ett befintligt Sök index som innehåller engelskt innehåll

+ En Sök klient för att skicka frågor

  Sök klienten måste ha stöd för för hands versioner av REST-API: er i förfrågan. Du kan använda [Postman](search-get-started-rest.md), [Visual Studio-kod](search-get-started-vs-code.md)eller kod som du har ändrat för att göra rest-anrop till för hands versions-API: erna. Du kan också använda [Sök Utforskaren](search-explorer.md) i Azure Portal för att skicka en semantisk fråga.

+ En [Sök dokument](/rest/api/searchservice/preview-api/search-documents) förfrågan med semantiskt alternativ och andra parametrar som beskrivs i den här artikeln.

## <a name="whats-a-semantic-query"></a>Vad är en semantisk fråga?

I Kognitiv sökning är en fråga en parametriserad begäran som avgör bearbetning av frågor och svars form. En *semantisk fråga* lägger till parametrar som anropar algoritmen för semantisk omrangering som kan bedöma kontexten och innebörden av matchande resultat och höja upp fler relevanta matchningar till toppen.

Följande begäran är representativ för en grundläggande semantisk fråga (utan svar).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

Precis som med alla frågor i Kognitiv sökning riktar begäran dokument samlingen för ett enskilt index. Dessutom utför en semantisk fråga samma sekvens med parsning, analys och skanning som en icke-semantisk fråga. Skillnaden beror på hur relevansen beräknas. Som definieras i den här för hands versionen är en semantisk fråga en vars *resultat* bearbetas på nytt med hjälp av avancerade algoritmer, vilket ger ett sätt att skapa en yta för de matchningar som bedöms mest relevanta av semantisk rangation i stället för de poäng som tilldelas av standardalgoritmen för likhets rankning. 

Endast de översta 50-matchningarna från de första resultaten kan semantiskt rangordnas och alla inkludera under texter i svaret. Alternativt kan du ange en **`answer`** parameter på begäran för att extrahera ett möjligt svar. Den här modellen upprättar upp till fem potentiella svar på frågan, som du kan välja att återge överst på Sök sidan.

## <a name="query-using-rest-apis"></a>Fråga med hjälp av REST API: er

Du hittar den fullständiga specifikationen för REST API i [sökdokument (rest-förhands granskning)](/rest/api/searchservice/preview-api/search-documents).

Semantiska frågor är avsedda för öppna frågor som "What ' The Best planta for pollinators" eller "How to Fry a ägg". Om du vill att svaret ska innehålla svar kan du lägga till en valfri **`answer`** parameter i begäran.

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

### <a name="formulate-the-request"></a>Formulera begäran

1. Ange **`"queryType"`** till "semantisk" och **`"queryLanguage"`** till "en-US". Båda parametrarna måste anges.

   QueryLanguage måste vara konsekvent med alla [språk analys](index-add-language-analyzers.md) verktyg som har tilldelats fält definitioner i index schemat. Om queryLanguage är "en-US" måste alla språk analyser också vara en engelsk variant ("en. Microsoft" eller "en. Lucene"). Alla språk oberoende analyser, till exempel nyckelord eller enkla, har ingen konflikt med queryLanguage-värden.

   Om du också använder [stavnings korrigering](speller-how-to-add.md)i en förfrågan, gäller den queryLanguage som du anger lika med stavning, svar och bild texter. Det finns ingen åsidosättning för enskilda delar. 

   Även om innehåll i ett Sök index kan bestå av flera språk, är frågans Indatatyp mest troligt i ett. Sökmotorn kontrollerar inte kompatibiliteten för queryLanguage, språk analys och det språk som innehållet består av, så se till att omfattnings frågorna inte genererar felaktiga resultat.

<a name="searchfields"></a>

1. Ange **`"searchFields"`** (valfritt, men rekommenderas).

   I en semantisk fråga återspeglar ordningen på fälten i "searchFields" den prioritet eller relativa prioriteten för fältet i semantisk rangordning. Endast sträng fält på översta nivån (fristående eller i en samling) används. Eftersom searchFields har andra beteenden i enkla och fullständiga Lucene-frågor (där det inte finns någon underförstådd prioritetsordning), resulterar det inte i ett fel i alla icke-sträng fält och under fält, men de används inte heller i semantisk rangordning.

   Följ dessa rikt linjer när du anger searchFields:

   + Koncisa fält, till exempel HotelName eller en rubrik, bör föregå utförliga fält som beskrivning.

   + Om ditt index har ett URL-fält som är text (som kan läsas av människa, t. ex., `www.domain.com/name-of-the-document-and-other-details` och inte maskin fokuserat som `www.domain.com/?id=23463&param=eis` ), så Lägg det andra i listan (Lägg det först om det inte finns något koncist rubrik fält).

   + Om det bara finns ett fält angivet, betraktas det som ett beskrivande fält för semantisk rangordning av dokument.  

   + Om det inte finns några fält angivna, kommer alla sökbara fält att beaktas för semantisk rangordning av dokument. Detta rekommenderas dock inte eftersom det kanske inte ger flest optimala resultat från Sök indexet.

1. Ta bort **`"orderBy"`** satser, om de finns i en befintlig begäran. Det semantiska resultatet används för att beställa resultat, och om du inkluderar explicit sorterings logik returneras ett HTTP 400-fel.

1. Du kan också lägga till **`"answers"`** set till "extraktion" och ange antalet svar om du vill ha mer än 1.

1. Du kan också anpassa markerings formatet som tillämpas på under texter. Under texter används Markera formatering framför nyckel passager i det dokument som sammanfattar svaret. Standardvärdet är `<em>`. Om du vill ange typ av formatering (till exempel gul bakgrund) kan du ange highlightPreTag och highlightPostTag.

1. Ange andra parametrar som du vill ha i begäran. Parametrar som [stavfel](speller-how-to-add.md), [Select](search-query-odata-select.md)och Count förbättrar kvaliteten på begäran och läsningen av svaret.

### <a name="review-the-response"></a>Granska svaret

Svar för ovanstående fråga returnerar följande matchning som den översta plockningen. Under texter returneras automatiskt med oformaterad text och markerade versioner. Mer information om semantiska svar finns i [semantisk rangordning och svar](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Parametrar som används i en semantisk fråga

I följande tabell sammanfattas frågeparametrar som används i en semantisk fråga så att du kan se dem holistiskt. En lista över alla parametrar finns i [Sök efter dokument (rest-förhands granskning)](/rest/api/searchservice/preview-api/search-documents)

| Parameter | Typ | Beskrivning |
|-----------|-------|-------------|
| queryType | Sträng | Giltiga värden är enkel, fullständig och semantisk. Värdet "semantisk" krävs för semantiska frågor. |
| queryLanguage | Sträng | Krävs för semantiska frågor. För närvarande är endast "en-US" implementerad. |
| searchFields | Sträng | En kommaavgränsad lista över sökbara fält. Valfritt men rekommenderas. Anger de fält över vilka semantisk rangordning inträffar. </br></br>I motsats till enkla och fullständiga frågetyper, bestämmer ordningen i vilka fält som är prioritet.|
| svar |Sträng | Valfritt fält för att ange om semantiska svar ingår i resultatet. För närvarande implementeras endast "extraktion". Svar kan konfigureras för att returnera högst fem. Det här exemplet "extraktioning|count3 "" visar antalet tre svar. Standard är 1.|

## <a name="query-with-search-explorer"></a>Fråga med Sökutforskaren

Följande fråga riktar sig till det inbyggda exempel indexet för hotell med API-version 2020-06-30-Preview och körs i Sök Utforskaren. `$select`Satsen begränsar resultatet till bara några fält, vilket gör det lättare att skanna i utförlig JSON i Sök Utforskaren.

### <a name="with-querytypesemantic"></a>Med frågetyp = semantisk

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

De första resultaten är följande.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>Med queryType (standard)

För jämförelse kör du samma fråga som ovan, tar bort `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . Observera att det inte finns något `"@search.rerankerScore"` i dessa resultat och att olika hotell visas i de tre översta positionerna.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Nästa steg

Kom ihåg att semantisk rangordning och svar skapas över en första resultat uppsättning. All logik som förbättrar kvaliteten på de första resultaten kommer att överföras till semantisk sökning. I nästa steg ska du gå igenom de funktioner som bidrar till de första resultaten, inklusive analys verktyg som påverkar hur strängarna är tokens, bedömnings profiler som kan justera resultat och standardalgoritmen för relevans.

+ [Analys verktyg för text bearbetning](search-analyzers.md)
+ [Likhet och poängsättning i Kognitiv sökning](index-similarity-and-scoring.md)
+ [Lägga till rankningsprofiler](index-add-scoring-profiles.md)
+ [Översikt över semantisk sökning](semantic-search-overview.md)
+ [Lägg till stavnings kontroll i sökord](speller-how-to-add.md)
