---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 87c28b906b1f810857fee86ba8a94a0834e1de68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026207"
---
I det här avsnittet beskrivs begränsningarna för Azure Storage tjänst och de namngivnings konventioner som krävs för Azure Files, Azure block-blobbar och Azure Page-blobar, enligt vad som är tillämpligt för Azure Stack Edge/Data Box Gateway-tjänsten. Granska lagrings gränserna noggrant och följ alla rekommendationer.

Den senaste informationen om begränsningar för Azure Storage-tjänsten och metod tips för namngivning av resurser, behållare och filer finns på:

- [Namnge och referera till behållare](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockera blobbar och Page BLOB-konventioner](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider Azure Storage-tjänstens gränser eller inte uppfyller namngivningskonventionerna för Azure Files/blobar så är inte de här filerna eller katalogerna inmatade i Azure Storage via Azure Stack Edge/Data Box Gateway-tjänsten.