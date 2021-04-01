---
title: moreLikeThis (för hands version) fråga-funktionen
titleSuffix: Azure Cognitive Search
description: Beskriver funktionen moreLikeThis (för hands version), som är tillgänglig i för hands versioner av Azure Kognitiv sökning REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1c2f8058a85bbc0643ed31a7dc910339d0f6d9dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94697058"
---
# <a name="morelikethis-preview-in-azure-cognitive-search"></a>moreLikeThis (för hands version) i Azure Kognitiv sökning

> [!IMPORTANT] 
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Den [REST API version 2020-06-30 – för hands version](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för Portal eller .NET SDK.

`moreLikeThis=[key]` är en frågeparameter i [search Documents-API: et](/rest/api/searchservice/search-documents) som hittar dokument som liknar det dokument som anges av dokument nyckeln. När en sökbegäran görs med `moreLikeThis` , genereras en fråga med Sök villkor som extraheras från det dokument som beskriver det dokumentet bäst. Den genererade frågan används sedan för att göra sökningen. Som standard beaktas innehållet i alla sökbara fält, minus eventuella begränsade fält som du har angett med `searchFields` parametern. `moreLikeThis`Parametern kan inte användas med Sök parametern, `search=[string]` .

Som standard beaktas innehållet i alla sökbara fält på den översta nivån. Om du vill ange specifika fält i stället kan du använda- `searchFields` parametern. 

Du kan inte använda `MoreLikeThis` i sökbara underordnade fält i en [komplex typ](search-howto-complex-data-types.md).

## <a name="examples"></a>Exempel

I följande exempel används hotell exemplet från [snabb start: skapa ett sökindex i Azure Portal](search-get-started-portal.md).

### <a name="simple-query"></a>Exempelfråga

Följande fråga hittar dokument vars beskrivnings fält liknar det fält i käll dokumentet som anges av `moreLikeThis` parametern:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=29&searchFields=Description&api-version=2020-06-30-Preview
```

I det här exemplet söker förfrågan efter hotell som liknar den som har `HotelId` 29.
I stället för att använda HTTP GET kan du också anropa `MoreLikeThis` med http post:

```
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
    {
      "moreLikeThis": "29",
      "searchFields": "Description"
    }
```

### <a name="apply-filters"></a>Använda filter

`MoreLikeThis` kan kombineras med andra vanliga frågeparametrar som `$filter` . Till exempel kan frågan begränsas till endast hotell vars kategori är "budget" och där omdömet är högre än 3,5:

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&api-version=2020-06-30-Preview
```

### <a name="select-fields-and-limit-results"></a>Välj fält och begränsa resultaten

`$top`Väljaren kan användas för att begränsa hur många resultat som ska returneras i en `MoreLikeThis` fråga. Du kan också välja fält med `$select` . Här är de tre översta hotellen markerade tillsammans med ID, namn och klassificering: 

```
GET /indexes/hotels-sample-index/docs?moreLikeThis=20&searchFields=Description&$filter=(Category eq 'Budget' and Rating gt 3.5)&$top=3&$select=HotelId,HotelName,Rating&api-version=2020-06-30-Preview
```

## <a name="next-steps"></a>Nästa steg

Du kan använda valfritt verktyg för webbtest för att experimentera med den här funktionen.  Vi rekommenderar att du använder Postman för den här övningen.

> [!div class="nextstepaction"]
> [Utforska Azure Kognitiv sökning REST-API: er](search-get-started-rest.md)