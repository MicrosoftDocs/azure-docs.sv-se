---
title: Uppgradera REST API-versioner
titleSuffix: Azure Cognitive Search
description: Granska skillnader i API-versioner och lär dig vilka åtgärder som krävs för att migrera befintlig kod till den senaste versionen av Azure Kognitiv sökning service REST API.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d7734fde529c24e8113ea3b019d343b7223f0122
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91929650"
---
# <a name="upgrade-to-the-latest-rest-api-in-azure-cognitive-search"></a>Uppgradera till den senaste REST API i Azure Kognitiv sökning

Om du använder en tidigare version av [**sök REST API**](/rest/api/searchservice/)kan den här artikeln hjälpa dig att uppgradera programmet till den senaste allmänt tillgängliga API-versionen, **2020-06-30**.

Version 2020-06-30 innehåller en viktig ny funktion ([kunskaps lager](knowledge-store-concept-intro.md)) och introducerar flera mindre beteende ändringar. Den här versionen är i huvudsak bakåtkompatibel så att kod ändringar ska vara minimala om du uppgraderar från den tidigare versionen (2019-05-06).

> [!NOTE]
> En Sök tjänst stöder flera REST API versioner, inklusive tidigare. Du kan fortsätta att använda dessa API-versioner, men vi rekommenderar att du migrerar din kod till den senaste versionen så att du kan komma åt nya funktioner. Med tiden kommer de mest inaktuella versionerna av REST API att bli föråldrade och [stöds inte längre](search-api-versions.md#unsupported-versions).

<a name="UpgradeSteps"></a>

## <a name="how-to-upgrade"></a>Så här uppgraderar du

När du uppgraderar till en ny version behöver du förmodligen inte göra många ändringar i din kod, förutom att ändra versions numret. De enda situationer där du kan behöva ändra kod är när:

* Koden fungerar inte när okända egenskaper returneras i ett API-svar. Som standard ska programmet ignorera egenskaper som det inte förstår.

* Din kod behåller API-begärandena och försöker skicka om dem till den nya API-versionen. Detta kan till exempel inträffa om ditt program behåller fortsättnings-token som returneras från Sök-API: et (mer information finns `@search.nextPageParameters` i [Sök-API-referensen](/rest/api/searchservice/Search-Documents)).

* Din kod refererar till en API-version som fördaterar 2019-05-06 och omfattas av en eller flera av de större ändringarna i den versionen. Avsnittet [Uppgradera till 2019-05-06](#upgrade-to-2019-05-06) innehåller mer information. 

Om någon av dessa situationer gäller dig kan du behöva ändra koden. Annars bör inga ändringar vara nödvändiga, men du kanske vill börja använda funktioner som har lagts till i den nya versionen.

## <a name="upgrade-to-2020-06-30"></a>Uppgradera till 2020-06-30

Version 2020-06-30 är den nya allmänt tillgängliga versionen av REST API. Det finns en rad brytnings förändring och flera beteende skillnader. 

Funktionerna är nu allmänt tillgängliga i den här API-versionen:

* [Kunskaps lager](knowledge-store-concept-intro.md), beständigt lagrings utrymme med innehåll som skapats via färdighetsuppsättningar, som skapats för underordnad analys och bearbetning via andra program. Med den här funktionen kan en indexerare-driven AI-pipeline fylla i ett kunskaps lager förutom ett Sök index. Om du har använt för hands versionen av den här funktionen motsvarar den allmänt tillgängliga versionen. Den enda kod ändring som krävs är att ändra API-versionen.

### <a name="breaking-change"></a>Bryta ändring

Befintlig kod som skrivs mot tidigare API-versioner kommer att brytas på API-version = 2020-06-30 och senare om koden innehåller följande funktioner:

* Alla EDM. date-litteraler (ett datum som består av år-månad-dag, t. ex. `2020-12-12` ) i filter uttryck måste följa formatet EDM. DateTimeOffset: `2020-12-12T00:00:00Z` . Den här ändringen var nödvändig för att hantera felaktiga eller oväntade frågeresultat på grund av skillnader i zonen.

### <a name="behavior-changes"></a>Beteende ändringar

* [Algoritmen för BM25](index-ranking-similarity.md) ersätter den tidigare rangordningen med nyare teknik. Nya tjänster kommer att använda den här algoritmen automatiskt. För befintliga tjänster måste du ange parametrar för att använda den nya algoritmen.

* Sorterade resultat för null-värden har ändrats i den här versionen, med null-värden som visas först om sorteringen är `asc` och sist `desc` . Om du har skrivit kod för att hantera hur null-värden sorteras bör du vara medveten om den här ändringen.

## <a name="upgrade-to-2019-05-06"></a>Uppgradera till 2019-05-06

Version 2019-05-06 är den tidigare allmänt tillgängliga versionen av REST API. Funktioner som är allmänt tillgängliga i den här API-versionen är:

* Funktionen [komplettera automatiskt](index-add-suggesters.md) är en typeahead-funktion som slutför en delvis angiven term Indatatyp.
* [Komplexa typer](search-howto-complex-data-types.md) ger inbyggt stöd för strukturerade objekt data i Sök index.
* [JsonLines tolknings lägen](search-howto-index-json-blobs.md), en del av Azure Blob-indexering, skapar ett Sök dokument per JSON-entitet som skiljs åt av en ny rad.
* [AI-anrikning](cognitive-search-concept-intro.md) ger indexering som utnyttjar AI-förrikande motorer i Cognitive Services.

### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

Befintlig kod som skrivs mot tidigare API-versioner kommer att brytas på API-version = 2019-05-06 och senare om koden innehåller följande funktioner:

#### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexerare för Azure Cosmos DB-DataSource är nu "typ": "cosmosdb"

Om du använder en [Cosmos db-indexerare](search-howto-index-cosmosdb.md )måste du ändra `"type": "documentdb"` till `"type": "cosmosdb"` .

#### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Resultat fel för körning av indexerare har inte längre status

Fel strukturen för körning av indexet hade tidigare ett `status` element. Det här elementet har tagits bort eftersom det inte tillhandahöll användbar information.

#### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Data källans API för indexeraren returnerar inte längre anslutnings strängar

Från API-versionerna 2019-05-06 och 2019-05-06 – för hands versions hantering returnerar API: et för data källan inte längre anslutnings strängar i svar på eventuella REST-åtgärder. I tidigare API-versioner, för data källor som skapats med POST, returnerade Azure Kognitiv sökning **201** följt av OData-svaret, som innehöll anslutnings strängen i oformaterad text.

#### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Arbetsenhets igenkänning för entitets igenkänning upphör nu

Om du har besvarat [namnet entitets igenkännings](cognitive-search-skill-named-entity-recognition.md) kunskap i din kod, Miss Miss anropet. Ersättnings funktionen är [enhets igenkänning](cognitive-search-skill-entity-recognition.md). Du bör kunna ersätta kunskaps referensen utan andra ändringar. API-signaturen är densamma för båda versionerna. 

### <a name="upgrading-complex-types"></a>Uppgradera komplexa typer

API-version 2019-05-06 lade till formell support för komplexa typer. Om din kod har implementerat tidigare rekommendationer för komplex typ som passar i 2017-11-11-Preview eller 2016-09-01 – för hands version, finns det några nya och ändrade gränser som börjar i den version 2019-05-06 som du måste vara medveten om:

+ Gränserna för djupet i under fält och antalet komplexa samlingar per index har sänkts. Om du har skapat index som överskrider dessa gränser med hjälp av API-versionerna för för hands versionen, kommer alla försök att uppdatera eller återskapa dem med hjälp av API version 2019-05-06 att Miss lyckas. Om detta gäller för dig måste du utforma om schemat så att det passar inom de nya gränserna och sedan återskapa ditt index.

+ Det finns en ny gräns som börjar i API-version 2019-05-06 för antalet element i komplexa samlingar per dokument. Om du har skapat index med dokument som överskrider dessa gränser med hjälp av API-versionerna för för hands versionen, kommer alla försök att indexera om dessa data med hjälp av API-version 2019-05-06 att Miss lyckas. Om detta gäller för dig måste du minska antalet komplexa samlings element per dokument innan du indexerar om dina data.

Mer information finns i [tjänst begränsningar för Azure kognitiv sökning](search-limits-quotas-capacity.md).

#### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Så här uppgraderar du en gammal komplex typ struktur

Om din kod använder komplexa typer med en av de äldre API-versionerna för för hands versionen kan du använda ett index definitions format som ser ut så här:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Ett nyare träd som liknar formatet för att definiera index fält introducerades i API-versionen 2017-11-11-Preview. I det nya formatet har varje komplex fält en fält samling där dess under fält har definierats. I API version 2019-05-06 används det här nya formatet enbart och om du försöker skapa eller uppdatera ett index med det gamla formatet kommer att Miss lyckas. Om du har index som skapats med det gamla formatet måste du använda API-versionen 2017-11-11-Preview för att uppdatera dem till det nya formatet innan de kan hanteras med API-version 2019-05-06.

Du kan uppdatera "platta" index till det nya formatet med följande steg med hjälp av API-version 2017-11-11-för hands version:

1. Hämta ditt index genom att utföra en GET-begäran. Om den redan är i det nya formatet är du klar.

2. Översätt indexet från det "platta" formatet till det nya formatet. Du måste skriva kod för detta eftersom det inte finns någon exempel kod tillgänglig vid tidpunkten för den här skrivningen.

3. Utför en skicka-begäran för att uppdatera indexet till det nya formatet. Se till att du inte ändrar någon annan information om indexet, till exempel sökmöjlighet/filtrering av fält, eftersom detta inte tillåts av uppdaterings indexets API.

> [!NOTE]
> Det går inte att hantera index som skapats med det gamla "platta" formatet från Azure Portal. Uppgradera dina index från "platt" åter givning till "Tree"-representationen i den första bekvämligheten.

## <a name="next-steps"></a>Nästa steg

Läs REST API referens dokumentation för Sök. Om du stöter på problem ber vi oss om hjälp med [Stack Overflow](https://stackoverflow.com/) eller [kontakta supporten](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Sök tjänst REST API referens](/rest/api/searchservice/)
