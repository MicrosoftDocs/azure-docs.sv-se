---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98225474"
---
Här följer gränserna för storleken på de data som kopieras till ett lagrings konto. Kontrol lera att de data som du överför följer dessa gränser. Den senaste informationen om dessa begränsningar finns i [skalbarhets-och prestanda mål för Blob Storage](../articles/storage/blobs/scalability-targets.md) och [Azure Files skalbarhet och prestanda mål](../articles/storage/files/storage-files-scale-targets.md).

| Storlek på data som kopieras till Azure Storage-kontot                      | Standardgräns          |
|---------------------------------------------------------------------|------------------------|
| Block Blob och Page BLOB                                            | Max gränsen är samma som den [lagrings gräns som definierats för Azure-prenumerationen](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) och innehåller data från alla källor, inklusive data Box-enhet.   |
| Azure Files                                                          | Data Box-enhet stöder stora fil resurser (100 TiB) om det är aktiverat innan Data Box-enhets ordningen skapas. <br> Om den här inställningen inte är aktive rad innan du skapar en order är den maximala storleken som stöds för fil resursen 5 <br> Data Box-enhet stöder Azure Premium-filresurser som ger totalt 100-TiB för alla resurser i lagrings kontot. <br> Den största användbara kapaciteten är något lägre på grund av det utrymme som kopierar loggar och gransknings loggar använder. Minst 100 GiB var reserverat för kopierings loggen och gransknings loggen. Mer information finns i [gransknings loggar för Azure Data Box Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md). <br> Alla mappar under *StorageAccount_AzureFiles* måste följa den här gränsen. <br> Mer information finns i [Aktivera och skapa stora fil resurser](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
