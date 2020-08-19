---
title: Resursbegränsningar för virtuell kärna för enkel databas
description: På den här sidan beskrivs några vanliga vCore resurs gränser för en enskild databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/21/2020
ms.openlocfilehash: bb69d48fe4e65d0fc27db027aecab0f1a745e8d5
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566190"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Resursbegränsningar för enskilda databaser med hjälp av vCore-inköpsmodellen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln innehåller detaljerade resurs gränser för enskilda databaser i Azure SQL Database med hjälp av vCore inköps modell.

För inköps modell gränser för DTU för enskilda databaser på en server, se [Översikt över resurs gränser på en server](resource-limits-logical-server.md).

Du kan ange tjänst nivå, beräknings storlek (tjänst mål) och lagrings utrymme för en enskild databas med hjälp av [Azure Portal](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), [Azure CLI](single-database-manage.md#the-azure-cli)eller [REST API](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Information om skalning och överväganden finns i [skala en enskild databas](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Generell användning – Server lös beräkning – Gen5

[Server lös beräknings nivån](serverless-tier-overview.md) är för närvarande endast tillgänglig på Gen5 maskin vara.

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-max virtuella kärnor|0.5-1|0.5-2|0,5-4|0,75-6|1.0-8|
|Min-max minne (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00 – 24|
|Min-max fördröjning för automatisk paus (minuter)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|512|1024|1024|1024|1536|
|Största logg storlek (GB)|154|307|307|307|461|
|Maximal data storlek för TempDB (GB)|32|64|128|192|256|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|320|640|1280|1920|2560|
|Högsta logg frekvens (Mbit/s)|3,8|7.5|15|22,5|30|
|Maximalt antal samtidiga arbetare (begär Anden)|75|150|300|450|600|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|
|Min-max virtuella kärnor|1,25-10|1.50 – 12|1,75-14|2,00-16|
|Min-max minne (GB)|3,75 – 30|4.50-36|5,25-42|6.00-48|
|Min-max fördröjning för automatisk paus (minuter)|60-10080|60-10080|60-10080|60-10080|
|Columnstore-stöd|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|1536|3072|3072|3072|
|Största logg storlek (GB)|461|461|461|922|
|Maximal data storlek för TempDB (GB)|320|384|448|512|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|3200|3840|4480|5120|
|Högsta logg frekvens (Mbit/s)|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|750|900|1050|1200|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Gen5 beräknings generation (del 3)

|Beräknings storlek (tjänst mål)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min-max virtuella kärnor|2.25-18|2.5-20|3–24|4-32|5-40|
|Min-max minne (GB)|6.75-54|7.5 – 60|9-72|12-96|15-120|
|Min-max fördröjning för automatisk paus (minuter)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|3072|3072|4096|4096|4096|
|Största logg storlek (GB)|922|922|1024|1024|1024|
|Maximal data storlek för TempDB (GB)|576|640|768|1024|1280|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|5760|6400|7680|10240|12800|
|Högsta logg frekvens (Mbit/s)|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|1350|1500|1800|2400|3000|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Storskalig-allokerad beräkning – Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100|
|Största logg storlek (TB)|Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |
|Maximal data storlek för TempDB (GB)|32|64|96|128|160|192|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max lokal SSD IOPS *|4000 |8000 |12000 |16000 |20000 |24000 |
|Högsta logg frekvens (Mbit/s)|100 |100 |100 |100 |100 |100 |
|I/o-latens (ungefärligt)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\* Förutom lokal SSD i/o kommer arbets belastningarna att använda [fjärrsidas serverns](service-tier-hyperscale.md#page-server) IO. Effektiv IOPS är beroende av arbets belastning. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance)och [data-i/o i statistik över resursutnyttjande](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100 |
|Största logg storlek (TB)|Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max lokal SSD IOPS *|28000 |32000 |36000 |40000 |64000 |76800 |
|Högsta logg frekvens (Mbit/s)|100 |100 |100 |100 |100 |100 |
|I/o-latens (ungefärligt)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\* Förutom lokal SSD i/o kommer arbets belastningarna att använda [fjärrsidas serverns](service-tier-hyperscale.md#page-server) IO. Effektiv IOPS är beroende av arbets belastning. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance)och [data-i/o i statistik över resursutnyttjande](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Storskalig-allokerad beräkning – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100 |100|
|Största logg storlek (TB)|Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |
|Maximal data storlek för TempDB (GB)|64|128|192|256|320|384|448|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max lokal SSD IOPS *|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|Högsta logg frekvens (Mbit/s)|100 |100 |100 |100 |100 |100 |100 |
|I/o-latens (ungefärligt)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\* Förutom lokal SSD i/o kommer arbets belastningarna att använda [fjärrsidas serverns](service-tier-hyperscale.md#page-server) IO. Effektiv IOPS är beroende av arbets belastning. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance)och [data-i/o i statistik över resursutnyttjande](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Ändra|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|3X-minne|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (TB)|100 |100 |100 |100 |100 |100 |100 |
|Största logg storlek (TB)|Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |Obegränsat |
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp| [Anmärkning 1](#notes) |[Anmärkning 1](#notes)|[Anmärkning 1](#notes)|[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |[Anmärkning 1](#notes) |
|Max lokal SSD IOPS *|64000 |72000 |80000 |96000 |160000 |192000 |204800 |
|Högsta logg frekvens (Mbit/s)|100 |100 |100 |100 |100 |100 |100 |
|I/o-latens (ungefärligt)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|[Anmärkning 2](#notes)|
|Maximalt antal samtidiga arbetare (begär Anden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundära repliker|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Kvarhållning av lagrings utrymme för säkerhets kopior|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|7 dagar|
|||

\* Förutom lokal SSD i/o kommer arbets belastningarna att använda [fjärrsidas serverns](service-tier-hyperscale.md#page-server) IO. Effektiv IOPS är beroende av arbets belastning. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance)och [data-i/o i statistik över resursutnyttjande](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Kommentarer

**Anmärkning 1**: storskalig är en arkitektur med flera nivåer med separata beräknings-och lagrings komponenter: [storskalig arkitektur för tjänst nivå](service-tier-hyperscale.md#distributed-functions-architecture)

**Anmärkning 2**: svars tiden är 1-2 MS för data på den lokala beräknings repliken SSD, som cachelagrar de mest använda data sidorna. Högre latens för data som hämtas från sid servrar.

## <a name="general-purpose---provisioned-compute---gen4"></a>Generell användning-allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|
|Största logg storlek (GB)|307|307|461|461|461|922|
|Maximal data storlek för TempDB (GB)|32|64|96|128|160|192|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|320|640|960|1280|1600|1920|
|Högsta logg frekvens (Mbit/s)|3.75|7.5|11,25|15|18,75|22,5|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|3072|3072|3072|3072|4096|4096|
|Största logg storlek (GB)|922|922|922|922|1229|1229|
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)
|Max data IOPS *|2240|2560|2880|3200|5120|7680|
|Högsta logg frekvens (Mbit/s)|26,3|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Generell användning-allokerad beräkning – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|461|461|461|922|922|
|Maximal data storlek för TempDB (GB)|64|128|192|256|320|384|384|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|640|1280|1920|2560|3200|3840|4480|
|Högsta logg frekvens (Mbit/s)|7.5|15|22,5|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1024|1024|1024|1024|
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|5120|5760|6400|7680|10240|12800|12800|
|Högsta logg frekvens (Mbit/s)|30|30|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Allmänt syfte – etablerad beräkning – Fsv2-serien

### <a name="fsv2-series-compute-generation-part-1"></a>Fsv2-seriens beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Beräknings generation|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|
|Virtuella kärnor|8|10|12|14|16|
|Minne (GB)|15,1|18,9|22,7|26,5|30,2|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|1024|1024|1024|1024|1536|
|Största logg storlek (GB)|336|336|336|336|512|
|Maximal data storlek för TempDB (GB)|333|333|333|333|333|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|2560|3200|3840|4480|5120|
|Högsta logg frekvens (Mbit/s)|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|400|500|600|700|800|
|Maximalt antal samtidiga inloggningar|800|1000|1200|1400|1600|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

### <a name="fsv2-series-compute-generation-part-2"></a>Fsv2-seriens beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Beräknings generation|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|
|Virtuella kärnor|18|20|24|32|36|72|
|Minne (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|1536|1536|1536|3072|3072|4096|
|Största logg storlek (GB)|512|512|512|1024|1024|1024|
|Maximal data storlek för TempDB (GB)|83,25|92,5|111|148|166,5|333|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS *|5760|6400|7680|10240|11520|23040|
|Högsta logg frekvens (Mbit/s)|30|30|30|30|30|30|
|Maximalt antal samtidiga arbetare (begär Anden)|900|1000|1200|1600|1800|3600|
|Maximalt antal samtidiga inloggningar|1800|2000|2400|3200|3600|7200|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Verksamhets kritiskt allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="gen4-compute-generation-part-1"></a>Gen4 beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|1024|1024|1024|1024|1024|1024|
|Största logg storlek (GB)|307|307|307|307|307|307|
|Maximal data storlek för TempDB (GB)|32|64|96|128|160|192|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|4 000|8,000|12 000|16 000|20 000|24 000|
|Högsta logg frekvens (Mbit/s)|8|16|24|32|40|48|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1000|1200|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Gen4 beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|7|8|9,5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|1024|1024|1024|1024|1024|1024|
|Största logg storlek (GB)|307|307|307|307|307|307|
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Maximal data-IOPS |28 000|32 000|36 000|40 000|64 000|76 800|
|Högsta logg frekvens (Mbit/s)|56|64|64|64|64|64|
|Maximalt antal samtidiga arbetare (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga inloggningar (begär Anden)|1400|1600|1800|2000|3200|4800|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Verksamhets kritiskt allokerad beräkning – Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximal data storlek (GB)|1024|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|461|461|461|922|922|
|Maximal data storlek för TempDB (GB)|64|128|192|256|320|384|448|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|8000|16 000|24 000|32 000|40 000|48 000|56 000|
|Högsta logg frekvens (Mbit/s)|24|48|72|96|96|96|96|
|Maximalt antal samtidiga arbetare (begär Anden)|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga inloggningar|200|400|600|800|1000|1200|1400|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Gen5 beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1024|1024|1024|1024|
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|64 000|72 000|80 000|96 000|128 000|160 000|204 800|
|Högsta logg frekvens (Mbit/s)|96|96|96|96|96|96|96|
|Maximalt antal samtidiga arbetare (begär Anden)|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga inloggningar|1600|1800|2000|2400|3200|4000|8000|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Verksamhets kritiskt allokerad beräkning – M-serien

### <a name="m-series-compute-generation-part-1"></a>Beräknings generation i M-serien (del 1)

|Beräknings storlek (tjänst mål)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Beräknings generation|M-serien|M-serien|M-serien|M-serien|M-serien|M-serien|
|Virtuella kärnor|8|10|12|14|16|18|
|Minne (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|64|80|96|112|128|150|
|Maximal data storlek (GB)|512|640|768|896|1024|1152|
|Största logg storlek (GB)|171|213|256|299|341|384|
|Maximal data storlek för TempDB (GB)|256|320|384|448|512|576|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|12 499|15 624|18 748|21 873|24 998|28 123|
|Högsta logg frekvens (Mbit/s)|48|60|72|84|96|108|
|Maximalt antal samtidiga arbetare (begär Anden)|800|1 000|1 200|1400|1600|1800|
|Maximalt antal samtidiga inloggningar|800|1 000|1 200|1400|1600|1800|
|Maximalt antal samtidiga sessioner|30000|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Nej|Nej|Nej|Nej|Nej|Nej|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

### <a name="m-series-compute-generation-part-2"></a>Beräknings generation i M-serien (del 2)

|Beräknings storlek (tjänst mål)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Beräknings generation|M-serien|M-serien|M-serien|M-serien|M-serien|
|Virtuella kärnor|20|24|32|64|128|
|Minne (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|172|216|304|704|1768|
|Maximal data storlek (GB)|1280|1536|2048|4096|4096|
|Största logg storlek (GB)|427|512|683|1024|1024|
|Maximal data storlek för TempDB (GB)|4096|2048|1024|768|640|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS *|31 248|37 497|49 996|99 993|160 000|
|Högsta logg frekvens (Mbit/s)|120|144|192|264|264|
|Maximalt antal samtidiga arbetare (begär Anden)|2 000|2 400|3 200|6 400|12 800|
|Maximalt antal samtidiga inloggningar|2 000|2 400|3 200|6 400|12 800|
|Maximalt antal samtidiga sessioner|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|
|Multi-AZ|Nej|Nej|Nej|Nej|Nej|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

\* Det maximala värdet för IO-storlekar mellan 8 KB och 64 KB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).



## <a name="next-steps"></a>Nästa steg

- För resurs gränser för DTU för en enskild databas, se [resurs gränser för enskilda databaser med inköps modellen DTU](resource-limits-dtu-single-databases.md)
- VCore resurs gränser för elastiska pooler finns i [resurs gränser för elastiska pooler med hjälp av vCore inköps modell](resource-limits-vcore-elastic-pools.md)
- För resurs gränser för DTU för elastiska pooler, se [resurs gränser för elastiska pooler med inköps modellen DTU](resource-limits-dtu-elastic-pools.md)
- Resurs begränsningar för SQL-hanterad instans finns i [resurs gränser för SQL-hanterade instanser](../managed-instance/resource-limits.md).
- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resurs gränser på en server finns i [Översikt över resurs gränser på en server](resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.
