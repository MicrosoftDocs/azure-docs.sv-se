---
title: Vad är tjänstnivån Hyperskala?
description: Den här artikeln beskriver tjänstnivån Hyperskala i köpmodellen baserad på virtuella kärnor i Azure SQL Database och förklarar hur den skiljer sig från Generell användning och Affärskritisk tjänstnivåer.
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
ms.openlocfilehash: e0982b4a43a931552574e447d5639d3fa92402d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773780"
---
# <a name="hyperscale-service-tier"></a>Hyperskalatjänstnivå

Azure SQL Database baseras på SQL Server Database Engine-arkitekturen som justeras för molnmiljön för att säkerställa 99,99 % tillgänglighet även i fall av infrastrukturfel. Det finns tre arkitekturmodeller som används i Azure SQL Database:

- Generell användning/Standard
- Hyperskala
- Affärskritisk/Premium

Tjänstnivån Hyperskala i Azure SQL Database är den senaste tjänstnivån i köpmodellen baserad på vCore. Den här tjänstnivån är en mycket skalbar lagrings- och beräkningsprestandanivå som utnyttjar Azure-arkitekturen för att skala ut lagrings- och beräkningsresurserna för en Azure SQL Database betydligt utöver de gränser som är tillgängliga för Generell användning- och Affärskritisk-tjänstnivåer.

> [!NOTE]
>
> - Mer information om Generell användning och Affärskritisk-tjänstnivåer i köpmodellen baserad på virtuella kärnor finns i [Generell användning](service-tier-general-purpose.md) och [Affärskritisk](service-tier-business-critical.md) tjänstnivåer. En jämförelse av köpmodellen baserad på virtuella kärnor med köpmodellen baserad på DTU finns i Azure SQL Database [köpmodeller och resurser](purchasing-models.md).
> - Tjänstnivån Hyperskala är för närvarande endast tillgänglig för Azure SQL Database och inte Azure SQL Managed Instance.

## <a name="what-are-the-hyperscale-capabilities"></a>Vilka är funktionerna i Hyperskala?

Tjänstnivån Hyperskala i Azure SQL Database ger följande ytterligare funktioner:

- Stöd för upp till 100 TB databasstorlek
- Nästan omedelbara databassäkerhetskopior (baserat på filögonblicksbilder som lagras i Azure Blob Storage) oavsett storlek utan att I/H påverkar beräkningsresurser  
- Snabba databasåterställningar (baserat på filögonblicksbilder) i minuter i stället för timmar eller dagar (inte en storlek på dataåtgärden)
- Högre övergripande prestanda på grund av högre loggdataflöde och snabbare transaktionsgenomströmning oavsett datavolymer
- Snabb utskalning – du kan etablera en eller flera skrivskyddade noder för avlastning av läsarbetsbelastningen och för användning i hett vänteläge
- Snabb uppskalning – du kan i konstant tid skala upp dina beräkningsresurser för att hantera tunga arbetsbelastningar när det behövs och sedan skala ned beräkningsresurserna igen när det inte behövs.

Tjänstnivån Hyperskala tar bort många av de praktiska gränser som traditionellt sett finns i molndatabaser. Om de flesta andra databaser begränsas av resurserna som är tillgängliga i en enda nod har databaser på tjänstnivån Hyperskala inga sådana begränsningar. Med sin flexibla lagringsarkitektur växer lagringsutrymmet efter behov. I själva verket skapas inte databaser i hyperskala med en definierad maxstorlek. En hyperskaladatabas växer efter behov och du debiteras bara för den kapacitet som du använder. För läsintensiva arbetsbelastningar ger tjänstnivån Hyperskala snabb utskalning genom att etablera ytterligare skrivskyddade repliker efter behov för avlastning av läsarbetsbelastningar.

Dessutom är den tid som krävs för att skapa säkerhetskopior av databasen eller för att skala upp eller ned inte längre kopplad till datavolymen i databasen. Databaser i hyperskala kan säkerhetskopieras praktiskt taget omedelbart. Du kan också skala en databas med tiotals terabyte uppåt eller nedåt på några minuter. Den här funktionen gör att du inte behöver oroa dig för att bli inrutad i dina första konfigurationsalternativ.

Mer information om beräkningsstorlekarna för tjänstnivån Hyperskala finns i [Egenskaper för tjänstnivå.](service-tiers-vcore.md#service-tiers)

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Vem bör överväga tjänstnivån Hyperskala

Tjänstnivån Hyperskala är avsedd för de flesta företagsarbetsbelastningar eftersom den ger stor flexibilitet och höga prestanda med oberoende skalbara beräknings- och lagringsresurser. Med möjlighet att autoskala lagring upp till 100 TB är det ett bra alternativ för kunder som:

- Ha stora databaser lokalt och vill modernisera sina program genom att flytta till molnet
- Finns redan i molnet och begränsas av de maximala storleksbegränsningarna för databaser på andra tjänstnivåer (1–4 TB)
- Ha mindre databaser, men kräver snabb vertikal och horisontell beräkningsskalning, höga prestanda, omedelbar säkerhetskopiering och snabb databasåterställning.

Tjänstnivån Hyperskala stöder ett brett utbud av SQL Server-arbetsbelastningar, från ren OLTP till ren analys, men den är främst optimerad för OLTP- och HTAP-arbetsbelastningar (hybrid transaction and analytical processing).

> [!IMPORTANT]
> Elastiska pooler stöder inte tjänstnivån Hyperskala.

## <a name="hyperscale-pricing-model"></a>Prismodell för hyperskala

Tjänstnivån Hyperskala är endast tillgänglig i [vCore-modellen](service-tiers-vcore.md). För att passa den nya arkitekturen skiljer sig prismodellen något från Generell användning eller Affärskritisk tjänstnivåer:

- **Beräkna**:

  Priset för beräkningsenhet i Hyperskala är per replik. Priset [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) tillämpas automatiskt på skrivskyddade skalningsrepliker. Vi skapar en primär replik och en skrivskyddad replik per databas i hyperskala som standard.  Användare kan justera det totala antalet repliker, inklusive det primära från 1 till 5.

- **Lagring**:

  Du behöver inte ange den maximala datastorleken när du konfigurerar en databas i hyperskala. På hyperskalanivån debiteras du för lagringen för databasen baserat på den faktiska allokeringen. Lagringen allokeras automatiskt mellan 40 GB och 100 TB i steg om 10 GB. Flera datafiler kan växa samtidigt om det behövs. En hyperskaladatabas skapas med en startstorlek på 10 GB och den börjar växa med 10 GB var 10:e minut tills den når storleken 40 GB.

Mer information om priser för Hyperskala finns [i Azure SQL Database prissättning](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arkitektur för distribuerade funktioner

Till skillnad från traditionella databasmotorer som har centraliserat alla datahanteringsfunktioner på en plats/process (även så kallade distribuerade databaser i produktion idag har flera kopior av en monolitisk datamotor), separerar en databas i hyperskala frågebearbetningsmotorn, där semantiken för olika datamotorer skiljer sig från de komponenter som tillhandahåller långsiktig lagring och hållbarhet för data. På så sätt kan lagringskapaciteten skalas ut smidigt så långt det behövs (det första målet är 100 TB). Skrivskyddade repliker delar samma lagringskomponenter så ingen datakopiering krävs för att skapa en ny läsbar replik.

Följande diagram illustrerar de olika typerna av noder i en hyperskala-databas:

![Arkitektur](./media/service-tier-hyperscale/hyperscale-architecture.png)

En hyperskaladatabas innehåller följande olika typer av komponenter:

### <a name="compute"></a>Compute

Beräkningsnoden är den plats där relationsmotorn finns. Det är här som språk-, fråge- och transaktionsbearbetning sker. Alla användarinteraktioner med en databas i hyperskala sker via dessa beräkningsnoder. Beräkningsnoder har SSD-baserade cacheminnen (märkta RBPEX – Resilient Buffer Pool Extension i föregående diagram) för att minimera antalet nätverksresor som krävs för att hämta en sida med data. Det finns en primär beräkningsnod där alla skrivskyddade arbetsbelastningar och transaktioner bearbetas. Det finns en eller flera sekundära beräkningsnoder som fungerar som reservnoder för snabb växling vid fel, samt fungerar som skrivskyddade beräkningsnoder för avlastning av läsarbetsbelastningar (om den här funktionen önskas).

Databasmotorn som körs på beräkningsnoder i Hyperskala är samma som Azure SQL Database andra tjänstnivåer. När användare interagerar med databasmotorn på beräkningsnoder i Hyperskala är det ytområde och motorbeteende som stöds samma som på andra tjänstnivåer, med undantag för [kända begränsningar.](#known-limitations)

### <a name="page-server"></a>Sidserver

Sidservrar är system som representerar en utskalade lagringsmotor.  Varje sidserver ansvarar för en delmängd av sidorna i databasen.  Varje sidserver styr nominellt antingen upp till 128 GB eller upp till 1 TB data. Inga data delas på mer än en sidserver (utanför sidserverrepliker som behålls för redundans och tillgänglighet). En sidservers uppgift är att betjäna databassidor till beräkningsnoderna på begäran och att hålla sidorna uppdaterade när transaktioner uppdaterar data. Sidservrar hålls uppdaterade genom att spela upp loggposter från loggtjänsten. Sidservrar underhåller även SSD-baserade cacheminnen för att förbättra prestandan. Långsiktig lagring av datasidor lagras i en Azure Storage för ytterligare tillförlitlighet.

### <a name="log-service"></a>Loggtjänst

Loggtjänsten accepterar loggposter från den primära beräkningsrepliken, bevarar dem i ett beständigt cacheminne och vidarebefordrar loggposterna till resten av beräkningsreplikerna (så att de kan uppdatera sina cacheminnen) samt relevanta sidservrar, så att data kan uppdateras där. På så sätt sprids alla dataändringar från den primära beräkningsrepliken genom loggtjänsten till alla sekundära beräkningsrepliker och sidservrar. Slutligen push-skickas loggposterna ut till långsiktig lagring i Azure Storage, vilket är en praktiskt taget oändlig lagringsplats. Den här mekanismen tar bort behovet av frekventa logg trunkering. Loggtjänsten har också lokalt minne och SSD-cacheminnen för att påskynda åtkomsten till loggposter.

### <a name="azure-storage"></a>Azure-lagring

Azure Storage innehåller alla datafiler i en databas. Sidservrar håller datafilerna Azure Storage uppdaterade. Den här lagringen används för säkerhetskopiering, samt för replikering mellan Azure-regioner. Säkerhetskopior implementeras med hjälp av ögonblicksbilder av datafiler. Återställningsåtgärder med ögonblicksbilder är snabba oavsett datastorlek. Data kan återställas till valfri tidpunkt inom databasens kvarhållningsperiod för säkerhetskopior.

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

Säkerhetskopior baseras på filögonblicksbilder och är därför nästan omedelbart. Lagrings- och beräkningsseparation gör att säkerhetskopierings-/återställningsåtgärden kan pushas till lagringslagret för att minska bearbetningsbelastningen på den primära beräkningsrepliken. Därför påverkar inte databassäkerhetskopiering den primära beräkningsnodens prestanda. Återställning till tidpunkt (PITR) görs på samma sätt genom att återgå till ögonblicksbilder av filer och därför inte är en storlek på dataåtgärden. Återställning av en hyperskaladatabas i samma Azure-region är en konstant tidsåtgärd, och databaser med flera terabyte kan återställas på några minuter i stället för timmar eller dagar. Att skapa nya databaser genom att återställa en befintlig säkerhetskopia drar också nytta av den här funktionen: det går att skapa databaskopior för utveckling eller testning, även för databaser med flera terabyte, på några minuter.

Information om geo-återställning av hyperskaladatabaser finns [i Återställa en hyperskala-databas till en annan region.](#restoring-a-hyperscale-database-to-a-different-region)

## <a name="scale-and-performance-advantages"></a>Skalnings- och prestandafördelar

Med möjligheten att snabbt skapa ytterligare skrivskyddade beräkningsnoder möjliggör arkitekturen i Hyperskala betydande lässkalningsfunktioner och kan även frigöra den primära beräkningsnoden för att hantera fler skrivbegäranden. Dessutom kan beräkningsnoderna skalas upp/ned snabbt på grund av arkitekturen för delad lagring i arkitekturen för hyperskala.

## <a name="create-a-hyperscale-database"></a>Skapa en databas i hyperskala

En hyperskaladatabas kan skapas med [hjälp Azure Portal,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/create-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)eller [CLI.](/cli/azure/sql/db#az_sql_db_create) Hyperskaladatabaser är endast tillgängliga med [köpmodellen baserad på virtuella kärnor.](service-tiers-vcore.md)

Följande T-SQL-kommando skapar en hyperskaladatabas. Du måste ange både utgåvan och tjänstmålet i `CREATE DATABASE` -instruktionen. Se [resursbegränsningarna för](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) en lista över giltiga tjänstmål.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Detta skapar en hyperskaladatabas på Gen5-maskinvara med fyra kärnor.

## <a name="upgrade-existing-database-to-hyperscale"></a>Uppgradera en befintlig databas till Hyperskala

Du kan flytta dina befintliga databaser Azure SQL Database till Hyperskala [med hjälp av Azure Portal,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/alter-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)eller [CLI](/cli/azure/sql/db#az_sql_db_update). Just nu är detta en envägsmigrering. Du kan inte flytta databaser från Hyperskala till en annan tjänstnivå, förutom genom att exportera och importera data. För konceptbevis rekommenderar vi att du gör en kopia av dina produktionsdatabaser och migrerar kopian till Hyperskala. Migrering av en befintlig databas i Azure SQL Database till hyperskalenivån är en storlek på dataåtgärden.

Följande T-SQL-kommando flyttar en databas till tjänstnivån Hyperskala. Du måste ange både utgåvan och tjänstmålet i `ALTER DATABASE` -instruktionen.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Ansluta till en skrivskyddad replik av en hyperskala-databas

I Hyperskala-databaser avgör argumentet i anslutningssträngen som tillhandahålls av klienten om anslutningen dirigeras till skrivrepliken eller till en `ApplicationIntent` skrivskyddad sekundär replik. Om inställt till och databasen inte har någon sekundär replik dirigeras anslutningen till den primära repliken och fungerar `ApplicationIntent` `READONLY` som `ReadWrite` standard.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Sekundära hyperskala-repliker är identiska med samma servicenivåmål som den primära repliken. Om det finns fler än en sekundär replik distribueras arbetsbelastningen över alla tillgängliga sekundära repliker. Varje sekundär replik uppdateras oberoende av varandra. Därför kan olika repliker ha olika datasvarstid i förhållande till den primära repliken.

## <a name="database-high-availability-in-hyperscale"></a>Hög tillgänglighet för databaser i Hyperskala

Precis som på alla andra tjänstnivåer garanterar Hyperskala datatillförlitlighet för de intjänade transaktionerna oavsett tillgänglighet för beräkningsrepliker. Stilleståndstiden på grund av att den primära repliken blir otillgänglig beror på typen av redundans (planerad kontra oplanerad) och på förekomsten av minst en sekundär replik. I en planerad redundans (dvs. en underhållshändelse) skapar systemet antingen den nya primära repliken innan en redundans initieras eller använder en befintlig sekundär replik som redundansmål. I en oplanerad redundans (dvs. ett maskinvarufel på den primära repliken) använder systemet en sekundär replik som ett redundansmål om det finns en sådan, eller skapar en ny primär replik från poolen med tillgänglig beräkningskapacitet. I det senare fallet är stilleståndstiden längre på grund av de extra steg som krävs för att skapa den nya primära repliken.

För Serviceavtal för hyperskala, [se SLA för Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Haveriberedskap för hyperskaladatabaser

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Återställa en hyperskala-databas till en annan region

Om du behöver återställa en databas i hyperskala i Azure SQL Database till en annan region än den som den för närvarande finns i, som en del av en katastrofåterställningsåtgärd eller ett test, en flytt eller någon annan orsak, är den primära metoden att göra en geo-återställning av databasen. Detta omfattar exakt samma steg som du använder för att återställa andra databaser i SQL Database till en annan region:

1. Skapa en [server](logical-servers.md) i målregionen om du inte redan har en lämplig server där.  Den här servern ska ägas av samma prenumeration som den ursprungliga (källservern).
2. Följ anvisningarna i [avsnittet om geo-återställning](./recovery-using-backups.md#geo-restore) på sidan om att återställa en databas i Azure SQL Database från automatiska säkerhetskopior.

> [!NOTE]
> Eftersom källan och målet finns i separata regioner kan databasen inte dela ögonblicksbildlagring med källdatabasen som i icke-geo-återställningar, som slutförs snabbt oavsett databasstorlek. Vid geo-återställning av en databas i hyperskala blir det en storleksåtgärd för data, även om målet finns i den sammankopplade regionen för den geo-replikerade lagringen. Därför tar en geo-återställning tid i proportion till storleken på databasen som återställs. Om målet finns i den parkopplade regionen kommer dataöverföringen att ske inom en region, vilket är betydligt snabbare än en dataöverföring mellan regioner, men det kommer fortfarande att vara en datastorleksåtgärd.

## <a name="available-regions"></a><a name=regions></a>Tillgängliga regioner

Nivån Azure SQL Database Hyperskala är tillgänglig i alla regioner men är aktiverad som standard i följande regioner som anges nedan. Om du vill skapa en databas i hyperskala i en region där Hyperskala inte är aktiverat som standard kan du skicka en onboarding-begäran via Azure Portal. Instruktioner finns i Begär [kvotökningar för Azure SQL Database](quota-increase-request.md) anvisningar. Använd följande riktlinjer när du skickar din begäran:

- Använd typen [Regionsåtkomst](quota-increase-request.md#region) SQL Database kvottyp.
- I beskrivningen lägger du till beräknings-SKU/totalt antal kärnor inklusive läsbara repliker och anger att du begär hyperskalakapacitet.
- Ange också en projektion av den totala storleken för alla databaser över tid i TB.

Aktiverade regioner:
- Australien, östra
- Australien, sydöstra
- Australien, centrala
- Brasilien, södra
- Kanada, centrala
- Kanada, östra
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
- Mellanöstern
- Tyskland, västra
- Sydafrika, norra
- USA, södra centrala
- Sydostasien
- Schweiz, västra
- Storbritannien, södra
- Storbritannien, västra
- USA DoD, centrala
- USA DoD, östra
- Us Gov, Arizona
- US Gov, Texas
- USA, västra centrala
- Europa, västra
- USA, västra
- USA, västra 2

## <a name="known-limitations"></a>Kända begränsningar

Det här är de aktuella begränsningarna för tjänstnivån Hyperskala från och med ga.  Vi arbetar aktivt med att ta bort så många av dessa begränsningar som möjligt.

| Problem | Beskrivning |
| :---- | :--------- |
| Fönstret Hantera säkerhetskopieringar för en server visar inte hyperskaladatabaser. Dessa filtreras från vyn.  | Hyperskala har en separat metod för att hantera säkerhetskopior, så Long-Term kvarhållning av säkerhetskopior och kvarhållning av säkerhetskopior från tidpunkt gäller inte. Därför visas inte hyperskaladatabaser i fönstret Hantera säkerhetskopiering.<br><br>För databaser som migrerats till Hyperskala från Azure SQL Database andra tjänstnivåer sparas säkerhetskopior [](automated-backups-overview.md#backup-retention) före migreringen under hela kvarhållningsperioden för säkerhetskopior för källdatabasen. Dessa säkerhetskopior kan användas för att [återställa](recovery-using-backups.md#programmatic-recovery-using-automated-backups) källdatabasen till en tidpunkt före migreringen.|
| Återställning från tidpunkt | En icke-Hyperskala-databas kan inte återställas som en databas i hyperskala och en databas i hyperskala kan inte återställas som en databas som inte är en hyperskaladatabas. För en databas som inte är hyperskala och som har migrerats till Hyperskala genom att ändra tjänstnivån kan du återställa till en tidpunkt före migreringen och inom kvarhållningsperioden för säkerhetskopior för databasen [programmatiskt](recovery-using-backups.md#programmatic-recovery-using-automated-backups). Den återställda databasen kommer att vara icke-Hyperskala. |
| När du Azure SQL Database till Hyperskala misslyckas åtgärden om databasen har datafiler som är större än 1 TB | I vissa fall kan det vara möjligt [](file-space-manage.md#shrinking-data-files) att lösa det här problemet genom att minska de stora filerna till mindre än 1 TB innan du försöker ändra tjänstnivån till Hyperskala. Använd följande fråga för att fastställa den aktuella storleken på databasfilerna. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL-hanterad instans | Azure SQL Managed Instance stöds inte för närvarande med databaser i hyperskala. |
| Elastiska pooler |  Elastiska pooler stöds för närvarande inte med Hyperskala.|
| Migrering till hyperskala är för närvarande en envägsåtgärd | När en databas har migrerats till Hyperskala kan den inte migreras direkt till en tjänstnivå som inte är hyperskala. För närvarande är det enda sättet att migrera en databas från Hyperskala till icke-Hyperskala att exportera/importera med hjälp av en bacpac-fil eller andra dataförflyttningstekniker (masskopiering, Azure Data Factory, Azure Databricks, SSIS osv.) Bacpac export/import från Azure Portal, från PowerShell med [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) eller [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport), från Azure CLI med [az sql db export](/cli/azure/sql/db#az_sql_db_export) och az sql db [import](/cli/azure/sql/db#az_sql_db_import), och från [REST API](/rest/api/sql/) stöds inte. Bacpac-import/-export för mindre Hyperskala-databaser (upp till 200 GB) stöds med SSMS och [SqlPackage](/sql/tools/sqlpackage) version 18.4 och senare. För större databaser kan bacpac-export/-import ta lång tid och kan misslyckas av olika orsaker.|
| Migrering av databaser med In-Memory OLTP-objekt | Hyperskala stöder en delmängd In-Memory OLTP-objekt, inklusive minnesoptimerade tabelltyper, tabellvariabler och inbyggda kompilerade moduler. Men om det finns In-Memory OLTP-objekt i databasen som migreras, stöds inte migrering från Premium- Affärskritisk-tjänstnivåer till Hyperskala. För att migrera en sådan databas till Hyperskala måste alla In-Memory OLTP-objekt och deras beroenden tas bort. När databasen har migrerats kan dessa objekt återskapas. Varaktiga och icke-varaktiga minnesoptimerade tabeller stöds inte för närvarande i Hyperskala och måste ändras till disktabeller.|
| Geo-replikering  | Du kan ännu inte konfigurera geo-replikering för Azure SQL Database Hyperskala. |
| Databaskopia | Databaskopiering i Hyperskala är nu i offentlig förhandsversion. |
| Intelligenta databasfunktioner | Med undantag för alternativet "Force Plan" stöds inte alla andra alternativ för automatisk justering i Hyperskala än: alternativ kan verka vara aktiverade, men det görs inga rekommendationer eller åtgärder. |
| Information om frågeprestanda | Query Performance Insights stöds för närvarande inte för hyperskala-databaser. |
| Krymp databas | DBCC SHRINKDATABASE eller DBCC SHRINKFILE stöds för närvarande inte för hyperskaladatabaser. |
| Databasintegritetskontroll | DBCC CHECKDB stöds för närvarande inte för hyperskaladatabaser. DBCC CHECKFILEGROUP och DBCC CHECKTABLE kan användas som en lösning. Se [Dataintegritet i Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) mer information om dataintegritetshantering i Azure SQL Database. |

## <a name="next-steps"></a>Nästa steg

- Vanliga frågor och svar om Hyperskala finns [i Vanliga frågor och svar om Hyperskala.](service-tier-hyperscale-frequently-asked-questions-faq.md)
- Information om tjänstnivåer finns i [Tjänstnivåer](purchasing-models.md)
- Se [Översikt över resursgränser på en server för](resource-limits-logical-server.md) information om gränser på server- och prenumerationsnivå.
- Information om hur du köper modellbegränsningar för en enkel databas finns i Azure SQL Database för [vCore-baserade köpmodeller för en enskild databas.](resource-limits-vcore-single-databases.md)
- En lista över funktioner och jämförelser finns i [Vanliga SQL-funktioner.](features-comparison.md)
