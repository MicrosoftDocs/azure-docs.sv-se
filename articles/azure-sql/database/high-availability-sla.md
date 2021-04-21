---
title: Hög tillgänglighet
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Läs mer om Azure SQL Database och SQL Managed Instance för hög tillgänglighet och funktioner
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
ms.openlocfilehash: 21ac73b461ebcb171f48621aa27a16dfc0e8c936
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781747"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Hög tillgänglighet för Azure SQL Database och SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Målet med arkitekturen för hög tillgänglighet i Azure SQL Database och SQL Managed Instance är att garantera att databasen är igång och körs minst 99,99 % av tiden (Mer information om specifika serviceavtal för olika nivåer finns i [serviceavtalet](https://azure.microsoft.com/support/legal/sla/sql-database/)för Azure SQL Database och SQL Managed Instance ), utan att du behöver bekymra dig om effekten av underhållsåtgärder och avbrott. Azure hanterar automatiskt kritiska underhållsaktiviteter, till exempel korrigeringar, säkerhetskopieringar, Windows- och Azure SQL-uppgraderingar, samt oplanerade händelser som underliggande maskinvara, programvara eller nätverksfel.  När den underliggande databasen i Azure SQL Database korrigeras eller misslyckas är driftstoppet inte märkbart om du använder logik för återförsök [i](develop-overview.md#resiliency) din app. SQL Database och SQL Managed Instance snabbt återställas även under de mest kritiska omständigheter, vilket säkerställer att dina data alltid är tillgängliga.

Lösningen för hög tillgänglighet är utformad för att säkerställa att intr dess data aldrig går förlorade på grund av fel, att underhållsåtgärder inte påverkar din arbetsbelastning och att databasen inte är en enskild felpunkt i programvaruarkitekturen. Det finns inga underhållstider eller driftstopp som kräver att du stoppar arbetsbelastningen medan databasen uppgraderas eller underhålls.

Det finns två arkitekturmodeller med hög tillgänglighet:

- **Standardtillgänglighetsmodell** som baseras på en uppdelning av beräkning och lagring.  Den förlitar sig på hög tillgänglighet och tillförlitlighet på fjärrlagringsnivån. Den här arkitekturen riktar sig till budgetorienterade affärsprogram som kan tolerera viss prestandaförsämring under underhållsaktiviteter.
- **Premium-tillgänglighetsmodell** som baseras på ett kluster med databasmotorprocesser. Den förlitar sig på det faktum att det alltid finns ett kvorum med tillgängliga databasmotornoder. Den här arkitekturen riktar sig till verksamhetskritiska program med höga I/O-prestanda, hög transaktionsfrekvens och garanterar minimal prestandapåverkan på din arbetsbelastning under underhållsaktiviteter.

SQL Database och SQL Managed Instance körs både på den senaste stabila versionen av SQL Server-databasmotorn och Windows-operativsystemet, och de flesta användare märker inte att uppgraderingar utförs kontinuerligt.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Lokal redundant tjänstnivå för Basic, Standard Generell användning och tjänstnivå

Tjänstnivån Basic, Standard och Generell användning använder standardtillgänglighetsarkitekturen för både serverlös och etablerad beräkning. Följande bild visar fyra olika noder med de avgränsade beräknings- och lagringsskikten.

![Separation av beräkning och lagring](./media/high-availability-sla/general-purpose-service-tier.png)

Standardtillgänglighetsmodellen innehåller två lager:

- Ett tillståndslöst beräkningslager som kör processen och som endast innehåller tillfälliga och cachelagrade data, till exempel TempDB, modelldatabaser på den anslutna `sqlservr.exe` SSD:n och plancache, buffertpool och kolumnlagringspool i minnet. Den här tillståndslösa noden drivs av Azure Service Fabric som initierar , kontrollerar nodens hälsotillstånd och utför redundans till en annan `sqlservr.exe` nod vid behov.
- Ett tillståndsfult datalager med databasfilerna (.mdf/.ldf) som lagras i Azure Blob Storage. Azure Blob Storage har inbyggd funktion för datatillgänglighet och redundans. Det garanterar att varje post i loggfilen eller sidan i datafilen bevaras även om `sqlservr.exe` processen kraschar.

När databasmotorn eller operativsystemet uppgraderas eller ett fel upptäcks flyttar Azure Service Fabric den tillståndslösa processen till en annan tillståndslös beräkningsnod med `sqlservr.exe` tillräckligt med ledigt utrymme. Data i Azure Blob Storage påverkas inte av flytten och data/loggfilerna kopplas till den nyligen initierade `sqlservr.exe` processen. Den här processen garanterar 99,99 % tillgänglighet, men en tung arbetsbelastning kan uppleva viss prestandaförsämring under övergången eftersom den nya processen `sqlservr.exe` börjar med kall cache.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Generell användning zonredundant tillgänglighet på tjänstnivå (förhandsversion)

Zonredundant konfiguration för tjänstnivån generell användning erbjuds för både serverlös och etablerad beräkning. Den här konfigurationen [använder Azure-tillgänglighetszoner](../../availability-zones/az-overview.md) replikera databaser över flera fysiska platser inom   en Azure-region.Genom att välja zonredundans kan du göra dina nya och befintliga serverlösa databaser och etablerade allmänna enkla databaser och elastiska pooler motståndskraftiga mot en mycket större uppsättning fel, inklusive oåterkalleliga datacenteravbrott, utan ändringar i programlogiken.

Zonredundant konfiguration för nivån generell användning har två lager:  

- Ett tillståndsful datalager med databasfilerna (.mdf/.ldf) som lagras i ZRS (zonredundant lagring). Med [ZRS](../../storage/common/storage-redundancy.md) kopieras data och loggfiler synkront över tre fysiskt isolerade Azure-tillgänglighetszoner.
- Ett tillståndslöst beräkningslager som kör sqlservr.exe-processen och endast innehåller tillfälliga och cachelagrade data, till exempel TempDB, modelldatabaser på den anslutna SSD:n och planera cacheminne, buffertpool och kolumnlagringspool i minnet. Den här tillståndslösa noden drivs av Azure Service Fabric som initierar sqlservr.exe, kontrollerar nodens hälsotillstånd och utför redundans till en annan nod vid behov. För zonredundant serverlösa och etablerade allmänna databaser är noder med reservkapacitet lättillgängliga i andra Tillgänglighetszoner för redundans.

Den zonredundanta versionen av arkitekturen för hög tillgänglighet för den allmänna tjänstnivån illustreras med följande diagram:

![Zonredundant konfiguration för generell användning](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Zonredundant konfiguration är endast tillgänglig när Gen5-beräkningsmaskinvara har valts. Den här funktionen är inte tillgänglig i SQL Managed Instance. Zonredundant konfiguration för serverlös och etablerade nivån generell användning är endast tillgänglig i följande regioner: USA, östra, USA, östra 2, USA, västra 2, Europa, norra, Europa, västra, Sydostasien, Australien, östra, Japan, östra, Storbritannien, södra och Frankrike, centrala.

> [!NOTE]
> Generell användning databaser med en storlek på 80 virtuella kärnor kan uppleva prestandaförsämring med zonredundant konfiguration. Dessutom kan åtgärder som säkerhetskopiering, återställning, databaskopiering, inställning av geo-DR-relationer och nedgradering av zonredundant databas från Affärskritisk till Generell användning uppleva långsammare prestanda för enskilda databaser som är större än 1 TB. Mer information finns [i vår dokumentation om svarstider för att skala](single-database-scale.md) en databas.
> 
> [!NOTE]
> Förhandsversionen omfattas inte av reserverad instans

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Lokalt redundant Affärskritisk Premium- och Affärskritisk-tjänstnivå

Premium- och Affärskritisk-tjänstnivåer utnyttjar Premium-tillgänglighetsmodellen, som integrerar beräkningsresurser `sqlservr.exe` (process) och lagring (lokalt ansluten SSD) på en enda nod. Hög tillgänglighet uppnås genom replikering av både beräkning och lagring till ytterligare noder, vilket skapar ett kluster med tre till fyra noder.

![Kluster med databasmotornoder](./media/high-availability-sla/business-critical-service-tier.png)

De underliggande databasfilerna (.mdf/.ldf) placeras på den anslutna SSD-lagringen för att tillhandahålla I/O med mycket kort svarstid till din arbetsbelastning. Hög tillgänglighet implementeras med en teknik som liknar SQL Server [Always On-tillgänglighetsgrupper.](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Klustret innehåller en enda primär replik som är tillgänglig för läs- och skrivbara kundarbetsbelastningar och upp till tre sekundära repliker (beräkning och lagring) som innehåller kopior av data. Den primära noden push-ar ständigt ändringar till de sekundära noderna i ordning och ser till att data synkroniseras till minst en sekundär replik innan varje transaktion genomförs. Den här processen garanterar att om den primära noden kraschar av någon anledning finns det alltid en helt synkroniserad nod att redundansa till. Redundansen initieras av Azure Service Fabric. När den sekundära repliken blir den nya primära noden skapas en annan sekundär replik för att se till att klustret har tillräckligt med noder (kvorumuppsättning). När redundansen är klar omdirigeras Azure SQL automatiskt till den nya primära noden.

Som en extra fördel inkluderar Premium-tillgänglighetsmodellen möjligheten att omdirigera skrivskyddade Azure SQL till en av de sekundära replikerna. Den här funktionen kallas [lässkalning.](read-scale-out.md) Det ger 100 % ytterligare beräkningskapacitet utan extra kostnad för skrivskyddade åtgärder som inte läses in, till exempel analytiska arbetsbelastningar, från den primära repliken.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Redundant tillgänglighet Affärskritisk Premium- och Affärskritisk-tjänstnivå 

Som standard skapas nodklustret för premiumtillgänglighetsmodellen i samma datacenter. Med introduktionen [av Azure-tillgänglighetszoner](../../availability-zones/az-overview.md)kan SQL Database placera olika repliker av Affärskritisk-databasen till olika tillgänglighetszoner i samma region. För att eliminera en felpunkt dupliceras även kontrollringen över flera zoner som tre gatewayringar (GW). Routningen till en specifik gateway-ring styrs [av Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Eftersom den zonredundanta konfigurationen i tjänstnivån Premium eller Affärskritisk inte skapar ytterligare databasredundans kan du aktivera den utan extra kostnad. Genom att välja en zonredundant konfiguration kan du göra dina Premium- eller Affärskritisk-databaser motståndskraftiga mot en mycket större uppsättning fel, inklusive oåterkalleliga datacenteravbrott, utan ändringar i programlogiken. Du kan också konvertera befintliga Premium- eller Affärskritisk databaser eller pooler till zonredundant konfiguration.

Eftersom zonredundant databaserna har repliker i olika datacenter med ett visst avstånd mellan dem kan den ökade nätverksfördröjningen öka genomförandetiden och därmed påverka prestandan för vissa OLTP-arbetsbelastningar. Du kan alltid återgå till konfigurationen för en zon genom att inaktivera inställningen för zonredundans. Den här processen är en onlineåtgärd som liknar den vanliga uppgraderingen av tjänstnivån. I slutet av processen migreras databasen eller poolen från en zonredundant ring till en enskild zonring eller vice versa.

> [!IMPORTANT]
> När du använder Affärskritisk-nivån är zonredundant konfiguration endast tillgänglig när Gen5-beräkningsmaskinvara har valts. Uppdaterad information om de regioner som stöder zonredundant databaser finns i [Tjänstsupport per region.](../../availability-zones/az-region.md)

> [!NOTE]
> Den här funktionen är inte tillgänglig i SQL Managed Instance.

Den zonredundanta versionen av arkitekturen för hög tillgänglighet illustreras med följande diagram:

![redundant arkitekturzon med hög tillgänglighet](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Tillgänglighet för tjänstnivå för hyperskala

Arkitekturen på tjänstnivå för hyperskala beskrivs i [Arkitektur](./service-tier-hyperscale.md#distributed-functions-architecture) för distribuerade funktioner och är för närvarande endast tillgänglig för SQL Database, inte SQL Managed Instance.

![Funktionell arkitektur för hyperskala](./media/high-availability-sla/hyperscale-architecture.png)

Tillgänglighetsmodellen i Hyperskala innehåller fyra lager:

- Ett tillståndslöst beräkningslager som kör processerna och endast innehåller tillfälliga och cachelagrade data, till exempel `sqlservr.exe` icke-heltäckande RBPEX-cache, TempDB, modelldatabas osv. på den anslutna SSD-disken och planera cache, buffertpool och columnstore-pool i minnet. Det här tillståndslösa lagret innehåller den primära beräkningsrepliken och eventuellt ett antal sekundära beräkningsrepliker som kan fungera som redundansmål.
- Ett tillståndslöst lagringslager som bildas av sidservrar. Det här lagret är den distribuerade lagringsmotorn för `sqlservr.exe` de processer som körs på beräkningsreplikerna. Varje sidserver innehåller endast tillfälliga och cachelagrade data, till exempel täcker RBPEX-cache på den anslutna SSD:n och datasidor som cachelagras i minnet. Varje sidserver har en länkad sidserver i en aktiv-aktiv-konfiguration för att tillhandahålla belastningsutjämning, redundans och hög tillgänglighet.
- Ett tillståndsful transaction log storage-lager som bildas av beräkningsnoden som kör loggtjänstprocessen, landningszonen för transaktionsloggen och långsiktig lagring av transaktionsloggen. Landningszoner och långsiktig lagring använder Azure Storage, [](../../storage/common/storage-redundancy.md) vilket ger tillgänglighet och redundans för transaktionsloggen, vilket säkerställer datatillförlitligheten för interna transaktioner.
- Ett tillståndsful datalagringslager med databasfilerna (.mdf/.ndf) som lagras i Azure Storage och uppdateras av sidservrar. Det här lagret använder funktioner för [datatillgänglighet och](../../storage/common/storage-redundancy.md) redundans i Azure Storage. Det garanterar att varje sida i en datafil bevaras även om processer i andra lager i arkitekturen i Hyperskala kraschar, eller om beräkningsnoder misslyckas.

Beräkningsnoder i alla hyperskalalager körs på Azure Service Fabric, som styr hälsotillståndet för varje nod och utför redundanser till tillgängliga felfria noder efter behov.

Mer information om hög tillgänglighet i Hyperskala finns i [Hög tillgänglighet för databaser i Hyperskala.](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)


## <a name="accelerated-database-recovery-adr"></a>Accelererad databasåterställning (ADR)

[Accelererad databasåterställning (ADR)](../accelerated-database-recovery.md) är en ny databasmotorfunktion som avsevärt förbättrar databasens tillgänglighet, särskilt i närvaro av långvariga transaktioner. ADR är för närvarande tillgängligt för Azure SQL Database, Azure SQL Managed Instance och Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Testa programmets återhämtning vid fel

Hög tillgänglighet är en grundläggande del av SQL Database- och SQL Managed Instance-plattformen som fungerar transparent för ditt databasprogram. Vi förstår dock att du kan vilja testa hur de automatiska redundansväxlingar som initieras under planerade eller oplanerade händelser skulle påverka ett program innan du distribuerar det till produktion. Du kan utlösa en redundans manuellt genom att anropa ett särskilt API för att starta om en databas, en elastisk pool eller en hanterad instans. Om en zonredundant serverlös eller etablerad Generell användning-databas eller elastisk pool skulle API-anropet resultera i att klientanslutningar omdirigeras till den nya primära i en tillgänglighetszon som skiljer sig från tillgänglighetszonen för den gamla primära. Förutom att testa hur redundans påverkar befintliga databassessioner kan du även kontrollera om det ändrar prestanda från slutet till slut på grund av ändringar i nätverksfördröjningen. Eftersom omstartsåtgärden är påfrestande och ett stort antal av dem kan stressa plattformen tillåts bara ett redundansanrop var 15:e minut för varje databas, elastisk pool eller hanterad instans.

En redundans kan initieras med hjälp av PowerShell, REST API eller Azure CLI:

|Distributionstyp|PowerShell|REST-API| Azure CLI|
|:---|:---|:---|:---|
|Databas|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Redundans för databas](/rest/api/sql/databases/failover)|[az rest](/cli/azure/reference-index#az_rest) kan användas för att anropa ett REST API-anrop från Azure CLI|
|Elastisk pool|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Redundans för elastisk pool](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[az rest](/cli/azure/reference-index#az_rest) kan användas för att anropa ett REST API-anrop från Azure CLI|
|Managed Instance|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Hanterade instanser – redundans](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az_sql_mi_failover)|

> [!IMPORTANT]
> Kommandot Redundans är inte tillgängligt för läsbara sekundära repliker av Hyperskala-databaser.

## <a name="conclusion"></a>Slutsats

Azure SQL Database och Azure SQL Managed Instance en inbyggd lösning för hög tillgänglighet som är djupt integrerad med Azure-plattformen. Den är beroende av Service Fabric för identifiering och återställning av fel, Azure Blob Storage för dataskydd och på Tillgänglighetszoner för högre feltolerans (som nämnts tidigare i dokumentet gäller inte för Azure SQL Managed Instance ännu). Dessutom kan SQL Database och SQL Managed Instance använda Always On-tillgänglighetsgruppens teknik från SQL Server instansen för replikering och redundans. Kombinationen av dessa tekniker gör det möjligt för program att fullt ut dra nytta av fördelarna med en blandad lagringsmodell och stödja de mest krävande serviceavtalen.

## <a name="next-steps"></a>Nästa steg

- Läs mer [om Azure-tillgänglighetszoner](../../availability-zones/az-overview.md)
- Läs mer [om Service Fabric](../../service-fabric/service-fabric-overview.md)
- Läs mer [om Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Lär [dig hur du initierar en manuell redundans på SQL Managed Instance](../managed-instance/user-initiated-failover.md)
- Fler alternativ för hög tillgänglighet och haveriberedskap finns i [Affärskontinunivå](business-continuity-high-availability-disaster-recover-hadr-overview.md)
