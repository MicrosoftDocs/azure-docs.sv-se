---
title: 'SQL Server till Azure SQL-hanterad instans: översikt över migrering'
description: Lär dig mer om de olika verktygen och alternativen som är tillgängliga för att migrera dina SQL Server-databaser till Azure SQL-hanterad instans.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 02/18/2020
ms.openlocfilehash: 1c187ae83ce87c9d4d8da4aa1a5dc38163261b52
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024905"
---
# <a name="migration-overview-sql-server-to-azure-sql-managed-instance"></a>Översikt över migrering: SQL Server till Azure SQL-hanterad instans
[!INCLUDE[appliesto--sqlmi](../../includes/appliesto-sqlmi.md)]

Lär dig mer om olika flyttnings alternativ och överväganden för att migrera dina SQL Server till Azure SQL-hanterad instans. 

Du kan migrera SQL Server som körs lokalt eller på: 

- SQL Server på virtuella datorer  
- Amazon Web Services (AWS) EC2 
- Amazon Relations databas tjänst (AWS RDS) 
- Beräknings motor (Google Cloud Platform-GCP)  
- Cloud SQL för SQL Server (Google Cloud Platform – GCP) 

Mer information om andra biflyttnings guider finns i [databas migrering](https://docs.microsoft.com/data-migration). 

## <a name="overview"></a>Översikt

[Azure SQL Managed instance](../../managed-instance/sql-managed-instance-paas-overview.md) är ett rekommenderat mål alternativ för SQL Server arbets belastningar som kräver en fullständigt hanterad tjänst utan att behöva hantera virtuella datorer eller deras operativ system. SQL-hanterad instans gör att du kan lyfta upp och flytta dina lokala program till Azure med minimala program-eller databas ändringar samtidigt som du har en fullständig isolering av dina instanser med inbyggt stöd för virtuella nätverk (VNet). 

## <a name="considerations"></a>Överväganden 

De viktiga faktorer som du bör tänka på när du utvärderar alternativen för migrering beror på: 
- Antal servrar och databaser
- Storlek på databaser
- Acceptabel drift stopp under migreringsprocessen 

En av de främsta fördelarna med att migrera dina SQL-servrar till SQL-hanterad instans är att du kan välja att migrera hela instansen eller bara en delmängd av enskilda databaser. Planera noggrant för att inkludera följande i migreringsprocessen: 
- Alla databaser som måste samplaceras till samma instans 
- Objekt på instans nivå som krävs för ditt program, inklusive inloggningar, autentiseringsuppgifter, SQL Agent-jobb och-operatörer och utlösare på server nivå. 

> [!NOTE]
> Den Azure SQL-hanterade instansen garanterar 99,99% tillgänglighet även i kritiska scenarier, vilket innebär att vissa funktioner i SQL MI inte kan inaktive ras. Mer information finns i [rotor Saks orsaker som kan orsaka olika prestanda på SQL Server och Azure SQL Managed instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) -bloggen. 


## <a name="choose-appropriate-target"></a>Välj lämpligt mål

Några allmänna rikt linjer som hjälper dig att välja rätt tjänst nivå och egenskaper för SQL-hanterad instans som hjälper till att matcha [prestanda bas linjen](sql-server-to-managed-instance-performance-baseline.md): 

- Använd bas linje för processor användning för att etablera en hanterad instans som matchar antalet kärnor som din instans av SQL Server använder. Det kan vara nödvändigt att skala resurser så att de matchar [egenskaperna för maskin varu generering](../../managed-instance/resource-limits.md#hardware-generation-characteristics). 
- Använd bas linje för minnes användning för att välja ett [vCore-alternativ](../../managed-instance/resource-limits.md#service-tier-characteristics) som stämmer med din minnesallokering. 
- Använd bas linjens IO-svars fördröjning för fil under systemet för att välja mellan Generell användning (latens över 5 ms) och Affärskritisk (laten mindre än 3 MS) service nivåer. 
- Använd baseline-genomflödet för att förallokera storleken på data-och loggfilerna för att uppnå förväntad IO-prestanda. 

Du kan välja beräknings-och lagrings resurser under distributionen och sedan [ändra dem när du har använt Azure Portal](../../database/scale-resources.md) utan att det har uppstått avbrott i programmet. 

> [!IMPORTANT]
> Eventuella skillnader i kraven för det [virtuella nätverket för den hanterade instansen](../../managed-instance/connectivity-architecture-overview.md#network-requirements) kan hindra dig från att skapa nya instanser eller använda befintliga. Lär dig mer om att [skapa nya](../../managed-instance/virtual-network-subnet-create-arm-template.md)   och [Konfigurera befintliga](../../managed-instance/vnet-existing-add-subnet.md)   nätverk. 

Ett annat viktigt övervägande vid valet av mål tjänst nivå i Azure SQL-hanterad instans (Generell användning vs Affärskritisk) är tillgängligheten för vissa funktioner som In-Memory OLTP som bara är tillgänglig i Affärskritisk-nivån. 

### <a name="sql-server-vm-alternative"></a>SQL Server VM alternativ

Ditt företag kan ha krav som gör SQL Server på virtuella Azure-datorer till ett mer lämpligt mål än Azure SQL-hanterad instans. 

Om följande gäller för din verksamhet bör du överväga att flytta till en SQL Server VM i stället: 

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
| [Azure Migrate](/azure/migrate/how-to-create-azure-sql-assessment) | Med Azure Migrate för Azure SQL kan du identifiera och utvärdera din SQL data-fastighets i stor skala när du arbetar på VMware, vilket ger rekommendationer för Azure SQL-distribution, mål storlek och månads uppskattningar. | 
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md)  | Azure-tjänsten från första part som stöder migrering i offline-läge för program som kan ge stillestånds tid under migreringsprocessen. Till skillnad från kontinuerlig migrering i onlineläge kör offline mode-migrering en eng ång slöation av en fullständig säkerhets kopia av databasen från källan till målet. | 
|[Inbyggd säkerhets kopiering och återställning](../../managed-instance/restore-sample-database-quickstart.md) | SQL-hanterad instans har stöd för återställning av interna SQL Server databas säkerhets kopior (. bak-filer), vilket gör det till det enklaste alternativet för migrering för kunder som kan ge fullständig databas säkerhets kopiering till Azure Storage. Fullständiga och differentiella säkerhets kopieringar stöds också och dokumenteras i [avsnittet migrerings till gångar](#migration-assets) längre fram i den här artikeln.| 
|[Logg uppspelnings tjänsten (LRS)](../../managed-instance/log-replay-service-migrate.md) | Det här är en moln tjänst som är aktive rad för hanterad instans baserat på SQL Server logg överförings teknik, vilket gör det till ett flyttnings alternativ för kunder som kan tillhandahålla fullständiga, differentiella och logga databas säkerhets kopior till Azure Storage. LRS används för att återställa säkerhetskopierade filer från Azure Blob Storage till SQL-hanterad instans.| 
| | |

### <a name="alternative-tools"></a>Alternativa verktyg

I följande tabell visas alternativa Migreringsverktyg: 

|**Teknik** |**Beskrivning**  |
|---------|---------|
|[Transaktionsreplikering](../../managed-instance/replication-transactional-overview.md) | Replikera data från käll SQL Server databas tabeller till SQL-hanterad instans genom att tillhandahålla ett Överflyttnings alternativ för utgivarens prenumerant och upprätthålla transaktionell konsekvens. | 
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| [Verktyget för Mass kopierings program (BCP)](/sql/tools/bcp-utility) kopierar data från en instans av SQL Server till en data fil. Använd BCP-verktyget för att exportera data från källan och importera data filen till den mål SQL-hanterade instansen.</br></br> För Mass kopierings åtgärder med hög hastighet för att flytta data till Azure SQL Database kan [Smart Mass](/samples/azure-samples/smartbulkcopy/smart-bulk-copy/) kopierings verktyget användas för att maximera överföringshastigheten genom att använda parallella kopierings aktiviteter. | 
|[Guiden Importera export/BACPAC](../../database/database-import.md?tabs=azure-powershell)| [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) är en Windows-fil med ett `.bacpac` tillägg som kapslar in en Databass schema och data. BACPAC kan användas för att exportera data från en käll SQL Server och för att importera filen till en hanterad Azure SQL-instans.  |  
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| [Kopierings aktiviteten](../../../data-factory/copy-activity-overview.md) i Azure Data Factory migrerar data från käll SQL Server databas (er) till SQL-hanterad instans med hjälp av inbyggda anslutningar och en [integration runtime](../../../data-factory/concepts-integration-runtime.md).</br> </br> ADF har stöd för en mängd olika [kopplingar](../../../data-factory/connector-overview.md) för att flytta data från SQL Server källor till SQL-hanterad instans. |
| | |

## <a name="compare-migration-options"></a>Jämför migrerings alternativ

Jämför migrations alternativen för att välja den sökväg som passar dina affärs behov. 

### <a name="recommended-options"></a>Rekommenderade alternativ

I följande tabell jämförs rekommenderade migrerings alternativ: 

|Migreringsalternativ  |När du ska använda detta  |Överväganden  |
|---------|---------|---------|
|[Azure Database Migration Service (DMS)](../../../dms/tutorial-sql-server-to-managed-instance.md) | – Migrera enskilda databaser eller flera databaser i stor skala. </br> – Kan hantera stillestånd under migreringsprocessen. </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM |  – Migreringar i skala kan automatiseras via [PowerShell](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). </br> – Tiden för att slutföra migreringen är beroende av databasens storlek och påverkas av säkerhets kopierings-och återställnings tiden. </br> – Tillräckligt med stillestånd kan krävas. |
|[Inbyggd säkerhets kopiering och återställning](../../managed-instance/restore-sample-database-quickstart.md) | – Migrera enskilda affärs program databaser (n).  </br> – Snabb och enkel migrering utan en separat migrerings tjänst eller verktyg.  </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | -Säkerhets kopiering av databasen använder flera trådar för att optimera data överföring till Azure Blob Storage, men ISV-bandbredd och databas storlek kan påverka överföringshastigheten. </br> – Nedtid bör vara tillräckligt lång tid som krävs för att utföra en fullständig säkerhets kopiering och återställning (vilket är en storlek på data åtgärd).| 
|[Logg uppspelnings tjänsten (LRS)](../../managed-instance/log-replay-service-migrate.md) | – Migrera enskilda affärs program databaser (n).  </br> – Mer kontroll krävs för migrering av databasen.  </br> </br> Källor som stöds: </br> -SQL Server (2008-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM | – Migreringen gör fullständiga databas säkerhets kopieringar på SQL Server och kopierar säkerhetskopieringsfiler till Azure Blob Storage. LRS används för att återställa säkerhetskopierade filer från Azure Blob Storage till SQL-hanterad instans. </br> -Databaser som återställs under migreringsprocessen är i ett återställnings läge och kan inte användas för att läsa eller skriva till dess att processen har slutförts.| 
| | | |

### <a name="alternative-options"></a>Alternativa alternativ

I följande tabell jämförs alternativa flyttnings alternativ: 

|Metod/teknik |När du ska använda detta |Överväganden  |
|---------|---------|---------|
|[Transaktionsreplikering](../../managed-instance/replication-transactional-overview.md) | – Migrera genom att kontinuerligt Publicera ändringar från käll databas tabeller till mål databas tabeller för SQL-hanterad instans. </br> – Fullständig eller partiell databas migrering av valda tabeller (delmängd av databas).  </br> </br> Källor som stöds: </br> -SQL Server (2012-2019) med vissa begränsningar </br> – AWS EC2  </br> -GCP Compute SQL Server VM | </br> – Installationen är relativt komplex jämfört med andra alternativ för migrering.   </br> -Tillhandahåller ett alternativ för kontinuerlig replikering för att migrera data (utan att databaserna tas offline).</br> -Transaktionell replikering har ett antal begränsningar som du bör tänka på när du konfigurerar utgivaren på käll SQL Server. Se [begränsningar för publicering av objekt](/sql/relational-databases/replication/publish/publish-data-and-database-objects#limitations-on-publishing-objects) för mer information.  </br> – Det finns en möjlighet att [övervaka replikeringen](/sql/relational-databases/replication/monitor/monitoring-replication) .    |
|[Masskopiering](/sql/relational-databases/import-export/import-and-export-bulk-data-by-using-the-bcp-utility-sql-server)| – Migrering av fullständiga eller partiella datamigreringar. </br> – Kan hantera stillestånds tid. </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM   | – Kräver stillestånds tid för att exportera data från källa och importera till målet. </br> – Fil formaten och data typerna som används i export/import måste vara konsekventa med tabell scheman. |
|[Guiden Importera export/BACPAC](../../database/database-import.md)| – Migrera enskilda affärs program databaser (n). </br>– Lämpligt för mindre databaser.  </br>  Kräver inte någon separat Migreringsverktyg eller verktyg. </br> </br> Källor som stöds: </br> -SQL Server (2005-2019) lokalt eller virtuell Azure-dator </br> – AWS EC2 </br> – AWS FJÄRR SKRIVBORDS TJÄNSTER </br> -GCP Compute SQL Server VM  |   </br> – Kräver stillestånds tid när data behöver exporteras vid källan och importeras vid målet.   </br> -De fil format och data typer som används i export/import måste vara konsekventa med tabell scheman för att undvika fel vid trunkering/data typs fel. </br> -Åtgången för att exportera en databas med ett stort antal objekt kan vara betydligt högre. |
|[Azure Data Factory (ADF)](../../../data-factory/connector-azure-sql-managed-instance.md)| – Migrera och/eller omvandla data från käll SQL Server databas (er).</br> -Sammanfoga data från flera data källor till en Azure SQL-hanterad instans för arbets belastningar i Business Intelligence (BI).   </br> – Kräver att du skapar pipeline för data förflyttning i ADF för att flytta data från källa till mål.   </br> - [Kostnaden](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) är en viktig faktor och baseras på pipelines utlösare, aktivitets körningar, varaktighet för data förflyttning osv. |
| | | |

## <a name="feature-interoperability"></a>Funktions samverkan 

Det finns ytterligare överväganden när du migrerar arbets belastningar som är beroende av andra SQL Server funktioner. 

#### <a name="sql-server-integration-services"></a>SQL Server Integration Services

Migrera SQL Server Integration Services-paket (SSIS) och projekt i SSISDB till Azure SQL-hanterad instans med [Azure Database migration service (DMS)](../../../dms/how-to-migrate-ssis-packages-managed-instance.md). 

Endast SSIS-paket i SSISDB som börjar med SQL Server 2012 stöds för migrering. Konvertera äldre SSIS-paket innan migreringen. Mer information finns i [självstudien om projekt konvertering](/sql/integration-services/lesson-6-2-converting-the-project-to-the-project-deployment-model) . 


#### <a name="sql-server-reporting-services"></a>SQL Server Reporting Services

Rapporter i SQL Server Reporting Services (SSRS) kan migreras till sid brytnings rapporter i Power BI. Använd [verktyget RDL-migrering](https://github.com/microsoft/RdlMigration) för att förbereda och migrera dina rapporter. Microsoft har utvecklat det här verktyget som hjälp för kunder som ska migrera RDL-rapporter från sina SSRS-servrar till Power BI. Den är tillgänglig på GitHub och dokumenterar en slut punkt till slut punkt – genom gång av migrerings scenariot. 

#### <a name="sql-server-analysis-services"></a>SQL Server Analysis Services

SQL Server Analysis Services tabell modeller från SQL Server 2012 och senare kan migreras till Azure Analysis Services, vilket är en distributions modell för PaaS för Analysis Services tabell modell i Azure. Du kan lära dig mer om att migrera lokal-modeller till Azure Analysis Services i den här [video kursen](https://azure.microsoft.com/resources/videos/azure-analysis-services-moving-models/).

Du kan också överväga att migrera dina lokala Analysis Services tabell modeller till [Power BI Premium att använda nya XMLA-Läs/skriv-slutpunkter](/power-bi/admin/service-premium-connect-tools). 
> [!NOTE]
> Power BI XMLA-funktionen för Läs/skriv slut punkter finns för närvarande i offentlig för hands version och bör inte beaktas för produktions arbets belastningar förrän funktionerna blir allmänt tillgängliga.

#### <a name="high-availability"></a>Hög tillgänglighet

Funktionerna för SQL Server hög tillgänglighet alltid på kluster instanser för redundanskluster och Always on-tillgänglighetsgrupper blir föråldrade på den Azure SQL Managed-instans som hög tillgänglighets arkitektur redan är inbyggd i både [generell användning (standard tillgänglighets modell)](../../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) och [affärskritisk (Premium Availability-modell) SQL-](../../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) hanterad instans. Premium-modellen ger också Läs skalning som gör det möjligt att ansluta till en av de sekundära noderna i skrivskyddat syfte.     

Utöver den hög tillgänglighets arkitektur som ingår i SQL-hanterad instans finns även funktionen för [Automatisk redundans](../../database/auto-failover-group-overview.md) som gör att du kan hantera replikering och redundans för databaser i en hanterad instans till en annan region. 

#### <a name="sql-agent-jobs"></a>SQL Agent-jobb

Använd alternativet offline Azure Database Migration Service (DMS) för att migrera [SQL Agent-jobb](../../../dms/howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md). Annars kan du skripta jobben i Transact-SQL (T-SQL) med SQL Server Management Studio och sedan manuellt återskapa dem på den SQL-hanterade instansen. 

> [!IMPORTANT]
> Azure DMS stöder för närvarande endast jobb med del system i T-SQL. Jobb med stegen i SSIS-paketet måste migreras manuellt. 

#### <a name="logins-and-groups"></a>Inloggningar och grupper

SQL-inloggningar från käll SQL Server kan flyttas till Azure SQL-hanterad instans med Database Migration Service (DMS) i offline-läge.  Använd bladet **[Välj inloggningar](../../../dms/tutorial-sql-server-to-managed-instance.md#select-logins)** i **migreringsguiden** för att migrera inloggningar till din mål SQL-hanterade instans. 

Som standard stöder Azure Database Migration Service bara migrering av SQL-inloggningar. Du kan dock aktivera möjligheten att migrera Windows-inloggningar genom att:

Se till att den SQL-hanterade instansen för SQL har Läs behörighet för Azure AD, som kan konfigureras via Azure Portal av en användare med rollen **Global administratör** .
Konfigurera Azure Database Migration Service-instansen för att aktivera migrering av Windows-användare/grupper, som har kon figurer ATS via Azure Portal, på sidan konfiguration. När du har aktiverat den här inställningen startar du om tjänsten för att ändringarna ska börja gälla.

När tjänsten har startats om visas Windows användare/grupp-inloggningar i listan över inloggningar som är tillgängliga för migrering. För alla Windows-användare/grupp-inloggningar som du migrerar uppmanas du att ange det associerade domän namnet. Tjänst användar konton (konto med domän namns UTFÄRDAre) och virtuella användar konton (konto namn med domän namns tjänsten) stöds inte.

Mer information finns i [så här migrerar du Windows-användare och grupper i en SQL Server instans till en Azure SQL-hanterad instans med hjälp av T-SQL](../../managed-instance/migrate-sql-server-users-to-instance-transact-sql-tsql-tutorial.md).

Du kan också använda PowerShell- [verktyget](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) som är särskilt utformat av Microsoft data migration Architects. Verktyget använder PowerShell för att skapa ett T-SQL-skript för att återskapa inloggningar och välja databas användare från källan till målet. Verktyget mappar automatiskt Windows AD-konton till Azure AD-konton och kan göra en UPN-sökning för varje inloggning mot käll Active Directory. Verktyget skripterar anpassade Server-och databas roller, samt roll medlemskap, databas roll och användar behörigheter. Inneslutna databaser stöds inte för närvarande och endast en delmängd av möjliga SQL Server behörigheter har skript. 

#### <a name="encryption"></a>Kryptering

När du migrerar databaser som skyddas av [Transparent datakryptering](../../database/transparent-data-encryption-tde-overview.md)   till en hanterad instans med hjälp av intern återställnings alternativ [migrerar du motsvarande certifikat](../../managed-instance/tde-certificate-migrate.md) från käll  SQL Server till den SQL-hanterade instans som ska hanteras av databasen. 

#### <a name="system-databases"></a>System databaser

Det finns inte stöd för återställning av system databaser. Om du vill migrera objekt på instans nivå (lagrade i Master-eller MSDB-databaser), skripterar du dem med hjälp av Transact-SQL (T-SQL) och återskapar dem sedan på den hanterade mål instansen. 

#### <a name="in-memory-oltp-memory-optimized-tables"></a>In-Memory OLTP (Minnesoptimerade tabeller)

SQL Server ger In-Memory OLTP-kapacitet som gör att du kan använda minnesoptimerade tabeller, minnesoptimerade tabell typer och internt kompilerade SQL-moduler för att köra arbets belastningar som har hög genom strömning och transaktions bearbetnings krav med låg latens. 

> [!IMPORTANT]
> In-Memory OLTP stöds endast på Affärskritisk nivå i Azure SQL-hanterad instans (och stöds inte i Generell användning-nivån).

Om du har minnesoptimerade tabeller eller minnesoptimerade tabell typer i din lokala SQL Server och du vill migrera till en Azure SQL-hanterad instans, bör du antingen:

- Välj Affärskritisk nivå för din mål Azure SQL-hanterade instans som stöder In-Memory OLTP eller
- Om du vill migrera till Generell användning nivå i Azure SQL Managed instance tar du bort minnesoptimerade tabeller, minnesoptimerade tabell typer och internt kompilerade SQL-moduler som interagerar med minnesoptimerade objekt innan du migrerar dina databaser. Följande T-SQL-fråga kan användas för att identifiera alla objekt som måste tas bort innan migrering till Generell användning nivå:

```tsql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

Mer information om minnes intern teknik finns i [optimera prestanda med hjälp av minnes intern teknik i Azure SQL Database och Azure SQL-hanterad instans](https://docs.microsoft.com/azure/azure-sql/in-memory-oltp-overview)

## <a name="leverage-advanced-features"></a>Utnyttja avancerade funktioner 

Se till att dra nytta av de avancerade molnbaserade funktioner som erbjuds av SQL-hanterad instans. Du behöver till exempel inte längre oroa dig för att hantera säkerhets kopieringar eftersom tjänsten gör det åt dig. Du kan återställa till vilken [tidpunkt som helst inom bevarande perioden](../../database/recovery-using-backups.md#point-in-time-restore). Dessutom behöver du inte bekymra dig om att konfigurera hög tillgänglighet eftersom [hög tillgänglighet är inbyggt](../../database/high-availability-sla.md). 

För att förstärka säkerheten bör du överväga att använda [Azure Active Directory autentisering](../../database/authentication-aad-overview.md), [granskning](../../managed-instance/auditing-configure.md), [hot identifiering](../../database/azure-defender-for-sql.md), [säkerhet på radnivå](/sql/relational-databases/security/row-level-security)och [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking).

Förutom avancerade hanterings-och säkerhetsfunktioner innehåller SQL-hanterad instans en uppsättning avancerade verktyg som kan hjälpa dig att [övervaka och finjustera din arbets belastning](../../database/monitor-tune-overview.md). Med [Azure SQL-analys](../../../azure-monitor/insights/azure-sql.md) kan du övervaka en stor uppsättning hanterade instanser på ett centraliserat sätt.  [Automatisk justering](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction)   i hanterade instanser övervakar kontinuerligt prestandan för din SQL Plans körnings statistik och korrigerar automatiskt de identifierade prestanda problemen. 

Vissa funktioner är bara tillgängliga när kompatibilitetsnivån för [databas](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) har ändrats till den senaste kompatibilitetsnivån (150). 

## <a name="migration-assets"></a>Migrera till gångar 

Mer hjälp finns i följande resurser som har utvecklats för Real World migration-projekt.

|Tillgång  |Beskrivning  |
|---------|---------|
|[Modell och verktyg för data arbets belastnings bedömning](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Det här verktyget ger föreslagna "bästa anpassning"-språkplattformar, moln beredskap och program/databas reparations nivåer för en specifik arbets belastning. Den erbjuder enkel, enkel beräkning och rapportgenerering som hjälper till att påskynda stora fastighets bedömningar genom att tillhandahålla och automatisera och enhetlig mål plattforms besluts process.|
|[DBLoader-verktyg](https://github.com/microsoft/DataMigrationTeam/tree/master/DBLoader%20Utility)|DBLoader kan användas för att läsa in data från avgränsade textfiler till SQL Server. I det här Windows-konsol verktyget används SQL Server inbyggda klient Bulkload-gränssnittet, som fungerar på alla versioner av SQL Server, inklusive Azure SQL MI.|
|[Verktyg för att flytta lokala SQL Server inloggningar till en Azure SQL-hanterad instans](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins)|Ett PowerShell-skript som skapar ett T-SQL-kommandoskript för att återskapa inloggningar och välja databas användare från lokala SQL Server till Azure SQL-hanterad instans. Verktyget tillåter automatisk mappning av Windows AD-konton till Azure AD-konton samt att även migrera SQL Server interna inloggningar.|
|[Automatisering av data insamling i perfmon med logman](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Perfmon%20Data%20Collection%20Automation%20Using%20Logman)|Ett verktyg som samlar in data för att förstå de grundläggande prestanda som bidrar till rekommendationen för migrering. Det här verktyget använder logman.exe för att skapa kommandot som skapar, startar, stoppar och tar bort prestanda räknare som anges på en fjärran sluten SQL Server.|
|[Whitepaper – migrering av databasen till Azure SQL-hanterad instans genom återställning av fullständiga och differentiella säkerhets kopior](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Database%20migrations%20to%20Azure%20SQL%20DB%20Managed%20Instance%20-%20%20Restore%20with%20Full%20and%20Differential%20backups.pdf)|Det här dokumentet innehåller vägledning och steg som hjälper dig att påskynda migreringen från SQL Server till en hanterad Azure SQL-instans om du bara har fullständiga och differentiella säkerhets kopieringar (och ingen logg säkerhets kopierings funktion).|

Dessa resurser har utvecklats som en del av data SQL-Ninja program, som sponsras av Azure Data Group Engineering-teamet. Huvud stadgan för data SQL Ninja-programmet är att avblockera och påskynda komplexa modernisering och konkurrera med data plattformens migrering till Microsofts Azure-dataplattform. Om du tror att organisationen är intresse rad av att delta i data SQL Ninja-programmet, kontaktar du ditt konto team och ber dem att skicka in en nominerad.


## <a name="next-steps"></a>Nästa steg

Information om hur du börjar migrera din SQL Server till Azure SQL-hanterad instans finns i [migreringen SQL Server till Azure SQL-hanterad instans](sql-server-to-managed-instance-guide.md).

- För en matris med tjänster och verktyg från Microsoft och tredje part som är tillgängliga för att hjälpa dig med olika scenarier för databas-och data migrering samt särskilda uppgifter, se [tjänst och verktyg för datamigrering](../../../dms/dms-tools-matrix.md).

- Mer information om Azure SQL-hanterad instans finns i:
   - [Tjänst nivåer i Azure SQL-hanterad instans](../../managed-instance/sql-managed-instance-paas-overview.md#service-tiers)
   - [Skillnader mellan SQL Server och Azure SQL-hanterad instans](../../managed-instance/transact-sql-tsql-differences-sql-server.md)
   - [Kostnad för total ägande kostnad för Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Mer information om ramverket och implementerings cykeln för molnbaserad migrering finns i
   -  [Cloud Adoption Framework för Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Metod tips för kostnads-och storleks arbets belastningar migreras till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 


- Information om hur du bedömer program åtkomst lagret finns i [Data Access Migration Toolkit (för hands version)](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)
- Mer information om hur du utför data åtkomst Layer A/B-testning finns [Database experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
