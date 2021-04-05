---
title: Hög tillgänglighet med Apache Kafka – Azure HDInsight
description: Lär dig mer om att säkerställa hög tillgänglighet med Apache Kafka på Azure HDInsight. Lär dig mer om att balansera om partitionsrepliker i Kafka så att de är inom olika feldomäner i Azure-regionen som innehåller HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e3f31bc1876a0c1f90ad4882ff782be25b4b6ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932762"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Hög tillgänglighet för dina data med Apache Kafka på HDInsight

Lär dig hur du konfigurerar partitioners repliker för Apache Kafka ämnen för att dra nytta av den underliggande maskin varu konfigurationen. Den här konfigurationen garanterar tillgängligheten för data som lagras i Apache Kafka på HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Fel-och uppdaterings domäner med Apache Kafka

En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. De virtuella datorer och hanterade diskar som implementerar noderna i ett HDInsight-kluster är fördelade mellan dessa feldomäner. Den här arkitekturen begränsar de potentiella problemen vid fysiska maskinvarufel.

Varje Azure-region har ett visst antal feldomäner. En lista med domäner och antalet feldomäner de innehåller finns i dokumentationen av [tillgänglighetsuppsättningar](../../virtual-machines/availability.md#availability-sets).

> [!IMPORTANT]  
> Kafka har ingen information om feldomäner. När du skapar ett ämne i Kafka kan det lagra alla partitionsrepliker i samma feldomän. HDInsight har [verktyget för ombalansering av Kafka-partitioner](https://github.com/hdinsight/hdinsight-kafka-tools) som lösning på det här problemet.

## <a name="when-to-rebalance-partition-replicas"></a>När ska du balansera om partitionsrepliker

Du får bästa möjliga tillgänglighet för dina Kafka-data om du balanserar om partitionsreplikerna för ditt ämne vid följande tidpunkter:

* När du skapar ett nytt ämne eller en ny partition

* När du skalar upp ett kluster

## <a name="replication-factor"></a>Replikeringsfaktor

> [!IMPORTANT]  
> Vi rekommenderar att du använder en Azure-region som innehåller tre feldomäner, och använder replikeringsfaktorn 3.

Om du måste använda en region som bara har två feldomäner ska du använda replikeringsfaktorn 4, så att replikerna fördelas jämnt mellan de två feldomänerna.

Ett exempel på hur du skapar ämnen och anger replikeringslänken finns i dokumentet [starta med Apache Kafka på HDInsight](apache-kafka-get-started.md) .

## <a name="how-to-rebalance-partition-replicas"></a>Så balanserar du om partitionsrepliker

Använd [verktyget för ombalansering av Apache Kafka partition](https://github.com/hdinsight/hdinsight-kafka-tools) för att balansera om valda ämnen. Du måste köra det här verktyget från en SSH-session till huvudnoden för ditt Kafka-kluster.

Mer information om hur du ansluter till HDInsight via SSH finns i dokumentet [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="next-steps"></a>Nästa steg

* [Skalbarhet för Apache Kafka i HDInsight](apache-kafka-scalability.md)
* [Spegling med Apache Kafka på HDInsight](apache-kafka-mirroring.md)