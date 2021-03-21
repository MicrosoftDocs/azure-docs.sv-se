---
title: Förstå Apache Spark för Azure Data Lake Analytics U-SQL-utvecklare.
description: I den här artikeln beskrivs Apache Spark begrepp som hjälper dig att skilja mellan U-SQL-utvecklare.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: a66a82a6d2a5bb1f534ed211091793b2ab4d2a28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221102"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Förstå Apache Spark för U-SQL-utvecklare

Microsoft stöder flera analys tjänster som [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) och [Azure HDInsight](../hdinsight/hdinsight-overview.md) samt Azure Data Lake Analytics. Vi hör från utvecklare att de har en tydlig inställning för lösningar med öppen källkod när de skapar analys pipeliner. För att hjälpa U-SQL-utvecklare att förstå Apache Spark och hur du kan omvandla dina U-SQL-skript till Apache Spark, har vi skapat den här vägledningen.

Det innehåller ett antal steg som du kan vidta och flera alternativ.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Steg för att transformera U-SQL till Apache Spark

1. Transformera dina jobb Dirigerings pipeliner.

   Om du använder [Azure Data Factory](../data-factory/introduction.md) för att dirigera dina Azure Data Lake Analytics-skript måste du justera dem för att dirigera de nya Spark-programmen.
2. Förstå skillnaderna mellan hur U-SQL och Spark hanterar data

   Om du vill flytta data från [Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) till [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)måste du kopiera både fildata och katalogen med data. Observera att Azure Data Lake Analytics endast stöder Azure Data Lake Storage Gen1. Se [förstå Spark data format](understand-spark-data-formats.md)
3. Omvandla U-SQL-skript till Spark

   Innan du kan omvandla U-SQL-skripten måste du välja en analys tjänst. Några av de tillgängliga beräknings tjänsterna är:
      - [Azure Data Factory-dataflöde](../data-factory/concepts-data-flow-overview.md) Mappning av data flöden är visuellt utformad med data transformationer som gör det möjligt för data tekniker att utveckla en grafisk data omvandlings logik utan att skriva kod. Även om det inte passar att köra komplex användar kod kan de enkelt representera traditionella SQL-liknande data flödes omvandlingar
      - [Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive i HDInsight är lämpligt för att extrahera, transformera och läsa in (ETL) åtgärder. Det innebär att du kommer att översätta U-SQL-skripten till Apache Hive.
      - Apache Spark-motorer som [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) eller [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) det innebär att du kommer att översätta U-SQL-skript till Spark. Mer information finns i [förstå Spark data format](understand-spark-data-formats.md)

> [!CAUTION]
> Både [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) och [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) är kluster tjänster och inte serverbaserade jobb som Azure Data Lake Analytics. Du måste fundera över hur du ska etablera klustren för att få rätt kostnad/prestanda-förhållande och hur du hanterar deras livs längd för att minimera kostnaderna. Dessa tjänster har olika prestanda egenskaper med användar kod som skrivits i .NET, så du måste antingen skriva omslutningar eller skriva om koden på ett språk som stöds. Mer information finns i [förstå Spark-dataformat](understand-spark-data-formats.md), [förstå Apache Spark kod koncept för U-SQL-utvecklare](understand-spark-code-concepts.md), [.net för Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Nästa steg

- [Förstå Spark data format för U-SQL-utvecklare](understand-spark-data-formats.md)
- [Förstå Spark Code-koncept för U-SQL-utvecklare](understand-spark-code-concepts.md)
- [Uppgradera dina Big data Analytics-lösningar från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET för Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Transformera data med Hadoop Hive-aktivitet i Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Transformera data med Spark-aktivitet i Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Vad är Apache Spark i Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)