---
title: 'SQL Server till Azure SQL Database: översikt över migrering'
description: Lär dig mer om de verktyg och alternativ som finns tillgängliga för att migrera dina SQL Server-databaser till Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: f515725ea0f306546039b92d953254a093b15b8b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065185"
---
# <a name="migration-overview-sql-server-to-azure-sql-database"></a>Översikt över migrering: SQL Server till Azure SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Lär dig mer om alternativen och överväganden för att migrera SQL Server-databaser till Azure SQL Database. 

Du kan migrera SQL Server databaser som körs lokalt eller på: 

- SQL Server på Azure Virtual Machines.  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2).
- AWS för Relations databas tjänsten (RDS).
- Compute-motor i Google Cloud Platform (GCP).  
- Cloud SQL för SQL Server i GCP. 

Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Översikt

[Azure SQL Database](../../database/sql-database-paas-overview.md) är ett rekommenderat mål alternativ för SQL Server arbets belastningar som kräver en fullständigt hanterad plattform som en tjänst (PaaS). SQL Database hanterar de flesta funktionerna för databas hantering. Den har också inbyggd hög tillgänglighet, intelligenta funktioner för behandling, skalbarhet och prestanda som passar många program typer. 

SQL Database ger flexibilitet med flera [distributions modeller](../../database/sql-database-paas-overview.md#deployment-models) och [tjänst nivåer](../../database/service-tiers-vcore.md#service-tiers) som tillgodoser olika typer av program eller arbets belastningar.

En av de främsta fördelarna med att migrera till SQL Database är att du kan modernisera ditt program med hjälp av PaaS-funktionerna. Du kan sedan eliminera eventuella beroenden av tekniska komponenter som omfattas av instans nivån, till exempel SQL Agent-jobb.

Du kan också spara kostnader genom att använda [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för SQL Server för att migrera dina SQL Server lokala licenser till Azure SQL Database. Det här alternativet är tillgängligt om du väljer den [vCore-baserade inköps modellen](../../database/service-tiers-vcore.md).

## <a name="considerations"></a>Överväganden 

Viktiga faktorer att tänka på när du utvärderar migreringsåtgärder är: 

- Antal servrar och databaser
- Storlek på databaser
- Acceptabel drift stopp under migreringsprocessen 

De migrerings alternativ som anges i den här guiden tar hänsyn till dessa faktorer. Vid migrering av logiska data till Azure SQL Database beror tiden för migreringen på både antalet objekt i en databas och databasens storlek. 

Verktyg är tillgängliga för olika arbets belastningar och användar inställningar. Vissa verktyg kan användas för att utföra en snabb migrering av en enkel databas via ett UI-baserat verktyg. Andra verktyg kan automatisera migreringen av flera databaser för att hantera migreringar i stor skala. 

## <a name="choose-an-appropriate-target"></a>Välj ett lämpligt mål

Överväg allmänna rikt linjer som hjälper dig att välja rätt distributions modell och tjänst nivå för Azure SQL Database. Du kan välja beräknings-och lagrings resurser under distributionen och sedan [ändra dem efteråt genom att använda Azure Portal](../../database/scale-resources.md) utan att det uppstår avbrott i programmet.

**Distributions modeller**: förstå programmets arbets belastning och användnings mönstret för att bestämma mellan en enskild databas eller en elastisk pool. 

- En [enda databas](../../database/single-database-overview.md) representerar en fullständigt hanterad databas som är lämplig för de flesta moderna moln program och mikrotjänster.
- En [elastisk pool](../../database/elastic-pool-overview.md) är en samling av enskilda databaser med en delad uppsättning resurser, till exempel processor eller minne. Det är lämpligt att kombinera databaser i en pool med förutsägbara användnings mönster som effektivt kan dela samma uppsättning resurser.

**Inköps modeller**: Välj mellan vCore, DTU (Database Transaction Unit) eller Server lös inköps modeller. 

- Med [vCore-modellen](../../database/service-tiers-vcore.md) kan du välja antalet virtuella kärnor för Azure SQL Database, så det är det enklaste valet när du översätter från lokala SQL Server. Detta är det enda alternativet som har stöd för att spara licens kostnader med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- [DTU-modellen](../../database/service-tiers-dtu.md) sammanfattar underliggande beräknings-, minnes-och I/O-resurser för att tillhandahålla en blandad DTU. 
- Den [serverbaserade modellen](../../database/serverless-tier-overview.md) är för arbets belastningar som kräver automatisk skalning på begäran med beräknings resurser som faktureras per sekund. Den serverbaserade beräknings nivån pausar automatiskt databaser under inaktiva perioder (där endast lagrings utrymme faktureras). Databaser återupptas automatiskt när aktiviteten returnerar. 

**Tjänst nivåer**: Välj mellan tre tjänst nivåer som är avsedda för olika typer av program.

- [Generell användning/standard-tjänstnivå](../../database/service-tier-general-purpose.md) erbjuder ett balanserat budgetorienteradt alternativ med beräkning och lagring som lämpar sig för att leverera program på mellan nivå och lägre nivå. Redundans är inbyggt i lagrings lagret för att återställa efter fel. Den är utformad för de flesta databas arbets belastningar. 
- [Affärskritisk/Premium-tjänstnivå](../../database/service-tier-business-critical.md) är för program på hög nivå som kräver höga transaktions hastigheter, I/O för låg latens och en hög återhämtnings nivå. Det finns sekundära repliker för redundans och för att avlasta Läs arbets belastningar.
- [Nivån för storskalig service](../../database/service-tier-hyperscale.md) är för databaser som har växande data volymer och som behöver skala upp till 100 TB automatiskt i databas storlek. Den är utformad för mycket stora databaser. 

> [!IMPORTANT]
> [Transaktions logg frekvensen regleras](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) i Azure SQL Database för att begränsa hög förbruknings frekvens. Under migreringen kan du behöva skala mål databas resurser (virtuella kärnor eller DTU: er) för att under lätta trycket på CPU eller data flöde. Välj rätt storlek på mål databasen, men planera att skala resurser upp för migreringen om det behövs. 


### <a name="sql-server-vm-alternative"></a>SQL Server VM alternativ

Ditt företag kan ha krav som gör [SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) ett mer lämpligt mål än Azure SQL Database. 

Om något av följande villkor gäller för din verksamhet kan du överväga att flytta till en SQL Server virtuell dator (VM) i stället: 

- Du behöver direkt åtkomst till operativ systemet eller fil systemet, t. ex. för att installera tredje part eller anpassade agenter på samma virtuella dator med SQL Server. 
- Du har strikt beroende av funktioner som fortfarande inte stöds, till exempel FileStream/FileTable-, PolyBase-och kors instans transaktioner. 
- Du måste stanna med en angiven version av SQL Server (till exempel 2012). 
- Beräknings kraven är mycket lägre än en hanterad instans erbjuder (till exempel en vCore) och databas konsolidering är inte ett acceptabelt alternativ. 


## <a name="migration-tools"></a>Migreringsverktyg 

Vi rekommenderar följande Migreringsverktyg: 

|Teknik | Beskrivning|
|---------|---------|
| [Azure Migrate](../../../migrate/how-to-create-azure-sql-assessment.md) | Med den här Azure-tjänsten kan du upptäcka och utvärdera din SQL data-fastighets i stor skala på VMware. Det ger rekommendationer för Azure SQL-distribution, mål storlek och månads uppskattningar. | 
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb)|Det här Skriv bords verktyget från Microsoft ger sömlös utvärdering av SQL Server och migreringar med en databas till Azure SQL Database (både schema och data). </br></br>Verktyget kan installeras på en server lokalt eller på den lokala datorn som har anslutning till dina käll databaser. Migreringsprocessen är en logisk data förflyttning mellan objekt i käll-och mål databaserna.|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)|Den här Azure-tjänsten kan migrera SQL Server databaser till Azure SQL Database via Azure Portal eller automatiskt via PowerShell. Database Migration Service kräver att du väljer ett önskat virtuellt Azure-nätverk under etableringen för att säkerställa anslutning till käll SQL Server databaserna. Du kan migrera enskilda databaser eller skala. |
| | |


I följande tabell visas alternativa Migreringsverktyg: 

|Teknik |Beskrivning  |
|---------|---------|
|[Transaktionsreplikering](../../database/replication-to-sql-database.md)|Replikera data från käll SQL Server databas tabeller till Azure SQL Database genom att tillhandahålla ett migrerings alternativ för utgivarens prenumerations typ samtidigt som du underhåller transaktions konsekvensen. Stegvisa data ändringar sprids till prenumeranter när de inträffar på utgivaren.|
|[Importera export tjänst/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) är en Windows-fil med tillägget. bacpac som kapslar in en Databass schema och data. Du kan använda BACPAC för att exportera data från en SQL Server källa och importera data till Azure SQL Database. En BACPAC-fil kan importeras till en ny SQL-databas via Azure Portal. </br></br> För skalning och prestanda med stora databaser eller ett stort antal databaser kan du överväga att använda kommando rads verktyget [SqlPackage](../../database/database-import.md#using-sqlpackage) för att exportera och importera databaser.|
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Verktyget för Mass kopierings program (BCP)](/sql/tools/bcp-utility) kopierar data från en instans av SQL Server till en data fil. Använd verktyget för att exportera data från källan och importera data filen till mål-SQL-databasen. </br></br> För Mass kopierings åtgärder med hög hastighet för att flytta data till Azure SQL Database kan du använda [verktyget Smart Mass kopiering](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) för att maximera överföringshastigheten genom att dra nytta av parallell kopierings aktiviteter.|
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)|[Kopierings aktiviteten](../../../data-factory/copy-activity-overview.md) i Azure Data Factory migrerar data från käll SQL Server databaser till Azure SQL Database med hjälp av inbyggda anslutningar och en [integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory stöder en mängd olika [kopplingar](../../../data-factory/connector-overview.md) för att flytta data från SQL Server källor till Azure SQL Database.|
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync är en tjänst som bygger på Azure SQL Database som gör att du kan synkronisera valda data i båda riktningarna över flera databaser, både lokalt och i molnet.</br>Datasynkronisering är användbart i fall där data måste uppdateras i flera databaser i Azure SQL Database eller SQL Server.|
| | |

## <a name="compare-migration-options"></a>Jämför migrerings alternativ

Jämför migrations alternativen för att välja den sökväg som passar dina affärs behov. 

I följande tabell jämförs de migreringsåtgärder som vi rekommenderar: 

|Migreringsalternativ  |När du ska använda detta  |Överväganden  |
|---------|---------|---------|
|[Data Migration Assistant](/sql/dma/dma-migrateonpremsqltosqldb) | – Migrera enskilda databaser (både schema och data).  </br> – Kan hantera nedtid under migreringen av data. </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | – Migrering utför data förflyttning mellan databas objekt (från källa till mål), så vi rekommenderar att du kör det vid låg belastnings tider. </br> -Data Migration Assistant rapporterar status för migreringen per databas objekt, inklusive antalet flyttade rader.  </br> – Använd Azure Database Migration Service för stora migreringar (antal databaser eller databasens storlek).|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Migrera enskilda databaser eller skala. </br> – Kan hantera nedtid under migreringsprocessen. </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | – Migreringar i skala kan automatiseras via [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> – Tiden för att slutföra migreringen beror på databasens storlek och antalet objekt i databasen. </br> – Kräver att käll databasen anges som skrivskyddad. |
| | | |

I följande tabell jämförs alternativa flyttnings alternativ: 

|Metod eller teknik |När du ska använda detta    |Överväganden  |
|---------|---------|---------|
|[Transaktionsreplikering](../../database/replication-to-sql-database.md)| – Migrera genom att kontinuerligt Publicera ändringar från käll databas tabeller till mål SQL Database tabeller. </br> – Utför fullständig eller partiell databas migrering av valda tabeller (delmängd av en databas).  </br> </br> Källor som stöds: </br> - [SQL Server (2016 till 2019) med vissa begränsningar](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> -GCP Compute SQL Server VM  | – Installationen är relativt komplex jämfört med andra alternativ för migrering.   </br> -Tillhandahåller ett alternativ för kontinuerlig replikering för att migrera data (utan att databaserna tas offline).  </br> -Transaktionsreplikering har begränsningar att tänka på när du konfigurerar utgivaren på käll SQL Servers instansen. Se [begränsningar för publicering av objekt](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) för mer information. </br>-Det är möjligt att [övervaka replikeringsåtgärden](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Importera export tjänst/BACPAC](../../database/database-import.md)| – Migrera enskilda affärs program databaser. </br>– Lämpligt för mindre databaser.  </br>  -Kräver ingen separat migrerings tjänst eller ett separat verktyg. </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM  |  -Kräver avbrott eftersom data måste exporteras vid källan och importeras vid målet.   </br> – Fil formaten och data typerna som används i exporten eller importen måste vara konsekventa med tabell scheman för att undvika trunkering eller data typs fel.  </br> -Åtgången för att exportera en databas med ett stort antal objekt kan vara betydligt högre.       |
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Gör fullständiga eller delvis datamigreringar. </br> – Kan hantera stillestånds tid. </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM   | – Kräver stillestånds tid för att exportera data från källan och importera till målet. </br> – Fil formaten och data typerna som används i exporten eller importen måste vara konsekventa med tabell scheman. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-database.md)| -Migrera och/eller transformera data från käll SQL Server databaser. </br> -Sammanslagning av data från flera data källor till Azure SQL Database är vanligt vis för Business Intelligence (BI) arbets belastningar.  |  -Kräver att pipeliner skapas i Data Factory för att flytta data från källa till mål.   </br> - [Kostnaden](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) är en viktig faktor och baseras på faktorer som pipeline-utlösare, aktivitets körningar och varaktighet för data förflyttning. |
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Synkronisera data mellan käll-och mål databaserna.</br> – Lämpligt för att köra kontinuerlig synkronisering mellan Azure SQL Database och lokala SQL Server i ett dubbelriktat flöde. | -Azure SQL Database måste vara Hub-databasen för synkronisering med en lokal SQL Server databas som en medlems databas.</br> -Jämfört med Transaktionsreplikering har SQL Data Sync stöd för dubbelriktad data synkronisering mellan lokala och Azure SQL Database. </br> -Kan ha en högre prestanda påverkan, beroende på arbets belastningen.|
| | | |

## <a name="feature-interoperability"></a>Funktions samverkan 

Det finns flera saker att tänka på när du migrerar arbets belastningar som är beroende av andra SQL Server funktioner.

### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migrera SQL Server Integration Services-paket (SSIS) till Azure genom att distribuera om paketen till Azure-SSIS-körningsmiljön i [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [stöder migrering av SSIS-paket](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) genom att tillhandahålla en körning som skapats för att köra SSIS-paket i Azure. Du kan också skriva om SSIS ETL (Extract, Transform, Load)-logiken i Azure Data Factory med hjälp av [data flöden](../../../data-factory/concepts-data-flow-overview.md).


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrera SQL Server Reporting Services (SSRS) rapporter till sid brytnings rapporter i Power BI. Använd [verktyget RDL-migrering](https://github.com/microsoft/RdlMigration) för att förbereda och migrera dina rapporter. Microsoft har utvecklat det här verktyget för att hjälpa kunder att migrera Report Definition Language-rapporter (RDL) från sina SSRS-servrar till Power BI. Du hittar det på GitHub tillsammans med dokumentation för ett komplett migreringsscenario. 

### <a name="high-availability"></a>Hög tillgänglighet
Manuell installation av SQL Server funktioner med hög tillgänglighet som Always on Cluster instances och Always on-tillgänglighetsgrupper blir föråldrade på mål-SQL-databasen. Hög tillgänglighets arkitektur är redan inbyggd i både [generell användning (standard tillgänglighets modell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) och [affärskritisk (Premium-tillgänglighets modell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) för Azure SQL Database. Service nivån Affärskritisk/Premium ger också Läs-och utskalning som gör det möjligt att ansluta till en av de sekundära noderna i skrivskyddat syfte. 

Utöver den hög tillgänglighets arkitektur som ingår i Azure SQL Database kan du med hjälp av funktionen för [Automatisk redundans](../../database/auto-failover-group-overview.md) hantera replikering och redundans av databaser i en hanterad instans till en annan region. 

### <a name="sql-agent-jobs"></a>SQL Agent-jobb
SQL Agent-jobb stöds inte direkt i Azure SQL Database och måste distribueras till [Elastic Database-jobb (för hands version)](../../database/job-automation-overview.md).

### <a name="logins-and-groups"></a>Inloggningar och grupper
Flytta SQL-inloggningar från SQL Server källa till Azure SQL Database genom att använda Database Migration Service i offline-läge. Använd fönstret **markerade inloggningar** i Migreringsguiden för att migrera inloggningar till mål-SQL-databasen. 

Du kan också migrera Windows-användare och grupper via Database Migration Service genom att aktivera motsvarande växling på sidan Database Migration Service **konfiguration** . 

Alternativt kan du använda PowerShell- [verktyget](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) speciellt utformat av Microsoft data migration Architects. Verktyget använder PowerShell för att skapa ett Transact-SQL-skript (T-SQL) för att återskapa inloggningar och välja databas användare från källan till målet. 

PowerShell-verktyget mappar automatiskt Windows Server Active Directory-konton till Azure Active Directory-konton (Azure AD) och kan göra en UPN-sökning för varje inloggning mot källan Active Directory-instansen. Verktyget skripterar anpassade Server-och databas roller, tillsammans med roll medlemskap och användar behörigheter. Inneslutna databaser stöds inte ännu och endast en delmängd av möjliga SQL Server behörigheter har skript. 

### <a name="system-databases"></a>System databaser
För Azure SQL Database är de enda tillämpliga system databaserna [Master](/sql/relational-databases/databases/master-database) och tempdb. Läs mer i [tempdb i Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="advanced-features"></a>Avancerade funktioner 

Se till att dra nytta av de avancerade molnbaserade funktionerna i SQL Database. Du behöver till exempel inte bekymra dig om att hantera säkerhets kopieringar eftersom tjänsten gör det åt dig. Du kan återställa till vilken [tidpunkt som helst inom bevarande perioden](../../database/recovery-using-backups.md#point-in-time-restore). 

För att förstärka säkerheten bör du överväga att använda [Azure AD-autentisering](../../database/authentication-aad-overview.md), [granskning](../../database/auditing-overview.md), [hot identifiering](../../database/azure-defender-for-sql.md), [säkerhet på radnivå](/sql/relational-databases/security/row-level-security)och [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking).

Förutom avancerade hanterings-och säkerhetsfunktioner innehåller SQL Database verktyg som kan hjälpa dig att [övervaka och finjustera din arbets belastning](../../database/monitor-tune-overview.md). [Azure SQL-analys (för hands version)](../../../azure-monitor/insights/azure-sql.md) är en avancerad lösning för att övervaka prestanda för alla dina databaser i Azure SQL Database i skala och över flera prenumerationer i en enda vy. Azure SQL-analys samlar in och visualiserar viktiga prestanda mått med inbyggd intelligens för prestanda fel sökning.

[Automatisk justering](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   övervakar kontinuerligt prestanda för SQL-körnings planen och korrigerar automatiskt identifierade prestanda problem. 


## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp finns i följande resurser som har utvecklats för verkliga migreringsjobb.

|Tillgång  |Beskrivning  |
|---------|---------|
|[Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Det här verktyget ger föreslagna "bästa anpassning"-mål plattform, moln beredskap och en reparations nivå för program/databaser för en arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla en automatiserad och enhetlig besluts process för mål plattformarna.|
|[DBLoader-verktyg](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Du kan använda DBLoader för att läsa in data från avgränsade textfiler till SQL Server. Det här Windows konsol verktyget använder gränssnittet SQL Server Native Client bulk-load. Gränssnittet fungerar på alla versioner av SQL Server, tillsammans med Azure SQL Database.|
|[Skapa en Mass databas med PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Du kan använda en uppsättning med tre PowerShell-skript som skapar en resurs grupp (create_rg.ps1), den [logiska servern i Azure](../../database/logical-servers.md) (create_sqlserver.ps1) och en SQL-databas (create_sqldb.ps1). Skripten innehåller loop-funktioner så att du kan iterera och skapa så många servrar och databaser som behövs.|
|[Mass schema distribution med MSSQL-Scripter och PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Den här till gången skapar en resurs grupp, skapar en eller flera [logiska servrar i Azure](../../database/logical-servers.md) som värd Azure SQL Database, exporterar varje schema från en lokal SQL Server instans (eller flera SQL Server 2005 + instanser) och importerar scheman till Azure SQL Database.|
|[Konvertera SQL Server Agent jobb till Elastic Database-jobb](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Det här skriptet migrerar dina käll SQL Server Agent jobb till Elastic Database-jobb.|
|[Skicka e-postmeddelanden från Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Den här lösningen är ett alternativ till SendMail-kapacitet och är tillgänglig för lokala SQL Server. Den använder Azure Functions och SendGrid-tjänsten för att skicka e-postmeddelanden från Azure SQL Database.|
|[Verktyg för att flytta lokala SQL Server inloggningar till Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Ett PowerShell-skript kan skapa ett T-SQL-kommandoskript för att återskapa inloggningar och välja databas användare från lokala SQL Server att Azure SQL Database. Verktyget tillåter automatisk mappning av Windows Server Active Directory-konton till Azure AD-konton, tillsammans med alternativ för att migrera SQL Server interna inloggningar.|
|[Automation-insamling av data insamling med hjälp av logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Du kan använda logman-verktyget för att samla in perfmon-data (för att hjälpa dig att förstå bas linje prestanda) och få rekommendationer om migrerings mål. Det här verktyget använder logman.exe för att skapa kommandot som skapar, startar, stoppar och tar bort prestanda räknare som anges på en fjärran sluten SQL Server instans.|
|[Migrering av databasen till Azure SQL Database med hjälp av BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Den här white paper ger vägledning och steg som hjälper dig att påskynda migreringen från SQL Server till Azure SQL Database med hjälp av BACPAC-filer.|

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.


## <a name="next-steps"></a>Nästa steg

- Information om hur du börjar migrera dina SQL Server-databaser till Azure SQL Database finns i [SQL Server till Azure SQL Database migration guide](sql-server-to-sql-database-guide.md).

- En matris med tjänster och verktyg som kan hjälpa dig med scenarier för databas-och data migrering samt särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om SQL Database finns i:
   - [Översikt över Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metodtips för kostnad och storleksändring av arbetsbelastningar som migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Mer information om hur du utför ett/B-test för data åtkomst skiktet finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
