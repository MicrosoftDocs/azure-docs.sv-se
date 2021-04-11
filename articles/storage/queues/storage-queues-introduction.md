---
title: Introduktion till Azure Queue Storage – Azure Storage
description: Se en introduktion till Azure Queue Storage, en tjänst för att lagra ett stort antal meddelanden. En Queue Storage tjänst innehåller ett URL-format, lagrings konto, kö och meddelande.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 03/18/2020
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 32ce5af5371047ae814602e9118f622ee036bd9c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276220"
---
# <a name="what-is-azure-queue-storage"></a>Vad är Azure Queue Storage?

Azure Queue Storage är en tjänst för lagring av ett stort antal meddelanden. Du kommer åt meddelanden från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Ett Queue-meddelande kan vara upp till 64 KB stort. En kö kan innehålla miljon tals meddelanden, upp till den totala kapacitets gränsen för ett lagrings konto. Köer används ofta för att skapa en efter släpning av arbete som ska bearbetas asynkront.

## <a name="queue-storage-concepts"></a>Queue Storage begrepp

Queue Storage innehåller följande komponenter:

![Diagram över relationen mellan ett lagrings konto, köer och meddelanden.](./media/storage-queues-introduction/queue1.png)

- **URL-format:** köer är adresserbara via följande URL-format:

  `https://<storage account>.queue.core.windows.net/<queue>`

  Följande URL adresserar en kö i diagrammet:

  `https://myaccount.queue.core.windows.net/images-to-download`

- **Lagrings konto:** All åtkomst till Azure Storage görs via ett lagrings konto. Information om kapacitet för lagrings konton finns i [skalbarhets-och prestanda mål för standard lagrings konton](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

- **Kö:** en kö innehåller en uppsättning meddelanden. Könamnet **får** bara innehålla gemener. Information om hur du namnger köer finns i [namnge köer och metadata](/rest/api/storageservices/naming-queues-and-metadata).

- **Meddelande:** ett meddelande i valfritt format, som är upp till 64 KB. Före version 2017-07-29 är den högsta tillåtna tiden till Live sju dagar. För version 2017-07-29 eller senare kan den maximala tiden till Live vara ett positivt tal eller-1 som anger att meddelandet inte upphör att gälla. Om den här parametern utelämnas, är standardvärdet för Time-to-Live sju dagar.

## <a name="next-steps"></a>Nästa steg

- [Skapa ett lagringskonto](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Kom igång med Queue Storage med .NET](storage-dotnet-how-to-use-queues.md)
- [Kom igång med Queue Storage med Java](storage-java-how-to-use-queue-storage.md)
- [Kom igång med Queue Storage med python](storage-python-how-to-use-queue-storage.md)
- [Kom igång med Queue Storage med Node.js](storage-nodejs-how-to-use-queues.md)
