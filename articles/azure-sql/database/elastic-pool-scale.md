---
title: Skala resurser i elastiska pooler
description: Den här sidan beskriver skalning av resurser för elastiska pooler i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: 1125ea0c6c625ece010b2acb416ad223e37aea84
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787133"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Skala elastiska poolresurser i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln beskriver hur du skalar beräknings- och lagringsresurser som är tillgängliga för elastiska pooler och pooldatabaser i Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Ändra beräkningsresurser (virtuella kärnor eller DPU:er)

När du har valde antalet virtuella kärnor eller eDPU:er kan du skala upp eller ned en elastisk pool dynamiskt baserat på den faktiska upplevelsen med hjälp av:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure-portalen](elastic-pool-manage.md#azure-portal)
* [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)
* [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)
* [REST-API](/rest/api/sql/elasticpools/update)


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Effekt av att ändra tjänstnivå eller omskalning av beräkningsstorlek

Att ändra tjänstnivå eller beräkningsstorlek för en elastisk pool följer ett liknande mönster som för enskilda databaser och omfattar huvudsakligen den tjänst som utför följande steg:

1. Skapa en ny beräkningsinstans för den elastiska poolen  

    En ny beräkningsinstans för den elastiska poolen skapas med den begärda tjänstnivån och beräkningsstorleken. För vissa kombinationer av ändringar av tjänstnivå och beräkningsstorlek måste en replik av varje databas skapas i den nya beräkningsinstansen, vilket inbegriper kopiering av data och kan starkt påverka den övergripande svarstiden. Oavsett förblir databaserna online under det här steget och anslutningar fortsätter att dirigeras till databaserna i den ursprungliga beräkningsinstansen.

2. Växla routning av anslutningar till en ny beräkningsinstans

    Befintliga anslutningar till databaserna i den ursprungliga beräkningsinstansen tas bort. Alla nya anslutningar upprättas till databaserna i den nya beräkningsinstansen. För vissa kombinationer av ändringar av tjänstnivå och beräkningsstorlek frånkopplas och återansluts databasfilerna under växeln.  Oavsett vilket kan växeln resultera i ett kort tjänstavbrott när databaser vanligtvis är otillgängliga i mindre än 30 sekunder och ofta bara under några sekunder. Om det finns långvariga transaktioner som körs när anslutningar avbryts kan det ta längre tid för det här steget att återställa avbrutna transaktioner. [Accelererad databasåterställning](../accelerated-database-recovery.md) kan minska påverkan från att avbryta långvariga transaktioner.

> [!IMPORTANT]
> Inga data går förlorade under något steg i arbetsflödet.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Svarstid vid ändring av tjänstnivå eller omskalning av beräkningsstorlek

Den uppskattade svarstiden för att ändra tjänstnivå, skala beräkningsstorleken för en enkel databas eller elastisk pool, flytta en databas in/ut från en elastisk pool eller flytta en databas mellan elastiska pooler parametriseras på följande sätt:

|Tjänstenivå|Enkel enkel databas,</br>Standard (S0-S1)|Grundläggande elastisk pool,</br>Standard (S2-S12), </br>Generell användning enkel databas eller elastisk pool|Premium eller Affärskritisk enkel databas eller elastisk pool|Hyperskala
|:---|:---|:---|:---|:---|
|**Enkel enkel databas, </br> Standard (S0-S1)**|&bull;&nbsp;Konstant tidsfördröjning oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 5 minuter|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|
|**Grundläggande elastisk pool, </br> Standard (S2-S12), </br> Generell användning enkel databas eller elastisk pool**|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;För enskilda databaser, konstant tidsfördröjning oberoende av använt utrymme</br>&bull;&nbsp;Vanligtvis mindre än 5 minuter för enskilda databaser</br>&bull;&nbsp;För elastiska pooler är det proportionellt mot antalet databaser|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|
|**Premium eller Affärskritisk enkel databas eller elastisk pool**|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|&bull;&nbsp;Svarstiden är proportionell mot databasutrymmet som används på grund av datakopiering</br>&bull;&nbsp;Normalt mindre än 1 minut per GB använt utrymme|
|**Hyperskala**|Saknas|Saknas|Saknas|&bull;&nbsp;Konstant tidsfördröjning oberoende av använt utrymme</br>&bull;&nbsp;Normalt mindre än 2 minuter|

> [!NOTE]
>
> - Om du ändrar tjänstnivån eller omskalningsberäkningen för en elastisk pool ska sammanfattningen av utrymmet som används för alla databaser i poolen användas för att beräkna uppskattningen.
> - När du flyttar en databas till/från en elastisk pool påverkar bara det utrymme som används av databasen svarstiden, inte det utrymme som används av den elastiska poolen.
> - För elastiska standardpooler och Generell användning-pooler är svarstiden för att flytta en databas in/ut från en elastisk pool eller mellan elastiska pooler proportionell mot databasstorleken om den elastiska poolen använder Premium-filresurslagring[(PFS).](../../storage/files/storage-files-introduction.md) För att avgöra om en pool använder PFS-lagring kör du följande fråga i kontexten för en databas i poolen. Om värdet i kolumnen AccountType är `PremiumFileStorage` eller `PremiumFileStorage-ZRS` använder poolen PFS-lagring.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Information om hur du övervakar pågående åtgärder finns i: Hantera åtgärder med [SQL REST API,](/rest/api/sql/operations/list)Hantera åtgärder med [CLI,](/cli/azure/sql/db/op)Övervaka åtgärder med [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) och dessa två PowerShell-kommandon: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) och [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Ytterligare överväganden när du ändrar tjänstnivå eller omskalning av beräkningsstorlek

- När du minskar storleken på virtuella kärnor eller eDPU:er för en elastisk pool måste poolens använda utrymme vara mindre än den största tillåtna storleken på måltjänstnivån och pool-eDPU:erna.
- Vid omskalning av eDPU:er för en elastisk pool gäller en extra lagringskostnad om (1) den maximala lagringsstorleken för poolen stöds av målpoolen och (2) den maximala lagringsstorleken överskrider den inkluderade lagringsmängden för målpoolen. Om till exempel en standardpool på 100 eDTU med en maximal storlek på 100 GB är nedtonad till en standardpool på 50 eDTU gäller en extra lagringskostnad eftersom målpoolen stöder en maxstorlek på 100 GB och den inkluderade lagringsmängden bara är 50 GB. Det extra lagringsutrymmet är alltså 100 GB – 50 GB = 50 GB. Priser för extra lagring finns i [SQL Database priser.](https://azure.microsoft.com/pricing/details/sql-database/) Om den faktiska mängden utrymme som används är mindre än den inkluderade lagringsmängden kan du undvika den här extra kostnaden genom att minska databasens maximala storlek till den inkluderade mängden.

### <a name="billing-during-rescaling"></a>Fakturering under omskalning

Du debiteras för varje timme som en databas finns med den högsta tjänstnivån + beräkningsstorleken som tillämpades under den timmen, oavsett användning eller om databasen var aktiv i mindre än en timme. Om du till exempel skapar en enkel databas och tar bort den fem minuter senare återspeglar fakturan en avgift för en databastimme.

## <a name="change-elastic-pool-storage-size"></a>Ändra lagringsstorlek för elastisk pool

> [!IMPORTANT]
> I vissa fall kan du behöva krympa en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera filutrymme i Azure SQL Database](file-space-manage.md).

### <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

- Lagring kan etableras upp till den maximala storleksgränsen:

  - För lagring på tjänstnivån standard eller generell användning ökar eller minskar du storleken i steg om 10 GB
  - För lagring på premium- eller affärskritiska tjänstnivåer kan du öka eller minska storleken i steg om 250 GB
- Lagring för en elastisk pool kan etableras genom att öka eller minska den maximala storleken.
- Priset för lagring för en elastisk pool är lagringsbeloppet multiplicerat med lagringsenhetspriset för tjänstnivån. Mer information om priset för extra lagringsutrymme finns i [SQL Database priser.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> I vissa fall kan du behöva krympa en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera filutrymme i Azure SQL Database](file-space-manage.md).

### <a name="dtu-based-purchasing-model"></a>Köpmodell baserad på DTU

- EDTU-priset för en elastisk pool inkluderar en viss mängd lagring utan extra kostnad. Extra lagringsutrymme utöver det inkluderade beloppet kan etableras för en extra kostnad upp till den maximala storleksgränsen i steg om 250 GB upp till 1 TB och sedan i steg om 256 GB över 1 TB. Information om inkluderade lagringsbelopp och maxstorleksgränser finns i Resursgränser för elastiska pooler med [köpmodellen DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) eller Resursgränser för elastiska pooler med köpmodellen [vCore](resource-limits-vcore-elastic-pools.md).
- Extra lagring för en elastisk pool kan etableras genom att öka den maximala storleken med [hjälp av Azure Portal,](elastic-pool-manage.md#azure-portal) [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool) [Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) [eller REST API](/rest/api/sql/elasticpools/update).
- Priset för extra lagring för en elastisk pool är den extra lagringsmängden multiplicerat med extra lagringsenhetspris för tjänstnivån. Mer information om priset för extra lagring finns i [SQL Database priser.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> I vissa fall kan du behöva krympa en databas för att frigöra outnyttjat utrymme. Mer information finns i [Hantera filutrymme i Azure SQL Database](file-space-manage.md).

## <a name="next-steps"></a>Nästa steg

Övergripande resursbegränsningar finns [i SQL Database](resource-limits-vcore-elastic-pools.md) resursbegränsningar för virtuella kärnor – elastiska pooler och [SQL Database DTU-baserade resursbegränsningar – elastiska pooler.](resource-limits-dtu-elastic-pools.md)