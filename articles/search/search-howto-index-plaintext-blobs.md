---
title: Sök över oformaterad text blobbar
titleSuffix: Azure Cognitive Search
description: Konfigurera en Sök indexerare för att extrahera oformaterad text från Azure-blobbar för full texts ökning i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 422346430e32ccb8745d5a5d829c5d61089a99c6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430436"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Så här indexerar du oformaterad text blobbar i Azure Kognitiv sökning

När du använder en [BLOB-indexerare](search-howto-indexing-azure-blob-storage.md) för att extrahera sökbar text för full texts ökning kan du anropa olika tolknings lägen för att få bättre indexerings resultat. Som standard parsar indexeraren BLOB-innehåll som ett enda text segment. Men om alla blobbar innehåller oformaterad text i samma kodning, kan du förbättra indexerings prestanda avsevärt genom att använda `text` tolknings läget.

Du bör använda `text` tolknings läget när:

+ Filtypen är. txt
+ Filerna är av vilken typ som helst, men själva innehållet är text (till exempel program käll kod, HTML, XML och så vidare). För filer på ett markerings språk kommer alla tecken som används som statisk text att komma in.

Kom ihåg att indexerare serialiseras till JSON. Innehållet i hela text filen kommer att indexeras inom ett enda stort fält som `"content": "<file-contents>"` . Nya rad-och retur instruktioner uttrycks som `\r\n\` .

Överväg följande lösningar om du vill ha ett mer detaljerat resultat:

+ [`delimitedText`](search-howto-index-csv-blobs.md) tolknings läge, om källan är CSV
+ [ `jsonArray` eller `jsonLines` ](search-howto-index-json-blobs.md), om källan är JSON

Ett tredje alternativ för att dela upp innehåll i flera delar kräver avancerade funktioner i form av [AI-anrikning](cognitive-search-concept-intro.md). Den lägger till analyser som identifierar och tilldelar fil segment till olika Sök fält. Du kan hitta en fullständig eller partiell lösning med hjälp av [inbyggda kunskaper](cognitive-search-predefined-skills.md), men en mer sannolik lösning skulle vara inlärnings modell som förstår ditt innehåll, som ledas i en anpassad inlärnings modell, omsluts i en [anpassad färdighet](cognitive-search-custom-skill-interface.md).

## <a name="set-up-plain-text-indexing"></a>Konfigurera oformaterad text indexering

Om du vill indexera oformaterad text blobbar skapar eller uppdaterar du en indexare-definition med `parsingMode` konfigurations egenskapen till `text` på en [skapa indexerare](/rest/api/searchservice/create-indexer) -begäran:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

Som standard `UTF-8` antas kodningen. Om du vill ange en annan kodning använder du `encoding` konfigurations egenskapen: 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Exempel på begäran

Tolknings lägen anges i index definitions definitionen.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Nästa steg

+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Så här konfigurerar du en BLOB-indexerare](search-howto-indexing-azure-blob-storage.md)
+ [Översikt över BLOB-indexering](search-blob-storage-integration.md)