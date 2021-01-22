---
title: Läs frågor om repliker
description: Azure SQL ger möjlighet att använda kapaciteten hos skrivskyddade repliker för Läs arbets belastningar, som kallas Läs-och utskalning.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/20/2021
ms.openlocfilehash: 5f9e7e1c96db2b60e41fe0ded69ea562cf8fcea6
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663993"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Använd skrivskyddade repliker för att avlasta skrivskyddade arbets belastningar
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Som en del av [arkitekturen för hög tillgänglighet](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)etablerades varje enskild databas, Elastic pool-databas och hanterad instans i Premium-och affärskritisk tjänst nivån automatiskt med en primär Read-Write-replik och flera sekundära skrivskyddade repliker. De sekundära replikerna är etablerade med samma beräknings storlek som den primära repliken. Med funktionen *Läs skalbar* kan du avlasta skrivskyddade arbets belastningar med beräknings kapaciteten för en av de skrivskyddade replikerna, i stället för att köra dem på den skrivskyddade repliken. På så sätt kan vissa skrivskyddade arbets belastningar isoleras från de Läs-och skriv arbets belastningarna och påverkar inte deras prestanda. Funktionen är avsedd för program som innehåller logiskt åtskilda skrivskyddade arbets belastningar, till exempel analys. På tjänst nivåerna Premium och Affärskritisk kan program få prestanda för delar med denna ytterligare kapacitet utan extra kostnad.

Funktionen för *Läs skalning* är också tillgänglig i den storskaliga tjänst nivån när minst en sekundär replik skapas. Flera sekundära repliker kan användas för skrivskyddade belastnings Utjämnings arbets belastningar som kräver fler resurser än vad som är tillgängligt på en sekundär replik.

Hög tillgänglighets arkitekturen för nivåerna Basic, standard och Generell användning omfattar inte några repliker. Funktionen för *Läs skalning* är inte tillgänglig i dessa tjänst nivåer.

I följande diagram illustreras funktionen.

![Skrivskyddade repliker](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

Funktionen *Läs skalning* är aktive rad som standard på nya Premium-, affärskritisk-och storskaliga databaser. För storskaliga skapas en sekundär replik som standard för nya databaser. 

> [!NOTE]
> Läs skalning är alltid aktiverat i Affärskritisk tjänst nivå för hanterad instans.

Om SQL-anslutningssträngen är konfigurerad med `ApplicationIntent=ReadOnly` omdirigeras programmet till en skrivskyddad replik av databasen eller en hanterad instans. Information om hur du använder `ApplicationIntent` egenskapen finns i [Ange program avsikt](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Om du vill säkerställa att programmet ansluter till den primära repliken oavsett `ApplicationIntent` inställningen i SQL-anslutningssträngen, måste du explicit inaktivera Läs-och utskalning när du skapar databasen eller när du ändrar konfigurationen. Om du till exempel uppgraderar din databas från standard-eller Generell användning-nivån till Premium, Affärskritisk eller nivån på den storskaliga nivån och vill se till att alla dina anslutningar fortsätter att gå till den primära repliken inaktiverar du Läs-och utskalning. Mer information om hur du inaktiverar det finns i [Aktivera och inaktivera Läs skalning](#enable-and-disable-read-scale-out).

> [!NOTE]
> Funktionerna Query Store och SQL profiler stöds inte för skrivskyddade repliker. 

## <a name="data-consistency"></a>Datakonsekvens

En av fördelarna med repliker är att replikerna alltid är i ett transaktions konsekvent tillstånd, men vid olika tidpunkter kan det uppstå en liten fördröjning mellan olika repliker. Läs skalbarhet stöder konsekvens på sessionsläge. Det innebär att om den skrivskyddade sessionen återansluter efter ett anslutnings fel på grund av otillgänglig replik, kan den omdirigeras till en replik som inte är 100% uppdaterad med den skrivskyddade repliken. På samma sätt gäller att om ett program skriver data med hjälp av en skrivbar session och läser den direkt med en skrivskyddad session, kanske de senaste uppdateringarna inte visas omedelbart på repliken. Svarstiden orsakas av en åtgärd för att göra om en asynkron transaktionslogg.

> [!NOTE]
> Replikering av fördröjningar i regionen är lågt och den här situationen är sällsynt. Information om hur du övervakar replikeringsfördröjning finns i [övervaka och felsöka skrivskyddad replikering](#monitoring-and-troubleshooting-read-only-replicas).

## <a name="connect-to-a-read-only-replica"></a>Anslut till en skrivskyddad replik

När du aktiverar Läs skalning för en databas, anger det `ApplicationIntent` alternativ i anslutnings strängen som klienten tillhandahåller om anslutningen dirigeras till Skriv repliken eller till en skrivskyddad replik. Om `ApplicationIntent` värdet är `ReadWrite` (standardvärdet) dirigeras anslutningen till Skriv-och skriv repliken. Detta är identiskt med beteendet när `ApplicationIntent` inte ingår i anslutnings strängen. Om `ApplicationIntent` värdet är `ReadOnly` dirigeras anslutningen till en skrivskyddad replik.

Följande anslutnings sträng ansluter till exempel klienten till en skrivskyddad replik (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Någon av följande anslutnings strängar ansluter klienten till en skrivskyddad replik (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Kontrol lera att en anslutning är till en skrivskyddad replik

Du kan kontrol lera om du är ansluten till en skrivskyddad replik genom att köra följande fråga i databasens kontext. Den kommer att returnera READ_ONLY när du är ansluten till en skrivskyddad replik.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> I Premium-och Affärskritisk-tjänst nivåerna är bara en av de skrivskyddade replikerna tillgängliga vid en specifik tidpunkt. Storskaligt stöder flera skrivskyddade repliker.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Övervakning och fel sökning av skrivskyddade repliker

När du är ansluten till en skrivskyddad replik visar DMV: er (Dynamic Management views) status för repliken och kan frågas om övervakning och fel söknings syfte. Databas motorn innehåller flera vyer för att visa en mängd olika övervaknings data. 

Ofta använda vyer är:

| Name | Syfte |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Tillhandahåller Mät värden för resursutnyttjande under den senaste timmen, inklusive CPU, data-IO och logg Skriv användning i förhållande till begränsningar i tjänst målet.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Tillhandahåller sammanställd wait-statistik för databas motor instansen. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Tillhandahåller hälso tillstånd för replik och synkronisering av statistik. Gör om kös Tor lek och gör om betygs ätt som indikatorer för data svars tid på den skrivskyddade repliken. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Tillhandahåller prestanda räknare för databas motorn.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Tillhandahåller körnings statistik per fråga, till exempel antal körningar, CPU-tid som använts osv.|
|[sys.dm_exec_query_plan ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Tillhandahåller cachelagrade fråge planer. |
|[sys.dm_exec_sql_text ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Tillhandahåller frågetext för en cachelagrad frågeplan.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Tillhandahåller förlopp för real tids fråga medan frågor körs.|
|[sys.dm_exec_query_plan_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Tillhandahåller den senast kända faktiska körnings planen, inklusive körnings statistik för en fråga.|
|[sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Tillhandahåller lagrings-IOPS, data flöde och svars tids statistik för alla databasfiler. |

> [!NOTE]
> - `sys.resource_stats` Och- `sys.elastic_pool_resource_stats` DMV: er i den logiska huvud databasen returnerar resurs användnings data för den primära repliken.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Övervaka skrivskyddade repliker med utökade händelser

Det går inte att skapa en Extended Event-session när den är ansluten till en skrivskyddad replik. I Azure SQL Database skapas och ändras definitionerna av en databas med [utökade händelse](xevent-db-diff-from-svr.md) sessioner som har skapats och ändrats på den primära repliken till skrivskyddade repliker, inklusive geo-repliker och avbildnings händelser på skrivskyddade repliker.

En Extended Event-session på en skrivskyddad replik som baseras på en partitionsdefinition från den primära repliken kan startas och stoppas oberoende av den primära repliken. När en Extended Event-session släpps på den primära repliken, släpps den också på alla skrivskyddade repliker.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Transaktions isolerings nivå för skrivskyddade repliker

Frågor som körs på skrivskyddade repliker mappas alltid till isolerings nivån för [ögonblicks bilds](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) transaktionen. Ögonblicks bild isolering använder rad versioner för att undvika att blockera scenarier där läsarna blockerar skribenter.

I sällsynta fall, om en ögonblicks bild isolerings transaktion har åtkomst till metadata för objekt som har ändrats i en annan samtidig transaktion, kan det ta emot fel [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error), "Det gick inte att skapa ögonblicks bild isolerings transaktion i databasen%. * ls eftersom objektet som används av instruktionen har ändrats av en DDL-instruktion i en annan samtidig transaktion sedan transaktionen startades. Detta är inte tillåtet, eftersom metadata saknar version. En samtidig uppdatering av metadata kan leda till inkonsekvens om den är blandad med ögonblicks bild isolering. "

### <a name="long-running-queries-on-read-only-replicas"></a>Tids krävande frågor om skrivskyddade repliker

Frågor som körs på skrivskyddade repliker behöver åtkomst till metadata för de objekt som refereras till i frågan (tabeller, index, statistik osv.) I sällsynta fall, om ett metadataobjekt ändras på den primära repliken medan en fråga har ett lås på samma objekt på den skrivskyddade repliken, kan frågan [blockera](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) processen som tillämpar ändringar från den primära repliken till den skrivskyddade repliken. Om en sådan fråga skulle köras under en längre tid skulle det innebära att den skrivskyddade repliken var betydligt ur synk med den primära repliken.

Om en tids krävande fråga på en skrivskyddad replik orsakar den här typen av blockering kommer den automatiskt att avslutas. Sessionen kommer att ta emot fel 1219, "din session har kopplats från på grund av en DDL-åtgärd med hög prioritet", eller fel 3947 ", avbröts av transaktionen eftersom den sekundära beräkningen inte kunde fångas upp upprepas. Försök att utföra transaktionen igen. "

> [!NOTE]
> Om du får fel meddelandet 3961, 1219 eller 3947 när du kör frågor mot en skrivskyddad replik kan du försöka köra frågan igen.

> [!TIP]
> När du är ansluten till en skrivskyddad replik i Premium-och Affärskritisk tjänst nivåerna `redo_queue_size` `redo_rate` kan kolumnerna i [sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV användas för att övervaka processen för synkronisering av data, och fungerar som indikatorer för data svars tiden på den skrivskyddade repliken.
> 

## <a name="enable-and-disable-read-scale-out"></a>Aktivera och inaktivera Läs skalning

Läs skalning är aktiverat som standard på Premium-, Affärskritisk-och Scale service-nivåer. Läs-och utskalning kan inte aktive ras på tjänst nivåerna Basic, standard eller Generell användning. Läs skalning är inaktiverat automatiskt på storskaliga databaser som kon figurer ATS med noll repliker.

Du kan inaktivera och återaktivera Läs utskalning på enkla databaser och Elastic pool-databaser i Premium-eller Affärskritisk tjänst nivåerna med följande metoder.

> [!NOTE]
> För enskilda databaser och Elastic pool-databaser, tillhandahålls möjligheten att inaktivera Läs skalning för bakåtkompatibilitet. Läs skalning kan inte inaktive ras på Affärskritisk hanterade instanser.

### <a name="azure-portal"></a>Azure Portal

Du kan hantera inställningen för Läs skalning på bladet **Konfigurera** databas.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande, men all framtida utveckling gäller AZ. SQL-modulen. Azure Resource Manager-modulen kommer att fortsätta att ta emot fel korrigeringar fram till minst december 2020.  Argumenten för kommandona i AZ-modulen och i Azure Resource Manager modulerna är i stort sett identiska. Mer information om deras kompatibilitet finns i [Introduktion till den nya Azure PowerShell AZ-modulen](/powershell/azure/new-azureps-module-az).

Hantering av Läs-och utskalning i Azure PowerShell kräver Azure PowerShells versionen december 2016 eller senare. Den senaste PowerShell-versionen finns i [Azure PowerShell](/powershell/azure/install-az-ps).

Du kan inaktivera eller återaktivera Läs-och utskalning i Azure PowerShell genom att anropa cmdleten [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) och skicka in det önskade värdet ( `Enabled` eller `Disabled` ) för `-ReadScale` parametern.

Så här inaktiverar du Läs skala ut i en befintlig databas (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Så här inaktiverar du Läs skala i en ny databas (ersätter objekten i vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Så här återaktiverar du Läs skalan för en befintlig databas (ersätter objekten inom vinkelparenteser med rätt värden för din miljö och släpper vinkelparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>REST-API

Om du vill skapa en databas med Läs skala inaktive rad, eller ändra inställningen för en befintlig databas, använder du följande metod med `readScale` egenskapen inställd på `Enabled` eller `Disabled` , som i följande exempel förfrågan.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Mer information finns i [databaser-skapa eller uppdatera](/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Använda `tempdb` databasen på en skrivskyddad replik

`tempdb`Databasen på den primära repliken replikeras inte till skrivskyddade repliker. Varje replik har en egen `tempdb` databas som skapas när repliken skapas. Detta säkerställer att `tempdb` kan uppdateras och kan ändras under frågekörningen. Om din skrivskyddade arbets belastning är beroende av att använda `tempdb` objekt bör du skapa dessa objekt som en del av frågans skript.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Använda Läs-och utskalning med geo-replikerade databaser

Geo-replikerade sekundära databaser har samma arkitektur för hög tillgänglighet som de primära databaserna. Om du ansluter till den geo-replikerade sekundära databasen med Läs skalning aktive rad dirigeras sessionerna med `ApplicationIntent=ReadOnly` till en av replikerna med hög tillgänglighet på samma sätt som de dirigeras till den primära skrivbara databasen. Sessionerna utan `ApplicationIntent=ReadOnly` kommer att dirigeras till den primära repliken av den geo-replikerade sekundära, som också är skrivskyddad. 

På det här sättet tillhandahåller en geo-replikering ytterligare två skrivskyddade repliker för en primär databas med Läs behörighet, för totalt tre skrivskyddade repliker. Varje ytterligare geo-replikering ger ett annat par av skrivskyddade repliker. Geo-repliker kan skapas i alla Azure-regioner, inklusive regionen för den primära databasen.

> [!NOTE]
> Det finns ingen automatisk resursallokering eller annan belastningsutjämnad routning mellan replikerna av en geo-replikerad sekundär databas.

## <a name="next-steps"></a>Nästa steg

- Mer information om SQL Database skalnings erbjudande finns i [storskalig Service Tier](service-tier-hyperscale.md).