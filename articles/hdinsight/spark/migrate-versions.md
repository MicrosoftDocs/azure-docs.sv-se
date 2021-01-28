---
title: Migrera Apache Spark 2,1-eller 2,2-arbetsbelastningar till 2,3 eller 2,4 – Azure HDInsight
description: Lär dig hur du migrerar Apache Spark 2,1 och 2,2 till 2,3 eller 2,4.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944757"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Migrera Apache Spark 2,1-och 2,2-arbetsbelastningar till 2,3 och 2,4

Det här dokumentet beskriver hur du migrerar Apache Spark arbets belastningar på Spark 2,1 och 2,2 till 2,3 eller 2,4.

Som det beskrivs i [viktig information](../hdinsight-release-notes.md#upcoming-changes), från den 1 juli 2020, stöds inte följande klusterkonfigurationer och kunder kommer inte att kunna skapa nya kluster med dessa konfigurationer:
 - Spark 2,1 och 2,2 i ett HDInsight 3,6 Spark-kluster
 - Spark 2,3 i ett HDInsight 4,0 Spark-kluster

Befintliga kluster i de här konfigurationerna kommer att köras som-är utan stöd från Microsoft. Om du är i Spark 2,1 eller 2,2 på HDInsight 3,6 flyttar du till Spark 2,3 på HDInsight 3,6 av juni 30 2020 för att undvika potentiell system/support-avbrott. Om du är i Spark 2,3 på ett HDInsight 4,0-kluster flyttar du till Spark 2,4 på HDInsight 4,0 av juni 30 2020 för att undvika eventuellt system-och support avbrott.

Allmän information om hur du migrerar ett HDInsight-kluster från 3,6 till 4,0 finns i [migrera HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md). Allmän information om hur du migrerar till en nyare version av Apache Spark finns i [Apache Spark: versions policy](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Vägledning om uppgraderingar av Spark-versioner på HDInsight

| Uppgraderingsscenario | Mekanism | Saker att tänka på | Spark/Hive-integrering |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3,6 Spark 2,1 till HDInsight 3,6 Spark 2,3| Återskapa kluster med HDInsight Spark 2,3 | Läs följande artiklar: <br> [Apache Spark: uppgradera från Spark SQL 2,2 till 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: uppgradera från Spark SQL 2,1 till 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Ingen ändring |
|HDInsight 3,6 Spark 2,2 till HDInsight 3,6 Spark 2,3 | Återskapa kluster med HDInsight Spark 2,3 | Läs följande artiklar: <br> [Apache Spark: uppgradera från Spark SQL 2,2 till 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Ingen ändring |
| HDInsight 3,6 Spark 2,1 till HDInsight 4,0 Spark 2,4 | Återskapa kluster med HDInsight 4,0 Spark 2,4 | Läs följande artiklar: <br> [Apache Spark: uppgradera från Spark SQL 2,3 till 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: uppgradera från Spark SQL 2,2 till 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: uppgradera från Spark SQL 2,1 till 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Spark-och Hive-integrering har ändrats i HDInsight 4,0. <br><br> I HDInsight 4,0 använder Spark och Hive oberoende kataloger för åtkomst till SparkSQL-eller Hive-tabeller. En tabell som skapats av Spark bor i Spark-katalogen. En tabell som skapats av Hive finns i Hive-katalogen. Det här beteendet skiljer sig från HDInsight 3,6 där Hive och Spark delat gemensamt katalog. Hive-och Spark-integrering i HDInsight 4,0 förlitar sig på Hive Warehouse Connector (INSTANSEN). INSTANSEN fungerar som en brygga mellan Spark och Hive. Lär dig mer om Hive lager koppling. <br> I HDInsight 4,0 om du vill dela metaarkiv mellan Hive och Spark kan du göra det genom att ändra egenskapen metaarkiv. Catalog. default till Hive i Spark-klustret. Du kan hitta den här egenskapen i Ambari Advanced spark2-Hive-site-override. Det är viktigt att förstå att delning av metaarkiv endast fungerar för externa Hive-tabeller, men detta fungerar inte om du har interna/hanterade Hive-tabeller eller syror tabeller. <br><br>Läs [migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) för mer information.<br><br> |
| HDInsight 3,6 Spark 2,2 till HDInsight 4,0 Spark 2,4 | Återskapa kluster med HDInsight 4,0 Spark 2,4 | Läs följande artiklar: <br> [Apache Spark: uppgradera från Spark SQL 2,3 till 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: uppgradera från Spark SQL 2,2 till 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Spark-och Hive-integrering har ändrats i HDInsight 4,0. <br><br> I HDInsight 4,0 använder Spark och Hive oberoende kataloger för åtkomst till SparkSQL-eller Hive-tabeller. En tabell som skapats av Spark bor i Spark-katalogen. En tabell som skapats av Hive finns i Hive-katalogen. Det här beteendet skiljer sig från HDInsight 3,6 där Hive och Spark delat gemensamt katalog. Hive-och Spark-integrering i HDInsight 4,0 förlitar sig på Hive Warehouse Connector (INSTANSEN). INSTANSEN fungerar som en brygga mellan Spark och Hive. Lär dig mer om Hive lager koppling. <br> I HDInsight 4,0 om du vill dela metaarkiv mellan Hive och Spark kan du göra det genom att ändra egenskapen metaarkiv. Catalog. default till Hive i Spark-klustret. Du kan hitta den här egenskapen i Ambari Advanced spark2-Hive-site-override. Det är viktigt att förstå att delning av metaarkiv endast fungerar för externa Hive-tabeller, men detta fungerar inte om du har interna/hanterade Hive-tabeller eller syror tabeller. <br><br>Läs [migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) för mer information.|

## <a name="next-steps"></a>Nästa steg

* [Migrera HDInsight-kluster till en nyare version](../hdinsight-upgrade-cluster.md)
* [Migrera Azure HDInsight 3,6 Hive-arbetsbelastningar till HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md)
