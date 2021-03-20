---
title: Vad är den storskaliga Service nivån?
description: Den här artikeln beskriver den storskaliga tjänst nivån i den vCore-baserade inköps modellen i Azure SQL Database och förklarar hur den skiljer sig från Generell användning-och Affärskritisk tjänst nivåerna.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: 4b5020b6cf7ac2f7aec586d7e6499285c1447b68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98209771"
---
# <a name="hyperscale-service-tier"></a>Hyperskalatjänstnivå

Azure SQL Database baseras på SQL Server databas motor arkitektur som justeras för moln miljön för att säkerställa 99,99% tillgänglighet även i händelse av infrastruktur haverier. Det finns tre arkitektur modeller som används i Azure SQL Database:

- Generell användning/standard
- Hyperskala
- Affärskritisk/Premium

Den storskaliga tjänst nivån i Azure SQL Database är den senaste tjänst nivån i den vCore-baserade inköps modellen. Den här tjänst nivån är en mycket skalbar lagrings-och beräknings prestanda nivå som utnyttjar Azure-arkitekturen för att skala ut lagrings-och beräknings resurserna för en Azure SQL Database betydligt mer än de tillgängliga gränserna för Generell användning-och Affärskritisk tjänst nivåerna.

> [!NOTE]
>
> - Mer information om Generell användning och Affärskritisk tjänst nivåer i den vCore-baserade inköps modellen finns i [generell användning](service-tier-general-purpose.md) och [affärskritisk](service-tier-business-critical.md) tjänst nivåer. En jämförelse av den vCore-baserade inköps modellen med den DTU-baserade inköps modellen finns i [Azure SQL Database köpa modeller och resurser](purchasing-models.md).
> - Den storskaliga Service nivån är för närvarande bara tillgänglig för Azure SQL Database och inte för Azure SQL-hanterad instans.

## <a name="what-are-the-hyperscale-capabilities"></a>Vilka är funktionerna för storskalighet

Den storskaliga tjänst nivån i Azure SQL Database ger följande ytterligare funktioner:

- Stöd för upp till 100 TB databas storlek
- Nästan momentan databas säkerhets kopieringar (baserat på ögonblicks bilder av filer lagrade i Azure Blob Storage) oavsett storlek utan i/o-påverkan på beräknings resurser  
- Snabba databaser återställer (baserat på ögonblicks bilder av filer) på några minuter i stället för timmar eller dagar (inte en storlek på data åtgärd)
- Högre övergripande prestanda på grund av högre logg data flöde och snabbare transaktions överförings tider, oavsett data volymer
- Snabbt skala ut – du kan etablera en eller flera skrivskyddade noder för att avlasta din Läs arbets belastning och för användning som frekvent vänte läge
- Snabbt skala upp – du kan i konstant tid skala upp dina beräknings resurser för att hantera stora arbets belastningar vid behov och sedan skala beräknings resurserna tillbaka när de inte behövs.

Den storskaliga Service nivån tar bort många av de praktiska gränser som traditionellt sett visas i moln databaserna. Om de flesta andra databaser begränsas av de resurser som är tillgängliga i en enda nod, har databaser i den storskaliga tjänst nivån inga sådana begränsningar. Med sin flexibla lagrings arkitektur växer lagringen vid behov. I själva verket skapas inte storskaliga databaser med en definierad Max storlek. En storskalig databas växer vid behov – och du debiteras bara för den kapacitet som du använder. För Läs intensiva arbets belastningar ger den storskaliga Service nivån snabb skalbarhet genom att tillhandahålla ytterligare Läs repliker efter behov för att avlasta Läs arbets belastningar.

Dessutom är tiden som krävs för att skapa databas säkerhets kopior eller skala upp eller ned inte längre kopplad till data mängden i databasen. Storskaliga databaser kan säkerhets kopie ras nästan omedelbart. Du kan också skala en databas i terabyte i terabyte upp eller ned på några minuter. Den här funktionen gör att du inte kan tänka på att bli inramad av de första konfigurations alternativen.

Mer information om beräknings storlekarna för den storskaliga tjänst nivån finns i [Egenskaper för tjänst nivå](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Vem bör tänka på den storskaliga tjänst nivån

Den storskaliga Service nivån är avsedd för de flesta företags arbets belastningar eftersom den ger stor flexibilitet och höga prestanda med oberoende skalbara beräknings-och lagrings resurser. Med möjligheten att Autoskala lagring upp till 100 TB är det ett bra val för kunder som:

- Har stora databaser lokalt och vill modernisera sina program genom att flytta till molnet
- Finns redan i molnet och begränsas av de maximala begränsningarna för databas storlek för andra tjänst nivåer (1-4 TB)
- Ha mindre databaser, men kräver snabba vertikala och vågräta beräknings skalningar, höga prestanda, omedelbar säkerhets kopiering och snabb databas återställning.

Den storskaliga tjänst nivån har stöd för ett brett utbud av SQL Server arbets belastningar, från ren OLTP till ren analys, men den är främst optimerad för OLTP-och HTAP-arbetsbelastningar (hybrid transaktion och analytisk bearbetning).

> [!IMPORTANT]
> Elastiska pooler har inte stöd för den storskaliga tjänst nivån.

## <a name="hyperscale-pricing-model"></a>Pris modell för storskalighet

Storskalig Service Tier är endast tillgängligt i [vCore-modellen](service-tiers-vcore.md). För att passa den nya arkitekturen skiljer sig pris modellen något från Generell användning eller Affärskritisk tjänst nivåer:

- **Compute**:

  Det storskaliga beräknings enhets priset är per replik. [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) priset används automatiskt för att läsa skalnings repliker. Vi skapar en primär replik och en skrivskyddad replik per storskalig databas som standard.  Användare kan justera det totala antalet repliker, inklusive primärt från 1-5.

- **Lagring**:

  Du behöver inte ange den maximala data storleken när du konfigurerar en storskalig databas. I den storskaliga nivån debiteras du för lagring av databasen baserat på den faktiska allokeringen. Storage tilldelas automatiskt mellan 40 GB och 100 TB, i steg om 10 GB. Flera datafiler kan växa vid samma tidpunkt om det behövs. En storskalig databas skapas med en start storlek på 10 GB och börjar växa med 10 GB var 10: e minut tills den når storleken på 40 GB.

Mer information om priser för storskalig prissättning finns [Azure SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arkitektur för distribuerade funktioner

Till skillnad från traditionella databas motorer som har centraliserat alla data hanterings funktioner på en plats/process (även kallade distribuerade databaser i produktion idag har flera kopior av en monolitisk-datamotor), separerar en storskalig databas motorn för frågekörning, där semantiken för olika data motorer avviker från de komponenter som tillhandahåller långsiktig lagring och hållbarhets tid för data. På det här sättet kan lagrings kapaciteten på ett smidigt sätt skalas ut så mycket som behövs (det inledande målet är 100 TB). Skrivskyddade repliker delar samma lagrings komponenter så att ingen data kopiering krävs för att skapa en ny läsbar replik.

Följande diagram illustrerar de olika typerna av noder i en storskalig databas:

![Arkitektur](./media/service-tier-hyperscale/hyperscale-architecture.png)

En storskalig databas innehåller följande typer av komponenter:

### <a name="compute"></a>Compute

Compute-noden är den plats där Relations motorn bor. Detta är där språk-, fråge-och transaktions bearbetning sker. Alla användar interaktioner med en storskalig databas sker genom de här Compute-noderna. Compute-noder har SSD-baserade cacheminnen (märkta RBPEX-elastiska buffertpooltillägget i diagrammet ovan) för att minimera antalet nätverks fördröjningar som krävs för att hämta en sida med data. Det finns en primär Compute-nod där alla Läs-och skriv åtgärder och transaktioner bearbetas. Det finns en eller flera sekundära datornoder som fungerar som frekventa vänte läge för redundans, samt fungerar som skrivskyddade Compute-noder för avlastning av Läs arbets belastningar (om den här funktionen önskas).

Databas motorn som körs på storskaliga Compute-noder är samma som i andra Azure SQL Database tjänst nivåer. När användarna interagerar med databas motorn på storskaliga Compute-noder, är det stödda området och motorns beteende samma som i andra tjänst nivåer, med undantag för [kända begränsningar](#known-limitations).

### <a name="page-server"></a>Sid Server

Sid servrar är system som representerar en utskalad lagrings motor.  Varje sid Server ansvarar för en delmängd av sidorna i databasen.  Varje sid Server styr då antingen upp till 128 GB eller upp till 1 TB data. Inga data delas på fler än en sid Server (utanför Page Server-repliker som hålls för redundans och tillgänglighet). En sid servers jobb är att hantera databas sidor till datornoderna på begäran, och för att behålla sidorna uppdaterade som transaktioner uppdatera data. Sid servrar hålls uppdaterade genom att spela upp logg poster från logg tjänsten. Sid servrar kan också hantera SSD-baserade cacheminnen för att förbättra prestanda. Långsiktig lagring av data sidor behålls i Azure Storage för ytterligare tillförlitlighet.

### <a name="log-service"></a>Logg tjänst

Logg tjänsten accepterar logg poster från den primära beräknings repliken, behåller dem i en varaktig cache och vidarebefordrar logg posterna till resten av beräknings replikerna (så att de kan uppdatera deras cacheminnen) samt de relevanta sid servrarna så att data kan uppdateras där. På så sätt sprids alla data ändringar från den primära beräknings repliken genom logg tjänsten till alla sekundära beräknings repliker och sid servrar. Slutligen flyttas logg posterna ut till långsiktig lagring i Azure Storage, vilket är ett stort antal lagrings lager. Den här mekanismen tar bort behovet av frekvent logg trunkering. Logg tjänsten har också lokala minnen och SSD-cacheminnen för att påskynda åtkomsten till logg poster.

### <a name="azure-storage"></a>Azure-lagring

Azure Storage innehåller alla datafiler i en databas. Sid servrar behåller datafilerna i Azure Storage aktuella. Den här lagringen används för säkerhets kopierings syfte och för replikering mellan Azure-regioner. Säkerhets kopieringar implementeras med lagrings ögonblicks bilder av datafiler. Återställnings åtgärder med hjälp av ögonblicks bilder är snabba oavsett data storlek. Data kan återställas till vilken tidpunkt som helst inom lagrings perioden för säkerhets kopiorna av databasen.

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhets kopior är fil-och ögonblicks bilder, och därför är de nästan momentant. Med lagrings-och beräknings separering kan du sänka säkerhets kopieringen/återställningen till lagrings lagret för att minska bearbetnings belastningen på den primära beräknings repliken. Det innebär att säkerhets kopieringen av databasen inte påverkar prestandan för den primära Compute-noden. På samma sätt är tidpunkten för tids återställning (PITR) gjord genom återställning till fil ögonblicks bilder och eftersom det inte är en storlek på data åtgärd. Återställning av en storskalig databas i samma Azure-region är en konstant åtgärd och även om flera terabyte-databaser kan återställas på några minuter i stället för timmar eller dagar. Att skapa nya databaser genom att återställa en befintlig säkerhets kopia drar också nytta av den här funktionen: att skapa databas kopior för utvecklings-eller testnings ändamål, även i flera terabyte-databaser, är doable på några minuter.

För geo-återställning av storskaliga databaser, se [återställa en storskalig databas till en annan region](#restoring-a-hyperscale-database-to-a-different-region).

## <a name="scale-and-performance-advantages"></a>Skalbarhet och prestanda för delar

Med möjligheten att snabbt kunna sätta upp eller ned ytterligare skrivskyddade Compute-noder ger storskaliga arkitektur avsevärda funktioner för Läs skalning och kan också frigöra den primära Compute-noden för att betjäna fler Skriv förfrågningar. Dessutom kan Compute-noderna skalas upp/ned snabbt på grund av den delade lagrings arkitekturen i den storskaliga arkitekturen.

## <a name="create-a-hyperscale-database"></a>Skapa en storskalig databas

En storskalig databas kan skapas med hjälp av [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/create-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)eller [CLI](/cli/azure/sql/db#az-sql-db-create). Storskaliga databaser är bara tillgängliga med den [vCore-baserade inköps modellen](service-tiers-vcore.md).

Följande T-SQL-kommando skapar en storskalig databas. Du måste ange både versions-och tjänst målet i `CREATE DATABASE` instruktionen. Se [resurs gränserna](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) för en lista över giltiga tjänst mål.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Då skapas en storskalig databas på Gen5-maskinvara med fyra kärnor.

## <a name="upgrade-existing-database-to-hyperscale"></a>Uppgradera den befintliga databasen till storskalig

Du kan flytta dina befintliga databaser i Azure SQL Database till skalning med hjälp av [Azure Portal](https://portal.azure.com), [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)eller [CLI](/cli/azure/sql/db#az-sql-db-update). För närvarande är detta en enkelriktad migrering. Du kan inte flytta databaser från storskalig till en annan tjänst nivå, förutom genom att exportera och importera data. För Proofing of Concept (POC) rekommenderar vi att du skapar en kopia av dina produktions databaser och migrerar kopian till storskalig skala. Att migrera en befintlig databas i Azure SQL Database till den storskaliga nivån är en storlek på data åtgärd.

Följande T-SQL-kommando flyttar en databas till den storskaliga tjänst nivån. Du måste ange både versions-och tjänst målet i `ALTER DATABASE` instruktionen.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ansluta till en storskalig replik av en storskalig databas

I storskaliga databaser anger `ApplicationIntent` argumentet i anslutnings strängen som klienten tillhandahåller om anslutningen dirigeras till Skriv repliken eller till en skrivskyddad sekundär replik. Om `ApplicationIntent` uppsättningen till `READONLY` och databasen inte har någon sekundär replik dirigeras anslutningen till den primära repliken och standardvärdet är `ReadWrite` beteendet.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Storskaliga sekundära repliker är alla identiska, med samma service nivå mål som den primära repliken. Om det finns fler än en sekundär replik fördelas arbets belastningen över alla tillgängliga sekundära. Varje sekundär replik uppdateras oberoende. Därför kan olika repliker ha olika data svars tider i förhållande till den primära repliken.

## <a name="database-high-availability-in-hyperscale"></a>Databas hög tillgänglighet i hög skala

Precis som i alla andra tjänst nivåer garanterar storskalig data hållbarhet för genomförda transaktioner oavsett tillgängligheten för beräknings repliker. Omfattningen av drift stopp på grund av att den primära repliken blir otillgänglig beror på typen av redundansväxling (planerad eller oplanerad) och på förekomst av minst en sekundär replik. I en planerad redundansväxling (dvs. en underhålls händelse) skapar systemet antingen den nya primära repliken innan en redundansväxling initieras, eller använder en befintlig sekundär replik som redundansväxlingen. I en oplanerad redundansväxling (dvs. ett maskin varu fel på den primära repliken) använder systemet en sekundär replik som ett failover-mål om det finns en sådan eller skapar en ny primär replik från poolen med tillgänglig beräknings kapacitet. I det senare fallet är stillestånds tiden längre, på grund av extra steg som krävs för att skapa den nya primära repliken.

Om du vill ha service avtal för storskalig skalning, se [SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Haveri beredskap för storskaliga databaser

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Återställa en storskalig databas till en annan region

Om du behöver återställa en storskalig databas i Azure SQL Database till en annan region än den som för närvarande är värd för, som en del av en haveri beredskaps åtgärd eller en detalj nivå, en annan orsak, är den primära metoden att göra en geo-återställning av databasen. Detta innebär exakt samma steg som vad du skulle använda för att återställa andra databaser i SQL Database till en annan region:

1. Skapa en [Server](logical-servers.md) i mål regionen om du inte redan har en lämplig server där.  Den här servern bör ägas av samma prenumeration som den ursprungliga (käll) servern.
2. Följ anvisningarna i avsnittet [geo-återställning](./recovery-using-backups.md#geo-restore) på sidan om hur du återställer en databas i Azure SQL Database från automatiska säkerhets kopieringar.

> [!NOTE]
> Eftersom källan och målet är i olika regioner, kan inte databasen dela lagring av ögonblicks bilder med käll databasen som i icke-geo-återställningar som slutförs snabbt oavsett databasens storlek. Om det finns en geo-återställning av en storskalig databas, är det en åtgärd för data storlek, även om målet är i det kopplade området för den geo-replikerade lagringen. Därför tar en geo-återställning tid i proportion till storleken på databasen som återställs. Om målet är i den länkade regionen, kommer data överföringen att vara inom en region, vilket kommer att bli betydligt snabbare än en data överföring över flera regioner, men det är fortfarande en åtgärd för data storlek.

## <a name="available-regions"></a><a name=regions></a>Tillgängliga regioner

Den Azure SQL Database skalnings nivån är tillgänglig i alla regioner, men aktive ras som standard i följande regioner som anges nedan. Om du vill skapa en storskalig databas i en region där skalning inte är aktiverat som standard, kan du skicka en onboarding-begäran via Azure Portal. Anvisningar finns i [begär kvot ökningar för Azure SQL Database](quota-increase-request.md) . Använd följande rikt linjer när du skickar in din begäran:

- Använd kvot typen [region åtkomst](quota-increase-request.md#region) SQL Database.
- I beskrivningen lägger du till beräknings-SKU: n/total kärnor, inklusive läsbara repliker, och anger att du begär skalnings kapacitet.
- Ange också en projektion av den totala storleken på alla databaser över tid i TB.

Aktiverade regioner:
- Australien, östra
- Australien, sydöstra
- Australien, centrala
- Brasilien, södra
- Kanada, centrala
- Central US
- Kina, östra 2
- Kina, norra 2
- Asien, östra
- East US
- USA, östra 2
- Frankrike, centrala
- Tyskland, västra centrala
- Japan, östra
- Japan, västra
- Sydkorea, centrala
- Sydkorea, södra
- USA, norra centrala
- Europa, norra
- Östra Norge
- Norge, väst
- Sydafrika, norra
- USA, södra centrala
- Sydostasien
- Schweiz, västra
- Storbritannien, södra
- Storbritannien, västra
- USA DoD, centrala
- USA DoD, östra
- US govt-Arizona
- US govt Texas
- USA, västra centrala
- Europa, västra
- USA, västra
- USA, västra 2

## <a name="known-limitations"></a>Kända begränsningar

Detta är de aktuella begränsningarna för den storskaliga tjänst nivån från och med GA.  Vi arbetar aktivt för att ta bort så många av dessa begränsningar som möjligt.

| Problem | Beskrivning |
| :---- | :--------- |
| I fönstret hantera säkerhets kopior för en server visas inte storskaliga databaser. De kommer att filtreras från vyn.  | Den storskaliga metoden för att hantera säkerhets kopieringar har en separat metod för att hantera säkerhets kopior, så Long-Term inställningarna för kvarhållning och säkerhets kopiering av tidpunkter gäller inte. Därför visas inte storskaliga databaser i fönstret hantera säkerhets kopiering.<br><br>För databaser som har migrerats till storskaligheten från andra Azure SQL Database tjänst nivåer behålls säkerhets kopiorna innan migreringen [](automated-backups-overview.md#backup-retention) för käll databasens varaktighet. Dessa säkerhets kopior kan användas för att [återställa](recovery-using-backups.md#programmatic-recovery-using-automated-backups) käll databasen till en tidpunkt innan migreringen.|
| Återställning från tidpunkt | En databas som inte är storskalig kan inte återställas som en storskalig databas och en storskalig databas kan inte återställas som en databas som inte är storskalig. För en icke-storskalig databas som har migrerats till storskalig genom att ändra dess tjänst nivå återställer du till en tidpunkt innan migreringen och inom lagrings perioden för säkerhets kopior av databasen stöds [program mässigt](recovery-using-backups.md#programmatic-recovery-using-automated-backups). Den återställda databasen får inte skalas. |
| När du ändrar Azure SQL Database tjänst nivå till storskalig, Miss lyckas åtgärden om databasen har några datafiler som är större än 1 TB | I vissa fall kan det vara möjligt att undvika det här problemet genom att [minska](file-space-manage.md#shrinking-data-files) de stora filerna till mindre än 1 TB innan du försöker ändra tjänst nivån till storskalig skalning. Använd följande fråga för att fastställa den aktuella storleken på databasfiler. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL-hanterad instans | Azure SQL Managed instance stöds för närvarande inte med storskaliga databaser. |
| Elastiska pooler |  Elastiska pooler stöds inte för närvarande med skalning.|
| Migrering till storskalig skalning är för närvarande en enkelriktad åtgärd | När en databas har migrerats till storskalig kan den inte migreras direkt till en icke-storskalig tjänst nivå. Det enda sättet att migrera en databas från storskalig till icke-storskalig är att exportera/importera med hjälp av en BACPAC-fil eller annan teknik för data förflyttning (Mass kopiering, Azure Data Factory, Azure Databricks, SSIS osv.) BACPAC export/import från Azure Portal, från PowerShell med [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) eller [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport), från Azure CLI med hjälp av [AZ SQL DB export](/cli/azure/sql/db#az-sql-db-export) och [az SQL DB-import](/cli/azure/sql/db#az-sql-db-import)och från [REST API](/rest/api/sql/databases%20-%20import%20export) stöds inte. BACPAC import/export för mindre storskaliga databaser (upp till 200 GB) stöds med SSMS och [SqlPackage](/sql/tools/sqlpackage) version 18,4 och senare. För större databaser kan BACPAC export/import ta lång tid och kan Miss lyckas av olika orsaker.|
| Migrering av databaser med In-Memory OLTP-objekt | Storskalig stöder en delmängd av In-Memory OLTP-objekt, inklusive minnesoptimerade tabell typer, Table-variabler och internt kompilerade moduler. Men när en typ av In-Memory OLTP-objekt finns i databasen som migreras, stöds inte migrering från Premium-och Affärskritisks tjänst nivåer till storskalig skalning. Alla In-Memory OLTP-objekt och deras beroenden måste släppas för att en sådan databas ska kunna migreras till skalning. När databasen har migrerats kan dessa objekt återskapas. Varaktiga och icke-varaktiga minnesoptimerade tabeller stöds inte för närvarande i storskaliga och måste ändras till disk tabeller.|
| Geo-replikering  | Du kan inte konfigurera geo-replikering för Azure SQL Database storskaligt. |
| Databas kopia | Databas kopiering på storskaligheten är nu i en offentlig för hands version. |
| Intelligenta databas funktioner | Med undantag för alternativet "framtvinga plan" stöds inte alla andra automatiska justerings alternativ i den storskaliga: alternativen kan verka vara aktiverade, men inga rekommendationer eller åtgärder har gjorts. |
| Information om frågeprestanda | Fråga prestanda insikter stöds för närvarande inte för storskaliga databaser. |
| Krymp databas | DBCC SHRINKDATABASE eller DBCC SHRINKFILE stöds för närvarande inte för storskaliga databaser. |
| Kontroll av databas integritet | DBCC CHECKDB stöds för närvarande inte för storskaliga databaser. DBCC CHECKFILEGROUP och DBCC CHECKTABLE kan användas som en lösning. Mer information om data integritets hantering i Azure SQL Database finns i [data integritet i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) . |

## <a name="next-steps"></a>Nästa steg

- Vanliga frågor och svar om storskalighet finns i vanliga frågor och svar [om storskaliga frågor](service-tier-hyperscale-frequently-asked-questions-faq.md).
- Information om tjänst nivåer finns i [tjänst nivåer](purchasing-models.md)
- Se [Översikt över resurs begränsningar på en server](resource-limits-logical-server.md) för information om begränsningar på Server-och prenumerations nivåer.
- För inköps modell gränser för en enskild databas, se [Azure SQL Database vCore-baserade inköps modell gränser för en enskild databas](resource-limits-vcore-single-databases.md).
- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](features-comparison.md).
