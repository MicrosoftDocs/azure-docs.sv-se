---
title: Skapa en grundläggande fråga
titleSuffix: Azure Cognitive Search
description: 'Lär dig hur du skapar en förfrågan i Kognitiv sökning, vilka verktyg och API: er som ska användas för testning och kod samt hur fråge beslut börjar med index design.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: a02d51d66b9d2b8bf3c08d4515713ecb062e0c8e
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400224"
---
# <a name="create-a-query-in-azure-cognitive-search"></a>Skapa en fråga i Azure Kognitiv sökning

Om du skapar en fråga för första gången beskriver den här artikeln de verktyg och API: er som du behöver, vilka metoder som används för att skapa en fråga och hur index struktur och innehåll kan påverka frågeresultat. För en introduktion till hur en fråge förfrågan ser ut, börjar du med [frågetyper och sammansättningar](search-query-overview.md).

## <a name="choose-tools-and-apis"></a>Välj verktyg och API: er

Du behöver ett verktyg eller ett API för att skapa en fråga. Något av följande förslag är användbart för testning och produktions arbets belastningar.

| Metodik | Beskrivning |
|-------------|-------------|
| Portalen| [Sök Utforskaren (portalen)](search-explorer.md) är ett frågespråk i Azure Portal som kör frågor mot index i den underliggande Sök tjänsten. Portalen gör REST API samtal bakom bakgrunden till åtgärden [Sök dokument](/rest/api/searchservice/search-documents) , men kan inte anropa Autoavsluta, förslag eller dokuments ökning.<br/><br/> Du kan välja valfritt index och REST API version, inklusive för hands version. En frågesträng kan använda enkel eller fullständig syntax, med stöd för alla frågeparametrar (filter, Select, searchFields och så vidare). När du öppnar ett index i portalen kan du arbeta med Sök Utforskaren tillsammans med index-JSON-definitionen i sida-vid-sida-flikar för enkel åtkomst till fältattribut. Kontrol lera vilka fält som är sökbara, sorterbara, kan filtreras och fasettiska vid testning av frågor. <br/>Rekommenderas för tidig undersökning, testning och validering. [Läs mer.](search-explorer.md) |
| Webb test verktyg| [Postman eller Visual Studio Code](search-get-started-rest.md) är starka val för att utforma en begäran om [sökning av dokument](/rest/api/searchservice/search-documents) och andra förfrågningar i vila. REST-API: er har stöd för varje möjlig programmerings åtgärd i Azure Kognitiv sökning, och när du använder ett verktyg som Postman eller Visual Studio Code kan du skicka förfrågningar interaktivt för att förstå hur funktionen fungerar innan du investerar i kod. Ett webb test verktyg är ett bra alternativ om du inte har deltagar-eller administratörs rättigheter i Azure Portal. Så länge du har en Sök-URL och en fråge-API-nyckel kan du använda verktygen för att köra frågor mot ett befintligt index. |
| Azure SDK | När du är redo att skriva kod kan du använda klient biblioteken Azure.Search.Document i Azure SDK: er för .NET, python, java script eller Java. Varje SDK är i ett eget versions schema, men du kan skapa och fråga index i alla. <br/><br/>[SearchClient (.net)](/dotnet/api/azure.search.documents.searchclient) kan användas för att fråga ett Sök index i C#.  [Läs mer.](search-howto-dotnet-sdk.md)<br/><br/>[SearchClient (python)](/dotnet/api/azure.search.documents.searchclient) kan användas för att fråga ett Sök index i python. [Läs mer.](search-get-started-python.md)<br/><br/>[SearchClient (Java Script)](/dotnet/api/azure.search.documents.searchclient) kan användas för att fråga ett Sök index i Java Script. [Läs mer.](search-get-started-javascript.md) |

## <a name="set-up-a-search-client"></a>Konfigurera en Sök klient

En Sök klient autentiserar till Sök tjänsten, skickar begär Anden och hanterar svar. Oavsett vilket verktyg eller API du använder måste en Sök klient ha följande:

| Egenskaper | Beskrivning |
|------------|-------------|
| Slutpunkt | En Sök tjänst är URL-adresserad i det här formatet: `https://[service-name].search.windows.net` . |
| API-åtkomst nyckel (administratör eller fråga) | Autentiserar begäran till Sök tjänsten. |
| Index namn | Frågor dirigeras alltid i dokument samlingen för ett enda index. Du kan inte ansluta index eller skapa anpassade eller tillfälliga data strukturer som mål för frågor. |
| API-version | REST-anrop kräver uttryckligen `api-version` på begäran. Klient bibliotek i Azure SDK har däremot versions hantering mot en bestämd REST API-version. För SDK: er `api-version` är implicit. |

### <a name="in-the-portal"></a>I portalen

Sök Utforskaren och andra Portal verktyg har en inbyggd klient anslutning till tjänsten med direkt åtkomst index och andra objekt från Portal sidor. Åtkomst till verktyg, guider och objekt kräver medlemskap i deltagar rollen eller ovanför tjänsten. 

### <a name="using-rest"></a>Använda REST

För REST-anrop kan du använda [Postman eller liknande verktyg](search-get-started-rest.md) som klienten för att ange en begäran om [sökning av dokument](/rest/api/searchservice/search-documents) . Varje begäran är fristående, så du måste ange slut punkten, index namnet och API-versionen på varje begäran. Andra egenskaper, innehålls typ och API-nyckel skickas till begär ande huvudet. 

Du kan använda POST eller GET för att fråga ett index. POST, med parametrar som anges i begär ande texten, är enklare att arbeta med. Om du använder POST ska du se till att ta med `docs/search` i URL: en:

```http
POST https://myservice.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

### <a name="using-azure-sdks"></a>Använda Azure SDK: er

Om du använder ett Azure SDK skapar du klienten i kod. Alla SDK: er tillhandahåller Sök klienter som kan spara tillstånd, så att anslutningen kan återanvändas. För frågor om operationer instansieras du en **`SearchClient`** och ger värden för följande egenskaper: slut punkt, nyckel, index. Sedan kan du anropa **`Search method`** för att skicka in frågesträngen. 

| Språk | Klient | Exempel |
|----------|--------|---------|
| C# och .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [Skicka din första Sök fråga i C #](/dotnet/api/overview/azure/search.documents-readme#send-your-first-search-query) |
| Python      | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [Skicka din första Sök fråga i python](/python/api/overview/azure/search-documents-readme#send-your-first-search-request) |
| Java        | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [Skicka din första Sök fråga i Java](/java/api/overview/azure/search-documents-readme#send-your-first-search-query)  |
| JavaScript  | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [Skicka din första Sök fråga i Java Script](/javascript/api/overview/azure/search-documents-readme#send-your-first-search-query)  |

## <a name="choose-a-parser-simple--full"></a>Välj en parser: enkel | fullständig

Om frågan är full texts ökning används en parser för att bearbeta innehållet i Sök parametern. Azure Kognitiv sökning erbjuder två fråge tolkare. Den enkla parsern förstår den [enkla frågesyntaxen](query-simple-syntax.md). Den här parsern valdes som standard för dess hastighet och effektivitet i kostnads fria formulär text frågor. Syntaxen stöder vanliga Sök operatorer (och, eller, inte) för term-och fras sökningar och prefix ( `*` )-sökning (som i "Sea *" för Seattle och Seaside). En allmän rekommendation är att testa den enkla parsern först och sedan gå vidare till fullständig parser om program kraven kräver mer kraftfulla frågor.

[Fullständig Lucene-frågesyntax](query-Lucene-syntax.md#bkmk_syntax), som aktive ras när du lägger till i `queryType=full` begäran, baseras på [Apache Lucene-parsern](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Fullständig syntax är en utökning av den enkla syntaxen, med fler operatorer så att du kan skapa avancerade frågor, till exempel suddig sökning, sökning med jokertecken, närhets sökning och reguljära uttryck. I följande exempel visas punkten: samma fråga, men med olika **`queryType`** Inställningar, vilket ger olika resultat. I den första enkla frågan `^3` `historic` behandlas efter som en del av Sök termen. Det översta resultatet för den här frågan är "Marquis Plaza & Suitess", *som har 10* i beskrivningen.

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

## <a name="choose-query-methods"></a>Välj fråge metoder

Sökningen är i grunden en användar driven övning där termer eller fraser samlas in från en sökruta eller från klicknings händelser på en sida. I följande tabell sammanfattas de mekanismer som du kan använda för att samla in användarindata, tillsammans med den förväntade Sök upplevelsen.

| Indata | Erfarenhet |
|-------|---------|
| [Sök metod](/rest/api/searchservice/search-documents) | En användare skriver in termer eller fraser i en sökruta, med eller utan operatorer, och klickar på Sök för att skicka begäran. Sökningen kan användas med filter på samma begäran, men inte med Autoavsluta eller förslag. |
| [Metoden komplettera automatiskt](/rest/api/searchservice/autocomplete) | En användare skriver några tecken och frågor initieras efter att varje nytt tecken har skrivits. Svaret är en slutförd sträng från indexet. Om den angivna strängen är giltig klickar användaren på Sök för att skicka frågan till tjänsten. |
| [Förslags metod](/rest/api/searchservice/suggestions) | Precis som med automatisk komplettering skapas en användare som skriver några tecken och stegvisa frågor. Svaret är en listruta med matchande dokument, som vanligt vis representeras av några unika eller beskrivande fält. Om något av valen är giltigt klickar användaren på ett och det matchande dokumentet returneras. |
| [Aspektbaserad navigering](/rest/api/searchservice/search-documents#query-parameters) | En sida visar klicknings bara navigerings länkar eller spår som begränsar Sök omfånget. En aspektad navigerings struktur sammanställs dynamiskt baserat på en inledande fråga. Om du till exempel `search=*` vill fylla i ett fasett navigerings träd som består av varje möjlig kategori. En fasett-navigerings struktur skapas från ett fråge svar, men det är också en mekanism för att uttrycka nästa fråga. n REST API referens `facets` är dokumenterad som en frågeparameter för en Sök dokument åtgärd, men den kan användas utan `search` parametern.|
| [Filter metod](/rest/api/searchservice/search-documents#query-parameters) | Filter används med Faces för att begränsa resultaten. Du kan också implementera ett filter bakom sidan, till exempel för att initiera sidan med språkspecifika fält. I REST API referens `$filter` dokumenteras som en frågeparameter för en Sök dokument åtgärd, men den kan användas utan `search` parametern.|

## <a name="know-your-field-attributes"></a>Känn till dina fältattribut

Om du tidigare har granskat [grunderna i en fråga](search-query-overview.md), kan du komma ihåg att parametrarna på fråge förfrågningen är beroende av hur fälten attributas i ett index. Om du till exempel vill använda i en fråga, filtrera eller sortera ordning måste ett fält vara *sökbart*, *filtrerat* och *sorterbart*. På samma sätt kan endast fält som har marker ATS som *hämtnings* bara visas i resultat. När du börjar ange `search` `filter` parametrarna,, och `orderby` i din begäran måste du kontrol lera attributen när du går för att undvika oväntade resultat.

I portalens skärm bild nedan av [exempel indexet Hotels](search-get-started-portal.md), kan endast de två sista fälten "LastRenovationDate" och "klassificering" användas i en `"$orderby"` enda sats.

![Index definition för hotellet-exemplet](./media/search-query-overview/hotel-sample-index-definition.png "Index definition för hotellet-exemplet")

En beskrivning av fältattribut finns i [create index (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Ta reda på dina tokens

Vid indexering använder frågemotor en analys för att utföra text analyser på strängar, vilket maximerar risken för matchning vid tidpunkten för frågan. Som minst är strängarna lägre-bokstäver, men de kan också genomgå lemmatisering och stoppa borttagning av ord. Större strängar eller sammansatta ord delas vanligt vis upp efter blank steg, bindestreck eller tank streck och indexeras som separata tokens. 

Den punkt som ska tas bort här är att det du tror att ditt index innehåller, och vad som faktiskt finns i det, kan vara olika. Om frågor inte returnerar förväntade resultat kan du granska de token som skapats av analysen genom att [analysera texten (REST API)](/rest/api/searchservice/test-analyzer). Mer information om tokenisering och hur du kan påverka frågor finns i [partiell terms ökning och mönster med specialtecken](search-query-partial-matching.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en bättre förståelse för hur en fråge förfrågan är konstruerad, kan du prova följande snabb starter för praktisk erfarenhet.

+ [Sök Utforskaren](search-explorer.md)
+ [Fråga i REST](search-get-started-rest.md)
+ [Så här frågar du i .NET](search-get-started-dotnet.md)
+ [Fråga i python](search-get-started-python.md)
+ [Fråga i Java Script](search-get-started-javascript.md)