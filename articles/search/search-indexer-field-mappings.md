---
title: Fältmappningar i indexerare
titleSuffix: Azure Cognitive Search
description: Konfigurera fält mappningar i en indexerare för att redovisa skillnader i fält namn och data representationer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: fb3a77291d8b24d5774094533f8c214f1527d771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430453"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Fält mappningar och transformeringar med Azure Kognitiv sökning indexerare

![Indexerings steg](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "indexerings steg")

När du använder Azure Kognitiv sökning indexerare ser du ibland till att indata inte stämmer överens med schemat för ditt mål index. I dessa fall kan du använda **fält mappningar** för att ändra form på dina data under indexerings processen.

Några situationer där fält mappningar är användbara:

* Data källan har ett fält med namnet `_id` , men Azure kognitiv sökning tillåter inte fält namn som börjar med ett under streck. Med en fält mappning kan du effektivt byta namn på ett fält.
* Du vill fylla i flera fält i indexet från samma data käll data. Du kanske till exempel vill använda olika analys verktyg för dessa fält.
* Du vill fylla i ett index fält med data från fler än en data källa och data källorna använder olika fält namn.
* Du måste base64 koda eller avkoda dina data. Fält mappningar har stöd för flera **mappnings funktioner**, inklusive funktioner för base64-kodning och avkodning.

> [!NOTE]
> Fält mappningar i indexerare är ett enkelt sätt att mappa data fält till index fält, med viss möjlighet till låg data konvertering. Mer komplexa data kan kräva för bearbetning för att forma om den till ett formulär som är till för indexering. Ett alternativ som du kan överväga är [Azure Data Factory](../data-factory/index.yml).

## <a name="set-up-field-mappings"></a>Konfigurera fält mappningar

En fält mappning består av tre delar:

1. En `sourceFieldName` , som representerar ett fält i data källan. Den här egenskapen är obligatorisk.
2. En valfri `targetFieldName` , som representerar ett fält i Sök indexet. Om det utelämnas används samma namn som i data källan.
3. En valfri `mappingFunction` , som kan transformera dina data med hjälp av en av flera fördefinierade funktioner. Detta kan användas både för mappningar för in-och utgående fält. Den fullständiga listan över funktioner finns [nedan](#mappingFunctions).

Fält mappningar läggs till i `fieldMappings` matrisen för index definition.

> [!NOTE]
> Om inga fält mappningar läggs till antar indexerarna att data käll fält ska mappas till index fält med samma namn. Om du lägger till en fält mappning tas dessa standard fält mappningar bort för fältet källa och mål. Vissa indexerare, till exempel [Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md), lägger till standard fält mappningar för fältet index nyckel.

## <a name="map-fields-using-rest"></a>Mappa fält med REST

Du kan lägga till fält mappningar när du skapar en ny indexerare med hjälp av API-förfrågan [skapa indexerare](/rest/api/searchservice/create-Indexer) . Du kan hantera fält mappningar för en befintlig indexerare med hjälp av API-begäran [Uppdatera indexerare](/rest/api/searchservice/update-indexer) .

Så här mappar du till exempel ett käll fält till ett målfält med ett annat namn:

```JSON
PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Det går att referera till ett käll fält i flera fält mappningar. I följande exempel visas hur du kopierar samma käll fält till två olika index fält med "förgrening" i ett fält:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Kognitiv sökning använder SKIFT läges okänslig jämförelse för att matcha fält-och funktions namn i fält mappningar. Detta är praktiskt (du behöver inte hämta alla Skift läges rättigheter), men det innebär att data källan eller indexet inte kan ha fält som bara skiljer sig från versaler.  
>

## <a name="map-fields-using-net"></a>Mappa fält med hjälp av .NET

Du definierar fält mappningar i .NET SDK med klassen [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) , som har egenskaperna `SourceFieldName` och `TargetFieldName` , och en valfri `MappingFunction` referens.

Du kan ange fält mappningar när du konstruerar indexeraren eller senare genom att ange egenskapen direkt `Indexer.FieldMappings` .

I följande C#-exempel anges fält mappningar när du skapar en indexerare.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Fält mappnings funktioner

En fält mappnings funktion omvandlar innehållet i ett fält innan det lagras i indexet. Följande mappnings funktioner stöds för närvarande:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>funktionen base64Encode

Utför *URL-säker base64-* kodning för Indatasträngen. Förutsätter att indatatypen är UTF-8-kodad.

#### <a name="example---document-key-lookup"></a>Exempel – sökning efter dokument nyckel

Endast URL-säkra tecken kan visas i en Azure Kognitiv sökning-dokument nyckel (eftersom kunderna måste kunna adressera dokumentet med [söknings-API: et](/rest/api/searchservice/lookup-document) ). Om käll fältet för nyckeln innehåller URL-osäkra tecken kan du använda `base64Encode` funktionen för att konvertera den vid indexerings tiden. Men en dokument nyckel (både före och efter konverteringen) får inte vara längre än 1 024 tecken.

När du hämtar den kodade nyckeln vid sökning kan du använda `base64Decode` funktionen för att hämta det ursprungliga nyckelvärdet och använda det för att hämta käll dokumentet.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>Exempel – Behåll ursprungliga värden

[Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md) lägger automatiskt till en fält mappning från `metadata_storage_path` , URI: n för blobben, till fältet index nyckel om ingen fält mappning har angetts. Det här värdet är Base64-kodat så att det är säkert att använda som en Azure Kognitiv sökning-dokument nyckel. I följande exempel visas hur du samtidigt mappar en *URL-säker base64-* kodad version av `metadata_storage_path` till ett `index_key` fält och behåller det ursprungliga värdet i ett `metadata_storage_path` fält:

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Om du inte inkluderar en parameter egenskap för mappnings funktionen används värdet som standard `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Kognitiv sökning stöder två olika base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64 encoding-alternativ](#base64details) för att avgöra vilka parametrar som ska användas.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>funktionen base64Decode

Utför base64-avkodning av Indatasträngen. Indatamängden antas vara en *URL-säker* Base64-kodad sträng.

#### <a name="example---decode-blob-metadata-or-urls"></a>Exempel – avkoda BLOB-metadata eller URL: er

Dina källdata kan innehålla base64-kodade strängar, till exempel BLOB-metadata strängar eller webb adresser, som du vill göra sökbara som oformaterad text. Du kan använda `base64Decode` funktionen för att omvandla kodade data tillbaka till vanliga strängar när du fyller i sökindexet.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Om du inte tar med en parameter egenskap används värdet som standard `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Kognitiv sökning stöder två olika base64-kodningar. Du bör använda samma parametrar när du kodar och avkodar samma fält. Mer information finns i [base64 encoding-alternativ](#base64details) för att avgöra vilka parametrar som ska användas.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>Base64-kodnings alternativ

Azure Kognitiv sökning stöder URL-säker base64-kodning och normal base64-kodning. En sträng som är Base64-kodad vid indexering ska avkodas senare med samma kodnings alternativ, eller annars matchar inte resultatet originalet.

Om `useHttpServerUtilityUrlTokenEncode` -eller `useHttpServerUtilityUrlTokenDecode` -parametrarna för encoding och deencoding är inställda på `true` , `base64Encode` beter sig sedan som [HttpServerUtility. UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) och `base64Decode` beter sig som [HttpServerUtility. UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Om `base64Encode` används för att skapa nyckel värden `useHttpServerUtilityUrlTokenEncode` måste anges till sant. Endast URL-säker base64-kodning kan användas för nyckel värden. Se [namngivnings regler &#40;Azure Kognitiv sökning&#41;](/rest/api/searchservice/naming-rules) för att få en fullständig uppsättning begränsningar för tecken i nyckel värden.

.NET-biblioteken i Azure Kognitiv sökning antar den fullständiga .NET Framework, som innehåller inbyggd kodning. `useHttpServerUtilityUrlTokenEncode`Alternativen och `useHttpServerUtilityUrlTokenDecode` utnyttjar denna inbyggda funktion. Om du använder .NET Core eller ett annat ramverk rekommenderar vi att du ställer in de här alternativen till `false` och anropar ramverkets kodning och avkodnings funktioner direkt.

I följande tabell jämförs olika base64-kodningar för strängen `00>00?00` . Om du vill fastställa nödvändig ytterligare bearbetning (om det finns någon) för dina base64-funktioner använder du din biblioteks kodnings funktion i strängen `00>00?00` och jämför utdata med förväntade utdata `MDA-MDA_MDA` .

| Kodning | Base64-koda utdata | Ytterligare bearbetning efter biblioteks kodning | Ytterligare bearbetning före biblioteks avkodning |
| --- | --- | --- | --- |
| Base64 med utfyllnad | `MDA+MDA/MDA=` | Använd URL-säkra tecken och ta bort utfyllnad | Använd standard base64-tecken och Lägg till utfyllnad |
| Base64 utan utfyllnad | `MDA+MDA/MDA` | Använd URL-säkra tecken | Använd standard base64-tecken |
| URL – säker base64 med utfyllnad | `MDA-MDA_MDA=` | Ta bort utfyllnad | Lägg till utfyllnad |
| URL – säker base64 utan utfyllnad | `MDA-MDA_MDA` | Inga | Inga |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>funktionen extractTokenAtPosition

Delar upp ett sträng fält med angiven avgränsare och plockar token på den angivna positionen i den resulterande delningen.

Den här funktionen använder följande parametrar:

* `delimiter`: en sträng som ska användas som avgränsare vid delning av Indatasträngen.
* `position`: en heltal noll-baserad position för den token som ska väljas efter att Indatasträngen har delats upp.

Om till exempel indatatypen är `Jane Doe` , `delimiter` är `" "` (blank steg) och `position` är 0, blir resultatet `Jane` `position` . om är 1 är resultatet `Doe` . Om positionen refererar till en token som inte finns returneras ett fel.

#### <a name="example---extract-a-name"></a>Exempel – extrahera ett namn

Data källan innehåller ett `PersonName` fält och du vill indexera det som två separata `FirstName` `LastName` fält och fält. Du kan använda den här funktionen för att dela upp indatamängden med blank stegs tecken som avgränsare.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>funktionen jsonArrayToStringCollection

Transformerar en sträng formaterad som en JSON-matris med strängar till en sträng mat ris som kan användas för att fylla ett `Collection(Edm.String)` fält i indexet.

Om till exempel Indatasträngen är `["red", "white", "blue"]` , fylls mål fält av typ `Collection(Edm.String)` med de tre värdena `red` , `white` och `blue` . Ett fel returneras för inmatade värden som inte kan parsas som JSON-sträng mat ris.

#### <a name="example---populate-collection-from-relational-data"></a>Exempel – fylla i samling från Relations data

Azure SQL Database har inte någon inbyggd datatyp som naturligt mappar till `Collection(Edm.String)` fält i Azure kognitiv sökning. För att fylla i sträng samlings fält kan du Förbearbeta dina källdata som en JSON-sträng mat ris och sedan använda `jsonArrayToStringCollection` mappnings funktionen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>funktionen urlEncode

Den här funktionen kan användas för att koda en sträng så att den är "säker URL". När den används med en sträng som innehåller tecken som inte tillåts i en URL, kommer funktionen att konvertera dessa "osäkra" tecken till motsvarande tecken i entiteter. Den här funktionen använder UTF-8-encoding-formatet.

#### <a name="example---document-key-lookup"></a>Exempel – sökning efter dokument nyckel

`urlEncode` funktionen kan användas som ett alternativ till `base64Encode` funktionen, om bara URL-osäkra tecken ska konverteras, samtidigt som de behåller andra tecken.

Anta att Indatasträngen är-och `<hello>` sedan fylls mål fält av typ i `(Edm.String)` med värdet `%3chello%3e`

När du hämtar den kodade nyckeln vid sökning kan du använda `urlDecode` funktionen för att hämta det ursprungliga nyckelvärdet och använda det för att hämta käll dokumentet.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>funktionen urlDecode

 Den här funktionen konverterar en URL-kodad sträng till en avkodad sträng med UTF-8-kodat format.

 ### <a name="example---decode-blob-metadata"></a>Exempel – avkoda BLOB-metadata

 Vissa Azure Storage-klienter automatiskt URL-koda BLOB-metadata om det innehåller icke-ASCII-tecken. Men om du vill göra dessa metadata sökbara (som oformaterad text) kan du använda `urlDecode` funktionen för att omvandla kodade data tillbaka till vanliga strängar när du fyller i Sök indexet.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>funktionen fixedLengthEncode
 
 Den här funktionen konverterar en sträng med valfri längd till en sträng med fast längd.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Exempel – mappa dokument nycklar som är för långa
 
Om det uppstår fel som klagande om dokument nyckeln är längre än 1024 tecken kan den här funktionen användas för att minska längden på dokument nyckeln.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```