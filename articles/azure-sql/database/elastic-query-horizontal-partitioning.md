---
title: Rapportering i utskalade moln databaser
description: så här konfigurerar du elastiska frågor över horisontella partitioner
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 148c4828309738a18dbda5fd35ea634e8384bfde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792114"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Rapportering i utskalade moln databaser (för hands version)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Fråga över Shards][1]

Shardade-databaser distribuerar rader över en utskalad data nivå. Schemat är identiskt på alla deltagande databaser, även kallat horisontell partitionering. Med en elastisk fråga kan du skapa rapporter som omfattar alla databaser i en shardade-databas.

För en snabb start, se [rapportering i utskalade moln databaser](elastic-query-getting-started.md).

För icke-shardade databaser, se [fråga över moln databaser med olika scheman](elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Förutsättningar

* Skapa en Shard-karta med klient biblioteket för Elastic Database. Se [Shard Map Management](elastic-scale-shard-map-management.md). Eller Använd exempel appen i [Kom igång med elastiska databas verktyg](elastic-scale-get-started.md).
* Du kan också se [migrera befintliga databaser för att skala ut databaser](elastic-convert-to-use-elastic-tools.md).
* Användaren måste ha ÄNDRINGs behörighet för en extern DATA källa. Den här behörigheten ingår i ALTER DATABASE-behörigheten.
* ÄNDRA behörigheter för en extern DATA källa krävs för att referera till den underliggande data källan.

## <a name="overview"></a>Översikt

Dessa uttryck skapar metadata-representationen av shardade-datanivån i den elastiska fråge databasen.

1. [SKAPA HUVUD NYCKEL](/sql/t-sql/statements/create-master-key-transact-sql)
2. [SKAPA DATABASENS BEGRÄNSADE AUTENTISERINGSUPPGIFTER](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [SKAPA EXTERN DATA KÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1,1 Skapa databasens begränsade huvud nyckel och autentiseringsuppgifter

Autentiseringsuppgiften används av den elastiska frågan för att ansluta till dina fjärrdatabaser.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Se till att *" \<username\> "* inte innehåller något *" \@ servername"* -suffix.

## <a name="12-create-external-data-sources"></a>1,2 skapa externa data källor

Syntax:

```sql
<External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
        (TYPE = SHARD_MAP_MANAGER,
                   LOCATION = '<fully_qualified_server_name>',
        DATABASE_NAME = ‘<shardmap_database_name>',
        CREDENTIAL = <credential_name>,
        SHARD_MAP_NAME = ‘<shardmapname>’
               ) [;]
```

### <a name="example"></a>Exempel

```sql
CREATE EXTERNAL DATA SOURCE MyExtSrc
WITH
(
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
);
```

Hämta listan över aktuella externa data Källor:

```sql
select * from sys.external_data_sources;
```

Den externa data källan refererar till din Shard-karta. En elastisk fråga använder sedan den externa data källan och den underliggande Shard-kartan för att räkna upp de databaser som ingår i data skiktet.
Samma autentiseringsuppgifter används för att läsa Shard-kartan och för att komma åt data på Shards under bearbetningen av en elastisk fråga.

## <a name="13-create-external-tables"></a>1,3 skapa externa tabeller

Syntax:  

```sql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <sharded_external_table_options> ) }
) [;]  

<sharded_external_table_options> ::=
  DATA_SOURCE = <External_Data_Source>,
  [ SCHEMA_NAME = N'nonescaped_schema_name',]
  [ OBJECT_NAME = N'nonescaped_object_name',]
  DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN
```

**Exempel**

```sql
CREATE EXTERNAL TABLE [dbo].[order_line](
     [ol_o_id] int NOT NULL,
     [ol_d_id] tinyint NOT NULL,
     [ol_w_id] int NOT NULL,
     [ol_number] tinyint NOT NULL,
     [ol_i_id] int NOT NULL,
     [ol_delivery_d] datetime NOT NULL,
     [ol_amount] smallmoney NOT NULL,
     [ol_supply_w_id] int NOT NULL,
     [ol_quantity] smallint NOT NULL,
      [ol_dist_info] char(24) NOT NULL
)

WITH
(
    DATA_SOURCE = MyExtSrc,
     SCHEMA_NAME = 'orders',
     OBJECT_NAME = 'order_details',
    DISTRIBUTION=SHARDED(ol_w_id)
);
```

Hämta listan över externa tabeller från den aktuella databasen:

```sql
SELECT * from sys.external_tables;
```

Så här släpper du externa tabeller:

```sql
DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]
```

### <a name="remarks"></a>Kommentarer

DATA \_ källans sats definierar den externa data källan (en Shard-karta) som används för den externa tabellen.  

SCHEMA \_ namnet och objekt \_ namns satserna mappar den externa tabell definitionen till en tabell i ett annat schema. Om detta utelämnas antas schemat för fjärrobjektet vara "dbo" och dess namn antas vara identiskt med det externa tabell namn som definieras. Detta är användbart om namnet på fjärrtabellen redan har tagits i databasen där du vill skapa den externa tabellen. Du vill till exempel definiera en extern tabell för att få en sammanställd vy över katalogvyer eller DMV: er på din skalade datanivå. Eftersom katalogvyer och DMV: er redan finns lokalt kan du inte använda deras namn för den externa tabell definitionen. Använd i stället ett annat namn och Använd katalog vyns eller DMV: s namn i SCHEMA \_ namn och/eller objekt \_ namns satser. (Se exemplet nedan.)

DISTRIBUTIONs satsen anger den data distribution som används för den här tabellen. Frågan processor använder den information som anges i DISTRIBUTIONs satsen för att bygga de mest effektiva fråge planerna.

1. **Shardade** innebär att data är vågrätt partitionerade i databaserna. Partitionerings nyckeln för data distributionen är **<sharding_column_name>** -parameter.
2. **Replikerad** innebär att identiska kopior av tabellen finns på varje databas. Det är ditt ansvar att se till att replikerna är identiska i databaserna.
3. **Avrunda \_ ROBIN** innebär att tabellen är vågrätt partitionerad med en program beroende distributions metod.

**Data nivå referens**: det externa tabell-DDL: en refererar till en extern data källa. Den externa data källan anger en Shard-karta som tillhandahåller den externa tabellen med den information som krävs för att hitta alla databaser i data skiktet.

### <a name="security-considerations"></a>Säkerhetsöverväganden

Användare med åtkomst till den externa tabellen får automatiskt till gång till de underliggande fjärrtabellerna under de autentiseringsuppgifter som angavs i definitionen för den externa data källan. Undvik oönskad höjning av privilegier genom autentiseringsuppgifterna för den externa data källan. Använd GRANT eller återkalla för en extern tabell som om den vore en vanlig tabell.  

När du har definierat din externa data källa och dina externa tabeller kan du nu använda fullständig T-SQL över dina externa tabeller.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exempel: fråga vågrätt partitionerade databaser

Följande fråga utför en tre vägs koppling mellan lager, order och order rader och använder flera mängder och ett selektivt filter. Det antar (1) horisontell partitionering (horisontell partitionering) och (2) att lager, order och order rader är shardade med kolumnen lager-ID och att den elastiska frågan kan samplacera kopplingarna på Shards och bearbeta den dyra delen av frågan på Shards parallellt.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Lagrad procedur för fjärran sluten T-SQL-körning: SP \_ execute_remote

Elastiska frågor introducerar också en lagrad procedur som ger direkt åtkomst till Shards. Den lagrade proceduren heter [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) och kan användas för att köra fjärrlagrade procedurer eller T-SQL-kod på fjärrdatabaserna. Det tar följande parametrar:

* Data källans namn (nvarchar): namnet på den externa data källan av typen RDBMS.
* Fråga (nvarchar): T-SQL-frågan som ska köras på varje Shard.
* Parameter deklaration (nvarchar) – valfritt: sträng med data typs definitioner för de parametrar som används i Frågeparametern (till exempel sp_executesql).
* Parameter värde List – valfritt: kommaavgränsad lista över parameter värden (till exempel sp_executesql).

Tjänsten SP \_ execute \_ använder den externa data källan som tillhandahölls i anrops parametrarna för att köra angivet T-SQL-uttryck på fjärrdatabaserna. Den externa data källans autentiseringsuppgifter används för att ansluta till shardmap Manager-databasen och fjärrdatabaserna.  

Exempel:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Använd vanliga SQL Server anslutnings strängar för att ansluta dina program, dina BI-och data integrerings verktyg till databasen med dina externa tabell definitioner. Kontrol lera att SQL Server stöds som data källa för ditt verktyg. Referera sedan till den elastiska fråge databasen som andra SQL Server databas som är ansluten till verktyget och Använd externa tabeller från verktyget eller programmet som om de vore lokala tabeller.

## <a name="best-practices"></a>Bästa praxis

* Se till att den elastiska frågans slut punkts databas har fått åtkomst till shardmap-databasen och alla Shards genom SQL Database brand väggar.  
* Verifiera eller Använd den data distribution som definieras av den externa tabellen. Om din faktiska data distribution skiljer sig från den distribution som anges i din tabell definition kan dina frågor ge oväntade resultat.
* Elastisk fråga utför för närvarande inte Shard Eli minering när predikat över horisontell partitionering-nyckeln skulle göra det möjligt för IT att på ett säkert sätt utesluta vissa Shards från bearbetning.
* Elastisk fråga fungerar bäst för frågor där merparten av beräkningen kan utföras på Shards. Du får vanligt vis bästa möjliga frågeresultat med selektiva filter-predikat som kan utvärderas på Shards eller anslutas över de partitionerings nycklar som kan utföras i ett partitions-justerat sätt på alla Shards. Andra fråge mönster kan behöva läsa in stora mängder data från Shards till Head-noden och kan fungera dåligt

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](elastic-query-overview.md).
* En lodrät partitionerings guide finns i [komma igång med kors databas fråga (lodrät partitionering)](elastic-query-getting-started-vertical.md).
* För syntax och exempel frågor för lodrätt partitionerade data, se [fråga lodrätt partitionerade data)](elastic-query-vertical-partitioning.md)
* En självstudie för horisontell partitionering (horisontell partitionering) finns i [komma igång med elastisk fråga för horisontell partitionering (horisontell partitionering)](elastic-query-getting-started.md).
* Se [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) för en lagrad procedur som kör ett Transact-SQL-uttryck på en enskild fjärr Azure SQL Database eller uppsättning databaser som fungerar som Shards i ett schema med vågrät partitionering.

<!--Image references-->
[1]: ./media/elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->