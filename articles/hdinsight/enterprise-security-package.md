---
title: Enterprise Security Package för Azure HDInsight
description: Lär dig Enterprise Security Package-komponenter och-versioner i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 442c21c92ef2124ebef1889f99a8d2b806c8ce10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943298"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Enterprise Security Package för Azure HDInsight

Enterprise Security är ett valfritt paket som du kan lägga till i ditt HDInsight-kluster som en del av arbets flödet skapa kluster. Enterprise Security Package stöder:

* Integrering med Active Directory för autentisering.

    Tidigare skapade du HDInsight-kluster med lokal administratörs användare och lokal SSH-användare. Den lokala administratörs användaren har åtkomst till alla filer, mappar, tabeller och kolumner.  Med Enterprise Security Package aktiverar du rollbaserad åtkomst kontroll i Azure genom att integrera HDInsight med ditt Azure Active Directory Domain Services.

    Mer information finns i:

    * [En introduktion till Apache Hadoop säkerhet med domänanslutna HDInsight-kluster](./domain-joined/hdinsight-security-overview.md)

    * [Planera Azure-domänanslutna Apache Hadoop kluster i HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Konfigurera en domänansluten testmiljö](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

    * [Konfigurera domänanslutna HDInsight-kluster med hjälp av Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Auktorisering för data

  * Integrering med Apache Ranger för auktorisering för Hive-, Spark SQL-och garn köer.
  * Du kan ange åtkomst kontroll för filer och mappar.

    Mer information finns i [konfigurera Apache Hive principer i domänanslutna HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

* Visa gransknings loggarna för att övervaka åtkomst och de konfigurerade principerna.

## <a name="supported-cluster-types"></a>Kluster typer som stöds

För närvarande stöder endast följande kluster typer Enterprise Security Package:

* Hadoop (endast HDInsight 3,6)
* Spark
* Kafka
* HBase
* Interaktiv fråga

## <a name="support-for-azure-data-lake-storage"></a>Stöd för Azure Data Lake Storage

Enterprise Security Package har stöd för att använda Azure Data Lake Storage som både den primära lagringen och lagringen för tillägg.

## <a name="pricing-and-service-level-agreement-sla"></a>Priser och service nivå avtal (SLA)

Information om priser och service avtal för Enterprise Security Package finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nästa steg

* [Kluster konfiguration för Apache Hadoop, Spark och mer på HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)
* [Hortonworks versions anteckningar som är associerade med Azure HDInsight-versioner](./hortonworks-release-notes.md)
* [Apache-komponenter på HDInsight](./hdinsight-component-versioning.md)