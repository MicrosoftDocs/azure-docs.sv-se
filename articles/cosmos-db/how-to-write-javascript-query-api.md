---
title: Skriv lagrade procedurer och utlösare med Java Script-fråge-API i Azure Cosmos DB
description: Skriva lagrade procedurer och utlösare med hjälp fråge-API:et för Javascript i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 449af63ef3409b8c8173edef7a5d77074cca02b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335848"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Skriva lagrade procedurer och utlösare med hjälp fråge-API:et för Javascript i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Med Azure Cosmos DB kan du utföra optimerade frågor med hjälp av ett flytande JavaScript-gränssnitt utan att behöva kunna SQL-språk som kan användas för att skriva lagrade procedurer eller utlösare. Mer information om stöd för JavaScript fråge-API i Azure Cosmos DB finns i artikeln [arbeta med JavaScript-språkintegrerade fråge-API i Azure Cosmos DB](javascript-query-api.md).

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>Lagrad procedur med JavaScript fråge-API

Följande kodexempel är ett exempel på hur JavaScript-fråge-API används i kontexten för en lagrad procedur. Den lagrade proceduren infogar ett Azure Cosmos-objekt som anges av en indataparameter och uppdaterar ett Metadatadokumentet med hjälp av `__.filter()` metoden, med minSize, MaxSize och totalSize baserat på indatatypens egenskap storlek.

> [!NOTE]
> `__` (dubbelunderstreck) är ett alias till `getContext().getCollection()` när du använder frågan JavaScript API.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>Nästa steg

Se följande funktioner för att lära dig hur du definierar lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB:

* [Skriva lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Registrera och använda lagrade procedurer i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Registrera och använda [förutlösare](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) och [efterutlösare](how-to-use-stored-procedures-triggers-udfs.md#post-triggers) i Azure Cosmos DB

* [Registrera och använda användardefinierade funktioner i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Syntetiska partitionsnycklar i Azure Cosmos DB](synthetic-partition-keys.md)
