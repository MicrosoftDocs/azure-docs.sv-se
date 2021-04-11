---
title: Så här modellerar du komplexa data typer
titleSuffix: Azure Cognitive Search
description: Kapslade eller hierarkiska data strukturer kan modelleras i ett Azure Kognitiv sökning-index med data typerna ComplexType och Collection.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: 0a9b1a746a5228ba958b9c1c0cca38b088ca1176
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222248"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Så här modellerar du komplexa data typer i Azure Kognitiv sökning

Externa data uppsättningar som används för att fylla ett Azure Kognitiv sökning-index kan komma i många former. Ibland innehåller de hierarkiska eller kapslade under strukturer. Exempel kan innehålla flera adresser för en enskild kund, flera färger och storlekar för en enda SKU, flera författare till en enda bok och så vidare. I modell villkor kan du se dessa strukturer som kallas *komplexa*, *sammansatta*, *sammansatta* eller *aggregerade* data typer. Termen Azure Kognitiv sökning används för det här konceptet är en **komplex typ**. I Azure Kognitiv sökning modelleras komplexa typer med hjälp av **komplexa fält**. Ett komplext fält är ett fält som innehåller underordnade (under fält) som kan vara av valfri datatyp, inklusive andra komplexa typer. Detta fungerar på samma sätt som strukturerade data typer i ett programmeringsspråk.

Komplexa fält representerar antingen ett enda objekt i dokumentet eller en matris med objekt, beroende på data typen. Fält av typen `Edm.ComplexType` representerar enstaka objekt, medan fält av typen `Collection(Edm.ComplexType)` representerar matriser med objekt.

Azure Kognitiv sökning har inbyggt stöd för komplexa typer och samlingar. Med de här typerna kan du modellera nästan alla JSON-strukturer i ett Azure Kognitiv sökning-index. I tidigare versioner av Azure Kognitiv sökning-API: er kunde endast sammanslagna rad uppsättningar importeras. I den senaste versionen kan indexet nu bättre motsvara källdata. Med andra ord, om dina källdata har komplexa typer, kan indexet också ha komplexa typer.

För att komma igång rekommenderar vi [hotell data uppsättningen](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), som du kan läsa in i guiden **importera data** i Azure Portal. Guiden identifierar komplexa typer i källan och föreslår ett index schema baserat på de identifierade strukturerna.

> [!Note]
> Stöd för komplexa typer blev allmänt tillgängligt från och med `api-version=2019-05-06` . 
>
> Om din Sök lösning bygger på tidigare lösningar för förenklade data uppsättningar i en samling bör du ändra indexet så att det innehåller komplexa typer som stöds i den senaste API-versionen. Mer information om hur du uppgraderar API-versioner finns i [Uppgradera till den nyaste REST API-versionen](search-api-migration.md) eller [Uppgradera till den senaste versionen av .NET SDK](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Exempel på en komplex struktur

Följande JSON-dokument består av enkla fält och komplexa fält. Komplexa fält, till exempel `Address` och `Rooms` , har underordnade fält. `Address` har en enda uppsättning värden för de underordnade fälten, eftersom det är ett enda objekt i dokumentet. Däremot har det `Rooms` flera värde uppsättningar för de underordnade fälten, en för varje objekt i samlingen.


```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Tags": ["Free wifi", "on-site parking", "indoor pool", "continental breakfast"],
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "RoomNumber": 1105,
      "BaseRate": 96.99,
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99,
    }
    . . .
  ]
}
```

## <a name="indexing-complex-types"></a>Indexera komplexa typer

Under indexeringen kan du ha högst 3000 element i alla komplexa samlingar i ett enda dokument. Ett element i en komplex samling är medlem i samlingen, så när det gäller rum (den enda komplexa samlingen i hotellet-exemplet) är varje rum ett-element. I exemplet ovan skulle hotellet-dokumentet ha 500 rums element om "Motel för hemliga punkter" hade 500-rum. För kapslade komplexa samlingar räknas även varje kapslat element, förutom det yttre (överordnade) elementet.

Den här gränsen gäller enbart för komplexa samlingar och inte komplexa typer (t. ex. adress) eller sträng samlingar (t. ex. taggar).

## <a name="creating-complex-fields"></a>Skapa komplexa fält

Precis som med alla index definitioner kan du använda portalen, [REST API](/rest/api/searchservice/create-index)eller [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindex) för att skapa ett schema som innehåller komplexa typer. 

I följande exempel visas ett index schema för JSON med enkla fält, samlingar och komplexa typer. Observera att varje under fält i en komplex typ har en typ och kan ha attribut, precis som fält på den översta nivån gör. Schemat motsvarar exempel data ovan. `Address` är ett komplext fält som inte är en samling (ett hotell har en adress). `Rooms` är ett komplext samlings fält (ett hotell har många rum).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Uppdaterar komplexa fält

Alla [Omindexering regler](search-howto-reindex.md) som gäller för fält i allmänhet gäller fortfarande för komplexa fält. Att lägga till ett fält i en komplex typ kräver inte att en index återskapas, men de flesta ändringar gör.

### <a name="structural-updates-to-the-definition"></a>Strukturella uppdateringar av definitionen

Du kan när som helst lägga till nya under fält i ett komplext fält utan att behöva skapa index igen. Till exempel är det tillåtet att lägga till "Postummer" till `Address` eller "bekvämligheterna" till `Rooms` , precis som att lägga till ett fält på översta nivån i ett index. Befintliga dokument har ett null-värde för nya fält tills du uttryckligen fyller i fälten genom att uppdatera dina data.

Observera att varje under fält i en komplex typ har en typ och kan ha attribut, precis som fält på den översta nivån gör

### <a name="data-updates"></a>Data uppdateringar

Uppdatering av befintliga dokument i ett index med `upload` åtgärden fungerar på samma sätt för komplexa och enkla fält – alla fält ersätts. Men `merge` (eller `mergeOrUpload` när det tillämpas på ett befintligt dokument) fungerar inte likadant i alla fält. Mer specifikt `merge` stöder inte sammanfogning av element i en samling. Den här begränsningen finns för samlingar med primitiva typer och komplexa samlingar. Om du vill uppdatera en samling måste du hämta det fullständiga samling svärdet, göra ändringar och sedan ta med den nya samlingen i API-begäran för indexet.

## <a name="searching-complex-fields"></a>Söker i komplexa fält

Sök uttryck med fri form fungerar som förväntat med komplexa typer. Om ett sökbart fält eller under fält var som helst i ett dokument matchar, är själva dokumentet en matchning.

Frågor får fler nyanserade när du har flera villkor och vissa villkor har angivna fält namn, vilket är möjligt med [Lucene-syntaxen](query-lucene-syntax.md). Den här frågan försöker till exempel matcha två villkor, "Göteborg" och "OR", mot två under fält i adress fältet:

> `search=Address/City:Portland AND Address/State:OR`

Frågor som detta är *korrelerade* för full texts ökning, till skillnad från filter. I filter korreleras frågor över underordnade fält i en komplex samling med hjälp av Range-variabler i [ `any` eller `all` ](search-query-odata-collection-operators.md). En Lucene-fråga ovan returnerar dokument som innehåller både "Göteborg, Maine" och "Göteborg, Göteborg", tillsammans med andra städer i Göteborg. Detta inträffar eftersom varje sats gäller för alla värden i sitt fält i hela dokumentet, så det finns inget sätt att använda ett "Aktuellt under dokument". Mer information finns i [förstå OData Collection filter i Azure kognitiv sökning](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Välja komplexa fält

`$select`Parametern används för att välja vilka fält som returneras i Sök resultaten. Om du vill använda den här parametern för att välja vissa underordnade fält i ett komplext fält inkluderar du det överordnade fältet och under fältet avgränsat med ett snedstreck ( `/` ).

> `$select=HotelName, Address/City, Rooms/BaseRate`

Fält måste markeras som hämtnings bara i indexet om du vill ha dem i Sök resultaten. Endast fält som har marker ATS som hämtnings bara kan användas i en `$select` instruktion.

## <a name="filter-facet-and-sort-complex-fields"></a>Filtrera, fasett och sortera komplexa fält

Samma [OData Path-syntax](query-odata-filter-orderby-syntax.md) som används för filtrering och fältade sökningar kan också användas för att fasetta, sortera och välja fält i en sökbegäran. För komplexa typer gäller reglerna som styr vilka under fält som kan markeras som sorterbara eller aspekt bara. Mer information om dessa regler finns i referens för [create index API](/rest/api/searchservice/create-index).

### <a name="faceting-sub-fields"></a>Fasett-underfält

Alla under fält kan markeras som fasettable om det inte är av typen `Edm.GeographyPoint` eller `Collection(Edm.GeographyPoint)` .

Antalet dokument som returneras i fasett-resultaten beräknas för det överordnade dokumentet (ett hotell), inte under dokumenten i en komplex samling (rum). Anta till exempel att ett hotell har 20 rum av typen "Suite". Med den här aspekt parametern är `facet=Rooms/Type` aspekt antalet ett för hotellet, inte 20 för rummen.

### <a name="sorting-complex-fields"></a>Sortera komplexa fält

Sorterings åtgärder gäller för dokument (hotell) och inte under dokument (rum). När du har en komplex typ samling, t. ex. rum, är det viktigt att du inser att du inte kan sortera på rum alls. I själva verket kan du inte sortera efter samlingar.

Sorterings åtgärder fungerar när fält har ett enda värde per dokument, om fältet är ett enkelt fält eller ett underordnat fält i en komplex typ. Kan till exempel `Address/City` vara sorterbar eftersom det bara finns en adress per hotell, så `$orderby=Address/City` att hotellen sorteras efter ort.

### <a name="filtering-on-complex-fields"></a>Filtrering av komplexa fält

Du kan referera till underordnade fält i ett komplext fält i ett filter uttryck. Använd bara samma [OData Path-syntax](query-odata-filter-orderby-syntax.md) som används för att fasetta, sortera och välja fält. Följande filter kommer till exempel att returnera alla hotell i Kanada:

> `$filter=Address/Country eq 'Canada'`

Om du vill filtrera efter ett komplext samlings fält kan du använda ett **lambda-uttryck** med [ `any` `all` operatorerna och](search-query-odata-collection-operators.md). I så fall är **Range-variabeln** för Lambda-uttrycket ett objekt med under fält. Du kan referera till de underordnade fälten med standard-syntaxen för OData-sökvägen. Följande filter returnerar till exempel alla hotell med minst ett Deluxe-rum och alla non-rökning-rum:

> `$filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)`

Precis som med enkla fält på översta nivån kan enkla under fält av komplexa fält bara tas med i filter om de har det **filter** bara attributet inställt på `true` i index definitionen. Mer information finns i referens för [create index API](/rest/api/searchservice/create-index).

## <a name="next-steps"></a>Nästa steg

Prova [hotell data uppsättningen](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) i guiden **Importera data** . Du behöver Cosmos DB anslutnings information som anges i Readme för att komma åt data.

Med den informationen i hand är ditt första steg i guiden att skapa en ny Azure Cosmos DB-datakälla. I guiden visas ett index med komplexa typer när du kommer till sidan mål index. Skapa och Läs in det här indexet och kör sedan frågor för att förstå den nya strukturen.

> [!div class="nextstepaction"]
> [Snabb start: Portal guide för import, indexering och frågor](search-get-started-portal.md)
