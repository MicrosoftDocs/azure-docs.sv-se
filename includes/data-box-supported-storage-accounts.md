---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706049"
---
Här är en lista över de lagrings konton och lagrings typer som stöds för en Data Box-enhet enhet. En fullständig lista över alla funktioner för alla typer av lagrings konton finns i [typer av lagrings konton](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

I följande tabell visas de lagrings konton som stöds för import order.

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB** _ |_ *Azure Files** |**Kommentarer**|
| --- | --- | -- | -- | -- |
| Klassisk standard | Y | Y | Y |
| Generell användning v1-standard  | Y | Y | Y | Både frekvent och låg frekvent stöds.|
| Generell användning v1 Premium  |  | Y| | |
| Generell användning v2 standard  | Y | Y | Y | Både frekvent och låg frekvent stöds.|
| Generell användning v2 Premium  |  |Y | | |
| Azure Premium-FileStorage |  |  | Y |  |  
| Blob Storage Standard |Y | | |Både frekvent och låg frekvent stöds. |

\**-Data som laddats upp till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.*

I följande tabell visas de lagrings konton som stöds för export order.

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB** _ |_ *Azure Files** |**Åtkomst nivåer som stöds**|
| --- | --- | -- | -- | -- |
| Klassisk standard | Y | Y | Y | |
| Generell användning v1-standard  | Y | Y | Y | Frekvent, låg frekvent|
| Generell användning v1 Premium  |  | Y| | |
| Generell användning v2 standard  | Y | Y | Y | Frekvent, låg frekvent|
| Generell användning v2 Premium  |  |Y | | |
| Azure Premium-FileStorage |  |  | Y |  |
| Blob Storage Standard |Y | | |Frekvent, låg frekvent |
| Blockera Blob Storage Premium |Y | | |Frekvent, låg frekvent |
| Page Blob Storage Premium | |Y | | |

> [!IMPORTANT]
> - För generella konton har Data Box-enhet inte stöd för kö-, tabell-och disk lagrings typer för import order. För export order stöder Data Box-enhet inte kö-, tabell-, disk-och Azure Data Lake gen 2-lagrings typer för allmänna-syfte-konton.
> - Data Box-enhet har inte stöd för att lägga till blobar för Blob Storage och blockera Blob Storage konton.
> - NFS (Network File System) 3,0 protokoll stöd i Azure Blob Storage stöds inte med Data Box-enhet.
> - Data som överförs till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.
> - Högst 80 TB kan exporteras.
> - Fil historik och blob-ögonblicksbilder exporteras inte.