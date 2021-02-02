---
title: Sök över CSV-blobbar
titleSuffix: Azure Cognitive Search
description: Extrahera och importera CSV från Azure Blob Storage med delimitedText tolknings läge.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430487"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Så här indexerar du CSV-blobar med delimitedText tolknings läge och blob-indexerare i Azure Kognitiv sökning

Azure Kognitiv sökning [BLOB-indexeraren](search-howto-indexing-azure-blob-storage.md) innehåller ett `delimitedText` tolknings läge för CSV-filer som behandlar varje rad i CSV-filen som ett separat sökdokument. Till exempel kan följande kommaavgränsad text `delimitedText` resultera i två dokument i Sök indexet: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Utan `delimitedText` tolknings läget behandlas hela innehållet i CSV-filen som ett Sök dokument.

När du skapar flera sökdokument från en enda BLOB måste du läsa [Indexera blobbar för att skapa flera Sök dokument](search-howto-index-one-to-many-blobs.md) för att förstå hur dokument nyckel tilldelningar fungerar. BLOB-indexeraren kan hitta eller generera värden som unikt definierar varje nytt dokument. Mer specifikt kan den skapa en överföring `AzureSearch_DocumentKey` som genereras när en BLOB parsas till mindre delar där värdet sedan används som sökdokumentets nyckel i indexet.

## <a name="setting-up-csv-indexing"></a>Konfigurera CSV-indexering

Om du vill indexera CSV-blobbar skapar eller uppdaterar du en indexare-definition med `delimitedText` tolknings läget på en [skapa indexerare](/rest/api/searchservice/create-indexer) -begäran:

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` anger att den första (icke-tomma) raden i varje BLOB innehåller rubriker.
Om blobbar inte innehåller en inledande rubrik rad, ska rubrikerna anges i indexerings konfigurationen: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Du kan anpassa avgränsnings tecken med hjälp av `delimitedTextDelimiter` konfigurations inställningen. Exempel:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> För närvarande stöds endast UTF-8-kodning. Om du behöver stöd för andra kodningar kan du rösta på det på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> När du använder avgränsat text tolknings läge förutsätter Azure Kognitiv sökning att alla blobbar i data källan kommer att vara CSV. Om du behöver stöd för en blandning av CSV-och icke-CSV-blobbar i samma data källa, bör du rösta på den på [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
>

## <a name="request-examples"></a>Exempel på begäran

Vi lägger samman allt här är de fullständiga nytto Last exemplen. 

DataSource 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexer

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


