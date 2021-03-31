---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187741"
---
| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal autentiseringsuppgifter för lagrings konto |64 | |
| Maximalt antal volym behållare |64 | |
| Maximalt antal volymer |255 | |
| Mall för maximalt antal scheman per bandbredd |168 |Ett schema för varje timme, varje dag i veckan. |
| Maximal storlek på en nivå volym på fysiska enheter |64 TB för StorSimple 8100 och StorSimple 8600 |StorSimple 8100 och StorSimple 8600 är fysiska enheter. |
| Maximal storlek på en nivå volym på virtuella enheter i Azure |30 TB för StorSimple 8010 <br></br> 64 TB för StorSimple 8020 |StorSimple 8010 och StorSimple 8020 är virtuella enheter i Azure som använder standard lagring respektive Premium lagring. |
| Maximal storlek för en lokalt fäst volym på fysiska enheter |9 TB för StorSimple 8100 <br></br> 24 TB för StorSimple 8600 |StorSimple 8100 och StorSimple 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maximalt antal åtkomst kontroll poster per enhet |64 | |
| Maximalt antal volymer per säkerhets kopierings princip |24 | |
| Maximalt antal säkerhets kopior som kvarhålls per säkerhets kopierings princip |64 | |
| Maximalt antal scheman per säkerhets kopierings princip |10 | |
| Maximalt antal ögonblicks bilder av alla typer som kan kvarhållas per volym |256 |Den här mängden inkluderar lokala ögonblicks bilder och moln ögonblicks bilder. |
| Maximalt antal ögonblicks bilder som kan finnas i vilken enhet som helst |10 000 | |
| Maximalt antal volymer som kan bearbetas parallellt för säkerhets kopiering, återställning eller kloning |16 |<ul><li>Om det finns fler än 16 volymer bearbetas de sekventiellt allteftersom bearbetnings platserna blir tillgängliga.</li><li>Nya säkerhets kopior av en klonad eller återställd nivå volym kan inte inträffa förrän åtgärden har slutförts. För en lokal volym är säkerhets kopior tillåtna efter att volymen är online.</li></ul> |
| Återställa och klona återställnings tid för volymer på nivå |<2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter en Restore-eller klonings åtgärd, oavsett volym storleken.</li><li>Volym prestandan kan inlednings vis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på storleken på den allokerade volymen. Metadata överförs automatiskt till enheten i bakgrunden med en hastighet på 5 minuter per TB allokerade volym data. Den här hastigheten kan påverkas av Internet bandbredden till molnet.</li><li>Restore-eller klonings åtgärden slutförs när alla metadata finns på enheten.</li><li>Säkerhets kopierings åtgärder kan inte utföras förrän återställnings-eller klonings åtgärden har slutförts fullständigt. |
| Återställa återställnings tid för lokalt fästa volymer |<2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter från återställnings åtgärden, oavsett volymens storlek.</li><li>Volym prestandan kan inlednings vis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på storleken på den allokerade volymen. Metadata överförs automatiskt till enheten i bakgrunden med en hastighet på 5 minuter per TB allokerade volym data. Den här hastigheten kan påverkas av Internet bandbredden till molnet.</li><li>Till skillnad från nivå volymer, om det finns lokalt fästa volymer, laddas även volym data ned lokalt på enheten. Återställningen slutförs när alla volym data har hämtats till enheten.</li><li>Återställnings åtgärderna kan vara långa och den totala tiden för att slutföra återställningen beror på storleken på den etablerade lokala volymen, din Internet bandbredd och befintliga data på enheten. Säkerhets kopierings åtgärder på den lokalt fästa volymen tillåts medan återställnings åtgärden pågår. |
| Tunn – Återställ tillgänglighet |Senaste redundans | |
| Högsta antal lästa/skrivna Skriv-och skriv åtgärder för klienter som hanteras från SSD-nivån * |920/720 MB/SEK med ett enda 10-Gigabit Ethernet-nätverks gränssnitt |Upp till två gånger med MPIO och två nätverks gränssnitt. |
| Högsta antal lästa/skrivna Skriv-och skriv åtgärder som hanteras från hård disk nivån * |120/250 MB/SEK | |
| Högsta Skriv-och skriv skydd i klienten, när de hanteras från moln nivån * |11/41 MB/SEK |Läs data flödet är beroende av klienter som genererar och upprätthåller tillräckligt I/O-ködjup. |

&#42;maximalt data flöde per I/O-typ mäts med 100 procents Läs-och 100 procent Skriv scenarier. Det faktiska data flödet kan vara lägre och beror på I/O-mix och nätverks förhållanden.

