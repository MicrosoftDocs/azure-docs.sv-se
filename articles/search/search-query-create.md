---
title: Skapa en grundläggande fråga
titleSuffix: Azure Cognitive Search
description: 'Lär dig hur du skapar en förfrågan i Kognitiv sökning, vilka verktyg och API: er som ska användas för testning och kod samt hur fråge beslut börjar med index design.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: ace887396bacf264f0ffbd186ef1349e96496786
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371223"
---
# <a name="create-a-basic-query-in-azure-cognitive-search"></a>Skapa en grundläggande fråga i Azure Kognitiv sökning

I den här artikeln förklaras hur du steg för steg visar frågor. Exempel finns i REST så att du kan kopiera strängar till **Sök Utforskaren** i portalen eller utveckla frågor interaktivt med Postman eller Visual Studio Code. Du kan använda valfri nivå eller version av Kognitiv sökning i exemplen i den här artikeln.

## <a name="choose-a-tool-or-api"></a>Välj ett verktyg eller en API

Välj bland följande verktyg och API: er för att skapa frågor för testning eller produktions arbets belastningar.

| Metodik | Beskrivning |
|-------------|-------------|
| Portalen| [Sök Utforskaren (portalen)](search-explorer.md) innehåller ett sökfält och alternativ för val av index och API-version. Resultat returneras som JSON-dokument. Rekommenderas för tidig undersökning, testning och validering. <br/>[Läs mer.](search-explorer.md) |
| Webb test verktyg| [Postman eller Visual Studio Code](search-get-started-rest.md) är starka val för att utforma [Sök dokument](/rest/api/searchservice/search-documents) rest-anrop. REST API har stöd för varje program mässig åtgärd i Azure Kognitiv sökning, så att du kan skicka förfrågningar interaktivt för att förstå hur det fungerar innan du investerar i kod.  |
| Azure SDK | [SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) kan användas för att fråga ett Sök index i C#.  [Läs mer.](search-howto-dotnet-sdk.md) <br/><br/>[SearchClient (python)](/dotnet/api/azure.search.documents.searchclient) kan användas för att fråga ett Sök index i python. [Läs mer.](search-get-started-python.md) <br/><br/> [SearchClient (Java Script)](/dotnet/api/azure.search.documents.searchclient) kan användas för att fråga ett Sök index i Java Script. [Läs mer.](search-get-started-javascript.md)  |

## <a name="set-up-a-search-client"></a>Konfigurera en Sök klient

En Sök klient autentiserar till Sök tjänsten, skickar begär Anden och hanterar svar. Frågor dirigeras alltid i dokument samlingen för ett enda index. Du kan inte ansluta index eller skapa anpassade eller tillfälliga data strukturer som mål för frågor.

### <a name="in-the-portal"></a>I portalen

Sök Utforskaren och andra Portal verktyg har en inbyggd klient anslutning till tjänsten med direkt åtkomst index och andra objekt från Portal sidor. Åtkomst till verktyg, guider och objekt förutsätter att du har administratörs behörighet för tjänsten. Med Sök Utforskaren kan du fokusera på att ange Sök sträng och andra parametrar. 

### <a name="using-rest"></a>Använda REST

För REST-anrop kan du använda [Postman eller liknande verktyg](search-get-started-rest.md) som klienten för att ange en begäran om [sökning av dokument](/rest/api/searchservice/search-documents) . Varje begäran är fristående, så du måste ange slut punkten (URL till tjänsten) och en administratörs-eller API-nyckel för åtkomst. Beroende på begäran kan URL: en även innehålla index namnet, dokument samlingen och andra egenskaper. Några få egenskaper, till exempel innehålls typ och API-nyckel, skickas till begär ande huvudet. Andra parametrar kan skickas till URL: en eller i bröd texten i begäran. Alla REST-anrop kräver en API-nyckel för autentisering och en API-version.

### <a name="using-azure-sdks"></a>Använda Azure SDK: er

Azure-SDK: er tillhandahåller Sök klienter som kan spara tillstånd, så att anslutningen kan återanvändas. För frågor om åtgärder instansieras du en SearchClient och anger värden för följande egenskaper: slut punkt, nyckel, index. Sedan kan du anropa Sök metoden för att ange frågesträngen. 

| Språk | Klient | Exempel |
|----------|--------|---------|
| C# och .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Skicka din första Sök fråga i C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Skicka din första Sök fråga i python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Skicka din första Sök fråga i Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Skicka din första Sök fråga i Java Script](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Välj en parser: enkel | fullständig

Med Azure Kognitiv sökning kan du välja mellan två fråge tolkare för hantering av typiska och specialiserade frågor. Begär Anden som använder den enkla parsern är vanligt vis fullständiga texts öknings frågor, formulerade med hjälp av [enkel frågesyntax](query-simple-syntax.md), valt som standard för dess hastighet och effektivitet i kostnads fria formulär text frågor. Den här syntaxen stöder ett antal vanliga Sök operatorer som operatörerna AND, OR, NOT, fras, suffix och prioritet.

Den [fullständiga Lucene-frågesyntaxen](query-Lucene-syntax.md#bkmk_syntax), som aktive ras när du lägger till `queryType=full` begäran, exponerar det vanligaste och lättfattliga programspecifika-frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Fullständig syntax utökar den enkla syntaxen. Alla frågor som du skriver för den enkla syntaxen körs under hela Lucene-parsern. 

I följande exempel visas punkten: samma fråga, men med olika **`queryType`** Inställningar, vilket ger olika resultat. I den första frågan `^3` `historic` behandlas efter som en del av Sök termen. Det översta resultatet för den här frågan är "Marquis Plaza & Suitess", *som har 10* i beskrivningen.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

Samma fråga som använder den fullständiga Lucene-parsern tolkar `^3` som en term förstärkning i fält. När du växlar parser ändras rangordningen, med resultat som innehåller den *historiska* termen flytta högst upp.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "ocean historic^3",
    "searchFields": "Description",
    "select": "HotelId, HotelName, Tags, Description",
}
```

## <a name="enable-query-behaviors-in-an-index"></a>Aktivera fråge beteenden i ett index

Index design och fråge design är nära kopplade i Azure Kognitiv sökning. *Index schemat*, med attribut för varje fält, bestämmer vilken typ av fråga som du kan bygga.

Indexattribut i ett fält ange tillåtna åtgärder – om ett fält är *sökbart* i indexet, kan *hämtas* i resultat, *sorterbart*, *filtrerat* och så vidare. I exempel frågorna `"$orderby": "Rating desc"` fungerar endast eftersom fältet klassificering är markerat som *sorterbart* i index schemat.

![Index definition för hotellet-exemplet](./media/search-query-overview/hotel-sample-index-definition.png "Index definition för hotellet-exemplet")

Skärm bilden ovan är en ofullständig lista över indexattribut för [exempel indexet Hotels](search-get-started-portal.md). Du kan skapa och Visa hela index schemat i portalen. Mer information om indexattribut finns i [skapa index (REST API)](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Nästa steg

Nu när du förstår hur begäran är konstruerad kan du prova med att använda både den enkla och fullständiga syntaxen.

+ [Enkla exempelfrågor](search-query-simple-examples.md)
+ [Exempel på Lucene-syntax för att skapa avancerade frågor](search-query-lucene-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)