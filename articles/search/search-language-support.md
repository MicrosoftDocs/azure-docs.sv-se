---
title: Indexering med flera språk för icke-engelska Sök frågor
titleSuffix: Azure Cognitive Search
description: Azure Kognitiv sökning stöder 56-språk, och använder språk analys verktyg från Microsoft för att bearbeta teknik från Lucene och naturliga språk.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 588de9c9cae114b5f5396db17f7ecb19bcde25c6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93423087"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Så här skapar du ett index för flera språk i Azure Kognitiv sökning

Index kan innehålla fält som innehåller innehåll från flera språk, till exempel när enskilda fält skapas för språkspecifika strängar. För bästa resultat vid indexering och frågor, tilldelar du en språk analys som innehåller lämpliga språk regler. 

Azure Kognitiv sökning erbjuder ett stort urval av språk analys verktyg från både Lucene och Microsoft som kan tilldelas enskilda fält med hjälp av egenskapen Analyzer. Du kan också ange en språk analys i portalen, enligt beskrivningen i den här artikeln.

## <a name="add-analyzers-to-fields"></a>Lägg till analyser i fält

En språk analys anges när ett fält skapas. Att lägga till en analys i en befintlig fält definition kräver överskrivning (och laddar om) indexet, eller att skapa ett nytt fält som är identiskt med originalet, men med en Analyzer-tilldelning. Du kan sedan ta bort fältet som används när du vill.

1. Logga in på [Azure Portal](https://portal.azure.com) och hitta Sök tjänsten.
1. Klicka på **Lägg till index** i kommando fältet längst upp på instrument panelen för att starta ett nytt index, eller öppna ett befintligt index om du vill ställa in en analys för nya fält som du lägger till i ett befintligt index.
1. Starta en fält definition genom att ange ett namn.
1. Välj data typen EDM. String. Endast sträng fält är full text sökbar.
1. Ange det **sökbara** attributet som aktiverar Analyzer-egenskapen. Ett fält måste vara text-baserat för att du ska kunna använda en språk analys.
1. Välj en av de tillgängliga analys verktyg. 

![Tilldela språk analyser under fält definition](media/search-language-support/select-analyzer.png "Tilldela språk analyser under fält definition")

Som standard använder alla sökbara fält [standard Lucene Analyzer](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) , vilket är språk-oberoende. Om du vill visa en fullständig lista över analyser som stöds, se [lägga till språk analys verktyg i ett Azure kognitiv sökning-index](index-add-language-analyzers.md).

I portalen är analys verktyg avsedda att användas som de är. Om du behöver anpassning eller en speciell konfiguration av filter och tokenizers bör du [skapa en anpassad analys](index-add-custom-analyzers.md) i kod. Portalen stöder inte val eller konfiguration av anpassade analyser.

## <a name="query-language-specific-fields"></a>Fråga språk – specifika fält

När du har valt språk analys för ett fält används det med varje indexerings-och Sök förfrågan för det fältet. När en fråga utfärdas mot flera fält med olika analys verktyg, kommer frågan att bearbetas oberoende av de tilldelade analys verktyg för varje fält.

Om språket för agenten som utfärdar en fråga är känt kan en sökbegäran begränsas till ett visst fält med hjälp av **searchFields** -Frågeparametern. Följande fråga kommer endast att utfärdas mot beskrivningen i polska:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2020-06-30`

Du kan fråga ditt index från portalen med hjälp av [**Sök Utforskaren**](search-explorer.md) för att klistra in en fråga som liknar den som visas ovan.

## <a name="boost-language-specific-fields"></a>Förstärka språkspecifika fält

Ibland är språket för den agent som utfärdar en fråga inte känt, och i så fall kan frågan utfärdas mot alla fält samtidigt. Om det behövs kan du definiera resultat för ett visst språk med hjälp av [bedömnings profiler](index-add-scoring-profiles.md). I exemplet nedan kommer matchningar som hittas i beskrivningen på engelska att bli högre jämfört med matchningar i polska och franska:

```http
    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]
```

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2020-06-30`

## <a name="next-steps"></a>Nästa steg

Om du är en .NET-utvecklare kan du tänka på att du kan konfigurera språk analyser med [Azure kognitiv sökning .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) och egenskapen [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) .