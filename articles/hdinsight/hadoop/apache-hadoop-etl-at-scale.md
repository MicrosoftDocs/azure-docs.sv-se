---
title: Extrahera, transformera och läsa in (ETL) i skala – Azure HDInsight
description: Lär dig hur extrahering, transformering och inläsning används i HDInsight med Apache Hadoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: fc488cecb762ff13bcb2999bb06521ebb70b44fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946575"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extrahera, transformera och läsa in (ETL) i skala

Extrahering, transformering och inläsning (ETL) är den process som data hämtas från olika källor. Data samlas in på standard platsen, rensas och bearbetas. Slutligen läses data in i ett data lager som de kan frågas från. Äldre ETL-processer importerar data, rengör dem på plats och lagrar dem sedan i en Relations data motor. Med Azure HDInsight har en mängd olika Apache Hadoops miljö komponenter stöd för ETL i stor skala.

Användningen av HDInsight i ETL-processen sammanfattas av den här pipelinen:

![HDInsight ETL i skalnings översikt](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

I följande avsnitt lär du dig var och en av de olika ETL-faserna och deras associerade komponenter.

## <a name="orchestration"></a>Orkestrering

Dirigering sträcker sig över alla faser i ETL-pipeline. ETL-jobb i HDInsight omfattar ofta flera olika produkter som arbetar tillsammans med varandra. Exempel:

- Du kan använda Apache Hive för att rensa en del av data och Apache gris för att rensa en annan del.
- Du kan använda Azure Data Factory för att läsa in data i Azure SQL Database från Azure Data Lake Store.

Dirigering krävs för att köra lämpligt jobb vid lämplig tidpunkt.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie är ett system för att koordinera arbetsflöden som hanterar Hadoop-jobb. Oozie körs i ett HDInsight-kluster och är integrerat med Hadoop-stacken. Oozie stöder Hadoop-jobb för Apache Hadoop MapReduce, gris, Hive och Sqoop. Du kan använda Oozie för att schemalägga jobb som är speciella för ett system, t. ex. Java-program eller Shell-skript.

Mer information finns i [använda Apache Oozie med Apache Hadoop för att definiera och köra ett arbets flöde i HDInsight](../hdinsight-use-oozie-linux-mac.md). Se även [operationalisera data pipeline](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory tillhandahåller Orchestration-funktioner i form av plattform som en tjänst (PaaS). Azure Data Factory är en molnbaserad dataintegreringstjänst. Du kan skapa data drivna arbets flöden för att dirigera och automatisera data förflyttning och data omvandling.

Använd Azure Data Factory för att:

1. Skapa och schemalägga data drivna arbets flöden. Dessa pipeliner matar in data från olika data lager.
1. Bearbeta och transformera data med hjälp av beräknings tjänster som HDInsight eller Hadoop. Du kan också använda Spark, Azure Data Lake Analytics, Azure Batch eller Azure Machine Learning för det här steget.
1. Publicera utdata till data lager, till exempel Azure Synapse Analytics, för att BI-program ska förbruka.

Mer information om Azure Data Factory finns i [dokumentationen](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Hämta fil lagring och resultat lagring

Källfiler för data läses vanligt vis in på en plats på Azure Storage eller Azure Data Lake Storage. Filerna är vanligt vis i ett flat format, som CSV. Men de kan vara i valfritt format.

### <a name="azure-storage"></a>Azure Storage

Azure Storage har speciella anpassnings mål. Mer information finns i [skalbarhets-och prestanda mål för Blob Storage](../../storage/blobs/scalability-targets.md) . För de flesta analytiska noder skalas Azure Storage bäst vid hantering av många mindre filer. Så länge som du befinner dig inom gränsen för dina konton, Azure Storage garanterar samma prestanda, oavsett hur stora filerna är. Du kan lagra flera terabyte data och ändå få konsekventa prestanda. Den här instruktionen är sann oavsett om du använder en delmängd eller alla data.

Azure Storage har flera typer av blobbar. En *tilläggs-BLOB* är ett bra alternativ för att lagra webb loggar eller sensor data.

Flera blobbar kan distribueras på många servrar för att skala ut åtkomsten till dem. Men en enda BLOB hanteras bara av en enda server. Även om blobbar kan grupperas logiskt i BLOB-behållare, finns det inga partitionerings konsekvenser från den här grupperingen.

Azure Storage har ett WebHDFS API-lager för blob-lagringen. Alla HDInsight-tjänster kan komma åt filer i Azure Blob Storage för data rensning och data bearbetning. Detta liknar hur dessa tjänster använder Hadoop Distributed File System (HDFS).

Data matas vanligt vis in i Azure Storage via PowerShell, Azure Storage SDK eller AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage är en hanterad storskalig lagrings plats för analys data. Den är kompatibel med och använder ett design paradigm som liknar HDFS. Data Lake Storage erbjuder obegränsad anpassning för total kapacitet och storleken på enskilda filer. Det är ett bra val när du arbetar med stora filer, eftersom de kan lagras på flera noder. Att partitionera data i Data Lake Storage görs i bakgrunden. Du får enorma data flöden för att köra analys jobb med tusentals samtidiga körningar som effektivt läser och skriver hundratals terabyte data.

Data matas vanligt vis in i Data Lake Storage via Azure Data Factory. Du kan också använda Data Lake Storage SDK: er, AdlCopy-tjänsten, Apache DistCp eller Apache Sqoop. Vilken tjänst du väljer beror på var datan finns. Om det finns i ett befintligt Hadoop-kluster kan du använda Apache DistCp, AdlCopy-tjänsten eller Azure Data Factory. För data i Azure Blob Storage kan du använda Azure Data Lake Storage .NET SDK, Azure PowerShell eller Azure Data Factory.

Data Lake Storage är optimerad för händelse inmatning via Azure Event Hubs eller Apache Storm.

### <a name="considerations-for-both-storage-options"></a>Att tänka på för båda lagrings alternativen

För att ladda upp data uppsättningar i intervallet terabyte kan nätverks fördröjningen vara ett större problem. Detta gäller särskilt om data kommer från en lokal plats. I sådana fall kan du använda följande alternativ:

- **Azure-ExpressRoute:** Skapa privata anslutningar mellan Azure-datacenter och din lokala infrastruktur. Dessa anslutningar ger ett tillförlitligt alternativ för överföring av stora mängder data. Mer information finns i [dokumentationen om Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

- **Data uppladdning från hård diskar:** Du kan använda [Azure import/export-tjänsten](../../import-export/storage-import-export-service.md) för att leverera hård diskar med dina data till ett Azure-datacenter. Dina data laddas först upp till Azure Blob Storage. Du kan sedan använda Azure Data Factory eller AdlCopy-verktyget för att kopiera data från Azure Blob Storage till Data Lake Storage.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure Synapse Analytics är ett lämpligt alternativ för att lagra för beredda resultat. Du kan använda Azure HDInsight för att utföra dessa tjänster för Azure Synapse Analytics.

Azure Synapse Analytics är ett Relations databas lager som är optimerat för analytiska arbets belastningar. Den skalas baserat på partitionerade tabeller. Tabeller kan partitioneras över flera noder. Noderna väljs vid tidpunkten för skapandet. De kan skalas efter faktumet, men det är en aktiv process som kan kräva data förflyttning. Mer information finns i [Hantera beräkning i Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase är ett nyckel/värde-lager tillgängligt i Azure HDInsight. Det är en NoSQL-databas med öppen källkod som bygger på Hadoop och modelleras efter Google BigTable. HBase tillhandahåller slumpmässig åtkomst och stark konsekvens för stora mängder ostrukturerade och delvis strukturerade data.

Eftersom HBase är en schema lös databas behöver du inte definiera kolumner och data typer innan du använder dem. Data lagras i raderna i en tabell och grupperas efter kolumn serie.

Den öppna källkoden skalas linjärt för att hantera petabyte med data på tusentals noder. HBase är beroende av dataredundans, batchbearbetning och andra funktioner som tillhandahålls av distribuerade program i Hadoop-miljön.

HBase är ett lämpligt mål för sensor-och loggdata för framtida analys.

HBase anpassning är beroende av antalet noder i HDInsight-klustret.

### <a name="azure-sql-databases"></a>Azure SQL-databaser

Azure erbjuder tre PaaS-relationella databaser:

* [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) är en implementering av Microsoft SQL Server. Mer information om prestanda finns [i justera prestanda i Azure SQL Database](../../azure-sql/database/performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md)  är en implementering av Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) är en implementering av postgresql.

Lägg till mer processor och minne för att skala upp dessa produkter.  Du kan också välja att använda Premium diskar med produkterna för bättre I/O-prestanda.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services är en analys data motor som används i besluts support och affärs analys. Den innehåller analys data för företags rapporter och klient program som Power BI. Analys data fungerar också med Excel, SQL Server Reporting Services rapporter och andra verktyg för data visualisering.

Skala Analysis-kuber genom att ändra nivåer för varje enskild kub. Mer information finns i [Azure Analysis Services prissättning](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahera och Läs in

När data finns i Azure kan du använda många tjänster för att extrahera och läsa in dem i andra produkter. HDInsight stöder Sqoop och FLUME.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop är ett verktyg som utformats för att effektivt överföra data mellan strukturerade, halv strukturerade och ostrukturerade data källor.

Sqoop använder MapReduce för att importera och exportera data för att tillhandahålla parallell drift och fel tolerans.

### <a name="apache-flume"></a>Apache FLUME

Apache FLUME är en distribuerad, tillförlitlig och tillgänglig tjänst för att effektivt samla in, aggregera och flytta stora mängder logg data. Den flexibla arkitekturen baseras på strömmande data flöden. FLUME är robust och feltolerant med justerbara Tillförlitlighets metoder. Det har många funktioner för redundans och återställning. FLUME använder en enkel utöknings bar data modell som möjliggör online-och analys program.

Apache FLUME kan inte användas med Azure HDInsight. Men en lokal Hadoop-installation kan använda FLUME för att skicka data till antingen Azure Blob Storage eller Azure Data Lake Storage. Mer information finns i [using Apache FLUME med HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformering

När data finns på den valda platsen måste du rensa den, kombinera den eller förbereda den för ett särskilt användnings mönster. Hive, gris och Spark SQL är alla lämpliga alternativ för den typen av arbete. De stöds i HDInsight.

## <a name="next-steps"></a>Nästa steg

- [Använda Apache Hive som ETL-verktyg](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Flytta data från Azure SQL Database till en Apache Hive tabell](./apache-hadoop-use-sqoop-mac-linux.md)
