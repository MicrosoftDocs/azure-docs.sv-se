---
title: 'SQL Server till SQL Database: översikt över migrering'
description: Lär dig mer om de olika verktygen och alternativen som är tillgängliga för att migrera dina SQL Server-databaser till Azure SQL Database.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 2d668775e8d11faa0b2913cebc41e5217b49c278
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357344"
---
# <a name="migration-overview-sql-server-to-sql-database"></a>Översikt över migrering: SQL Server till SQL Database
[!INCLUDE[appliesto--sqldb](../../includes/appliesto-sqldb.md)]

Lär dig mer om olika flyttnings alternativ och överväganden för att migrera SQL Server till Azure SQL Database. 

Du kan migrera SQL Server som körs lokalt eller på: 

- SQL Server på virtuella datorer  
- Amazon Web Services (AWS) EC2 
- Amazon Relations databas tjänst (AWS RDS) 
- Beräknings motor (Google Cloud Platform-GCP)  
- Cloud SQL för SQL Server (Google Cloud Platform – GCP) 

För andra scenarier, se [Guide för databas migrering](https://datamigration.microsoft.com/). 

## <a name="overview"></a>Översikt

[Azure SQL Database](../../database/sql-database-paas-overview.md) är ett rekommenderat mål alternativ för SQL Server arbets belastningar som kräver en fullständigt hanterad plattform som en tjänst (PaaS). SQL Database hanterar de flesta funktionerna för databas hantering, tillsammans med hög tillgänglighet, behandling av intelligenta frågor, skalbarhet och prestanda som är inbyggda för att passa många olika program typer. 

SQL Database ger flexibilitet med flera [distributions modeller](../../database/sql-database-paas-overview.md#deployment-models) och [tjänst nivåer](../../database/service-tiers-vcore.md#service-tiers) som tillgodoser olika typer av program eller arbets belastningar.

En av de främsta fördelarna med att migrera till SQL Database är att du kan modernisera ditt program genom att använda PaaS-funktionerna och eliminera eventuella beroenden av tekniska komponenter som omfattas av instans nivån, till exempel SQL Agent-jobb.

Du kan också spara pengar genom att migrera dina SQL Server lokala licenser till Azure SQL Database att använda [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) för SQL Server om du väljer den [vCore-baserade inköps modellen](../../database/service-tiers-vcore.md).

Den här guiden syftar till att klargör migrerings alternativ och överväganden när du förbereder för att migrera dina SQL Server-databaser till Azure SQL Database.  

## <a name="considerations"></a>Överväganden 

De viktiga faktorer som du bör tänka på när du utvärderar alternativen för migrering beror på: 

- Antal servrar och databaser
- Storlek på databaser
- Acceptabel drift stopp under migreringsprocessen 

De migrerings alternativ som anges i den här guiden tar hänsyn till dessa faktorer. För att migrera logiska data till Azure SQL Database kan tiden för migrering vara beroende av både antalet objekt i en databas och databasens storlek. 

Olika verktyg är tillgängliga för olika arbets belastningar och användar inställningar. Vissa verktyg kan användas för att utföra en snabb migrering av en enskild databas med hjälp av ett UI-baserat verktyg medan andra verktyg kan migrera flera databaser som kan automatiseras för att hantera migreringar i skala. 

## <a name="choose-appropriate-target"></a>Välj lämpligt mål

Överväg allmänna rikt linjer som hjälper dig att välja rätt distributions modell och tjänst nivå för Azure SQL Database. Du kan välja beräknings-och lagrings resurser under distributionen och sedan ändra dem senare med hjälp av  [Azure Portal](../../database/scale-resources.md)  utan att det uppstår avbrott i programmet.


**Distributions modeller**: förstå programmets arbets belastning och användnings mönstret för att bestämma mellan en enskild databas eller elastisk pool. 

- En [enda databas](../../database/single-database-overview.md) representerar en fullständigt hanterad databas som passar för de flesta moderna moln program och mikrotjänster.
- En [elastisk pool](../../database/elastic-pool-overview.md) är en samling av enskilda databaser med en delad uppsättning resurser, till exempel processor eller minne, och passar för att kombinera databaser i en pool med förutsägbara användnings mönster som effektivt kan dela samma uppsättning resurser.

**Inköps modeller**: Välj mellan inköps modellen vCore, DTU eller Server lös. 

- Med [vCore-modellen](../../database/service-tiers-vcore.md) kan du välja antalet virtuella kärnor för din Azure SQL Database, vilket gör det till det enklaste valet vid översättning från lokala SQL Server. Detta är det enda alternativet som har stöd för att spara på licens kostnaden med [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). 
- [DTU-modellen](../../database/service-tiers-dtu.md) sammanfattar underliggande beräknings-, minnes-och IO-resurser för att tillhandahålla en blandad DTU. 
- Den [serverbaserade modellen](../../database/serverless-tier-overview.md) är avsedd för arbets belastningar som kräver automatisk skalning på begäran med beräknings resurser som faktureras per sekund. Den serverbaserade beräknings nivån pausar automatiskt databaser under inaktiva perioder (där endast lagrings enheter faktureras) och återupptar automatiskt databaser när aktiviteten returnerar. 

**Tjänst nivåer**: Välj mellan tre tjänst nivåer som är avsedda för olika typer av program.

- [Generell användning/standard-tjänstnivå](../../database/service-tier-general-purpose.md) erbjuder ett balanserat budgetorienteradt alternativ med beräkning och lagring som lämpar sig för att leverera program med flera nivåer, med inbyggd redundans på lagrings lagret för att återställa efter fel. Utformad för de flesta databas arbets belastningar. 
- [Affärskritisk/Premium-tjänstnivå](../../database/service-tier-business-critical.md) är för program med hög nivå som kräver höga transaktions hastigheter, i/o för låg latens och en hög återhämtnings nivå med sekundära repliker som är tillgängliga både för redundans och för att avlasta Läs arbets belastningar.
- [Nivån för storskalig service](../../database/service-tier-hyperscale.md) är för databaser som har växande data volymer och som måste skala upp till 100 TB databas storlek automatiskt. Utformad för mycket stora databaser. 

> [!IMPORTANT]
> [Transaktions logg frekvensen regleras](../../database/resource-limits-logical-server.md#transaction-log-rate-governance) i Azure SQL Database för att begränsa hög förbruknings frekvens. Under migreringen kan det vara nödvändigt att skala mål databas resurser (virtuella kärnor/DTU: er) för att under lätta trycket på CPU eller data flöde. Välj lämplig mål databas, men planera att skala resurser upp för migreringen om det behövs. 


### <a name="sql-server-on-azure-vm-alternative"></a>SQL Server på alternativ för virtuell Azure-dator

Ditt företag kan ha krav som gör [SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) ett mer lämpligt mål än Azure SQL Database. 

Om följande gäller för din verksamhet bör du överväga att flytta till en SQL Server på Azure VM i stället: 

- Om du behöver direkt åtkomst till operativ systemet eller fil systemet, till exempel för att installera tredje part eller anpassade agenter på samma virtuella dator med SQL Server. 
- Om du har strikt beroende på funktioner som fortfarande inte stöds, till exempel FileStream/FileTable-, PolyBase-och kors instans transaktioner. 
- Om du absolut behöver stanna med en angiven version av SQL Server (2012, t. ex.). 
- Om beräknings kraven är mycket lägre än de som har hanterade instanser (en vCore, till exempel) och databas konsolidering är inte ett acceptabelt alternativ. 


## <a name="migration-tools"></a>Migreringsverktyg 

De rekommenderade verktygen för migrering är Data Migration Assistant och Azure Database Migration Service. Det finns även andra alternativa alternativ för migrering. 

### <a name="recommended-tools"></a>Rekommenderade verktyg

I följande tabell visas rekommenderade Migreringsverktyg: 

|Teknik | Beskrivning|
|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb)|Data Migration Assistant är ett Skriv bords verktyg som ger sömlös utvärdering av SQL Server och migreringar till Azure SQL Database (både schema och data). Verktyget kan installeras på en server lokalt eller på den lokala datorn som har anslutning till dina käll databaser. Migreringsprocessen är en logisk data förflyttning mellan objekt i käll-och mål databasen. </br> -Migrera enskilda databaser (både schema och data)|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)|En Azure-tjänst från första part som kan migrera dina SQL Server-databaser till Azure SQL Database med hjälp av Azure Portal eller automatiserad med PowerShell. Azure DMS kräver att du väljer en önskad Azure-Virtual Network (VNet) under etableringen för att säkerställa att det finns en anslutning till käll SQL Server databaserna. </br> – Migrera enskilda databaser eller skala. |
| | |


### <a name="alternative-tools"></a>Alternativa verktyg

I följande tabell visas alternativa Migreringsverktyg: 

|Teknik |Beskrivning  |
|---------|---------|
|[Transaktionsreplikering](../../database/replication-to-sql-database.md)|Replikera data från käll SQL Server databas tabeller till SQL Database genom att tillhandahålla ett överordnat alternativ för utgivarens prenumerations typ och samtidigt upprätthålla transaktions konsekvensen. Stegvisa data ändringar sprids till prenumeranter när de inträffar på utgivaren.|
|[Importera export tjänst/BACPAC](../../database/database-import.md)|[BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) är en Windows-fil med ett `.bacpac` tillägg som kapslar in en Databass schema och data. BACPAC kan användas för att exportera data från en käll SQL Server och importera data till Azure SQL Database. BACPAC-filen kan importeras till en ny Azure SQL Database med hjälp av Azure Portal. </br></br> För skalning och prestanda med stora databaser eller stora antal databaser bör du överväga att använda kommando rads verktyget [SqlPackage](../../database/database-import.md#using-sqlpackage) för att exportera och importera databaser.|
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)|[Verktyget för Mass kopierings program (BCP)](/sql/tools/bcp-utility) kopierar data från en instans av SQL Server till en data fil. Använd BCP-verktyget för att exportera data från källan och importera data filen till mål SQL Database. </br></br> För Mass kopierings åtgärder med hög hastighet för att flytta data till Azure SQL Database kan [Smart Mass](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) kopierings verktyget användas för att maximera överföringshastigheten genom att använda parallella kopierings aktiviteter.|
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)|[Kopierings aktiviteten](../../../data-factory/copy-activity-overview.md) i Azure Data Factory migrerar data från käll SQL Server databas (er) till SQL Database att använda inbyggda kopplingar och en [integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF har stöd för en mängd olika [kopplingar](../../../data-factory/connector-overview.md) för att flytta data från SQL Server källor till SQL Database.|
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)|SQL Data Sync är en tjänst som bygger på Azure SQL Database som låter dig synkronisera data som du väljer i båda riktningarna i flera databaser, både lokalt och i molnet.</br>Datasynkronisering är användbart i fall där data måste uppdateras i flera databaser i Azure SQL Database eller SQL Server.|
| | |

## <a name="compare-migration-options"></a>Jämför migrerings alternativ

Jämför migrations alternativen för att välja den sökväg som passar dina affärs behov. 

### <a name="recommended-options"></a>Rekommenderade alternativ

I följande tabell jämförs rekommenderade migrerings alternativ: 

|Migreringsalternativ  |När du ska använda detta  |Överväganden  |
|---------|---------|---------|
|[Data Migration Assistant (DMA)](/sql/dma/dma-migrateonpremsqltosqldb) | – Migrera enskilda databaser (både schema och data).  </br> – Kan hantera nedtid under migreringen av data. </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | – Migrering utför data förflyttning mellan databas objekt (från källa till mål) och rekommenderas därför att köras vid låg belastnings tider. </br> -DMA rapporterar status för migreringen per databas objekt, inklusive antalet flyttade rader.  </br> – Använd Azure Database Migration Service som anges nedan för stora migreringar (antal databaser/databas storlek).|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-azure-sql.md)| – Migrera enskilda databaser eller skala. </br> – Kan hantera stillestånd under migreringsprocessen. </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | – Migreringar i skala kan automatiseras via [PowerShell](../../../dms/howto-sql-server-to-azure-sql-powershell.md). </br> – Tiden för att slutföra migreringen beror på databasens storlek och antalet objekt i databasen. </br> – Kräver att käll databasen anges som skrivskyddad. |
| | | |

### <a name="alternative-options"></a>Alternativa alternativ

I följande tabell jämförs alternativa flyttnings alternativ: 

|Metod/teknik |När du ska använda detta    |Överväganden  |
|---------|---------|---------|
|[Transaktionsreplikering](../../database/replication-to-sql-database.md)| – Migrera genom att kontinuerligt Publicera ändringar från käll databas tabeller till mål SQL Database tabeller. </br> – Fullständig eller partiell databas migrering av valda tabeller (delmängd av databas).  </br> </br> Källor som stöds: </br> - [SQL Server (2016-2019) med vissa begränsningar](/sql/relational-databases/replication/replication-backward-compatibility) </br> – AWS EC2  </br> -GCP Compute SQL Server VM  | – Installationen är relativt komplex jämfört med andra alternativ för migrering.   </br> -Tillhandahåller ett alternativ för kontinuerlig replikering för att migrera data (utan att databaserna tas offline).  </br> -Transaktionell replikering har ett antal begränsningar som du bör tänka på när du konfigurerar utgivaren på käll SQL Server. Se [begränsningar för publicering av objekt](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) för mer information. </br>-Det är möjligt att [övervaka replikeringsåtgärden](/sql/relational-databases/replication/monitor/monitoring-replication).    |
|[Importera export tjänst/BACPAC](../../database/database-import.md)| – Migrera enskilda affärs program databaser. </br>– Lämpligt för mindre databaser.  </br>  -Kräver ingen separat migrerings tjänst eller ett separat verktyg. </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM  |  – Kräver stillestånds tid när data behöver exporteras vid källan och importeras vid målet.   </br> -De fil format och data typer som används i export/import måste vara konsekventa med tabell scheman för att undvika fel vid trunkering/data typs fel.  </br> -Åtgången för att exportera en databas med ett stort antal objekt kan vara betydligt högre.       |
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Migrera fullständiga eller partiella datamigreringar. </br> – Kan hantera stillestånds tid. </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM   | – Kräver stillestånds tid för att exportera data från källa och importera till målet. </br> – Fil formaten och data typerna som används i export/import måste vara konsekventa med tabell scheman. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-database.md)| -Migrera och/eller transformera data från käll SQL Server databaser. </br> -Sammanfoga data från flera data källor till Azure SQL Database vanligt vis för BI-arbetsbelastningar (Business Intelligence).  |  – Kräver att du skapar pipeline för data förflyttning i ADF för att flytta data från källa till mål.   </br> - [Kostnaden](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) är en viktig faktor och baseras på pipelines utlösare, aktivitets körningar, varaktighet för data förflyttning osv. |
|[SQL Data Sync](../../database/sql-data-sync-data-sql-server-sql-database.md)| -Synkronisera data mellan käll-och mål databaserna.</br> – Lämpligt för att köra kontinuerlig synkronisering mellan Azure SQL Database och lokala SQL Server i ett dubbelriktat flöde. | -Azure SQL Database måste vara Hub-databasen för synkronisering med lokal SQL Server-databasen som medlems databas.</br> -Jämfört med Transaktionsreplikering har SQL Data Sync stöd för dubbelriktad datasynkronisering mellan lokalt och Azure SQL Database. </br> -Kan ha en högre prestanda påverkan beroende på arbets belastningen.|
| | | |

## <a name="feature-interoperability"></a>Funktions samverkan 

Det finns ytterligare överväganden när du migrerar arbets belastningar som är beroende av andra SQL Server funktioner.

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services
Migrera SQL Server Integration Services-paket (SSIS) till Azure genom att distribuera om paketen till Azure-SSIS runtime i [Azure Data Factory](../../../data-factory/introduction.md). Azure Data Factory [stöder migrering av SSIS-paket](../../../data-factory/scenario-ssis-migration-overview.md#azure-sql-database-as-database-workload-destination) genom att tillhandahålla en körning som skapats för att köra SSIS-paket i Azure. Du kan också skriva om SSIS ETL-logiken internt i ADF med [data flöden](../../../data-factory/concepts-data-flow-overview.md).


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services
Migrera SQL Server Reporting Services (SSRS) rapporter till sid brytnings rapporter i Power BI. Använd [verktyget RDL-migrering](https://github.com/microsoft/RdlMigration) för att förbereda och migrera dina rapporter. Microsoft har utvecklat det här verktyget som hjälp för kunder som ska migrera RDL-rapporter från sina SSRS-servrar till Power BI. Den är tillgänglig på GitHub och dokumenterar en slut punkt till slut punkt – genom gång av migrerings scenariot. 

#### <a name="high-availability"></a>Hög tillgänglighet
Manuell installation av SQL Server funktioner med hög tillgänglighet som Always on Cluster instances och Always on-tillgänglighetsgrupper blir föråldrade på mål Azure SQL Database eftersom hög tillgänglighets arkitektur redan är inbyggd i både [generell användning (standard tillgänglighets modell](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) ) och [affärskritisk (Premium Availability-modell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) SQL Database. Service nivån Affärskritisk/Premium ger också Läs-och utskalning som gör det möjligt att ansluta till en av de sekundära noderna i skrivskyddat syfte. 

Utöver den hög tillgänglighets arkitektur som ingår i SQL Database finns det också en funktion för [Automatisk redundans](../../database/auto-failover-group-overview.md) som gör att du kan hantera replikering och redundans av databaser i en hanterad instans till en annan region. 

#### <a name="sql-agent-jobs"></a>SQL Agent-jobb
SQL Agent-jobb stöds inte direkt i Azure SQL Database och måste distribueras till [Elastic Database jobb (för hands version)](../../database/job-automation-overview.md#elastic-database-jobs-preview).

#### <a name="logins-and-groups"></a>Inloggningar och grupper
Flytta SQL-inloggningar från käll SQL Server till Azure SQL Database med Database Migration Service (DMS) i offline-läge.  Använd bladet **valda inloggningar** i **migreringsguiden** för att migrera inloggningar till mål SQL Database. 

Windows-användare och-grupper kan också migreras med hjälp av DMS genom att aktivera motsvarande växlings knapp på konfigurations sidan för DMS. 

Du kan också använda PowerShell- [verktyget](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) som är särskilt utformat av Microsoft data migration Architects. Verktyget använder PowerShell för att skapa ett Transact-SQL-skript (T-SQL) för att återskapa inloggningar och välja databas användare från källan till målet. Verktyget mappar automatiskt Windows AD-konton till Azure AD-konton och kan göra en UPN-sökning för varje inloggning mot käll Active Directory. Verktyget skripterar anpassade Server-och databas roller, samt roll medlemskap, databas roll och användar behörigheter. Inneslutna databaser stöds inte ännu och endast en delmängd av möjliga SQL Server behörigheter har skript. 


#### <a name="system-databases"></a>System databaser
För Azure SQL Database är de enda tillämpliga system databaserna [Master](/sql/relational-databases/databases/master-database) och tempdb. Läs mer i [tempdb i Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).

## <a name="leverage-advanced-features"></a>Utnyttja avancerade funktioner 

Se till att dra nytta av de avancerade molnbaserade funktionerna som erbjuds av SQL Database. Du behöver till exempel inte längre oroa dig för att hantera säkerhets kopieringar eftersom tjänsten gör det åt dig. Du kan återställa till vilken [tidpunkt som helst inom bevarande perioden](../../database/recovery-using-backups.md#point-in-time-restore). 

För att förstärka säkerheten bör du överväga att använda [Azure Active Directory autentisering](../../database/authentication-aad-overview.md), [granskning](../../database/auditing-overview.md), [hot identifiering](../../database/azure-defender-for-sql.md), [säkerhet på radnivå](/sql/relational-databases/security/row-level-security)och [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking).

Förutom avancerade hanterings-och säkerhetsfunktioner innehåller SQL Database en uppsättning avancerade verktyg som kan hjälpa dig att [övervaka och finjustera din arbets belastning](../../database/monitor-tune-overview.md). [Azure SQL-analys (för hands version)](../../../azure-monitor/insights/azure-sql.md) är en avancerad lösning för moln övervakning för att övervaka prestanda för alla dina databaser i Azure SQL Database i skala och över flera prenumerationer i en enda vy. Azure SQL-analys samlar in och visualiserar viktiga prestanda mått med inbyggd intelligens för prestanda fel sökning.

[Automatisk justering](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   övervakar kontinuerligt prestanda för SQL execution plan-statistiken och korrigerar automatiskt identifierade prestanda problem. 


## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp finns i följande resurser som har utvecklats för Real World migration-projekt.

|Tillgång  |Beskrivning  |
|---------|---------|
|[Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla en automatiserad och enhetlig mål plattforms besluts process.|
|[DBLoader-verktyg](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader kan användas för att läsa in data från avgränsade textfiler till SQL Server. I det här Windows-konsol verktyget används SQL Server inbyggda klient Bulkload-gränssnittet, som fungerar på alla versioner av SQL Server, inklusive Azure SQL Database.|
|[Skapa en Mass databas med PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Database%20Creation%20with%20PowerShell)|Detta omfattar en uppsättning av tre PowerShell-skript som skapar en resurs grupp (create_rg.ps1), den [logiska servern i Azure](../../database/logical-servers.md) (create_sqlserver.ps1) och Azure SQL Database (create_sqldb.ps1). Skripten innehåller loop-funktioner så att du kan iterera och skapa så många servrar och databaser som behövs.|
|[Mass schema distribution med MSSQL-Scripter & PowerShell](https://github.com/Microsoft/DataMigrationTeam/tree/master/Bulk%20Schema%20Deployment%20with%20MSSQL-Scripter%20&%20PowerShell)|Den här till gången skapar en resurs grupp, en eller flera [logiska servrar i Azure](../../database/logical-servers.md) som värd Azure SQL Database, exporterar varje schema från en lokal SQL Server (eller flera SQL-servrar (2005 +) och importerar den till Azure SQL Database.|
|[Konvertera SQL Server Agent jobb till Elastic Database jobb](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Convert%20SQL%20Server%20Agent%20Jobs%20into%20Elastic%20Database%20Jobs)|Det här skriptet migrerar käll SQL Server Agent jobb till Elastic Database jobb.|
|[Skicka e-postmeddelanden från Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/AF%20SendMail)|Detta ger en lösning som ett alternativ till SendMail-funktioner som är tillgängliga i lokala SQL Server. Lösningen använder Azure Functions och Azure SendGrid-tjänsten för att skicka e-postmeddelanden från Azure SQL Database.|
|[Verktyg för att flytta lokala SQL Server inloggningar till Azure SQL Database](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Ett PowerShell-skript som skapar ett T-SQL-kommandoskript för att återskapa inloggningar och välja databas användare från lokala SQL Server att Azure SQL Database. Verktyget tillåter automatisk mappning av Windows AD-konton till Azure AD-konton samt att även migrera SQL Server interna inloggningar.|
|[Automatisering av data insamling i PerfMon med logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Ett verktyg som samlar in PerMon-data för att förstå bas linje prestanda och hjälp vid migrering av mål rekommendationer. Det här verktyget använder logman.exe för att skapa kommandot som skapar, startar, stoppar och tar bort prestanda räknare som anges på en fjärran sluten SQL Server|
|[Whitepaper – migrering av databasen till Azure SQL DB med BACPAC](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20-%20Benchmarks%20and%20Steps%20to%20Import%20to%20Azure%20SQL%20DB%20Single%20Database%20from%20BACPAC.pdf)|Det här dokumentet innehåller vägledning och steg som hjälper dig att påskynda migreringen från SQL Server till Azure SQL Database att använda BACPAC-filer.|

Dessa resurser har utvecklats som en del av data SQL-Ninja program, som sponsras av Azure Data Group Engineering-teamet. Huvud stadgan för data SQL Ninja-programmet är att avblockera och påskynda komplexa modernisering och konkurrera med data plattformens migrering till Microsofts Azure-dataplattform. Om du tror att organisationen är intresse rad av att delta i data SQL Ninja-programmet, kontaktar du ditt konto team och ber dem att skicka in en nominerad.


## <a name="next-steps"></a>Nästa steg

Information om hur du börjar migrera SQL Server till Azure SQL Database finns i [SQL Server till SQL Database migration guide](sql-server-to-sql-database-guide.md).

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om SQL Database finns i:
   - [Översikt över Azure SQL Database](../../database/sql-database-paas-overview.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).