---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 86bf4911026e46c997469b956f9e7c75c4f17164
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698054"
---
#### <a name="additional-premium-file-share-level-limits"></a>Ytterligare begränsningar för Premium fil resurs nivå

|Område  |Mål  |
|---------|---------|
|Minsta storlek för ökning/minskning    |1 GiB      |
|Bas linje IOPS    |400 + 1 IOPS per GiB, upp till 100 000|
|IOPS-burst    |Max (4000, 3x IOPS per GiB), upp till 100 000|
|Utgående hastighet         |60 MiB/s + 0,06 * etablerad GiB        |
|Ingress-pris| 40 MiB/s + 0,04 * etablerad GiB |

#### <a name="file-level-limits"></a>Fil nivå begränsningar

|Område  |Standard fil  |Premium-fil  |
|---------|---------|---------|
|Storlek     |1 TiB         |4 TiB         |
|Max IOPS per fil      |1 000         |Upp till 8 000 *         |
|Samtidiga referenser     |2 000         |2 000         |
|Utgående     |Se standard fil data flödes värden         |300 MiB/SEK (upp till 1 GiB/s med SMB Multichannel-förhands granskning) * *         |
|Ingress     |Se standard fil data flödes värden         |200 MiB/SEK (upp till 1 GiB/s med SMB Multichannel-förhands granskning) * *        |
|Dataflöde     |Upp till 60 MiB/s         |Se ingångs-/utgående värden för Premium filen         |

\*<sup>Gäller för Läs-och skriv iOS (vanligt vis mindre IO-storlekar <= 64K). Metadata-åtgärder, förutom läsning och skrivningar, kan vara lägre. </sup>

\*\*<sup>Omfattas av gränser för dator nätverk, tillgänglig bandbredd, IO-storlek, ködjup och andra faktorer. Mer information finns i [SMB Multichannel-prestanda](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
