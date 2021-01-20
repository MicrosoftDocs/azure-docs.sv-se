---
title: Använda externa tabeller med Synapse SQL
description: Läsa eller skriva datafiler med Synapse SQL
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 0fbf5c07b2d8fd5d754a46484d6b072cc9b682d6
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599844"
---
# <a name="use-external-tables-with-synapse-sql"></a>Använda externa tabeller med Synapse SQL

En extern tabell pekar på data som finns i Hadoop, Azure Storage BLOB eller Azure Data Lake Storage. Externa tabeller används för att läsa data från filer eller skriva data till filer i Azure Storage. Med Synapse SQL kan du använda externa tabeller för att läsa och skriva data till dedikerad SQL-pool eller SQL-pool utan server.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Externa tabeller i dedikerad SQL-pool och Server lös SQL-pool

### <a name="dedicated-sql-pool"></a>[Dedikerad SQL-pool](#tab/sql-pool) 

I dedikerad SQL-pool kan du använda en extern tabell för att:

- Fråga Azure Blob Storage och Azure Data Lake Gen2 med Transact-SQL-uttryck.
- Importera och lagra data från Azure Blob Storage och Azure Data Lake Storage till dedikerad SQL-pool.

När det används tillsammans med [CREATE TABLE som Select](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) -instruktion, importerar data till en tabell i SQL-poolen genom att välja från en extern tabell. Förutom [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)är externa tabeller användbara för att läsa in data. 

En inläsnings kurs finns i [använda PolyBase för att läsa in data från Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

### <a name="serverless-sql-pool"></a>[SQL-pool utan Server](#tab/sql-on-demand)

För en server utan SQL-pool använder du en extern tabell för att:

- Fråga efter data i Azure Blob Storage eller Azure Data Lake Storage med Transact-SQL-uttryck
- Lagra frågeresultat i SQL-pooler i Azure Blob Storage eller Azure Data Lake Storage med hjälp av [CETAS](develop-tables-cetas.md)

Du kan skapa externa tabeller med en server lös SQL-pool via följande steg:

1. SKAPA EXTERN DATA KÄLLA
2. CREATE EXTERNAL FILE FORMAT
3. SKAPA EXTERN TABELL

---

### <a name="security"></a>Säkerhet

Användaren måste ha `SELECT` behörighet för den externa tabellen för att kunna läsa data.
Extern tabell åtkomst till underliggande Azure-lagring med hjälp av databasen begränsade autentiseringsuppgifter som definierats i data källan med hjälp av följande regler:
- Data källan utan autentiseringsuppgifter gör det möjligt för externa tabeller att komma åt offentligt tillgängliga filer i Azure Storage.
- Data källan kan ha autentiseringsuppgifter som gör det möjligt för externa tabeller att enbart komma åt filerna på Azure Storage med SAS-token eller arbets ytans hanterade identitet – exempel finns i artikeln [utveckla lagrings fil åtkomst kontroll](develop-storage-files-storage-access-control.md#examples) .

> [!IMPORTANT]
> I en dedikerad SQL-pool gör en data källa som skapats utan autentiseringsuppgifter att Azure AD-användare kan komma åt lagringsfiler med sin Azure AD-identitet. I SQL-poolen utan server måste du skapa en data källa med en databas som har en databas som innehåller `IDENTITY='User Identity'` Egenskaper – se [exempel här](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>SKAPA EXTERN DATA KÄLLA

Externa data källor används för att ansluta till lagrings konton. Den fullständiga dokumentationen beskrivs [här](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="syntax-for-create-external-data-source"></a>Syntax för skapa extern DATA källa

#### <a name="dedicated-sql-pool"></a>[Dedikerad SQL-pool](#tab/sql-pool)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="serverless-sql-pool"></a>[SQL-pool utan Server](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argument för skapa extern DATA källa

data_source_name

Anger det användardefinierade namnet för data källan. Namnet måste vara unikt i databasen.

#### <a name="location"></a>Plats
LOCATION = `'<prefix>://<path>'`   -tillhandahåller anslutnings protokollet och sökvägen till den externa data källan. Följande mönster kan användas på platsen:

| Extern data Källa        | Location-prefix | Sökväg till plats                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` prefix gör att du kan använda undermappen i sökvägen.

#### <a name="credential"></a>Autentiseringsuppgift
CREDENTIAL = `<database scoped credential>` är valfri autentiseringsuppgift som ska användas för att autentisera i Azure Storage. Extern data källa utan autentiseringsuppgifter kan komma åt offentligt lagrings konto. 

Externa data källor utan autentiseringsuppgifter i den dedikerade SQL-poolen kommer att använda anroparens Azure AD-identitet för att komma åt filer på lagrings platsen. En extern data källa för en server lös SQL-pool med autentiseringsuppgifter  `IDENTITY='User Identity'` kommer att använda anroparens Azure AD-identitet för att komma åt filer.
- I dedikerad SQL-pool kan databas omfattnings behörighet ange anpassad program identitet, hanterad identitet för arbets yta eller SAK nyckel. 
- I SQL-poolen utan server kan databasens begränsade autentiseringsuppgifter ange anroparens Azure AD-identitet, arbets ytans hanterade identitet eller SAS-nyckel. 

#### <a name="type"></a>TYP
TYPE = `HADOOP` är det obligatoriska alternativet i en dedikerad SQL-pool och anger att PolyBase-tekniken används för att komma åt underliggande filer. Den här parametern kan inte användas i en SQL-pool utan server som använder inbyggd inbyggd läsare.

### <a name="example-for-create-external-data-source"></a>Exempel på Skapa extern DATA källa

#### <a name="dedicated-sql-pool"></a>[Dedikerad SQL-pool](#tab/sql-pool)

I följande exempel skapas en extern data källa för Azure Data Lake Gen2 som pekar på data uppsättningen New York:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="serverless-sql-pool"></a>[SQL-pool utan Server](#tab/sql-on-demand)

I följande exempel skapas en extern data källa för Azure Data Lake Gen2 som kan nås med SAS-autentiseringsuppgifter:

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

I följande exempel skapas en extern data källa för Azure Data Lake Gen2 som pekar på den offentligt tillgängliga New York-data uppsättningen:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Skapar ett externt fil formats objekt som definierar externa data som lagras i Azure Blob Storage eller Azure Data Lake Storage. Att skapa ett externt fil format är ett krav för att skapa en extern tabell. Den fullständiga dokumentationen finns [här](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Genom att skapa ett externt fil format anger du den faktiska layouten för de data som en extern tabell refererar till.

### <a name="syntax-for-create-external-file-format"></a>Syntax för skapa externt fil FORMAT

#### <a name="sql-pool"></a>[SQL-pool](#tab/sql-pool)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
}
```

#### <a name="serverless-sql-pool"></a>[SQL-pool utan Server](#tab/sql-on-demand)

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

---


### <a name="arguments-for-create-external-file-format"></a>Argument för skapa externt fil FORMAT

file_format_name-anger ett namn för det externa fil formatet.

FORMAT_TYPE = [PARQUET | DELIMITEDTEXT] – anger formatet för externa data.

- PARQUET – anger ett Parquet-format.
- DELIMITEDTEXT – anger ett text format med kolumn avgränsare, vilket även kallas avslutare för fält.

FIELD_TERMINATOR = *field_terminator* -gäller endast för avgränsade textfiler. Fält begränsningen anger ett eller flera tecken som markerar slutet på varje fält (kolumn) i den text avgränsade filen. Standardvärdet är pipe-tecknet (ꞌ | ꞌ).

Exempel:

- FIELD_TERMINATOR = |
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ \ t ꞌ

STRING_DELIMITER = *string_delimiter* -anger fält begränsningen för data av typen sträng i den text avgränsade filen. Sträng avgränsaren är ett eller flera tecken i längd och omges med enkla citat tecken. Standardvärdet är den tomma strängen ("").

Exempel:

- STRING_DELIMITER = "" "
- STRING_DELIMITER = ' * '
- STRING_DELIMITER = ꞌ, ꞌ

FIRST_ROW = *First_row_int* -anger rad numret som läses först och gäller för alla filer. Om du anger värdet till två kommer den första raden i varje fil (rubrik rad) att hoppas över när data läses in. Rader hoppas över baserat på förekomsten av rad avbrott (/r/n,/r,/n).

USE_TYPE_DEFAULT = {TRUE | **Falskt** } -Anger hur värden som saknas ska hanteras i avgränsade textfiler när data hämtas från text filen.

SANT – om du hämtar data från text filen lagrar du varje värde som saknas genom att använda Standardvärdets datatyp för motsvarande kolumn i den externa tabell definitionen. Ersätt till exempel ett värde som saknas med:

- 0 om kolumnen definieras som en numerisk kolumn. Decimal kolumner stöds inte och resulterar i fel.
- Tom sträng ("") om kolumnen är en sträng kolumn.
- 1900-01-01 om kolumnen är en datum kolumn.

FALSe – lagra alla värden som saknas som NULL. Alla NULL-värden som lagras med hjälp av ordet NULL i den avgränsade text filen importeras som strängen NULL.

Encoding = {' UTF8 ' | ' UTF16 '}-SQL-poolen utan server kan läsa UTF8 och UTF16-kodade avgränsade textfiler.

DATA_COMPRESSION = *data_compression_method* – det här argumentet anger data komprimerings metoden för externa data. 

Fil formats typen PARQUET stöder följande komprimerings metoder:

- DATA_COMPRESSION = ' org. apache. Hadoop. io. compress. GzipCodec '
- DATA_COMPRESSION = ' org. apache. Hadoop. io. compress. SnappyCodec '

När du läser från PARQUET externa tabeller, ignoreras det här argumentet, men används vid skrivning till externa tabeller med [CETAS](develop-tables-cetas.md).

Fil formats typen DELIMITEDTEXT stöder följande komprimerings metod:

- DATA_COMPRESSION = ' org. apache. Hadoop. io. compress. GzipCodec '

PARSER_VERSION = parser_version anger vilken parser-version som ska användas vid läsning av filer. Se PARSER_VERSION argument i [OpenRowSet-argument](develop-openrowset.md#arguments) för mer information.

### <a name="example-for-create-external-file-format"></a>Exempel på Skapa externt fil FORMAT

I följande exempel skapas ett externt fil format för inventerings filer:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>SKAPA EXTERN TABELL

Kommandot Skapa extern tabell skapar en extern tabell för Synapse-SQL för att komma åt data som lagras i Azure Blob Storage eller Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Syntax för skapa extern tabell

```sql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

### <a name="arguments-create-external-table"></a>Argument skapa extern tabell

*{database_name. schema_name. table_name | schema_name. table_name | table_name}*

Namnet på en till tre delar av tabellen som ska skapas. För en extern tabell lagrar SQL-poolen utan server endast tabellens metadata. Inga faktiska data flyttas eller lagras i SQL-poolen utan server.

<column_definition>,... *n* ]

Skapa extern tabell stöder möjligheten att konfigurera kolumn namn, datatyp, null och sortering. Du kan inte använda standard BEGRÄNSNINGen på externa tabeller.

>[!IMPORTANT]
>Kolumndefinitionerna, inklusive datatyp och antalet kolumner, måste matcha data i de externa filerna. Vid felmatchning avvisas filraderna när du kör frågor mot faktiska data.

När du läser från Parquet-filer kan du bara ange de kolumner som du vill läsa och hoppa över resten.

PLATS =*folder_or_filepath*

Anger mappen eller sökvägen och fil namnet för faktiska data i Azure Blob Storage. Platsen börjar från rotmappen. Rotmappen är den dataplats som anges i den externa datakällan.

Om du anger en mapplats, kommer en server lös SQL-pool-fråga att välja från den externa tabellen och hämta filer från mappen.

> [!NOTE]
> Till skillnad från Hadoop och PolyBase returnerar inte Server lös SQL-poolen undermappar. Den returnerar filer för vilka fil namnet börjar med en understrykning (_) eller en punkt (.).

I det här exemplet returneras rader från mydata.txt och _hidden.txt om plats = '/webdata/', en server lös SQL-pool fråga. Den returnerar inte mydata2.txt och mydata3.txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* – anger namnet på den externa data källa som innehåller platsen för externa data. Om du vill skapa en extern data källa använder du [skapa extern data källa](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* – anger namnet på det externa fil formats objekt som lagrar filtypen och komprimerings metoden för externa data. Om du vill skapa ett externt fil format använder du [Skapa externt fil format](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Behörigheter för att skapa extern tabell

Om du vill välja från en extern tabell måste du ha rätt autentiseringsuppgifter med list-och Läs behörighet.

### <a name="example-create-external-table"></a>Exempel på Skapa extern tabell

I följande exempel skapas en extern tabell. Den returnerar den första raden:

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Skapa och fråga externa tabeller från en fil i Azure Data Lake

Med hjälp av Data Lake utforsknings funktioner kan du nu skapa och skicka frågor till en extern tabell med hjälp av en dedikerad SQL-pool eller en server lös SQL-pool med enkel högerklickning på filen. En enkel klickning för att skapa externa tabeller från ADLS Gen2 lagrings kontot stöds bara för Parquet-filer. 

### <a name="prerequisites"></a>Förutsättningar

- Du måste ha åtkomst till arbets ytan med minst Storage BLOB data Contributor-åtkomst roll till ADLS Gen2 kontot

- Du måste ha minst [behörighet för att skapa](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#permissions-2&preserve-view=true) och fråga externa tabeller i SQL-poolen eller SQL OD

I panelen data väljer du den fil som du vill skapa den externa tabellen från:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Ett dialog fönster öppnas. Välj en dedikerad SQL-pool eller Server lös SQL-pool, namnge tabellen och välj Öppna skript:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

SQL-skriptet genererar automatiskt schemat från filen:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Kör skriptet. Skriptet kommer automatiskt att köra en SELECT Top 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Den externa tabellen skapas nu, för framtida utforskning av innehållet i den externa tabellen som användaren kan skicka frågor till direkt från fönstret data:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Nästa steg

Se artikeln om [CETAS](develop-tables-cetas.md) för att spara frågeresultaten till en extern tabell i Azure Storage. Du kan också börja fråga [Apache Spark för externa Azure Synapse-tabeller](develop-storage-files-spark-tables.md).
