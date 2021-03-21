---
title: Skapa extern tabell som SELECT (CETAS) i Synapse SQL
description: Använda skapa extern tabell som SELECT (CETAS) med Synapse SQL
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 23b74c06ebd45092b9efe36a870eeac7f6fb828a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677568"
---
# <a name="cetas-with-synapse-sql"></a>CETAS med Synapse SQL

Du kan använda skapa extern tabell som SELECT (CETAS) i en dedikerad SQL-pool eller Server lös SQL-pool för att utföra följande uppgifter:  

- Skapa en extern tabell
- Exportera, parallellt, resultatet av ett Transact-SQL SELECT-uttryck för att:

  - Hadoop
  - Azure Storage Blob
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-dedicated-sql-pool"></a>CETAS i dedikerad SQL-pool

För dedikerad SQL-pool, CETAS-användning och syntax, markerar du artikeln [skapa extern tabell som Välj](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) . Mer information om CTAS med hjälp av dedikerad SQL-pool finns i artikeln [CREATE TABLE som Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) .

## <a name="cetas-in-serverless-sql-pool"></a>CETAS i SQL-pool utan Server

När du använder en server lös SQL-pool, används CETAS för att skapa en extern tabell och exportera frågeresultaten till Azure Storage Blob eller Azure Data Lake Storage Gen2.

## <a name="syntax"></a>Syntax

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>Argument

*[[ *database_name* . [ *schema_name* ]. ] | *schema_name* . ] *table_name**

Namnet på en till tre delar av tabellen som ska skapas. För en extern tabell lagrar SQL-poolen utan server endast tabellens metadata. Inga faktiska data flyttas eller lagras i SQL-poolen utan server.

PLATS = *path_to_folder*

Anger var resultatet av SELECT-instruktionen ska skrivas på den externa data källan. Rotmappen är den dataplats som anges i den externa datakällan. PLATSEN måste peka på en mapp och ha ett avslutande/. Exempel: aggregated_data/

DATA_SOURCE = *external_data_source_name*

Anger namnet på det externa data käll objekt som innehåller den plats där de externa data ska lagras. Om du vill skapa en extern data källa använder du [skapa en extern data källa (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source).

FILE_FORMAT = *external_file_format_name*

Anger namnet på det externa fil formats objekt som innehåller formatet för den externa data filen. Om du vill skapa ett externt fil format använder du [Skapa externt fil format (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format). Endast externa fil format med FORMAT_TYPE = PARQUET och FORMAT_TYPE = DELIMITEDTEXT stöds för närvarande. GZip-komprimering för DELIMITEDTEXT-format stöds inte.

MED *<common_table_expression>*

Anger en tillfällig namngiven resultat uppsättning, kallat ett gemensamt tabell uttryck (common Table EXPRESSIONS). Mer information finns i [med common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true).

Välj <select_criteria>

Fyller den nya tabellen med resultaten från en SELECT-instruktion. *select_criteria* är bröd texten i SELECT-instruktionen som avgör vilka data som ska kopieras till den nya tabellen. Information om SELECT-instruktioner finns i [Select (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true).

> [!NOTE]
> ORDER BY-satsen i SELECT stöds inte för CETAS.

## <a name="permissions"></a>Behörigheter

Du måste ha behörighet att lista innehållet i mappen och skriva till mappen LOCATION för att CETAS ska fungera.

## <a name="examples"></a>Exempel

I de här exemplen används CETAS för att spara total population som sammanställs per år och stat till en aggregated_data mapp som finns i population_ds data källan.

Det här exemplet använder sig av autentiseringsuppgifter, data källa och externt fil format som skapats tidigare. Referera till det [externa tabell](develop-tables-external-tables.md) dokumentet. Om du vill spara frågeresultaten till en annan mapp i samma data källa ändrar du plats argumentet. 

Om du vill spara resultatet i ett annat lagrings konto skapar du och använder en annan data källa för DATA_SOURCE argument.

> [!NOTE]
> I exemplen som följer används ett offentligt Azure Open data Storage-konto. Den är skrivskyddad. Om du vill köra dessa frågor måste du ange data källan som du har Skriv behörighet för.

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

I följande exempel används en extern tabell som källa för CETAS. Den förlitar sig på autentiseringsuppgifter, data källa, externt fil format och extern tabell som skapats tidigare. Referera till det [externa tabell](develop-tables-external-tables.md) dokumentet.

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>Datatyper som stöds

CETAS kan användas för att lagra resultat uppsättningar med följande SQL-data typer:

- binary
- varbinary
- char
- varchar
- nchar
- nvarchar
- smalldate
- date
- datetime
- datetime2
- datetimeoffset
- time
- decimal
- numeric
- flyt
- real
- bigint
- tinyint
- smallint
- int
- bigint
- bit
- money
- smallmoney
- uniqueidentifier

> [!NOTE]
> LOBs större än 1 MB kan inte användas med CETAS.

## <a name="next-steps"></a>Nästa steg

Försök att köra frågor [mot Apache Spark för externa Azure Synapse-tabeller](develop-storage-files-spark-tables.md).
