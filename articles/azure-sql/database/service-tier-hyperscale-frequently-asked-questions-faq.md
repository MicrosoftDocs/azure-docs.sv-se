---
title: Vanliga frågor och svar om Azure SQL Database storskalig
description: Svar på vanliga frågor som kunder ställer om en databas i SQL Database i den storskaliga tjänst nivån – vanligt vis kallas för en storskalig databas.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 02/03/2021
ms.openlocfilehash: c6faa1703b0935d66d291281f33027b3a66a59d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526830"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Vanliga frågor och svar om Azure SQL Database storskalig
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln innehåller svar på vanliga frågor och svar om kunder som överväger en databas i Azure SQL Database storskalig tjänst nivå, som kallas bara för skala i resten av vanliga frågor och svar. I den här artikeln beskrivs de scenarier som stöds av storskaliga funktioner och funktioner som är kompatibla med storskalighet.

- Vanliga frågor och svar är avsedda för läsare som har en kort förståelse för den storskaliga tjänst nivån och som vill ha sina egna frågor och frågor.
- Dessa vanliga frågor och svar är inte avsedda att vara Guidebook eller besvara frågor om hur du använder en storskalig databas. För en introduktion till storskaligheten rekommenderar vi att du läser [Azure SQL Database storskalig](service-tier-hyperscale.md) dokumentation.

## <a name="general-questions"></a>Allmänna frågor

### <a name="what-is-a-hyperscale-database"></a>Vad är en storskalig databas?

En storskalig databas är en databas i SQL Database i den storskaliga tjänst nivån som backas upp av den storskaliga lagrings tekniken. En storskalig databas har stöd för upp till 100 TB data och ger hög genom strömning och prestanda, samt snabb skalning för att anpassa sig till arbets belastnings kraven. Skalning är transparent för program – anslutning, fråga bearbetning osv. fungerar som alla andra databaser i Azure SQL Database.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Vilka resurs typer och inköps modeller stöder storskalig skalning

Den storskaliga Service nivån är endast tillgänglig för enskilda databaser med den vCore-baserade inköps modellen i Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Hur skiljer sig den storskaliga tjänst nivån från Generell användning och Affärskritisk tjänst nivåerna

VCore-baserade tjänst nivåer särskiljs baserat på databasens tillgänglighet och lagrings typ, prestanda och maximal storlek, enligt beskrivningen i följande tabell.

| | Resurstyp | Generell användning |  Hyperskala | Affärskritisk |
|:---:|:---:|:---:|:---:|:---:|
| **Bäst för** |Alla|Erbjuder budget orienterade balanserade beräknings-och lagrings alternativ.|De flesta företags arbets belastningar. Automatisk skalning av lagrings utrymme på upp till 100 TB, fast lodrät och vågrät beräknings skalning, snabb databas återställning.|OLTP-program med hög transaktions frekvens och låg IO-latens. Erbjuder högsta möjliga återhämtning till fel och snabba växlingar med hjälp av flera synkront uppdaterade repliker.|
|  **Resurstyp** ||SQL Database-/SQL-hanterad instans | Enskild databas | SQL Database-/SQL-hanterad instans |
| **Beräknings storlek**|SQL Database * | 1 till 80 virtuella kärnor | 1 till 80 virtuella kärnor * | 1 till 80 virtuella kärnor |
| **Beräknings storlek**|SQL-hanterad instans | 8, 16, 24, 32, 40, 64, 80 virtuella kärnor | Ej tillämpligt | 8, 16, 24, 32, 40, 64, 80 virtuella kärnor |
| **Lagringstyp** | Alla |Premium-Fjärrlagring (per instans) | Fristående lagring med lokal SSD-cache (per instans) | Super-fast lokal SSD-lagring (per instans) |
| **Lagrings storlek** | SQL Database *| 5 GB – 4 TB | Upp till 100 TB | 5 GB – 4 TB |
| **Lagrings storlek** | SQL-hanterad instans  | 32 GB – 8 TB | Ej tillämpligt | 32 GB – 4 TB |
| **IOPS** | Enskild databas | 500 IOPS per vCore med 7000 maximal IOPS | Hög skalning är en arkitektur med flera nivåer med cachelagring på flera nivåer. Effektiv IOPS är beroende av arbets belastningen. | 5000 IOPS med 200 000 högsta IOPS|
| **IOPS** | SQL-hanterad instans | Beror på fil storlek | Ej tillämpligt | 1375 IOPS/vCore |
|**Tillgänglighet**|Alla|1 replik, ingen Läs skalning, ingen lokal cache | Flera repliker, upp till 4 Läs skalbarhet, delvis lokal cache | 3 repliker, 1 Läs-och utskalning, zon-redundanta HA, fullständig lokal lagring |
|**Säkerhetskopior**|Alla|RA-GRS, 7-35 dag kvarhållning (7 dagar som standard)| RA-GRS, 7 dagars kvarhållning, konstant Time-Time-återställning (PITR) | RA-GRS, 7-35 dag kvarhållning (7 dagar som standard) |

\* Elastiska pooler stöds inte i den storskaliga tjänst nivån

### <a name="who-should-use-the-hyperscale-service-tier"></a>Vem ska använda den storskaliga tjänst nivån

Den storskaliga Service nivån är avsedd för kunder som har stora lokala SQL Server databaser och vill modernisera sina program genom att flytta till molnet, eller för kunder som redan använder Azure SQL Database och vill öka risken för databas tillväxt. Hög skalning är också avsett för kunder som söker både höga prestanda och hög skalbarhet. Med storskalighet får du:

- Databasens storlek upp till 100 TB
- Snabba databas säkerhets kopieringar oavsett databas storlek (säkerhets kopior baseras på lagrings ögonblicks bilder)
- Snabba databaser återställs oavsett databas storlek (återställningar från lagrings ögonblicks bilder)
- Högre logg data flöde, oavsett databasens storlek och antalet virtuella kärnor
- Läs skalbarhet med en eller flera skrivskyddade repliker, som används för Läs avlastning och som snabb växling.
- Snabbt skala upp till beräkning, i konstant tid, som är mer kraftfullt för att hantera den stora arbets belastningen och sedan skala ned, i konstant tid. Detta liknar att skala upp och ned mellan en P6 och en p11, till exempel, men mycket snabbare eftersom detta inte är en storlek på data åtgärd.

### <a name="what-regions-currently-support-hyperscale"></a>Vilka regioner som för närvarande stöder storskalighet

Den storskaliga Service nivån är för närvarande tillgänglig i de regioner som anges under [Azure SQL Database översikt över flera nivåer](service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>Kan jag skapa flera storskaliga databaser per server

Ja. Mer information och begränsningar för antalet storskaliga databaser per server finns i [SQL Database resurs gränser för enskilda databaser och databaser på en server](resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Vad är prestanda egenskaperna för en storskalig databas

Den storskaliga arkitekturen ger hög prestanda och data flöde och stöder stora databas storlekar.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Vad är skalbarheten för en storskalig databas

Med storskalig skalbarhet får du snabb skalbarhet baserat på ditt arbets belastnings behov.

- **Skala upp/ned**

  Med storskalig skalning kan du skala upp den primära beräknings storleken i termer av resurser som processor och minne, och sedan skala ned, i konstant tid. Eftersom lagrings utrymmet är delat är skalning och skalning ned inte en storlek på data åtgärd.  
- **Skala in/ut**

  Med storskalighet får du också möjlighet att etablera en eller flera ytterligare beräknings repliker som du kan använda för att hantera dina Läs begär Anden. Det innebär att du kan använda dessa ytterligare beräknings repliker som skrivskyddade repliker för att avlasta din Läs arbets belastning från den primära beräkningen. Förutom skrivskyddade, fungerar dessa repliker också som frekventa vänte läge i händelse av en redundansväxling från den primära.

  Etableringen av var och en av dessa ytterligare beräknings repliker kan göras i konstant tid och är en online-åtgärd. Du kan ansluta till dessa ytterligare skrivskyddade beräknings repliker genom att ange `ApplicationIntent` argumentet för anslutnings strängen till `ReadOnly` . Alla anslutningar med `ReadOnly` program avsikten dirigeras automatiskt till en av de ytterligare skrivskyddade beräknings replikerna.

## <a name="deep-dive-questions"></a>Djupgående frågor

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>Kan jag blanda storskalig och enstaka databaser på en enda server

Ja, det kan du.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Kräver storskalig min programmerings modell för att ändra

Nej, din programmerings modell finns kvar. Du använder anslutnings strängen som vanligt och de andra vanliga sätten att interagera med din storskaliga databas.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Vilken transaktions isolerings nivå är standard i en storskalig databas

På den primära repliken är standard isolerings nivån för transaktioner RCSI (Read Committed ögonblicks bild isolering). På de sekundära replikerna för Läs skalning, är standard isolerings nivån ögonblicks bild.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Kan jag använda min lokala eller IaaS SQL Server-licens till storskalig

Ja, [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) är tillgängligt för storskaliga. Varje SQL Server Standard kärna kan mappas till 1 storskalig virtuella kärnor. Varje SQL Server Enterprise kärna kan mappas till 4 virtuella kärnor. Du behöver inte en SQL-licens för sekundära repliker. Azure Hybrid-förmån priset används automatiskt för att läsa skalnings-(sekundär) repliker.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Vilken typ av arbets belastningar är storskaligt utformade för

Storskaligt stöder alla SQL Server arbets belastningar, men den är främst optimerad för OLTP. Du kan också lägga till hybrid-och analys-och data mart-arbetsbelastningar.

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Hur kan jag välja mellan Azure Synapse Analytics och Azure SQL Database storskalig

Om du kör interaktiva analys frågor med SQL Server som ett informations lager är storskaligt ett bra alternativ eftersom du kan vara värd för små och medel stora informations lager (till exempel några TB upp till 100 TB) till en lägre kostnad, och du kan migrera dina SQL Server data lager arbets belastningar till skala med minimala T-SQL-kod ändringar.

Om du kör data analys i stor skala med komplexa frågor och varaktiga inmatnings priser som är högre än 100 MB/s, eller använder Parallel Data Warehouse (PDW), Teradata eller andra data lager för massivt parallell bearbetning (MPP), kan Azure Synapse Analytics vara det bästa valet.
  
## <a name="hyperscale-compute-questions"></a>Storskaliga beräknings frågor

### <a name="can-i-pause-my-compute-at-any-time"></a>Kan jag pausa min data bearbetning när som helst

Inte just nu, men du kan skala din beräkning och antalet repliker nedåt för att minska kostnaderna under låg belastnings tider.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Kan jag etablera en beräknings replik med extra RAM-minne för min minnes intensiv arbets belastning

Nej. Om du vill ha mer RAM-minne måste du uppgradera till en högre beräknings storlek. Mer information finns i [storskalig lagring och beräknings storlekar](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Kan jag etablera flera beräknings repliker av olika storlekar

Nej.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Hur många Läs skalbara repliker som stöds

De storskaliga databaserna skapas med en skalbar replik (två repliker inklusive primär) som standard. Du kan skala antalet skrivskyddade repliker mellan 0 och 4 med hjälp av [Azure Portal](https://portal.azure.com) eller [REST API](/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>För hög tillgänglighet måste jag etablera ytterligare beräknings repliker

I storskaliga databaser tillhandahålls data återhämtning på lagrings nivå. Du behöver bara en replik för att tillhandahålla återhämtning. När beräknings repliken är avstängd skapas en ny replik automatiskt utan data förlust.

Men om det bara finns en replik kan det ta en stund att skapa den lokala cachen i den nya repliken efter redundansväxlingen. Under fasen för cache-återuppbyggnad hämtar databasen data direkt från sidan servrar, vilket resulterar i högre svars tider för lagring och försämrade frågor.

För verksamhets kritiska appar som kräver hög tillgänglighet med minimal redundans påverkan bör du etablera minst 2 beräknings repliker, inklusive den primära beräknings repliken. Det här är standardkonfigurationen. På så sätt finns det en aktive ras replik som fungerar som ett failover-mål.

## <a name="data-size-and-storage-questions"></a>Data storlek och lagrings frågor

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Vad är den maximala databas storleken som stöds med storskalig

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Vad är storleken på transaktions loggen med storskalig

Transaktions loggen med stor skala är nästan oändlig. Du behöver inte bekymra dig om att köra slut på logg utrymme på ett system med hög logg data flöde. Logg skapande takten kan dock begränsas för kontinuerlig skrivning av arbets belastningar. Den högsta varaktiga logg skapande hastigheten är 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Växer min `tempdb` skalning som min databas

`tempdb`Databasen finns på lokal SSD-lagring och anpassas proportionellt till den beräknings storlek som du etablerar. Din `tempdb` är optimerad för att ge maximala prestanda för delar. `tempdb` storleken kan inte konfigureras och hanteras åt dig.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Växer storleken på databasen automatiskt eller måste jag hantera storleken på datafilerna

Databas storleken ökar automatiskt när du infogar/matar in mer data.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Vad är den minsta databas storlek som stöds av skalan eller börjar med

40 GB. En storskalig databas skapas med en start storlek på 10 GB. Sedan börjar den växa med 10 GB var 10: e minut tills den når storleken på 40 GB. Vart och ett av dessa 10 GB-segment tilldelas i en annan sid Server för att tillhandahålla mer IOPS och högre I/O-parallellitet. På grund av den här optimeringen, även om du väljer ursprunglig databas storlek som är mindre än 40 GB, växer databasen till minst 40 GB automatiskt.

### <a name="in-what-increments-does-my-database-size-grow"></a>I vilka ökningar växer min databas storlek

Varje data fil växer med 10 GB. Flera datafiler kan växa samtidigt.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Är lagringen i storskalig lokal eller fjärran sluten

I storskalig lagring lagras datafiler i Azure standard Storage. Data cachelagras fullständigt på lokal SSD-lagring på sidan servrar som är nära beräknings replikerna. Dessutom har Compute-repliker datacaches på lokal SSD och i minnet för att minska frekvensen för att hämta data från fjärrservrar.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kan jag hantera eller definiera filer eller fil grupper med storskalig skalning

Nej. Datafiler läggs till automatiskt. Vanliga orsaker till att skapa ytterligare fil grupper gäller inte i den storskaliga lagrings arkitekturen.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kan jag etablera en hård tak för data tillväxten för min databas

Nej.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Hur är datafiler som är placerade med storskalig

Datafilerna styrs av sid servrar, med en sid server per data fil. Allteftersom data storleken växer läggs datafiler och associerade sid servrar till.

### <a name="is-database-shrink-supported"></a>Stöds databas krympning

Nej.

### <a name="is-data-compression-supported"></a>Stöds data komprimering

Ja, inklusive rad-, sid-och columnstore-komprimering.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Om jag har en stor tabell, blir mina tabell data spridda över flera datafiler

Ja. Data sidorna som är kopplade till en specifik tabell kan ingå i flera datafiler, som är en del av samma filgrupp. SQL Server använder en [proportionell fyllnings strategi](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) för att distribuera data över datafiler.

## <a name="data-migration-questions"></a>Frågor om datamigrering

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>Kan jag flytta mina befintliga databaser i Azure SQL Database till den storskaliga tjänst nivån

Ja. Du kan flytta dina befintliga databaser i Azure SQL Database till skalning. Det här är en enkelriktad migrering. Du kan inte flytta databaser från storskalig till en annan tjänst nivå. För Proofing of Concept (POC) rekommenderar vi att du gör en kopia av databasen och migrerar kopian till storskalig skalning. 

Tiden som krävs för att flytta en befintlig databas till skalan består av tiden för att kopiera data och tiden för att spela upp de ändringar som gjorts i käll databasen när data kopieras. Data kopierings tiden är proportionell till data storleken. Tiden för att spela upp ändringar blir kortare om flytten görs under en period med låg Skriv aktivitet.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Kan jag flytta mina storskaliga databaser till andra tjänst nivåer

Nej. För närvarande kan du inte flytta en storskalig databas till en annan tjänst nivå.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Förlorar jag alla funktioner eller funktioner efter migreringen till den storskaliga tjänst nivån

Ja. Vissa Azure SQL Database-funktioner stöds inte i storskaliga ännu, inklusive men inte begränsat till långsiktig kvarhållning av säkerhets kopior. När du har migrerat databaserna till storskaligt upphör dessa funktioner att fungera.  Vi räknar med att de här begränsningarna är tillfälliga.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Kan jag flytta min lokala SQL Server-databas eller min SQL Server-databas i en virtuell dator i molnet till storskalig

Ja. Du kan använda alla befintliga migrerings tekniker för att migrera till storskalig, inklusive transaktionell replikering och andra data förflyttnings tekniker (Mass kopiering, Azure Data Factory, Azure Databricks, SSIS). Se även [Azure Database migration service](../../dms/dms-overview.md), som har stöd för många olika migrerings scenarier.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Vad är drift stopp under migreringen från en lokal eller virtuell dator miljö till storskalighet, och hur kan jag minimera den

Nedtid för migrering till storskalig skalning är detsamma som stillestånds tiden när du migrerar dina databaser till andra Azure SQL Database tjänst nivåer. Du [kan använda Transaktionsreplikering för att](replication-to-sql-database.md#data-migration-scenario
) minimera migreringen av drift stopp för databaser upp till några TB i storlek. För mycket stora databaser (10 + TB) kan du överväga att migrera data med hjälp av ADF, Spark eller andra tekniker för data förflyttning.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Hur lång tid tar det att ta upp X data storlek till storskalig

Hög skalning kan förbruka 100 MB/s av nya/ändrade data, men den tid som behövs för att flytta data till databaser i Azure SQL Database påverkas också av tillgängligt nätverks data flöde, Läs-och Läs hastighet för mål databasen.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>Kan jag läsa data från Blob Storage och göra en snabb belastning (t. ex. PolyBase i Azure Synapse Analytics)

Du kan låta ett klient program läsa data från Azure Storage och läsa in data inläsning i en storskalig databas (precis som du kan med andra databaser i Azure SQL Database). PolyBase stöds för närvarande inte i Azure SQL Database. Som ett alternativ till att ge en snabb belastning kan du använda [Azure Data Factory](../../data-factory/index.yml)eller använda ett Spark-jobb i [Azure Databricks](/azure/azure-databricks/) med [Spark-anslutaren för SQL](spark-connector.md). Spark-anslutningen till SQL stöder Mass infogning.

Du kan också massredigera Läs data från Azure Blob Store med BULK INSERT eller OpenRowSet: [exempel på Mass åtkomst till data i Azure Blob Storage](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

Enkel återställning eller Mass loggnings modell stöds inte i storskaliga versioner. Fullständig återställnings modell krävs för att tillhandahålla hög tillgänglighet och återställning av tidpunkter. Skalnings logg arkitekturen ger dock bättre data inmatnings hastighet jämfört med andra Azure SQL Database tjänst nivåer.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Tillåter storskalig etablering av flera noder för parallell inmatning av stora mängder data

Nej. Storskaligheten är en SMP-arkitektur (symmetrisk multi-Processing) och är inte en massiv parallell bearbetning (MPP) eller en arkitektur med flera huvud servrar. Du kan bara skapa flera repliker för att skala ut skrivskyddade arbets belastningar.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Vad är den äldsta SQL Server-versionen som stöds för migrering till storskalig

SQL Server 2005. Mer information finns i [migrera till en enskild databas eller en databas i poolen](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database). För kompatibilitetsproblem, se [lösa kompatibilitetsproblem med databas migrering](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Stöder storskalig migrering från andra data källor som Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 och andra databas plattformar

Ja. [Azure Database migration service](../../dms/dms-overview.md) har stöd för många olika migrerings scenarier.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Frågor om verksamhets kontinuitet och haveri beredskap

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Vilka service avtal som tillhandahålls för en storskalig databas

Se [SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Ytterligare sekundär beräknings repliker ökar tillgängligheten, upp till 99,99% för en databas med två eller flera sekundära beräknings repliker.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>Är databas säkerhets kopior som hanteras åt mig av Azure SQL Database

Ja.

### <a name="how-often-are-the-database-backups-taken"></a>Hur ofta säkerhets kopiering av databasen görs

Det finns inga traditionella fullständiga, differentiella och logg säkerhets kopior för storskaliga databaser. I stället finns det regelbundna lagrings ögonblicks bilder av datafiler. Loggen som genereras är bara bevarad som är för den konfigurerade kvarhållningsperioden, vilket gör det möjligt att återställa till alla tidpunkter inom kvarhållningsperioden.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Stöder storskalig återställning av plats-i-tid

Ja.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Vad är återställnings punkt mål (återställnings punkt mål)/Recovery tids mål (RTO) för databas återställning i storskalig

Återställningen är 0 min. De flesta återställnings åtgärder slutförs inom 60 minuter oavsett databas storlek. Återställnings tiden kan vara längre för större databaser och om databasen hade en betydande Skriv aktivitet före och efter återställnings tidpunkten.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Påverkar säkerhets kopiering av databasen beräknings prestanda för Mina primära eller sekundära repliker

Nej. Säkerhets kopior hanteras av underlag rings systemet och utnyttjar lagrings ögonblicks bilder. De påverkar inte användarens arbets belastningar.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Kan jag utföra geo-återställning med en storskalig databas

Ja. Geo-återställning stöds fullt ut. Till skillnad från vid återställning av tidpunkt kräver geo-återställning en åtgärd för data storlek. Datafiler kopieras parallellt, så varaktigheten för den här åtgärden beror främst på storleken på den största filen i databasen, i stället för den totala databas storleken. Geo-Restore-tiden blir betydligt kortare om databasen återställs i Azure-regionen som är [kopplad](../../best-practices-availability-paired-regions.md) till käll databasens region.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Kan jag konfigurera geo-replikering med storskalig databas

Inte just nu.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Kan jag göra en säkerhets kopia av en storskalig databas och återställa den till min lokala server eller på SQL Server på en virtuell dator

Nej. Lagrings formatet för storskaliga databaser skiljer sig från alla utgivna versioner av SQL Server och du styr inte säkerhets kopior eller har åtkomst till dem. Om du vill ta dina data från en storskalig databas kan du extrahera data med hjälp av alla data flyttnings tekniker, t. ex. Azure Data Factory, Azure Databricks, SSIS osv.

## <a name="cross-feature-questions"></a>Frågor över funktioner

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Förlorar jag alla funktioner eller funktioner efter migreringen till den storskaliga tjänst nivån

Ja. Vissa Azure SQL Database funktioner stöds inte i storskalig, inklusive men inte begränsat till långsiktig kvarhållning av säkerhets kopior. När du har migrerat databaserna till storskaligt upphör dessa funktioner att fungera.

### <a name="will-polybase-work-with-hyperscale"></a>PolyBase fungerar med storskalig

Nej. PolyBase stöds inte i Azure SQL Database.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Har storskalig support för R och python

Inte just nu.

### <a name="are-compute-nodes-containerized"></a>Behållare för beräknade noder

Nej. Storskaliga processer körs på [Service Fabric](https://azure.microsoft.com/services/service-fabric/) noder (VM), inte i behållare.

## <a name="performance-questions"></a>Prestanda frågor

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Hur mycket Skriv data flöde som kan jag push-överföra i en storskalig databas

Transaktions loggens data flödes gräns är inställt på 100 MB/s för eventuell förstorings beräknings storlek. Möjligheten att uppnå denna hastighet beror på flera faktorer, inklusive men inte begränsat till arbets belastnings typ, klient konfiguration och att det finns tillräckligt med beräknings kapacitet på den primära beräknings repliken för att skapa logg med denna hastighet.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Hur många IOPS får jag på den största beräkningen

IOPS-och IO-svars tiden varierar beroende på arbets belastnings mönstren. Om data som nås cachelagras på beräknings repliken ser du liknande IO-prestanda som lokal SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Påverkas mitt data flöde av säkerhets kopior

Nej. Compute är frikopplad från lagrings skiktet. Detta eliminerar prestanda påverkan från säkerhets kopieringen.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Påverkas mitt data flöde när jag etablerar ytterligare beräknings repliker

Eftersom lagrings utrymmet delas och det inte finns någon direkt fysisk replikering mellan primära och sekundära beräknings repliker, kommer data flödet på den primära repliken inte att påverkas direkt genom att de sekundära replikerna läggs till. Vi kan dock begränsa kontinuerlig skrivning av arbets belastningen på den primära till Tillåt att loggen tillämpas på sekundära repliker och sid servrar, för att undvika dåliga Läs prestanda på sekundära repliker.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Hur gör jag för att diagnostisera och felsöka prestanda problem i en storskalig databas

För de flesta prestanda problem, särskilt de som inte har rotats i lagrings prestanda, gäller vanliga SQL-diagnostik och fel söknings steg. För storskalig Minnesdiagnostik, se [fel söknings diagnostik för SQL-skalbarhet](hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Frågor om skalbarhet

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Hur lång tid tar det att skala upp och ned en beräknings replik

Skalnings beräkning upp eller ned tar vanligt vis upp till 2 minuter, oavsett data storlek.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Är min databas offline medan åtgärden för att skala upp/ned pågår

Nej. Uppskalning uppåt och nedåt är online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Ska jag förvänta dig att släppa anslutningen när skalnings åtgärderna pågår

Att skala upp eller ned resulterar i att befintliga anslutningar bryts när en redundansväxling sker i slutet av skalnings åtgärden. Att lägga till sekundära repliker resulterar inte i anslutning.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Är skalning uppåt och nedåt av beräknings repliker automatiskt eller slutanvändarens Utlös ande åtgärd

Slutanvändare. Inte automatisk.  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>Växer storleken på min `tempdb` databas och RBPEX cache även när beräkningen skalas upp

Ja. `tempdb`Storleken på databas-och RBPEX- [cachen](service-tier-hyperscale.md#distributed-functions-architecture) på datornoderna skalas upp automatiskt när antalet kärnor ökas.

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Kan jag etablera flera primära beräknings repliker, till exempel ett multi-master-system, där flera primära beräknings huvuden kan driva en högre nivå av samtidighet

Nej. Endast den primära beräknings repliken tar emot Läs-/skriv förfrågningar. Sekundär beräknings repliker accepterar endast skrivskyddade begär Anden.

## <a name="read-scale-out-questions"></a>Läs skalbara frågor

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Hur många sekundära beräknings repliker kan jag etablera

Vi skapar en sekundär replik för storskaliga databaser som standard. Om du vill justera antalet repliker kan du göra det med hjälp av [Azure Portal](https://portal.azure.com) eller [REST API](/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Hur gör jag för att ansluta till dessa sekundära beräknings repliker

Du kan ansluta till dessa ytterligare skrivskyddade beräknings repliker genom att ange `ApplicationIntent` argumentet för anslutnings strängen till `ReadOnly` . Alla anslutningar som marker ATS med `ReadOnly` dirigeras automatiskt till en av de ytterligare skrivskyddade beräknings replikerna. Mer information finns i [använda skrivskyddade repliker för att avlasta skrivskyddade arbets belastningar](read-scale-out.md).

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Hur gör jag för att validera om jag har lyckats ansluta till en sekundär beräknings replik med hjälp av SSMS eller andra klient verktyg?

Du kan köra följande T-SQL-fråga: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')` .
Resultatet är `READ_ONLY` om du är ansluten till en skrivskyddad sekundär replik och `READ_WRITE` om du är ansluten till den primära repliken. Observera att databas sammanhanget måste anges till namnet på den storskaliga databasen, inte till `master` databasen.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Kan jag skapa en dedikerad slut punkt för en skalbar replik med Läs behörighet

Nej. Du kan bara ansluta till expanderade repliker genom att ange `ApplicationIntent=ReadOnly` .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Utför systemet intelligent belastnings utjämning för Läs arbets belastningen

Nej. En ny anslutning med skrivskyddad avsikt omdirigeras till en godtycklig expanderad replikering.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Kan jag skala upp/ned de sekundära beräknings replikerna oberoende av den primära repliken

Nej. Den sekundära beräknings repliken används också som redundans för hög tillgänglighet, så att de måste ha samma konfiguration som den primära för att tillhandahålla förväntade prestanda efter redundansväxlingen.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Får jag en annan `tempdb` storlek för min primära beräkning och mina ytterligare sekundär beräknings repliker

Nej. `tempdb`Databasen har kon figurer ATS baserat på beräknings storleks etablering, dina sekundära beräknings repliker har samma storlek som den primära beräkningen.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Kan jag lägga till index och vyer på mina sekundära beräknings repliker

Nej. Storskaliga databaser har delad lagring, vilket innebär att alla beräknings repliker ser samma tabeller, index och vyer. Om du vill att ytterligare index ska optimeras för läsningar på den sekundära måste du lägga till dem på den primära.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Hur lång fördröjning kommer att vara mellan de primära och sekundära beräknings replikerna

Data svars tid från det att en transaktion allokeras till den primära tiden som den kan läsas på en sekundär beror på den aktuella logg Genererings taxan, transaktions storlek, belastning på repliken och andra faktorer. Vanliga data svars tider för små transaktioner är i flera millisekunder, men det finns ingen övre bindning för data svars tid. Data på en särskild sekundär replik är alltid konsekventa. Men vid en viss tidpunkt kan data fördröjningen vara olika för olika sekundära repliker. Arbets belastningar som behöver läsa allokerade data omedelbart bör köras på den primära repliken.

## <a name="next-steps"></a>Nästa steg

Mer information om den storskaliga tjänst nivån finns i [storskalig Service Tier](service-tier-hyperscale.md).