---
title: Skapa en fråga
titleSuffix: Azure Cognitive Search
description: 'Lär dig hur du skapar en förfrågan i Kognitiv sökning, vilka verktyg och API: er som ska användas för testning och kod samt hur fråge beslut börjar med index design.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 5a89e9ae05b0733c865d537ffeb1714d3b3ebef1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489369"
---
# <a name="creating-queries-in-azure-cognitive-search"></a>Skapa frågor i Azure Kognitiv sökning

Om du skapar en fråga för första gången beskriver den här artikeln metoder och metoder för att ställa in frågor. Den introducerar också en förfrågan och förklarar hur fältattribut och språkanalytiker kan påverka frågeresultat.

## <a name="whats-a-query-request"></a>Vad är en förfrågan om fråga?

En fråga är en skrivskyddad begäran mot dokument samlingen för ett enda sökindex. Den anger en search-parameter som innehåller frågeuttrycket, som består av termer, citat tecken, fraser och operatorer.

Ytterligare parametrar ger mer definition till frågan och svaret. Till exempel, "searchFields"-frågor för vissa fält, "Select" anger vilka fält som returneras i resultat och "count" returnerar antalet matchningar som finns i indexet.

I följande exempel får du en allmän uppfattning om en förfrågan genom att visa en delmängd av de tillgängliga parametrarna. Mer information om frågans sammansättning finns i [frågetyper och sammansättningar](search-query-overview.md) och [Sök dokument (rest)](/rest/api/searchservice/search-documents).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "NY +view",
    "queryType": "simple",
    "searchMode": "all",
    "searchFields": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "select": "HotelName, Description, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

## <a name="choose-a-client"></a>Välj en klient

Du behöver ett verktyg som Azure Portal eller Postman eller kod som instansierar en fråga-klient med hjälp av API: er. Vi rekommenderar Azure Portal-eller REST-API: er för tidig utveckling och koncept validerings testning.

### <a name="permissions"></a>Behörigheter

Alla åtgärder, inklusive fråge förfrågningar, fungerar under en [administrations-API-nyckel](search-security-api-keys.md), men förfrågningar om frågor kan eventuellt användas för att använda en [API-nyckel för frågor](search-security-api-keys.md#create-query-keys). Fråge-API-nycklar rekommenderas starkt. Du kan skapa upp till 50 per tjänst och tilldela olika nycklar till olika program.

I Azure Portal kräver åtkomst till verktyg, guider och objekt medlemskap i deltagar rollen eller ovanför tjänsten. 

### <a name="use-azure-portal-to-query-an-index"></a>Använda Azure Portal för att fråga ett index

[Sök Utforskaren (portalen)](search-explorer.md) är ett frågespråk i Azure Portal som kör frågor mot index i den underliggande Sök tjänsten. Internt gör portalen [sökdokument](/rest/api/searchservice/search-documents) begär Anden, men kan inte anropa Autoavsluta, förslag eller dokuments ökning. 

Du kan välja valfritt index och REST API version, inklusive för hands version. En frågesträng kan använda enkel eller fullständig syntax, med stöd för alla frågeparametrar (filter, Select, searchFields och så vidare). När du öppnar ett index i portalen kan du arbeta med Sök Utforskaren tillsammans med index-JSON-definitionen i sida-vid-sida-flikar för enkel åtkomst till fältattribut. Kontrol lera vilka fält som är sökbara, sorterbara, kan filtreras och fasettiska vid testning av frågor.

### <a name="use-a-rest-client"></a>Använd en REST-klient

Både Postman och Visual Studio Code (med ett tillägg för Azure Kognitiv sökning) kan fungera som en klient för frågor. Med något av verktygen kan du ansluta till Sök tjänsten och skicka [Sök dokument (rest)](/rest/api/searchservice/search-documents) begär Anden. Flera självstudier och exempel demonstrerar REST-klienter för att fråga indexering. 

Börja med någon av dessa artiklar för att lära dig om varje klient (båda innehåller instruktioner för frågor):

+ [Skapa ett Sök index med REST och Postman](search-get-started-rest.md)
+ [Kom igång med Visual Studio Code och Azure Kognitiv sökning](search-get-started-vs-code.md)

Varje begäran är fristående, så du måste ange slut punkten, index namnet och API-versionen på varje begäran. Andra egenskaper, innehålls typ och API-nyckel skickas till begär ande huvudet. Mer information finns i [Sök efter dokument (rest)](/rest/api/searchservice/search-documents) för att få hjälp med att utforma fråge förfrågningar.

### <a name="use-an-sdk"></a>Använda en SDK

För Kognitiv sökning implementerar Azure SDK: er allmänt tillgängliga funktioner. Därför kan du använda alla SDK: er för att fråga ett index. Alla tillhandahåller en **SearchClient** som har metoder för att interagera med ett index, från att läsa in ett index med sökdokument för att utforma förfrågningar.

| Azure SDK | Klient | Exempel |
|-----------|--------|----------|
| .NET | [SearchClient](/dotnet/api/azure.search.documents.searchclient) | [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) |
| Java | [SearchClient](/java/api/com.azure.search.documents.searchclient) | [SearchForDynamicDocumentsExample. java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchForDynamicDocumentsExample.java) |
| JavaScript | [SearchClient](/javascript/api/@azure/search-documents/searchclient) | [readonlyQuery.js](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) |
| Python | [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) | [sample_simple_query. py ](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_simple_query.py) |

## <a name="choose-a-query-type-simple--full"></a>Välj typ av fråga: enkel | fullständig

Om frågan är full texts ökning, används en Query parser för att bearbeta all text som skickas som Sök villkor och fraser. Azure Kognitiv sökning erbjuder två fråge tolkare. 

+ Den enkla parsern förstår den [enkla frågesyntaxen](query-simple-syntax.md). Den här parsern valdes som standard för dess hastighet och effektivitet i kostnads fria formulär text frågor. Syntaxen stöder vanliga Sök operatorer (och, eller, inte) för term-och fras sökningar och prefix ( `*` )-sökning (som i "Sea *" för Seattle och Seaside). En allmän rekommendation är att testa den enkla parsern först och sedan gå vidare till fullständig parser om program kraven kräver mer kraftfulla frågor.

+ [Fullständig Lucene-frågesyntax](query-Lucene-syntax.md#bkmk_syntax), som aktive ras när du lägger till i `queryType=full` begäran, baseras på [Apache Lucene-parsern](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

Fullständig syntax och enkel syntax överlappar den utsträckning som båda stöder samma prefix och booleska åtgärder, men den fullständiga syntaxen innehåller fler operatorer. Det finns dessutom fler operatorer för booleska uttryck och fler operatorer för avancerade frågor, till exempel suddig sökning, sökning med jokertecken, närhets sökning och reguljära uttryck.

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

Om du tidigare har granskat [frågetyper och sammansättning](search-query-overview.md)kan du komma ihåg att parametrarna på fråge förfrågningen är beroende av hur fälten attributas i ett index. Om du till exempel vill använda i en fråga, filtrera eller sortera ordning måste ett fält vara *sökbart*, *filtrerat* och *sorterbart*. På samma sätt kan endast fält som har marker ATS som *hämtnings* bara visas i resultat. När du börjar ange `search` `filter` parametrarna,, och `orderby` i din begäran måste du kontrol lera attributen när du går för att undvika oväntade resultat.

I portalens skärm bild nedan av [exempel indexet Hotels](search-get-started-portal.md), kan endast de två sista fälten "LastRenovationDate" och "klassificering" användas i en `"$orderby"` enda sats.

![Index definition för hotellet-exemplet](./media/search-query-overview/hotel-sample-index-definition.png "Index definition för hotellet-exemplet")

En beskrivning av fältattribut finns i [create index (REST API)](/rest/api/searchservice/create-index).

## <a name="know-your-tokens"></a>Ta reda på dina tokens

Under indexeringen använder sökmotorn en analys för att utföra text analyser på strängar, vilket maximerar risken för matchning vid tidpunkten för frågan. Som minst är strängarna lägre-bokstäver, men de kan också genomgå lemmatisering och stoppa borttagning av ord. Större strängar eller sammansatta ord delas vanligt vis upp efter blank steg, bindestreck eller tank streck och indexeras som separata tokens. 

Den punkt som ska tas bort här är att det du tror att ditt index innehåller, och vad som faktiskt finns i det, kan vara olika. Om frågor inte returnerar förväntade resultat kan du granska de token som skapats av analysen genom att [analysera texten (REST API)](/rest/api/searchservice/test-analyzer). Mer information om tokenisering och hur du kan påverka frågor finns i [partiell terms ökning och mönster med specialtecken](search-query-partial-matching.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en bättre förståelse för hur fråge förfrågningar fungerar, kan du prova följande snabb starter för praktisk erfarenhet.

+ [Sök Utforskaren](search-explorer.md)
+ [Fråga i REST](search-get-started-rest.md)
+ [Så här frågar du i .NET](search-get-started-dotnet.md)
+ [Fråga i python](search-get-started-python.md)
+ [Fråga i Java Script](search-get-started-javascript.md)