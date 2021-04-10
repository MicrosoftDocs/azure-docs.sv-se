---
title: Utforma ELT i stället för ETL
description: Implementera flexibla data inläsnings strategier för dedikerade SQL-pooler i Azure Synapse Analytics.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 04bd4767445f9378aedb303e63bf463f44e40034
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602204"
---
# <a name="data-loading-strategies-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Data inläsnings strategier för dedikerad SQL-pool i Azure Synapse Analytics

Traditionella SMP-dedikerade SQL-pooler använder ETL-process (Extract, Transform och Load) för att läsa in data. Synapse SQL, i Azure Synapse Analytics, använder arkitektur för distribuerad bearbetning av processer som drar nytta av skalbarheten och flexibiliteten i beräknings-och lagrings resurser.

Genom att använda en process för extrahering, inläsning och transformering (ELT) används inbyggda funktioner för distribuerad frågekörning och eliminerar de resurser som krävs för dataomvandling innan inläsningen.

Även om dedikerade SQL-pooler stöder många inläsnings metoder, inklusive populära SQL Server alternativ som [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) och [SqlBulkCopy-API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), är det snabbaste och mest skalbara sättet att läsa in data via PolyBase-externa tabeller och [kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

Med PolyBase och KOPIERINGs instruktionen kan du komma åt externa data som lagras i Azure Blob Storage eller Azure Data Lake Store via T-SQL-språket. Vi rekommenderar att du använder COPY-instruktionen för största möjliga flexibilitet vid inläsning.


## <a name="what-is-elt"></a>Vad är ELT?

Extrahera, läsa in och transformera (ELT) är en process genom vilken data extraheras från ett käll system, läses in i en dedikerad SQL-pool och omvandlas sedan.

De grundläggande stegen för att implementera ELT är:

1. Extrahera källdata till textfiler.
2. Landa data i Azure Blob Storage eller Azure Data Lake Store.
3. Förbered data för inläsning.
4. Läs in data i mellanlagrings tabeller med PolyBase eller kommandot COPY.
5. Transformera data.
6. Infoga data i produktionstabeller.

En inläsnings kurs finns i [läsa in data från Azure Blob Storage](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="1-extract-the-source-data-into-text-files"></a>1. extrahera källdata i textfiler

Att hämta data från käll systemet beror på lagrings platsen. Målet är att flytta data till avgränsade text-eller CSV-filer som stöds.

### <a name="supported-file-formats"></a>Filformat som stöds

Med PolyBase och KOPIERINGs instruktionen kan du läsa in data från UTF-8-och UTF-16-kodade avgränsade text-eller CSV-filer. Förutom avgränsade text-eller CSV-filer läses de in från Hadoop-filformat som ORC och Parquet. PolyBase och KOPIERINGs instruktionen kan också läsa in data från gzip och fästa komprimerade filer.

Utökad ASCII, fast bredds format och kapslade format som WinZip eller XML stöds inte. Om du exporterar från SQL Server kan du använda [kommando rads verktyget BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att exportera data till avgränsade textfiler.

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. landa data i Azure Blob Storage eller Azure Data Lake Store

Om du vill landa data i Azure Storage kan du flytta dem till [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) eller [Azure Data Lake Store Gen2](../../data-lake-store/data-lake-store-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). På någon av platserna ska data lagras i textfiler. PolyBase och KOPIERINGs instruktionen kan läsas in från vilken plats som helst.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) service förbättrar nätverks data flöde, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar dina data via en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga Internet. Anslutningarna ger högre tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via det offentliga Internet.
- [AzCopy-verktyget](../../storage/common/storage-choose-data-transfer-solution.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) flyttar data till Azure Storage över det offentliga Internet. Detta fungerar om data storlekarna är mindre än 10 TB. Om du vill utföra belastningen regelbundet med AZCopy testar du nätverks hastigheten för att se om den är acceptabel.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) har en gateway som du kan installera på den lokala servern. Sedan kan du skapa en pipeline för att flytta data från din lokala server upp till Azure Storage. Om du vill använda Data Factory med dedikerade SQL-pooler, se [inläsning av data för dedikerade SQL-pooler](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Förbered data för inläsning

Du kan behöva förbereda och rensa data i ditt lagrings konto innan du läser in. Data förberedelse kan utföras medan dina data är i källan, när du exporterar data till textfiler, eller när data har Azure Storage.  Det är enklast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-the-tables"></a>Definiera tabellerna

Du måste först definiera de tabeller som du läser in till i din dedikerade SQL-pool när du använder COPY-instruktionen.

Om du använder PolyBase måste du definiera externa tabeller i den dedikerade SQL-poolen innan du läser in. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. En extern tabell liknar en Database-vy. Den externa tabellen innehåller tabellens schema och pekar på data som lagras utanför den dedikerade SQL-poolen.

Definiera externa tabeller innebär att du anger data källan, formatet på textfilerna och tabell definitionerna. Referens artiklar för T-SQL-syntax som du behöver:

- [SKAPA EXTERN DATA KÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

Använd följande SQL Data Type-mappning vid inläsning av Parquet-filer:

|                         Typ av Parquet                         |   Parquet logiska typ (anteckning)   |  SQL-datatyp   |
| :----------------------------------------------------------: | :-----------------------------------: | :--------------: |
|                           BOOLESKT                            |                                       |       bit        |
|                     BINÄR/BYTE_ARRAY                      |                                       |    varbinary     |
|                            DOUBLE                            |                                       |      flyt       |
|                            FLYTA                             |                                       |       real       |
|                            INT32                             |                                       |       int        |
|                            INT64                             |                                       |      bigint      |
|                            INT96                             |                                       |    datetime2     |
|                     FIXED_LEN_BYTE_ARRAY                     |                                       |      binary      |
|                            BINARY                            |                 UTF8                  |     nvarchar     |
|                            BINARY                            |                NOLLÄNGD                 |     nvarchar     |
|                            BINARY                            |                 RÄKNING                  |     nvarchar     |
|                            BINARY                            |                 UUID                  | uniqueidentifier |
|                            BINARY                            |                DECIMAL                |     decimal      |
|                            BINARY                            |                 JSON                  |  nvarchar(MAX)   |
|                            BINARY                            |                 BSON                  |  varbinary(max)  |
|                     FIXED_LEN_BYTE_ARRAY                     |                DECIMAL                |     decimal      |
|                          BYTE_ARRAY                          |               INTERVALL                |  varchar (max),   |
|                            INT32                             |             INT (8, sant)              |     smallint     |
|                            INT32                             |            INT (16, sant)            |     smallint     |
|                            INT32                             |             INT (32, sant)             |       int        |
|                            INT32                             |            INT (8, falskt)            |     tinyint      |
|                            INT32                             |            INT (16, falskt)             |       int        |
|                            INT32                             |           INT (32, falskt)            |      bigint      |
|                            INT32                             |                 DATE                  |       date       |
|                            INT32                             |                DECIMAL                |     decimal      |
|                            INT32                             |            TID (MILLIS)             |       time       |
|                            INT64                             |            INT (64, sant)            |      bigint      |
|                            INT64                             |           INT (64, falskt)            |  decimal (20, 0)   |
|                            INT64                             |                DECIMAL                |     decimal      |
|                            INT64                             |         TID (MILLIS)                 |       time       |
|                            INT64                             | TIDSSTÄMPEL (MILLIS)                  |    datetime2     |
| [Komplex typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23lists&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=6Luk047sK26ijTzfvKMYc%2FNu%2Fz0AlLCX8lKKTI%2F8B5o%3D&reserved=0) |                 LISTA                  |   varchar(max)   |
| [Komplex typ](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fgithub.com%2Fapache%2Fparquet-format%2Fblob%2Fmaster%2FLogicalTypes.md%23maps&data=02\|01\|kevin%40microsoft.com\|19f74d93f5ca45a6b73c08d7d7f5f111\|72f988bf86f141af91ab2d7cd011db47\|1\|0\|637215323617803168&sdata=FiThqXxjgmZBVRyigHzfh5V7Z%2BPZHjud2IkUUM43I7o%3D&reserved=0) |                  MAPPA                  |   varchar(max)   |

>[!IMPORTANT] 
>- SQL-dedikerade pooler stöder för närvarande inte Parquet-datatyper med MICRO-och NANO-precision. 
>- Följande fel kan uppstå om typer inte stämmer överens mellan Parquet och SQL eller om du har Parquet-data typer som inte stöds: **"HdfsBridge:: recordReaderFillBuffer-oväntat fel vid fyllning av Record Reader-buffert: ClassCastException:..."**
>- Det finns inte stöd för att läsa in ett värde utanför intervallet 0-127 i en tinyint-kolumn för fil formatet Parquet och ORC.

Ett exempel på hur du skapar externa objekt finns i [skapa externa tabeller](../sql/develop-tables-external-tables.md?tabs=sql-pool).

### <a name="format-text-files"></a>Formatera textfiler

Om du använder PolyBase måste de externa objekt som definieras justera raderna i textfilerna med den externa tabell-och fil formats definitionen. Data i varje rad i text filen måste överensstämma med tabell definitionen.
Formatera textfilerna:

- Om dina data kommer från en icke-relationell källa måste du omvandla den till rader och kolumner. Oavsett om data kommer från en Relations källa eller icke-relationell källa måste data omvandlas för att anpassas efter kolumn definitionerna för den tabell som du planerar att läsa in data i.
- Formatera data i text filen så att de överensstämmer med kolumnerna och data typerna i mål tabellen. Fel justering mellan data typer i externa textfiler och den dedikerade SQL-adresspoolen gör att rader avvisas under belastningen.
- Separera fält i text filen med en avslutning.  Se till att du använder ett Character eller en teckensekvens som inte finns i dina källdata. Använd den avslutnings fil som du angav med [Skapa externt fil format](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-using-polybase-or-the-copy-statement"></a>4. Läs in data med PolyBase eller kopiera instruktionen

Vi rekommenderar att du läser in data i en mellanlagringsplats. Med mellanlagrings tabeller kan du hantera fel utan att störa produktions tabellerna. Med en mellanlagringsplats får du också möjlighet att använda den dedikerade parallell bearbetnings arkitekturen för SQL-pool för data transformationer innan du infogar data i produktions tabeller.

### <a name="options-for-loading"></a>Alternativ för inläsning

Om du vill läsa in data kan du använda något av följande inläsnings alternativ:

- [Kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) är det rekommenderade inläsnings verktyget, som du kan använda för att sömlöst och flexibelt läsa in data. Instruktionen har många ytterligare inläsnings funktioner som PolyBase inte tillhandahåller. 
- [PolyBase med T-SQL](./load-data-from-azure-blob-storage-using-copy.md) kräver att du definierar externa data objekt.
- [PolyBase-och Copy-uttryck med Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) är ett annat Orchestration-verktyg.  Den definierar en pipeline och schemalägger jobb.
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) fungerar bra när dina källdata är i SQL Server. SSIS definierar källa till mål tabell mappningar och dirigerar även belastningen. Om du redan har SSIS-paket kan du ändra paketen så att de fungerar med det nya informations lager målet.
- [PolyBase med Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json) överför data från en tabell till en Databricks-dataframe och/eller skriver data från en Databricks-dataframe till en tabell med PolyBase.

### <a name="other-loading-options"></a>Andra inläsnings alternativ

Förutom PolyBase och KOPIERINGs instruktionen kan du använda [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) -eller SqlBulkCopy- [API: et](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). BCP läser in direkt till databasen utan att gå via Azure Blob Storage och är endast avsedd för små belastningar.

> [!NOTE]
> Belastnings prestandan för de här alternativen är långsammare än PolyBase och KOPIERINGs instruktionen.

## <a name="5-transform-the-data"></a>5. transformera data

När data finns i mellanlagringsplatsen ska du utföra omvandlingar som din arbets belastning kräver. Flytta sedan data till en produktions tabell.

## <a name="6-insert-the-data-into-production-tables"></a>6. infoga data i produktions tabeller

INSERT INTO... SELECT-instruktionen flyttar data från mellanlagrings tabellen till den permanenta tabellen.

När du utformar en ETL-process kan du prova att köra processen på ett litet test exempel. Försök att extrahera 1000 rader från tabellen till en fil, flytta den till Azure och försök sedan att läsa in den i en mellanlagringsplats.

## <a name="partner-loading-solutions"></a>Lösningar för partner inläsning

Många av våra partner har inläsnings lösningar. Om du vill veta mer kan du se en lista över våra [lösnings partner](sql-data-warehouse-partner-business-intelligence.md).

## <a name="next-steps"></a>Nästa steg

Hjälp med inläsningen hittar du i [Vägledning för att läsa in data](guidance-for-loading-data.md).