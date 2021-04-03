---
title: Fråga över moln databaser med olika scheman
description: så här konfigurerar du frågor över flera databaser över lodräta partitioner
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: c507a4c618713ba83d25b9defa918092db1a3c8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792097"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Fråga över moln databaser med olika scheman (förhands granskning)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Fråga över tabeller i olika databaser][1]

Lodrätt partitionerade databaser använder olika uppsättningar tabeller i olika databaser. Det innebär att schemat är olika i olika databaser. Till exempel är alla tabeller för inventering i en databas medan alla redovisnings tabeller finns på en andra databas.

## <a name="prerequisites"></a>Förutsättningar

* Användaren måste ha ÄNDRINGs behörighet för en extern DATA källa. Den här behörigheten ingår i ALTER DATABASE-behörigheten.
* ÄNDRA behörigheter för en extern DATA källa krävs för att referera till den underliggande data källan.

## <a name="overview"></a>Översikt

> [!NOTE]
> Till skillnad från vågrät partitionering, är dessa DDL-instruktioner inte beroende av att definiera en data nivå med en Shard-karta via klient biblioteket för Elastic Database.
>

1. [SKAPA HUVUD NYCKEL](/sql/t-sql/statements/create-master-key-transact-sql)
2. [SKAPA DATABASENS BEGRÄNSADE AUTENTISERINGSUPPGIFTER](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [SKAPA EXTERN DATA KÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Skapa databasens begränsade huvud nyckel och autentiseringsuppgifter

Autentiseringsuppgiften används av den elastiska frågan för att ansluta till dina fjärrdatabaser.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Se till att `<username>` inte innehåller något **" \@ servername"** -suffix.

## <a name="create-external-data-sources"></a>Skapa externa data källor

Syntax:

<External_Data_Source>:: = skapa extern DATA källa <data_source_name> med (typ = RDBMS, LOCATION = ' <fully_qualified_server_name> ' DATABASE_NAME = ' <remote_database_name> ',  
    CREDENTIAL = <credential_name>) [;]

> [!IMPORTANT]
> TYP parametern måste anges som **RDBMS**.

### <a name="example"></a>Exempel

I följande exempel visas användningen av CREATE-instruktionen för externa data källor.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

Hämta listan över aktuella externa data Källor:

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Externa tabeller

Syntax:

SKAPA en extern tabell [database_name. [schema_name]. | schema_name. ] table_name  
    ({<column_definition>} [,... n]) {med (<rdbms_external_table_options>)}) [;]

<rdbms_external_table_options>:: = DATA_SOURCE = <External_Data_Source>, [SCHEMA_NAME = N "nonescaped_schema_name",] [OBJECT_NAME = N "nonescaped_object_name",]

### <a name="example"></a>Exempel

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

I följande exempel visas hur du hämtar listan över externa tabeller från den aktuella databasen:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Kommentarer

Elastisk fråga utökar den befintliga syntaxen för den externa tabellen för att definiera externa tabeller som använder externa data källor av typen RDBMS. En extern tabell definition för vertikal partitionering omfattar följande aspekter:

* **Schema**: DDL: en för den externa tabellen definierar ett schema som dina frågor kan använda. Det schema som anges i den externa tabell definitionen måste matcha schemat för tabellerna i fjärrdatabasen där faktiska data lagras.
* **Fjärran sluten databas referens**: det externa tabell-DDL: en refererar till en extern data källa. Den externa data källan anger Server namnet och databas namnet för fjärrdatabasen där de faktiska tabell data lagras.

Med hjälp av en extern data källa som beskrivs i föregående avsnitt är syntaxen för att skapa externa tabeller följande:

DATA_SOURCE-satsen definierar den externa data källan (t. ex. fjärrdatabasen vid vertikal partitionering) som används för den externa tabellen.  

SCHEMA_NAME-och OBJECT_NAME-satserna ger möjlighet att mappa den externa tabell definitionen till en tabell i ett annat schema på fjärrdatabasen eller till en tabell med ett annat namn. Detta är användbart om du vill definiera en extern tabell till en katalogvy eller DMV på din fjärrdatabas, eller någon annan situation där fjärrtabellens namn redan används lokalt.  

Följande DDL-instruktion släpper en befintlig extern tabell definition från den lokala katalogen. Den påverkar inte fjärrdatabasen.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

**Behörigheter för att skapa/släppa extern tabell**: ändra behörigheter för externa data källor krävs för extern tabell-DDL, vilket också krävs för att referera till den underliggande data källan.  

## <a name="security-considerations"></a>Säkerhetsöverväganden

Användare med åtkomst till den externa tabellen får automatiskt till gång till de underliggande fjärrtabellerna under de autentiseringsuppgifter som angavs i definitionen för den externa data källan. Du bör noggrant hantera åtkomst till den externa tabellen för att undvika oönskad höjning av privilegier genom den externa data källans autentiseringsuppgifter. Vanliga SQL-behörigheter kan användas för att bevilja eller återkalla åtkomst till en extern tabell precis som om det vore en vanlig tabell.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exempel: fråga lodrätt partitionerade databaser

Följande fråga utför en tre vägs koppling mellan de två lokala tabellerna för beställningar och order rader och fjär tabellen för kunder. Detta är ett exempel på användnings fall för referens data för elastisk fråga:

```sql
    SELECT
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline,
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer
    JOIN orders
    ON c_id = o_c_id
    JOIN  order_line
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Lagrad procedur för fjärran sluten T-SQL-körning: SP \_ execute_remote

Elastiska frågor introducerar också en lagrad procedur som ger direkt åtkomst till fjärrdatabasen. Den lagrade proceduren heter [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) och kan användas för att köra fjärrlagrade procedurer eller T-SQL-kod i fjärrdatabasen. Det tar följande parametrar:

* Data källans namn (nvarchar): namnet på den externa data källan av typen RDBMS.
* Fråga (nvarchar): T-SQL-frågan som ska köras på fjärrdatabasen.
* Parameter deklaration (nvarchar) – valfritt: sträng med data typs definitioner för de parametrar som används i Frågeparametern (till exempel sp_executesql).
* Parameter värde List – valfritt: kommaavgränsad lista över parameter värden (till exempel sp_executesql).

Tjänsten SP \_ execute \_ använder den externa data källan som tillhandahölls i anrops parametrarna för att köra angivet T-SQL-uttryck på fjärrdatabasen. Den externa data källans autentiseringsuppgifter används för att ansluta till fjärrdatabasen.  

Exempel:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Anslutning för verktyg

Du kan använda vanliga SQL Server anslutnings strängar för att ansluta dina BI-och data integrerings verktyg till databaser på den server där elastisk fråga har Aktiver ATS och externa tabeller har definierats. Kontrol lera att SQL Server stöds som data källa för ditt verktyg. Referera sedan till den elastiska fråge databasen och dess externa tabeller precis som andra SQL Server databas som du skulle ansluta till med ditt verktyg.

## <a name="best-practices"></a>Bästa praxis

* Se till att den elastiska frågans slut punkts databas har fått åtkomst till fjärrdatabasen genom att aktivera åtkomst för Azure-tjänster i den Azure SQL Database brand Väggs konfigurationen. Se också till att de autentiseringsuppgifter som anges i definitionen för den externa data källan kan logga in i fjärrdatabasen och har behörighet att komma åt fjärrtabellen.  
* Elastisk fråga fungerar bäst för frågor där merparten av beräkningen kan utföras på fjärrdatabaserna. Du får vanligt vis bästa möjliga frågeresultat med selektiva filter-predikat som kan utvärderas på fjärrdatabaser eller kopplingar som kan utföras helt på fjärrdatabasen. Andra fråge mönster kan behöva läsa in stora mängder data från fjärrdatabasen och kan fungera dåligt.

## <a name="next-steps"></a>Nästa steg

* En översikt över elastisk fråga finns i [Översikt över elastiska frågor](elastic-query-overview.md).
* En lodrät partitionerings guide finns i [komma igång med kors databas fråga (lodrät partitionering)](elastic-query-getting-started-vertical.md).
* En självstudie för horisontell partitionering (horisontell partitionering) finns i [komma igång med elastisk fråga för horisontell partitionering (horisontell partitionering)](elastic-query-getting-started.md).
* För syntax och exempel frågor för vågrätt partitionerade data, se [fråga efter vågrätt partitionerade data)](elastic-query-horizontal-partitioning.md)
* Se [SP \_ execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) för en lagrad procedur som kör ett Transact-SQL-uttryck på en enskild fjärr Azure SQL Database eller uppsättning databaser som fungerar som Shards i ett schema med vågrät partitionering.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->