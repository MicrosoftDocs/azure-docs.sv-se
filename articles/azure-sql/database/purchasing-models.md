---
title: Köpmodeller
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Lär dig mer om de inköps modeller som är tillgängliga för Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788833"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>Välj mellan vCore-och DTU-inköps modeller – Azure SQL Database-och SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med Azure SQL Database och Azure SQL-hanterad instans kan du enkelt köpa en helt hanterad plattform som en tjänst (PaaS)-databas motor som passar dina prestanda-och kostnads behov. Beroende på vilken distributions modell som du har valt för Azure SQL Database kan du välja den inköps modell som passar dig bäst:

- [Virtual Core (vCore)-baserad inköps modell](service-tiers-vcore.md) (rekommenderas). Den här inköps modellen ger ett val mellan en allokerad beräknings nivå och en server lös beräknings nivå. Med den allokerade beräknings nivån väljer du den exakta mängden beräknings resurser som alltid är etablerade för din arbets belastning. Med Server lös beräknings nivån anger du automatisk skalning av beräknings resurserna över ett konfigurerbart beräknings intervall. Med den här beräknings nivån kan du också automatiskt pausa och återuppta databasen baserat på arbets belastnings aktivitet. VCore enhets pris per tidsenhet är lägre än den allokerade beräknings nivån än den är i Server lös beräknings nivån.
- [-Baserad inköps modell för databas transaktions enhet (DTU)](service-tiers-dtu.md). Den här inköps modellen tillhandahåller paket för beräkning och lagring som är balanserade för vanliga arbets belastningar.

Det finns två inköps modeller:

- [vCore-baserad inköps modell](service-tiers-vcore.md) är tillgänglig för både [Azure SQL Database](sql-database-paas-overview.md) och [Azure SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md). Den [storskaliga Service nivån](service-tier-hyperscale.md) är tillgänglig för enskilda databaser som använder den [vCore-baserade inköps modellen](service-tiers-vcore.md).
- [DTU-baserad inköps modell](service-tiers-dtu.md) är tillgänglig för [Azure SQL Database](single-database-manage.md).

I följande tabell och diagram jämförs och kontrasterar vCore-baserade och DTU-baserade inköps modeller:

|**Köpmodell**|**Beskrivning**|**Bäst för**|
|---|---|---|
|DTU-baserad|Den här modellen är baserad på ett samlat mått för beräknings-, lagrings-och I/O-resurser. Beräkningsstorlekarna anges i DTU:er för enstaka databaser och i elastiska databastransaktioner (eDTU:er) för elastiska pooler. Du kan läsa mer om DTU:er och eDTU:er i [Vad är DTU:er och eDTU:er?](purchasing-models.md#dtu-based-purchasing-model).|Kunder som vill ha enkla, förkonfigurerade resurs alternativ|
|vCore-baserad|Med den här modellen kan du oberoende välja beräknings-och lagrings resurser. Med den vCore-baserade inköpsmodellen kan du också spara in på kostnaderna med [Azure Hybrid-förmånen](https://azure.microsoft.com/pricing/hybrid-benefit/) för SQL Server.|Kunder som har värde flexibilitet, kontroll och transparens|
||||  

![Jämförelse av pris modell](./media/purchasing-models/pricing-model.png)

Vill du optimera och Spara på dina moln utgifter?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>Beräknings kostnader

### <a name="provisioned-compute-costs"></a>Etablerade beräknings kostnader

I den allokerade beräknings nivån återspeglar beräknings kostnaden den totala beräknings kapaciteten som är etablerad för programmet.

I Affärskritisk tjänst nivån allokerar vi automatiskt minst tre repliker. För att avspegla den här ytterligare allokeringen av beräknings resurser, är priset i den vCore-baserade inköps modellen ungefär 2,7 gånger högre på den Affärskritisk tjänst nivån än den är på Generell användning tjänst nivå. På samma sätt återspeglar det högre lagrings priset per GB i Affärskritisk tjänst nivån de högre i/o-gränserna och lägre latens för SSD-lagringen.

Kostnaden för lagring av säkerhets kopior är samma för Affärskritisk tjänst nivå och Generell användning tjänst nivå eftersom båda nivåerna använder standard lagring för säkerhets kopiering.

### <a name="serverless-compute-costs"></a>Beräknings kostnader utan Server

En beskrivning av hur beräknings kapaciteten definieras och kostnader beräknas för Server lös beräknings nivån finns i [SQL Database Server lös nivå](serverless-tier-overview.md).

## <a name="storage-costs"></a>Lagringskostnader

Olika typer av lagring faktureras på olika sätt. För data lagring debiteras du för det tillhandahållna lagrings utrymmet baserat på den maximala databas-eller pool storlek som du väljer. Kostnaden ändras inte om du inte minskar eller ökar Max värdet. Lagring av säkerhets kopior är kopplad till automatiserade säkerhets kopieringar av din instans och tilldelas dynamiskt. Om du ökar säkerhets kopierings perioden ökar säkerhets kopierings lagringen som används av din instans.

Som standard kopieras sju dagars automatiserade säkerhets kopieringar av dina databaser till ett GRS-standard-Blob Storage-konto (Read-Access Geo-redundant Storage). Den här lagringen används av varje veckovis fullständig säkerhets kopiering, dagliga differentiella säkerhets kopieringar och säkerhets kopior av transaktions loggar, som kopieras var femte minut. Storleken på transaktions loggarna beror på ändrings takten för databasen. En minsta lagrings mängd som motsvarar 100 procent av databasens storlek tillhandahålls utan extra kostnad. Ytterligare förbrukning av lagring av säkerhets kopior debiteras i GB per månad.

Mer information om lagrings priser finns på sidan med [priser](https://azure.microsoft.com/pricing/details/sql-database/single/) .

## <a name="vcore-based-purchasing-model"></a>Köpmodell baserad på virtuell kärna

En virtuell kärna (vCore) representerar en logisk CPU och ger dig möjlighet att välja mellan generationens maskin vara och maskin varans fysiska egenskaper (till exempel antalet kärnor, minne och lagrings storlek). Den vCore-baserade inköps modellen ger dig flexibilitet, kontroll, transparens för enskild resursanvändning och ett enkelt sätt att översätta lokala arbets belastnings krav till molnet. Med den här modellen kan du välja beräknings-, minnes-och lagrings resurser utifrån dina arbets belastnings behov.

I den vCore-baserade inköps modellen kan du välja mellan [generell användning](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) -och [affärskritisk](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) tjänst nivåerna för SQL Database och SQL-hanterad instans.  För enskilda databaser kan du också välja den [storskaliga tjänst nivån](service-tier-hyperscale.md).

Med den vCore-baserade inköps modellen kan du välja beräknings-och lagrings resurser oberoende av varandra, matcha lokala prestanda och optimera priset. I den vCore-baserade inköps modellen betalar du för:

- Beräkningsresurser (tjänstnivå + antal virtuella kärnor samt mängden minne + maskinvarans generation).
- Typ av och mängd vid data- och logglagring.
- Lagring av säkerhets kopior (RA-GRS).

> [!IMPORTANT]
> Beräknings resurser, I/O och data-och logg lagring debiteras per databas eller elastisk pool. Lagring av säkerhets kopior debiteras per varje databas. Mer information om avgifter för SQL-hanterade instanser finns i [SQL-hanterad instans](../managed-instance/sql-managed-instance-paas-overview.md).
> **Regions begränsningar:** Den aktuella listan över regioner som stöds finns i [produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Om du vill skapa en hanterad instans i en region som för närvarande inte stöds [skickar du en supportbegäran via Azure Portal](quota-increase-request.md).

Om databasen förbrukar mer än 300 DTU: er kan du minska kostnaderna genom att konvertera till den vCore-baserade inköps modellen. Du kan konvertera genom att använda ditt API eller genom att använda Azure Portal, utan drift stopp. Konverteringen är dock inte obligatorisk och sker inte automatiskt. Om den DTU-baserade inköps modellen uppfyller dina prestanda-och affärs behov bör du fortsätta att använda den.

Om du vill konvertera från den DTU-baserade inköps modellen till den vCore-baserade inköps modellen läser du [Migrera från DTU till vCore](migrate-dtu-to-vcore.md).

## <a name="dtu-based-purchasing-model"></a>DTU-baserad inköps modell

En databas transaktions enhet (DTU) representerar ett blandat mått på processor, minne, läsningar och skrivningar. Den DTU-baserade inköps modellen erbjuder en uppsättning förkonfigurerade paket med beräknings resurser och inkluderat lagrings utrymme för att driva olika nivåer av program prestanda. Om du föredrar att förenkla en förkonfigurerad samling och fasta betalningar varje månad kan den DTU-baserade modellen vara lämpligare för dina behov.

I den DTU-baserade inköps modellen kan du välja mellan tjänst nivåerna Basic, standard och Premium för Azure SQL Database. Den DTU-baserade inköps modellen är inte tillgänglig för Azure SQL-hanterad instans.

### <a name="database-transaction-units-dtus"></a>Databas transaktions enheter (DTU: er)

För en enskild databas med en viss beräknings storlek inom en [tjänst nivå](single-database-scale.md)garanterar Azure en viss nivå av resurser för databasen (oberoende av andra databaser i Azure-molnet). Den här garantin ger en förutsägbar prestanda nivå. Mängden resurser som har allokerats för en databas beräknas som ett antal DTU: er och är ett samlat mått för beräknings-, lagrings-och I/O-resurser.

Förhållandet mellan dessa resurser bestäms ursprungligen av en [benchmark-belastning (Online Transaction Processing)](service-tiers-dtu.md) som utformats för att vara typiska för verkliga OLTP-arbetsbelastningar. När din arbets belastning överskrider mängden av någon av dessa resurser begränsas ditt data flöde, vilket ger sämre prestanda och timeout.

De resurser som används av arbets belastningen påverkar inte resurserna som är tillgängliga för andra databaser i Azure-molnet. På samma sätt påverkar resurserna som används av andra arbets belastningar inte resurserna som är tillgängliga för din databas.

![Avgränsnings ruta](./media/purchasing-models/bounding-box.png)

DTU: er är mest användbara för att förstå de relativa resurser som är allokerade för databaser med olika beräknings storlekar och tjänst nivåer. Exempel:

- Att dubblera DTU: er genom att öka beräknings storleken för en databas, motsvarar att dubblera den uppsättning av resurser som är tillgängliga för den databasen.
- En Premium Service Tier p11-databas med 1750 DTU: er ger 350 gånger mer DTU-beräknings kraft än en grundläggande tjänst nivå databas med 5 DTU: er.  

Om du vill få djupare insikt i resurs användningen för din arbets belastning använder du [frågor om prestanda insikter](query-performance-insight-use.md) för att:

- Identifiera de vanligaste frågorna efter processor/varaktighet/antal körningar som kan justeras för bättre prestanda. Till exempel kan en I/O-intensiv fråga ha nytta av [minnes optimerings tekniker](../in-memory-oltp-overview.md) för att bättre kunna använda det tillgängliga minnet på en viss tjänst nivå och beräknings storlek.
- Öka detalj nivån i informationen om en fråga för att visa dess text och dess historik för resursanvändning.
- Få åtkomst till prestanda justerings rekommendationer som visar åtgärder som vidtagits av [SQL Database Advisor](database-advisor-implement-performance-recommendations.md).

### <a name="elastic-database-transaction-units-edtus"></a>Elastiska databas transaktions enheter (eDTU: er)

För databaser som alltid är tillgängliga, i stället för att tillhandahålla en dedikerad uppsättning resurser (DTU: er) som kanske inte alltid behövs, kan du placera dessa databaser i en [elastisk pool](elastic-pool-overview.md). Databaserna i en elastisk pool finns på en enda server och delar en pool med resurser.

De delade resurserna i en elastisk pool mäts av elastiska databas transaktions enheter (eDTU: er). Elastiska pooler ger en enkel och kostnads effektiv lösning för att hantera prestanda mål för flera databaser som har mycket varierande och oförutsägbara användnings mönster. En elastisk pool garanterar att alla resurser inte kan konsumeras av en databas i poolen, samtidigt som varje databas i poolen alltid har en minsta mängd resurser som är tillgängliga.

En pool får ett angivet antal eDTU: er för ett angivet pris. I den elastiska poolen kan enskilda databaser skalas i den konfigurerade gränsen. En databas under en kraftigare belastning förbrukar mer eDTU: er för att möta efter frågan. Databaser under ljusare belastning förbrukar färre eDTU: er. Databaser utan belastning förbrukar ingen eDTU: er. Eftersom resurser etablerades för hela poolen, i stället för per databas, fören klar elastiska pooler dina hanterings uppgifter och ger en förutsägbar budget för poolen.

Du kan lägga till ytterligare eDTU: er i en befintlig pool utan avbrott i databasen och utan påverkan på databaserna i poolen. Om du inte längre behöver extra eDTU: er kan du när som helst ta bort dem från en befintlig pool. Du kan också lägga till databaser i eller ta bort databaser från en pool när som helst. Om du vill reservera eDTU: er för andra databaser begränsar du antalet eDTU: er som en databas kan använda under en kraftig belastning. Om en databas konsekvent underanvänder resurser, så flytta den ut från poolen och konfigurera den som en enkel databas med en förutsägbar mängd resurser som krävs.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Fastställa antalet DTU: er som krävs av en arbets belastning

Om du vill migrera en befintlig lokal eller SQL Server virtuell dators arbets belastning till SQL Database använder du DTU- [kalkylatorn](https://dtucalculator.azurewebsites.net/) för att uppskatta antalet DTU: er som behövs. För en befintlig SQL Database arbets belastning kan du använda [frågor om prestanda](query-performance-insight-use.md) för att förstå din databas resurs förbrukning (DTU: er) och få djupare insikter för att optimera din arbets belastning. I [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) Dynamic Management View (DMV) kan du Visa resursförbrukning för den senaste timmen. I vyn [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) katalog visas resursförbrukning under de senaste 14 dagarna, men med en lägre åter givning på fem minuters genomsnitt.

### <a name="determine-dtu-utilization"></a>Fastställa DTU-användning

Använd följande formel för att fastställa den genomsnittliga procent andelen DTU/eDTU-användning i relation till gränsen för DTU/eDTU för en databas eller elastisk pool:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Indatavärdena för den här formeln kan hämtas från [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)och [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV: er. Med andra ord kan du välja det största procentuella värdet från följande: `avg_cpu_percent` , `avg_data_io_percent` och `avg_log_write_percent` vid en viss tidpunkt, för att fastställa procent andelen DTU/eDTU-användning mot gränsen för DTU/eDTU för en databas eller elastisk pool.

> [!NOTE]
> DTU-gränsen för en databas bestäms av CPU, läsningar, skrivningar och minne som är tillgängligt för databasen. Men eftersom SQL Database-motorn vanligt vis använder allt tillgängligt minne för Datacachen för att förbättra prestandan, `avg_memory_usage_percent` är värdet normalt nära 100 procent, oavsett den aktuella databas belastningen. Det innebär att även om minnet påverkar DTU-gränsen, används den inte i formeln DTU-användning.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Arbets belastningar som drar nytta av en elastisk pool av resurser

Pooler lämpar sig väl för databaser med ett genomsnittligt medelvärde för resursanvändning och relativt ovanliga användnings toppar. Mer information finns i [när bör du överväga en SQL Database elastisk pool?](elastic-pool-overview.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Maskin varu generationer i den DTU-baserade inköps modellen

I den DTU-baserade inköps modellen kan kunder inte välja den maskin varu generation som används för sina databaser. Även om en viss databas vanligt vis hålls på en specifik maskin varu generation under en längre tid (vanligt vis i flera månader), finns det vissa händelser som kan orsaka att en databas flyttas till en annan maskin varu generation.

Till exempel kan en databas flyttas till en annan maskin varu generation om den skalas upp eller ned till ett annat tjänst mål, eller om den aktuella infrastrukturen i ett Data Center närmar sig sin kapacitets gräns, eller om den för närvarande använda maskin varan inaktive ras på grund av dess livs längd.

Om en databas flyttas till en annan maskin vara kan arbets belastnings prestandan ändras. DTU-modellen garanterar att data flödet och svars tiden för [DTU-benchmark](./service-tiers-dtu.md#dtu-benchmark) -arbetsbelastningen förblir väsentligen identiska när databasen flyttas till en annan maskin varu generation, så länge tjänst målet (antalet DTU: er) är oförändrat.

Men över hela spektrumet av kund arbets belastningar som körs i Azure SQL Database, kan effekten av att använda annan maskin vara för samma tjänst mål vara mer uttalad. Olika arbets belastningar kan dra nytta av olika maskin varu konfiguration och-funktioner. För andra arbets belastningar än DTU-Benchmark är det därför möjligt att se prestanda skillnader om databasen flyttas från en maskin varu generation till en annan.

Till exempel kan ett program som är känsligt för nätverks fördröjning se bättre prestanda på Gen5 maskin vara jämfört med Gen4 på grund av användningen av accelererade nätverk i Gen5, men ett program som använder intensiv läsning i/o kan se bättre prestanda på Gen4 maskin vara jämfört med Gen5 på grund av ett högre minne per Core-förhållande på Gen4.

Kunder med arbets belastningar som är känsliga för maskin varu förändringar eller kunder som vill kontrol lera valet av maskin varu generering för deras databas kan använda [vCore](service-tiers-vcore.md) -modellen för att välja den maskin varu generation som används när databasen skapas och skalas. I vCore-modellen dokumenteras resurs gränserna för varje tjänst mål för varje maskin varu generation, för både [enkla databaser](resource-limits-vcore-single-databases.md) och [elastiska pooler](resource-limits-vcore-elastic-pools.md). Mer information om maskin varu generationer i vCore-modellen finns i [maskin varu generationer](./service-tiers-vcore.md#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Vanliga frågor och svar

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Måste jag ta mitt program offline för att konvertera från en DTU-baserad tjänst nivå till en vCore tjänst nivå?

Nej. Du behöver inte göra programmet offline. De nya tjänst nivåerna erbjuder en enkel online-konverterings metod som liknar den befintliga processen för att uppgradera databaser från standard till Premium service-nivån och det andra sättet. Du kan starta konverteringen med hjälp av Azure Portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enskilda databaser](single-database-scale.md) och [Hantera elastiska pooler](elastic-pool-overview.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kan jag konvertera en databas från en tjänst nivå i den vCore-baserade inköps modellen till en tjänst nivå i den DTU-baserade inköps modellen?

Ja, du kan enkelt konvertera databasen till alla prestanda mål som stöds med hjälp av Azure Portal, PowerShell, Azure CLI, T-SQL eller REST API. Se [Hantera enskilda databaser](single-database-scale.md) och [Hantera elastiska pooler](elastic-pool-overview.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om den vCore-baserade inköps modellen finns i [vCore-baserad inköps modell](service-tiers-vcore.md).
- Mer information om den DTU-baserade inköps modellen finns i [DTU-baserad inköps modell](service-tiers-dtu.md).