---
title: Utforma en PolyBase-data inläsningsstrategi för dedikerad SQL-pool
description: I stället för ETL utformar du en ELT-process (Extract, Load, and Transform) för inläsning av data med dedikerad SQL.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 518843e688da7f940b36e77aee2667b4984ea5a3
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567364"
---
# <a name="design-a-polybase-data-loading-strategy-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Utforma en PolyBase-data inläsningsstrategi för dedikerad SQL-pool i Azure Synapse Analytics

Traditionella SMP-informationslager använder en process för extrahering, transformering och inläsning (ETL) för inläsning av data. Azure SQL en MPP-arkitektur (Massively Parallel Processing) som drar nytta av skalbarheten och flexibiliteten hos beräknings- och lagringsresurser. Med hjälp av en ELT-process (extrahering, inläsning och transformering) kan du dra nytta av inbyggda funktioner för distribuerad frågebearbetning och eliminera de resurser som behövs för att transformera data före inläsningen.

SQL-poolen stöder många inläsningsmetoder, inklusive icke-PolyBase-alternativ som BCP och SQL BulkCopy API, men det snabbaste och mest skalbara sättet att läsa in datum är via PolyBase.  PolyBase är en teknik som ger åtkomst till externa data som lagras i Azure Blob Storage eller Azure Data Lake Store via T-SQL-språket.

> [!VIDEO https://www.youtube.com/embed/l9-wP7OdhDk]

## <a name="extract-load-and-transform-elt"></a>Extrahering, inläsning och transformering (ELT)

Extrahering, inläsning och transformering (ELT) är en process genom vilken data extraheras från ett källsystem, läses in i ett informationslager och sedan transformeras.

De grundläggande stegen för att implementera en PolyBase ELT för dedikerad SQL-pool är:

1. Extrahera källdata till textfiler.
2. Landa data till Azure Blob Storage eller Azure Data Lake Store.
3. Förbered data för inläsning.
4. Läs in data till dedikerade mellanlagringstabeller för SQL-pooler med PolyBase.
5. Transformera data.
6. Infoga data i produktionstabeller.

En självstudiekurs om inläsning [finns i Använda PolyBase för att läsa in data från Azure Blob Storage till Azure Synapse Analytics](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

Mer information finns i [Läsa in mönster blogg](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies).

## <a name="1-extract-the-source-data-into-text-files"></a>1. Extrahera källdata till textfiler

Att hämta data från källsystemet beror på lagringsplatsen.  Målet är att flytta data till PolyBase-stödda avgränsade textfiler.

### <a name="polybase-external-file-formats"></a>Externa PolyBase-filformat

PolyBase läser in data från UTF-8- och UTF-16-kodade avgränsade textfiler. Förutom de avgränsade textfilerna läses de in från Hadoop-filformaten RC-fil, ORC och Parquet. PolyBase kan också läsa in data från Gzip- och Snappy-komprimerade filer. PolyBase stöder för närvarande inte utökat ASCII-format, format med fast bredd och kapslade format som WinZip, JSON och XML.

Om du exporterar från SQL Server kan du använda [kommandoradsverktyget bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) för att exportera data till avgränsade textfiler. Parquet för att Azure Synapse Analytics mappning av datatyp är följande:

| **Parquet-datatyp** |                      **SQL-datatyp**                       |
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

## <a name="2-land-the-data-into-azure-blob-storage-or-azure-data-lake-store"></a>2. Landa data i Azure Blob Storage eller Azure Data Lake Store

Om du vill landa data i Azure Storage kan du flytta dem till [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) eller Azure [Data Lake Store](../../data-lake-store/data-lake-store-overview.md). På båda platserna ska data lagras i textfiler. PolyBase kan läsas in från någon av platserna.

Verktyg och tjänster som du kan använda för att flytta data till Azure Storage:

- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) tjänst förbättrar nätverkets dataflöde, prestanda och förutsägbarhet. ExpressRoute är en tjänst som dirigerar dina data via en dedikerad privat anslutning till Azure. ExpressRoute-anslutningar dirigerar inte data via det offentliga Internet. Anslutningarna ger bättre tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via offentligt Internet.
- [AZCopy-verktyget](../../storage/common/storage-use-azcopy-v10.md) flyttar data Azure Storage över det offentliga Internet. Detta fungerar om dina datastorlekar är mindre än 10 TB. Om du vill utföra belastningar regelbundet med AZCopy testar du nätverkshastigheten för att se om det är acceptabelt.
- [Azure Data Factory (ADF)](../../data-factory/introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) har en gateway som du kan installera på din lokala server. Sedan kan du skapa en pipeline för att flytta data från din lokala server upp till Azure Storage. Om du vill Data Factory med dedikerad SQL-pool kan du läsa [in data i dedikerad SQL-pool.](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="3-prepare-the-data-for-loading"></a>3. Förbered data för inläsning

Du kan behöva förbereda och rensa data i ditt lagringskonto innan du läser in dem i en dedikerad SQL-pool. Förberedelse av data kan utföras när dina data finns i källan, när du exporterar data till textfiler eller när data finns i Azure Storage.  Det är enklast att arbeta med data så tidigt i processen som möjligt.  

### <a name="define-external-tables"></a>Definiera externa tabeller

Innan du kan läsa in data måste du definiera externa tabeller i informationslagret. PolyBase använder externa tabeller för att definiera och komma åt data i Azure Storage. En extern tabell liknar en databasvy. Den externa tabellen innehåller tabellschemat och pekar på data som lagras utanför informationslagret.

Att definiera externa tabeller innebär att ange datakällan, formatet för textfilerna och tabelldefinitionerna. Följande är de avsnitt om T-SQL-syntax som du behöver:

- [SKAPA EXTERN DATAKÄLLA](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [SKAPA EXTERN TABELL](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="format-text-files"></a>Formatera textfiler

När de externa objekten har definierats måste du justera raderna i textfilerna med den externa tabellen och filformatdefinitionen. Data på varje rad i textfilen måste justeras med tabelldefinitionen.
Så här formaterar du textfilerna:

- Om dina data kommer från en icke-relationell källa måste du transformera dem till rader och kolumner. Oavsett om data kommer från en relationskälla eller icke-relationell källa måste data omvandlas för att justeras med kolumndefinitionerna för den tabell som du planerar att läsa in data i.
- Formatera data i textfilen så att de överensstämmer med kolumnerna och datatyperna i måltabellen för SQL-poolen. Justeringsfel mellan datatyper i de externa textfilerna och informationslagertabellen gör att rader avvisas under inläsningen.
- Avgränsa fält i textfilen med en avgränsare.  Se till att använda ett tecken eller en teckensekvens som inte finns i dina källdata. Använd den avslutande som du angav med [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true).

## <a name="4-load-the-data-into-dedicated-sql-pool-staging-tables-using-polybase"></a>4. Läs in data i dedikerade SQL-pool-mellanlagringstabeller med PolyBase

Det är bästa praxis att läsa in data i en mellanlagringstabell. Med mellanlagringstabeller kan du hantera fel utan att störa produktionstabellerna. En mellanlagringstabell ger dig också möjlighet att använda inbyggda funktioner för distribuerad frågebearbetning i SQL-pool för datatransformationer innan du infogar data i produktionstabeller.

### <a name="options-for-loading-with-polybase"></a>Alternativ för inläsning med PolyBase

Om du vill läsa in data med PolyBase kan du använda något av följande inläsningsalternativ:

- [PolyBase med T-SQL](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) fungerar bra när dina data finns i Azure Blob Storage eller Azure Data Lake Store. Det ger dig störst kontroll över inläsningsprocessen, men kräver också att du definierar externa dataobjekt. De andra metoderna definierar dessa objekt i bakgrunden när du mappar källtabeller till måltabeller.  För att samordna T-SQL-inbelastningar kan du använda Azure Data Factory,SSIS eller Azure-funktioner.
- [PolyBase med SSIS](/sql/integration-services/load-data-to-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) fungerar bra när dina källdata finns SQL Server. SSIS definierar käll-till-måltabellmappningar och orkestrering av belastningen. Om du redan har SSIS-paket kan du ändra paketen så att de fungerar med det nya informationslagermålet.
- [PolyBase med Azure Data Factory (ADF) är](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ett annat orkestreringsverktyg.  Den definierar en pipeline och schemalägger jobb.
- [PolyBase med Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) överför data från en Azure Synapse Analytics-tabell till en Databricks-dataram och/eller skriver data från en Databricks-dataram till en Azure Synapse Analytics-tabell med PolyBase.

### <a name="non-polybase-loading-options"></a>Inläsningsalternativ som inte är PolyBase

Om dina data inte är kompatibla med PolyBase kan du använda [bcp](/sql/tools/bcp-utility?view=azure-sqldw-latest&preserve-view=true) eller [SQLBulkCopy API.](/dotnet/api/system.data.sqlclient.sqlbulkcopy) bcp läser in direkt till dedikerad SQL-pool utan att gå via Azure Blob Storage och är endast avsett för små belastningar. Observera att inläsningsprestandan för dessa alternativ är betydligt långsammare än PolyBase.

## <a name="5-transform-the-data"></a>5. Transformera data

När data finns i mellanlagringstabellen utför du transformningar som din arbetsbelastning kräver. Flytta sedan data till en produktionstabell.

## <a name="6-insert-the-data-into-production-tables"></a>6. Infoga data i produktionstabeller

INFOGA I ... SELECT-instruktionen flyttar data från mellanlagringstabellen till den permanenta tabellen.

När du utformar en ETL-process kan du prova att köra processen på ett litet testexempel. Prova att extrahera 1 000 rader från tabellen till en fil, flytta den till Azure och prova sedan att läsa in den i en mellanlagringstabell.

## <a name="partner-loading-solutions"></a>Inläsningslösningar för partner

Många av våra partner har inläsningslösningar. Mer information finns i en lista över våra [lösningspartner.](../sql-data-warehouse/sql-data-warehouse-partner-business-intelligence.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="next-steps"></a>Nästa steg

Vägledning för inläsning finns i [Vägledning för att läsa in data.](data-loading-best-practices.md)