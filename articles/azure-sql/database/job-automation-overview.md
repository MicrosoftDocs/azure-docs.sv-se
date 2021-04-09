---
title: Översikt över jobb automatisering med elastiska jobb
description: Använd elastiska jobb för jobb automatisering för att köra Transact-SQL-skript (T-SQL) i en uppsättning med en eller flera databaser
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
ms.openlocfilehash: 1f4bd28d2b95aeebe07fcad84d757327622d51f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690438"
---
# <a name="automate-management-tasks-using-elastic-jobs-preview"></a>Automatisera hanterings uppgifter med elastiska jobb (förhands granskning)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Du kan skapa och schemalägga elastiska jobb som regelbundet kan köras mot en eller flera Azure SQL-databaser för att köra Transact-SQL-frågor (T-SQL) och utföra underhålls aktiviteter. 

Du kan definiera en måldatabas eller grupper av databaser där jobbet ska köras, och även definiera scheman för när jobbet ska köras.
Ett jobb hanterar uppgiften att logga in på måldatabasen. Du definierar, underhåller och bevarar även Transact-SQL-skript som ska köras över en grupp databaser.

Alla jobb loggar status för körning och försöker automatiskt utföra åtgärderna på nytt om det uppstår fel.

## <a name="when-to-use-elastic-jobs"></a>När du ska använda elastiska jobb

Det finns flera scenarier när du kan använda automatisering av elastiska jobb:

- Automatisera hanterings uppgifter och Schemalägg dem att köras varje vardag, efter timmar osv.
  - Distribuera schemaändringar, hantering av autentiseringsuppgifter, insamling av prestandadata eller insamling av telemetri för klientorganisation (kund).
  - Uppdatera referensdata (information som är gemensam över alla databaser), läsa in data från Azure Blob-lagring.
- Konfigurera jobb att köras över en samling databaser regelbundet, till exempel vid tidpunkter för låg belastning.
  - Samla in frågeresultat från en uppsättning databaser till en central tabell regelbundet. Prestandafrågor kan kontinuerligt köras och konfigureras för att utlösa ytterligare åtgärder som ska utföras.
- Samla in data för rapportering
  - Samla in data från en samling databaser till en enda mål tabell.
  - Kör databearbetningsfrågor med längre körningstid över en stor uppsättning databaser, till exempel insamling av kundtelemetri. Resultaten samlas till en enskild måltabell för ytterligare analys.
- Dataflyttar 

### <a name="automation-on-other-platforms"></a>Automatisering på andra plattformar

Överväg följande funktioner för schemaläggning av jobb på olika plattformar:

- **Elastiska jobb** är jobb schemaläggnings tjänster som kör anpassade jobb på en eller flera databaser i Azure SQL Database.
- **SQL Agent-jobb** körs av SQL-agenttjänsten som fortsätter att användas för uppgifts automatisering i SQL Server och ingår också i Azure SQL Managed instances. SQL Agent-jobb är inte tillgängliga i Azure SQL Database.

Elastiska jobb kan rikta in sig på [Azure SQL-databaser](sql-database-paas-overview.md), [Azure SQL Database elastiska pooler](elastic-pool-overview.md)och Azure SQL-databaser i [Shard Maps](elastic-scale-shard-map-management.md).

För skript för T-SQL-skript i SQL Server och Azure SQL-hanterad instans, Överväg [SQL-agenten](job-automation-managed-instances.md). 

För skript för T-SQL-skript i Azure Synapse Analytics kan du överväga [pipelines med återkommande utlösare](../../synapse-analytics/data-integration/concepts-data-factory-differences.md), som [baseras på Azure Data Factory](../../synapse-analytics/data-integration/concepts-data-factory-differences.md).

Det är värt att notera skillnaderna mellan SQL-agenten (tillgänglig i SQL Server och som en del av SQL-hanterad instans) och den elastiska jobb agenten i databasen (som kan köra T-SQL på Azure SQL-databaser eller databaser i SQL Server och Azure SQL Managed instance, Azure Synapse Analytics).

| |Elastiska jobb |SQL Agent |
|---------|---------|---------|
|**Omfång** | Valfritt antal databaser i Azure SQL Database och/eller informations lager i samma Azure-moln som jobb agenten. Mål kan vara på olika servrar, prenumerationer och/eller regioner. <br><br>Mål grupper kan bestå av enskilda databaser eller informations lager, eller alla databaser i en server, pool eller Shard-mappning (dynamiskt räknas i jobb körningen). | En enskild databas i samma instans som SQL-agenten. Funktionen för administration av flera servrar i SQL Server Agent tillåter att huvud-/mål instanser koordinerar jobb körningen, men den här funktionen är inte tillgänglig i SQL-hanterad instans. |
|**API:er och verktyg som stöds** | Portal, PowerShell, T-SQL, Azure Resource Manager | T-SQL, SQL Server Management Studio (SSMS) |
 
## <a name="elastic-job-targets"></a>Elastiska jobb mål

**Elastiska jobb** ger möjlighet att köra ett eller flera T-SQL-skript parallellt, över ett stort antal databaser, enligt ett schema eller på begäran.

Du kan köra schemalagda jobb mot valfri kombination av databaser: en eller flera enskilda databaser, alla databaser på en server, alla databaser i en elastisk pool eller Shard karta med den extra flexibiliteten att ta med eller undanta en viss databas. Jobb kan köras över flera servrar och flera pooler och kan även köras mot databaser i olika prenumerationer. Servrar och pooler räknas upp dynamiskt vid körning. Därför körs jobben mot alla databaser som finns i målgruppen vid tidpunkten för körningen.

Följande bild visar en jobbagent som kör flera jobb över olika typer av målgrupper:

![Konceptuell modell för elastisk jobbagent](./media/job-automation-overview/conceptual-diagram.png)

### <a name="elastic-job-components"></a>Elastiska jobb komponenter

|Komponent | Beskrivning (mer information finns nedanför tabellen) |
|---------|---------|
|[**Elastisk jobbagent**](#elastic-job-agent) | Den Azure-resurs som du skapar för att köra och hantera jobb. |
|[**Jobbdatabas**](#elastic-job-database) | En databas i Azure SQL Database som jobb agenten använder för att lagra projektrelaterade data, jobb definitioner osv. |
|[**Målgrupp**](#target-group) | Den uppsättning servrar, pooler, databaser och shardkartor som ett jobb ska köras mot. |
|[**Jobb**](#elastic-jobs-and-job-steps) | Ett jobb är en arbetsprocess som består av ett eller flera jobbsteg. Jobbsteg anger vilket T-SQL-skript som ska köras samt annan information som krävs för att köra skriptet. |

#### <a name="elastic-job-agent"></a>Elastisk jobb agent

En agent för elastiska jobb är Azure-resursen för att skapa, köra och hantera jobb. Den elastiska jobbagenten är en Azure-resurs som du skapar i portalen ([PowerShell](elastic-jobs-powershell-create.md) och REST stöds också).

Att skapa en **elastisk jobb agent** kräver en befintlig databas i Azure SQL Database. Agenten konfigurerar den här befintliga Azure SQL Database som [*jobb databas*](#elastic-job-database).

Det är kostnadsfritt att skapa en elastisk jobbagent. Jobb databasen debiteras enligt samma taxa som vilken databas som helst i Azure SQL Database.

#### <a name="elastic-job-database"></a>Elastisk jobb databas

*Jobbdatabasen* används för att definiera jobb och spåra status och historik för jobbkörningar. *Jobb databasen* används också för att lagra Gent-metadata, loggar, resultat, jobb definitioner och innehåller också många användbara lagrade procedurer och andra databas objekt för att skapa, köra och hantera jobb med T-SQL.

För den aktuella för hands versionen krävs en befintlig databas i Azure SQL Database (S0 eller högre) för att skapa en elastisk jobb agent.

*Jobb databasen* måste vara en ren, tom, S0 eller högre tjänst mål Azure SQL Database. Det rekommenderade tjänst målet för *jobb databasen* är S1 eller högre, men det bästa valet beror på jobbets prestanda krav: antalet jobb steg, antalet jobb mål och hur ofta jobb körs. 

Om åtgärder mot jobb databasen är långsammare än förväntat, [övervaka](monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) databas prestanda och resursutnyttjande i jobb databasen under perioder med låg belastning med Azure Portal eller [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) DMV. Om användningen av en resurs, till exempel CPU, data-i/o eller logg skrivnings metoder 100% och korreleras med perioder av låg belastning, bör du öka skala databasen till högre tjänste mål (antingen i [DTU-modellen](service-tiers-dtu.md) eller i [vCore-modellen](service-tiers-vcore.md)) tills prestanda för jobb databasen är tillräckligt bättre.

##### <a name="elastic-job-database-permissions"></a>Databas behörigheter för elastiskt jobb

När en jobbagent skapas så skapas ett schema, tabeller och en roll som heter *jobs_reader* i *jobbdatabasen*. Rollen skapas med följande behörigheter och är utformad för att ge administratörer mer detaljerad åtkomstkontroll för jobbövervakning:

|Rollnamn |'jobs'-schemabehörigheter |'jobs_internal'-schemabehörigheter |
|---------|---------|---------|
|**jobs_reader** | SELECT | Inget |

> [!IMPORTANT]
> Tänk på säkerhetsaspekterna innan du beviljar åtkomst till *jobbdatabasen* som en databasadministratör. En obehörig användare med behörighet att skapa eller redigera jobb kan skapa eller redigera ett jobb som använder lagrade autentiseringsuppgifter för att ansluta till en databas under den skadliga användarens kontroll, vilket kan göra det möjligt för obehöriga användare att fastställa autentiseringsuppgifternas lösen ord.

#### <a name="target-group"></a>Målgrupp

En *målgrupp* definierar den uppsättning databaser som ett jobbsteg ska köras mot. En målgrupp kan innehålla vilket antal och vilken kombination som helst av följande:

- **Logisk SQL Server** – om en server anges, är alla databaser som finns på-servern vid tidpunkten för jobb körningen del av gruppen. Huvuddatabasens autentiseringsuppgifter måste anges så att gruppen kan räknas upp och uppdateras före jobbkörning. Mer information om logiska servrar finns i [Vad är en server i Azure SQL Database och Azure Synapse Analytics?](logical-servers.md).
- **Elastisk pool** – om en elastisk pool anges utgör alla databaser som finns i den elastiska poolen vid tidpunkten för jobbkörningen en del av gruppen. Vad gäller en server måste huvuddatabasens autentiseringsuppgifter anges så att gruppen kan uppdateras före jobbkörningen.
- **Enskild databas** – ange en eller flera enskilda databaser som ska utgöra en del av gruppen.
- **Shard Map** -databaser för en Shard-karta.

> [!TIP]
> Vid tidpunkten för jobbkörning omvärderar *dynamisk uppräkning* uppsättningen databaser i de målgrupper som inkluderar servrar eller pooler. Dynamisk uppräkning ser till att **jobb körs över alla databaser som finns i servern eller poolen vid tidpunkten för jobbkörningen**. Omvärdering av lista över databaser vid körning är särskilt användbart för scenarier där pool- eller servermedlemskap ändras ofta.

Pooler och enskilda databaser kan anges som inkluderade eller exkluderade från gruppen. På så sätt går det att skapa en målgrupp med valfri kombination av databaser. Till exempel kan du lägga till en server till en målgrupp men exkludera specifika databaser i den elastiska poolen (eller exkludera en hel elastisk pool).

En målgrupp kan inkludera databaser i flera prenumerationer och över flera regioner. Observera att körningar mellan regioner har en högre fördröjning än körningar inom samma region.

Följande exempel visar hur olika målgruppsdefinitioner räknas upp dynamiskt när ett jobb körs för att fastställa vilka databaser jobbet ska köra:

![Exempel med målgrupper](./media/job-automation-overview/targetgroup-examples1.png)

**Exempel 1** illustrerar en målgrupp som består av en lista med enskilda databaser. När ett jobbsteg körs med den här målgruppen körs åtgärden i jobbsteget i var och en av dessa databaser.<br>
**Exempel 2** visar en mål grupp som innehåller en server som mål. När ett jobbsteg körs med den här målgruppen räknas servern upp dynamiskt för att fastställa vilken listan med databaser som för närvarande finns på servern. Åtgärden i jobbsteget körs i var och en av dessa databaser.<br>
**Exempel 3** illustrerar en målgrupp liknande den i *exempel 2*, men en databas undantas uttryckligen. Åtgärden i jobbsteget körs *inte* i den undantagna databasen.<br>
**Exempel 4** illustrerar en målgrupp som innehåller en elastisk pool som mål. Precis som i *exempel 2* räknas poolen upp dynamiskt när jobbet körs för att fastställa listan med databaser i poolen.
<br><br>

![Fler exempel på mål grupper](./media/job-automation-overview/targetgroup-examples2.png)

**Exempel 5** och **exempel 6** visar avancerade scenarier där servrar, elastiska pooler och databaser kan kombineras med regler för att inkludera och exkludera.<br>
**Exempel 7** illustrerar hur shards (fragment) i en shard-karta även kan utvärderas när ett jobb körs.

> [!NOTE]
> Själva jobb databasen kan vara målet för ett jobb. I det här scenariot behandlas jobb databasen precis som vilken annan mål databas som helst. Jobb användaren måste ha skapats och beviljats tillräckliga behörigheter i jobb databasen, och databasen begränsade autentiseringsuppgifter för jobb användaren måste också finnas i jobb databasen, precis som för alla andra mål databaser.

#### <a name="elastic-jobs-and-job-steps"></a>Elastiska jobb och jobb steg

Ett *jobb* är en arbetsprocess som körs enligt ett schema eller som ett engångsjobb. Jobb består att ett eller flera *jobbsteg*.

Varje jobbsteg anger ett T-QSL-skript som ska köras, en eller flera målgrupper som T-SQL-skriptet ska köras mot samt de autentiseringsuppgifter som jobbagenten behöver för att ansluta till måldatabasen. Varje jobbsteg har anpassade principer för tidsgränser och försök på nytt, och kan valfritt ange utdataparametrar.

#### <a name="job-output"></a>Jobbutdata

Resultatet av stegen i ett jobb på varje måldatabas registreras i detalj, och skriptutdata kan samlas in i en specifik tabell. Du kan ange en databas för att spara alla data som returneras från ett jobb.

#### <a name="job-history"></a>Jobbhistorik

Visa körnings historiken för elastiska jobb i *jobb databasen* genom att [skicka en fråga till tabellen Jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). Ett systemrensningsjobb rensar körningshistorik som är äldre än 45 dagar. Om du vill ta bort historik som är äldre än 45 dagar anropar du den lagrade proceduren **sp_purge_history** i *jobbdatabasen*.

#### <a name="job-status"></a>Jobb status

Du kan övervaka elastiska jobb körningar i *jobb databasen* genom att [köra frågor mot tabellen Jobs.job_executions](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status). 

### <a name="agent-performance-capacity-and-limitations"></a>Agentprestanda, kapacitet och begränsningar

Elastiska jobb använder minimalt med beräkningsresurser medan de väntar på att jobb med lång körningstid blir klara.

Beroende på storleken på målgruppen med databaser och önskad körningstid för ett jobb (antalet samtidiga arbetare) kräver agenten olika mängder beräkningsresurser och prestanda från *jobbdatabasen* (ju fler mål och ju högre antal jobb, desto större beräkningsresurser krävs det).

Gränsen är för närvarande 100 samtidiga jobb.

#### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Förhindra jobb från att minska måldatabasens prestanda

För att resurser inte ska överbelastas vid körning av jobb mot databaser i en elastisk SQL-pool kan jobb konfigureras för att begränsa antalet databaser som ett jobb kan köras mot samtidigt.

## <a name="next-steps"></a>Nästa steg

- [Så skapar du och hanterar elastiska jobb](elastic-jobs-overview.md)
- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell-create.md)
- [Skapa och hantera elastiska jobb med Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)