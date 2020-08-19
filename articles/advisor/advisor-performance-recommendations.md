---
title: Förbättra prestanda för Azure Apps med Advisor
description: Använd prestanda rekommendationer i Azure Advisor för att förbättra hastighet och svars tider för affärs kritiska program.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bdca8cd39427fb0d25f8b3308eaf2be24e0eb81a
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257459"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Förbättra prestanda för Azure-program med hjälp av Azure Advisor

Prestanda rekommendationerna i Azure Advisor kan hjälpa till att förbättra hastighet och svars tider för affärs kritiska program. Du kan få prestanda rekommendationer från Advisor på fliken **prestanda** på instrument panelen för Advisor.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Minska DNS-tiden till Live i Traffic Managers profilen för att växla över till felfria slut punkter snabbare

Du kan använda [Inställningar för TTL (Time-to-Live)](../traffic-manager/traffic-manager-performance-considerations.md) på din Azure Traffic Manager-profil för att ange hur snabbt ska byta slut punkter om en specifik slut punkt slutar svara på frågor. Om du minskar TTL-värdena kommer klienterna att dirigeras till fungerande slut punkter snabbare.

Azure Advisor identifierar Traffic Manager profiler som har ett längre TTL-värde konfigurerat. Vi rekommenderar att du konfigurerar TTL till antingen 20 sekunder eller 60 sekunder, beroende på om profilen har kon figurer ATS för [snabb redundans](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>Förbättra databasens prestanda genom att använda SQL Database Advisor (tillfälligt inaktive rad)

Azure Advisor ger en enhetlig, sammanslagen översikt över rekommendationer för alla dina Azure-resurser. Den integreras med SQL Database Advisor för att få rekommendationer för att förbättra prestandan för dina databaser.SQL Database Advisor utvärderar prestanda för dina databaser genom att analysera användnings historiken. Den erbjuder sedan rekommendationer som passar bäst för att köra databasens typiska arbets belastning.

> [!NOTE]
> Innan du kan få rekommendationer måste databasen användas i ungefär en vecka och inom den veckan måste det vara en konsekvent aktivitet. SQL Database Advisor kan optimeras enklare för konsekventa fråge mönster än för slumpmässiga aktiviteter.

Mer information finns i [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Uppgradera ditt lagrings klient bibliotek till den senaste versionen för bättre tillförlitlighet och prestanda

Den senaste versionen av SDK: n för klient bibliotek innehåller korrigeringar av problem som rapporter ATS av kunder och som proaktivt har identifierats via vår frågor och svars process. Den senaste versionen ger också Tillförlitlighets-och prestanda optimering tillsammans med nya funktioner som kan förbättra den övergripande upplevelsen med att använda Azure Storage. Advisor ger rekommendationer och steg som krävs för att uppgradera till den senaste versionen av SDK om du använder en inaktuell version. Rekommendationerna är för språk som stöds: C++ och .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Förbättra App Service prestanda och tillförlitlighet

Azure Advisor integrerar rekommendationer för att förbättra din App Service upplevelse och identifiera relevanta plattforms funktioner. Exempel på App Service rekommendationer är:
* Identifiering av instanser där minnes-eller processor resurser förbrukas av app-runtime, med alternativ för att minska.
* Identifiering av instanser där samplacering av resurser som webbappar och databaser kan förbättra prestanda och minska kostnaderna.

Mer information finns i [metod tips för Azure App Service](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Använd Managed disks för att förhindra disk-I/O-begränsning

Advisor identifierar virtuella datorer som tillhör ett lagrings konto som når sitt skalbarhets mål. Det här tillståndet gör att de virtuella datorerna är utsatta för I/O-begränsning. Advisor rekommenderar att du använder hanterade diskar för att förhindra försämrade prestanda.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Förbättra prestanda och tillförlitlighet för virtuella dator diskar med hjälp av Premium Storage

Advisor identifierar virtuella datorer med standard diskar som har en stor mängd transaktioner på ditt lagrings konto. Vi rekommenderar att du uppgraderar till Premium diskar. 

Azure Premium Storage levererar stöd för hög prestanda, låg latens disk för virtuella datorer som kör I/O-intensiva arbets belastningar. Virtuella dator diskar som använder Premium Storage konto Arkiv data i solid-state-hårddiskar (SSD). För bästa prestanda för ditt program rekommenderar vi att du migrerar alla virtuella dator diskar som kräver hög IOPS till Premium Storage.

## <a name="remove-data-skew-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Ta bort data skevningen i SQL Data Warehouse tabeller för att öka prestandan för frågor

Data skevning kan orsaka onödig data förflyttning eller Flask halsar i resurser när du kör arbets belastningen. Advisor identifierar fördelnings data skevning på mer än 15%. Vi rekommenderar att du distribuerar om dina data och återgår till dina val av tabell distributions nyckel. Mer information om hur du identifierar och tar bort skevning finns i [Felsöka skevning](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skapa eller uppdatera gammal tabell statistik i SQL Data Warehouse tabeller för att öka prestanda för frågor

Advisor identifierar tabeller som inte har aktuell [tabell statistik](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) och rekommenderar att du skapar eller uppdaterar statistiken. Frågans optimering i Azure SQL Data Warehouse använder uppdaterad statistik för att beräkna kardinalitet eller antal rader i frågeresultat. Dessa uppskattningar gör det möjligt för Query Optimering att skapa en frågeplan för att ge snabbast prestanda.

## <a name="improve-mysql-connection-management"></a>Förbättra hanteringen av MySQL-anslutningar

Advisor-analys kan indikera att ditt program som ansluter till en MySQL-server kanske inte hanterar anslutningar effektivt. Det här tillståndet kan leda till onödig resursförbrukning och övergripande program svars tid. För att förbättra anslutningshanteringen rekommenderar vi att du minskar antalet kortvariga anslutningar och eliminerar onödiga inaktiva anslutningar. Du kan göra dessa förbättringar genom att konfigurera en server sidans anslutningspool, som ProxySQL.


## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Skala upp för att optimera användningen av cachen på dina SQL Data Warehouse tabeller för att öka prestanda för frågor

Azure Advisor identifierar om dina SQL Data Warehouse tabeller har en hög cache-Använd procent andel och en låg procent andel. Det här villkoret indikerar hög cache-avlägsnande, vilket kan påverka prestandan för SQL Data Warehouse-instansen. Advisor rekommenderar att du skalar upp SQL Data Warehouse-instansen så att du kan allokera tillräckligt med cache-kapacitet för din arbets belastning.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konvertera SQL Data Warehouse tabeller till replikerade tabeller för att öka prestanda för frågor

Advisor identifierar tabeller som inte är replikerade tabeller, men som skulle ha nytta av konverteringen. Det föreslår att du konverterar dessa tabeller. Rekommendationerna baseras på:
- Storleken på den replikerade tabellen. 
- Antalet kolumner. 
- Tabell distributions typen. 
- Antalet partitioner i SQL Data Warehouses tabellen. 

Ytterligare heuristik kan ges i rekommendationen för Context. Mer information om hur den här rekommendationen fastställs finns [SQL Data Warehouse rekommendationer](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Migrera ditt lagrings konto till Azure Resource Manager för att få de senaste Azure-funktionerna

Migrera lagrings kontots distributions modell till Azure Resource Manager för att dra nytta av:
- Mall distributioner.
- Ytterligare säkerhets alternativ.
- Möjlighet att uppgradera till ett GPv2-konto så att du kan använda de senaste Azure Storage funktionerna. 

Advisor identifierar alla fristående lagrings konton som använder den klassiska distributions modellen och rekommenderar att du migrerar till distributions modellen för Resource Manager.

> [!NOTE]
> Klassiska varningar i Azure Monitor drogs tillbaka i augusti 2019. Vi rekommenderar att du uppgraderar det klassiska lagrings kontot så att du kan använda Resource Manager för att behålla aviserings funktionen med den nya plattformen. Mer information finns i [indragningen av klassiska aviseringar](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Utforma dina lagrings konton för att förhindra att den maximala prenumerations gränsen uppnås

En Azure-region stöder högst 250 lagrings konton per prenumeration. När gränsen har nåtts kan du inte skapa lagrings konton i denna region/prenumerations kombination. Advisor kontrollerar dina prenumerationer och ger rekommendationer för att utforma för färre lagrings konton för alla prenumerationer/regioner som är nära att nå Max gränsen.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Överväg att öka storleken på din VPN Gateway SKU för att hantera hög P2S användning

Varje Azure VPN Gateway SKU har endast stöd för ett angivet antal samtidiga P2S-anslutningar. Om antalet anslutningar ligger nära din gateway-gräns kan ytterligare anslutnings försök Miss lyckas. Om du ökar storleken på din gateway kan du stödja fler samtidiga P2S-användare. Advisor ger rekommendationer och instruktioner för att öka storleken på din gateway.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Överväg att öka storleken på din VPN Gateway SKU för att hantera hög CPU

Under hög trafik belastning kan din VPN-gateway släppa paket på grund av hög CPU. Överväg att uppgradera VPN Gateway SKU: n. Om du ökar storleken på din VPN-gateway ser du till att anslutningarna inte bryts på grund av hög CPU. Advisor ger rekommendationer för att proaktivt lösa det här problemet. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Öka batchstorleken vid inläsning för att maximera dataflöde, datakomprimering och frågeprestanda

Advisor identifierar om du kan öka belastnings prestanda och data flöde genom att öka batchstorleken vid inläsning i databasen. Du kan överväga att använda KOPIERINGs instruktionen. Om du inte kan använda KOPIERINGs instruktionen bör du överväga att öka batchstorleken när du använder inläsnings verktyg som SQLBulkCopy-API eller BCP. En lämplig allmän regel är att använda en batchstorlek på mellan 100 000 och 1 000 000 rader. Att öka batchstorleken ökar belastnings data flödet, data komprimering och frågans prestanda.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Samplacera lagrings kontot i samma region för att minimera svars tiden vid inläsning

Advisor identifierar om du läser in från en annan region än SQL-poolen. Överväg att läsa in från ett lagrings konto som är i samma region som SQL-poolen för att minimera svars tiden vid inläsning av data. Den här ändringen bidrar till att minimera svars tiden och öka belastnings prestandan.

## <a name="use-a-supported-kubernetes-version"></a>Använd en Kubernetes-version som stöds

Advisor identifierar versioner av Kubernetes som inte stöds.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Optimera prestanda för dina Azure Database for MySQL, Azure Database for PostgreSQL och Azure Database for MariaDB-servrar

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Åtgärda processor belastningen för Azure Database for MySQL, Azure Database for PostgreSQL och Azure Database for MariaDB servrar med processor Flask halsar
Hög användning av processorn under en längre period kan orsaka långsamma prestanda för frågor för din arbets belastning. Att öka processor storleken bidrar till att optimera körningen av databas frågorna och förbättra den övergripande prestandan. Advisor identifierar servrar med hög processor användning som troligen kör CPU-begränsade arbets belastningar och rekommenderar att du skalar beräkningen.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Minska minnes begränsningar på Azure Database for MySQL, Azure Database for PostgreSQL och Azure Database for MariaDB servrar eller flytta till en Minnesoptimerade SKU
Ett lågt cacheträffar kan resultera i sämre prestanda för frågor och ökad IOPS. Det här tillståndet kan orsakas av en dålig frågeplan eller en minnes intensiv arbets belastning. Om du korrigerar en frågeplan eller [ökar minnet](../postgresql/concepts-pricing-tiers.md) för Azure Database for PostgreSQL, Azure Database for MySQL eller Azure Database for MariaDB servern kan du optimera körningen av databasens arbets belastning. Azure Advisor identifierar servrar som påverkas av den här omsättningen för den stora bufferten. Vi rekommenderar att du vidtar följande åtgärder: 
- Åtgärda frågeuttrycket
- Flytta till en SKU som har mer minne 
- Öka lagrings utrymmet för att få mer IOPS.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Använd en Azure Database for MySQL eller Azure Database for PostgreSQL Läs replik för att skala upp läsningar för Läs intensiva arbets belastningar
Advisor använder arbetsbaserade heuristik som förhållandet mellan läsningar och skrivningar på servern under de senaste sju dagarna för att identifiera Läs intensiva arbets belastningar. En Azure Database for PostgreSQL-eller Azure Database for MySQL resurs med högt Läs/skriv-förhållande kan leda till processor-eller minnes innehåll och leda till långsam frågans prestanda. Om du lägger till en [replik](../postgresql/howto-read-replicas-portal.md) kan du skala upp läsningar till replik servern och förhindra processor-eller minnes begränsningar på den primära servern. Advisor identifierar servrar med Läs intensiva arbets belastningar och rekommenderar att du lägger till en [Läs replik](../postgresql/concepts-read-replicas.md)   för att avlasta några av de Läs arbets belastningarna.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skala din Azure Database for MySQL, Azure Database for PostgreSQL eller Azure Database for MariaDB server till en högre SKU för att förhindra anslutnings begränsningar
Varje ny anslutning till databas servern upptar minne. Databas serverns prestanda försämras om anslutningar till servern inte kan utföras på grund av en [övre gräns](../postgresql/concepts-limits.md) i minnet. Azure Advisor identifierar servrar som kör med många anslutnings problem. Vi rekommenderar att du uppgraderar serverns anslutnings gränser för att ge mer minne till servern genom att vidta någon av följande åtgärder:
- Skala upp beräkning. 
- Använd Minnesoptimerade SKU: er, som har mer data bearbetning per kärna.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skala cacheminnet till en annan storlek eller SKU för att förbättra cache-och program prestanda

Cache-instanser fungerar bäst när de inte körs under hög minnes belastning, hög server belastning eller hög nätverks bandbredd. Dessa villkor kan orsaka att de inte svarar, upplever data förlust eller blir otillgängliga. Advisor identifierar cache-instanser i dessa villkor. Vi rekommenderar att du vidtar följande åtgärder:
- Använd metod tips för att minska minnes trycket, Server belastningen eller nätverks bandbredden.
- Skala till en annan storlek eller SKU som har mer kapacitet.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Lägg till regioner med trafik till ditt Azure Cosmos DB-konto

Advisor identifierar Azure Cosmos DB konton som har trafik från en region som inte är konfigurerad för tillfället. Vi rekommenderar att du lägger till den regionen. Detta förbättrar svars tiden för begär Anden som kommer från den regionen och garanterar tillgängligheten i händelse av region avbrott. [Läs mer om global data distribution med Azure Cosmos DB.](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Konfigurera Azure Cosmos DB indexerings princip med hjälp av egna inkluderade eller undantagna sökvägar

Advisor identifierar Azure Cosmos DB behållare som använder standard indexerings principen, men kan dra nytta av en anpassad indexerings princip. Den här bestämningen baseras på arbets belastnings mönstret. Standard indexerings principen indexerar alla egenskaper. En anpassad indexerings princip med explicit inkluderade eller undantagna sökvägar som används i Frågefilter kan minska ru: er och lagring som förbrukas för indexering. [Läs mer om hur du ändrar index principer.](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Ange sid storlek för Azure Cosmos DB (MaxItemCount) till-1 

Azure Advisor identifierar Azure Cosmos DB behållare som använder en sid storlek på 100. Vi rekommenderar att du använder en sid storlek på-1 för snabbare genomsökningar. [Läs mer om MaxItemCount.](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>Överväg att använda accelererade skrivningar i ditt HBase-kluster för att förbättra kluster prestanda
Azure Advisor analyserar system loggarna under de senaste 7 dagarna och identifierar om ditt kluster har påträffat följande scenarier:
1. Långa svarstider för WAL-synkroniseringstid 
2. Högt antal skrivbegäranden (minst 3 fönster på en timme med över 1 000 avg_write_requests/second/node – genomsnittliga skrivbegäranden/sekund/nod)

Dessa tillstånd är tecken på att klustret lider av långa skrivningssvarstider. Detta kan bero på att tung belastning utförs på klustret. För att förbättra klustrets prestanda kanske du vill överväga att använda de accelererade Skriv funktioner som tillhandahålls av Azure HDInsight HBase. Funktionen för accelererade skrivningar för HDInsight Apache HBase-kluster ansluter Premium SSD-hanterade diskar till varje RegionServer (arbetsnod) i stället för att använda molnlagring. Tack vare det får du kort skrivningssvarstid och bättre återhämtning för dina program. Läs mer om den här funktionen i [Mer information](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes#how-to-enable-accelerated-writes-for-hbase-in-hdinsight)

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>Granska Azure Datautforskaren Table cache-period (princip) för bättre prestanda (för hands version)
Den här rekommendationen visar Azure Data Explorer-tabeller som har ett stort antal frågor som går tillbaka utöver den konfigurerade cacheperioden (principen) (du kommer att se de 10 främsta tabellerna efter frågeprocent som har åtkomst till data utöver cacheperioden). Den rekommenderade åtgärden för att förbättra klustrets prestanda: Begränsa frågor för den här tabellen till det minsta tidsintervallet som krävs (inom den definierade principen). Om data från hela tidsintervallet krävs ökar du cacheperioden till det rekommenderade värdet.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>Förbättra prestanda genom att optimera storleken på temporära MySQL-tabeller
Advisor-analysen anger att MySQL-servern kan bli onödigt I/O-overhead på grund av en låg temporär tabell parameter inställningar. Det här kan leda till onödiga diskbaserade transaktioner och sämre prestanda. Vi rekommenderar att du ökar värdena för parametrarna ”tmp_table_size” och ”max_heap_table_size” så att du minskar antalet diskbaserade transaktioner. [Läs mer](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Distribuera data i Server gruppen för att distribuera arbets belastningen mellan noder
Advisor identifierar de Server grupper där data inte har distribuerats utan kvar på koordinatorn. Baserat på detta rekommenderar Advisor att för full citus-förmåner distribuerar data på arbetsnoder för dina Server grupper. Detta förbättrar prestanda för frågor genom att använda resursen för varje nod i Server gruppen. [Läs mer](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Få åtkomst till prestanda rekommendationer i Advisor

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna [Advisor](https://aka.ms/azureadvisordashboard).

2.  På instrument panelen för Advisor väljer du fliken **prestanda** .

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:

* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer i Advisor](advisor-cost-recommendations.md)
* [Rekommendationer om Advisor-tillförlitlighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
