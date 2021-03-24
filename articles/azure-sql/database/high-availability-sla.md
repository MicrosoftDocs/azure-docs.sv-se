---
title: Hög tillgänglighet
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Lär dig mer om funktioner och funktioner för hög tillgänglighet för Azure SQL Database och SQL-hanterad instans tjänst
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 1c210eab0332d01fc6514edc790d729172ed2174
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889067"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Hög tillgänglighet för Azure SQL Database-och SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Målet med hög tillgänglighets arkitekturen i Azure SQL Database-och SQL-hanterade instans är att garantera att databasen är igång minst 99,99% av tiden (mer information om ett särskilt service avtal för olika nivåer finns i [SLA för Azure SQL Database och SQL-hanterad instans](https://azure.microsoft.com/support/legal/sla/sql-database/)) utan att oroa dig över påverkan av underhålls åtgärder och avbrott. Azure hanterar automatiskt kritiska underhålls uppgifter, till exempel korrigeringar, säkerhets kopieringar, Windows-och Azure SQL-uppgraderingar, samt oplanerade händelser som underliggande maskin vara, program vara eller nätverks fel.  När den underliggande databasen i Azure SQL Database korrigeras eller flyttas över, märks inte stillestånds tiden om du [använder logik för omprövning](develop-overview.md#resiliency) i appen. SQL Database och SQL-hanterad instans kan snabbt återställas även under de mest kritiska omständigheterna för att se till att dina data alltid är tillgängliga.

Lösningen för hög tillgänglighet är utformad för att säkerställa att allokerade data aldrig går förlorade på grund av problem, att underhålls åtgärder inte påverkar din arbets belastning och att databasen inte är en enskild felpunkt i din program varu arkitektur. Det finns inga underhålls perioder som kräver att du stoppar arbets belastningen medan databasen uppgraderas eller underhålls.

Det finns två arkitektur modeller med hög tillgänglighet:

- **Standard tillgänglighets modell** som baseras på en separation av data bearbetning och lagring.  Den förlitar sig på hög tillgänglighet och tillförlitlighet för Fjärrlagrings nivån. Den här arkitekturen riktar sig till budgetorienterade företags program som kan tolerera viss prestanda försämring under underhålls aktiviteter.
- **Premium-tillgänglighets modell** som baseras på ett kluster med databas motor processer. Det förlitar sig på att det alltid finns ett kvorum med tillgängliga databas motor noder. Den här arkitekturen riktar sig till verksamhets kritiska program med hög IO-prestanda, hög transaktions hastighet och garanterar minimal prestanda påverkan för din arbets belastning under underhålls aktiviteter.

SQL Database-och SQL-hanterad instans körs både på den senaste stabila versionen av SQL Server databas motorn och Windows-operativsystemet, och de flesta användare skulle inte märka att uppgraderingar utförs kontinuerligt.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Basic, standard och Generell användning tjänst nivån lokalt redundant tillgänglighet

Tjänst nivåerna Basic, standard och Generell användning utnyttjar standard tillgänglighets arkitekturen för både server lös och allokerad beräkning. Följande bild visar fyra olika noder med de avgränsade beräknings-och lagrings lagren.

![Separering av beräkning och lagring](./media/high-availability-sla/general-purpose-service-tier.png)

Standard tillgänglighets modellen innehåller två lager:

- Ett tillstånds löst beräknings lager som kör `sqlservr.exe` processen och bara innehåller temporära och cachelagrade data, till exempel tempdb, modell databaser på anslutna SSD och planera cache, resurspool och columnstore-pool i minnet. Den här tillstånds lösa noden drivs av Azure-Service Fabric som initierar `sqlservr.exe` , kontrollerar nodens hälsa och utför redundans till en annan nod vid behov.
- Ett tillstånds känsligt data lager med databasfiler (. MDF/. ldf) som lagras i Azure Blob Storage. Azure Blob Storage har inbyggd data tillgänglighet och en redundans funktion. Det garanterar att alla poster i logg filen eller sidan i data filen kommer att bevaras även om `sqlservr.exe` process kraschar.

När databas motorn eller operativ systemet uppgraderas, eller om ett problem upptäcks, kommer Azure Service Fabric flytta `sqlservr.exe` processen utan tillstånd till en annan tillstånds lös Compute-nod med tillräckligt med ledigt utrymme. Data i Azure Blob Storage påverkas inte av flyttningen och data-/loggfilerna bifogas till den nya initierade `sqlservr.exe` processen. Den här processen garanterar 99,99% tillgänglighet, men en kraftig arbets belastning kan uppleva viss prestanda försämring under över gången sedan den nya `sqlservr.exe` processen börjar med kallt cacheminne.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Generell användning tjänst nivå zonens redundant tillgänglighet (för hands version)

Zon redundant konfiguration för tjänst nivån generell användning använder [Azure-tillgänglighetszoner](../../availability-zones/az-overview.md)   för att replikera databaser över flera fysiska platser i en Azure-region.Genom att välja zon redundans kan du göra nya och befintliga generella enkla databaser och elastiska pooler elastiska till en mycket större mängd problem, inklusive oåterkalleliga Data Center avbrott, utan några ändringar i program logiken.

Zon redundant konfiguration för generell användnings nivån har två nivåer:  

- Ett tillstånds känsligt data lager med databasfiler (. MDF/. ldf) som lagras i ZRS-PFS (Zone-redundant [lagring Premium-filresurs](../../storage/files/storage-how-to-create-file-share.md). Med [zon-redundant lagring](../../storage/common/storage-redundancy.md) kopieras data-och loggfilerna synkront över tre fysiskt isolerade tillgänglighets zoner i Azure.
- Ett tillstånds löst beräknings lager som kör sqlservr.exes processen och bara innehåller temporära och cachelagrade data, till exempel TempDB, modell databaser på anslutna SSD och planera cache, resurspool och columnstore-pool i minnet. Den här tillstånds lösa noden drivs av Azure-Service Fabric som initierar sqlservr.exe, kontrollerar nodens hälsa och utför redundans till en annan nod vid behov. För zoner med redundanta databaser för generell användning är noder med reserv kapacitet lättillgängliga i andra Tillgänglighetszoner för redundans.

Zonens redundanta version av hög tillgänglighets arkitektur för tjänst nivån generell användning illustreras av följande diagram:

![Zon redundant konfiguration för generell användning](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Zon redundant konfiguration är bara tillgänglig när Gen5 Compute-maskinvaran har valts. Den här funktionen är inte tillgänglig i SQL-hanterad instans. Zon redundant konfiguration för generell användnings nivå är endast tillgängligt i följande regioner: östra USA, östra USA 2, västra USA 2, norra Europa, Västeuropa, Sydostasien, östra Australien, Östra Japan, Storbritannien, södra och Frankrike, centrala.

> [!NOTE]
> Generell användning-databaser med en storlek på 80 vCore kan orsaka försämrade prestanda med Zone-redundant konfiguration. Dessutom kan åtgärder som säkerhets kopiering, återställning, databas kopiering och konfiguration av Geo-DR-relationer uppleva sämre prestanda för alla enskilda databaser som är större än 1 TB. 
> 
> [!NOTE]
> Förhands granskningen omfattas inte av en reserverad instans

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Premium och Affärskritisk Service Tier lokalt redundant tillgänglighet

Premium-och Affärskritisk tjänst nivåerna utnyttjar Premium Availability-modellen, som integrerar beräknings resurser ( `sqlservr.exe` process) och lagring (lokalt anslutna SSD) på en enda nod. Hög tillgänglighet uppnås genom att replikera både beräkning och lagring till ytterligare noder som skapar ett kluster med tre noder.

![Kluster med noder i databas motorn](./media/high-availability-sla/business-critical-service-tier.png)

De underliggande databasfilerna (. MDF/. ldf) placeras på den anslutna SSD-lagringen för att tillhandahålla mycket låg latens i/o för din arbets belastning. Hög tillgänglighet implementeras med hjälp av en teknik som liknar SQL Server [Always on-tillgänglighetsgrupper](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Klustret innehåller en enda primär replik som kan nås av kundens arbets belastningar med Läs-och skriv åtgärder och upp till tre sekundära repliker (data bearbetning och lagring) som innehåller kopior av data. Den primära noden skickar konstanter ändringar till de sekundära noderna i ordning och säkerställer att data synkroniseras till minst en sekundär replik innan varje transaktion bekräftas. Den här processen garanterar att om den primära noden kraschar av någon anledning finns det alltid en helt synkroniserad nod att redundansväxla till. Redundansväxlingen initieras av Azure-Service Fabric. När den sekundära repliken blir den nya primära noden skapas en annan sekundär replik för att säkerställa att klustret har tillräckligt många noder (kvorumkonfigurationen). När redundansväxlingen är klar omdirigeras Azure SQL-anslutningar automatiskt till den nya primära noden.

Som en extra förmån innehåller Premium Availability-modellen möjligheten att omdirigera skrivskyddade Azure SQL-anslutningar till en av de sekundära replikerna. Den här funktionen kallas för [Läs utskalning](read-scale-out.md). Den ger 100% ytterligare beräknings kapacitet utan extra kostnad för att inaktivera Läs åtgärder, till exempel analytiska arbets belastningar, från den primära repliken.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Tillgänglighet för Premium-och Affärskritisk tjänst skikt zon 

Som standard skapas klustret av noder för Premium Availability-modellen i samma data Center. Med introduktionen av [Azure-tillgänglighetszoner](../../availability-zones/az-overview.md)kan SQL Database placera olika repliker av affärskritisk-databasen till olika tillgänglighets zoner i samma region. För att eliminera en enskild felpunkt dupliceras också kontroll ringen över flera zoner som tre Gateway-ringar (GW). Routningen till en angiven Gateway-ring styrs av [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom zonens redundanta konfiguration i Premium-eller Affärskritisk tjänst nivåerna inte skapar ytterligare databas-redundans, kan du aktivera den utan extra kostnad. Genom att välja en redundant konfiguration av zonen kan du göra dina Premium-eller Affärskritisk-databaser flexibla till en större mängd olika problem, inklusive oåterkalleliga Data Center avbrott, utan några ändringar i program logiken. Du kan också konvertera befintliga Premium-eller Affärskritisk-databaser eller pooler till zonens redundanta konfiguration.

Eftersom zonens redundanta databaser har repliker i olika data Center med lite avstånd mellan dem, kan den ökade nätverks fördröjningen öka genomförande tiden och därmed påverka prestanda för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till konfigurationen med en zon genom att inaktivera inställningen zon redundans. Den här processen är en online-åtgärd som liknar uppgraderingen av Service nivån. I slutet av processen migreras databasen eller poolen från en zon redundant ring till en enskild zon ring eller vice versa.

> [!IMPORTANT]
> När du använder Affärskritisk nivån är zonens redundant konfiguration bara tillgänglig när Gen5 Compute-maskinvaran är vald. För uppdaterad information om de regioner som har stöd för zonens redundanta databaser, se [tjänster support per region](../../availability-zones/az-region.md).

> [!NOTE]
> Den här funktionen är inte tillgänglig i SQL-hanterad instans.

Zonens redundanta version av hög tillgänglighets arkitektur illustreras med följande diagram:

![redundant arkitektur zon för hög tillgänglighet](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Tillgänglighet för storskalig Service Tier

Den storskaliga Service Tier-arkitekturen beskrivs i [arkitekturen distribuerade funktioner](./service-tier-hyperscale.md#distributed-functions-architecture) och är för närvarande endast tillgänglig för SQL Database, inte SQL-hanterad instans.

![Funktions arkitektur för skala](./media/high-availability-sla/hyperscale-architecture.png)

Tillgänglighets modellen i storskalig innehåller fyra lager:

- Ett tillstånds löst beräknings lager som kör `sqlservr.exe` processerna och bara innehåller temporära och cachelagrade data, till exempel icke-omfattande RBPEX cache, tempdb, modell databas osv. på anslutna SSD och planera cache, resurspool och columnstore-pool i minnet. Detta tillstånds lösa lager inkluderar den primära beräknings repliken och eventuellt ett antal sekundära beräknings repliker som kan fungera som redundans.
- Ett tillstånds löst lagrings lager som bildas av sid servrar. Det här lagret är den distribuerade lagrings motorn för de `sqlservr.exe` processer som körs på beräknings replikerna. Varje Page Server innehåller bara tillfälliga och cachelagrade data, till exempel för att täcka RBPEX-cache på anslutna SSD och data sidor som cachelagras i minnet. Varje sid Server har en länkad sid server i en aktiv-aktiv konfiguration för att tillhandahålla belastnings utjämning, redundans och hög tillgänglighet.
- Ett tillstånds känsligt lagrings lager för transaktions logg som bildas av Compute-noden som kör logg tjänst processen, landnings zonen för transaktions loggen och transaktions loggen långsiktig lagring. Landnings zon och långsiktig lagring använder Azure Storage, vilket ger tillgänglighet och [redundans](../../storage/common/storage-redundancy.md) för transaktions loggen, vilket garanterar data hållbarhet för genomförda transaktioner.
- Ett tillstånds känsligt data lagrings lager med databasfilerna (MDF/. NDF) som lagras i Azure Storage och som uppdateras av sid servrar. Det här lagret använder data tillgänglighet och [redundanta](../../storage/common/storage-redundancy.md) funktioner i Azure Storage. Det garanterar att varje sida i en datafil kommer att bevaras även om processer i andra lager av arkitektur krascher i stor skala, eller om det inte går att beräkna noder.

Compute-noder i alla storskaliga lager körs på Azure Service Fabric, som kontrollerar hälso tillståndet för varje nod och utför redundans till tillgängliga felfria noder vid behov.

Mer information om hög tillgänglighet i hög tillgänglighet finns i [databas hög tillgänglighet i hög skala](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Accelererad databas återställning (ADR)

[Accelererad databas återställning (ADR)](../accelerated-database-recovery.md) är en ny databas motor funktion som avsevärt förbättrar databasens tillgänglighet, särskilt i närvaro av tids krävande transaktioner. ADR är för närvarande tillgängligt för Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Testa program Fels återhämtning

Hög tillgänglighet är en grundläggande del av SQL Database- och SQL Managed Instance-plattformen som fungerar transparent för ditt databasprogram. Vi förstår dock att du kan vilja testa hur de automatiska redundansväxlingar som initieras under planerade eller oplanerade händelser skulle påverka ett program innan du distribuerar det till produktion. Du kan utlösa en redundansväxling manuellt genom att anropa ett särskilt API för att starta om en databas, en elastisk pool eller en hanterad instans. I händelse av en redundant databas eller elastisk pool skulle API-anrop leda till omdirigering av klient anslutningar till den nya primära i en tillgänglighets zon som skiljer sig från tillgänglighets zonen för den gamla primära. Förutom att testa hur redundansväxlingen påverkar befintliga Databassessioner, kan du också kontrol lera om den ändrar prestandan från slut punkt till slut punkt på grund av ändringar i nätverks fördröjningen. Eftersom omstarten är påträngande och ett stort antal av dem kan stressa plattformen, är det bara ett failover-anrop som är tillåtet var 15: e minut för varje databas, elastisk pool eller hanterad instans.

En redundansväxling kan initieras med PowerShell, REST API eller Azure CLI:

|Distributions typ|PowerShell|REST-API| Azure CLI|
|:---|:---|:---|:---|
|Databas|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Redundansväxling av databas](/rest/api/sql/databases/failover)|[AZ rest](/cli/azure/reference-index#az-rest) kan användas för att anropa ett REST API-anrop från Azure CLI|
|Elastisk pool|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Redundans för elastisk pool](/rest/api/sql/elasticpools/failover)|[AZ rest](/cli/azure/reference-index#az-rest) kan användas för att anropa ett REST API-anrop från Azure CLI|
|Managed Instance|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Hanterade instanser – redundans](/rest/api/sql/managed%20instances%20-%20failover/failover)|[AZ SQL mi redundans](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> Kommandot redundans är inte tillgängligt för läsbara sekundär repliker av storskaliga databaser.

## <a name="conclusion"></a>Slutsats

Azure SQL Database och Azure SQL-hanterad instans har en inbyggd lösning för hög tillgänglighet, som är djupt integrerad med Azure-plattformen. Den är beroende av Service Fabric för identifiering och återställning av fel i Azure Blob Storage för data skydd och på Tillgänglighetszoner för högre fel tolerans (som tidigare nämnts i dokument som inte gäller för Azure SQL Managed instance). Dessutom utnyttjar SQL Database-och SQL-hanterade instanser den Always on-tillgänglighetsgrupper från SQL Server-instansen för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att helt kunna utnyttja fördelarna med en blandad lagrings modell och stödja de mest krävande service avtal.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure-tillgänglighetszoner](../../availability-zones/az-overview.md)
- Läs mer om [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Lär dig mer om [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Lär dig [hur du initierar en manuell redundansväxling på SQL-hanterad instans](../managed-instance/user-initiated-failover.md)
- Fler alternativ för hög tillgänglighet och haveri beredskap finns i [verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md)
