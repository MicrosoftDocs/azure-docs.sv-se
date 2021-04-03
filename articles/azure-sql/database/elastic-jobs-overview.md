---
title: Elastic Database jobb (förhands granskning)
description: Konfigurera Elastic Database jobb (för hands version) för att köra Transact-SQL-skript (T-SQL) i en uppsättning av en eller flera databaser i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: f9a026ed47d662b80ef01e505bfbcf8f32d20b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792182"
---
# <a name="create-configure-and-manage-elastic-jobs-preview"></a>Skapa, konfigurera och hantera elastiska jobb (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här artikeln får du lära dig hur du skapar, konfigurerar och hanterar elastiska jobb.

Om du inte har använt elastiska jobb kan du [läsa mer om jobb automatiserings begreppen i Azure SQL Database](job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Skapa och konfigurera agenten

1. Skapa eller identifiera en tom S0 eller högre databas. Den här databasen kommer att användas som *jobb databas* när en elastisk jobb agent skapas.
2. Skapa en elastisk jobb agent i [portalen](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) eller med [PowerShell](elastic-jobs-powershell-create.md#create-the-elastic-job-agent).

   ![Skapa en elastisk jobb agent](./media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Skapa, kör och hantera jobb

1. Skapa en autentiseringsuppgift för jobb körning i *jobb databasen* med hjälp av [PowerShell](elastic-jobs-powershell-create.md) eller [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-credential-for-job-execution).
2. Definiera mål gruppen (databaserna som du vill köra jobbet mot) med hjälp av [PowerShell](elastic-jobs-powershell-create.md) eller [T-SQL](elastic-jobs-tsql-create-manage.md#create-a-target-group-servers).
3. Skapa autentiseringsuppgifter för en jobbagent i varje databas som jobbet ska köras mot [(lägg till användaren (eller rollen) till varje databas i gruppen)](logins-create-manage.md). Exempel finns i [PowerShell-självstudien](elastic-jobs-powershell-create.md).
4. Skapa ett jobb med hjälp av [PowerShell](elastic-jobs-powershell-create.md) eller [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases).
5. Lägg till jobbsteg med [PowerShell](elastic-jobs-powershell-create.md) eller [T-SQL](elastic-jobs-tsql-create-manage.md#deploy-new-schema-to-many-databases).
6. Kör ett jobb med [PowerShell](elastic-jobs-powershell-create.md#run-the-job) eller [T-SQL](elastic-jobs-tsql-create-manage.md#begin-unplanned-execution-of-a-job).
7. Övervaka jobb körnings status med hjälp av portalen, [PowerShell](elastic-jobs-powershell-create.md#monitor-status-of-job-executions) eller [T-SQL](elastic-jobs-tsql-create-manage.md#monitor-job-execution-status).

   ![Portal](./media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>Autentiseringsuppgifter för att köra jobb

Jobbet använder [databasbegränsade autentiseringsuppgifter](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) för att ansluta till de databaser som anges av målgruppen vid körning. Om en målgrupp innehåller servrar eller pooler används de här databasbegränsade autentiseringsuppgifterna för att ansluta till huvuddatabasen för att räkna upp de tillgängliga databaserna.

Att konfigurera rätt autentiseringsuppgifter för att köra ett jobb kan vara något förvirrande, så tänk på följande punkter:

- Autentiseringsuppgifterna för databasens omfång måste skapas i *jobb databasen*.
- **Alla mål databaser måste ha en inloggning med [tillräckliga behörigheter](/sql/relational-databases/security/permissions-database-engine) för att jobbet ska kunna slutföras** ( `jobuser` i diagrammet nedan).
- Autentiseringsuppgifter kan återanvändas mellan jobb och lösen ord för autentiseringsuppgifter krypteras och skyddas från användare som har skrivskyddad åtkomst till jobb objekt.

Följande bild är utformad för att underlätta förståelse och konfiguration av rätt autentiseringsuppgifter för jobb. **Kom ihåg att skapa användaren i varje databas (alla *målanvändardatabaser*) som jobbet behöver köras mot**.

![Autentiseringsuppgifter för elastiska jobb](./media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Rekommenderade säkerhetsmetoder

Några metodtips för att arbeta med elastiska jobb:

- Begränsa användningen av API:er till betrodda personer.
- Autentiseringsuppgifter ska ha minsta möjliga behörigheter som krävs för att utföra jobbsteget. Mer information finns i [auktorisering och behörigheter](/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- När du använder en mål grupps medlem för Server och/eller pool, rekommenderar vi starkt att du skapar en separat autentiseringsuppgift med behörighet på huvud databasen för att Visa/lista databaser som används för att expandera databas listor för-servrar och/eller pooler innan jobb körningen.

## <a name="agent-performance-capacity-and-limitations"></a>Agentprestanda, kapacitet och begränsningar

Elastiska jobb använder minimalt med beräkningsresurser medan de väntar på att jobb med lång körningstid blir klara.

Beroende på storleken på målgruppen med databaser och önskad körningstid för ett jobb (antalet samtidiga arbetare) kräver agenten olika mängder beräkningsresurser och prestanda från *jobbdatabasen* (ju fler mål och ju högre antal jobb, desto större beräkningsresurser krävs det).

För tillfället är förhandsgranskningen begränsad till 100 samtidiga jobb.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>Förhindra jobb från att minska måldatabasens prestanda

För att resurser inte ska överbelastas vid körning av jobb mot databaser i en elastisk SQL-pool kan jobb konfigureras för att begränsa antalet databaser som ett jobb kan köras mot samtidigt.

Ange antalet samtidiga databaser som ett jobb körs på genom att ange den `sp_add_jobstep` lagrade procedurens `@max_parallelism` parameter i T-SQL eller `Add-AzSqlElasticJobStep -MaxParallelism` i PowerShell.

## <a name="best-practices-for-creating-jobs"></a>Metodtips för att skapa jobb

### <a name="idempotent-scripts"></a>Idempotent-skript
T-SQL-skripten för ett jobb måste vara [idempotent](https://en.wikipedia.org/wiki/Idempotence). **Idempotent** innebär att om skriptet lyckas och körs igen, så erhålls samma resultat. Ett skript kan misslyckas på grund av tillfälliga nätverksproblem. I så fall försöker jobbet automatiskt köra skriptet på nytt ett förinställt antal gånger innan det ger upp. Ett idempotent-skript ger samma resultat även om det har lyckats köra två eller fler gånger.

En enkel metod är att testa om ett objekt finns innan det skapas.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

På liknande sätt måste ett skript kunna lyckas köra genom att logiskt testa och motverka eventuella tillstånd som det hittar.



## <a name="next-steps"></a>Nästa steg

- [Skapa och hantera elastiska jobb med PowerShell](elastic-jobs-powershell-create.md)
- [Skapa och hantera elastiska jobb med Transact-SQL (T-SQL)](elastic-jobs-tsql-create-manage.md)