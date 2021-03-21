---
title: Fråga data lagring med Server lös SQL-pool
description: Den här artikeln beskriver hur du frågar Azure Storage med hjälp av den serverbaserade SQL-adresspoolen i Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: d299afca0bd8070a1da738e02812b64c41a7101c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675054"
---
# <a name="query-storage-files-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Fråga Storage-filer med Server lös SQL-pool i Azure Synapse Analytics

Med Server lös SQL-pool kan du fråga efter data i data Lake. Den innehåller en fråge yta för T-SQL-frågor som hanterar halv strukturerade och ostrukturerade data frågor. För frågor stöds följande T-SQL-aspekter:

- Fullständigt [val](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) av yta, inklusive majoriteten av [SQL Functions och operatorer](overview-features.md).
- Skapa extern tabell som SELECT ([CETAS](develop-tables-cetas.md)) skapar en [extern tabell](develop-tables-external-tables.md) och sedan exporterar parallellt resultatet av en Transact-SQL SELECT-instruktion till Azure Storage.

Mer information om vad är vs. vad som inte stöds för närvarande finns i [översikts](on-demand-workspace-overview.md) artikeln om SQL-poolen utan server eller följande artiklar:
- [Utveckla lagrings åtkomst](develop-storage-files-overview.md) där du kan lära dig hur du använder den [externa tabell](develop-tables-external-tables.md) -och [OpenRowSet](develop-openrowset.md) -funktionen för att läsa data från lagringen.
- [Kontrol lera lagrings åtkomst](develop-storage-files-storage-access-control.md) där du kan lära dig hur du aktiverar Synapse SQL för att få åtkomst till lagring med SAS-autentisering eller arbets ytans hanterade identitet.

## <a name="overview"></a>Översikt

För att ge stöd för en smidig upplevelse för att skicka frågor till data som finns i Azure Storage filer, använder SQL-poolen utan server funktionen [OpenRowSet](develop-openrowset.md) med ytterligare funktioner:

- [Fråga efter flera filer eller mappar](#query-multiple-files-or-folders)
- [PARQUET-filformat](#query-parquet-files)
- [Fråga CSV och avgränsad text (fält avslutning, rad avslutning, escape-tecken)](#query-csv-files)
- [Läs en vald delmängd av kolumner](#read-a-chosen-subset-of-columns)
- [Schema härledning](#schema-inference)
- [funktionen filename](#filename-function)
- [funktionen filsökväg](#filepath-function)
- [Arbeta med komplexa typer och kapslade eller upprepade data strukturer](#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="query-parquet-files"></a>Fråga PARQUET-filer

Om du vill fråga Parquet-källdata använder du FORMAT = ' PARQUET '

```syntaxsql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net//mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Granska artikeln [query Parquet-filer](query-parquet-files.md) för användnings exempel.

## <a name="query-csv-files"></a>Köra frågor mot CSV-filer

Om du vill fråga CSV-källdata använder du FORMAT = CSV. Du kan ange schemat för CSV-filen som en del av `OPENROWSET` funktionen när du frågar CSV-filer:

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.csv', FORMAT = 'CSV', PARSER_VERSION='2.0') 
WITH (C1 int, C2 varchar(20), C3 varchar(max)) as rows
```

Det finns ytterligare alternativ som kan användas för att justera tolknings regler till anpassat CSv-format:
- ESCAPE_CHAR = char anger det tecken i filen som används för att undanta sig själv och alla avgränsare värden i filen. Om Escape-symbolen följs av ett annat värde än sig själv, eller någon av avgränsarna, ignoreras escape-tecken vid läsning av värdet.
Parametern ESCAPE_CHAR tillämpas om FIELDQUOTE är eller inte är aktive rad. Den används inte för att undanta citat tecken. Citat tecken måste föregås av ett annat citat tecken. Citat tecken får bara förekomma i kolumn värden om värdet är kapslat med citat tecken.
- FIELDTERMINATOR = ' field_terminator ' anger vilken fält avslutning som ska användas. Standard fält avslutning är ett kommatecken ("**,**")
- ROWTERMINATOR = ' row_terminator ' anger rad avslutningen som ska användas. Standard rads avgränsaren är ett rad matnings tecken: **\r\n**.

## <a name="file-schema"></a>Filschema

SQL-språket i Synapse SQL gör att du kan definiera schemat för filen som en del av `OPENROWSET` funktionen och läsa alla eller en delmängd av kolumner, eller så försöker den automatiskt avgöra kolumn typer från filen med hjälp av schema härledning.

### <a name="read-a-chosen-subset-of-columns"></a>Läs en vald delmängd av kolumner

Om du vill ange kolumner som du vill läsa kan du ange en valfri WITH-sats i `OPENROWSET` instruktionen.

- Om det finns CSV-datafiler kan du läsa alla kolumner genom att ange kolumn namn och deras data typer. Om du vill använda en delmängd av kolumner använder du ordnings tal för att välja kolumner från de ursprungliga datafilerna enligt ordnings tal. Kolumnerna binds enligt ordnings beteckningen.
- Om det finns Parquet-datafiler, anger du kolumn namn som matchar kolumn namnen i de ursprungliga datafilerna. Kolumnerna kommer att bindas efter namn.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
WITH (
      C1 int, 
      C2 varchar(20),
      C3 varchar(max)
) as rows
```

För varje kolumn måste du ange kolumn namn och typ i- `WITH` satsen.
Exempel finns i [läsa CSV-filer utan att ange alla kolumner](query-single-csv-file.md#return-a-subset-of-columns).

## <a name="schema-inference"></a>Schema härledning

Genom att utelämna satsen WITH från `OPENROWSET` instruktionen kan du instruera tjänsten att automatiskt identifiera (härleda) schemat från underliggande filer.

> [!NOTE]
> Detta fungerar för närvarande endast för PARQUET-filformat.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/mycontainer/mysubfolder/data.parquet', FORMAT = 'PARQUET') 
```

Kontrol lera att [rätt härledda data typer](best-practices-sql-on-demand.md#check-inferred-data-types) används för optimala prestanda. 

## <a name="query-multiple-files-or-folders"></a>Fråga efter flera filer eller mappar

Om du vill köra en T-SQL-fråga över en uppsättning filer i en mapp eller en uppsättning mappar samtidigt som du behandlar dem som en enda enhet eller rad uppsättning, anger du en sökväg till en mapp eller ett mönster (med jokertecken) över en uppsättning filer eller mappar.

Följande regler gäller:

- Mönster kan visas antingen som en del av en katalog Sök väg eller i ett fil namn.
- Flera mönster kan visas i samma katalog steg eller fil namn.
- Om det finns flera jokertecken tas filer i alla matchande sökvägar med i den resulterande fil uppsättningen.

```sql
SELECT * FROM
OPENROWSET( BULK N'https://myaccount.dfs.core.windows.net/myroot/*/mysubfolder/*.parquet', FORMAT = 'PARQUET' ) as rows
```

Se [fråge mappar och flera filer](query-folders-multiple-csv-files.md) för användnings exempel.

## <a name="file-metadata-functions"></a>Funktioner för fil-metadata

### <a name="filename-function"></a>Funktionen filename

Den här funktionen returnerar fil namnet som raden kommer från. 

Om du vill fråga efter vissa filer läser du avsnittet filename i artikeln [query Specific Files](query-specific-files.md#filename) .

Retur data typen är nvarchar (1024). För bästa prestanda måste du alltid omvandla resultatet av fil namns funktionen till rätt datatyp. Om du använder tecken data typ ser du till att lämplig längd används.

### <a name="filepath-function"></a>Funktionen filsökväg

Den här funktionen returnerar en fullständig sökväg eller en del av sökvägen:

- När den anropas utan parameter returnerar den fullständiga fil Sök vägen som en rad kommer från.
- När den anropas med parameter, returnerar den en del av sökvägen som matchar jokertecknet i den position som anges i parametern. Parameter värde 1 skulle till exempel returnera en del av sökvägen som matchar det första jokertecknet.

Mer information finns i avsnittet sökväg i artikeln [query Specific Files](query-specific-files.md#filepath) .

Retur data typen är nvarchar (1024). För bästa prestanda måste du alltid skicka resultatet av funktionen fil Sök väg till rätt datatyp. Om du använder tecken data typ ser du till att lämplig längd används.

## <a name="work-with-complex-types-and-nested-or-repeated-data-structures"></a>Arbeta med komplexa typer och kapslade eller upprepade data strukturer

Om du vill aktivera en smidig upplevelse med data som lagras i kapslade eller upprepade data typer, t. ex. i [Parquet](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#nested-types) -filer, har SQL-poolen utan server lagt till de tillägg som följer.

#### <a name="project-nested-or-repeated-data"></a>Projekt kapslade eller upprepade data

Om du vill projicera data kör du ett SELECT-uttryck över Parquet-filen som innehåller kolumner med kapslade data typer. Vid utdata serialiseras kapslade värden i JSON och returneras som en SQL-datatyp av typen VARCHAR (8000).

```sql
    SELECT * FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Mer detaljerad information finns i avsnittet projekt-kapslade eller upprepade data i artikeln [fråga Parquet kapslade typer](query-parquet-nested-types.md#project-nested-or-repeated-data) .

#### <a name="access-elements-from-nested-columns"></a>Få åtkomst till element från kapslade kolumner

Om du vill komma åt kapslade element från en kapslad kolumn, till exempel struct, använder du "punkt notation" för att sammanfoga fält namn i sökvägen. Ange sökvägen som column_name i WITH-satsen i `OPENROWSET` funktionen.

Exempel på syntaxfel är följande:

```syntaxsql
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    WITH ({'column_name' 'column_type',})
    [AS alias]
    'column_name' ::= '[field_name.] field_name'
```

Som standard `OPENROWSET` matchar funktionen käll fält namn och sökväg med kolumn namnen som anges i With-satsen. Element som finns på olika kapslings nivåer i samma käll Parquet-fil kan nås via WITH-satsen.

**Returvärden**

- Funktionen returnerar ett skalärt värde, till exempel int, decimal och varchar, från det angivna elementet och på den angivna sökvägen för alla Parquet typer som inte finns i den kapslade typ gruppen.
- Om sökvägen pekar på ett element som är av en kapslad typ returnerar funktionen ett JSON-fragment som börjar från det översta elementet på den angivna sökvägen. JSON-fragmentet är av typen VARCHAR (8000).
- Om egenskapen inte kan hittas på den angivna column_name, returnerar funktionen ett fel.
- Om egenskapen inte kan hittas på den angivna column_path, beroende på [Sök vägs läge](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE), returnerar funktionen ett fel när det är i strikt läge eller null i lax-läge.

Läs avsnittet åtkomst element från kapslade kolumner i artikeln [fråga Parquet kapslade typer](query-parquet-nested-types.md#read-properties-from-nested-object-columns) för fråge exempel.

#### <a name="access-elements-from-repeated-columns"></a>Få åtkomst till element från upprepade kolumner

För att få åtkomst till element från en upprepad kolumn, t. ex. ett element i en matris eller karta, använder du funktionen [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?view=azure-sqldw-latest&preserve-view=true) för varje skalärt element som du behöver för att projicera och tillhandahålla:

- Kapslad eller upprepad kolumn som den första parametern
- En [JSON-sökväg](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) som anger det element eller den egenskap som ska kommas åt, som en andra parameter

Om du vill komma åt icke-skalära element från en upprepad kolumn använder du funktionen [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?view=azure-sqldw-latest&preserve-view=true) för varje icke-skalärt element som du behöver för att projicera och tillhandahålla:

- Kapslad eller upprepad kolumn som den första parametern
- En [JSON-sökväg](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true) som anger det element eller den egenskap som ska kommas åt, som en andra parameter

Se syntaxen nedan:

```syntaxsql
    SELECT
       { JSON_VALUE (column_name, path_to_sub_element), }
       { JSON_QUERY (column_name [ , path_to_sub_element ]), )
    FROM
    OPENROWSET
    (   BULK 'unstructured_data_path' ,
        FORMAT = 'PARQUET' )
    [AS alias]
```

Du kan hitta exempel på frågor för att komma åt element från upprepade kolumner i artikeln [fråga Parquet kapslade typer](query-parquet-nested-types.md#access-elements-from-repeated-columns) .

## <a name="query-samples"></a>Exempel på frågor

Du kan lära dig mer om att fråga olika typer av data med hjälp av exempel frågorna.

### <a name="tools"></a>Verktyg

De verktyg du behöver för att utfärda frågor:
    - Azure Synapse Studio 
    - Azure Data Studio
    - SQL Server Management Studio

### <a name="demo-setup"></a>Demo konfiguration

Ditt första steg är att **skapa en databas** där du ska köra frågorna. Sedan initierar du objekten genom att köra [installations skriptet](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) för den databasen. 

Det här installations skriptet skapar data källorna, autentiseringsuppgifterna för databasen och de externa fil formaten som används för att läsa data i dessa exempel.

> [!NOTE]
> Databaser används bara för att visa metadata, inte för faktiska data.  Skriv ned det databas namn du använder, men du behöver det senare.

```sql
CREATE DATABASE mydbname;
```

### <a name="provided-demo-data"></a>Tillhandahållna demonstrations data

Demonstrations data innehåller följande data uppsättningar:

- NYC taxi – gul taxi-resa – del av offentlig NYC-data uppsättning i CSV-och Parquet-format
- Populations data uppsättning i CSV-format
- Testa Parquet-filer med kapslade kolumner
- Böcker i JSON-format

| Mappsökväg                                                  | Beskrivning                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| SKV                                                        | Överordnad mapp för data i CSV-format                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Mappar med populations data filer i olika CSV-format. |
| /csv/taxi/                                                   | Mapp med NYC offentliga datafiler i CSV-format              |
| Parquet                                                    | Överordnad mapp för data i Parquet-format                     |
| /parquet/taxi                                                | NYC offentliga datafiler i Parquet-format, partitionerade efter år och månad med Hive/Hadoop-partitionerings schema. |
| /parquet/nested/                                             | Testa Parquet-filer med kapslade kolumner                     |
| utgör                                                       | Överordnad mapp för data i JSON-format                        |
| /json/books/                                                 | JSON-filer med data från böcker                                   |


## <a name="next-steps"></a>Nästa steg

Mer information om hur du frågar olika filtyper och hur du skapar och använder vyer finns i följande artiklar:

- [Köra frågor mot CSV-filer](query-single-csv-file.md)
- [Efterfråga Parquet-filer](query-parquet-files.md)
- [Efterfråga JSON-filer](query-json-files.md)
- [Fråga efter kapslade värden](query-parquet-nested-types.md)
- [Fråga mappar och flera CSV-filer](query-folders-multiple-csv-files.md)
- [Använda fil-metadata i frågor](query-specific-files.md)
- [Skapa och använda vyer](create-use-views.md)
