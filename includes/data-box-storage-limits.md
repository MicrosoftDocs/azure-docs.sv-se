---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: 0e1b70ba3f1359c7cf9c6633ab5e0e05c7b6e946
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027694"
---
I det här avsnittet beskrivs gränserna för Azure Storage tjänst och de namngivnings konventioner som krävs för Azure Files, Azure block-blobbar och Azure Page-blobbar, som är tillämpliga för Data Box-enhet-tjänsten. Granska lagrings gränserna noggrant och följ alla rekommendationer.

Den senaste informationen om begränsningar för Azure Storage-tjänsten och metod tips för namngivning av resurser, behållare och filer finns på:

- [Namnge och referera till behållare](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockera blobbar och Page BLOB-konventioner](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider Azure Storage-tjänstens gränser eller inte uppfyller Azure Files/BLOB-namngivnings konventionerna, matas dessa filer eller kataloger inte in i Azure Storage via Data Box-enhet-tjänsten.