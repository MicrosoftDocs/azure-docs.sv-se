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
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509460"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Så här indexerar du oformaterad text blobbar i Azure Kognitiv sökning

När du använder en [BLOB-indexerare](search-howto-indexing-azure-blob-storage.md) för att extrahera sökbara BLOB-text för full texts ökning kan du tilldela ett tolknings läge för att få bättre indexerings resultat. Som standard parsar indexeraren BLOB-innehåll som ett enda text segment. Men om alla blobbar innehåller oformaterad text i samma kodning, kan du förbättra indexerings prestanda avsevärt genom att använda `text` tolknings läget.

Rekommendationer för användnings `text` parsning är:

+ Filtypen är. txt
+ Filerna är av vilken typ som helst, men själva innehållet är text (till exempel program käll kod, HTML, XML och så vidare). För filer på ett markerings språk kommer alla tecken som används som statisk text att komma in.

Kom ihåg att alla indexerare serialiseras till JSON. Som standard kommer innehållet i hela text filen att indexeras inom ett stort fält som `"content": "<file-contents>"` . Eventuella nya rad-och retur instruktioner är inbäddade i innehålls fältet och uttrycks som `\r\n\` .

Om du vill ha ett mer detaljerat resultat och om filtypen är kompatibel, bör du tänka på följande lösningar:

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