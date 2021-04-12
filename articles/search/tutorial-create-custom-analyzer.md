---
title: 'Självstudie: skapa en anpassad analys'
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar en anpassad analys för att förbättra kvaliteten på Sök resultaten i Azure Kognitiv sökning.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99097664"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Självstudie: skapa en anpassad analys för telefonnummer

[Analys](search-analyzers.md) verktyg är en viktig komponent i alla Sök lösningar. För att förbättra kvaliteten på Sök resultaten är det viktigt att förstå hur analyserare fungerar och påverka dessa resultat.

I vissa fall, t. ex. med ett fritext fält, behöver du bara välja rätt [språk analys](index-add-language-analyzers.md) för att förbättra Sök resultaten. Vissa scenarier, till exempel korrekt sökning av telefonnummer, URL: er eller e-postmeddelanden kan kräva att anpassade analys verktyg används.

Den här självstudien använder [REST-API: er](/rest/api/searchservice/) för Postman och Azure kognitiv sökning för att:

> [!div class="checklist"]
> * Förklara hur analyserare fungerar
> * Definiera en anpassad analys för sökning av telefonnummer
> * Testa hur den anpassade Analyzer-tokenizes text
> * Skapa separata analyser för indexering och sökning för att ytterligare förbättra resultaten

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster och verktyg krävs för den här självstudien.

+ [Skrivbordsappen Postman](https://www.getpostman.com/)
+ [Skapa](search-create-service-portal.md) eller [hitta en befintlig Sök tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>Ladda ned filer

Käll koden för den här självstudien finns i mappen [Anpassade analyser](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) i mappen [Azure-samples/Azure-Search-Postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples) GitHub.

## <a name="1---create-azure-cognitive-search-service"></a>1 – Skapa Azure Kognitiv sökning-tjänsten

För att slutföra den här självstudien behöver du en Azure Kognitiv sökning-tjänst som du kan [skapa i portalen](search-create-service-portal.md). Du kan använda den kostnads fria nivån för att slutföra den här genom gången.

I nästa steg måste du känna till namnet på Sök tjänsten och dess API-nyckel. Om du är osäker på hur du hittar dessa objekt kan du titta på den här [snabb](search-create-service-portal.md#get-a-key-and-url-endpoint)starten.


## <a name="2---set-up-postman"></a>2 – Konfigurera PostMan

Starta sedan Postman och importera samlingen som du laddade ned från [Azure-samples/Azure-Search-Postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

Importera samlingen genom att gå till **filer**  >  **Importera** och sedan välja den samlings fil som du vill importera.

För varje begäran måste du:

1. Ersätt `<YOUR-SEARCH-SERVICE>` med namnet på söktjänsten.

1. Ersätt `<YOUR-ADMIN-API-KEY>` med antingen den primära eller sekundära nyckeln för Sök tjänsten.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="URL och rubrik för Postman-begäran" border="false":::

Om du inte är bekant med Postman kan du läsa mer i [utforska Azure KOGNITIV sökning REST-API: er](search-get-started-rest.md).

## <a name="3---create-an-initial-index"></a>3 – skapa ett ursprungligt index

I det här steget ska vi skapa ett första index, läsa in dokument i indexet och sedan fråga dokumenten och se hur våra inledande sökningar fungerar.

### <a name="create-index"></a>Skapa index

Vi börjar med att skapa ett enkelt index som kallas `tutorial-basic-index` med två fält: `id` och `phone_number` . Vi har inte definierat någon analys än så att `standard.lucene` analys verktyget används som standard.

För att skapa indexet skickar vi följande begäran:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "fields": [
      {
        "name": "id",
        "type": "Edm.String",
        "key": true,
        "searchable": true,
        "filterable": false,
        "facetable": false,
        "sortable": true
      },
      {
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Läsa in data

Nu ska vi läsa in data i indexet. I vissa fall kanske du inte har kontroll över formatet på de telefonnummer som matats in så vi ska testa mot olika typer av format. Vi rekommenderar att en Sök lösning returnerar alla matchande telefonnummer oavsett format.

Data läses in i indexet med följande begäran: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Vi är redo att börja söka med data i indexet.

### <a name="search"></a>Sök

För att göra sökningen intuitiv är det bäst att inte förvänta användarna att formatera frågor på ett särskilt sätt. En användare kan söka efter `(425) 555-0100` i alla format som vi visade ovan och kommer fortfarande att vänta på att resultaten returneras. I det här steget ska vi testa några exempel frågor för att se hur de fungerar.

Vi börjar med att söka `(425) 555-0100` :

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Den här frågan returnerar **tre av fyra förväntade resultat** , men returnerar även **två oväntade resultat**:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Nu ska vi söka efter ett tal utan formatering `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Den här frågan har ännu sämre resultat, och returnerar bara **en av fyra korrekta matchningar**.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Om du hittar dessa resultat förvirrande är du inte ensam. I nästa avsnitt får vi veta varför vi får de här resultaten.

## <a name="4---debug-search-results"></a>4 – Felsök Sök Resultat

För att förstå dessa Sök resultat är det viktigt att du först förstår hur analys programmet fungerar. Därifrån kan vi testa standard analys verktyget med [analysera text-API](/rest/api/searchservice/test-analyzer) och sedan skapa en analys som uppfyller våra behov.

### <a name="how-analyzers-work"></a>Så här fungerar analys verktyg

En [Analyzer](search-analyzers.md) är en komponent i den [fullständiga texts öknings motorn](search-lucene-query-architecture.md) som ansvarar för bearbetning av text i frågesträngar och indexerade dokument. Olika analys verktyg ändrar text på olika sätt beroende på scenariot. I det här scenariot måste vi bygga en analys som är anpassad till telefonnummer.

Analyserare består av tre komponenter:

+ [**Tecken filter**](#CharFilters) som tar bort eller ersätter enskilda tecken från inmatad text.
+ En [**Tokenizer**](#Tokenizers) som delar upp indatamängden i tokens, vilket blir nycklar i Sök indexet.
+ [**Token filter**](#TokenFilters) som ändrar de token som genereras av tokenizer.

I diagrammet nedan kan du se hur dessa tre komponenter fungerar tillsammans för att Tokenize en mening:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Diagram över Analyzer-processen för att Tokenize en mening":::

Dessa tokens lagras sedan i ett inverterat index, vilket möjliggör snabba, full texts ökningar.  Ett inverterat index möjliggör full texts ökning genom att mappa alla unika villkor som extraheras under lexikal analys till de dokument där de förekommer. Du kan se ett exempel i diagrammet nedan:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Exempel på inverterat index":::

All sökning är nedtryckt för att söka efter termer som lagras i det inverterade indexet. När en användare utfärdar en fråga:

1. Frågan parsas och villkoren för frågan analyseras.
1. Det inverterade indexet genomsöks sedan efter dokument med matchande villkor.
1. Slutligen rangordnas de hämtade dokumenten av [algoritmen för likheter](index-ranking-similarity.md).

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Diagram över likhets sätt i Analyzer-processen":::

Om villkoren i frågan inte matchar villkoren i det inverterade indexet returneras inte resultatet. Mer information om hur frågor fungerar finns i den här artikeln om [full texts ökning](search-lucene-query-architecture.md).

> [!Note]
> [Partiella term frågor](search-query-partial-matching.md) är ett viktigt undantag för den här regeln. Dessa frågor (prefixlängd, fråga med jokertecken, regex-fråga) kringgår den lexikala analys processen till skillnad från vanliga term frågor. Delar av termer är bara små i gemener innan de matchas mot termer i indexet. Om en Analyzer inte har kon figurer ATS för att stödja dessa typer av frågor får du ofta oväntade resultat eftersom matchnings villkoren inte finns i indexet.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Test analys med hjälp av API för analys av text

Azure Kognitiv sökning innehåller ett [analys text-API](/rest/api/searchservice/test-analyzer) som gör att du kan testa analyser för att förstå hur de bearbetar text.

API för analys av text anropas med följande begäran:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

API: et returnerar sedan en lista med de token som extraherats från texten. Du ser att standard Lucene Analyzer delar telefonnumret i tre separata tokens:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

I motsats är telefonnumret `4255550100` formaterat utan skiljetecken token till en token.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Tänk på att både sökorden och de indexerade dokumenten analyseras. Vi går tillbaka till Sök resultaten från föregående steg, men vi kan börja se varför de returnerade resultaten.

I den första frågan returnerade felaktiga telefonnummer på grund av deras villkor, `555` matchade en av de sökord som vi sökte i. I den andra frågan returnerade bara det enda antalet eftersom det var den enda posten som hade en term matchning `4255550100` .

## <a name="5---build-a-custom-analyzer"></a>5 – Bygg en anpassad analys

Nu när vi förstår de resultat vi ser kan vi bygga en anpassad analys för att förbättra tokenisering-logiken.

Målet är att tillhandahålla intuitiv sökning mot telefonnummer oavsett vilket format frågan eller den indexerade strängen är i. För att uppnå det här resultatet ska vi ange ett [Character filter](#CharFilters), en [tokenizer](#Tokenizers)och ett [token-filter](#TokenFilters).

<a name="CharFilters"></a>

### <a name="character-filters"></a>Character filter

Tecken filter används för att bearbeta text innan den matas in i tokenizer. Vanliga användnings områden för tecken filter är att filtrera ut HTML-element eller ersätta specialtecken.

För telefonnummer vill vi ta bort blank steg och specialtecken eftersom alla telefonnummer format inte innehåller samma specialtecken och blank steg.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

Filtret ovan tar bort `-` `(` `)` `+` `.` och blank steg från indatamängden.

|Indata|Resultat|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Tokenizers

Tokenizers dela upp text i tokens och ta bort vissa tecken, t. ex. interpunktion, på väg. I många fall är målet för tokenisering att dela upp en mening i enskilda ord.

I det här scenariot använder vi en nyckelords tokenizer, `keyword_v2` eftersom vi vill samla in telefonnumret som en enda term. Observera att detta inte är det enda sättet att lösa det här problemet. Se avsnittet [alternativa metoder](#Alternate) nedan.

Nyckelordet tokenizers skriver alltid samma text som den angav som en enda term.

|Indata|Resultat|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Token-filter

Token filter filtrerar bort eller ändrar de tokens som genereras av tokenizer. En vanlig användning av ett token-filter är att använda gemener i alla tecken med ett gement token-filter. En annan vanlig användning är att filtrera ut stoppord som `the` , `and` eller `is` .

Vi behöver inte använda något av dessa filter för det här scenariot, vi använder ett nGram token-filter för att tillåta delvis sökning av telefonnummer.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

[Filtret för nGram_v2 token](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) delar upp tokens i n-gram av en särskild storlek baserat på `minGram` parametrarna och `maxGram` .

För telefon analys är vi inställt `minGram` på `3` eftersom det är den kortaste under sträng som användarna förväntar sig att söka. `maxGram` är inställt på `20` att se till att alla telefonnummer, även med tillägg, får plats i en enda n-gram.

 Den olycklig sido effekt på n-gram är att vissa falska positiva identifieringar returneras. Vi åtgärdar detta i steg 7 genom att skapa en separat analys för sökningar som inte inkluderar token-filtret i n g.

|Indata|Resultat|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analysen

Med våra tangent filter, tokenizer och token-filter på plats är vi redo att definiera vår analys.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Indata|Resultat|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Observera att alla tokens i utdata nu kan genomsökas. Om frågan innehåller någon av dessa tokens returneras telefonnumret.

När du har definierat den anpassade analysen återskapar du indexet så att den anpassade analysen blir tillgänglig för testning i nästa steg. För enkelhetens skull skapar Postman-samlingen ett nytt index med namnet `tutorial-first-analyzer` analys som vi definierade.

## <a name="6---test-the-custom-analyzer"></a>6 – testa den anpassade analysen

När du har skapat indexet kan du nu testa analys verktyget som vi skapade med följande begäran:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Du kommer sedan att kunna se samlingen av tokens som härrör från telefonnumret:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 – Bygg en anpassad analys för frågor

När du har gjort några exempel frågor mot indexet med Custom Analyzer ser du att återställningen har förbättrats och att alla matchande telefonnummer nu returneras. Token i n-gram-token gör dock att vissa falska positiva identifieringar också returneras. Detta är en gemensam sido effekt av ett token-token i n g.

För att förhindra falska positiva identifieringar skapar vi en separat analys för frågor. Den här analysen är samma som den Analyzer som vi skapade redan men **utan** `custom_ngram_filter` .

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

I index definitionen anger vi både en `indexAnalyzer` och en `searchAnalyzer` .

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Med den här ändringen är allt klart. Återskapa indexet, indexera data och testa frågorna igen för att kontrol lera att sökningen fungerar som förväntat. Om du använder Postman-samlingen, skapas ett tredje index med namnet `tutorial-second-analyzer` .

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Alternativa metoder

Analys verktyget ovan har utformats för att maximera flexibiliteten för sökning. Det innebär dock att kostnaden för att lagra många potentiellt oviktiga villkor i indexet lagras.

Exemplet nedan visar en annan analys som även kan användas för den här uppgiften. 

Analysen fungerar bra förutom indata, t. ex. `14255550100` som gör det svårt att logiskt segmentera telefonnumret. Analysen skulle till exempel inte kunna separera lands koden, `1` från rikt numret `425` . Den här avvikelsen skulle leda till att talet ovan inte returneras om en användare inte har angett en landskod i sökningen.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

Du kan se i exemplet nedan att telefonnumret är uppdelat i segmenten. du förväntar dig normalt en användare att söka efter.

|Indata|Resultat|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

Beroende på dina krav kan det vara en mer effektiv metod för problemet.

## <a name="reset-and-rerun"></a>Återställa och köra igen

I den här självstudien har du skapat tre nya index för enkelhetens skull. Det är dock vanligt att ta bort och återskapa index under de tidiga utvecklings faserna. Du kan ta bort ett index i Azure Portal eller använda följande API-anrop:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Lärdomar

I den här självstudien demonstreras processen för att skapa och testa en anpassad analys. Du har skapat ett index, indexerade data och tillfrågats mot indexet för att se vilka Sök resultat som returnerades. Därifrån har du använt analys text-API: et för att se den lexikala analys processen i praktiken.

Även om den analys som definierats i den här självstudien erbjuder en enkel lösning för sökning mot telefonnummer, kan samma process användas för att bygga en anpassad telefon analys för alla scenarier som du kan ha.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar med din egen prenumeration är det en bra idé att ta bort de resurser som du inte längre behöver i slutet av projektet. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken alla resurser eller resurs grupper i det vänstra navigerings fönstret.

## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med hur du skapar en anpassad analys kan vi ta en titt på alla olika filter, tokenizers och analyser som är tillgängliga för att skapa en omfattande Sök upplevelse.

> [!div class="nextstepaction"]
> [Anpassade analys verktyg i Azure Kognitiv sökning](index-add-custom-analyzers.md)