---
title: Resursbegränsningar för virtuell kärna för elastisk pool
description: På den här sidan beskrivs några vanliga vCore resurs gränser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 01/15/2021
ms.openlocfilehash: 2daa07315be85e1fcd543480cd30a57c118d8547
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251496"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Resurs begränsningar för elastiska pooler med vCore inköps modell
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln innehåller detaljerade resurs gränser för Azure SQL Database elastiska pooler och databaser i pooler med hjälp av vCore inköps modell.

För inköps modell gränser för DTU, se [SQL Database gräns för DTU-resurs-elastiska pooler](resource-limits-dtu-elastic-pools.md).

> [!IMPORTANT]
> Under vissa omständigheter kan du behöva krympa en databas för att frigöra utrymme som inte används. Mer information finns i [Hantera fil utrymme i Azure SQL Database](file-space-manage.md).

Du kan ange tjänst nivå, beräknings storlek (tjänst mål) och lagrings belopp med hjälp av [Azure Portal](elastic-pool-manage.md#azure-portal), [POWERSHELL](elastic-pool-manage.md#powershell), [Azure CLI](elastic-pool-manage.md#azure-cli)eller [REST API](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Information om skalning och överväganden finns i [skala en elastisk pool](elastic-pool-scale.md).

## <a name="general-purpose---provisioned-compute---gen4"></a>Generell användning-allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Tjänst nivå för generell användning: generation 4-beräknings plattform (del 1)

|Beräknings storlek (tjänst mål)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|1|2|3|4|5|6|
|Minne (GB)|7|14|21|28|35|42|
|Max antal databaser per pool <sup>1</sup>|100|200|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|512|756|1536|1536|1536|2048|
|Maximal loggstorlek|154|227|461|461|461|614|
|Maximal data storlek för TempDB (GB)|32|64|96|128|160|192|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS per pool <sup>2</sup> |400|800|1200|1600|2000|2400|
|Högsta logg hastighet per pool (Mbit/s)|6|12|18|24|30|36|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup> |210|420|630|840|1050|1260|
|Maximalt antal samtidiga inloggningar per pool <sup>3</sup> |210|420|630|840|1050|1260|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Tjänst nivå för generell användning: generation 4 beräknings plattform (del 2)

|Beräknings storlek (tjänst mål)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|Max antal databaser per pool <sup>1</sup>|500|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|2048|2048|2048|2048|3584|4096|
|Största logg storlek (GB)|614|614|614|614|1075|1229|
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Högsta logg hastighet per pool (Mbit/s)|42|48|48|48|48|48|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Maximal pool för samtidiga inloggningar (begär Anden) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).    

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

## <a name="general-purpose---provisioned-compute---gen5"></a>Generell användning-allokerad beräkning – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Tjänst nivå för generell användning: generation 5 beräknings plattform (del 1)

|Beräknings storlek (tjänst mål)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|2|4|6|8|10|12|14|
|Minne (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Max antal databaser per pool <sup>1</sup>|100|200|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|512|756|1536|1536|1536|2048|2048|
|Största logg storlek (GB)|154|227|461|461|461|614|614|
|Maximal data storlek för TempDB (GB)|64|128|192|256|320|384|448|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Högsta logg hastighet per pool (Mbit/s)|12|24|36|48|48|48|48|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Tjänst nivå för generell användning: generation 5 beräknings plattform (del 2)

|Beräknings storlek (tjänst mål)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Max antal databaser per pool <sup>1</sup>|500|500|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|2048|3072|3072|3072|4096|4096|4096|
|Största logg storlek (GB)|614|922|922|922|1229|1229|1229|
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS per pool <sup>2</sup> |6 400|7 200|8,000|9 600|12 800|16 000|16 000|
|Högsta logg hastighet per pool (Mbit/s)|48|48|48|48|48|48|48|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Antal repliker|1|1|1|1|1|1|1|
|Multi-AZ|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[Tillgängligt för hands version](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Allmänt syfte – etablerad beräkning – Fsv2-serien

### <a name="fsv2-series-compute-generation-part-1"></a>Fsv2-seriens beräknings generation (del 1)

|Beräknings storlek (tjänst mål)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Beräknings generation|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|
|Virtuella kärnor|8|10|12|14|16|
|Minne (GB)|15,1|18,9|22,7|26,5|30,2|
|Max antal databaser per pool <sup>1</sup>|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|1024|1024|1024|1024|1536|
|Största logg storlek (GB)|336|336|336|336|512|
|Maximal data storlek för TempDB (GB)|333|333|333|333|333|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|2560|3200|3840|4480|5120|
|Högsta logg hastighet per pool (Mbit/s)|48|48|48|48|48|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|400|500|600|700|800|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|800|1000|1200|1400|1600|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0-8|0-10|0-12|0-14|0-16|
|Antal repliker|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|


<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="fsv2-series-compute-generation-part-2"></a>Fsv2-seriens beräknings generation (del 2)

|Beräknings storlek (tjänst mål)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Beräknings generation|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|Fsv2-serien|
|Virtuella kärnor|18|20|24|32|36|72|
|Minne (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Max antal databaser per pool <sup>1</sup>|500|500|500|500|500|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|1536|1536|1536|3072|3072|4096|
|Största logg storlek (GB)|512|512|512|1024|1024|1024|
|Maximal data storlek för TempDB (GB)|83,25|92,5|111|148|166,5|333|
|Lagringstyp|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|Fjärr-SSD|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|5760|6400|7680|10240|11520|23040|
|Högsta logg hastighet per pool (Mbit/s)|48|48|48|48|48|48|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|900|1000|1200|1600|1800|3600|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|1800|2000|2400|3200|3600|7200|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0-18|0-20|0-24|0-32|0-36|0-72|
|Antal repliker|1|1|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.


## <a name="general-purpose---provisioned-compute---dc-series"></a>Allmänt syfte – etablerad beräkning – DC-serien

|Beräknings storlek (tjänst mål)|GP_DC_2|GP_DC_4|GP_DC_6|GP_DC_8|
|:--- | --: |--: |--: |--: |
|Beräknings generation|DC|DC|DC|DC|
|Virtuella kärnor|2|4|6|8|
|Minne (GB)|9|18|27|36|
|Max antal databaser per pool <sup>1</sup>|100|400|400|400|
|Columnstore-stöd|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|Saknas|Saknas|Saknas|Saknas|
|Maximal data storlek (GB)|756|1536|2048|2048|
|Största logg storlek (GB)|227|461|614|614|
|Maximal data storlek för TempDB (GB)|64|128|192|256|
|Lagringstyp|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|Premium-lagring (Remote)|
|I/o-latens (ungefärligt)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|5-7 MS (skrivning)<br>5-10 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|800|1600|2400|3200|
|Högsta logg hastighet per pool (Mbit/s)|9,4|18,8|28,1|32,8|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|168|336|504|672|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|168|336|504|672|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|2|2... 4|2... 6|2... 8|
|Antal repliker|1|1|1|1|
|Multi-AZ|Saknas|Saknas|Saknas|Saknas|
|Lässkalning|Saknas|Saknas|Saknas|Saknas|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

## <a name="business-critical---provisioned-compute---gen4"></a>Verksamhets kritiskt allokerad beräkning – Gen4

> [!IMPORTANT]
> Nya Gen4-databaser stöds inte längre i regionerna Australien, östra eller Brasilien, södra.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Verksamhets kritisk tjänst nivå: generation 4 beräknings plattform (del 1)

|Beräknings storlek (tjänst mål)|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|2|3|4|5|6|
|Minne (GB)|14|21|28|35|42|
|Max antal databaser per pool <sup>1</sup>|50|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|2|3|4|5|6|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|1024|1024|1024|1024|1024|
|Största logg storlek (GB)|307|307|307|307|307|
|Maximal data storlek för TempDB (GB)|64|96|128|160|192|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|9,000|13 500|18 000|22 500|27 000|
|Högsta logg hastighet per pool (Mbit/s)|20|30|40|50|60|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|420|630|840|1050|1260|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|420|630|840|1050|1260|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Antal repliker|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Verksamhets kritisk tjänst nivå: generation 4 beräknings plattform (del 2)

|Beräknings storlek (tjänst mål)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Beräknings generation|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|Virtuella kärnor|7|8|9|10|16|24|
|Minne (GB)|49|56|63|70|112|159,5|
|Max antal databaser per pool <sup>1</sup>|100|100|100|100|100|100|
|Columnstore-stöd|Saknas|Saknas|Saknas|Saknas|Saknas|Saknas|
|Minnes intern OLTP-lagring (GB)|7|8|9,5|11|20|36|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|Maximal data storlek (GB)|1024|1024|1024|1024|1024|1024|
|Största logg storlek (GB)|307|307|307|307|307|307|
|Maximal data storlek för TempDB (GB)|224|256|288|320|512|768|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|31 500|36 000|40 500|45 000|72 000|96 000|
|Högsta logg hastighet per pool (Mbit/s)|70|80|80|80|80|80|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|1470|1 680|1890|2100|3 360|5040|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

## <a name="business-critical---provisioned-compute---gen5"></a>Verksamhets kritiskt allokerad beräkning – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Verksamhets kritisk tjänst nivå: generation 5 beräknings plattform (del 1)

|Beräknings storlek (tjänst mål)|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|4|6|8|10|12|14|
|Minne (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Max antal databaser per pool <sup>1</sup>|50|100|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximal data storlek (GB)|1024|1536|1536|1536|3072|3072|
|Största logg storlek (GB)|307|307|461|461|922|922|
|Maximal data storlek för TempDB (GB)|128|192|256|320|384|448|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|18 000|27 000|36 000|45 000|54 000|63 000|
|Högsta logg hastighet per pool (Mbit/s)|60|90|120|120|120|120|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Verksamhets kritisk tjänst nivå: generation 5 beräknings plattform (del 2)

|Beräknings storlek (tjänst mål)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Beräknings generation|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuella kärnor|16|18|20|24|32|40|80|
|Minne (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Max antal databaser per pool <sup>1</sup>|100|100|100|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Maximal data storlek (GB)|3072|3072|3072|4096|4096|4096|4096|
|Största logg storlek (GB)|922|922|922|1229|1229|1229|1229|
|Maximal data storlek för TempDB (GB)|512|576|640|768|1024|1280|2560|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|72 000|81 000|90 000|108 000|144 000|180 000|256 000|
|Högsta logg hastighet per pool (Mbit/s)|120|120|120|120|120|120|120|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|1 680|1890|2100|2 520|3 360|4200|8400|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Antal repliker|4|4|4|4|4|4|4|
|Multi-AZ|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

## <a name="business-critical---provisioned-compute---m-series"></a>Verksamhets kritiskt allokerad beräkning – M-serien

### <a name="m-series-compute-generation-part-1"></a>Beräknings generation i M-serien (del 1)

|Beräknings storlek (tjänst mål)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Beräknings generation|M-serien|M-serien|M-serien|M-serien|M-serien|M-serien|
|Virtuella kärnor|8|10|12|14|16|18|
|Minne (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Max antal databaser per pool <sup>1</sup>|100|100|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|64|80|96|112|128|150|
|Maximal data storlek (GB)|512|640|768|896|1024|1152|
|Största logg storlek (GB)|171|213|256|299|341|384|
|Maximal data storlek för TempDB (GB)|256|320|384|448|512|576|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|12 499|15 624|18 748|21 873|24 998|28 123|
|Högsta logg hastighet per pool (Mbit/s)|48|60|72|84|96|108|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|800|1 000|1 200|1400|1600|1800|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|800|1 000|1 200|1400|1600|1800|
|Maximalt antal samtidiga sessioner|30000|30000|30000|30000|30000|30000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|0-8|0-10|0-12|0-14|0-16|0-18|
|Antal repliker|4|4|4|4|4|4|
|Multi-AZ|Nej|Nej|Nej|Nej|Nej|Nej|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

Om alla virtuella kärnor av en elastisk pool är upptagna får varje databas i poolen en lika stor mängd data bearbetnings resurser för att bearbeta frågor. Azure SQL Database tillhandahåller resurs delnings skälighet mellan databaser genom att se till att det är lika med olika sektorer av beräknings tiden. Resurs delnings skälighet för elastisk pool är utöver den mängd resurser som på annat sätt garanteras för varje databas när vCore min per databas är inställt på ett värde som inte är noll.

### <a name="m-series-compute-generation-part-2"></a>Beräknings generation i M-serien (del 2)

|Beräknings storlek (tjänst mål)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Beräknings generation|M-serien|M-serien|M-serien|M-serien|M-serien|
|Virtuella kärnor|20|24|32|64|128|
|Minne (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Max antal databaser per pool <sup>1</sup>|100|100|100|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|172|216|304|704|1768|
|Maximal data storlek (GB)|1280|1536|2048|4096|4096|
|Största logg storlek (GB)|427|512|683|1024|1024|
|Maximal data storlek för TempDB (GB)|4096|2048|1024|768|640|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|31 248|37 497|49 996|99 993|160 000|
|Högsta logg hastighet per pool (Mbit/s)|120|144|192|264|264|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|2 000|2 400|3 200|6 400|12 800|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|2 000|2 400|3 200|6 400|12 800|
|Maximalt antal samtidiga sessioner|30000|30000|30000|30000|30000|
|Antal repliker|4|4|4|4|4|
|Multi-AZ|Nej|Nej|Nej|Nej|Nej|
|Lässkalning|Ja|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

Om alla virtuella kärnor av en elastisk pool är upptagna får varje databas i poolen en lika stor mängd data bearbetnings resurser för att bearbeta frågor. Azure SQL Database tillhandahåller resurs delnings skälighet mellan databaser genom att se till att det är lika med olika sektorer av beräknings tiden. Resurs delnings skälighet för elastisk pool är utöver den mängd resurser som på annat sätt garanteras för varje databas när vCore min per databas är inställt på ett värde som inte är noll.

## <a name="business-critical---provisioned-compute---dc-series"></a>Verksamhets kritiskt allokerad beräkning-DC-serien

|Beräknings storlek (tjänst mål)|BC_DC_2|BC_DC_4|BC_DC_6|BC_DC_8|
|:--- | --: |--: |--: |--: |
|Beräknings generation|DC|DC|DC|DC|
|Virtuella kärnor|2|4|6|8|
|Minne (GB)|9|18|27|36|
|Max antal databaser per pool <sup>1</sup>|50|100|100|100|
|Columnstore-stöd|Ja|Ja|Ja|Ja|
|Minnes intern OLTP-lagring (GB)|1,7|3.7|5.9|8,2|
|Maximal data storlek (GB)|768|768|768|768|
|Största logg storlek (GB)|230|230|230|230|
|Maximal data storlek för TempDB (GB)|64|128|192|256|
|Lagringstyp|Lokal SSD|Lokal SSD|Lokal SSD|Lokal SSD|
|I/o-latens (ungefärligt)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|1-2 ms (skrivning)<br>1-2 ms (läsa)|
|Max data IOPS per pool <sup>2</sup>|15750|31500|47250|56000|
|Högsta logg hastighet per pool (Mbit/s)|20|60|90|120|
|Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup>|168|336|504|672|
|Maximalt antal samtidiga inloggningar per pool (begär Anden) <sup>3</sup>|168|336|504|672|
|Maximalt antal samtidiga sessioner|30 000|30 000|30 000|30 000|
|Minsta/högsta antal vCore alternativ för elastisk pool per databas|2|2... 4|2... 6|2... 8|
|Antal repliker|4|4|4|4|
|Multi-AZ|Nej|Nej|Nej|Nej|
|Lässkalning|Ja|Ja|Ja|Ja|
|Inkluderad säkerhets kopierings lagring|1X DB-storlek|1X DB-storlek|1X DB-storlek|1X DB-storlek|

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> det maximala värdet för IO-storlekar mellan 8 kb och 64 kB. Faktisk IOPS är arbets belastnings beroende. Mer information finns i [data IO-styrning](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

## <a name="database-properties-for-pooled-databases"></a>Databas egenskaper för databaser i pooler

I följande tabell beskrivs egenskaperna för databaser i pooler.

> [!NOTE]
> Resurs gränserna för enskilda databaser i elastiska pooler är vanligt vis samma som för enskilda databaser utanför pooler som har samma beräknings storlek (tjänst målet). Till exempel är maximalt antal samtidiga arbetare för en GP_Gen4_1-databas 200 arbetare. Det innebär att maximalt antal samtidiga arbetare för en databas i en GP_Gen4_1 pool också är 200 arbetare. Observera att det totala antalet samtidiga arbetare i GP_Gen4_1 pool är 210.

| Egenskap | Beskrivning |
|:--- |:--- |
| Max virtuella kärnor per databas |Det maximala antalet virtuella kärnor som en databas i poolen kan använda, om den är tillgänglig baserat på användningen av andra databaser i poolen. Max virtuella kärnor per databas är inte en resurs garanti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange Max virtuella kärnor per databas tillräckligt högt för att hantera toppar i databas användningen. En viss grad av överbelastning förväntas eftersom poolen vanligt vis förutsätter frekventa och kalla användnings mönster för databaser där alla databaser inte samtidigt överskrider varandra.|
| Minsta virtuella kärnor per databas |Det minsta antalet virtuella kärnor som alla databaser i poolen garanterar. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Den minsta virtuella kärnor per databas kan anges till 0 och är också standardvärdet. Den här egenskapen anges till var som helst mellan 0 och den genomsnittliga virtuella kärnor-användningen per databas. Produkten av antalet databaser i poolen och det minsta virtuella kärnor per databas får inte överskrida virtuella kärnor per pool.|
| Maximalt lagrings utrymme per databas |Maximal databas storlek som har angetts av användaren för en databas i en pool. Databaser i pooler delar allokerat lagrings utrymme, så den storlek som en databas kan uppnå är begränsad till den mindre av de återstående lagrings utrymmena för poolen och databas storleken.  Maximal databasstorlek avser den maximala storleken för datafilerna och omfattar inte utrymmet som används av loggfiler. |
|||

## <a name="next-steps"></a>Nästa steg

- För vCore resurs gränser för en enskild databas, se [resurs gränser för enskilda databaser med vCore inköps modell](resource-limits-vcore-single-databases.md)
- För resurs gränser för DTU för en enskild databas, se [resurs gränser för enskilda databaser med inköps modellen DTU](resource-limits-dtu-single-databases.md)
- För resurs gränser för DTU för elastiska pooler, se [resurs gränser för elastiska pooler med inköps modellen DTU](resource-limits-dtu-elastic-pools.md)
- Resurs begränsningar för hanterade instanser finns i [resurs gränser för hanterade](../managed-instance/resource-limits.md)instanser.
- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resurs gränser på en logisk SQL-Server finns i [Översikt över resurs gränser på en logisk SQL-Server](resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.
