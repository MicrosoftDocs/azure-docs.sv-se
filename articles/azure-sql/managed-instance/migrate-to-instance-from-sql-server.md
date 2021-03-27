---
title: Migrera från SQL Server till Azure SQL-hanterad instans
description: Lär dig hur du migrerar en databas från SQL Server till en Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: ccc6acfd27a1430a4f6a31886c06322c5c09e224
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628381"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>SQL Server instans migrering till Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

I den här artikeln får du lära dig mer om metoderna för att migrera en SQL Server 2005-instans eller senare version till en [hanterad Azure SQL-instans](sql-managed-instance-paas-overview.md). Information om hur du migrerar till en enskild databas eller elastisk pool finns i [Översikt över migrering: SQL Server till SQL Database](../migration-guides/database/sql-server-to-sql-database-overview.md). Information om migrering om migrering från andra plattformar och vägledning om verktyg och alternativ finns i [migrera till Azure SQL](../migration-guides/index.yml).

> [!NOTE]
> Om du snabbt vill starta och testa Azure SQL-hanterad instans kanske du vill gå till snabb starts [guiden](quickstart-content-reference-guide.md) i stället för den här sidan.

På en hög nivå ser databasens migreringsprocessen ut så här:

![Migreringsprocessen](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Utvärdera kompatibilitet för SQL-hanterad instans](#assess-sql-managed-instance-compatibility) där du bör se till att det inte finns några spärrnings problem som kan förhindra migreringar.
  
  Det här steget inkluderar även skapandet av en [bas linje för prestanda](#create-a-performance-baseline) för att fastställa resursanvändningen på din käll SQL Server instans. Det här steget behövs om du vill distribuera en korrekt hanterad instans och kontrol lera att prestandan efter migreringen inte påverkas.
- [Välj anslutnings alternativ för appar](connect-application-instance.md).
- [Distribuera till en hanterad instans med optimal storlek](#deploy-to-an-optimally-sized-managed-instance) där du väljer tekniska egenskaper (antal virtuella kärnor, mängd minne) och prestanda nivå (affärskritisk generell användning) för din hanterade instans.
- [Välj metod för migrering och migrera](#select-a-migration-method-and-migrate) var du migrerar dina databaser med hjälp av offline-migrering (intern säkerhets kopiering/återställning, databas import/export) eller online-migrering (Azure Data Migration service, Transaktionsreplikering).
- [Övervaka program](#monitor-applications) för att säkerställa att du har förväntat prestanda.

> [!NOTE]
> Information om hur du migrerar en enskild databas till en enskild databas eller en elastisk pool finns i [Migrera en SQL Server databas till Azure SQL Database](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Utvärdera kompatibilitet för SQL-hanterad instans

Kontrol lera först om SQL-hanterad instans är kompatibel med programmets databas krav. SQL-hanterad instans är utformad för att ge enkel växel och Shift-migrering för de flesta befintliga program som använder SQL Server. Du kan dock ibland behöva funktioner eller funktioner som ännu inte stöds och kostnaden för att implementera en lösning är för hög.

Använd [Data Migration Assistant](/sql/dma/dma-overview) för att identifiera potentiella kompatibilitetsproblem som påverkar databas funktioner på Azure SQL Database. Om det finns rapporterade spärrnings problem kan du behöva överväga ett alternativt alternativ, till exempel [SQL Server på den virtuella Azure-datorn](https://azure.microsoft.com/services/virtual-machines/sql-server/). Här är några exempel:

- Om du behöver direkt åtkomst till operativ systemet eller fil systemet, till exempel för att installera tredje part eller anpassade agenter på samma virtuella dator med SQL Server.
- Om du har strikt beroende på funktioner som fortfarande inte stöds, till exempel FileStream/FileTable-, PolyBase-och kors instans transaktioner.
- Om du absolut behöver stanna med en angiven version av SQL Server (2012, t. ex.).
- Om beräknings kraven är mycket lägre än de som har hanterade instanser (en vCore, till exempel) och databas konsolidering är inte ett acceptabelt alternativ.

Observera att vissa av ändringarna kan påverka arbets Belastningens prestanda om du har löst alla identifierade migreringsåtgärder och fortsätter migreringen till SQL-hanterad instans:

- Den obligatoriska fullständiga återställnings modellen och regelbundna automatiserade säkerhets kopierings scheman kan påverka prestanda för dina arbets belastnings-eller underhålls-eller ETL-åtgärder om du regelbundet har använt Enkel/Mass loggad modell eller stoppat säkerhets kopieringar
- Andra konfigurationer på Server-eller databas nivå, till exempel spårnings flaggor eller kompatibilitetsnivå.
- Nya funktioner som du använder, till exempel transparent databas kryptering (TDE) eller grupper för automatisk redundans kan påverka CPU-och i/o-användning.

SQL-hanterad instans garanterar 99,99% tillgänglighet även i kritiska scenarier, så det går inte att inaktivera kostnader som orsakas av dessa funktioner. Mer information finns i [rotor saken som kan orsaka olika prestanda på SQL Server och Azure SQL-hanterad instans](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

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

Mer information om minnes intern teknik finns i [optimera prestanda med hjälp av minnes intern teknik i Azure SQL Database och Azure SQL-hanterad instans](../in-memory-oltp-overview.md)

### <a name="create-a-performance-baseline"></a>Skapa en bas linje för prestanda

Om du behöver jämföra arbets Belastningens prestanda på en hanterad instans med den ursprungliga arbets belastningen som körs på SQL Server måste du skapa en bas linje för prestanda som ska användas för jämförelse.

Prestanda bas linje är en uppsättning parametrar, till exempel genomsnittlig/högsta CPU-användning, genomsnittlig/högsta disk-i/o-latens, data flöde, IOPS, Genomsnittligt/Max sid livs längd förväntad och genomsnittlig Max storlek för tempdb. Du vill ha liknande eller till och med bättre parametrar efter migreringen, så det är viktigt att mäta och registrera bas linje värden för dessa parametrar. Förutom system parametrar måste du välja en uppsättning av de representativa frågorna eller de viktigaste frågorna i arbets belastningen och mäta min/genomsnittlig/högsta varaktighet och CPU-användning för de valda frågorna. Med dessa värden kan du jämföra prestanda för arbets belastningen som körs på den hanterade instansen med de ursprungliga värdena på käll SQL Servers instansen.

Några av de parametrar som du behöver mäta i SQL Server-instansen är:

- [Övervaka CPU-användning på SQL Server-instansen](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) och registrera den genomsnittliga och högsta CPU-användningen.
- [Övervaka minnes användningen på SQL Server-instansen](/sql/relational-databases/performance-monitor/monitor-memory-usage) och Bestäm hur mycket minne som används av olika komponenter, t. ex. buffert, planera cache, Column-Store-pool, [minnes intern OLTP](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage)osv. Dessutom bör du hitta de genomsnittliga och högsta värdena för sid livstidens förväntad minnes prestanda räknare.
- Övervaka disk-i/o-användning på käll SQL Servers instansen med [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Visa eller [prestanda räknare](/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Övervaka arbets belastning och fråga prestanda eller din SQL Server instans genom att undersöka vyer för dynamisk hantering eller Frågearkivet om du migrerar från en SQL Server 2016 +-version. Identifiera Genomsnittlig varaktighet och CPU-användning för de viktigaste frågorna i arbets belastningen för att jämföra dem med de frågor som körs på den hanterade instansen.

> [!Note]
> Om du upptäcker eventuella problem med arbets belastningen på SQL Server, till exempel hög CPU-användning, konstant minnes belastning eller tempdb-eller Parameterisering-problem, bör du försöka lösa dem på käll SQL Servers instansen innan du tar bas linjen och migreringen. Om du migrerar kända problem till nya system kan det leda till oväntade resultat och ogiltig prestanda jämförelse.

Som ett resultat av den här aktiviteten bör du ha dokumenterade genomsnitts-och topp värden för processor-, minnes-och IO-användning i ditt käll system, samt genomsnittlig och maximal varaktighet och CPU-användning för de dominerande och de mest kritiska frågorna i arbets belastningen. Du bör använda de här värdena senare för att jämföra arbets Belastningens prestanda på en hanterad instans med bas linje prestanda för arbets belastningen på käll SQL Servers instansen.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Distribuera till en hanterad instans med optimal storlek

SQL-hanterad instans är anpassad för lokala arbets belastningar som planerar att flytta till molnet. Den introducerar en [ny inköps modell](../database/service-tiers-vcore.md) som ger större flexibilitet när du väljer rätt resurs nivå för dina arbets belastningar. I den lokala världen är du förmodligen van att ändra storlek på dessa arbets belastningar med hjälp av fysiska kärnor och IO-bandbredd. Inköps modellen för en hanterad instans baseras på virtuella kärnor, eller "virtuella kärnor", med ytterligare lagring och IO tillgängligt separat. VCore-modellen är ett enklare sätt att förstå dina beräknings krav i molnet jämfört med vad du använder lokalt. Med den nya modellen kan du anpassa mål miljön i molnet till rätt storlek. Några allmänna rikt linjer som kan hjälpa dig att välja rätt tjänst nivå och egenskaper beskrivs här:

- Baserat på den grundläggande processor användningen kan du etablera en hanterad instans som matchar antalet kärnor som du använder på SQL Server, med tanke på att CPU-egenskaperna kan behöva skalas för att matcha [VM-egenskaper där den hanterade instansen är installerad](resource-limits.md#hardware-generation-characteristics).
- Utifrån bas linje minnes användningen väljer [du tjänst nivån som har motsvarande minne](resource-limits.md#hardware-generation-characteristics). Mängden minne kan inte väljas direkt, så du måste välja den hanterade instansen med mängden virtuella kärnor som har motsvarande minne (till exempel 5,1 GB/vCore i Gen5).
- Utifrån bas linjens IO-svars tid för fil under systemet väljer du mellan Generell användning (svars tid som är större än 5 ms) och Affärskritisk (laten mindre än 3 MS) tjänst nivåer.
- Baserat på baseline-dataflöde, förallokera storleken på data eller loggfiler för att få förväntad IO-prestanda.

Du kan välja beräknings-och lagrings resurser vid distributions tiden och sedan ändra det efteråt utan att införa nedtid för ditt program med hjälp av [Azure Portal](../database/scale-resources.md):

![Storlek på hanterad instans](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Information om hur du skapar VNet-infrastrukturen och en hanterad instans finns i [skapa en hanterad instans](instance-create-quickstart.md).

> [!IMPORTANT]
> Det är viktigt att behålla målets VNet och undernät i enlighet med de [hanterade instansens VNet-krav](connectivity-architecture-overview.md#network-requirements). Eventuella inkompatibiliteter kan hindra dig från att skapa nya instanser eller att använda de som du redan har skapat. Lär dig mer om att [skapa nya](virtual-network-subnet-create-arm-template.md) och [Konfigurera befintliga](vnet-existing-add-subnet.md) nätverk.

## <a name="select-a-migration-method-and-migrate"></a>Välj en metod för migrering och migrera

SQL-hanterad instans riktar sig mot användar scenarier som kräver migrering från lokala eller virtuella Azure-databaser. De är det bästa valet när du behöver lyfta upp och flytta Server delen av programmen som regelbundet använder instans nivå och/eller funktioner för olika databaser. Om det här är ditt scenario kan du flytta en hel instans till en motsvarande miljö i Azure utan att behöva skapa program på nytt.

Om du vill flytta SQL-instanser måste du planera noggrant:

- Migreringen av alla databaser som måste vara samordnad (som körs på samma instans).
- Migreringen av objekt på instans nivå som programmet är beroende av, inklusive inloggningar, autentiseringsuppgifter, SQL Agent-jobb och-operatörer och utlösare på server nivå.

SQL-hanterad instans är en hanterad tjänst som gör att du kan delegera några av de vanliga DBA-aktiviteterna till plattformen som de är inbyggda i. Därför behöver vissa data på instans nivå inte migreras, till exempel underhålls jobb för regelbundna säkerhets kopieringar eller alltid på konfigurationen, eftersom [hög tillgänglighet](../database/high-availability-sla.md) är inbyggt.

SQL-hanterad instans har stöd för följande alternativ för migrering av databas (för närvarande är de enda metoder som stöds):

- Azure Database Migration Service migrering med nästan noll stillestånds tid.
- Native `RESTORE DATABASE FROM URL` – använder interna säkerhets kopieringar från SQL Server och kräver vissa avbrott.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

[Azure Database migration service](../../dms/dms-overview.md) är en fullständigt hanterad tjänst som är utformad för att möjliggöra sömlös migrering från flera databas källor till Azure-dataplattformar med minimal stillestånds tid. Den här tjänsten effektiviserar de uppgifter som krävs för att flytta befintliga tredje parter och SQL Server databaser till Azure. Distributions alternativ i offentlig för hands version innehåller databaser i Azure SQL Database och SQL Server databaser på en virtuell Azure-dator. Database Migration Service är den rekommenderade metoden för migrering för företagets arbets belastningar.

Om du använder SQL Server Integration Services (SSIS) på SQL Server lokalt stöder Database Migration Service inte migrering av SSIS-katalogen (SSISDB) som lagrar SSIS-paket, men du kan etablera Azure-SSIS Integration Runtime (IR) i Azure Data Factory, vilket skapar en ny SSISDB i en hanterad instans så att du kan distribuera paketen till den igen. Se [skapa Azure-SSIS IR i Azure Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md).

Mer information om det här scenariot och konfigurations stegen för Database Migration Service finns i [migrera din lokala databas till hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Intern återställning från URL

ÅTERSTÄLLNING av interna säkerhets kopior (. bak-filer) som tagits från en SQL Server instans, tillgänglig på [Azure Storage](https://azure.microsoft.com/services/storage/), är en av de viktigaste funktionerna i SQL-hanterad instans som möjliggör snabb och enkel migrering av databasen.

Följande diagram ger en övergripande översikt över processen:

![Diagrammet visar SQL Server med en pil med etiketten säkerhets kopiering/uppladdning till URL som flödar till Azure Storage och en andra pil som har etiketten Återställ från URL: en som flödar från Azure Storage till en hanterad instans av SQL.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

Följande tabell innehåller mer information om de metoder som du kan använda, beroende på vilken käll SQL Servers version du kör:

|Steg|SQL-motor och version|Metod för säkerhets kopiering/återställning|
|---|---|---|
|Lägg till säkerhets kopia i Azure Storage|Före 2012 SP1-CU2|Ladda upp. bak-filen direkt till Azure Storage|
||2012 SP1 CU2-2016|Direkt säkerhets kopiering med föråldrad syntax [för autentiseringsuppgift](/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 och uppåt|Direkt säkerhets kopiering med [SAS-autentiseringsuppgifter](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Återställa från Azure Storage till en hanterad instans|[Återställ från URL med SAS-AUTENTISERINGSUPPGIFTER](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - När du migrerar en databas som skyddas av [Transparent datakryptering](../database/transparent-data-encryption-tde-overview.md) till en hanterad instans med hjälp av ett internt återställnings alternativ måste motsvarande certifikat från den lokala eller virtuella Azure-SQL Server migreras innan databasen återställs. Detaljerade anvisningar finns i [Migrera ett TDE-certifikat till en hanterad instans](tde-certificate-migrate.md).
> - Det finns inte stöd för återställning av system databaser. För att migrera instans nivå objekt (lagras i Master-eller MSDB-databaser) rekommenderar vi att du skriptar ut dem och kör T-SQL-skript på mål instansen.

En snabb start som visar hur du återställer en databas säkerhets kopia till en hanterad instans med hjälp av SAS-autentiseringsuppgifter finns i [återställa från en säkerhets kopia till en hanterad instans](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Övervakning av program

När du har slutfört migreringen till en hanterad instans bör du spåra programmets beteende och prestanda för din arbets belastning. Den här processen omfattar följande aktiviteter:

- [Jämför prestanda för arbets belastningen som körs på den hanterade instansen](#compare-performance-with-the-baseline) med den [prestanda bas linje som du skapade på käll SQL Servers instansen](#create-a-performance-baseline).
- [Övervaka prestanda i arbets belastningen](#monitor-performance) kontinuerligt för att identifiera potentiella problem och förbättringar.

### <a name="compare-performance-with-the-baseline"></a>Jämför prestanda med bas linjen

Den första aktiviteten som du måste vidta omedelbart efter lyckad migrering är att jämföra arbets Belastningens prestanda med bas linjens prestanda. Målet med den här aktiviteten är att bekräfta att arbets belastnings prestandan på din hanterade instans uppfyller dina behov.

Vid migrering av databasen till en hanterad instans sparas databas inställningarna och dess ursprungliga kompatibilitetsnivå i merparten av fallen. De ursprungliga inställningarna bevaras om möjligt för att minska risken för viss prestanda försämring jämfört med din käll SQL Servers instans. Om kompatibilitetsnivån för en användar databas var 100 eller högre innan migreringen, förblir den densamma efter migreringen. Om kompatibilitetsnivån för en användar databas var 90 innan migreringen är kompatibilitetsnivån inställd på 100 i den uppgraderade databasen, vilket är den lägsta kompatibilitetsnivå som stöds i en hanterad instans. Kompatibilitetsnivån för system databaser är 140. Eftersom migreringen till en hanterad instans faktiskt migreras till den senaste versionen av SQL Server databas motorn bör du vara medveten om att du behöver testa prestandan för arbets belastningen igen för att undvika vissa överraskande prestanda problem.

Se till att du har slutfört följande aktiviteter som krav:

- Justera inställningarna på den hanterade instansen med inställningarna från käll SQL Servers instansen genom att undersöka olika instanser, databas, tempdb-inställningar och konfigurationer. Se till att du inte har ändrat inställningar som kompatibilitetsnivå eller kryptering innan du kör den första prestanda jämförelsen eller godkänn risken att några av de nya funktionerna som du har aktiverat kan påverka vissa frågor. Om du vill minska riskerna för migreringen ändrar du kompatibilitetsnivån för databas efter prestanda övervakning.
- Implementera [rikt linjer för metod tips för lagring för generell användning](https://techcommunity.microsoft.com), till exempel för att förallokera filernas storlek för att få bättre prestanda.
- Lär dig mer om de [viktiga miljö skillnaderna som kan orsaka prestanda skillnader mellan en hanterad instans och SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)och identifiera riskerna som kan påverka prestandan.
- Se till att du behåller det aktiverade Frågearkivet och automatisk justering på din hanterade instans. Med de här funktionerna kan du mäta arbets belastnings prestanda och automatiskt åtgärda de potentiella prestanda problemen. Lär dig hur du använder Query Store som ett optimalt verktyg för att få information om arbets belastnings prestanda före och efter ändring av databasens kompatibilitetsnivå, enligt beskrivningen i [Behåll prestanda stabiliteten under uppgraderingen till en nyare SQL Server version](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
När du har för berett miljön som är så mycket som möjligt i din lokala miljö kan du börja köra din arbets belastning och mät prestanda. Mått processen bör inkludera samma parametrar som du mätte [när du skapade bas linje prestanda för dina arbets belastnings mått på käll SQL Servers instansen](#create-a-performance-baseline).
Därför bör du jämföra prestanda parametrarna med bas linjen och identifiera kritiska skillnader.

> [!NOTE]
> I många fall skulle du inte kunna få exakt matchande prestanda på den hanterade instansen och SQL Server. En hanterad Azure SQL-instans är en SQL Server databas motor, men en infrastruktur och en konfiguration med hög tillgänglighet på en hanterad instans kan medföra vissa skillnader. Du kan förvänta dig att vissa frågor kan vara snabbare medan andra kan vara långsammare. Syftet med jämförelsen är att kontrol lera att arbets belastnings prestandan i den hanterade instansen matchar prestanda på SQL Server (i genomsnitt) och identifiera kritiska frågor med den prestanda som inte matchar dina ursprungliga prestanda.

Resultatet av prestanda jämförelsen kan vara:

- Arbets belastnings prestanda på den hanterade instansen är anpassad eller bättre än arbets belastnings prestandan på SQL Server. I det här fallet har du bekräftat att migreringen lyckades.
- Majoriteten av prestanda parametrarna och frågorna i arbets belastningen fungerar bra, med vissa undantag med försämrade prestanda. I det här fallet skulle du behöva identifiera skillnaderna och deras prioritet. Om det finns några viktiga frågor med försämrade prestanda bör du undersöka om de underliggande SQL-planerna har ändrats eller om frågorna träffar några resurs gränser. Minskning i det här fallet kan vara att använda vissa tips på kritiska frågor (till exempel ändrad kompatibilitetsnivå, en äldre kardinalation-uppskattning), antingen direkt eller med hjälp av plan guider, återskapa eller skapa statistik och index som kan påverka planerna.
- De flesta frågor går långsammare på en hanterad instans jämfört med din käll SQL Server instans. I det här fallet kan du försöka identifiera rotor orsakerna till skillnaden, till exempel att nå ut till [en resurs gräns](resource-limits.md#service-tier-characteristics) som IO-gränser, minnes gräns, instans logg hastighets begränsning osv. Om det inte finns några resurs gränser som kan orsaka skillnaden kan du försöka ändra kompatibilitetsnivån för databasen eller ändra databas inställningarna som äldre kardinalitet och starta om testet. Granska de rekommendationer som tillhandahålls av vyerna hanterad instans eller Frågearkivet för att identifiera de frågor som försämrat prestanda.

> [!IMPORTANT]
> Den hanterade Azure SQL-instansen har en inbyggd funktion för automatisk plan korrigering som är aktive rad som standard. Den här funktionen säkerställer att frågor som fungerat bra i inklistring inte försämras i framtiden. Se till att den här funktionen är aktive rad och att du har kört arbets belastningen tillräckligt lång med de gamla inställningarna innan du ändrar nya inställningar för att aktivera den hanterade instansen för att lära dig mer om bas linje prestanda och planer.

Ändra parametrarna eller uppgradera tjänst nivåerna så att de konvergerar till den optimala konfigurationen tills du får den arbets belastnings prestanda som passar dina behov.

### <a name="monitor-performance"></a>Övervaka prestanda

SQL-hanterad instans innehåller många avancerade verktyg för övervakning och fel sökning, och du bör använda dem för att övervaka prestanda på din instans. Några av de parametrar som du behöver övervaka är:

- CPU-användning på instansen för att avgöra om antalet virtuella kärnor som du har allokerat är rätt matchning för din arbets belastning.
- Förväntad på din hanterade instans för att avgöra [om du behöver mer minne](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Statistik som `INSTANCE_LOG_GOVERNOR` eller `PAGEIOLATCH` som avgör om du har lagrings-i/o-problem, särskilt på generell användning nivå, där du kan behöva förallokera filer för att få bättre IO-prestanda.

## <a name="leverage-advanced-paas-features"></a>Utnyttja avancerade PaaS-funktioner

När du är på en helt hanterad plattform och har verifierat att arbets belastnings prestandan matchar dina SQL Server arbets belastnings prestanda, använder du fördelar som tillhandahålls automatiskt som en del av tjänsten.

Även om du inte gör några ändringar i den hanterade instansen under migreringen, finns det stora chanser att du kan aktivera några av de nya funktionerna när du kör din instans för att dra nytta av de senaste förbättringarna i databas motorn. Vissa ändringar aktive ras endast när [kompatibilitetsnivån för databas har ändrats](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

Du behöver till exempel inte skapa säkerhets kopior på en hanterad instans – tjänsten utför säkerhets kopieringar automatiskt. Du behöver inte längre bekymra dig om schemaläggning, säkerhets kopiering och hantering av säkerhets kopior. SQL-hanterad instans ger dig möjlighet att återställa till vilken tidpunkt som helst inom denna kvarhållningsperiod med hjälp av [återställning av tidpunkt (PITR)](../database/recovery-using-backups.md#point-in-time-restore). Dessutom behöver du inte bekymra dig om att konfigurera hög tillgänglighet eftersom [hög tillgänglighet](../database/high-availability-sla.md) är inbyggt.

För att förstärka säkerheten bör du överväga att använda [Azure Active Directory autentisering](../database/security-overview.md), [granskning](auditing-configure.md), [hot identifiering](../database/azure-defender-for-sql.md), [säkerhet på radnivå](/sql/relational-databases/security/row-level-security)och [dynamisk data maskning](/sql/relational-databases/security/dynamic-data-masking).

Förutom avancerade hanterings-och säkerhetsfunktioner innehåller en hanterad instans en uppsättning avancerade verktyg som kan hjälpa dig att [övervaka och finjustera din arbets belastning](../database/monitor-tune-overview.md). Med [Azure SQL-analys](../../azure-monitor/insights/azure-sql.md) kan du övervaka en stor uppsättning hanterade instanser och centralisera övervakningen av ett stort antal instanser och databaser. [Automatisk justering](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) i hanterade instanser övervakar kontinuerligt prestanda i SQL Plans körnings statistik och korrigerar automatiskt identifierade prestanda problem.

## <a name="next-steps"></a>Nästa steg

- Information om Azure SQL-hanterad instans finns i [Vad är Azure SQL-hanterad instans?](sql-managed-instance-paas-overview.md).
- En själv studie kurs som innehåller en återställning från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- För självstudier som visar migrering med hjälp av Database Migration Service, se [migrera din lokala databas till Azure SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).