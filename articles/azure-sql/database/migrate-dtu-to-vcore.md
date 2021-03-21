---
title: Migrera från DTU till virtuell kärna
description: Migrera en databas i Azure SQL Database från DTU-modellen till vCore-modellen. Migrering till vCore liknar uppgradering eller nedgradering mellan standard-och Premium nivåerna.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/09/2021
ms.openlocfilehash: 332a2273a377268a425619a0cdaa5f4780b46e73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361663"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrera Azure SQL Database från den DTU-baserade modellen till den vCore-baserade modellen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln beskrivs hur du migrerar databasen i Azure SQL Database från den [DTU-baserade inköps modellen](service-tiers-dtu.md) till den [vCore-baserade inköps modellen](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Migrera en databas

Migrering av en databas från den DTU-baserade inköps modellen till den vCore-baserade inköps modellen liknar skalning mellan tjänstens mål på tjänst nivåerna Basic, standard och Premium, med samma [varaktighet](single-database-scale.md#latency) och en [minimal nedtid](scale-resources.md) i slutet av migreringsprocessen. En databas som migreras till den vCore-baserade inköps modellen kan migreras tillbaka till den DTU-baserade inköps modellen när som helst på samma sätt, med undantag för databaser som migrerats till den [storskaliga](service-tier-hyperscale.md) tjänst nivån. 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>Välj tjänst nivå och tjänst mål för vCore

För de flesta DTU-vCore, kommer databaser och elastiska pooler på nivåerna Basic och standard att mappas till [generell användning](service-tier-general-purpose.md) tjänst nivå. Databaser och elastiska pooler på Premium-tjänstnivå kommer att mappas till [affärskritisk](service-tier-business-critical.md) tjänst nivå. Beroende på program scenario och-krav kan den [storskaliga](service-tier-hyperscale.md) tjänst nivån ofta användas som mål för migrering för enskilda databaser i alla nivåer för DTU-tjänster.

För att kunna välja tjänst målet, eller beräknings storlek, för den migrerade databasen i vCore-modellen, kan du använda en enkel men ungefärlig tumregel: var 100 DTU: er på Basic-eller standard-nivåerna kräver *minst* 1 vCore och varje 125-DTU: er på Premium nivån kräver *minst* 1 vcore. 

> [!TIP]
> Den här regeln är ungefärlig eftersom den inte beaktar den maskin varu generation som används för DTU-databasen eller elastisk pool. 

I DTU-modellen kan alla tillgängliga [maskin varu generationer](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) användas för din databas eller elastisk pool. Vidare har du bara indirekt kontroll över antalet virtuella kärnor (logiska processorer) genom att välja högre eller lägre DTU-eller eDTU-värden. 

Med vCore-modellen måste kunderna göra ett explicit val av både maskin varu generering och antalet virtuella kärnor (logiska processorer). DTU-modellen erbjuder inte de här valen, men maskin varu genereringen och antalet logiska processorer som används för varje databas och elastisk pool exponeras via vyer för dynamisk hantering. Detta gör det möjligt att fastställa det matchande vCore tjänst målet mer exakt. 

I följande metod används den här informationen för att fastställa ett vCore tjänst mål med en liknande allokering av resurser, för att få en liknande prestanda nivå efter migrering till vCore-modellen.

### <a name="dtu-to-vcore-mapping"></a>Mappning av DTU till vCore

En T-SQL-fråga nedan, när den körs i kontexten för en DTU-databas som ska migreras, returnerar ett matchande (möjligen bråktal) antal virtuella kärnor i varje maskin varu generation i vCore-modellen. Genom att avrunda det här talet till det närmaste antalet virtuella kärnor som är tillgängligt för [databaser](resource-limits-vcore-single-databases.md) och [elastiska pooler](resource-limits-vcore-elastic-pools.md) i varje maskin varu generation i vCore-modellen, kan kunderna välja det vCore tjänst mål som är den närmaste matchningen för deras DTU-databas eller elastisk pool. 

Exempel scenarier för migrering som använder den här metoden beskrivs i avsnittet [exempel](#dtu-to-vcore-migration-examples) .

Kör den här frågan i kontexten för databasen som ska migreras i stället för i `master` databasen. När du migrerar en elastisk pool ska du köra frågan i kontexten för en databas i poolen.

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Ytterligare faktorer

Förutom antalet virtuella kärnor (logiska processorer) och maskin varu genereringen kan flera andra faktorer påverka valet av vCore service mål:

- Mappnings-T-SQL-frågan matchar DTU-och vCore-tjänstens mål vad gäller deras CPU-kapacitet, och därför blir resultaten mer exakta för PROCESSORbaserade arbets belastningar.
- För samma maskin varu generation och samma antal virtuella kärnor är IOPS-och transaktions logg data flödes resurs gränser för vCore-databaser ofta högre än för DTU-databaser. För IO-baserade arbets belastningar kan det vara möjligt att minska antalet virtuella kärnor i vCore-modellen för att uppnå samma prestanda nivå. Resurs begränsningar för DTU-och vCore-databaser i absoluta värden visas i vyn [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . Genom att jämföra de här värdena mellan DTU-databasen som ska migreras och en vCore-databas med ett cirka matchande tjänst mål kan du välja vCore-tjänstens mål mer precis.
- Mappnings frågan returnerar även mängden minne per kärna för DTU-databasen eller den elastiska poolen som ska migreras, och för varje maskin varu generation i vCore-modellen. Att se till att det finns ett liknande eller högre total minne efter migrering till vCore är viktigt för arbets belastningar som kräver en stor minnes data cache för att uppnå tillräckligt med prestanda eller arbets belastningar som kräver stora minnes bidrag för bearbetning av frågor. För sådana arbets belastningar, beroende på faktiska prestanda, kan det vara nödvändigt att öka antalet virtuella kärnor för att få tillräckligt med minne.
- Den [historiska resurs användningen](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) av DTU-databasen bör övervägas när du väljer vCore tjänst mål. DTU-databaser med konsekvent underutnyttjade processor resurser kan behöva färre virtuella kärnor än det antal som returneras av mappnings frågan. Däremot kan DTU-databaser där konsekvent hög processor belastning medför otillräckliga arbets belastnings prestanda kräva fler virtuella kärnor än vad som returneras av frågan.
- Om du migrerar databaser med tillfälliga eller oförutsägbara användnings mönster bör du överväga att använda [Server](serverless-tier-overview.md) lös beräknings nivå. Observera att max antalet samtidiga arbetare (begär Anden) i Server lös är 75% gränsen för allokerad beräkning för samma antal konfigurerade virtuella kärnor. Dessutom är Max tillgängligt minne i Server Lös 3 GB gånger det maximala antalet virtuella kärnor som kon figurer ATS. till exempel är maximalt minne 120 GB när 40 max virtuella kärnor har kon figurer ATS.   
- I vCore-modellen kan den maximala databas storleken som stöds variera beroende på maskin varu genereringen. För stora databaser kontrollerar du de maximala storlekarna som stöds i vCore-modellen för [enskilda databaser](resource-limits-vcore-single-databases.md) och [elastiska pooler](resource-limits-vcore-elastic-pools.md).
- För elastiska pooler har modellerna [DTU](resource-limits-dtu-elastic-pools.md) och [vCore](resource-limits-vcore-elastic-pools.md) skillnader i det högsta antalet databaser som stöds per pool. Detta bör övervägas när du migrerar elastiska pooler med många databaser.
- Vissa maskin varu generationer är kanske inte tillgängliga i varje region. Kontrol lera tillgängligheten under [maskin varu generationer](service-tiers-vcore.md#hardware-generations).

> [!IMPORTANT]
> De här rikt linjerna för DTU-vCore för storleks ändring ovan tillhandahålls för att hjälpa till med den första uppskattningen av mål databasens tjänst mål.
>
> Den optimala konfigurationen av mål databasen är arbets belastnings beroende. Därför kan det vara bra att uppnå optimal pris-/prestanda grad efter migreringen, så att du kan behöva dra nytta av vCore-modellens flexibilitet för att justera antalet virtuella kärnor, [maskin varu generering](service-tiers-vcore.md#hardware-generations), [tjänst-](service-tiers-vcore.md#service-tiers) och [beräknings](service-tiers-vcore.md#compute-tiers) nivåer samt justering av andra parametrar för databas konfiguration, till exempel [Maximal grad av parallellitet](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).
> 

### <a name="dtu-to-vcore-migration-examples"></a>Exempel på DTU till vCore-migrering

> [!NOTE]
> Värdena i exemplen nedan är endast för illustrations syfte. Faktiska värden som returneras i de scenarier som beskrivs kan vara olika.
> 

**Migrera en standard S9-databas**

Mappnings frågan returnerar följande resultat (vissa kolumner visas inte för det kortfattat):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|Gen5|5,40|16,800|7|24,000|5,05|

Vi ser att DTU-databasen har 24 logiska processorer (virtuella kärnor), med 5,4 GB minne per vCore och använder Gen5 maskin vara. Direkt matchning till det är en Generell användning 24 vCore-databas på Gen5-maskinvara, d.v.s. **GP_Gen5_24** vCore tjänst mål.

**Migrera en Standard S0-databas**

Mappnings frågan returnerar följande resultat (vissa kolumner visas inte för det kortfattat):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Gen4|0,42|0.250|7|0.425|5,05|

Vi ser att DTU-databasen motsvarar 0,25 logiska processorer (virtuella kärnor), med 0,42 GB minne per vCore och använder Gen4 maskin vara. De minsta vCore-tjänst målen i Gen4-och Gen5-maskin varu generationer, **GP_Gen4_1** och **GP_Gen5_2**, ger fler beräknings resurser än Standard-S0-databasen, så en direkt matchning är inte möjlig. Eftersom Gen4 maskin vara inaktive [ras är](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)alternativet **GP_Gen5_2** lämpligt. Om arbets belastningen passar bra för [Server](serverless-tier-overview.md) lös beräknings nivån är **GP_S_Gen5_1** dessutom en närmare motsvarighet.

**Migrera en Premium p15-databas**

Mappnings frågan returnerar följande resultat (vissa kolumner visas inte för det kortfattat):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|Gen5|4,86|29,400|7|42,000|5,05|

Vi ser att DTU-databasen har 42 logiska processorer (virtuella kärnor), med 4,86 GB minne per vCore och använder Gen5 maskin vara. Även om det inte finns något vCore tjänst mål med 42 kärnor, är det **BC_Gen5_40** tjänst målet mycket nära både vad gäller processor-och minnes kapacitet och är en bra matchning.

**Migrera en elastisk pool för en enkel 200-eDTU**

Mappnings frågan returnerar följande resultat (vissa kolumner visas inte för det kortfattat):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4,00|Gen5|5,40|2,800|7|4,000|5,05|

Vi ser att den elastiska DTU-poolen har 4 logiska processorer (virtuella kärnor), med 5,4 GB minne per vCore och använder Gen5 maskin vara. Den direkta matchningen i vCore-modellen är en **GP_Gen5_4** elastisk pool. Detta tjänst mål har dock stöd för högst 200 databaser per pool, medan den elastiska poolen på Basic 200 eDTU stöder upp till 500-databaser. Om den elastiska poolen som ska migreras har fler än 200 databaser måste det matchande vCore-tjänst målet vara **GP_Gen5_6**, som har stöd för upp till 500-databaser.

## <a name="migrate-geo-replicated-databases"></a>Migrera geo-replikerade databaser

Migrering från den DTU-baserade modellen till den vCore-baserade inköps modellen liknar att uppgradera eller nedgradera geo-replikeringsrelationen mellan databaser på standard-och premium-tjänst nivåerna. Under migreringen behöver du inte stoppa geo-replikering, men du måste följa dessa ordningsföljds regler:

- När du uppgraderar måste du uppgradera den sekundära databasen först och sedan uppgradera den primära databasen.
- Ändra ordning när du nedgraderar ordningen: du måste nedgradera den primära databasen först och sedan nedgradera den sekundära.

När du använder geo-replikering mellan två elastiska pooler rekommenderar vi att du anger en pool som primär och den andra som den sekundära. I så fall bör du använda samma rikt linjer när du migrerar elastiska pooler. Men om du har elastiska pooler som innehåller både primära och sekundära databaser, behandlar poolen med den högre användningen som primär och följer ordningsföljden för ordningsföljd.  

Följande tabell innehåller vägledning för olika scenarier för migrering:

|Aktuell tjänst nivå|Mål tjänst nivå|Typ av migrering|Användaråtgärder|
|---|---|---|---|
|Standard|Generellt syfte|Lateral|Kan migrera i vilken ordning som helst, men måste säkerställa lämplig vCore storlek enligt beskrivningen ovan|
|Premium|Affärskritisk|Lateral|Kan migrera i vilken ordning som helst, men måste säkerställa lämplig vCore storlek enligt beskrivningen ovan|
|Standard|Affärskritisk|Uppgradera|Måste migrera sekundär första|
|Affärskritisk|Standard|Nedgradera|Måste migrera primär första|
|Premium|Generellt syfte|Nedgradera|Måste migrera primär första|
|Generellt syfte|Premium|Uppgradera|Måste migrera sekundär första|
|Affärskritisk|Generellt syfte|Nedgradera|Måste migrera primär första|
|Generellt syfte|Affärskritisk|Uppgradera|Måste migrera sekundär första|
||||

## <a name="migrate-failover-groups"></a>Migrera redundansväxla grupper

Migrering av failover-grupper med flera databaser kräver en individuell migrering av de primära och sekundära databaserna. Under den processen gäller samma överväganden och ordningsföljd regler. När databaserna har konverterats till den vCore-baserade inköps modellen, kommer gruppen för redundans att gälla med samma princip inställningar.

### <a name="create-a-geo-replication-secondary-database"></a>Skapa en sekundär databas för geo-replikering

Du kan skapa en sekundär databas för geo-replikering (endast en geo-sekundär) med samma tjänst nivå som du använde för den primära databasen. För databaser med hög logg skapande frekvens rekommenderar vi att du skapar geo-Secondary med samma beräknings storlek som den primära.

Om du skapar en geo-sekundär i den elastiska poolen för en enda primär databas kontrollerar du att `maxVCore` inställningen för poolen matchar den primära databasens beräknings storlek. Om du skapar en geo-Secondary för en primär i en annan elastisk pool, rekommenderar vi att poolerna har samma `maxVCore` Inställningar.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Använd databas kopiering för att migrera från DTU till vCore

Du kan kopiera alla databaser med en DTU-baserad beräknings storlek till en databas med en vCore beräknings storlek utan begränsningar eller särskilda sekvenser så länge mål beräknings storleken har stöd för den maximala databas storleken för käll databasen. Databas kopian skapar en ögonblicks bild av data från den tidpunkt då kopieringen startar och synkroniserar inte data mellan källan och målet.

## <a name="next-steps"></a>Nästa steg

- De angivna beräknings storlekarna och lagrings storleks alternativen för enskilda databaser finns i [SQL Database vCore resurs gränser för enskilda databaser](resource-limits-vcore-single-databases.md).
- De angivna beräknings storlekarna och lagrings storleks alternativen för elastiska pooler finns i [SQL Database vCore resurs gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md).