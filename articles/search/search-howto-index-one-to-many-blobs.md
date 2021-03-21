---
title: Indexera blobbar som innehåller flera dokument
titleSuffix: Azure Cognitive Search
description: Crawla Azure-blobbar för text innehåll med hjälp av Azure Kognitiv sökning BLOB-indexeraren, där varje BLOB kan ge ett eller flera Sök index dokument.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: ea22b3cff8a0303c4e6698db4090df0f5ed2153a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430988"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>Indexera blobbar för att skapa flera Sök dokument

Som standard behandlar en BLOB-indexeraren innehållet i en blob som ett enda Sök dokument. Om du vill ha en mer detaljerad representation av blobben i ett Sök index kan du ange **parsingMode** -värden för att skapa flera Sök dokument från en blob. **ParsingMode** -värden som resulterar i många Sök dokument inkluderar `delimitedText` (för [CSV](search-howto-index-csv-blobs.md)) och `jsonArray` eller `jsonLines` (för [JSON](search-howto-index-json-blobs.md)).

När du använder något av dessa tolknings lägen måste de nya Sök dokumenten som visas ha unika dokument nycklar, och ett problem uppstår när du ska bestämma var värdet kommer från. Den överordnade blobben har minst ett unikt värde i form av `metadata_storage_path property` , men om det bidrar till värdet till fler än ett sökdokument är nyckeln inte längre unik i indexet.

För att lösa det här problemet genererar BLOB-indexeraren en `AzureSearch_DocumentKey` som unikt identifierar varje underordnat sökdokument som skapats från den enskilda BLOB-överordnad. I den här artikeln förklaras hur den här funktionen fungerar.

## <a name="one-to-many-document-key"></a>En-till-många-dokument nyckel

Varje dokument som visas i ett Azure Kognitiv sökning-index identifieras unikt av en dokument nyckel. 

När inget tolknings läge har angetts, och om det inte finns någon [explicit fält mappning](search-indexer-field-mappings.md) i indexerings definitionen för Sök dokument nyckeln, mappas BLOB-indexeraren automatiskt `metadata_storage_path property` som dokument nyckel. Den här mappningen säkerställer att varje BLOB visas som ett distinkt sökdokument, och det sparar dig för att du ska kunna skapa fält mappningen själv (normalt mappas endast fält med identiska namn och typer automatiskt).

När du använder något av de tolknings lägen som anges ovan mappar en blob till "många" sökdokument, vilket gör att en dokument nyckel endast baseras på BLOB-metadata som är olämpliga. För att lösa den här begränsningen kan Azure Kognitiv sökning generera en "en-till-många"-dokument nyckel för varje enskild enhet som extraheras från en blob. Den här egenskapen heter AzureSearch_DocumentKey och läggs till i varje enskild enhet som extraheras från blobben. Värdet för den här egenskapen är unikt för varje enskild entitet över blobbar och entiteterna visas som separata Sök dokument.

När inga explicita fält mappningar för nyckel index fältet har angetts `AzureSearch_DocumentKey` mappas som standard till den med hjälp av `base64Encode` fält mappnings funktionen.

## <a name="example"></a>Exempel

Anta att du har en index definition med följande fält:

+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Och blob-behållaren har blobbar med följande struktur:

_Blob1.jspå_

```json
{ "temperature": 100, "pressure": 100, "timestamp": "2020-02-13T00:00:00Z" }
{ "temperature" : 33, "pressure" : 30, "timestamp": "2020-02-14T00:00:00Z" }
```

_Blob2.jspå_

```json
{ "temperature": 1, "pressure": 1, "timestamp": "2019-01-12T00:00:00Z" }
{ "temperature" : 120, "pressure" : 3, "timestamp": "2017-05-11T00:00:00Z" }
```

När du skapar en indexerare och anger **parsingMode** till `jsonLines` -utan att ange några explicita fält mappningar för nyckel fältet, kommer följande mappning att tillämpas implicit.

```http
{
    "sourceFieldName" : "AzureSearch_DocumentKey",
    "targetFieldName": "id",
    "mappingFunction": { "name" : "base64Encode" }
}
```

Den här installationen leder till disambiguated-dokument nycklar, som liknar följande bild (Base64-kodat ID förkortat för det kortfattat).

| ID | temperatur | tryck | timestamp |
|----|-------------|----------|-----------|
| aHR0 ... YjEuanNvbjsx | 100 | 100 | 2020-02-13T00:00:00Z |
| aHR0 ... YjEuanNvbjsy | 33 | 30 | 2020-02-14T00:00:00Z |
| aHR0 ... YjIuanNvbjsx | 1 | 1 | 2019-01-12T00:00:00Z |
| aHR0 ... YjIuanNvbjsy | 120 | 3 | 2017-05-11T00:00:00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>Anpassad fält mappning för index nyckel fält

Om du antar samma index definition som i föregående exempel, antar det att BLOB-behållaren har blobbar med följande struktur:

_Blob1.jspå_

```json
recordid, temperature, pressure, timestamp
1, 100, 100,"2019-02-13T00:00:00Z" 
2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.jspå_

```json
recordid, temperature, pressure, timestamp
1, 1, 1,"2018-01-12T00:00:00Z" 
2, 120, 3,"2013-05-11T00:00:00Z" 
```

När du skapar en indexerare med `delimitedText` **parsingMode** kan det vara naturligt att skapa en fält mappnings funktion till nyckel fältet enligt följande:

```http
{
    "sourceFieldName" : "recordid",
    "targetFieldName": "id"
}
```

Den här mappningen kommer dock _inte_ att resultera i 4 dokument som visas i indexet, eftersom `recordid` fältet inte är unikt i _blobbar_. Därför rekommenderar vi att du använder den implicita fält mappning som används från `AzureSearch_DocumentKey` egenskapen till nyckel index fältet för "1-till-många"-tolknings lägen.

Om du vill skapa en explicit fält mappning kontrollerar du att _sourceField_ är distinkt för varje enskild entitet **i alla blobbar**.

> [!NOTE]
> Den metod som används av `AzureSearch_DocumentKey` för att säkerställa unikhet per extraherad entitet kan ändras och därför bör du inte förlita dig på dess värde för programmets behov.

## <a name="next-steps"></a>Nästa steg

Om du inte redan är bekant med den grundläggande strukturen och arbets flödet för BLOB-indexering bör du granska [indexeringen av azure Blob Storage med azure kognitiv sökning](search-howto-index-json-blobs.md) först. Läs följande artiklar om du vill ha mer information om att parsa lägen för olika typer av BLOB-innehåll.

> [!div class="nextstepaction"]
> [INDEXERA CSV-blobbar](search-howto-index-csv-blobs.md) 
>  [INDEXERA JSON-blobbar](search-howto-index-json-blobs.md)
