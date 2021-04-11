---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/01/2021
ms.author: tamram
ms.openlocfilehash: 16da73fe453760e2dc84e7d683c3a16c12b8a06f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218623"
---
| Resurs | Mål |
|-|-|
| Maximal storlek för enskild BLOB-behållare | Samma som högsta kapacitet för lagrings konto |
| Maximalt antal block i en Block-Blob eller Lägg till BLOB | 50 000 block |
| Maximal storlek för ett block i en Block-Blob | 4000 MiB |
| Maximal storlek för en Block-Blob | 50 000 X 4000 MiB (cirka 190,7 TiB) |
| Maximal storlek för ett block i en tilläggs-BLOB | 4 MiB |
| Maximal storlek för en tilläggs-BLOB | 50 000 x 4 MiB (cirka 195 GiB) |
| Maximal storlek på en sid-BLOB | 8 TiB<sup>2</sup> |
| Maximalt antal lagrade åtkomst principer per BLOB-behållare | 5 |
| Mål begär ande frekvens för en enskild BLOB | Upp till 500 begär Anden per sekund |
| Mål data flöde för en enda sid-BLOB | Upp till 60 MiB per sekund<sup>2</sup> |
| Mål data flöde för en enda Block-Blob | Upp till lagrings kontots inkommande/utgående gränser<sup>1</sup> |

<sup>1</sup> data flöde för en enskild BLOB är beroende av flera faktorer, inklusive, men inte begränsat till: samtidighet, begär ande storlek, prestanda nivå, hastighet på källa för uppladdningar och mål för hämtningar. Om du vill dra nytta av prestanda förbättringarna för [block blobbar med höga data flöden](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)laddar du upp större blobbar eller block. Mer specifikt kan du anropa åtgärden för att [parkera](/rest/api/storageservices/put-blob) eller [blockera](/rest/api/storageservices/put-block) med en BLOB-eller block storlek som är större än 4 MIB för standard lagrings konton. För Premium Block-Blob eller för Data Lake Storage Gen2 lagrings konton använder du en block-eller BLOB-storlek som är större än 256 KiB.

<sup>2</sup> Page blobbar stöds ännu inte i konton som har inställningen **hierarkiskt namn område** .

I följande tabell beskrivs de största block-och blob-storlekarna som tillåts av tjänst versionen.

| Tjänst version | Maximal block storlek (via skicka block) | Maximal BLOB-storlek (via lista över blockerade block) | Maximal BLOB-storlek via enskild Skriv åtgärd (via skicka BLOB) |
|-|-|-|-|
| Version 2019-12-12 och senare | 4000 MiB | Cirka 190,7 TiB (4000 MiB X 50 000 Blocks) | 5000 MiB (för hands version) |
| Version 2016-05-31 till och med version 2019-07-07 | 100 MiB | Cirka 4,75 TiB (100 MiB X 50 000 Blocks) | 256 MiB |
| Versioner före 2016-05-31 | 4 MiB | Cirka 195 GiB (4 MiB X 50 000 block) | 64 MiB |
