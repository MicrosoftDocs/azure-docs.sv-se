---
title: Optimera Spark-jobb för prestanda – Azure HDInsight
description: Visa vanliga strategier för bästa prestanda för Apache Spark kluster i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 567fc2637538408d9727d07d3185a5b0e3cf20c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929943"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Optimera Apache Spark jobb i HDInsight

Den här artikeln innehåller en översikt över strategier för att optimera Apache Spark jobb på Azure HDInsight.

## <a name="overview"></a>Översikt

Prestanda för dina Apache Spark jobb beror på flera faktorer. Dessa prestanda faktorer är: hur dina data lagras, hur klustret konfigureras och vilka åtgärder som används vid bearbetning av data.

Vanliga utmaningar som du kan tänka på är: minnes begränsningar på grund av felaktigt storleksändrade körningar, långvariga åtgärder och uppgifter som resulterar i kartesiska-åtgärder.

Det finns också många optimeringar som kan hjälpa dig att lösa dessa utmaningar, till exempel cachelagring, och att data skevas.

I var och en av följande artiklar finns information om olika aspekter av Spark-optimering.

* [Optimera data lagring för Apache Spark](optimize-data-storage.md)
* [Optimera data bearbetning för Apache Spark](optimize-data-processing.md)
* [Optimera minnes användning för Apache Spark](optimize-memory-usage.md)
* [Optimera konfiguration av HDInsight-kluster för Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Nästa steg

* [Felsöka Apache Spark-jobb som körs i Azure HDInsight](apache-spark-job-debugging.md)
* [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
* [Konfigurera Apache Spark-inställningar](apache-spark-settings.md)
