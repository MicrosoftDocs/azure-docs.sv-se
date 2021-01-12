---
title: Skapa ett sökindex
titleSuffix: Azure Cognitive Search
description: Introducerar indexerings begrepp och verktyg i Azure Kognitiv sökning, inklusive schema definitioner och den fysiska data strukturen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3d5663177bb087e936a49dd7289659b684d85860
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116202"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Skapa ett Basic Search-index i Azure Kognitiv sökning

I Azure Kognitiv sökning lagrar ett *sökindex* sökbart innehåll som används för fullständig text och filtrerade frågor. Ett index definieras av ett schema och sparas i tjänsten, med data import enligt ett andra steg. 

Index innehåller *dokument*. Ett dokument är konceptuellt en enskild enhet med sökbara data i ditt index. En åter försäljare kan ha ett dokument för varje produkt, en nyhets organisation kan ha ett dokument för varje artikel och så vidare. Mappa dessa begrepp till mer välkända databas motsvarigheter: ett *sökindex* motsvarar en *tabell*, och *dokument* är ungefär lika med *rader* i en tabell.

Den fysiska strukturen för ett index bestäms av schemat, med fält som har marker ATS som sökbara, vilket resulterar i ett inverterat index som skapats för fältet. 

Du kan skapa ett index med följande verktyg och API: er:

* I Azure Portal använder du guiden **Lägg till index** eller **Importera data**
* Använda [create index (REST API)](/rest/api/searchservice/create-index)
* Använda [.NET SDK](./search-get-started-dotnet.md)

Det är lättare att lära dig med ett Portal verktyg. Portalen tillämpar krav och schema regler för särskilda data typer, t. ex. att tillåta full texts ökning på numeriska fält. När du har ett fungerande index kan du övergå till kod genom att hämta JSON-definitionen från tjänsten med hjälp av [Get index (REST API)](/rest/api/searchservice/get-index) och lägga till den i din lösning.

## <a name="recommended-workflow"></a>Rekommenderat arbets flöde

Den slutliga index designen är en iterativ process. Det är vanligt att börja med portalen för att skapa det ursprungliga indexet och sedan växla till kod för att placera indexet under käll kontroll.

1. Bestäm om du kan använda [**Importera data**](search-import-data-portal.md). Guiden utför allt-i-ett indexerare-baserat index om käll informationen kommer från en [typ av data källa som stöds i Azure](search-indexer-overview.md#supported-data-sources).

1. Om du inte kan använda **Importera data** börjar du med **Lägg till index** för att definiera schemat.

   ![Lägg till index kommando](media/search-what-is-an-index/add-index.png "Lägg till index kommando")

1. Ange ett namn och en nyckel som används för att unikt identifiera varje sökdokument i indexet. Nyckeln är obligatorisk och måste vara av typen EDM. String. Under importen bör du planera att mappa ett unikt fält i källdata till det här fältet. 

   Portalen ger dig ett `id` fält för nyckeln. Om du vill åsidosätta standardvärdet `id` skapar du ett nytt fält (till exempel en ny fält definition som heter `HotelId` ) och väljer det i **nyckel**.

   ![Fyll i de obligatoriska egenskaperna](media/search-what-is-an-index//field-attributes.png "Fyll i de obligatoriska egenskaperna")

1. Lägg till fler fält. Portalen [visar vilka fältattribut som är](#index-attributes) tillgängliga för olika data typer. Detta är användbart om du inte har använt index design tidigare.

   Om inkommande data är hierarkiska kan du tilldela data typen [komplex typ](search-howto-complex-data-types.md) som representerar de kapslade strukturerna. Den inbyggda exempel data uppsättningen, hotell, visar komplexa typer som använder en adress (innehåller flera delfält) som har en 1-till-en-relation med varje hotell, och en komplex samling för rum där flera rum är associerade med varje hotell. 

1. Tilldela eventuella [analyser](#analyzers) till sträng fält innan indexet skapas. Gör samma sak för [förslags ställare](#suggesters) om du vill aktivera Autoavsluta för vissa fält.

1. Skapa fysiska strukturer i din Sök tjänst genom att klicka på **skapa** .

1. När ett index har skapats kan du använda ytterligare kommandon för att granska definitioner eller lägga till fler element.

   ![Sidan Lägg till index som visar attribut efter datatyp](media/search-what-is-an-index//field-definitions.png "Sidan Lägg till index som visar attribut efter datatyp")

1. Hämta index schema med [Get index (REST API)](/rest/api/searchservice/get-index) och ett webbtest-verktyg som [Postman](search-get-started-rest.md). Nu har du en JSON-representation av indexet som du kan anpassa efter kod.

1. [Läs in ditt index med data](search-what-is-data-import.md). Azure Kognitiv sökning accepterar JSON-dokument. Om du vill läsa in dina data program mässigt kan du använda Postman med JSON-dokument i nytto lasten för begäran. Om dina data inte är enkla att uttryckas som JSON, är det här steget det mest arbets krävande. 

    När ett index har lästs in med data, kräver de flesta ändringar i befintliga fält att du släpper och återskapar ett index.

1. Fråga ditt index, granska resultaten och upprepa detta genom att upprepa index schemat tills du börjar se de resultat du förväntar dig. Du kan använda [**Sök Utforskaren**](search-explorer.md) eller Postman för att fråga ditt index.

Under utvecklingen bör du planera om frekventa återuppbyggnadar. Eftersom fysiska strukturer skapas i tjänsten är det nödvändigt att [släppa och återskapa index](search-howto-reindex.md) för de flesta ändringar i en befintlig fält definition. Du kan överväga att arbeta med en delmängd av dina data för att göra återskapandet snabbare. 

> [!Tip]
> Kod i stället för en portal metod rekommenderas för att arbeta med index design och data import samtidigt. Som ett alternativ är verktyg som [Postman](search-get-started-rest.md) eller [Visual Studio Code](search-get-started-vs-code.md) användbara för koncept bevis testning när utvecklings projekt fortfarande är i tidiga faser. Du kan göra stegvisa ändringar i en index definition i en begär ande text och sedan skicka begäran till tjänsten för att återskapa ett index med ett uppdaterat schema.

## <a name="index-schema"></a>Index schema

Det krävs ett index för att ha ett namn och ett särskilt nyckel fält (av EDM. String) i fält samlingen. [*Fält samlingen*](#fields-collection) är vanligt vis den största delen av ett index, där varje fält namnges, skrivs och attributas med tillåtna beteenden som avgör hur det används. 

Andra element är [förslags ställare](#suggesters), [bedömnings profiler](#scoringprofiles), [analys](#analyzers) verktyg som används för att bearbeta strängar i token enligt språkliga regler eller andra egenskaper som stöds av analysatorn, och CORS-inställningar [(Cross-Origin Remote Scripting)](#corsoptions) .

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
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
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Fält samling och fält-attribut

Fält har ett namn, en typ som klassificerar lagrade data och attribut som anger hur fältet används.

### <a name="data-types"></a>Datatyper

| Typ | Description |
|------|-------------|
| Edm.String |Text som kan, om du vill, använda en token för full texts ökning (ord brytning, ord och så vidare). |
| Collection(Edm.String) |En lista med strängar som kan tokeniseras för textsökning. Det finns ingen teoretisk övre gräns för antalet objekt i en samling, men den övre gränsen på 16 MB för nyttolasten gäller för samlingar. |
| Edm.Boolean |Innehåller sant/falskt-värden. |
| Edm.Int32 |32-bitars heltalsvärden. |
| Edm.Int64 |64-bitars heltalsvärden. |
| Edm.Double |Numeriska data med dubbel precision. |
| Edm.DateTimeOffset |Datum/tid-värden som representeras i OData v4-formatet (till exempel `yyyy-MM-ddTHH:mm:ss.fffZ` eller `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| Edm.GeographyPoint |En punkt som representerar en geografisk plats i världen. |

Mer information finns i [data typer som stöds](/rest/api/searchservice/Supported-data-types).

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

## `analyzers`

I elementet analyser anges namnet på den språk analys som ska användas för fältet. Mer information om vilka analys intervall som är tillgängliga finns i [lägga till analyser i ett Azure kognitiv sökning-index](search-analyzers.md). Analys verktyg kan bara användas med sökbara fält. När analysen har tilldelats ett fält kan det inte ändras om du inte bygger om indexet.

## `suggesters`

En förslags ställare är en del av schemat som definierar vilka fält i ett index som används för att stödja automatisk fullständig eller typ av frågor i sökningar. Normalt skickas partiella Sök strängar till [förslagen (REST API)](/rest/api/searchservice/suggestions) medan användaren skriver en Sök fråga och API: et returnerar en uppsättning föreslagna dokument eller fraser. 

Fält som läggs till i en förslags lista används för att generera typ kommande Sök villkor. Alla Sök termer skapas vid indexering och lagras separat. Mer information om hur du skapar en förslags struktur finns i [lägga till förslag](index-add-suggesters.md).

## `corsOptions`

Java Script på klient sidan kan inte anropa några API: er som standard eftersom webbläsaren förhindrar alla cross-origin-begäranden. Om du vill tillåta frågor om kors ursprung till ditt index aktiverar du CORS (resurs delning mellan ursprung) genom att ange attributet **corsOptions** . Av säkerhets skäl stöder endast API: er för frågor. 

Följande alternativ kan ställas in för CORS:

+ **allowedOrigins** (obligatoriskt): det här är en lista över ursprung som ska beviljas åtkomst till ditt index. Det innebär att alla JavaScript-koder som hanteras från dessa ursprung kommer att kunna fråga ditt index (förutsatt att det innehåller rätt API-nyckel). Varje ursprung är vanligt vis ett formulär `protocol://<fully-qualified-domain-name>:<port>` , men det `<port>` är ofta utelämnat. Se [resurs delning mellan ursprung (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) om du vill ha mer information.

  Om du vill tillåta åtkomst till alla ursprung, inkludera `*` som ett enskilt objekt i **allowedOrigins** -matrisen. *Detta är inte en rekommenderad metod för produktions Sök tjänster* , men det är ofta användbart för utveckling och fel sökning.

+ **maxAgeInSeconds** (valfritt): webbläsare använder det här värdet för att fastställa varaktigheten (i sekunder) för att cachelagra CORS preflight-svar. Detta måste vara ett icke-negativt heltal. Ju större det här värdet är, desto bättre prestanda blir, men det längre tar för CORS-principens ändringar att börja gälla. Om den inte anges används en standard varaktighet på 5 minuter.

## `scoringProfiles`

En [bedömnings profil](index-add-scoring-profiles.md) är en del av schemat som definierar anpassade bedömnings beteenden som låter dig påverka vilka objekt som visas högre upp i Sök resultaten. Bedömnings profiler består av fält vikter och-funktioner. Om du vill använda dem anger du en profil efter namn i frågesträngen.

En standard bedömnings profil fungerar i bakgrunden för att beräkna en Sök Poäng för varje objekt i en resultat uppsättning. Du kan använda den interna, namnlösa bedömnings profilen. Du kan också ange att **defaultScoringProfile** ska använda en anpassad profil som standard, anropas när en anpassad profil inte anges i frågesträngen.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Attribut och index storlek (lagrings effekter)

Storleken på ett index bestäms av storleken på de dokument du överför, plus index konfiguration, till exempel om du tar med förslag och hur du anger attribut för enskilda fält. 

Följande skärm bild illustrerar index lagrings mönster som orsakas av olika kombinationer av attribut. Indexet baseras på **fastighets indexets exempel index**, som du enkelt kan skapa med hjälp av guiden Importera data. Även om index scheman inte visas kan du härleda attributen baserat på index namnet. Till exempel är *realestate-sökbart* index det "sökbara" attributet markerat och inget annat, *realestate-hämtnings* Bart index har attributet "hämtnings Bart" markerat och inget annat, och så vidare.

![Index storlek baserat på val av attribut](./media/search-what-is-an-index/realestate-index-size.png "Index storlek baserat på val av attribut")

Även om de här varianterna av index är artificiella kan vi referera till dem för att se hur attribut påverkar lagringen. Ger inställningen "hämtnings bar" ökning av index storlek? Nej. Lägger du till fält till en **förslags** öknings index storlek? Ja.

Index som stöder filter och sortering är proportionerligt större än index som stöder bara full texts ökning. Detta beror på att filtrerings-och sorterings åtgärder söker efter exakta matchningar, vilket kräver förekomst av orda Grant text strängar. Sökbara fält som stöder full text frågor använder inverterade index, som är ifyllda med token-termer som förbrukar mindre utrymme än hela dokument. 

> [!Note]
> Lagrings arkitektur betraktas som en implementerings detalj i Azure Kognitiv sökning och kan ändras utan föregående meddelande. Det finns ingen garanti för att det aktuella beteendet är kvar i framtiden.

## <a name="next-steps"></a>Nästa steg

Med förståelse för index komposition kan du fortsätta i portalen för att skapa ditt första index. Vi rekommenderar att du börjar med guiden **Importera data** och väljer antingen data källor för *realestate-US-Sample* eller *hotell-exempel* värd.

> [!div class="nextstepaction"]
> [Guiden Importera data (portal)](search-get-started-portal.md)

För båda data uppsättningarna kan guiden härleda ett index schema, importera data och generera ett sökbart index som du kan fråga med hjälp av Sök Utforskaren. Hitta dessa data källor på sidan **Anslut till dina data** i guiden **Importera data** .

   ![Skapa ett exempel index](media/search-what-is-an-index//import-wizard-sample-data.png "Skapa ett exempel index")