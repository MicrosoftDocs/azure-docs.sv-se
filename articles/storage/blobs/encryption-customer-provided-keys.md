---
title: Ange en krypterings nyckel för en begäran till Blob Storage
titleSuffix: Azure Storage
description: Klienter som begär förfrågningar mot Azure Blob Storage har möjlighet att tillhandahålla en krypterings nyckel per begäran. Inklusive krypterings nyckeln på begäran ger detaljerad kontroll över krypterings inställningarna för Blob Storage-åtgärder.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97694704"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Ange en krypterings nyckel för en begäran till Blob Storage

Klienter som begär förfrågningar mot Azure Blob Storage har möjlighet att tillhandahålla en AES-256-krypterings nyckel per begäran. Inklusive krypterings nyckeln på begäran ger detaljerad kontroll över krypterings inställningarna för Blob Storage-åtgärder. Kundspecifika nycklar kan lagras i Azure Key Vault eller i en annan nyckel lagrings plats.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Kryptera Läs-och skriv åtgärder

När ett klient program tillhandahåller en krypterings nyckel på begäran utför Azure Storage kryptering och dekryptering transparent vid läsning och skrivning av BLOB-data. Azure Storage skriver en SHA-256-hash av krypterings nyckeln bredvid blobens innehåll. Hashen används för att kontrol lera att alla efterföljande åtgärder mot blobben använder samma krypterings nyckel.

Azure Storage lagrar eller hanterar inte den krypterings nyckel som klienten skickar med begäran. Nyckeln tas bort säkert så snart krypterings- eller dekrypteringsprocessen har slutförts.

När en klient skapar eller uppdaterar en blob med hjälp av en kundanged nyckel på begäran, måste efterföljande Läs-och skriv förfrågningar för denna BLOB också tillhandahålla nyckeln. Om nyckeln inte anges på en begäran för en blob som redan har krypterats med en kundanged nyckel, Miss lyckas begäran med felkod 409 (konflikt).

Om klient programmet skickar en krypterings nyckel på begäran och lagrings kontot också krypteras med hjälp av en Microsoft-hanterad nyckel eller en kundhanterad nyckel, använder Azure Storage nyckeln som anges i begäran om kryptering och dekryptering.

För att skicka krypterings nyckeln som en del av begäran måste en klient upprätta en säker anslutning till Azure Storage med hjälp av HTTPS.

Varje Blob-ögonblicksbild kan ha en egen krypterings nyckel.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Begärandehuvuden för att ange kundtillhandahållna nycklar

För REST-anrop kan klienterna använda följande rubriker för att på ett säkert sätt skicka krypterings nyckel information till en begäran till Blob Storage:

|Begärandehuvud | Beskrivning |
|---------------|-------------|
|`x-ms-encryption-key` |Krävs för både Skriv-och Läs begär Anden. Ett base64-kodat AES-256-krypterings nyckel värde. |
|`x-ms-encryption-key-sha256`| Krävs för både Skriv-och Läs begär Anden. Den base64-kodade SHA256 för krypterings nyckeln. |
|`x-ms-encryption-algorithm` | Krävs för Skriv begär Anden, valfritt för Läs begär Anden. Anger algoritmen som ska användas vid kryptering av data med den aktuella nyckeln.  Värdet för den här rubriken måste vara `AES256` . |

Det är valfritt att ange krypterings nycklar för begäran. Men om du anger ett av de sidhuvuden som anges ovan för en Skriv åtgärd måste du ange alla.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Blob Storage-åtgärder som stöder kundspecifika nycklar

Följande Blob Storage-åtgärder stöder sändning av kundtillhandahållna krypterings nycklar på en begäran:

- [Placera blob](/rest/api/storageservices/put-blob)
- [Lista över blockerade](/rest/api/storageservices/put-block-list)
- [Spärra block](/rest/api/storageservices/put-block)
- [Skicka block från URL](/rest/api/storageservices/put-block-from-url)
- [Placerings sida](/rest/api/storageservices/put-page)
- [Lägg till sida från URL](/rest/api/storageservices/put-page-from-url)
- [Lägg till block](/rest/api/storageservices/append-block)
- [Ange BLOB-egenskaper](/rest/api/storageservices/set-blob-properties)
- [Ange BLOB-metadata](/rest/api/storageservices/set-blob-metadata)
- [Hämta BLOB](/rest/api/storageservices/get-blob)
- [Hämta blobegenskaper](/rest/api/storageservices/get-blob-properties)
- [Hämta BLOB-metadata](/rest/api/storageservices/get-blob-metadata)
- [Ta ögonblicksbild av blob](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Rotera kundtillhandahållna nycklar

Om du vill rotera en krypterings nyckel som användes för att kryptera en BLOB, laddar du ned blobben och laddar sedan upp den igen med den nya krypterings nyckeln.

> [!IMPORTANT]
> Azure Portal kan inte användas för att läsa från eller skriva till en behållare eller BLOB som är krypterad med en nyckel som anges i begäran.
>
> Se till att skydda krypterings nyckeln som du anger på en begäran till Blob Storage i en säker nyckel lagring som Azure Key Vault. Om du försöker utföra en Skriv åtgärd på en behållare eller BLOB utan krypterings nyckeln, Miss lyckas åtgärden och du kommer att förlora åtkomsten till objektet.

## <a name="next-steps"></a>Nästa steg

- [Ange en kundspecifik nyckel på en begäran till Blob Storage med .NET](storage-blob-customer-provided-key.md)
- [Azure Storage-kryptering av vilande data](../common/storage-service-encryption.md)
