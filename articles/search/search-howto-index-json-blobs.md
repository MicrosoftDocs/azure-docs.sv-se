---
title: Sök över JSON-blobbar
titleSuffix: Azure Cognitive Search
description: Crawla Azure JSON-blobbar för text innehåll med hjälp av Azure Kognitiv sökning BLOB-indexeraren. Indexerare automatiserar data inmatning för valda data källor som Azure Blob Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259058"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Så här indexerar du JSON-blobbar med en BLOB-indexerare i Azure Kognitiv sökning

Den här artikeln visar hur du [konfigurerar en BLOB-indexerare](search-howto-indexing-azure-blob-storage.md) för blobbar som består av JSON-dokument. JSON-blobar i Azure Blob Storage förutsätter vanligt vis något av följande formulär:

+ Ett enda JSON-dokument
+ Ett JSON-dokument som innehåller en matris med välformulerade JSON-element
+ Ett JSON-dokument som innehåller flera entiteter, avgränsade med en ny rad

BLOB-indexeraren innehåller en **`parsingMode`** parameter för att optimera utdata från Sök dokumentet baserat på strukturernas tolknings lägen består av följande alternativ:

| parsingMode | JSON-dokument | Beskrivning |
|--------------|-------------|--------------|
| **`json`** | En per BLOB | objekt Parsar JSON-blobbar som ett enda text segment. Varje JSON-BLOB blir ett enda Sök dokument. |
| **`jsonArray`** | Flera per BLOB | Parsar en JSON-matris i blobben där varje element i matrisen blir ett separat sökdokument.  |
| **`jsonLines`** | Flera per BLOB | Tolkar en blob som innehåller flera JSON-entiteter (även en matris) med enskilda element åtskilda av en ny rad. Indexeraren startar ett nytt Sök dokument efter varje ny rad. |

För både **`jsonArray`** och **`jsonLines`** , bör du granska [indexeringen av en BLOB för att skapa många Sök dokument](search-howto-index-one-to-many-blobs.md) för att förstå hur BLOB-indexeraren hanterar avvisning av dokument nyckeln för flera Sök dokument som har skapats från samma blob.

Inom index definitions definitionen kan du välja att ange [fält mappningar](search-indexer-field-mappings.md) för att välja vilka egenskaper för käll-JSON-dokumentet som används för att fylla i mål Sök indexet. Om du till exempel använder **`jsonArray`** tolknings läget, om matrisen finns som en lågnivå egenskap, kan du ange en **`document root`** egenskap som anger var matrisen placeras i blobben.

I följande avsnitt beskrivs varje läge i detalj. Om du inte känner till indexerings klienter och-koncept kan du läsa [skapa en Sök indexerare](search-howto-create-indexers.md). Du bör också känna till informationen om en [grundläggande BLOB-indexerare](search-howto-indexing-azure-blob-storage.md), som inte upprepas här.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Indexera enskilda JSON-dokument (ett per BLOB)

Som standard parsar BLOB-blobar JSON-blobbar som ett enda text segment, ett Sök dokument för varje BLOB i en behållare. Om JSON är strukturerad kan sökdokumentet avspegla den strukturen, med enskilda element som visas som enskilda fält. Anta till exempel att du har följande JSON-dokument i Azure Blob Storage:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

BLOB-indexeraren parsar JSON-dokumentet i ett enda sökdokument, läser in ett index genom att matcha "text", "datePublished" och "Taggar" från källan till identiskt namngivna och angivna mål index fält. Med ett index med fälten "text", "datePublished" och "Taggar" kan BLOB-indexeraren härleda rätt mappning utan att det finns någon fält mappning i begäran.

Även om standard beteendet är ett Sök dokument per JSON-BLOB, ändrar "JSON"-tolknings läget ändringar av interna fält mappningar för innehåll, och höjer fält inuti `content` till faktiska fält i sökindexet. En exempel Indexer definition för **`json`** tolknings läget kan se ut så här:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Som med alla indexerare, om fält inte är helt identiska, bör du förvänta dig att uttryckligen ange enskilda [fält mappningar](search-indexer-field-mappings.md) om du inte använder de implicita fält mappningarna som är tillgängliga för BLOB-innehåll och metadata, enligt beskrivningen i [grundläggande BLOB-indexeraren](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>JSON-exempel (enstaka hotell-JSON-filer)

[Data uppsättningen hotell-JSON-dokument](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) på GitHub är användbar för att testa JSON-parsning, där varje BLOB representerar en strukturerad JSON-fil. Du kan ladda upp datafilerna till Blob Storage och använda guiden **Importera data** för att snabbt utvärdera hur innehållet tolkas i enskilda Sök dokument. 

Data uppsättningen består av fem blobbar, som var och en innehåller ett hotell dokument med en adress samling och en rums samling. BLOB-indexeraren identifierar båda samlingarna och återspeglar strukturen i insamlings dokumenten i index schemat.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Parsa JSON-matriser

Du kan också använda alternativet JSON-matris. Det här alternativet är användbart när blobbar innehåller en matris med välformulerade JSON-objekt, och du vill att varje element ska bli ett separat sökdokument. Med hjälp av **`jsonArrays`** följande JSON-BLOB skapas tre separata dokument, vart `"id"` och ett med `"text"` fälten och.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

**`parameters`** Egenskapen på indexeraren innehåller tolknings läges värden. För en JSON-matris bör index definitions definitionen se ut ungefär som i följande exempel.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>exempel på jsonArrays (kliniska prövnings exempel data)

De [kliniska testerna JSON-data uppsättning](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) på GitHub är användbara för att testa JSON-matrisen. Du kan ladda upp datafilerna till Blob Storage och använda guiden **Importera data** för att snabbt utvärdera hur innehållet tolkas i enskilda Sök dokument. 

Data uppsättningen består av åtta blobbar, som var och en innehåller en JSON-matris med entiteter, för totalt 100 entiteter. Entiteterna varierar beroende på vilka fält som är ifyllda, men slut resultatet är ett Sök dokument per entitet, från alla matriser, i alla blobbar.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Parsar kapslade JSON-matriser

För JSON-matriser med kapslade element kan du ange en **`documentRoot`** för att ange en struktur på flera nivåer. Om dina blobbar till exempel ser ut så här:

```http
{
    "level1" : {
        "level2" : [
            { "id" : "1", "text" : "Use the documentRoot property" },
            { "id" : "2", "text" : "to pluck the array you want to index" },
            { "id" : "3", "text" : "even if it's nested inside the document" }  
        ]
    }
}
```

Använd den här konfigurationen för att indexera matrisen som finns i `level2` egenskapen:

```http
{
    "name" : "my-json-array-indexer",
    ... other indexer properties
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
}
```

## <a name="parse-json-entities-separated-by-newlines"></a>Parsa JSON-entiteter avgränsade med newlines

Om blobben innehåller flera JSON-enheter avgränsade med en ny rad, och du vill att varje element ska bli ett separat sökdokument, använder du **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

För JSON-linjer bör index indefinitionen se ut ungefär som i följande exempel.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines-exempel (caselaw exempel data)

[CASELAW JSON-datauppsättningen](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) på GitHub är användbart för att testa JSON New Line-parsning. Precis som med andra exempel kan du överföra dessa data till Blob Storage och använda guiden **Importera data** för att snabbt utvärdera effekten av tolknings läge på enskilda blobbar.

Data uppsättningen består av en blob som innehåller 10 JSON-entiteter åtskilda med en ny rad, där varje entitet beskriver ett enda juridiskt fall. Slut resultatet är ett Sök dokument per entitet.

## <a name="map-json-fields-to-search-fields"></a>Mappa JSON-fält till sökfält

Fält mappningar används för att koppla ett käll fält till ett målfält i situationer där fält namn och typer inte är identiska. Fält mappningar kan också användas för att matcha delar av ett JSON-dokument och "lyft" dem till toppnivå fält i Sök dokumentet.

I följande exempel visas det här scenariot. Mer information om fält mappningar i allmänhet finns i [fält mappningar](search-indexer-field-mappings.md).

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2016-04-13"
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

Anta ett sökindex med följande fält: `text` typ, typ `Edm.String` `date` `Edm.DateTimeOffset` och `tags` typ `Collection(Edm.String)` . Observera skillnaden mellan "datePublished" i källa och `date` fält i indexet. Använd följande fält mappningar för att mappa JSON till önskad form:

```http
"fieldMappings" : [
    { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
    { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
    { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
    ]
```

Käll fält anges med JSON- [pekarens](https://tools.ietf.org/html/rfc6901) format. Du börjar med ett snedstreck för att referera till roten i JSON-dokumentet och sedan väljer du önskad egenskap (på valfri kapslings nivå) med hjälp av en snedstreck-separerad sökväg.

Du kan också referera till enskilda mat ris element genom att använda ett nollbaserat index. Om du till exempel vill välja det första elementet i matrisen "Taggar" från exemplet ovan använder du en fält mappning som detta:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Om **`sourceFieldName`** refererar till en egenskap som inte finns i JSON-blobben, hoppas mappningen över utan fel. Med det här beteendet kan indexering fortsätta för JSON-blobbar som har ett annat schema (som är ett vanligt användnings fall). Eftersom det inte finns någon validerings kontroll, kontrol lera mappningarna noggrant för att göra att du inte förlorar dokumenten på fel sätt.
>

## <a name="next-steps"></a>Nästa steg

+ [Konfigurera BLOB-indexerare](search-howto-indexing-azure-blob-storage.md)
+ [Definiera fält mappningar](search-indexer-field-mappings.md)
+ [Översikt över indexerare](search-indexer-overview.md)
+ [Så här indexerar du CSV-blobar med en BLOB-indexerare](search-howto-index-csv-blobs.md)
+ [Självstudie: söka i halv strukturerade data från Azure Blob Storage](search-semi-structured-data.md)