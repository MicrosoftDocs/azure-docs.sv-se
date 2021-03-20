---
title: Buffert kod för XEvent-ring
description: Innehåller ett kod exempel för Transact-SQL som är enkelt och snabbt med hjälp av målet för ringbufferten, i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: a646588616b874e40b1ed2a5a0b5e691b075075d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487311"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Ring buffertens mål kod för utökade händelser i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Du vill ha ett fullständigt kod exempel för det enklaste sättet att samla in och rapportera information om en utökad händelse under ett test. Det enklaste målet för utökade händelse data är [målet för ringbufferten](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)).

I det här avsnittet presenteras ett Transact-SQL-kod exempel som:

1. Skapar en tabell med data att demonstrera med.
2. Skapar en session för en befintlig utökad händelse, nämligen **SQLServer.sql_statement_starting**.

   * Händelsen är begränsad till SQL-uttryck som innehåller en viss uppdaterings sträng: **instruktion som% Update tabEmployee%**.
   * Väljer att skicka utdata för händelsen till ett mål av typen ring-buffert, nämligen  **package0.ring_buffer**.
3. Startar händelsesessionen.
4. Utfärdar ett par enkla SQL UPDATE-instruktioner.
5. Utfärdar ett SQL SELECT-uttryck för att hämta händelse utdata från ringbufferten.

   * **sys.dm_xe_database_session_targets** och andra vyer för dynamisk hantering (DMV: er) är anslutna.
6. Stoppar händelsesessionen.
7. Släpper målet för ringbufferten för att frigöra resurser.
8. Släpper händelsesessionen och demonstrations tabellen.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto och prenumeration. Registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* Alla databaser du kan skapa en tabell i.
  
  * Du kan också [skapa en **AdventureWorksLT** demonstrations databas](single-database-create-quickstart.md) på bara några minuter.
* SQL Server Management Studio (ssms.exe), helst den senaste månatliga uppdaterings versionen.
  Du kan hämta de senaste ssms.exe från:
  
  * Avsnitt med rubriken [hämta SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  * [En direkt länk till nedladdningen.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Kodexempel

Med mycket mindre modifiering kan följande kod exempel för ringbufferten köras på antingen Azure SQL Database eller Microsoft SQL Server. Skillnaden är förekomsten av noden _database i namnet på vissa vyer för dynamisk hantering (DMV: er), som används i from-satsen i steg 5. Exempel:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Ring buffertens innehåll

Vi använde `ssms.exe` för att köra kod exemplet.

För att visa resultaten klickade vi på cellen under kolumn rubriken **target_data_XML**.

Sedan klickade du på cellen under kolumn rubriken **target_data_XML** i resultat fönstret. Då klickar du på fliken skapad en annan fil i ssms.exe där innehållet i resultat cellen visades, som XML.

Utdata visas i följande block. Det ser långt ut, men det är bara två **\<event>** element.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Frigör resurser som innehas av din ringbufferten

När du är färdig med din ringbufferten kan du ta bort den och släppa dess resurser som har utfärdat en **ändring** som följande:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

Definitionen av händelsesessionen uppdateras, men tas inte bort. Senare kan du lägga till en annan instans av ringbufferten till händelsesessionen:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Mer information

Det primära avsnittet för utökade händelser på Azure SQL Database är:

* [Utökade händelser i Azure SQL Database](xevent-db-diff-from-svr.md), vilket skiljer sig från några aspekter av utökade händelser som skiljer sig mellan Azure SQL Database jämfört med Microsoft SQL Server.

Andra kod exempel ämnen för utökade händelser finns i följande länkar. Du måste dock regelbundet kontrol lera ett exempel för att se om exempel målen Microsoft SQL Server respektive Azure SQL Database. Sedan kan du bestämma om mindre ändringar krävs för att köra exemplet.

* Kod exempel för Azure SQL Database: [händelse filens mål kod för utökade händelser i Azure SQL Database](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->