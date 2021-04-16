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
ms.openlocfilehash: 0986a1d6a75f0d464eb405841af821c606c68200
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565329"
---
# <a name="use-external-tables-with-synapse-sql"></a>Använda externa tabeller med Synapse SQL

En extern tabell pekar på data som finns i Hadoop, Azure Storage blob eller Azure Data Lake Storage. Externa tabeller används för att läsa data från filer eller skriva data till filer i Azure Storage. Med Synapse SQL kan du använda externa tabeller för att läsa och skriva data till dedikerad SQL-pool eller serverlös SQL-pool.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>Externa tabeller i dedikerad SQL-pool och serverlös SQL-pool

### <a name="dedicated-sql-pool"></a>[Dedikerad SQL-pool](#tab/sql-pool) 

I dedikerad SQL-pool kan du använda en extern tabell för att:

- Fråga Azure Blob Storage Azure Data Lake Gen2 med Transact-SQL-instruktioner.
- Importera och lagra data från Azure Blob Storage och Azure Data Lake Storage till dedikerad SQL-pool.

När den används tillsammans med [CREATE TABLE AS SELECT-instruktionen](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) och väljer från en extern tabell importeras data till en tabell i SQL-poolen. Förutom COPY-instruktionen [är externa](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)tabeller användbara för att läsa in data. 

En självstudiekurs om inläsning finns [i Använda PolyBase för att läsa in data från Azure Blob Storage](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

### <a name="serverless-sql-pool"></a>[Serverlös SQL-pool](#tab/sql-on-demand)

För serverlös SQL-pool använder du en extern tabell för att:

- Fråga efter data Azure Blob Storage eller Azure Data Lake Storage Transact-SQL-uttryck
- Lagra serverlösa SQL-poolfrågeresultat till filer i Azure Blob Storage eller Azure Data Lake Storage med HJÄLP av [CETAS](develop-tables-cetas.md)

Du kan skapa externa tabeller med hjälp av en serverlös SQL-pool med hjälp av följande steg:

1. SKAPA EXTERN DATAKÄLLA
2. CREATE EXTERNAL FILE FORMAT
3. SKAPA EXTERN TABELL

---

### <a name="security"></a>Säkerhet

Användaren måste ha `SELECT` behörighet för extern tabell för att kunna läsa data.
Extern tabell har åtkomst till underliggande Azure-lagring med hjälp av databasomfångsuppgifterna som definierats i datakällan med hjälp av följande regler:
- Datakälla utan autentiseringsuppgifter gör att externa tabeller kan komma åt offentligt tillgängliga filer i Azure Storage.
- Datakällan kan ha autentiseringsuppgifter som gör att externa tabeller endast kan komma åt filer i Azure Storage med hjälp av SAS-token eller hanterad identitet för arbetsytan . Exempel finns i artikeln Utveckla lagringsfiler med [åtkomstkontroll](develop-storage-files-storage-access-control.md#examples) för lagring.

> [!IMPORTANT]
> I en dedikerad SQL-pool gör en datakälla som skapats utan autentiseringsuppgifter att Azure AD-användare kan komma åt lagringsfiler med hjälp av sin Azure AD-identitet. I en serverlös SQL-pool måste du skapa en datakälla med en databasomfattning för autentiseringsuppgifter som har egenskapen `IDENTITY='User Identity'` – se exempel [här](develop-storage-files-storage-access-control.md#examples).

## <a name="create-external-data-source"></a>SKAPA EXTERN DATAKÄLLA

Externa datakällor används för att ansluta till lagringskonton. Den fullständiga dokumentationen beskrivs [här.](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="syntax-for-create-external-data-source"></a>Syntax för CREATE EXTERNAL DATA SOURCE

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

#### <a name="serverless-sql-pool"></a>[Serverlös SQL-pool](#tab/sql-on-demand)

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>Argument för CREATE EXTERNAL DATA SOURCE

data_source_name

Anger det användardefinierade namnet för datakällan. Namnet måste vara unikt i databasen.

#### <a name="location"></a>Location
LOCATION = `'<prefix>://<path>'`   – Tillhandahåller anslutningsprotokollet och sökvägen till den externa datakällan. Följande mönster kan användas på plats:

| Extern datakälla        | Platsprefix | Platssökväg                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` med prefixet kan du använda undermappen i sökvägen.

#### <a name="credential"></a>Autentiseringsuppgift
CREDENTIAL = `<database scoped credential>` är valfria autentiseringsuppgifter som används för att autentisera i Azure Storage. Extern datakälla utan autentiseringsuppgifter kan komma åt det offentliga lagringskontot. 

Externa datakällor utan autentiseringsuppgifter i dedikerad SQL-pool använder anroparens Azure AD-identitet för att komma åt filer i lagringen. En extern datakälla för serverlös SQL-pool med autentiseringsuppgifter använder anroparens  `IDENTITY='User Identity'` Azure AD-identitet för att komma åt filer.
- I dedikerad SQL-pool kan databasomfattningar ange anpassad programidentitet, hanterad identitet för arbetsyta eller SAK-nyckel. 
- I en serverlös SQL-pool kan databasomfångsberoende autentiseringsuppgifter ange anroparens Azure AD-identitet, arbetsytans hanterade identitet eller SAS-nyckel. 

#### <a name="type"></a>TYP
TYPE = `HADOOP` är det obligatoriska alternativet i dedikerad SQL-pool och anger att Polybase-teknik används för att komma åt underliggande filer. Den här parametern kan inte användas i en serverlös SQL-pool som använder inbyggd läsare.

### <a name="example-for-create-external-data-source"></a>Exempel för CREATE EXTERNAL DATA SOURCE (Skapa extern datakälla)

#### <a name="dedicated-sql-pool"></a>[Dedikerad SQL-pool](#tab/sql-pool)

I följande exempel skapas en extern datakälla för Azure Data Lake Gen2 som pekar på New York-datauppsättningen:

```sql
CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

#### <a name="serverless-sql-pool"></a>[Serverlös SQL-pool](#tab/sql-on-demand)

I följande exempel skapas en extern datakälla för Azure Data Lake Gen2 som kan nås med hjälp av SAS-autentiseringsuppgifter:

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

I följande exempel skapas en extern datakälla för Azure Data Lake Gen2 som pekar på den offentligt tillgängliga New York-datauppsättningen:

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Skapar ett externt filformatobjekt som definierar externa data som lagras i Azure Blob Storage eller Azure Data Lake Storage. Att skapa ett externt filformat är en förutsättning för att skapa en extern tabell. Den fullständiga dokumentationen finns [här.](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)

Genom att skapa ett externt filformat anger du den faktiska layouten för de data som en extern tabell refererar till.

### <a name="syntax-for-create-external-file-format"></a>Syntax för CREATE EXTERNAL FILE FORMAT

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

#### <a name="serverless-sql-pool"></a>[Serverlös SQL-pool](#tab/sql-on-demand)

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


### <a name="arguments-for-create-external-file-format"></a>Argument för CREATE EXTERNAL FILE FORMAT

file_format_name- Anger ett namn för det externa filformatet.

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT]- Anger formatet för externa data.

- PARQUET – Anger ett Parquet-format.
- DELIMITEDTEXT – Anger ett textformat med kolumnavgränsare, som även kallas fältavgränsare.

FIELD_TERMINATOR = *field_terminator* – Gäller endast för avgränsade textfiler. Fältavgränsaren anger ett eller flera tecken som markerar slutet av varje fält (kolumn) i den textavgränsade filen. Standardvärdet är pipe-tecknet (ꞌ|ꞌ).

Exempel:

- FIELD_TERMINATOR = "|"
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* – Anger fältavgränsaren för data av typen sträng i den textavgränsade filen. Strängavgränsaren är ett eller flera tecken lång och omges av enkla citattecken. Standardvärdet är den tomma strängen ("").

Exempel:

- STRING_DELIMITER = """
- STRING_DELIMITER = *'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* – Anger radnumret som läses först och gäller för alla filer. Om du anger värdet till två hoppas den första raden i varje fil (rubrikrad) över när data läses in. Rader hoppas över baserat på förekomsten av radterminatorer (/r/n, /r, /n).

USE_TYPE_DEFAULT = { TRUE | **FALSE** } - Anger hur du hanterar saknade värden i avgränsade textfiler när data hämtas från textfilen.

TRUE – Om du hämtar data från textfilen lagrar du varje värde som saknas med hjälp av standardvärdets datatyp för motsvarande kolumn i definitionen av den externa tabellen. Ersätt till exempel ett värde som saknas med:

- 0 om kolumnen definieras som en numerisk kolumn. Decimalkolumner stöds inte och orsakar ett fel.
- Tom sträng ("") om kolumnen är en strängkolumn.
- 1900-01-01 om kolumnen är en datumkolumn.

FALSE – Lagra alla saknade värden som NULL. Alla NULL-värden som lagras med hjälp av ordet NULL i den avgränsade textfilen importeras som strängen "NULL".

Encoding = {'UTF8' | UTF16 – Serverlös SQL-pool kan läsa UTF8- och UTF16-kodade avgränsade textfiler.

DATA_COMPRESSION = *data_compression_method* – Det här argumentet anger datakomprimeringsmetoden för externa data. 

Filformattypen PARQUET stöder följande komprimeringsmetoder:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

Vid läsning från externa PARQUET-tabeller ignoreras det här argumentet, men används när du skriver till externa tabeller med [HJÄLP av CETAS](develop-tables-cetas.md).

Filformattypen DELIMITEDTEXT stöder följande komprimeringsmetod:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = "parser_version" Anger vilken parserversion som ska användas vid läsning av filer. Kontrollera PARSER_VERSION argument i [OPENROWSET-argument för](develop-openrowset.md#arguments) mer information.

### <a name="example-for-create-external-file-format"></a>Exempel för CREATE EXTERNAL FILE FORMAT (SKAPA EXTERNT FILFORMAT)

I följande exempel skapas ett externt filformat för censusfiler:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>SKAPA EXTERN TABELL

Kommandot CREATE EXTERNAL TABLE skapar en extern tabell för att Synapse SQL komma åt data som lagras i Azure Blob Storage eller Azure Data Lake Storage. 

### <a name="syntax-for-create-external-table"></a>Syntax för CREATE EXTERNAL TABLE

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

### <a name="arguments-create-external-table"></a>Argument CREATE EXTERNAL TABLE

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

Namnet på tabellen i en till tre delar som ska skapas. För en extern tabell lagrar serverlös SQL-pool endast tabellmetadata. Inga faktiska data flyttas eller lagras i en serverlös SQL-pool.

<column_definition>, ... *n* ]

CREATE EXTERNAL TABLE stöder möjligheten att konfigurera kolumnnamn, datatyp och sortering. Du kan inte använda DEFAULT CONSTRAINT för externa tabeller.

>[!IMPORTANT]
>Kolumndefinitionerna, inklusive datatyp och antalet kolumner, måste matcha data i de externa filerna. Vid felmatchning avvisas filraderna när du kör frågor mot faktiska data.

När du läser från Parquet-filer kan du bara ange de kolumner som du vill läsa och hoppa över resten.

LOCATION =*'folder_or_filepath'*

Anger mappen eller sökvägen och filnamnet för faktiska data i Azure Blob Storage. Platsen börjar från rotmappen. Rotmappen är den dataplats som anges i den externa datakällan.

Om du anger en plats för mappen, kommer en serverlös SQL-poolfråga att välja från den externa tabellen och hämta filer från mappen.

> [!NOTE]
> Till skillnad från Hadoop och PolyBase returnerar inte serverlös SQL-pool undermappar om du inte anger /** i slutet av sökvägen. Precis som Hadoop och PolyBase returnerar den inte filer som filnamnet börjar med en understrykning (_) eller en punkt (.).

I det här exemplet returnerar om LOCATION='/webdata/', en serverlös SQL-poolfråga, rader från mydata.txt. Den returnerar inte mydata2.txt och mydata3.txt eftersom de finns i en undermapp.

![Rekursiva data för externa tabeller](./media/develop-tables-external-tables/folder-traversal.png)

DATA_SOURCE = *external_data_source_name* – Anger namnet på den externa datakälla som innehåller platsen för externa data. Om du vill skapa en extern datakälla använder [du CREATE EXTERNAL DATA SOURCE](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* – Anger namnet på det externa filformatsobjekt som lagrar filtypen och komprimeringsmetoden för externa data. Om du vill skapa ett externt filformat använder [du CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

### <a name="permissions-create-external-table"></a>Behörigheter CREATE EXTERNAL TABLE

Om du vill välja från en extern tabell behöver du rätt autentiseringsuppgifter med list- och läsbehörigheter.

### <a name="example-create-external-table"></a>Exempel på CREATE EXTERNAL TABLE

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

Med hjälp av Data Lake-utforskningsfunktioner kan du nu skapa och fråga en extern tabell med dedikerad SQL-pool eller serverlös SQL-pool med ett enkelt högerklick på filen. Gesten med ett klick för att skapa externa tabeller ADLS Gen2 lagringskontot stöds endast för Parquet-filer. 

### <a name="prerequisites"></a>Förutsättningar

- Du måste ha åtkomst till arbetsytan med minst ARM-åtkomstrollen Storage Blob Data-deltagare till ADLS Gen2 konto

- Du måste minst ha behörighet [att skapa och fråga](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true) externa tabeller i SQL-poolen eller SQL OD

Från panelen Data väljer du den fil som du vill skapa den externa tabellen från:
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

Ett dialogfönster öppnas. Välj dedikerad SQL-pool eller serverlös SQL-pool, ge tabellen ett namn och välj öppna skript:

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

SQL-skriptet skapas automatiskt och här framtar schemat från filen:
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

Kör skriptet. Skriptet kör automatiskt ett Select Top 100 *.:
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

Den externa tabellen skapas nu. För framtida utforskning av innehållet i den externa tabellen kan användaren fråga den direkt från fönstret Data:
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>Nästa steg

I [CETAS-artikeln](develop-tables-cetas.md) finns information om hur du sparar frågeresultaten i en extern tabell i Azure Storage. Eller så kan du börja fråga [Apache Spark för Azure Synapse externa tabeller](develop-storage-files-spark-tables.md).
