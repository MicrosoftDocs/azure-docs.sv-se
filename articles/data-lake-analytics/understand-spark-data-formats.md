---
title: Förstå Apache Spark data format för Azure Data Lake Analytics U-SQL-utvecklare.
description: I den här artikeln beskrivs Apache Spark koncept som hjälper U_SQL utvecklare att förstå skillnader mellan U-SQL-och Spark-dataformat.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221119"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Förstå skillnader mellan data formaten U-SQL och Spark

Om du vill använda antingen [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) eller [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md)rekommenderar vi att du migrerar dina data från [Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) till [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Förutom att flytta dina filer vill du också göra dina data lagrade i U-SQL-tabeller, tillgängliga för Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Flytta data som lagras i Azure Data Lake Storage Gen1 filer

Data som lagras i filer kan flyttas på olika sätt:

- Skriv en [Azure Data Factory](../data-factory/introduction.md) pipeline för att kopiera Data från [Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) -konto till [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -kontot.
- Skriv ett Spark-jobb som läser data från det [Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) kontot och skriver det till [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -kontot. Baserat på ditt användnings fall kanske du vill skriva det i ett annat format, till exempel Parquet om du inte behöver behålla det ursprungliga fil formatet.

Vi rekommenderar att du läser artikeln [uppgradera dina Big data Analytics-lösningar från Azure Data Lake Storage gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Flytta data som lagras i U-SQL-tabeller

U-SQL-tabeller kan inte tolkas av Spark. Om du har data som lagras i U-SQL-tabeller, kör du ett U-SQL-jobb som extraherar tabell data och sparar dem i ett format som Spark förstår. Det lämpligaste formatet är att skapa en uppsättning Parquet-filer som följer Hive-metaarkiv Folder-layouten.

Utdata kan uppnås i U-SQL med den inbyggda Parquet-utmatningen och med hjälp av dynamisk utdata partitionering med fil uppsättningar för att skapa diskpartitioner. Att [bearbeta fler filer än någonsin och använda Parquet](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) innehåller ett exempel på hur du skapar sådana Spark-förbruknings bara data.

Efter den här omvandlingen kopierar du data som beskrivs i kapitlet [Flytta data lagrade i Azure Data Lake Storage gen1 filer](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Varningar

- Datasemantiken vid kopiering av filer görs kopian på byte-nivån. Samma data ska visas i [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -kontot. Dock kan Spark tolka vissa tecken på olika sätt. Den kan till exempel använda en annan standard för en rad avgränsare i en CSV-fil.
    Om du däremot kopierar inskrivna data (från tabeller) kan Parquet och Spark ha olika precision och skala för några av de angivna värdena (till exempel ett float) och kan behandla null-värden på olika sätt. U-SQL har till exempel C#-semantik för null-värden, medan Spark har en tre-värdes logik för null-värden.

- Data organisation (partitionering) U-SQL-tabeller tillhandahåller partitionering på två nivåer. Den yttre nivån ( `PARTITIONED BY` ) är av värde och mappar mest till Hive/Spark-partitionerings schema med hjälp av mapphierarkier. Du måste se till att null-värden mappas till rätt mapp. Den inre nivån ( `DISTRIBUTED BY` ) i U-SQL erbjuder 4 distributions scheman: resursallokering, intervall, hash och direkt hash.
    Hive/Spark-tabeller stöder bara värde partitionering eller hash-partitionering, med en annan hash-funktion än U-SQL. När du matar ut dina U-SQL-tabell data kommer du förmodligen bara att kunna mappa till värdet partitionering för Spark och kan behöva göra ytterligare justeringar av datalayouten beroende på dina slutliga Spark-frågor.

## <a name="next-steps"></a>Nästa steg

- [Förstå Spark Code-koncept för U-SQL-utvecklare](understand-spark-code-concepts.md)
- [Uppgradera dina Big data Analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET för Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformera data med Spark-aktivitet i Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformera data med Hadoop Hive-aktivitet i Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Vad är Apache Spark i Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)