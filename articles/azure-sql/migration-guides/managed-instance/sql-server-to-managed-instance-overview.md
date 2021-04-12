---
title: 'SQL Server till SQL-hanterad instans: översikt över migrering'
description: Lär dig mer om de verktyg och alternativ som finns tillgängliga för att migrera dina SQL Server-databaser till Azure SQL-hanterad instans.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 0a3fd1b492d19e241d89cc5477891c7c836e4640
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078986"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Översikt över migrering: SQL Server till Azure SQL-hanterad instans
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Lär dig mer om alternativen och överväganden för att migrera SQL Server-databaser till Azure SQL-hanterad instans. 

Du kan migrera SQL Server databaser som körs lokalt eller på: 

- SQL Server på Azure Virtual Machines.  
- Amazon Web Services (AWS) Elastic Compute Cloud (EC2). 
- AWS för Relations databas tjänsten (RDS). 
- Compute-motor i Google Cloud Platform (GCP).  
- Cloud SQL för SQL Server i GCP. 

Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Översikt

[Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md) är ett rekommenderat mål alternativ för SQL Server arbets belastningar som kräver en fullständigt hanterad tjänst utan att behöva hantera virtuella datorer eller deras operativ system. SQL-hanterad instans gör att du kan flytta dina lokala program till Azure med minimala program-eller databas ändringar. Den erbjuder fullständig isolering av dina instanser med inbyggt stöd för virtuella nätverk. 

## <a name="considerations"></a>Överväganden 

Viktiga faktorer att tänka på när du utvärderar migreringsåtgärder är: 
- Antal servrar och databaser
- Storlek på databaser
- Acceptabel drift stopp under migreringsprocessen 

En av de främsta fördelarna med att migrera SQL Server-databaser till SQL-hanterad instans är att du kan välja att migrera hela instansen eller bara en delmängd av enskilda databaser. Planera noggrant för att inkludera följande i migreringsprocessen: 
- Alla databaser som måste samplaceras till samma instans 
- Objekt på instans nivå som krävs för ditt program, inklusive inloggningar, autentiseringsuppgifter, SQL Agent-jobb och-operatörer och utlösare på server nivå 

> [!NOTE]
> Azure SQL Managed instance garanterar 99,99 procents tillgänglighet, även i kritiska scenarier. Det går inte att inaktivera kostnader som orsakas av vissa funktioner i SQL-hanterad instans. Mer information finns i [nyckel orsaker till prestanda skillnader mellan SQL-hanterad instans och SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) blogg inlägg. 


## <a name="choose-an-appropriate-target"></a>Välj ett lämpligt mål

Följande allmänna rikt linjer kan hjälpa dig att välja rätt tjänste nivå och egenskaper för SQL-hanterad instans som hjälper till att matcha [prestanda bas linjen](sql-server-to-managed-instance-performance-baseline.md): 

- Använd bas linje för processor användning för att etablera en hanterad instans som matchar antalet kärnor som din instans av SQL Server använder. Det kan vara nödvändigt att skala resurser så att de matchar [egenskaperna för maskin varu generering](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Använd bas linje för minnes användning för att välja ett [vCore-alternativ](../../managed-instance/resource-limits.md#service-tier-characteristics) som stämmer med din minnesallokering. 
- Använd bas linjen i/O-fördröjningen för fil under systemet för att välja mellan Generell användning (svars tid på över 5 ms) och Affärskritisk (laten mindre än 3 MS) service nivåer. 
- Använd baseline-genomflödet för att förallokera storleken på data-och loggfilerna för att uppnå förväntade I/O-prestanda. 

Du kan välja beräknings-och lagrings resurser under distributionen och sedan [ändra dem efteråt med hjälp av Azure Portal](../../database/scale-resources.md), utan att det uppstår avbrott för ditt program. 

> [!IMPORTANT]
> En avvikelse i de [virtuella nätverks kraven för hanterade instanser](../../managed-instance/connectivity-architecture-overview.md#network-requirements) kan förhindra att du skapar nya instanser eller använder befintliga. Lär dig mer om att [skapa nya](../../managed-instance/virtual-network-subnet-create-arm-template.md)   och [Konfigurera befintliga](../../managed-instance/vnet-existing-add-subnet.md)   nätverk. 

Ett annat övervägande vid valet av mål tjänst nivå i Azure SQL-hanterad instans (Generell användning jämfört med Affärskritisk) är tillgängligheten för vissa funktioner, t. ex In-Memory OLTP, som endast är tillgängliga i Affärskritisk nivån. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM alternativ

Ditt företag kan ha krav som gör [SQL Server på Azure Virtual Machines](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) ett mer lämpligt mål än en hanterad Azure SQL-instans. 

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
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Den här Azure-tjänsten stöder migrering i offline-läge för program som kan ge drift stopp under migreringsprocessen. Till skillnad från kontinuerlig migrering i onlineläge kör offline mode-migrering en eng ång slöation av en fullständig säkerhets kopia av databasen från källan till målet. | 
|[Inbyggd säkerhets kopiering och återställning](../../managed-instance/restore-sample-database-quickstart.md) | SQL-hanterad instans har stöd för återställning av interna SQL Server säkerhets kopior av databasen (. bak-filer). Det är det enklaste flyttnings alternativet för kunder som kan tillhandahålla fullständiga databas säkerhets kopieringar för att Azure Storage. Fullständiga och differentiella säkerhets kopieringar stöds också och dokumenteras i [avsnittet om migrerings till gångar](#migration-assets) senare i den här artikeln.| 
|[Logga repetitions tjänsten](../../managed-instance/log-replay-service-migrate.md) | Den här moln tjänsten är aktive rad för SQL-hanterad instans baserat på SQL Server logg leverans teknik. Det är ett flyttnings alternativ för kunder som kan tillhandahålla fullständiga, differentiella och loggade databas säkerhets kopior för att Azure Storage. Logg återuppspelnings tjänsten används för att återställa säkerhetskopierade filer från Azure Blob Storage till SQL-hanterad instans.| 
| | |

I följande tabell visas alternativa Migreringsverktyg: 

|**Teknik** |**Beskrivning**  |
|---------|---------|
|[Transaktionsreplikering](../../managed-instance/replication-transactional-overview.md) | Replikera data från käll SQL Server databas tabeller till SQL-hanterad instans genom att tillhandahålla ett Överflyttnings alternativ för utgivarens prenumerant och upprätthålla transaktions konsekvens. | 
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Verktyget för Mass kopierings program (BCP)](/sql/tools/bcp-utility) kopierar data från en instans av SQL Server till en data fil. Använd verktyget för att exportera data från källan och importera data filen till den mål SQL-hanterade instansen. </br></br> För Mass kopierings åtgärder med hög hastighet för att flytta data till en Azure SQL-hanterad instans kan du använda [verktyget för smart Mass kopiering](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) för att maximera överföringshastigheten genom att dra nytta av parallell kopierings aktiviteter. | 
|[Guiden Importera export/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) är en Windows-fil med tillägget. bacpac som kapslar in en Databass schema och data. Du kan använda BACPAC för att exportera data från en SQL Server källa och importera data tillbaka till en hanterad Azure SQL-instans. |  
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)|  [Kopierings aktiviteten](../../../data-factory/copy-activity-overview.md) i Azure Data Factory migrerar data från käll SQL Server databaser till SQL-hanterad instans med hjälp av inbyggda anslutningar och en [integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> Data Factory stöder en mängd olika [kopplingar](../../../data-factory/connector-overview.md) för att flytta data från SQL Server källor till SQL-hanterad instans. |

## <a name="compare-migration-options"></a>Jämför migrerings alternativ

Jämför migrations alternativen för att välja den sökväg som passar dina affärs behov. 

I följande tabell jämförs de migreringsåtgärder som vi rekommenderar: 

|Migreringsalternativ  |När du ska använda detta  |Överväganden  |
|---------|---------|---------|
|[Azure Database Migration Service](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Migrera enskilda databaser eller flera databaser i stor skala. </br> – Kan hantera nedtid under migreringsprocessen. </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM |  – Migreringar i skala kan automatiseras via [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> – Tiden för att slutföra migreringen beror på databasens storlek och påverkas av säkerhets kopierings-och återställnings tiden. </br> – Tillräckligt med stillestånd kan krävas. |
|[Inbyggd säkerhets kopiering och återställning](../../managed-instance/restore-sample-database-quickstart.md) | – Migrera enskilda affärs program databaser.  </br> – Snabb och enkel migrering utan en separat migrerings tjänst eller verktyg.  </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | -Säkerhets kopiering av databasen använder flera trådar för att optimera data överföring till Azure Blob Storage, men partner bandbredd och databas storlek kan påverka överföringshastigheten. </br> – Nedtid bör vara tillräckligt lång tid som krävs för att utföra en fullständig säkerhets kopiering och återställning (vilket är en storlek på data åtgärd).| 
|[Logga repetitions tjänsten](../../managed-instance/log-replay-service-migrate.md) | – Migrera enskilda affärs program databaser.  </br> – Mer kontroll krävs för migrering av databasen.  </br> </br> Källor som stöds: </br> -SQL Server (2008 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | – Migreringen gör fullständiga databas säkerhets kopieringar på SQL Server och kopierar säkerhetskopieringsfiler till Azure Blob Storage. Logg återuppspelnings tjänsten används för att återställa säkerhetskopierade filer från Azure Blob Storage till SQL-hanterad instans. </br> -Databaser som återställs under migreringsprocessen är i ett återställnings läge och kan inte användas för att läsa eller skriva tills processen har avslut ATS.| 
| | | |

I följande tabell jämförs alternativa flyttnings alternativ: 

|Metod eller teknik |När du ska använda detta |Överväganden  |
|---------|---------|---------|
|[Transaktionsreplikering](../../managed-instance/replication-transactional-overview.md) | – Migrera genom att kontinuerligt Publicera ändringar från käll databas tabeller till mål databas tabeller för SQL-hanterad instans. </br> – Utför fullständig eller partiell databas migrering av valda tabeller (delmängd av en databas).  </br> </br> Källor som stöds: </br> – SQL Server (2012 till 2019) med vissa begränsningar </br> – AWS EC2  </br> -GCP Compute SQL Server VM | </br> – Installationen är relativt komplex jämfört med andra alternativ för migrering.   </br> -Tillhandahåller ett alternativ för kontinuerlig replikering för att migrera data (utan att databaserna tas offline).</br> -Transaktionsreplikering har begränsningar att tänka på när du konfigurerar utgivaren på käll SQL Servers instansen. Se [begränsningar för publicering av objekt](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) för mer information.  </br> – Det finns en möjlighet att [övervaka replikeringen](/sql/relational-databases/replication/monitor/monitoring-replication) .    |
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| -Gör fullständiga eller delvis datamigreringar. </br> – Kan hantera stillestånds tid. </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM   | – Kräver stillestånds tid för att exportera data från källan och importera till målet. </br> – Fil formaten och data typerna som används i exporten eller importen måste vara konsekventa med tabell scheman. |
|[Guiden Importera export/BACPAC](../../database/database-import.md)| – Migrera enskilda affärs program databaser. </br>– Lämpligt för mindre databaser.  </br>  Kräver inte någon separat Migreringsverktyg eller verktyg. </br> </br> Källor som stöds: </br> -SQL Server (2005 till 2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM  |   </br> -Kräver avbrott eftersom data måste exporteras vid källan och importeras vid målet.   </br> – Fil formaten och data typerna som används i exporten eller importen måste vara konsekventa med tabell scheman för att undvika trunkering eller data typs fel. </br> -Åtgången för att exportera en databas med ett stort antal objekt kan vara betydligt högre. |
|[Azure Data Factory](../../../data-factory/connector-azure-sql-managed-instance.md)| -Migrera och/eller transformera data från käll SQL Server databaser.</br> -Sammanfoga data från flera data källor till Azure SQL Managed instance är vanligt vis för Business Intelligence (BI) arbets belastningar.   </br> -Kräver att pipeliner skapas i Data Factory för att flytta data från källa till mål.   </br> - [Kostnaden](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) är en viktig faktor och baseras på faktorer som pipeline-utlösare, aktivitets körningar och varaktighet för data förflyttning. |
| | | |

## <a name="feature-interoperability"></a>Funktions samverkan 

Det finns flera saker att tänka på när du migrerar arbets belastningar som är beroende av andra SQL Server funktioner. 

### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migrera SQL Server Integration Services-paket (SSIS) och projekt i SSISDB till Azure SQL-hanterad instans med hjälp av [Azure Database migration service](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Endast SSIS-paket i SSISDB som börjar med SQL Server 2012 stöds för migrering. Konvertera äldre SSIS-paket innan migreringen. Mer information finns i [självstudien om projekt konvertering](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Du kan migrera SQL Server Reporting Services-rapporter (SSRS) till sid brytnings rapporter i Power BI. Använd [verktyget RDL-migrering](https://github.com/microsoft/RdlMigration) för att förbereda och migrera dina rapporter. Microsoft har utvecklat det här verktyget för att hjälpa kunder att migrera Report Definition Language-rapporter (RDL) från sina SSRS-servrar till Power BI. Du hittar det på GitHub tillsammans med dokumentation för ett komplett migreringsscenario. 

### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services tabell modeller från SQL Server 2012 och senare kan migreras till Azure Analysis Services, som är en PaaS-distributions modell (Platform as a Service) för Analysis Services tabell modell i Azure. Du kan lära dig mer om att migrera lokala modeller till Azure Analysis Services i [den här video kursen](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Du kan också överväga att migrera dina lokala Analysis Services tabell modeller till [Power BI Premium genom att använda nya XMLA-Läs/skriv-slutpunkter](/power-bi/admin/service-premium-connect-tools). 

### <a name="high-availability"></a>Hög tillgänglighet

SQL Server hög tillgänglighets funktioner alltid på kluster instanser för växling vid fel och alltid på tillgänglighets grupper blir föråldrade på den SQL-hanterade instansen. Hög tillgänglighets arkitektur är redan inbyggd i både [generell användning (standard tillgänglighets modell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) och [affärskritisk (Premium-tillgänglighets modell)](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) för SQL-hanterad instans. Premium-modellen ger också Läs skalning som gör det möjligt att ansluta till en av de sekundära noderna i skrivskyddat syfte.     

Utöver den hög tillgänglighets arkitektur som ingår i SQL-hanterad instans kan du med funktionen för [Automatisk redundans](../../database/auto-failover-group-overview.md) hantera replikeringen och redundansväxlingen av databaser i en hanterad instans till en annan region. 

### <a name="sql-agent-jobs"></a>SQL Agent-jobb

Använd alternativet offline Azure Database Migration Service för att migrera [SQL Agent-jobb](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). Annars kan du skripta jobben i Transact-SQL (T-SQL) med hjälp av SQL Server Management Studio och sedan manuellt återskapa dem på den SQL-hanterade instansen. 

> [!IMPORTANT]
> För närvarande stöder Azure Database Migration Service endast jobb med del system i T-SQL. Jobb med steg för SSIS-paket måste migreras manuellt. 

### <a name="logins-and-groups"></a>Inloggningar och grupper

Flytta SQL-inloggningar från SQL Server källa till Azure SQL-hanterad instans med Database Migration Service i offline-läge.  Använd fönstret [Välj inloggningar](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins) i Migreringsguiden för att migrera inloggningar till din mål SQL-hanterade instans. 

Azure Database Migration Service stöder som standard bara migrering av SQL-inloggningar. Du kan dock aktivera migrering av Windows-inloggningar genom att:

- Se till att den SQL-hanterade instans som har Azure Active Directory (Azure AD) har Läs behörighet. En användare som har rollen global administratör kan konfigurera den åtkomsten via Azure Portal.
- Konfigurera Azure Database Migration Service för att aktivera migrering av Windows-användare eller grupper. Du ställer in detta via Azure Portal på sidan **konfiguration** . När du har aktiverat den här inställningen startar du om tjänsten för att ändringarna ska börja gälla.

När du har startat om tjänsten visas Windows användare eller grupp inloggningar i listan över inloggningar som är tillgängliga för migrering. För alla Windows-användare eller grupp inloggningar som du migrerar uppmanas du att ange det associerade domän namnet. Användar konton för tjänsten (konton med domän namns UTFÄRDAre) och virtuella användar konton (konton med domän namns tjänsten) stöds inte. Mer information finns i [så här migrerar du Windows-användare och grupper i en SQL Server instans till en Azure SQL-hanterad instans med hjälp av T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Alternativt kan du använda PowerShell- [verktyget](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) speciellt utformat av Microsoft data migration Architects. Verktyget använder PowerShell för att skapa ett T-SQL-skript för att återskapa inloggningar och välja databas användare från källan till målet. 

PowerShell-verktyget mappar automatiskt Windows Server Active Directory-konton till Azure AD-konton, och det kan göra en UPN-sökning för varje inloggning mot käll Active Directorys instansen. Verktyget skripterar anpassade Server-och databas roller, tillsammans med roll medlemskap och användar behörigheter. Inneslutna databaser stöds inte ännu och endast en delmängd av möjliga SQL Server behörigheter har skript. 

### <a name="encryption"></a>Kryptering

När du migrerar databaser som skyddas av  [Transparent datakryptering](../../database/transparent-data-encryption-tde-overview.md)   till en hanterad instans med hjälp av alternativet för intern återställning, [migrerar du motsvarande certifikat](../../managed-instance/tde-certificate-migrate.md) från käll SQL Servers INstansen till den SQL-hanterade instansen *innan* databasen återställs. 

### <a name="system-databases"></a>System databaser

Det finns inte stöd för återställning av system databaser. Om du vill migrera objekt på instans nivå (lagrade i Master-och msdb-databaserna), skripterar du dem med hjälp av T-SQL och återskapar dem sedan på den hanterade mål instansen. 

### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (minnesoptimerade tabeller)

SQL Server ger en In-Memory OLTP-funktion. Den tillåter användning av minnesoptimerade tabeller, minnesoptimerade tabell typer och internt kompilerade SQL-moduler för att köra arbets belastningar med höga data flöden och låg latens för transaktions bearbetning. 

> [!IMPORTANT]
> In-Memory OLTP stöds bara i Affärskritisk nivån i Azure SQL-hanterad instans. Det stöds inte i Generell användning nivån.

Om du har minnesoptimerade tabeller eller minnesoptimerade tabell typer i den lokala SQL Server-instansen och du vill migrera till en Azure SQL-hanterad instans bör du antingen:

- Välj Affärskritisk nivå för din mål-SQL-hanterade instans som stöder In-Memory OLTP.
- Om du vill migrera till Generell användning nivån i den hanterade Azure SQL-instansen tar du bort minnesoptimerade tabeller, minnesoptimerade tabell typer och internt kompilerade SQL-moduler som interagerar med minnesoptimerade objekt innan du migrerar dina databaser. Du kan använda följande T-SQL-fråga för att identifiera alla objekt som måste tas bort innan migreringen till Generell användnings nivån:

   ```tsql
   SELECT * FROM sys.tables WHERE is_memory_optimized=1
   SELECT * FROM sys.table_types WHERE is_memory_optimized=1
   SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
   ```

Mer information om minnes intern teknik finns i [optimera prestanda med hjälp av minnes intern teknik i Azure SQL Database och Azure SQL-hanterad instans](../../in-memory-oltp-overview.md).

## <a name="advanced-features"></a>Avancerade funktioner 

Se till att dra nytta av de avancerade molnbaserade funktionerna i SQL-hanterad instans. Du behöver till exempel inte bekymra dig om att hantera säkerhets kopieringar eftersom tjänsten gör det åt dig. Du kan återställa till vilken [tidpunkt som helst inom bevarande perioden](../../database/recovery-using-backups.md#point-in-time-restore). Dessutom behöver du inte bekymra dig om att konfigurera hög tillgänglighet eftersom [hög tillgänglighet är inbyggt](../../database/high-availability-sla.md). 

För att förstärka säkerheten bör du överväga att använda [Azure AD-autentisering](../../database/authentication-aad-overview.md), [granskning](../../managed-instance/auditing-configure.md), [hot identifiering](../../database/azure-defender-for-sql.md), [säkerhet på radnivå](/sql/relational-databases/security/row-level-security)och [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking).

Förutom avancerade hanterings-och säkerhetsfunktioner innehåller SQL-hanterad instans avancerade verktyg som kan hjälpa dig att [övervaka och finjustera din arbets belastning](../../database/monitor-tune-overview.md). Med [Azure SQL-analys](../../../azure-monitor/insights/azure-sql.md) kan du övervaka en stor uppsättning hanterade instanser på ett centraliserat sätt.  [Automatisk justering](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   i hanterade instanser övervakar kontinuerligt prestanda för SQL plan-körningen och åtgärdar automatiskt de identifierade prestanda problemen. 

Vissa funktioner är bara tillgängliga när kompatibilitetsnivån för [databas](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) har ändrats till den senaste kompatibilitetsnivån (150). 

## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp finns i följande resurser som har utvecklats för verkliga migreringsjobb.

|Tillgång  |Beskrivning  |
|---------|---------|
|[Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Det här verktyget ger föreslagna "bästa anpassning"-mål plattform, moln beredskap och en reparations nivå för program/databaser för en arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla en automatiserad och enhetlig besluts process för mål plattformarna.|
|[DBLoader-verktyg](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|Du kan använda DBLoader för att läsa in data från avgränsade textfiler till SQL Server. Det här Windows konsol verktyget använder gränssnittet SQL Server Native Client bulk-load. Gränssnittet fungerar på alla versioner av SQL Server, tillsammans med en hanterad Azure SQL-instans.|
|[Verktyg för att flytta lokala SQL Server inloggningar till en Azure SQL-hanterad instans](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Ett PowerShell-skript kan skapa ett T-SQL-kommandoskript för att återskapa inloggningar och välja databas användare från lokala SQL Server till Azure SQL-hanterad instans. Verktyget tillåter automatisk mappning av Windows Server Active Directory-konton till Azure AD-konton, tillsammans med alternativ för att migrera SQL Server interna inloggningar.|
|[Automation-insamling av data insamling med hjälp av logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Du kan använda logman-verktyget för att samla in perfmon-data (för att hjälpa dig att förstå bas linje prestanda) och få rekommendationer om migrerings mål. Det här verktyget använder logman.exe för att skapa kommandot som skapar, startar, stoppar och tar bort prestanda räknare som anges på en fjärran sluten SQL Server instans.|
|[Migrering av databasen till Azure SQL-hanterad instans genom återställning av fullständiga och differentiella säkerhets kopior](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Den här white paper ger vägledning och steg som hjälper dig att påskynda migreringen från SQL Server till Azure SQL-hanterad instans om du bara har fullständiga och differentiella säkerhets kopieringar (och ingen logg säkerhets kopierings funktion).|

Data SQL Engineering-teamet utvecklade dessa resurser. Det här teamets kärn stadgan är att avblockera och påskynda komplexa modernisering för migrering av data plattformar till Microsofts Azure-dataplattform.


## <a name="next-steps"></a>Nästa steg

- Information om hur du börjar migrera dina SQL Server-databaser till Azure SQL-hanterad instans finns i [migreringen SQL Server till Azure SQL-hanterad instans](sql-server-to-managed-instance-guide.md).

- En matris med tjänster och verktyg som kan hjälpa dig med scenarier för databas-och data migrering samt särskilda uppgifter finns i [tjänster och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL-hanterad instans finns i:
   - [Tjänst nivåer i Azure SQL-hanterad instans](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Skillnader mellan SQL Server och Azure SQL-hanterad instans](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 

- Mer information om ramverket och implementerings cykeln för migrering av moln finns i:
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metodtips för kostnad och storleksändring av arbetsbelastningar som migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).

- Mer information om hur du utför en/B-testning på data åtkomst lagret finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
