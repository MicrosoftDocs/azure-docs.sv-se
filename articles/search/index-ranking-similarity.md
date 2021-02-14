---
title: Algoritm för rangordning av likhet
titleSuffix: Azure Cognitive Search
description: Så här anger du algoritmen för likhet för att testa en ny likhets algoritm för rangordning
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: e2caa09d41abb1842100ed8259e82ec411390ccb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520637"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Ranknings algoritm i Azure Kognitiv sökning

> [!IMPORTANT]
> Från och med den 15 juli 2020 kommer nya Sök tjänster att använda funktionen BM25 rangordning automatiskt, som har visat i de flesta fall att tillhandahålla Sök rankninger som passar bättre med användar förväntningar än den aktuella standard rangordningen. Efter överlägsen rangordning möjliggör BM25 också konfigurations alternativ för justering av resultat baserat på faktorer som dokument storlek.  
>
> Med den här ändringen kommer du troligen att se smärre ändringar i ordningen för dina Sök resultat. För de som vill testa effekten av den här ändringen är BM25-algoritmen tillgänglig i API-versionen 2019-05-06-Preview och i 2020-06-30.  

Den här artikeln beskriver hur du kan använda den nya algoritmen för BM25 i befintliga Sök tjänster för nya index som skapats och efterfrågats med hjälp av för hands versionen av API.

Azure Kognitiv sökning håller på att anta den officiella Lucene-implementeringen av Okapi BM25-algoritmen, *BM25Similarity*, som ersätter den tidigare använda *ClassicSimilarity* -implementeringen. Precis som den äldre ClassicSimilarity-algoritmen är BM25Similarity en TF-IDF-liknande hämtnings funktion som använder term frekvensen (TF) och inverterad dokument frekvens (IDF) som variabler för att beräkna resultat för varje dokument-frågeuttryck som sedan används för rangordning. 

Samtidigt som den äldre klassiska likhets algoritmen används, tar BM25 sin rot i Probabilistic-informations hämtningen för att kunna förbättra den. BM25 erbjuder också avancerade anpassnings alternativ, till exempel att låta användaren bestämma hur relevans poängen skalas med villkors frekvensen för matchade villkor.

## <a name="how-to-test-bm25-today"></a>Testa BM25 idag

När du skapar ett nytt index kan du ange en **likhets** egenskap för att ange algoritmen. Du kan använda `api-version=2019-05-06-Preview` , som du ser nedan, eller `api-version=2020-06-30` .

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
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

Egenskapen **likhet** är användbar under den här tillfälliga perioden när båda algoritmerna är tillgängliga, endast för befintliga tjänster. 

| Egenskap | Beskrivning |
|----------|-------------|
| likhets | Valfritt. Giltiga värden är *"#Microsoft. Azure. search. ClassicSimilarity"* eller *"#Microsoft. Azure. search. BM25Similarity"*. <br/> Kräver `api-version=2019-05-06-Preview` eller senare på en Sök tjänst som skapats före den 15 juli 2020. |

För nya tjänster som skapats efter den 15 juli 2020 används BM25 automatiskt och är den enda likhets algoritmen. Om du försöker ange **likhet** med `ClassicSimilarity` på en ny tjänst returneras ett 400-fel eftersom algoritmen inte stöds i en ny tjänst.

För befintliga tjänster som skapats före den 15 juli 2020 är den klassiska likheten fortfarande standardalgoritmen. Om egenskapen **likhets** värde utelämnas eller anges till null används den klassiska algoritmen av indexet. Om du vill använda den nya algoritmen måste du ange **likhets sätt** enligt beskrivningen ovan.

## <a name="bm25-similarity-parameters"></a>Parametrar för BM25-likhet

BM25-likhet lägger till två användarvänliga parametrar för att kontrol lera de beräknade resultatet.

### <a name="k1"></a>K1

Parametern *K1* styr skalnings funktionen mellan term frekvensen för varje matchande villkor med den slutliga relevans poängen för ett dokument-frågeuttryck.

Värdet noll representerar en "binär modell" där bidraget för en enskild matchande term är detsamma för alla matchande dokument, oavsett hur många gånger som termen visas i texten, medan ett större K1-värde gör att poängen fortsätter att öka eftersom fler förekomster av samma term finns i dokumentet. Som standard använder Azure Kognitiv sökning värdet 1,2 för parametern K1. Att använda ett högre K1-värde kan vara viktigt i de fall där det förväntar sig att flera villkor ska ingå i en Sök fråga. I dessa fall kanske vi vill prioritera dokument som matchar många av de olika sökorden som genomsöks över dokument som bara matchar en enda, flera gånger. Exempel: när du frågar efter indexet för dokument som innehåller villkoren "Apollo spaceflight", kanske vi vill minska poängen för en artikel om grekiska Mythology som innehåller termen "Apollo" några dussin gånger, utan omnämnande av "spaceflight", jämfört med en annan artikel som uttryckligen nämner både "Apollo" och "spaceflight" en fåtal av gånger. 
 
### <a name="b"></a>b

Parametern *b* styr hur lång tid ett dokument påverkar relevans-poängen.

Värdet 0,0 innebär att dokumentets längd inte påverkar poängen, medan värdet 1,0 innebär att term frekvensen på relevans poängen normaliseras av dokumentets längd. Standardvärdet som används i Azure-Kognitiv sökning för b-parametern är 0,75. Att normalisera term frekvensen med dokumentets längd är användbart i de fall där vi vill bestraffa längre dokument. I vissa fall är längre dokument (till exempel en fullständig sådan) mer sannolika att innehålla många irrelevanta villkor, jämfört med mycket kortare dokument.

### <a name="setting-k1-and-b-parameters"></a>Ställer in K1-och b-parametrar

Om du vill anpassa b-eller K1-värden lägger du bara till dem som egenskaper till likhets objektet när du använder BM25:

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

Det går bara att ställa in likhets algoritmen vid skapande av index. Det innebär att den likhets algoritm som används inte kan ändras för befintliga index. Parametrarna *"b"* och *"K1"* kan ändras när du uppdaterar en befintlig index definition som använder BM25. Om du ändrar dessa värden i ett befintligt index tas indexet offline under minst några sekunder, vilket gör att din indexerings-och fråge förfrågningar Miss söker. Därför måste du ange parametern "allowIndexDowntime = true" i frågesträngen för din uppdaterings förfrågan:

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>Se även  

+ [REST API referens](/rest/api/searchservice/)
+ [Lägg till bedömnings profiler i ditt index](index-add-scoring-profiles.md)
+ [Skapa index-API](/rest/api/searchservice/create-index)
+ [Azure Kognitiv sökning .NET SDK](/dotnet/api/overview/azure/search)