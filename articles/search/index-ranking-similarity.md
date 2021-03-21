---
title: Konfigurera likhets algoritmen
titleSuffix: Azure Cognitive Search
description: Lär dig hur du aktiverar BM25 på äldre Sök tjänster och hur BM25-parametrar kan ändras för att bättre anpassa innehållet i dina index.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232842"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Konfigurera algoritmen för rangordning av likhet i Azure Kognitiv sökning

Azure Kognitiv sökning stöder två likartade algoritmer för rangordning:

+ En *klassisk likhets* algoritm som används av alla Sök tjänster fram till den 15 juli 2020.
+ En implementering av *OKAPI BM25* -algoritmen som används i alla Sök tjänster som skapats efter den 15 juli.

BM25 rangordning är det nya standardvärdet eftersom det är bra att skapa Sök rankninger som bättre överensstämmer med användarnas förväntningar. Den innehåller [parametrar](#set-bm25-parameters) för att justera resultat baserat på faktorer som dokument storlek. 

För nya tjänster som skapats efter den 15 juli 2020 används BM25 automatiskt och är den enda likhets algoritmen. Om du försöker ange likhet med ClassicSimilarity på en ny tjänst returneras ett HTTP 400-fel på grund av att algoritmen inte stöds av tjänsten.

För äldre tjänster som skapats före den 15 juli 2020 är den klassiska likheten fortfarande standardalgoritmen. Äldre tjänster kan uppgraderas till BM25 per index enligt beskrivningen nedan. Om du växlar från klassisk till BM25 kan du vänta på att se vissa skillnader hur Sök Resultat beställs.

> [!NOTE]
> Semantisk rangordning, som för närvarande finns som för hands version för standard tjänster i valda regioner, är ett ytterligare steg framåt i att producera mer relevanta resultat. Till skillnad från andra algoritmer är det en tilläggs funktion som itererar över en befintlig resultat uppsättning. Mer information finns i [Översikt över semantisk sökning](semantic-search-overview.md) och [semantisk rangordning](semantic-ranking.md).

## <a name="enable-bm25-scoring-on-older-services"></a>Aktivera BM25-Poängsättning på äldre tjänster

Om du kör en Sök tjänst som skapades före den 15 juli 2020 kan du aktivera BM25 genom att ange en likhets egenskap för nya index. Egenskapen visas bara för nya index, så om du vill BM25 på ett befintligt index måste du släppa och [återskapa indexet](search-howto-reindex.md) med en ny likhets egenskap som är inställd på "Microsoft. Azure. search. BM25Similarity".

När det finns ett index med en likhets egenskap kan du växla mellan BM25Similarity eller ClassicSimilarity. 

Följande länkar beskriver egenskapen likhet i Azure SDK: er. 

| Klientbibliotek | Egenskap för likhet |
|----------------|---------------------|
| .NET  | [SearchIndex. likhet](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex. likhet](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [Egenskapen likhet i SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST-exempel

Du kan också använda [REST API](/rest/api/searchservice/create-index), som i följande exempel visas:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

## <a name="set-bm25-parameters"></a>Ange BM25-parametrar

BM25-likhet lägger till två användarvänliga parametrar för att kontrol lera de beräknade resultatet. Du kan ställa in BM25-parametrar när du skapar index eller som en index uppdatering om BM25-algoritmen angavs när index skapas.

| Egenskap | Typ | Beskrivning |
|----------|------|-------------|
| K1 | antal | Styr skalnings funktionen mellan term frekvensen för varje matchande villkor till den slutliga relevans poängen för ett dokument-frågeuttryck. Värdena är vanligt vis 0,0 till 3,0, med 1,2 som standard. </br></br>Värdet 0,0 representerar en "binär modell" där bidraget för en enskild matchande term är detsamma för alla matchande dokument, oavsett hur många gånger som termen visas i texten, medan ett större K1-värde gör att poängen fortsätter att öka eftersom fler förekomster av samma term finns i dokumentet. </br></br>Att använda ett högre K1-värde kan vara viktigt i de fall där det förväntar sig att flera villkor ska ingå i en Sök fråga. I dessa fall kanske vi vill prioritera dokument som matchar många av de olika sökorden som genomsöks över dokument som bara matchar en enda, flera gånger. Exempel: när du frågar efter indexet för dokument som innehåller villkoren "Apollo spaceflight", kanske vi vill minska poängen för en artikel om grekiska Mythology som innehåller termen "Apollo" några dussin gånger, utan omnämnande av "spaceflight", jämfört med en annan artikel som uttryckligen nämner både "Apollo" och "spaceflight" en fåtal av gånger. |
| b | antal | Styr hur längden på ett dokument påverkar relevans poängen. Värdena är mellan 0 och 1, med 0,75 som standard. </br></br>Värdet 0,0 innebär att dokumentets längd inte påverkar poängen, medan värdet 1,0 innebär att term frekvensen på relevans poängen normaliseras av dokumentets längd. </br></br>Att normalisera term frekvensen med dokumentets längd är användbart i de fall där vi vill bestraffa längre dokument. I vissa fall är längre dokument (till exempel en fullständig sådan) mer sannolika att innehålla många irrelevanta villkor, jämfört med mycket kortare dokument. |

### <a name="setting-k1-and-b-parameters"></a>Ställer in K1-och b-parametrar

Om du vill ange eller ändra b-eller K1-värden lägger du till dem i BM25 likhets objekt. Om du anger eller ändrar dessa värden i ett befintligt index tas indexet offline under minst några sekunder, vilket gör att aktiva indexerings-och fråge begär Anden Miss söker. Därför bör du ange parametern "allowIndexDowntime = true" för uppdaterings förfrågan:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>Se även  

+ [REST API referens](/rest/api/searchservice/)
+ [Lägg till bedömnings profiler i ditt index](index-add-scoring-profiles.md)
+ [Skapa index-API](/rest/api/searchservice/create-index)
+ [Azure Kognitiv sökning .NET SDK](/dotnet/api/overview/azure/search)