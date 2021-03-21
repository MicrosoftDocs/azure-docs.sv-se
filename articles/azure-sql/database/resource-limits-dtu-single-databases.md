---
title: DTU-resurs begränsar enskilda databaser
description: På den här sidan beskrivs några vanliga värden för DTU-resurser för enskilda databaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: references_regions, seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: fd9a811fd1c19d115f3ff15194b7e632114140df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790261"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Resurs gränser för enskilda databaser med hjälp av inköps modellen DTU – Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln innehåller detaljerade resurs gränser för Azure SQL Database enskilda databaser med hjälp av inköps modellen DTU.

För resurs gränser för DTU-inköp för elastiska pooler, se [resurs gränser för DTU-elastiska pooler](resource-limits-dtu-elastic-pools.md). För vCore resurs gränser, se [vCore-resurs gränser – enkla databaser](resource-limits-vcore-single-databases.md) och [vCore resurs gränser – elastiska pooler](resource-limits-vcore-elastic-pools.md). Mer information om olika inköps modeller finns i [inköps modeller och tjänst nivåer](purchasing-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Enkel databas: lagrings storlekar och beräknings storlekar

Följande tabeller visar de resurser som är tillgängliga för en enskild databas på varje tjänst nivå och beräknings storlek. Du kan ange tjänst nivå, beräknings storlek och lagrings belopp för en enskild databas med hjälp av [Azure Portal](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), [Azure CLI](single-database-manage.md#the-azure-cli)eller [REST API](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Information om skalning och överväganden finns i [skala en enskild databas](single-database-scale.md)

### <a name="basic-service-tier"></a>Basic tjänstnivå

| **Beräknings storlek** | **Basic** |
| :--- | --: |
| Maximala DTU:er | 5 |
| Inkluderat lagrings utrymme (GB) | 2 |
| Högsta lagrings utrymme (GB) | 2 |
| Maximal InMemory-OLTP-lagring (GB) |Ej tillämpligt |
| Maximalt antal samtidiga arbetare (begär Anden) | 30 |
| Maximalt antal samtidiga sessioner | 300 |
|||

> [!IMPORTANT]
> Den grundläggande tjänst nivån ger mindre än en vCore (CPU).  För CPU-intensiva arbets belastningar rekommenderas tjänst nivån S3 eller högre.
>
>För data lagring placeras den grundläggande tjänst nivån på standard-sid-blobar. Med standard Page blobbar används hård diskbaserade lagrings medier som är bäst lämpade för utveckling, testning och andra arbets belastningar som inte används ofta och som är mindre känsliga för prestanda variationer.
>

### <a name="standard-service-tier"></a>Standardtjänstnivå

| **Beräknings storlek** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximala DTU:er | 10 | 20 | 50 | 100 |
| Inkluderat lagrings utrymme (GB) <sup>1</sup> | 250 | 250 | 250 | 250 |
| Högsta lagrings utrymme (GB) | 250 | 250 | 250 | 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Saknas | Saknas | Saknas | Saknas |
| Maximalt antal samtidiga arbetare (begär Anden)| 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga sessioner |600 | 900 | 1200 | 2400 |
||||||

<sup>1</sup> se [SQL Database pris alternativ](https://azure.microsoft.com/pricing/details/sql-database/single/) för mer information om ytterligare kostnader som uppstår på grund av ytterligare lagrings utrymme.

> [!IMPORTANT]
> Standard S0, S1-och S2-nivåerna ger mindre än en vCore (CPU).  För CPU-intensiva arbets belastningar rekommenderas tjänst nivån S3 eller högre.
>
>För data lagring, placeras Standard-S0 och S1-tjänst nivåerna på standard sid-blobar. Med standard Page blobbar används hård diskbaserade lagrings medier som är bäst lämpade för utveckling, testning och andra arbets belastningar som inte används ofta och som är mindre känsliga för prestanda variationer.
>

### <a name="standard-service-tier-continued"></a>Standard tjänst nivå (forts.)

| **Beräknings storlek** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximala DTU:er | 200 | 400 | 800 | 1600 | 3000 |
| Inkluderat lagrings utrymme (GB) <sup>1</sup> | 250 | 250 | 250 | 250 | 250 |
| Högsta lagrings utrymme (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Saknas | Saknas | Saknas | Saknas |Saknas |
| Maximalt antal samtidiga arbetare (begär Anden)| 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga sessioner |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

<sup>1</sup> se [SQL Database pris alternativ](https://azure.microsoft.com/pricing/details/sql-database/single/) för mer information om ytterligare kostnader som uppstår på grund av ytterligare lagrings utrymme.

### <a name="premium-service-tier"></a>Premium tjänstnivån

| **Beräknings storlek** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximala DTU:er | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Inkluderat lagrings utrymme (GB) <sup>1</sup> | 500 | 500 | 500 | 500 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Högsta lagrings utrymme (GB) | 1024 | 1024 | 1024 | 1024 | 4096 <sup>2</sup> | 4096 <sup>2</sup> |
| Maximal InMemory-OLTP-lagring (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximalt antal samtidiga arbetare (begär Anden)| 200 | 400 | 800 | 1600 | 2800 | 6400 |
| Maximalt antal samtidiga sessioner | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

<sup>1</sup> se [SQL Database pris alternativ](https://azure.microsoft.com/pricing/details/sql-database/single/) för mer information om ytterligare kostnader som uppstår på grund av ytterligare lagrings utrymme.

<sup>2</sup> från 1024 GB upp till 4096 GB i steg om 256 GB.

> [!IMPORTANT]
> Mer än 1 TB lagrings utrymme på Premium-nivån är för närvarande tillgängligt i alla regioner utom: Kina, östra, Kina, norra, Tyskland, centrala och Tyskland nordöstra. I dessa regioner är det maximala lagringsutrymmet på Premium-nivån begränsat till 1 TB.  Mer information finns i [Aktuella begränsningar för P11–P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).
> [!NOTE]
> För `tempdb` gränser, se [tempdb-gränser](/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Nästa steg

- För vCore resurs gränser för en enskild databas, se [resurs gränser för enskilda databaser med vCore inköps modell](resource-limits-vcore-single-databases.md)
- VCore resurs gränser för elastiska pooler finns i [resurs gränser för elastiska pooler med hjälp av vCore inköps modell](resource-limits-vcore-elastic-pools.md)
- För resurs gränser för DTU för elastiska pooler, se [resurs gränser för elastiska pooler med inköps modellen DTU](resource-limits-dtu-elastic-pools.md)
- Resurs gränser för hanterade instanser i Azure SQL-hanterad instans finns i [resurs gränser för SQL-hanterade](../managed-instance/resource-limits.md)instanser.
- Information om allmänna Azure-gränser finns i [Azure-prenumeration och tjänst begränsningar, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Information om resurs gränser på en logisk SQL-Server finns i [Översikt över resurs gränser på en logisk SQL-Server](resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.