---
title: Utforma en PolyBase data inläsnings strategi för dedikerad SQL-pool
description: I stället för ETL kan du utforma en process för att extrahera, läsa in och transformera (ELT) för att läsa in data med dedikerad SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 18a2cf0de94641c955ed72a48f28352d13115ef0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667575"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Utforma en PolyBase data inläsnings strategi för dedikerad SQL-pool i Azure Synapse Analytics

Traditionella SMP-datalager använder en process för extrahering, transformering och inläsning (ETL) för att läsa in data. Azure SQL-poolen är en minnes trycks arkitektur med massivt parallell bearbetning (MPP) som drar nytta av skalbarheten och flexibiliteten i beräknings-och lagrings resurser. Att använda en process för att extrahera, läsa in och transformera (ELT) kan dra nytta av inbyggda funktioner för distribuerad frågekörning och eliminera de resurser som behövs för att transformera data innan de läses in.

SQL-poolen stöder många inläsnings metoder, inklusive icke-PolyBase-alternativ som BCP och SQL BulkCopy API, och det snabbaste och mest skalbara sättet att läsa in datum är genom PolyBase.  PolyBase är en teknik som använder externa data som lagras i Azure Blob Storage eller som Azure Data Lake Store via T-SQL-språket.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extrahera, Läs in och transformera (ELT)

Extrahera, läsa in och transformera (ELT) är en process som data extraheras från ett käll system, läses in i ett data lager och sedan omvandlas.

De grundläggande stegen för att implementera en PolyBase-ELT för dedikerad SQL-pool är:

1. Extrahera källdata till textfiler.
2. Landa data i Azure Blob Storage eller Azure Data Lake Store.
3. Förbered data för inläsning.
4. Läs in data i dedikerade SQL-pool för mellanlagring av tabeller med PolyBase.
5. Transformera data.
6. Infoga data i produktionstabeller.

En inläsnings kurs finns i [använda PolyBase för att läsa in data från Azure Blob Storage till Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Mer information finns i [blogg om inläsnings mönster](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. extrahera källdata i textfiler

Att hämta data från käll systemet beror på lagrings platsen.  Målet är att flytta data till PolyBase-stödda avgränsade textfiler.

### <a name="polybase-external-file-formats"></a>Externa fil format för PolyBase

PolyBase läser in data från UTF-8-och UTF-16-kodade avgränsade textfiler. Förutom de avgränsade textfilerna läses de in från Hadoop-filformatet RC-filen, ORC och Parquet. PolyBase kan också läsa in data från gzip och fästa komprimerade filer. PolyBase stöder för närvarande inte utökade ASCII-, fast bredd-format och kapslade format som WinZip, JSON och XML.

Om du exporterar från SQL Server kan du använda [kommando rads verktyget BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) för att exportera data till avgränsade textfiler. Data typs mappningen Parquet till Azure Synapse Analytics är följande:

| **Data typen Parquet** |                      **SQL-datatyp**                       |
| :-------------------: | :----------------------------------------------------------: |
|        tinyint        |                           tinyint                            |
|       smallint        |                           smallint                           |
|          int          |                             int                              |
|        bigint         |                            bigint                            |
|        boolean        |                             bit                              |
|        double         |                            flyt                             |
|         flyt         |                             real                             |
|        double         |                            money                             |
|        double         |                          smallmoney                          |
|        sträng         |                            nchar                             |
|        sträng         |                           nvarchar                           |
|        sträng         |                             char                             |
|        sträng         |                           varchar                            |
|        binary         |                            binary                            |
|        binary         |                          varbinary                           |
|       timestamp       |                             date                             |
|       timestamp       |                        smalldatetime                         |
|       timestamp       |                          datetime2                           |
|       timestamp       |                           datetime                           |
|       timestamp       |                             time                             |
|       date            |                             date                             |
|        decimal        |                            decimal                           |

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. landa data i Azure Blob Storage eller Azure Data Lake Store

Om du vill landa data i Azure Storage kan du flytta dem till [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) eller [Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). På någon av platserna ska data lagras i textfiler. PolyBase kan läsas in från vilken plats som helst.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) service förbättrar nätverks data flöde, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar dina data via en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga Internet. Anslutningarna ger högre tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via det offentliga Internet.
- [AzCopy-verktyget](../../storage/common/storage-use-azcopy-v10.md) flyttar data till Azure Storage över det offentliga Internet. Detta fungerar om data storlekarna är mindre än 10 TB. Om du vill utföra belastningen regelbundet med AZCopy testar du nätverks hastigheten för att se om den är acceptabel.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) har en gateway som du kan installera på den lokala servern. Sedan kan du skapa en pipeline för att flytta data från din lokala server upp till Azure Storage. Om du vill använda Data Factory med dedikerad SQL-pool, se [Läs in data i dedikerad SQL-pool](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="3-prepare-the-data-for-loading"></a>3. Förbered data för inläsning

Du kan behöva förbereda och rensa data i ditt lagrings konto innan du läser in det i en dedikerad SQL-pool. Data förberedelse kan utföras medan dina data är i källan, när du exporterar data till textfiler, eller när data har Azure Storage.  Det är enklast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-external-tables"></a>Definiera externa tabeller

Innan du kan läsa in data måste du definiera externa tabeller i ditt informations lager. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. En extern tabell liknar en Database-vy. Den externa tabellen innehåller tabellens schema och pekar på data som lagras utanför data lagret.

Definiera externa tabeller innebär att du anger data källan, formatet på textfilerna och tabell definitionerna. I följande avsnitt finns information om T-SQL-syntaxen som du behöver:

- [SKAPA EXTERN DATA KÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Formatera textfiler

När de externa objekten har definierats måste du justera raderna i textfilerna med den externa tabell-och fil formats definitionen. Data i varje rad i text filen måste överensstämma med tabell definitionen.
Formatera textfilerna:

- Om dina data kommer från en icke-relationell källa måste du omvandla den till rader och kolumner. Oavsett om data kommer från en Relations källa eller icke-relationell källa måste data omvandlas för att anpassas efter kolumn definitionerna för den tabell som du planerar att läsa in data i.
- Formatera data i text filen så att de överensstämmer med kolumnerna och data typerna i SQL-poolens mål tabell. Fel justering mellan data typer i externa textfiler och data lager tabellen gör att rader avvisas under belastningen.
- Separera fält i text filen med en avslutning.  Se till att du använder ett Character eller en teckensekvens som inte finns i dina källdata. Använd den avslutnings fil som du angav med [Skapa externt fil format](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Läs in data i dedikerade SQL-pool för mellanlagrings tabeller med PolyBase

Vi rekommenderar att du läser in data i en mellanlagringsplats. Med mellanlagrings tabeller kan du hantera fel utan att störa produktions tabellerna. Med en mellanlagringsplats får du också möjlighet att använda SQL-poolen inbyggda funktioner för distribuerad frågekörning för data transformationer innan du infogar data i produktions tabeller.

### <a name="options-for-loading-with-polybase"></a>Alternativ för att läsa in med PolyBase

Om du vill läsa in data med PolyBase kan du använda något av följande inläsnings alternativ:

- [PolyBase med T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) fungerar bra när dina data finns i Azure Blob storage eller Azure Data Lake Store. Du får störst kontroll över inläsnings processen, men du måste också definiera externa data objekt. De andra metoderna definierar dessa objekt bakom scenerna när du mappar käll tabeller till mål tabeller.  För att dirigera T-SQL-inläsningar kan du använda Azure Data Factory, SSIS eller Azure Functions.
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) fungerar bra när dina källdata är i SQL Server. SSIS definierar källa till mål tabell mappningar och dirigerar även belastningen. Om du redan har SSIS-paket kan du ändra paketen så att de fungerar med det nya informations lager målet.
- [PolyBase med Azure Data Factory (ADF)](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) är ett annat Orchestration-verktyg.  Den definierar en pipeline och schemalägger jobb.
- [PolyBase med Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) överför data från en Azure Synapse Analytics-tabell till en Databricks-dataframe och/eller skriver data från en Databricks-dataframe till en Azure Synapse Analytics-tabell med PolyBase.

### <a name="non-polybase-loading-options"></a>Alternativ för icke-PolyBase-inläsning

Om dina data inte är kompatibla med PolyBase kan du använda [BCP](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) -eller [SQLBulkCopy-API: et](/dotnet/api/system.data.sqlclient.sqlbulkcopy). BCP läser in direkt till dedikerad SQL-pool utan att gå via Azure Blob Storage, och är endast avsedd för små belastningar. Obs! belastnings prestandan för de här alternativen är betydligt långsammare än PolyBase.

## <a name="5-transform-the-data"></a>5. transformera data

När data finns i mellanlagringsplatsen ska du utföra omvandlingar som din arbets belastning kräver. Flytta sedan data till en produktions tabell.

## <a name="6-insert-the-data-into-production-tables"></a>6. infoga data i produktions tabeller

INSERT INTO... SELECT-instruktionen flyttar data från mellanlagrings tabellen till den permanenta tabellen.

När du utformar en ETL-process kan du prova att köra processen på ett litet test exempel. Försök att extrahera 1000 rader från tabellen till en fil, flytta den till Azure och försök sedan att läsa in den i en mellanlagringsplats.

## <a name="partner-loading-solutions"></a>Lösningar för partner inläsning

Många av våra partner har inläsnings lösningar. Om du vill veta mer kan du se en lista över våra [lösnings partner](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="next-steps"></a>Nästa steg

Information om hur du läser in vägledning finns i [rikt linjer för att läsa in data](data-loading-best-practices.md).