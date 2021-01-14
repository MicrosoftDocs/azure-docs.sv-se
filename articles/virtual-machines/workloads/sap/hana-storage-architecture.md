---
title: Lagrings arkitektur för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Lagrings arkitektur för hur du distribuerar SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2729369347de92153e9e8b84e008e5e22e732081
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197446"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Lagrings arkitektur för SAP HANA (stora instanser)

Storage-layouten för SAP HANA på Azure (stora instanser) konfigureras genom att SAP HANA på den klassiska distributions modellen per SAP rekommenderade rikt linjer. Rikt linjerna beskrivs i [SAP HANA lagrings krav](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) White Paper.

Den stora HANA-instansen av typen I-klassen levereras med fyra gånger minnes volymen som lagrings volym. För typ II-klassen av HANA stor instans enheter är lagringen inte fyra gånger mer. Enheterna levereras med en volym som är avsedd för att lagra HANA-säkerhetskopieringar i transaktions loggen. Mer information finns i [Installera och konfigurera SAP HANA (stora instanser) i Azure](hana-installation.md).

Se följande tabell vad gäller lagrings tilldelning. Tabellen visar den grova kapaciteten för de olika volymerna som tillhandahålls med de olika volymerna i HANA-stor instans.

| HANA stor instans-SKU | Hana/data | Hana/logg | Hana/delad | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S96 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm |  11 520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S384 | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576m | 20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576xm | 31 744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36 000 GB | 4 100 GB | 2 050 GB | 4 100 GB |
| S896m | 33 792 GB | 512 GB | 1 024 GB | 512 GB |

Nyare SKU: er av HANA-stora instanser levereras med diskkonfigurationer som ser ut så här:

| HANA stor instans-SKU | Hana/data | Hana/logg | Hana/delad | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4 224 GB | 512 GB | 1 024 GB | 512 GB |
| S224oo | 6 336 GB | 512 GB | 1 024 GB | 512 GB |
| S224m | 8 448 GB | 512 GB | 1 024 GB | 512 GB |
| S224om | 8 448 GB | 512 GB | 1 024 GB | 512 GB |
| S224ooo | 10 560 GB | 512 GB | 1 024 GB | 512 GB |
| S224oom | 12 672 GB | 512 GB | 1 024 GB | 512 GB |
| S448 | 8 448 GB | 512 GB | 1 024 GB | 512 GB |
| S448oo | 12 672 GB | 512 GB | 1 024 GB | 512 GB |
| S448m | 16 896 GB | 512 GB | 1 024 GB | 512 GB |
| S448om | 16 896 GB | 512 GB | 1 024 GB | 512 GB |
| S448ooo | 21 120 GB | 512 GB | 1 024 GB | 512 GB |
| S448oom | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S672 | 12 672 GB | 512 GB | 1 024 GB | 512 GB |
| S672oo | 19 008 GB | 512 GB | 1 024 GB | 512 GB |
| S672m | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S672om | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S672ooo | 31 680 GB | 512 GB | 1 024 GB | 512 GB |
| S672oom | 38 016 GB | 512 GB | 1 024 GB | 512 GB |
| S896 | 16 896 GB | 512 GB | 1 024 GB | 512 GB |
| S896oo | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S896om | 33 792 GB | 512 GB | 1 024 GB | 512 GB |
| S896ooo | 42 240 GB | 512 GB | 1 024 GB | 512 GB |
| S896oom | 50 688 GB | 512 GB | 1 024 GB | 512 GB |


Faktiska distribuerade volymer kan variera beroende på distribution och det verktyg som används för att visa volym storlekarna.

Om du delar upp ett stort antal HANA-instanser av typen HANA kan några exempel på möjliga divisioner se ut så här:

| Minnes partition i GB | Hana/data | Hana/logg | Hana/delad | Hana/logg/säkerhets kopiering |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1 024 | 1 792 GB | 640 GB | 1 024 GB | 640 GB |
| 1,536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


De här storlekarna är grova volym siffror som kan variera något beroende på distribution och verktyg som används för att titta på volymerna. Det finns också andra partitionstyper, till exempel 2,5 TB. Dessa lagrings storlekar beräknas med en formel som liknar den som används för föregående partitioner. Termen "partitioner" innebär inte att operativ system, minne eller processor resurser är på något sätt partitionerade. Den anger diskpartitioner för de olika HANA-instanserna som du kanske vill distribuera i en enda HANA-stor instans enhet. 

Du kan behöva mer lagrings utrymme. Du kan lägga till lagring genom att köpa ytterligare lagrings utrymme i enheter om 1 TB. Detta ytterligare lagrings utrymme kan läggas till som ytterligare volym. Det kan också användas för att utöka en eller flera av de befintliga volymerna. Det går inte att minska storlek på volymerna som ursprungligen distribuerade och mest dokumenterade av föregående tabeller. Det går inte heller att ändra namnen på volymerna eller monterings namnen. Lagrings volymerna som beskrivs ovan är anslutna till de stora HANA-instans enheterna som NFS4-volymer.

Du kan använda lagrings ögonblicks bilder för säkerhets kopierings-och återställnings-och haveri beredskap. Mer information finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).

Se [HLI-scenarier som stöds](hana-supported-scenario.md) för information om lagrings layout för ditt scenario.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Kör flera SAP HANA instanser på en HANA stor instans enhet

Det är möjligt att vara värd för fler än en aktiv SAP HANA instans på HANA stora instans enheter. För att tillhandahålla funktioner för lagrings ögonblicks bilder och haveri beredskap, kräver en sådan konfiguration en volym uppsättning per instans. För närvarande kan HANA stora instans enheter delas upp på följande sätt:

- **S72, S72m, S96, S144, S192**: i steg om 256 GB, med 256 GB från den minsta startenheten. Olika steg som 256 GB och 512 GB kan kombineras till det maximala minnet för enheten.
- **S144m och S192m**: i steg om 256 GB, med 512 GB den minsta enheten. Olika steg som 512 GB och 768 GB kan kombineras till det maximala minnet för enheten.
- **Typ II-klass**: i steg om 512 GB, med den minsta start enheten på 2 TB. Olika steg som 512 GB, 1 TB och 1,5 TB kan kombineras till det maximala minnet för enheten.

Några exempel på att köra flera SAP HANA instanser kan se ut så här.

| SKU | Minnes storlek | Lagrings storlek | Storlekar med flera databaser |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768-GB HANA-instans<br /> eller 1x512-GB instans + 1x256-GB-instans<br /> eller 3x256-GB-instanser | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA-instanser<br />eller 1x512-GB instans + 1x1-TB instans<br />eller 6x256-GB-instanser<br />eller 1x 1,5 – TB instans | 
| S192m | 4 TB | 16 TB | 8x512 GB-instanser<br />eller 4x1 – TB instanser<br />eller 4x512-GB-instanser + 2x1-TB-instanser<br />eller 4x768-GB-instanser + 2x512-GB-instanser<br />eller 1x4-TB-instans |
| S384xm | 8 TB | 22 TB | 4x2 – TB-instanser<br />eller 2x4 – TB instanser<br />eller 2x3-TB-instanser + 1x2-TB-instanser<br />eller 2x 2.5-TB-instanser + 1x3 – TB-instanser<br />eller 1x8-TB-instans |


Det finns även andra varianter. 

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila
Den lagring som används för HANA stor instans använder transparent kryptering för data som lagras på diskarna sedan årets slut 2018. I tidigare distributioner kan du välja att hämta de krypterade volymerna. Om du har bestämt dig för det alternativet kan du begära att få de volymer som är krypterade online. Flyttningen från icke-krypterade till krypterade volymer är transparent och kräver ingen stillestånds tid. 

Med typ I-klassen för SKU: er lagras volymen som start-LUN på, krypteras. I revision 3 HANA stora instans stämplar använder du typ II-klassen för SKU: er av HANA stor instans, måste du kryptera omstarts-LUN med OS-metoder. I revision 4 HANA stora instans stämplar, använder Type II-enheter volymen. start-LUN lagras och krypteras även i vila som standard. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Nödvändiga inställningar för större HANA-instanser på HANA stora instanser
Lagringen som används i HANA stora instanser har en begränsning för fil storlek. [Storleks begränsningen är 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per fil. Till skillnad från fil storleks begränsningar i EXT3-filsystemen är HANA inte medveten om den lagrings begränsning som tillämpas av lagrings utrymmet för stora instanser av HANA. Det innebär att HANA inte automatiskt skapar en ny datafil när fil storleks gränsen på 16 TB nås. När HANA försöker växa till filen bortom 16 TB rapporterar HANA fel och index servern kommer att krascha i slutet.

> [!IMPORTANT]
> För att förhindra att HANA försöker växa datafiler utöver 16 TB fil storleks gräns på HANA stor instans lagring, måste du ange följande parametrar i global.ini konfigurations filen HANA
> 
> - datavolume_striping = sant
> - datavolume_striping_size_gb = 15000
> - Se även SAP Obs [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Tänk på SAP not [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Nästa steg**
- Referera [till scenarier som stöds för Hana-stora instanser](hana-supported-scenario.md)