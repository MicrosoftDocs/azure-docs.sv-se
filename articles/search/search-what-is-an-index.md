---
title: Skapa ett index
titleSuffix: Azure Cognitive Search
description: Introducerar indexerings begrepp och verktyg i Azure Kognitiv sökning, inklusive schema definitioner och den fysiska data strukturen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d9f4ba48a7dc6cdcf6d60e4e9da5f68fcc6b1f28
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509341"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Skapa Sök index i Azure Kognitiv sökning

Ett Sök index lagrar sökbart innehåll som används för fullständig text och filtrerade frågor. Ett index definieras av ett schema och sparas i tjänsten, med data import enligt ett andra steg. 

Index innehåller *dokument*. Ett dokument är konceptuellt en enskild enhet med sökbara data i ditt index. En åter försäljare kan ha ett dokument för varje produkt, en nyhets organisation kan ha ett dokument för varje artikel och så vidare. Mappa dessa begrepp till mer välkända databas motsvarigheter: ett *sökindex* motsvarar en *tabell*, och *dokument* är ungefär lika med *rader* i en tabell.

## <a name="whats-an-index-schema"></a>Vad är ett index schema?

Den fysiska strukturen för ett index bestäms av schemat. Fields-samlingen är vanligt vis den största delen av ett index, där varje fält namnges, tilldelas en [datatyp](/rest/api/searchservice/Supported-data-types)och attributas med tillåtna beteenden som avgör hur det används.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Andra element komprimeras för det kortfattat, men följande länkar kan ge information: [förslags ställare](index-add-suggesters.md), [bedömnings profiler](index-add-scoring-profiles.md), [analys](search-analyzers.md) verktyg som används för att bearbeta strängar i token enligt språkliga regler eller andra egenskaper som stöds av analysatorn, och CORS-inställningar [(Cross-Origin Remote Scripting)](#corsoptions) .

## <a name="choose-a-client"></a>Välj en klient

Det finns flera metoder för att skapa ett sökindex. Vi rekommenderar Azure Portal-eller REST-API: er för tidig utveckling och koncept validerings testning.

Under utvecklingen bör du planera om frekventa återuppbyggnadar. Eftersom fysiska strukturer skapas i tjänsten är det nödvändigt att [släppa och återskapa index](search-howto-reindex.md) för de flesta ändringar i en befintlig fält definition. Du kan överväga att arbeta med en delmängd av dina data för att göra återskapandet snabbare.

### <a name="permissions"></a>Behörigheter

Alla åtgärder som rör ett sökindex, inklusive GET-begäranden som dess definition, kräver en [admin-API-nyckel](search-security-api-keys.md) på begäran.

### <a name="limits"></a>Gränser

Alla [tjänst nivåer begränsar](search-limits-quotas-capacity.md#index-limits) antalet objekt som du kan skapa. Om du experimenterar på den kostnads fria nivån kan du bara ha tre index vid en specifik tidpunkt.

### <a name="use-azure-portal-to-create-a-search-index"></a>Använd Azure Portal för att skapa ett sökindex

Portalen innehåller två alternativ för att skapa ett sökindex: [**guiden Importera data**](search-import-data-portal.md) och **Lägg till index** som innehåller fält för att ange ett index schema. Guide paketen i ytterligare åtgärder genom att även skapa en indexerare, data källa och läsa in data. Om det är mer än vad du vill ha bör du bara använda **Lägg till index** eller någon annan metod.

Följande skärm bild visar var du kan hitta **Lägg till index** i portalen. **Importera data** är rätt nästa dörr.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Lägg till index kommando" border="true":::

> [!Tip]
> Index design via portalen kräver krav och schema regler för särskilda data typer, t. ex. att inte tillåta fullständiga texts öknings funktioner på numeriska fält. När du har ett fungerande index kan du kopiera JSON-filen från portalen och lägga till den i din lösning.

### <a name="use-a-rest-client"></a>Använd en REST-klient

Både Postman och Visual Studio Code (med ett tillägg för Azure Kognitiv sökning) kan fungera som en Sök index klient. Med något av verktygen kan du ansluta till Sök tjänsten och skicka begäran om att [skapa index (rest)](/rest/api/searchservice/create-index) . Det finns flera självstudier och exempel som demonstrerar REST-klienter för att skapa objekt. 

Börja med någon av dessa artiklar för att lära dig om varje klient:

+ [Skapa ett Sök index med REST och Postman](search-get-started-rest.md)
+ [Kom igång med Visual Studio Code och Azure Kognitiv sökning](search-get-started-vs-code.md)

Se [index åtgärder (rest)](/rest/api/searchservice/index-operations) om du vill ha hjälp med att utforma index begär Anden.

### <a name="use-an-sdk"></a>Använda en SDK

För Kognitiv sökning implementerar Azure SDK: er allmänt tillgängliga funktioner. Därför kan du använda valfri SDK för att skapa ett sökindex. Alla tillhandahåller en **SearchIndexClient** som har metoder för att skapa och uppdatera index.

| Azure SDK | Klient | Exempel |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [Azure-Search-dotNet-samples/snabb start/v11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| JavaScript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Index](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="defining-fields"></a>Definiera fält

Ett fält av typen EDM. String måste anges som dokument nyckel. Den används för att unikt identifiera varje sökdokument. Du kan hämta ett dokument efter dess nyckel för att fylla i en informations sida.  

Om inkommande data är hierarkiska kan du tilldela data typen [komplex typ](search-howto-complex-data-types.md) som representerar de kapslade strukturerna. Den inbyggda exempel data uppsättningen, hotell, visar komplexa typer som använder en adress (innehåller flera delfält) som har en 1-till-en-relation med varje hotell, och en komplex samling för rum där flera rum är associerade med varje hotell. 

Tilldela eventuella analyser till sträng fält innan indexet skapas. Gör samma sak för förslags ställare om du vill aktivera Autoavsluta för vissa fält.

<a name="index-attributes"></a>

### <a name="attributes"></a>Attribut

Fältattributen avgör hur ett fält används, till exempel om det används i fulltextsökning, fasetterad navigering, sorteringsåtgärder och så vidare. 

Sträng fält markeras ofta som "sökbara" och "hämtnings bara". Fält som används för att begränsa Sök resultaten är "sorterbar", "Filterable" och "fasettable".

|Attribut|Beskrivning|  
|---------------|-----------------|  
|sökbara |Fulltextsökbart och omfattas av lexikal analys som radbrytning under indexering. Om du anger ett sökbart fält till ett värde som ”solig dag” delas det upp internt i två enskilda token, ”solig” och ”dag”. Mer information finns i [Hur fulltextsökning fungerar](search-lucene-query-architecture.md).|  
|filtrerbara |Refereras till i $filter-frågor. Filtrerbara fält av typen `Edm.String` eller `Collection(Edm.String)` genomgår inte ordseparation, så jämförelserna gäller endast exakta matchningar. Om du till exempel anger ”solig dag” för ett sådant fält hittar inte `$filter=f eq 'sunny'` några matchningar, men det gör `$filter=f eq 'sunny day'`. |  
|sorterbar |Som standard sorterar systemet resultaten efter bedömning, men du kan konfigurera sortering som baseras på fält i dokumenten. Fält av typen `Collection(Edm.String)` får inte vara "sorterbar". |  
|fasettbar |Används vanligtvis i en presentation av sökresultat som innehåller ett antal träffar efter kategori (till exempel hotell på en viss ort). Det här alternativet kan inte användas med fält av typen `Edm.GeographyPoint`. Fält av typen `Edm.String` som kan filtreras, "sorterbar" eller "fasettable" kan vara högst 32 kilobyte långa. Mer information finns i [Skapa index (REST-API)](/rest/api/searchservice/create-index).|  
|knapp |Unik identifierare för dokument i indexet. Exakt ett fält måste väljas som nyckelfält, och det måste vara av typen `Edm.String`.|  
|Hämtnings bar |Anger om fältet kan returneras i ett sökresultat. Detta är användbart om du vill använda ett fält (som *vinstmarginal*) som ett filter eller en sorterings- eller bedömningsmekanism, men inte vill att fältet ska visas för användaren. Det här attributet måste vara `true` för `key`-fält.|  

Du kan visserligen lägga till nya fält när som helst, men befintliga fältdefinitioner är låsta under indexets hela livslängd. Av den anledningen använder många utvecklare portalen för att skapa enkla index, testa idéer och använda portalsidorna för att söka reda på en inställning. Frekvent upprepning av en indexdesign är mer effektiv om du följer en kodbaserad metod så att du enkelt kan återskapa indexet.

> [!NOTE]
> API: er som du använder för att bygga ett index har varierande standard beteenden. För [REST-API: erna](/rest/api/searchservice/Create-Index)är de flesta attribut aktiverade som standard (till exempel "sökbara" och "hämtnings bara" är sanna för sträng fält) och du behöver ofta bara ange dem om du vill inaktivera dem. För .NET SDK är motsatsen sant. På alla egenskaper som du inte uttryckligen anger är standardvärdet att inaktivera motsvarande Sök beteende om du inte uttryckligen aktiverar det.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attribut och index storlek (lagrings effekter)

Storleken på ett index bestäms av storleken på de dokument du överför, plus index konfiguration, till exempel om du tar med förslag och hur du anger attribut för enskilda fält. 

Följande skärm bild illustrerar index lagrings mönster som orsakas av olika kombinationer av attribut. Indexet baseras på **fastighets indexets exempel index**, som du enkelt kan skapa med hjälp av guiden Importera data. Även om index scheman inte visas kan du härleda attributen baserat på index namnet. Till exempel är *realestate-sökbart* index det "sökbara" attributet markerat och inget annat, *realestate-hämtnings* Bart index har attributet "hämtnings Bart" markerat och inget annat, och så vidare.

![Index storlek baserat på val av attribut](./media/search-what-is-an-index/realestate-index-size.png "Index storlek baserat på val av attribut")

Även om de här varianterna av index är artificiella kan vi referera till dem för att se hur attribut påverkar lagringen. Ger inställningen "hämtnings bar" ökning av index storlek? Nej. Lägger du till fält till en **förslags** öknings index storlek? Ja.

Index som stöder filter och sortering är proportionerligt större än index som stöder bara full texts ökning. Detta beror på att filtrerings-och sorterings åtgärder söker efter exakta matchningar, vilket kräver förekomst av orda Grant text strängar. Sökbara fält som stöder full text frågor använder inverterade index, som är ifyllda med token-termer som förbrukar mindre utrymme än hela dokument. 

> [!Note]
> Lagrings arkitektur betraktas som en implementerings detalj i Azure Kognitiv sökning och kan ändras utan föregående meddelande. Det finns ingen garanti för att det aktuella beteendet är kvar i framtiden.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Tar `corsOptions`

Index scheman innehåller ett avsnitt för inställningen `corsOptions` . Java Script på klient sidan kan inte anropa några API: er som standard eftersom webbläsaren förhindrar alla cross-origin-begäranden. Om du vill tillåta frågor om kors ursprung till ditt index aktiverar du CORS (resurs delning mellan ursprung) genom att ange attributet **corsOptions** . Av säkerhets skäl stöder endast API: er för frågor. 

Följande alternativ kan ställas in för CORS:

+ **allowedOrigins** (obligatoriskt): det här är en lista över ursprung som ska beviljas åtkomst till ditt index. Det innebär att alla JavaScript-koder som hanteras från dessa ursprung kommer att kunna fråga ditt index (förutsatt att det innehåller rätt API-nyckel). Varje ursprung är vanligt vis ett formulär `protocol://<fully-qualified-domain-name>:<port>` , men det `<port>` är ofta utelämnat. Se [resurs delning mellan ursprung (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) om du vill ha mer information.

  Om du vill tillåta åtkomst till alla ursprung, inkludera `*` som ett enskilt objekt i **allowedOrigins** -matrisen. *Detta är inte en rekommenderad metod för produktions Sök tjänster* , men det är ofta användbart för utveckling och fel sökning.

+ **maxAgeInSeconds** (valfritt): webbläsare använder det här värdet för att fastställa varaktigheten (i sekunder) för att cachelagra CORS preflight-svar. Detta måste vara ett icke-negativt heltal. Ju större det här värdet är, desto bättre prestanda blir, men det längre tar för CORS-principens ändringar att börja gälla. Om den inte anges används en standard varaktighet på 5 minuter.

## <a name="next-steps"></a>Nästa steg

Du kan få praktisk erfarenhet av att skapa ett index med hjälp av nästan alla exempel eller genom gång för Kognitiv sökning. Du kan välja någon av snabb starterna från innehålls förteckningen för att komma igång.

Du ska också bekanta dig med metoder för att läsa in ett index med data. Index definitionen och populationen görs tillsammans. I följande artiklar finns mer information.

+ [Översikt över dataimport](search-what-is-data-import.md)

+ [Lägga till, uppdatera eller ta bort dokument (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 