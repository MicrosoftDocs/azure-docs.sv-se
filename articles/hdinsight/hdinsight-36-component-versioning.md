---
title: Apache Hadoop komponenter och versioner – Azure HDInsight 3,6
description: Lär dig mer om Apache Hadoop-komponenter och-versioner i Azure HDInsight 3,6.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727696"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3,6-komponent versioner

I den här artikeln får du lära dig mer om komponenter och versioner i Apache Hadoop-miljön i Azure HDInsight 3,6.

## <a name="support-for-hdinsight-36"></a>Stöd för HDInsight 3,6

Från och med den 1 juli 2021 kommer Microsoft att erbjuda Basic Support för vissa typer av HDI 3,6-kluster.
I tabellen nedan visas support ramarna för kluster typerna HDInsight 3,6.

| Typ av kluster                    | Framework-version | Förfallo tid för standard support       | Basic Support förfallo datum | Datum för indragning |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 Spark             | 2.3               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6-Kafka             | 1.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6-HBase             | 1.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 interaktiv fråga | 2.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 Storm             | 1.1               | 30 juni 2021                     | 3 april 2022                | 4 april 2022 |
| HDInsight 3,6 ML-tjänster      | 9,3               | -                                 | -                            | 31 december 2020 |
| HDInsight 3,6 Spark             | 2.2               | -                                 | -                            | 30 juni 2020 |
| HDInsight 3,6 Spark             | 2.1               | -                                 | -                            | 30 juni 2020 |
| HDInsight 3,6-Kafka             | 1.0               | -                                 | -                            | 30 juni 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Apache-komponenter som är tillgängliga med HDInsight version 3,6

De OSS-komponent versioner som är associerade med HDInsight 3,6 visas i följande tabell.

| Komponent              | HDInsight 3,6 (standard)     |
|------------------------|-----------------------------|
| Apache Hadoop och garn | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache gris             | 0.16.0                      |
| Apache Hive            | (2.1.0 på en inaktiv ESP-fråga) |
| Apache Tez-Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0 +                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0,4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Nästa steg

- [Kluster konfiguration för Apache Hadoop, Spark och mer på HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise Security Package](./enterprise-security-package.md)
- [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)
