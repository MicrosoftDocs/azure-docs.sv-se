---
title: Utforma ELT i stället för ETL
description: Implementera flexibla data inläsningsstrategier för dedikerade SQL-pooler i Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 8a8f857dcfdc271a3aaad71f4b9c26d474033383
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566111"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Data inläsningsstrategier för dedikerad SQL-pool i Azure Synapse Analytics

Traditionella SMP-dedikerade SQL-pooler använder en process för extrahering, transformering och inläsning (ETL) för inläsning av data. Synapse SQL i Azure Synapse Analytics använder distribuerad frågebearbetningsarkitektur som drar nytta av skalbarheten och flexibiliteten hos beräknings- och lagringsresurser.

Med hjälp av en ELT-process (extrahering, inläsning och transformering) används inbyggda funktioner för distribuerad frågebearbetning och eliminerar de resurser som krävs för datatransformering före inläsning.

Dedikerade SQL-pooler stöder många inläsningsmetoder, inklusive populära SQL Server-alternativ som [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) och [SQLBulkCopy API.](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)Det snabbaste och mest skalbara sättet att läsa in data är via externa PolyBase-tabeller och [COPY-instruktionen](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Med PolyBase och COPY-instruktionen kan du komma åt externa data som lagras i Azure Blob Storage eller Azure Data Lake Store via T-SQL-språket. För största flexibilitet vid inläsning rekommenderar vi att du använder COPY-instruktionen.


## <a name="what-is-elt"></a>Vad är ELT?

Extrahering, inläsning och transformering (ELT) är en process genom vilken data extraheras från ett källsystem, läses in i en dedikerad SQL-pool och sedan transformeras.

De grundläggande stegen för att implementera ELT är:

1. Extrahera källdata till textfiler.
2. Landa data till Azure Blob Storage eller Azure Data Lake Store.
3. Förbered data för inläsning.
4. Läs in data i mellanlagringstabeller med PolyBase eller kommandot COPY.
5. Transformera data.
6. Infoga data i produktionstabeller.

En självstudiekurs om inläsning finns [i Läsa in data från Azure Blob Storage.](./load-data-from-azure-blob-storage-using-copy.md)

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahera källdata till textfiler

Att hämta data från källsystemet beror på lagringsplatsen. Målet är att flytta data till avgränsad text eller CSV-filer som stöds.

### <a name="supported-file-formats"></a>Filformat som stöds

Med PolyBase och COPY-instruktionen kan du läsa in data från UTF-8 och UTF-16-kodad avgränsad text eller CSV-filer. Förutom avgränsad text eller CSV-filer läses den in från Hadoop-filformat som ORC och Parquet. PolyBase och COPY-instruktionen kan också läsa in data från komprimerade Gzip- och Snappy-filer.

Utökat ASCII-format, format med fast bredd och kapslade format som WinZip eller XML stöds inte. Om du exporterar från SQL Server kan du använda [kommandoradsverktyget bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att exportera data till avgränsade textfiler.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Landa data i Azure Blob Storage eller Azure Data Lake Store

Om du vill landa data i Azure Storage kan du flytta dem till [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eller Azure Data Lake Store [Gen2.](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) På båda platserna ska data lagras i textfiler. PolyBase och COPY-instruktionen kan läsas in från någon av platserna.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tjänst förbättrar nätverkets dataflöde, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar dina data via en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga Internet. Anslutningarna ger bättre tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via offentligt Internet.
- [AZCopy-verktyget](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) flyttar data Azure Storage över det offentliga Internet. Detta fungerar om dina datastorlekar är mindre än 10 TB. Om du regelbundet vill utföra belastningar med AZCopy testar du nätverkshastigheten för att se om det är acceptabelt.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) har en gateway som du kan installera på din lokala server. Sedan kan du skapa en pipeline för att flytta data från din lokala server upp till Azure Storage. Information om hur Data Factory med dedikerade SQL-pooler finns i [Läsa in data för dedikerade SQL-pooler.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Förbered data för inläsning

Du kan behöva förbereda och rensa data i ditt lagringskonto innan du läser in dem. Förberedelse av data kan utföras när dina data finns i källan, när du exporterar data till textfiler eller när data finns i Azure Storage.  Det är enklast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-the-tables"></a>Definiera tabellerna

Du måste först definiera de tabeller som du läser in till i din dedikerade SQL-pool när du använder COPY-instruktionen.

Om du använder PolyBase måste du definiera externa tabeller i din dedikerade SQL-pool innan du läser in. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. En extern tabell liknar en databasvy. Den externa tabellen innehåller tabellschemat och pekar på data som lagras utanför den dedikerade SQL-poolen.

Att definiera externa tabeller innebär att ange datakällan, formatet för textfilerna och tabelldefinitionerna. De referensartiklar för T-SQL-syntax som du behöver är:

- [SKAPA EXTERN DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Använd följande mappning av SQL-datatyper vid inläsning av Parquet-filer:

|                         Parquet-typ                         |   Logisk parquet-typ (anteckning)   |  SQL-datatyp   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           Boolean                            |                                       |       bit        |
|                     BINÄR/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      flyt       |
|                            Flyta                             |                                       |       real       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINARY                            |                 UTF8                  |     nvarchar     |
|                            BINARY                            |                Sträng                 |     nvarchar     |
|                            BINARY                            |                 Enum                  |     nvarchar     |
|                            BINARY                            |                 Uuid                  | uniqueidentifier |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               Intervall                |  varchar(max),   |
|                            INT32                             |             INT(8, true)              |     smallint     |
|                            INT32                             |            INT(16, true)            |     smallint     |
|                            INT32                             |             INT(32, true)             |       int        |
|                            INT32                             |            INT(8, false)            |     tinyint      |
|                            INT32                             |            INT(16, false)             |       int        |
|                            INT32                             |           INT(32, false)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TIME (MILLE)             |       time       |
|                            INT64                             |            INT(64, true)            |      bigint      |
|                            INT64                             |           INT(64, false )            |  decimal(20,0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TIME (MILLE)                 |       time       |
|                            INT64                             | TIDSSTÄMPEL (AMP)                  |    datetime2     |
| [Komplex typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 Lista                  |   varchar(max)   |
| [Komplex typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  Karta                  |   varchar(max)   |

>[!IMPORTANT] 
>- SQL-dedikerade pooler stöder för närvarande inte Parquet-datatyper med MICROS- och NANOS-precision. 
>- Följande fel kan uppstå om typerna är felmatchade mellan Parquet och SQL eller om du har Parquet-datatyper som inte stöds: **"HdfsBridge::recordReaderFillBuffer – Oväntat** fel vid fyllning av postläsarbuffert: ClassCastException: ..."
>- Det går inte att läsa in ett värde utanför intervallet 0–127 i en tinyint-kolumn för Filformatet Parquet och ORC.

Ett exempel på hur du skapar externa objekt finns i [Skapa externa tabeller.](../sql/develop-tables-external-tables.md?tabs=sql-pool)

### <a name="format-text-files"></a>Formatera textfiler

Om du använder PolyBase måste de externa objekt som definierats justera raderna i textfilerna med den externa tabellen och filformatdefinitionen. Data i varje rad i textfilen måste justeras med tabelldefinitionen.
Så här formaterar du textfilerna:

- Om dina data kommer från en icke-relationell källa måste du transformera dem till rader och kolumner. Oavsett om data kommer från en relationskälla eller icke-relationell källa måste data omvandlas för att justeras med kolumndefinitionerna för den tabell som du planerar att läsa in data i.
- Formatera data i textfilen så att de överensstämmer med kolumnerna och datatyperna i måltabellen. Justeringsfel mellan datatyper i de externa textfilerna och den dedikerade SQL-pooltabellen gör att rader avvisas under inläsningen.
- Avgränsa fält i textfilen med en avgränsare.  Se till att använda ett tecken eller en teckensekvens som inte finns i dina källdata. Använd den avslutande som du angav med [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Läs in data med PolyBase eller COPY-instruktionen

Det är bästa praxis att läsa in data i en mellanlagringstabell. Med mellanlagringstabeller kan du hantera fel utan att störa produktionstabellerna. En mellanlagringstabell ger dig också möjlighet att använda den dedikerade arkitekturen för parallell bearbetning av SQL-pooler för datatransformationer innan du infogar data i produktionstabeller.

### <a name="options-for-loading"></a>Alternativ för inläsning

Om du vill läsa in data kan du använda något av följande inläsningsalternativ:

- [COPY-instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) är det rekommenderade inläsningsverktyget eftersom det gör att du kan läsa in data sömlöst och flexibelt. -instruktionen har många ytterligare inläsningsfunktioner som PolyBase inte tillhandahåller. 
- [PolyBase med T-SQL](./load-data-from-azure-blob-storage-using-copy.md) kräver att du definierar externa dataobjekt.
- [PolyBase- och COPY-instruktion med Azure Data Factory (ADF) är](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) ett annat orkestreringsverktyg.  Den definierar en pipeline och schemalägger jobb.
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) fungerar bra när dina källdata finns SQL Server. SSIS definierar käll-till-måltabellmappningar och orkestrering av belastningen. Om du redan har SSIS-paket kan du ändra paketen så att de fungerar med det nya informationslagermålet.
- [PolyBase med Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) överför data från en tabell till en Databricks-dataram och/eller skriver data från en Databricks-dataram till en tabell med PolyBase.

### <a name="other-loading-options"></a>Andra inläsningsalternativ

Förutom PolyBase och COPY-instruktionen kan du använda [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) eller [SQLBulkCopy API.](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) bcp läser in direkt till databasen utan att gå via Azure Blob Storage och är endast avsett för små belastningar.

> [!NOTE]
> Inläsningsprestandan för dessa alternativ är långsammare än PolyBase och COPY-instruktionen.

## <a name="5-transform-the-data"></a>5. Transformera data

När data finns i mellanlagringstabellen utför du transformningar som din arbetsbelastning kräver. Flytta sedan data till en produktionstabell.

## <a name="6-insert-the-data-into-production-tables"></a>6. Infoga data i produktionstabeller

INFOGA I ... SELECT-instruktionen flyttar data från mellanlagringstabellen till den permanenta tabellen.

När du utformar en ETL-process kan du prova att köra processen på ett litet testexempel. Prova att extrahera 1 000 rader från tabellen till en fil, flytta den till Azure och prova sedan att läsa in den i en mellanlagringstabell.

## <a name="partner-loading-solutions"></a>Inläsningslösningar för partner

Många av våra partner har inläsningslösningar. Mer information finns i en lista över våra [lösningspartner.](sql-data-warehouse-partner-business-intelligence.md)

## <a name="next-steps"></a>Nästa steg

Hjälp med inläsningen hittar du i [Vägledning för att läsa in data](guidance-for-loading-data.md).