---
title: System gränser för StorSimple 8000-serien | Microsoft Docs
description: Beskriver system begränsningar och rekommenderade storlekar för komponenter och anslutningar i StorSimple 8000-serien.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "68963374"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Vad är StorSimple 8000-seriens system gränser?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

StorSimple tillhandahåller skalbar och flexibel lagring för ditt data Center. Det finns dock vissa begränsningar som du bör ha i åtanke när du planerar, distribuerar och använder din StorSimple-lösning. I följande tabell beskrivs dessa begränsningar och det finns några rekommendationer så att du får ut mesta möjliga av din StorSimple-lösning.

| Gräns för identifierare | Gräns | Kommentarer |
| --- | --- | --- |
| Maximalt antal autentiseringsuppgifter för lagrings konto |64 | |
| Maximalt antal volym behållare |64 | |
| Maximalt antal volymer |255 | |
| Maximalt antal lokalt fästa volymer |32 | |
| Mall för maximalt antal scheman per bandbredd |168 |Ett schema för varje timme, varje dag i veckan (24 * 7). |
| Maximal storlek på en nivå volym på fysiska enheter |64 TB för 8100 och 8600 |8100 och 8600 är fysiska enheter. |
| Maximal storlek på en nivå volym på virtuella enheter i Azure |30 TB för 8010 <br></br> 64 TB för 8020 |8010 och 8020 är virtuella enheter i Azure som använder standard lagring respektive Premium Storage. |
| Maximal storlek för en lokalt fäst volym på fysiska enheter |8,5 TB för 8100 <br></br> 22,5 TB för 8600 |8100 och 8600 är fysiska enheter. |
| Maximalt antal iSCSI-anslutningar |512 | |
| Maximalt antal iSCSI-anslutningar från initierare |512 | |
| Maximalt antal åtkomst kontroll poster per enhet |64 | |
| Maximalt antal volymer per säkerhets kopierings princip |20 | |
| Maximalt antal säkerhets kopior som kvarhålls per schema (i en säkerhets kopierings princip) |64 | |
| Maximalt antal scheman per säkerhets kopierings princip |10 | |
| Maximalt antal ögonblicks bilder av alla typer som kan kvarhållas per volym |256 |Det här antalet inkluderar lokala ögonblicks bilder och moln ögonblicks bilder. |
| Maximalt antal ögonblicks bilder som kan finnas i vilken enhet som helst |10 000 | |
| Maximalt antal volymer som kan bearbetas parallellt för säkerhets kopiering, återställning eller kloning |16 |<ul><li>Om det finns fler än 16 volymer bearbetas de sekventiellt allteftersom bearbetnings platserna blir tillgängliga.</li><li>Nya säkerhets kopior av en klonad eller återställd nivå volym kan inte inträffa förrän åtgärden har slutförts. Men för en lokal volym tillåts säkerhets kopieringar när volymen är online.</li></ul> |
| Återställa och klona återställnings tid för volymer på nivå |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter efter återställnings-eller klonings åtgärder, oavsett volym storlek.</li><li>Volym prestandan kan inlednings vis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på storleken på den allokerade volymen. Metadata överförs automatiskt till enheten i bakgrunden med en hastighet på 5 minuter per TB allokerade volym data. Den här hastigheten kan påverkas av Internet bandbredden till molnet.</li><li>Restore-eller klonings åtgärden slutförs när alla metadata finns på enheten.</li><li>Säkerhets kopierings åtgärder kan inte utföras förrän återställnings-eller klonings åtgärden har slutförts fullständigt. |
| Återställa återställnings tid för lokalt fästa volymer |< 2 minuter |<ul><li>Volymen görs tillgänglig inom 2 minuter från återställnings åtgärden, oavsett volymens storlek.</li><li>Volym prestandan kan inlednings vis vara långsammare än normalt eftersom de flesta data och metadata fortfarande finns i molnet. Prestanda kan öka när data flödar från molnet till StorSimple-enheten.</li><li>Den totala tiden för hämtning av metadata beror på storleken på den allokerade volymen. Metadata överförs automatiskt till enheten i bakgrunden med en hastighet på 5 minuter per TB allokerade volym data. Den här hastigheten kan påverkas av Internet bandbredden till molnet.</li><li>Till skillnad från skiktade volymer, för lokalt fästa volymer, hämtas även volym data lokalt på enheten. Återställningen slutförs när alla volym data har hämtats till enheten.</li><li>Återställnings åtgärderna kan vara långa. Den totala tiden för att slutföra återställningen beror på storleken på den etablerade lokala volymen, din Internet bandbredd och befintliga data på enheten. Säkerhets kopierings åtgärder på den lokalt fästa volymen tillåts medan återställnings åtgärden pågår. |
| Bearbetnings takt för moln ögonblicks bilder |15 minuter/TB |<ul><li>Minsta tid för att göra moln ögonblicks bilder redo att laddas upp per TB allokerade volym data i säkerhets kopieringen. </li><li> Total ögonblicks bild av molnet beräknas genom att lägga till den här tiden i ögonblicks bild överförings tiden, som påverkas av Internet bandbredden till molnet. |
| Högsta antal lästa/skrivbara klient data flöden (när de hanteras från SSD-nivån) * |920/720 MB/s med ett enda 10 GbE-nätverks gränssnitt |Upp till 2x med MPIO och två nätverks gränssnitt. |
| Högsta antal lästa och skrivbara klienter i klienten (när de hanteras från hård disk nivån) * |120/250 MB/s | |
| Högsta lästa/skrivbara klient data flöde (när de hanteras från moln nivån) * för uppdatering 3 och senare * * |40/60 MB/s för skiktade volymer<br><br>60/80 MB/s för skiktade volymer med arkiverings alternativ valt när volymen skapas |Läs data flödet är beroende av klienter som genererar och upprätthåller tillräckligt I/O-ködjup. <br><br>Hastigheten uppnås beroende på hastigheten för det underliggande lagrings kontot som används. |

&#42; maximalt data flöde per I/O-typ mäts med 100 procents Läs-och 100 procent Skriv scenarier. Det faktiska data flödet kan vara lägre och beror på I/O-mix och nätverks förhållanden.

&#42;&#42; prestanda nummer före uppdatering 3 kan vara lägre.

## <a name="next-steps"></a>Nästa steg
Granska [system kraven för StorSimple](storsimple-8000-system-requirements.md).

