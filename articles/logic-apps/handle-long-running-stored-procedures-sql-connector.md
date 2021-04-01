---
title: Hantera tids krävande lagrade procedurer i SQL-kopplingen
description: Hantera lagrade procedurer som nådde tids gränsen vid användning av SQL-anslutaren i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93103111"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Hantera tids gränser för lagrade procedurer i SQL-anslutningen för Azure Logic Apps

När din Logi Kap par fungerar med resultat uppsättningar så att [SQL-kopplingen](../connectors/connectors-create-api-sqlazure.md) inte returnerar alla resultat samtidigt, eller om du vill ha mer kontroll över storlek och struktur för dina resultat uppsättningar, kan du skapa en [lagrad procedur](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) som ordnar resultatet på det sätt som du vill. SQL-anslutningen innehåller många backend-funktioner som du kan komma åt genom att använda [Azure Logic Apps](../logic-apps/logic-apps-overview.md) så att du enkelt kan automatisera affärs uppgifter som fungerar med SQL Database-tabeller.

När du till exempel hämtar eller infogar flera rader kan din Logi Kap par iterera genom dessa rader genom att använda en [ **until** -slinga](../logic-apps/logic-apps-control-flow-loops.md#until-loop) inom dessa [gränser](../logic-apps/logic-apps-limits-and-config.md). Men när din Logic-app måste arbeta med tusentals eller miljon tals rader, vill du minimera kostnaderna på grund av anrop till databasen. Mer information finns i [Hantera Mass data med hjälp av SQL-anslutningen](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Tids gräns för körning av lagrad procedur

SQL-anslutningen har en tids gräns för lagrade procedurer som är [mindre än 2 minuter](/connectors/sql/#known-issues-and-limitations). Vissa lagrade procedurer kan ta längre tid än den här gränsen att slutföras, vilket orsakar ett `504 Timeout` fel. Ibland kodas dessa långvariga processer som lagrade procedurer explicit för detta ändamål. På grund av timeout-gränsen kan anrop av de här procedurerna från Azure Logic Apps skapa problem. Även om SQL-anslutaren inte har inbyggt stöd för ett asynkront läge, kan du undvika det här problemet och simulera det här läget genom att använda en SQL-slutförande-utlösare, en intern SQL-direktautentisering, en tillstånds tabell och jobb på Server sidan. För den här uppgiften kan du använda [Azure elastiska jobb agenten](../azure-sql/database/elastic-jobs-overview.md) för [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). Du kan använda [SQL Server Agent](/sql/ssms/agent/sql-server-agent)för [SQL Server lokalt](/sql/sql-server/sql-server-technical-documentation) och [Azure SQL-hanterad instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

Anta till exempel att du har följande tids krävande lagrade procedur, vilket tar längre tid än tids gränsen för att slutföra körningen. Om du kör den här lagrade proceduren från en Logic-app med hjälp av SQL-anslutningen visas ett `HTTP 504 Gateway Timeout` fel meddelande som resultat.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

I stället för att direkt anropa den lagrade proceduren kan du köra proceduren asynkront i bakgrunden med hjälp av en *jobb agent*. Du kan lagra indata och utdata i en tillstånds tabell som du sedan kan interagera med via din Logic app. Om du inte behöver indata och utdata, eller om du redan har skrivit resultatet i en tabell i den lagrade proceduren, kan du förenkla den här metoden.

> [!IMPORTANT]
> Se till att din lagrade procedur och alla jobb är *idempotenta*, vilket innebär att de kan köras flera gånger utan att det påverkar resultatet. Om den asynkrona bearbetningen Miss lyckas eller om tids gränsen uppnås kan jobb agenten försöka utföra steget igen och därmed lagrad procedur flera gånger. Du kan undvika duplicering av utdata innan du skapar några objekt genom att granska dessa [metod tips och metoder](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts).

I nästa avsnitt beskrivs hur du kan använda Azure elastiska jobb agent för Azure SQL Database. Du kan använda SQL Server Agent för SQL Server och Azure SQL-hanterad instans. Vissa hanterings uppgifter skiljer sig, men de grundläggande stegen är desamma som när du konfigurerar en jobb agent för Azure SQL Database.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Jobb agent för Azure SQL Database

Om du vill skapa ett jobb som kan köra den lagrade proceduren för [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)använder du [Azures elastiska jobb agent](../azure-sql/database/elastic-jobs-overview.md). Skapa din jobb agent i Azure Portal. Med den här metoden kan du lägga till flera lagrade procedurer i databasen som används av agenten, även kallat *Agent databasen*. Du kan sedan skapa ett jobb som kör din lagrade procedur i mål databasen och avbilda utdata när det är klart.

Innan du kan skapa jobbet måste du konfigurera behörigheter, grupper och mål enligt beskrivningen i den [fullständiga dokumentationen för Azure elastiska jobb agenten](../azure-sql/database/elastic-jobs-overview.md). Du måste också skapa en stödjande tabell i mål databasen enligt beskrivningen i följande avsnitt.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Skapa tillstånds tabell för att registrera parametrar och lagra indata

SQL Agent-jobb accepterar inte indataparametrar. I stället, i mål databasen, skapar du en tillstånds tabell där du registrerar parametrarna och lagrar de indata som ska användas för att anropa dina lagrade procedurer. Alla agent jobbs steg körs mot mål databasen, men jobbets lagrade procedurer körs mot Agent databasen. 

Använd det här schemat för att skapa tillstånds tabellen:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

Så här ser den resulterande tabellen i [SQL Server Management Studio (Smss)](/sql/ssms/download-sql-server-management-studio-ssms):

![Skärm bild som visar skapade tillstånds tabell som lagrar indata för lagrad procedur.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

För att säkerställa bästa prestanda och se till att Agent jobbet kan hitta den associerade posten använder tabellen jobb körnings-ID ( `jobid` ) som primär nyckel. Om du vill kan du också lägga till enskilda kolumner för indataparametrarna. Det tidigare beskrivna schemat kan ofta hantera flera parametrar, men det är begränsat till storleken som beräknas av `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Skapa ett jobb på den översta nivån för att köra den lagrade proceduren

Om du vill köra den tids krävande lagrade proceduren skapar du den här jobb agenten på den översta nivån i Agent databasen:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Lägg nu till steg i jobbet som Parameterisera, kör och slutför den lagrade proceduren. Som standard kan ett jobb steg ta längre tid efter 12 timmar. Om den lagrade proceduren behöver mer tid, eller om du vill att proceduren ska ta tid ut tidigare, kan du ändra `step_timeout_seconds` parametern till ett annat värde som anges i sekunder. Som standard har ett steg 10 inbyggda återförsök med en backoff-timeout mellan varje nytt försök, som du kan använda för din fördel.

Här är de steg du lägger till:

1. Vänta tills parametrarna visas i `LongRunningState` tabellen.

   Det första steget väntar på att parametrarna ska läggas till i `LongRunningState` tabellen, vilket sker strax efter att jobbet har startats. Om jobb körnings-ID: t ( `jobid` ) inte läggs till i `LongRunningState` tabellen, Miss lyckas inte steget och standardvärdet för återförsök eller backoff sker i väntan:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Fråga parametrarna från tillstånds tabellen och skicka dem till den lagrade proceduren. Det här steget Kör också proceduren i bakgrunden. 

   Om din lagrade procedur inte behöver parametrar, anropar du bara den lagrade proceduren direkt. Annars `@timespan` `@callparams` kan du använda, som du även kan utöka för att skicka ytterligare parametrar, för att skicka parametern.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Slutför jobbet och registrera resultatet.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Starta jobbet och skicka parametrarna

För att starta jobbet använder du en genom strömnings intern fråga med [åtgärden **kör en SQL-fråga**](/connectors/sql/#execute-a-sql-query-(v2)) och push-överför jobbets parametrar direkt till tillstånds tabellen. Om du vill ange indata till `jobid` attributet i mål tabellen, Logic Apps lägger till en **för varje** loop som upprepas genom tabellens utdata från föregående åtgärd. För varje jobb körnings-ID kör du en **Infoga rad** -åtgärd som använder dynamiska data utdata, `ResultSets JobExecutionId` för att lägga till parametrarna för jobbet för att packa upp och skicka till den lagrade mål proceduren.

![Skärm bild som visar åtgärder som ska användas för att starta jobbet och skicka parametrar till den lagrade proceduren.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

När jobbet har slutförts uppdaterar jobbet `LongRunningState` tabellen så att du enkelt kan utlösa resultatet med hjälp av [ **när ett objekt ändras** av utlösaren](/connectors/sql/#when-an-item-is-modified-(v2)). Om du inte behöver utdata, eller om du redan har en utlösare som övervakar en utdataparameter, kan du hoppa över den här delen.

![Skärm bild som visar SQL-utlösaren för när ett objekt ändras.](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Jobb agent för SQL Server eller Azure SQL-hanterad instans

I samma scenario kan du använda [SQL Server Agent](/sql/ssms/agent/sql-server-agent) för [SQL Server lokalt](/sql/sql-server/sql-server-technical-documentation) och [Azure SQL-hanterad instans](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Även om vissa hanterings detaljer skiljer sig, är de grundläggande stegen desamma som när du konfigurerar en jobb agent för Azure SQL Database.

## <a name="next-steps"></a>Nästa steg

[Ansluta till SQL Server, Azure SQL Database eller Azure SQL-hanterad instans](../connectors/connectors-create-api-sqlazure.md)

