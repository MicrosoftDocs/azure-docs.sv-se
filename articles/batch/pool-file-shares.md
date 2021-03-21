---
title: Azure-filresurs för Azure Batch pooler
description: Montera en Azure Files resurs från datornoder i en Linux-eller Windows-pool i Azure Batch.
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: e5682e7ba853973592c3a650a06ce72615cec7b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735502"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Använda en Azure-filresurs med en batch-pool

[Azure Files](../storage/files/storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via SMB-protokollet (Server Message Block). Den här artikeln innehåller information och kod exempel för montering och användning av en Azure-filresurs på poolens datornoder.

## <a name="considerations-for-use-with-batch"></a>Att tänka på när du använder batch

* Överväg att använda en Azure-filresurs när du har pooler som kör ett relativt lågt antal parallella uppgifter om du använder icke-Premium-Azure Files. Granska [prestanda-och skalnings målen](../storage/files/storage-files-scale-targets.md) för att avgöra om Azure Files (som använder ett Azure Storage konto) ska användas, baserat på den förväntade storleken på poolen och antalet till gångs filer. 

* Azure-filresurser är [kostnads effektiva](https://azure.microsoft.com/pricing/details/storage/files/) och kan konfigureras med datareplikering till en annan region så att de är globalt redundanta. 

* Du kan montera en Azure-filresurs samtidigt från en lokal dator. Men se till att du förstår [samtidighets konsekvenser](../storage/blobs/concurrency-manage.md) särskilt när du använder REST-API: er.

* Se även de allmänna [planerings övervägandena](../storage/files/storage-files-planning.md) för Azure-filresurser.


## <a name="create-a-file-share"></a>Skapa en filresurs

[Skapa en fil resurs](../storage/files/storage-how-to-create-file-share.md) i ett lagrings konto som är länkat till ditt batch-konto eller ett separat lagrings konto.

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Montera en Azure-filresurs i en batch-pool

Läs dokumentationen om hur du [monterar ett virtuellt fil system i en batch-pool](virtual-file-mount.md).

## <a name="next-steps"></a>Nästa steg

* Andra alternativ för att läsa och skriva data i batch finns i [Spara jobb-och Uppgiftsutdata](batch-task-output.md).
* Se även [batch Shipyard](https://github.com/Azure/batch-shipyard) Toolkit, som innehåller [Shipyard-recept](https://github.com/Azure/batch-shipyard/tree/master/recipes) för att distribuera fil system för arbets belastningar för batch-behållare.