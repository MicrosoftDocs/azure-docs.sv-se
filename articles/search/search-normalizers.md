---
title: Text normalisering för filter, Faces, sort
titleSuffix: Azure Cognitive Search
description: Ange normaliseringar till textfält i ett index för att anpassa det strikta nyckelords matchnings beteendet för filtrering, aspektning och sortering.
author: IshanSrivastava
manager: jlembicz
ms.author: ishansri
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/23/2021
ms.openlocfilehash: 3e7a33d9213d7af44d2cfc50baa847534618f7e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609987"
---
# <a name="text-normalization-for-case-insensitive-filtering-faceting-and-sorting"></a>Text normalisering för Skift läges okänslig filtrering, aspektning och sortering

 > [!IMPORTANT]
 > Normalisering finns i en offentlig för hands version, som är tillgänglig via **REST API 2020-06-30 – för hands version**. För hands versions funktionerna erbjuds i befintligt skick under kompletterande användnings villkor.

Att söka efter och hämta dokument från ett Azure Kognitiv sökning-index måste matcha frågan mot innehållet i dokumentet. Innehållet kan analyseras för att skapa token för matchning som är fallet när `search` parametern används, eller kan användas som-är för strikt nyckelords matchning som det visas i `$filter` , `facets` och `$orderby` . Denna allt-eller-Nothing-metod täcker de flesta scenarier, men är kort där enkel för bearbetning som t. ex. Skift läge, borttagning av accenttecken, asciifolding och så vidare krävs utan att gå igenom hela analys kedjan.

Överväg följande exempel:

+ `$filter=City eq 'Las Vegas'` kommer bara att returnera dokument som innehåller den exakta texten "Las Vegas" och utesluta dokument med "LAS VEGAS" och "Las Vegas", vilket är olämpligt när användnings fallet kräver alla dokument oavsett Skift läge.

+ `search=*&facet=City,count:5` Returnerar "Las Vegas", "LAS VEGAS" och "Las Vegas" som distinkta värden trots att de är samma ort.

+ `search=usa&$orderby=City` Returnerar städerna i lexicographical-ordningen: "Las Vegas", "Seattle", "Las Vegas", även om avsikten är att beställa samma städer tillsammans oberoende av fallet. 

## <a name="normalizers"></a>Normalisering

En *normalisering* är en komponent i sökmotorn som ansvarar för för bearbetning av text för nyckelords matchning. Normaliseringar liknar analys verktyg, förutom att de inte Tokenize frågan. Några av de omvandlingar som kan uppnås med hjälp av normalisering är:

+ Konvertera till gemener eller versaler.
+ Normalisera accenttecken och dia kritiska tecken som ö eller ê till ASCII-motsvarigheten "o" och "e".
+ Mappa tecken som `-` och blank steg till ett användardefinierat tecken.

Normaliseringar kan anges i textfält i indexet och tillämpas både vid indexering och frågekörningen.

## <a name="predefined-and-custom-normalizers"></a>Fördefinierade och anpassade normaliseringar 

Azure Kognitiv sökning stöder fördefinierade normaliseringar för vanliga användnings fall tillsammans med möjligheten att anpassa efter behov.

| Kategori | Beskrivning |
|----------|-------------|
| [Fördefinierade normaliseringar](#predefined-normalizers) | Medföljer-Box och kan användas utan någon konfiguration. |
|[Anpassade normaliserings](#add-custom-normalizers) | För avancerade scenarier. Kräver användardefinierad konfiguration av en kombination av befintliga element, bestående av char-och token-filter. <sup>1</sup>|

<sup>(1)</sup> anpassade normaliseringar anger inte tokenizers eftersom normaliserare alltid skapar en enda token.

## <a name="how-to-specify-normalizers"></a>Så här anger du normalisering

Normaliseringar kan anges per fält i textfält ( `Edm.String` och `Collection(Edm.String)` ) som har minst en av `filterable` `sortable` egenskaperna,, eller har `facetable` angetts till sant. Att ange en normalisering är valfritt och är `null` som standard. Vi rekommenderar att du utvärderar fördefinierade normaliserings metoder innan du konfigurerar en anpassad för enkel användning. Försök med en annan normalisering om resultatet inte förväntas.

Normaliseringar kan bara anges när ett nytt fält läggs till i indexet. Vi rekommenderar att du bedömer normaliseringen och tilldelar normalisering i de första utvecklings stegen när du släpper och återskapar index är rutinmässigt. Det går inte att ange normalisering för ett fält som redan har skapats.

1. När du skapar en fält definition i [indexet](/rest/api/searchservice/create-index)ställer du in  **normaliserings** egenskapen på något av följande: en [fördefinierad normalisering](#predefined-normalizers) som `lowercase` eller en anpassad normalisering (definieras i samma index schema).  
 
   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "filterable": true,
      "analyzer": "en.microsoft",
      "normalizer": "lowercase"
      ...
    },
   ```

2. Anpassade normaliseringar måste definieras i avsnittet **[normaliserings]** i indexet först och sedan tilldelas fält definitionen som visas i föregående steg. Mer information finns i [skapa index](/rest/api/searchservice/create-index) och även [lägga till anpassade normaliserings](#add-custom-normalizers).


   ```json
   "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": null,
      "normalizer": "my_custom_normalizer"
    },
   ```

 
> [!NOTE]
> Om du vill ändra normalisering för ett befintligt fält måste du återskapa indexet helt (du kan inte återskapa enskilda fält).

En bra lösning för produktions index, där återuppbyggnad av index är kostsamt, är att skapa ett nytt fält som är identiskt med det gamla, men med den nya normaliseringen och använda det i stället för det gamla. Använd [Uppdatera index](/rest/api/searchservice/update-index) för att lägga till det nya fältet och [mergeOrUpload](/rest/api/searchservice/addupdate-or-delete-documents) för att fylla det. Som en del av den planerade indexerings servicen kan du senare rensa indexet för att ta bort föråldrade fält.

## <a name="add-custom-normalizers"></a>Lägg till anpassade normalisering

Anpassade normaliserings definitioner definieras i index schemat och kan anges med fält egenskapen. Definitionen av anpassad normalisering innehåller ett namn, en typ, ett eller flera tecken filter och token filter. Char filter och token filter är Bygg blocken för en anpassad normalisering och ansvarar för bearbetningen av texten. Dessa filter används från vänster till höger.

 `token_filter_name_1`Är namnet på token-filtret och `char_filter_name_1` `char_filter_name_2` är namnen på char-filter (se de [token-filter](#supported-token-filters) och Char filter-tabeller som stöds nedan för giltiga värden).

Normaliserings definitionen är en del av det större indexet. Se [skapa index-API](/rest/api/searchservice/create-index) för information om resten av indexet.

```
"normalizers":(optional)[
   {
      "name":"name of normalizer",
      "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenFilters":[
         "token_filter_name_1
      ]
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name_1",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name_1",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

Du kan lägga till anpassade normaliseringar när du skapar index eller senare genom att uppdatera en befintlig. Om du lägger till en anpassad normalisering i ett befintligt index måste **allowIndexDowntime** -flaggan anges i [Update index](/rest/api/searchservice/update-index) och det gör att indexet inte är tillgängligt under några sekunder.

## <a name="normalizers-reference"></a>Normaliserings referens

### <a name="predefined-normalizers"></a>Fördefinierade normaliseringar
|**Namn**|**Beskrivning och alternativ**|  
|-|-|  
|standard| Gemener i texten följt av asciifolding.|  
|versal| Transformerar tecken till gemener.|
|vers| Transformerar tecken till versaler.|
|asciifolding| Transformerar tecken som inte finns i det grundläggande latinska Unicode-blocket till deras ASCII-motsvarighet, om ett sådant finns. Till exempel, ändra à till a.|
|elision| Tar bort elision från början av tokens.|

### <a name="supported-char-filters"></a>Tecken filter som stöds
Mer information om char-filter finns i referens för [char filter](index-add-custom-analyzers.md#CharFilter).
+ [mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)  
+ [pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)

### <a name="supported-token-filters"></a>Token-filter som stöds
Listan nedan visar de token-filter som stöds för normalisering och är en delmängd av de övergripande token-filter som ingår i lexikalisk analys. Mer information om filtren finns i [referens för token filter](index-add-custom-analyzers.md#TokenFilters).

+ [arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)
+ [asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)
+ [cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)  
+ [elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)  
+ [german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)
+ [hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)  
+ [indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)
+ [persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)
+ [scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)  
+ [scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)
+ [sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)  
+ [versal](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)
+ [vers](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)


## <a name="custom-normalizer-example"></a>Anpassat normaliserings exempel

Exemplet nedan illustrerar en anpassad normaliserings definition med motsvarande tecken filter och token filter. Anpassade alternativ för char-filter och token-filter anges separat som namngivna konstruktioner och refereras sedan till i normaliserings definitionen som illustreras nedan.

* En anpassad normalisering "my_custom_normalizer" definieras i `normalizers` avsnittet i index definitionen.
* Normaliseringen består av två char-filter och tre token-filter: elision, gemener och anpassade asciifolding filter my_asciifolding.
* Det första char-filtret "map_dash" ersätter alla streck med under streck medan det andra "remove_whitespace" tar bort alla blank steg.

```json
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false,
        },
        {
           "name":"city",
           "type":"Edm.String",
           "filterable": true,
           "facetable": true,
           "normalizer": "my_custom_normalizer"
        }
     ],
     "normalizers":[
        {
           "name":"my_custom_normalizer",
           "@odata.type":"#Microsoft.Azure.Search.CustomNormalizer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],,
           "tokenFilters":[              
              "my_asciifolding",
              "elision",
              "lowercase",
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
```

## <a name="see-also"></a>Se även
+ [Analys verktyg för språk-och text bearbetning](search-analyzers.md)

+ [REST API för dokumentsökning](/rest/api/searchservice/search-documents) 
