---
title: Översikt över jobbautomatisering med elastiska jobb
description: Använd elastiska jobb för jobbautomatisering för att köra Transact-SQL-skript (T-SQL) över en uppsättning av en eller flera databaser
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, contperf-fy21q3
ms.devlang: ''
dev_langs:
- TSQL
ms.topic: conceptual
author: williamdassafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 2/1/2021
ms.openlocfilehash: 295889cf64d27761021dd09549a3366ea142516e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752040"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Automatisera hanteringsuppgifter med elastiska jobb (förhandsversion)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Du kan skapa och schemalägga elastiska jobb som kan köras regelbundet mot en eller flera Azure SQL-databaser för att köra Transact-SQL-frågor (T-SQL) och utföra underhållsuppgifter. 

Du kan definiera en måldatabas eller grupper av databaser där jobbet ska köras, och även definiera scheman för när jobbet ska köras.
Ett jobb hanterar uppgiften att logga in på måldatabasen. Du definierar, underhåller och bevarar även Transact-SQL-skript som ska köras över en grupp databaser.

Alla jobb loggar status för körning och försöker automatiskt utföra åtgärderna på nytt om det uppstår fel.

## <a name="when-to-use-elastic-jobs"></a>När elastiska jobb ska användas

Det finns flera scenarier när du kan använda elastisk jobbautomatisering:

- Automatisera hanteringsuppgifter och schemalägga dem så att de körs varje veckodag, efter timmar osv.
  - Distribuera schemaändringar, hantering av autentiseringsuppgifter, insamling av prestandadata eller insamling av telemetri för klientorganisation (kund).
  - Uppdatera referensdata (information som är gemensam över alla databaser), läsa in data från Azure Blob-lagring.
- Konfigurera jobb att köras över en samling databaser regelbundet, till exempel vid tidpunkter för låg belastning.
  - Samla in frågeresultat från en uppsättning databaser till en central tabell regelbundet. Prestandafrågor kan kontinuerligt köras och konfigureras för att utlösa ytterligare åtgärder som ska utföras.
- Samla in data för rapportering
  - Aggregera data från en samling databaser till en enda måltabell.
  - Kör databearbetningsfrågor med längre körningstid över en stor uppsättning databaser, till exempel insamling av kundtelemetri. Resultaten samlas till en enskild måltabell för ytterligare analys.
- Dataflyttar 

### <a name="automation-on-other-platforms"></a>Automatisering på andra plattformar

Överväg följande tekniker för jobbschemaläggning på olika plattformar:

- **Elastiska** jobb är jobbschemaläggningstjänster som kör anpassade jobb på en eller flera databaser i Azure SQL Database.
- **SQL Agent-jobb** körs av SQL Agent-tjänsten som fortsätter att användas för uppgiftsautomatisering i SQL Server och ingår även i Azure SQL Hanterade instanser. SQL Agent-jobb är inte tillgängliga i Azure SQL Database.

Elastiska jobb kan [Azure SQL databaser,](sql-database-paas-overview.md) [Azure SQL Database elastiska pooler](elastic-pool-overview.md)och Azure SQL databaser i [fragmentkartor](elastic-scale-shard-map-management.md).

För automatisering av T-SQL-skriptjobb i SQL Server och Azure SQL Managed Instance bör du överväga [SQL Agent](job-automation-managed-instances.md). 

För automatisering av T-SQL-skriptjobb i Azure Synapse Analytics bör du överväga [pipelines](../../synapse-analytics/data-integration/concepts-data-factory-differences.md)med återkommande utlösare , [som baseras på Azure Data Factory](../../synapse-analytics/data-integration/concepts-data-factory-differences.md).

Det är värt att notera skillnaderna mellan SQL Agent (finns i SQL Server och som en del av SQL Managed Instance) och Database Elastic Job-agenten (som kan köra T-SQL på Azure SQL-databaser eller databaser i SQL Server och Azure SQL Managed Instance, Azure Synapse Analytics).

| |Elastiska jobb |SQL Agent |
|---------|---------|---------|
|**Omfång** | Val av antal databaser i Azure SQL Database och/eller informationslager i samma Azure-moln som jobbagenten. Mål kan finnas i olika servrar, prenumerationer och/eller regioner. <br><br>Målgrupper kan bestå av enskilda databaser eller informationslager, eller alla databaser i en server, pool eller fragmentkarta (dynamiskt uppräknade vid jobbkörning). | Alla enskilda databaser i samma instans som SQL-agenten. Funktionen Administration av flera servrar i SQL Server Agent tillåter att huvudinstanser/målinstanser samordnar jobbkörningen, även om den här funktionen inte är tillgänglig i SQL Managed Instance. |
|**API:er och verktyg som stöds** | Portal, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Elastiska jobbmål

**Elastiska** jobb ger möjlighet att köra ett eller flera T-SQL-skript parallellt, över ett stort antal databaser, enligt ett schema eller på begäran.

Du kan köra schemalagda jobb mot valfri kombination av databaser: en eller flera enskilda databaser, alla databaser på en server, alla databaser i en elastisk pool eller fragmentkarta, med den ytterligare flexibiliteten att inkludera eller exkludera en specifik databas. Jobb kan köras över flera servrar och flera pooler och kan även köras mot databaser i olika prenumerationer. Servrar och pooler räknas upp dynamiskt vid körning. Därför körs jobben mot alla databaser som finns i målgruppen vid tidpunkten för körningen.

Följande bild visar en jobbagent som kör flera jobb över olika typer av målgrupper:

![Konceptuell modell för elastisk jobbagent](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Elastiska jobbkomponenter

|Komponent | Beskrivning (mer information finns nedanför tabellen) |
|---------|---------|
|[**Elastisk jobbagent**](#elastic-job-agent) | Den Azure-resurs som du skapar för att köra och hantera jobb. |
|[**Jobbdatabas**](#elastic-job-database) | En databas i Azure SQL Database som jobbagenten använder för att lagra jobbrelaterade data, jobbdefinitioner osv. |
|[**Målgrupp**](#target-group) | Den uppsättning servrar, pooler, databaser och shardkartor som ett jobb ska köras mot. |
|[**Jobb**](#elastic-jobs-and-job-steps) | Ett jobb är en arbetsprocess som består av ett eller flera jobbsteg. Jobbsteg anger vilket T-SQL-skript som ska köras samt annan information som krävs för att köra skriptet. |

#### <a name="elastic-job-agent"></a>Elastisk jobbagent

En agent för elastiska jobb är Azure-resursen för att skapa, köra och hantera jobb. Den elastiska jobbagenten är en Azure-resurs som du skapar i portalen ([PowerShell](elastic-jobs-powershell-create.md) och REST stöds också).

För att **skapa en elastisk jobbagent** krävs en befintlig databas i Azure SQL Database. Agenten konfigurerar den här Azure SQL Database som [*jobbdatabasen*](#elastic-job-database).

Det är kostnadsfritt att skapa en elastisk jobbagent. Jobbdatabasen debiteras med samma pris som alla databaser i Azure SQL Database.

#### <a name="elastic-job-database"></a>Elastisk jobbdatabas

*Jobbdatabasen* används för att definiera jobb och spåra status och historik för jobbkörningar. *Jobbdatabasen* används också för att lagra agentmetadata, loggar, resultat, jobbdefinitioner och innehåller även många användbara lagrade procedurer och andra databasobjekt för att skapa, köra och hantera jobb med T-SQL.

För den aktuella förhandsversionen krävs en befintlig databas i Azure SQL Database (S0 eller senare) för att skapa en elastisk jobbagent.

*Jobbdatabasen* ska vara en ren, tom, S0 eller högre tjänstmål Azure SQL Database. Det rekommenderade tjänstmålet för jobbdatabasen är S1 eller högre, men det optimala valet beror på prestandabehoven för dina jobb: antalet jobbsteg, antalet jobbmål och hur ofta jobben körs.  

Om åtgärderna mot jobbdatabasen är [](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) långsammare än förväntat kan du övervaka databasens prestanda och resursanvändningen i jobbdatabasen under perioder med långsamhet med hjälp av Azure Portal eller [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV. Om användningen av en resurs, till exempel CPU, data-IO eller loggskrivning, närmar sig 100 % och korrelerar med perioder med långsamhet, bör du överväga att stegvis skala databasen till högre tjänstmål (antingen i [DTU-modellen](service-tiers-dtu.md) eller i [vCore-modellen](service-tiers-vcore.md)) tills jobbdatabasens prestanda är tillräckligt bättre.

##### <a name="elastic-job-database-permissions"></a>Behörigheter för elastisk jobbdatabas

När en jobbagent skapas så skapas ett schema, tabeller och en roll som heter *jobs_reader* i *jobbdatabasen*. Rollen skapas med följande behörigheter och är utformad för att ge administratörer mer detaljerad åtkomstkontroll för jobbövervakning:

|Rollnamn |'jobs'-schemabehörigheter |'jobs_internal'-schemabehörigheter |
|---------|---------|---------|
|**jobs_reader** | SELECT | Inget |

> [!IMPORTANT]
> Tänk på säkerhetsaspekterna innan du beviljar åtkomst till *jobbdatabasen* som en databasadministratör. En obehörig användare med behörighet att skapa eller redigera jobb kan skapa eller redigera ett jobb som använder en lagrad autentiseringsbehörighet för att ansluta till en databas under den skadliga användarens kontroll, vilket kan göra det möjligt för den skadliga användaren att fastställa autentiseringsfilens lösenord.

#### <a name="target-group"></a>Målgrupp

En *målgrupp* definierar den uppsättning databaser som ett jobbsteg ska köras mot. En målgrupp kan innehålla vilket antal och vilken kombination som helst av följande:

- **Logisk SQL-server** – om en server anges ingår alla databaser som finns på servern vid tidpunkten för jobbkörningen i gruppen. Huvuddatabasens autentiseringsuppgifter måste anges så att gruppen kan räknas upp och uppdateras före jobbkörning. Mer information om logiska servrar finns i [Vad är en server i Azure SQL Database och Azure Synapse Analytics?](logical-servers.md).
- **Elastisk pool** – om en elastisk pool anges utgör alla databaser som finns i den elastiska poolen vid tidpunkten för jobbkörningen en del av gruppen. Vad gäller en server måste huvuddatabasens autentiseringsuppgifter anges så att gruppen kan uppdateras före jobbkörningen.
- **Enskild databas** – ange en eller flera enskilda databaser som ska utgöra en del av gruppen.
- **Fragmentkarta –** databaser på en fragmentkarta.

> [!TIP]
> Vid tidpunkten för jobbkörning omvärderar *dynamisk uppräkning* uppsättningen databaser i de målgrupper som inkluderar servrar eller pooler. Dynamisk uppräkning ser till att **jobb körs över alla databaser som finns i servern eller poolen vid tidpunkten för jobbkörningen**. Omvärdering av lista över databaser vid körning är särskilt användbart för scenarier där pool- eller servermedlemskap ändras ofta.

Pooler och enskilda databaser kan anges som inkluderade eller exkluderade från gruppen. På så sätt går det att skapa en målgrupp med valfri kombination av databaser. Till exempel kan du lägga till en server till en målgrupp men exkludera specifika databaser i den elastiska poolen (eller exkludera en hel elastisk pool).

En målgrupp kan inkludera databaser i flera prenumerationer och över flera regioner. Observera att körningar mellan regioner har en högre fördröjning än körningar inom samma region.

Följande exempel visar hur olika målgruppsdefinitioner räknas upp dynamiskt när ett jobb körs för att fastställa vilka databaser jobbet ska köra:

![Exempel med målgrupper](./media/job-automation-overview/targetgroup-examples1.png)

**Exempel 1** illustrerar en målgrupp som består av en lista med enskilda databaser. När ett jobbsteg körs med den här målgruppen körs åtgärden i jobbsteget i var och en av dessa databaser.<br>
**Exempel 2** visar en målgrupp som innehåller en server som mål. När ett jobbsteg körs med den här målgruppen räknas servern upp dynamiskt för att fastställa vilken listan med databaser som för närvarande finns på servern. Åtgärden i jobbsteget körs i var och en av dessa databaser.<br>
**Exempel 3** illustrerar en målgrupp liknande den i *exempel 2*, men en databas undantas uttryckligen. Åtgärden i jobbsteget körs *inte* i den undantagna databasen.<br>
**Exempel 4** illustrerar en målgrupp som innehåller en elastisk pool som mål. Precis som i *exempel 2* räknas poolen upp dynamiskt när jobbet körs för att fastställa listan med databaser i poolen.
<br><br>

![Ytterligare målgruppsexempel](./media/job-automation-overview/targetgroup-examples2.png)

**Exempel 5 och** **exempel 6 visar** avancerade scenarier där servrar, elastiska pooler och databaser kan kombineras med hjälp av regler för att inkludera och exkludera.<br>
**Exempel 7** illustrerar hur shards (fragment) i en shard-karta även kan utvärderas när ett jobb körs.

> [!NOTE]
> Själva jobbdatabasen kan vara målet för ett jobb. I det här scenariot behandlas jobbdatabasen precis som alla andra måldatabaser. Jobbanvändaren måste skapas och beviljas tillräcklig behörighet i jobbdatabasen, och den databasomfattning som krävs för jobbanvändaren måste också finnas i jobbdatabasen, precis som för andra måldatabaser.

#### <a name="elastic-jobs-and-job-steps"></a>Elastiska jobb och jobbsteg

Ett *jobb* är en arbetsprocess som körs enligt ett schema eller som ett engångsjobb. Jobb består att ett eller flera *jobbsteg*.

Varje jobbsteg anger ett T-QSL-skript som ska köras, en eller flera målgrupper som T-SQL-skriptet ska köras mot samt de autentiseringsuppgifter som jobbagenten behöver för att ansluta till måldatabasen. Varje jobbsteg har anpassade principer för tidsgränser och försök på nytt, och kan valfritt ange utdataparametrar.

#### <a name="job-output"></a>Jobbutdata

Resultatet av stegen i ett jobb på varje måldatabas registreras i detalj, och skriptutdata kan samlas in i en specifik tabell. Du kan ange en databas för att spara alla data som returneras från ett jobb.

#### <a name="job-history"></a>Jobbhistorik

Visa körningshistoriken för elastiska jobb i [jobbdatabasen genom att fråga tabellen jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status).  Ett systemrensningsjobb rensar körningshistorik som är äldre än 45 dagar. Om du vill ta bort historik som är äldre än 45 **dagar anropar sp_purge_jobhistory** lagrade proceduren i *jobbdatabasen*.

#### <a name="job-status"></a>Jobbstatus

Du kan övervaka elastiska jobbkörningar i *jobbdatabasen* genom [att fråga tabellen jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). 

### <a name="agent-performance-capacity-and-limitations"></a>Agentprestanda, kapacitet och begränsningar

Elastiska jobb använder minimalt med beräkningsresurser medan de väntar på att jobb med lång körningstid blir klara.

Beroende på storleken på målgruppen med databaser och önskad körningstid för ett jobb (antalet samtidiga arbetare) kräver agenten olika mängder beräkningsresurser och prestanda från *jobbdatabasen* (ju fler mål och ju högre antal jobb, desto större beräkningsresurser krävs det).

För närvarande är gränsen 100 samtidiga jobb.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Förhindra jobb från att minska måldatabasens prestanda

För att resurser inte ska överbelastas vid körning av jobb mot databaser i en elastisk SQL-pool kan jobb konfigureras för att begränsa antalet databaser som ett jobb kan köras mot samtidigt.

## <a name="next-steps"></a>Nästa steg

- [Så skapar du och hanterar elastiska jobb](elastic-jobs-overview.md)
- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell-create.md)
- [Skapa och hantera elastiska jobb med Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)
