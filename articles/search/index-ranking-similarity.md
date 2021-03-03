---
title: Konfigurera algoritm för rangordning av likhet
titleSuffix: Azure Cognitive Search
description: Så här anger du algoritmen för likhet för att testa en ny likhets algoritm för rangordning
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 9f806b512ae8e118fca8f32115c8be3b493fd681
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677789"
---
# <a name="configure-ranking-algorithms-in-azure-cognitive-search"></a>Konfigurera rankade algoritmer i Azure Kognitiv sökning

Azure Kognitiv sökning stöder två likartade algoritmer för rangordning:

+ En *klassisk likhets* algoritm som används av alla Sök tjänster fram till den 15 juli 2020.
+ En implementering av *OKAPI BM25* -algoritmen som används i alla Sök tjänster som skapats efter den 15 juli.

BM25 rangordning är det nya standardvärdet eftersom det är bra att skapa Sök rankninger som bättre överensstämmer med användarnas förväntningar. Du kan också använda konfigurations alternativ för att justera resultat baserat på faktorer som dokument storlek. För nya tjänster som skapats efter den 15 juli 2020 används BM25 automatiskt och är den enda likhets algoritmen. Om du försöker ange likhet med ClassicSimilarity på en ny tjänst returneras ett HTTP 400-fel på grund av att algoritmen inte stöds av tjänsten.

För äldre tjänster som skapats före den 15 juli 2020 är den klassiska likheten fortfarande standardalgoritmen. Äldre tjänster kan ange egenskaper för ett sökindex för att anropa BM25, enligt beskrivningen nedan. Om du växlar från klassisk till BM25 kan du vänta på att se vissa skillnader hur Sök Resultat beställs.

> [!NOTE]
> Semantisk sökning är en ytterligare semantisk omrangordnings algoritm som begränsar avståndet mellan förväntningar och resultat ännu mer. Till skillnad från andra algoritmer är det en tilläggs funktion som itererar över en befintlig resultat uppsättning. Om du vill använda den semantiska sökalgoritmen för förhands granskning måste du skapa en ny tjänst och du måste ange en [typ av semantisk fråga](semantic-how-to-query-request.md). Mer information finns i [Översikt över semantisk sökning](semantic-search-overview.md).

## <a name="create-a-search-index-for-bm25-scoring"></a>Skapa ett Sök index för BM25-Poängsättning

Om du kör en Sök tjänst som skapades före den 15 juli 2020 kan du ange likhets egenskapen till antingen BM25Similarity eller ClassicSimilarity i index definitionen. Om egenskapen likhets värde utelämnas eller anges till null används den klassiska algoritmen av indexet.

Det går bara att ställa in likhets algoritmen vid skapande av index. Men när ett index har skapats med BM25 kan du uppdatera det befintliga indexet för att ange eller ändra BM25-parametrarna.

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

## <a name="bm25-similarity-parameters"></a>Parametrar för BM25-likhet

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