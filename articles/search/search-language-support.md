---
title: Indexering med flera språk för icke-engelska Sök frågor
titleSuffix: Azure Cognitive Search
description: Skapa ett index som stöder innehåll på flera språk och skapa sedan frågor som är begränsade till det innehållet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801612"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Så här skapar du ett index för flera språk i Azure Kognitiv sökning

Ett nyckel krav i ett program för flerspråkig sökning är möjligheten att söka över och hämta resultat i användarens eget språk. I Azure Kognitiv sökning är det ett sätt att uppfylla språk kraven för en flerspråkig app att skapa dedikerade fält för att lagra strängar på ett specifikt språk och sedan begränsa full texts ökningen till bara dessa fält vid tidpunkten för frågan.

+ Ange en språk analys som anropar språk reglerna för mål språket i fält definitioner. Om du vill visa en fullständig lista över analyser som stöds, se [lägga till språk analys](index-add-language-analyzers.md)verktyg.

+ Ange parametrar som omfånget fullständig texts ökning på vissa fält i frågan och ta sedan bort resultaten från fält som inte innehåller innehåll som är kompatibelt med den Sök funktion som du vill leverera.

Det här är en lyckad genom gång av den här metoden för att skydda fält innehåll. Azure Kognitiv sökning översätter inte strängar eller utför språk identifiering som en del av frågekörningen. Det är upp till dig att se till att fälten innehåller de strängar som du förväntar dig.

## <a name="define-fields-for-content-in-different-languages"></a>Definiera fält för innehåll på olika språk

I Azure Kognitiv sökning riktar sig frågor mot ett enda index. Utvecklare som vill tillhandahålla språkspecifika strängar i en enda Sök funktion definierar vanligt vis dedikerade fält för att lagra värdena: ett fält för engelska strängar, ett för franska och så vidare.

Egenskapen "Analyzer" i en fält definition används för att ange [språk analys](index-add-language-analyzers.md). Den kommer att användas för både indexerings-och frågekörning.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Bygga och läsa in ett index

Ett mellanliggande (och eventuellt uppenbart) steg är att du måste [bygga och fylla i indexet](search-get-started-dotnet.md) innan du skapar en fråga. Vi nämner detta steg för att slutföra. Ett sätt att avgöra indexets tillgänglighet är genom att kontrol lera listan index i [portalen](https://portal.azure.com).

> [!TIP]
> Språk identifiering och text översättning stöds vid data inmatning via AI- [anrikning](cognitive-search-concept-intro.md) och [färdighetsuppsättningar](cognitive-search-working-with-skillsets.md). Om du har en Azure-datakälla med blandat språk innehåll kan du prova funktionerna för språk identifiering och översättning med hjälp av [guiden Importera data](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Begränsa frågan och trimma resultatet

Parametrar i frågan används för att begränsa sökningen till vissa fält och sedan trimma resultaten för fält som inte är användbara för ditt scenario. 

| Parametrar | Syfte |
|-----------|--------------|
| **searchFields** | Begränsar fullständig texts ökning till listan över namngivna fält. |
| **$select** | Trimmar svaret så att det endast innehåller de fält som du anger. Som standard returneras alla fält som kan hämtas. Med parametern **$Select** kan du välja vilka som ska returneras. |

Baserat på ett mål för att begränsa sökningen till fält som innehåller franska strängar, använder du **searchFields** för att rikta frågan på fält som innehåller strängar på det språket.

Det är inte nödvändigt att ange Analyzer för en förfrågan. En språk analys på fält definitionen kommer alltid att användas vid bearbetning av frågor. För frågor som anger flera fält som anropar olika språk analyser, bearbetas villkoren eller fraserna oberoende av de tilldelade analys aktiviteterna för varje fält.

Som standard returnerar en sökning alla fält som är markerade som hämtnings bara. Därför kanske du vill utesluta fält som inte överensstämmer med den språkspecifika Sök upplevelse som du vill tillhandahålla. Om du har begränsat sökningen till ett fält med franska strängar vill du förmodligen utesluta fält med engelska strängar från dina resultat. Med hjälp av parametern **$Select** fråga kan du styra vilka fält som returneras till det anropande programmet.

#### <a name="example-in-rest"></a>Exempel i REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Exempel i C #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Förstärka språkspecifika fält

Ibland är språket för den agent som utfärdar en fråga inte känt, och i så fall kan frågan utfärdas mot alla fält samtidigt. IA för resultat på ett visst språk kan definieras med hjälp av [bedömnings profiler](index-add-scoring-profiles.md). I exemplet nedan kommer matchningar som hittas i beskrivningen på engelska att bli högre jämfört med matchningar på andra språk:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Sedan inkluderar du bedömnings profilen i Sök förfrågan:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Nästa steg

+ [Språkanalysverktyg](index-add-language-analyzers.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API för dokumentsökning](/rest/api/searchservice/search-documents)
+ [Översikt över AI-anrikning](cognitive-search-concept-intro.md)
+ [Översikt över färdighetsuppsättningar](cognitive-search-working-with-skillsets.md)