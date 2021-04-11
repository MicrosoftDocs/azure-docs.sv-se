---
title: DTU-resurs begränsar elastiska pooler
description: På den här sidan beskrivs några vanliga värden för DTU-resurser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 03/30/2021
ms.openlocfilehash: f3c20774accf9e0b589b898cb903d6da4516e553
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058926"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Resurs begränsningar för elastiska pooler med inköps modellen DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln innehåller detaljerade resurs gränser för databaser i Azure SQL Database som finns i en elastisk pool med hjälp av inköps modellen DTU.

* För Azure SQL Database, se resurs gränser för DTU-inköp [– Azure SQL Database](resource-limits-dtu-single-databases.md).
* För vCore resurs gränser, se [vCore Resource Limits-Azure SQL Database](resource-limits-vcore-single-databases.md) [-och vCore-resurs gränser-elastiska pooler](resource-limits-vcore-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastisk pool: lagrings storlekar och beräknings storlekar

För Azure SQL Database elastiska pooler visar följande tabeller de resurser som är tillgängliga på varje tjänst nivå och beräknings storlek. Du kan ange tjänst nivå, beräknings storlek och lagrings belopp med:

* [Azure-portalen](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Azure CLI](elastic-pool-manage.md#azure-cli)
* [REST API](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> För skalnings vägledning och överväganden, se [skala en elastisk pool](elastic-pool-scale.md)

Resurs gränserna för enskilda databaser i elastiska pooler är vanligt vis samma som för enskilda databaser utanför pooler baserat på DTU: er och tjänst nivån. Till exempel är maximalt antal samtidiga arbetare för en S2-databas 120 arbetare. Därför är det högsta antalet samtidiga arbetare för en databas i en standard-pool också 120 arbetare om max värdet för DTU per databas i poolen är 50 DTU: er (som motsvarar S2).
 
För samma antal DTU: er kan resurser som tillhandahålls till en elastisk pool överskrida resurserna som tillhandahålls till en enskild databas utanför en elastisk pool. Det innebär att det är möjligt att använda eDTU-användningen av en elastisk pool för att vara mindre än summan av DTU-användningen mellan databaser i poolen, beroende på arbets belastnings mönster. I ett extrema fall med bara en databas i en elastisk pool där databas-DTU-användningen är 100% är det möjligt att använda pool-eDTU-användning som 50% för vissa arbets belastnings mönster. Detta kan inträffa även om max DTU per databas är kvar vid det högsta värdet som stöds för den angivna pool storleken.

> [!NOTE]
> Resurs gränsen för lagring per pool i följande tabeller inkluderar inte tempdb och logg lagring.

### <a name="basic-elastic-pool-limits"></a>Gränser för grundläggande elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Inkluderat lagrings utrymme per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Högsta lagringsutrymme per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max In-Memory OLTP-lagring per pool (GB) | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas |
| Max antal databaser per pool <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare (begär Anden) per pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximalt antal samtidiga sessioner per pool <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Minsta DTU per databas val | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max DTU per databas val | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Största lagringsutrymme per databas (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>2</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="standard-elastic-pool-limits"></a>Standardgränser för elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Inkluderat lagrings utrymme per pool (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Högsta lagringsutrymme per pool (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2048 |
| Max In-Memory OLTP-lagring per pool (GB) | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas |
| Högsta antal databaser per pool <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare (begär Anden) per pool <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximalt antal samtidiga sessioner per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minsta DTU per databas val | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max DTU per databas val | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Största lagringsutrymme per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> se [SQL Database pris alternativ](https://azure.microsoft.com/pricing/details/sql-database/elastic/) för mer information om ytterligare kostnader som uppstår på grund av ytterligare lagrings utrymme.

<sup>2</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="standard-elastic-pool-limits-continued"></a>Standardgränser för elastisk pool (forts.)

| eDTU:er per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Inkluderat lagrings utrymme per pool (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Högsta lagringsutrymme per pool (GB) | 2560 | 3072 | 3584 | 4096 | 4096 |
| Max In-Memory OLTP-lagring per pool (GB) | Saknas | Saknas | Saknas | Saknas | Saknas |
| Högsta antal databaser per pool <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare (begär Anden) per pool <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximalt antal samtidiga sessioner per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minsta DTU per databas val | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max DTU per databas val | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Största lagringsutrymme per databas (GB) | 1024 | 1536 | 1792 | 2304 | 2816 |
|||||||

<sup>1</sup> se [SQL Database pris alternativ](https://azure.microsoft.com/pricing/details/sql-database/elastic/) för mer information om ytterligare kostnader som uppstår på grund av ytterligare lagrings utrymme.

<sup>2</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="premium-elastic-pool-limits"></a>Gränser för Premium elastisk pool

| eDTU:er per pool | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Inkluderat lagrings utrymme per pool (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Högsta lagringsutrymme per pool (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Max In-Memory OLTP-lagring per pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Högsta antal databaser per pool <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Maximalt antal samtidiga arbetare per pool (begär Anden) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Maximalt antal samtidiga sessioner per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalt antal eDTU:er per databas | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Maximalt antal eDTU:er per databas | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Största lagringsutrymme per databas (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
|||||||

<sup>1</sup> se [SQL Database pris alternativ](https://azure.microsoft.com/pricing/details/sql-database/elastic/) för mer information om ytterligare kostnader som uppstår på grund av ytterligare lagrings utrymme.

<sup>2</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

### <a name="premium-elastic-pool-limits-continued"></a>Premiumgränser för elastisk pool (forts.)

| eDTU:er per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Inkluderat lagrings utrymme per pool (GB) <sup>1</sup> | 2048 | 2560 | 3072 | 3548 | 4096 |
| Högsta lagringsutrymme per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Max In-Memory OLTP-lagring per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Högsta antal databaser per pool <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Maximalt antal samtidiga arbetare (begär Anden) per pool <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximalt antal samtidiga sessioner per pool <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minsta DTU per databas val | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Max DTU per databas val | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Största lagringsutrymme per databas (GB) | 2048 | 2560 | 3072 | 3584 | 4096 |
|||||||

<sup>1</sup> se [SQL Database pris alternativ](https://azure.microsoft.com/pricing/details/sql-database/elastic/) för mer information om ytterligare kostnader som uppstår på grund av ytterligare lagrings utrymme.

<sup>2</sup> se [resurs hantering i tätare elastiska pooler](elastic-pool-resource-management.md) för ytterligare överväganden.

<sup>3</sup> för maximalt antal samtidiga arbetare (begär Anden) för enskilda databaser, se [gränser för enskilda databas resurser](resource-limits-vcore-single-databases.md). Om den elastiska poolen exempelvis använder Gen5 och Max vCore per databas anges till 2, är värdet för Max samtidiga arbets tagare 200.  Om max vCore per databas är inställt på 0,5, är Max värdet för samtidiga arbetare 50 sedan på Gen5. det finns högst 100 samtidiga arbetare per vCore. För andra Max vCore-inställningar per databas som är mindre än 1 vCore eller mindre, skalas antalet samtidiga arbets tagare på samma sätt.

> [!IMPORTANT]
> Mer än 1 TB lagrings utrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala och Tyskland nordöstra. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Om alla DTU:er för en elastisk pool används får varje databas i poolen lika många resurser för att bearbeta frågor. SQL Database-tjänsten tillhandahåller rättvis resursdelning mellan databaser genom att tilldela lika mycket beräkningstid till dem. En rättvis resursdelning i elastiska pooler tillämpas utöver den mängd resurser som varje databas är garanterad om det minsta antalet DTU:er per databas har angetts till ett annat värde än noll.

> [!NOTE]
> För `tempdb` gränser, se [tempdb-gränser](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Databas egenskaper för databaser i pooler

I följande tabell beskrivs egenskaperna för databaser i pooler.

| Egenskap | Beskrivning |
|:--- |:--- |
| Maximalt antal eDTU:er per databas |Det högsta antalet eDTU:er som en databas i poolen kan använda, om de är tillgängliga beroende på användningen av andra databaser i poolen. Det högsta antalet eDTU:er per databas utgör ingen resursgaranti för en databas. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Ange ett högsta antal eDTU:er per databas som är tillräckligt högt för att hantera toppar i databasanvändningen. En viss grad av övertilldelning är att förvänta eftersom poolen normalt arbetar efter ett mönster med frekvent och lågfrekvent användning av databaser där inte alla databaser har aktivitetstoppar samtidigt. Anta exempelvis att toppanvändningen per databas är 20 eDTU:er och att endast 20 % av de 100 databaserna i poolen har hög belastning samtidigt. Om det högsta antalet eDTU:er per databas har angetts till 20 eDTU:er är det rimligt att övertilldela poolen med 5 gånger så mycket och ange eDTU:erna per pool till 400. |
| Minimalt antal eDTU:er per databas |Det minsta antalet eDTU:er som en databas i poolen är garanterad. Den här inställningen är en global inställning som gäller för alla databaser i poolen. Det minsta antalet eDTU:er per databas kan anges till 0, vilket även är standardvärdet. Den här egenskapen anges till ett värde mellan 0 och den genomsnittliga eDTU-användningen per databas. Produkten av antalet databaser i poolen och det minsta antalet eDTU:er per databas får inte överskrida antalet eDTU:er per pool. Om en pool till exempel har 20 databaser och det minsta antalet eDTU:er per databas har angetts till 10 eDTU:er så måste antalet eDTU:er per pool vara minst 200 eDTU:er. |
| Maximalt lagrings utrymme per databas |Maximal databas storlek som har angetts av användaren för en databas i en pool. Men poolerade databaser delar allokerat lagrings utrymme för pooler. Även om den totala maximala lagrings utrymmet *per databas* har angetts till att vara större än det totala tillgängliga lagrings *utrymmet för poolen*, kan det totala utrymmet som faktiskt används av alla databaser inte överskrida gränsen för tillgängliga pooler.  Maximal databasstorlek avser den maximala storleken för datafilerna och omfattar inte utrymmet som används av loggfiler. |
|||

## <a name="next-steps"></a>Nästa steg

* För vCore resurs gränser för en enskild databas, se [resurs gränser för enskilda databaser med vCore inköps modell](resource-limits-vcore-single-databases.md)
* För resurs gränser för DTU för en enskild databas, se [resurs gränser för enskilda databaser med inköps modellen DTU](resource-limits-dtu-single-databases.md)
* VCore resurs gränser för elastiska pooler finns i [resurs gränser för elastiska pooler med hjälp av vCore inköps modell](resource-limits-vcore-elastic-pools.md)
* Resurs gränser för hanterade instanser i Azure SQL-hanterad instans finns i [resurs gränser för SQL-hanterade](../managed-instance/resource-limits.md)instanser.
* Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Information om resurs gränser på en logisk SQL-Server finns i [Översikt över resurs gränser på en logisk SQL-Server](resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.